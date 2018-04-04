---
title: EventKit
description: Questa guida fornisce una panoramica su come accedere e utilizzare i dati di calendari e CalendarEvents promemoria archiviati nel Database di calendario, esposto tramite la EventKit. Vengono illustrate le classi principali e il relativo ruolo nella programmazione EventKit, nonché un numero di attività comuni associate EventKit framework.
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a8439586ac92f8139cf9341611125352c85706e5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="eventkit"></a>EventKit

_Questa guida fornisce una panoramica su come accedere e utilizzare i dati di calendari e CalendarEvents promemoria archiviati nel Database di calendario, esposto tramite la EventKit. Vengono illustrate le classi principali e il relativo ruolo nella programmazione EventKit, nonché un numero di attività comuni associate EventKit framework._

iOS è incorporate due applicazioni correlate: l'applicazione di calendario e l'applicazione di promemoria. È abbastanza semplice comprendere come l'applicazione di calendario gestisce i dati del calendario, ma l'applicazione di promemoria è meno ovvio. Le date associate in termini di scadenza, quando è completate, quando sono in realtà sono promemoria e così via. Di conseguenza, iOS archivia tutti i dati di calendario, gli eventi del calendario o promemoria, in un'unica posizione, chiamata di *Database calendario*.

Il framework EventKit fornisce un modo per accedere il *calendari*, *gli eventi del calendario*, e *promemoria* dati archiviati nel Database del calendario. Accesso ai calendari e calendario eventi è stata disponibile da iOS 4, ma l'accesso a promemoria è una novità di iOS 6.

In questa Guida verrà per coprire:

-   **Nozioni di base EventKit** – questo presenterà gli elementi fondamentali dell'EventKit tramite le classi principali e fornisce la comprensione del loro utilizzo. In questa sezione è necessario leggere prima di affrontare la parte successiva del documento. 
-   **Attività comuni** – la sezione attività comune deve essere un riferimento rapido su come eseguire operazioni comuni, ad esempio, l'enumerazione dei calendari, creazione, salvataggio e il recupero di calendario eventi e i promemoria, nonché mediante il controller incorporato per creazione e modifica gli eventi del calendario. In questa sezione è necessario leggere anteriore posteriore, come e mira a essere un riferimento per attività specifiche. 


Tutte le attività in questa guida sono disponibili nell'applicazione di esempio complementare:

 [![](eventkit-images/01.png "Le schermate dell'applicazione di esempio complementare")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>Requisiti

EventKit è stata introdotta in iOS 4.0, ma l'accesso ai dati di promemoria è stata introdotta in iOS 6.0. Di conseguenza, per effettuare lo sviluppo EventKit generale, è necessario almeno come destinazione versioni 4.0 e 6.0 per i promemoria.

Inoltre, l'applicazione di promemoria non è disponibile nel simulatore, il che significa che i dati di promemoria anche non saranno disponibili, a meno che non è necessario aggiungerli prima. Inoltre, le richieste di accesso vengono visualizzate solo per l'utente sul dispositivo reale. Di conseguenza, sviluppo EventKit meglio viene testato nel dispositivo.

## <a name="event-kit-basics"></a>Evento Kit nozioni di base

Quando si lavora con EventKit, è importante disporre quindi le classi comuni e il relativo utilizzo. Tutte queste classi sono reperibili il `EventKit` e `EventKitUI` (per il `EKEventEditController`).

### <a name="eventstore"></a>EventStore

Il *EventStore* è la classe più importante in EventKit perché è necessario eseguire altre operazioni in EventKit. Può considerato come l'archivio permanente, o motore di database per tutti i dati EventKit. Da `EventStore` si ha accesso ai calendari e gli eventi del calendario dell'applicazione di calendario, nonché l'applicazione di promemoria i promemoria.

Poiché `EventStore` è ad esempio un motore di database deve essere lunga durata, per indicare che deve essere creato ed eliminato minor nel corso di un'istanza di applicazione. Infatti, si consiglia che dopo aver creato un'istanza di un `EventStore` in un'applicazione, mantenere il riferimento per l'intera durata dell'applicazione, a meno che non si è certi di non sarà necessario. Inoltre, tutte le chiamate devono transitare a un singolo `EventStore` istanza. Per questo motivo, è consigliabile il criterio Singleton per mantenere una singola istanza disponibile.

#### <a name="creating-an-event-store"></a>Creazione di un archivio di eventi

Il codice seguente viene illustrato un modo efficiente per creare una singola istanza di `EventStore` classe e renderlo disponibile in modo statico dall'interno di un'applicazione:

```csharp
public class App
{
    public static App Current {
            get { return current; }
    }
    private static App current;

    public EKEventStore EventStore {
            get { return eventStore; }
    }
    protected EKEventStore eventStore;

    static App ()
    {
            current = new App();
    }
    protected App () 
    {
            eventStore = new EKEventStore ( );
    }
}
```

Il codice precedente utilizza il modello Singleton per creare un'istanza di `EventStore` quando l'applicazione carica. Il `EventStore` è possibile accedere a livello globale all'interno dell'applicazione come indicato di seguito:

```csharp
App.Current.EventStore;
```

Si noti che tutti gli esempi in questo punto utilizzano questo modello, in modo fanno riferimento le `EventStore` tramite `App.Current.EventStore`.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Richiedere l'accesso al calendario e i dati di promemoria

Prima di poter accedere ai dati tramite il EventStore, un'applicazione deve prima richiedere l'accesso a dati di eventi del calendario o di promemoria, a seconda di quale i dati necessari. A tale scopo, il `EventStore` espone un metodo denominato `RequestAccess` che, quando viene chiamato, consentirà di visualizzare una visualizzazione avvisi per l'utente che informa che l'applicazione richiede l'accesso a dati del calendario, o di dati di promemoria, a seconda di quale `EKEntityType`viene passato al metodo. In quanto genera una visualizzazione avvisi, la chiamata è asincrona e chiama un gestore di completamento passato come un `NSAction` (o Lambda) al quale verrà assegnato a due parametri; un valore booleano di fatto è stato concesso l'accesso e un `NSError`che, se vengono apportate non null contiene informazioni di errore nella richiesta. Ad esempio, la codificato verrà richiesta seguente accesso ai dati dell'evento del calendario e Mostra un avviso consente di visualizzare se la richiesta non è stato concesso.

```csharp
App.Current.EventStore.RequestAccess (EKEntityType.Event, 
    (bool granted, NSError e) => {
            if (granted)
                    //do something here
            else
                    new UIAlertView ( "Access Denied", 
"User Denied Access to Calendar Data", null,
"ok", null).Show ();
            } );
```

Dopo la richiesta è stata concessa, viene memorizzato fino a quando l'applicazione è installata nel dispositivo e non verrà visualizzato un avviso all'utente.
Tuttavia, l'accesso viene fornito solo per il tipo di risorsa, gli eventi del calendario o promemoria concesso. Se un'applicazione deve accedere a entrambi, è necessario richiedere entrambi.

Poiché viene memorizzata l'autorizzazione, è relativamente conveniente effettuare la richiesta ogni volta, pertanto è consigliabile richiedere sempre l'accesso prima di eseguire un'operazione.

Inoltre, poiché il gestore di completamento viene chiamato su un thread separato (non dell'interfaccia utente), tutti gli aggiornamenti all'interfaccia utente nel gestore di completamento devono essere chiamati tramite `InvokeOnMainThread`, in caso contrario verrà generata un'eccezione e se non è stato rilevato, l'applicazione verificherà un arresto anomalo.

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` è un'enumerazione che descrive il tipo di `EventKit` elemento o dati. Ha due valori: `Event` e promemoria. Viene usato in diversi modi, ad esempio `EventStore.RequestAccess` indicare `EventKit` il tipo di dati per ottenere l'accesso a o recuperare.

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* rappresenta un calendario, che contiene un gruppo di eventi del calendario. Calendari possono essere archiviati in numerose posizioni diverse, ad esempio in locale, in *iCloud*, in un 3rd party percorso del provider, ad esempio un *Exchange Server* o *Google*e così via. Numero di volte `EKCalendar` viene utilizzato per indicare `EventKit` posizione in cui cercare gli eventi o come salvarli.

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* è reperibile nel `EventKitUI` dello spazio dei nomi e un controller incorporato che può essere usato per modificare o creare gli eventi del calendario. In modo analogo alle incorporato nei controller di fotocamera `EKEventEditController` esegue le operazioni necessarie per l'utente per la visualizzazione dell'interfaccia utente e la gestione di salvataggio.

### <a name="ekevent"></a>EKEvent

 *EKEvent* rappresenta un evento del calendario. Entrambi `EKEvent` e `EKReminder` ereditare `EKCalendarItem` e avere campi, ad esempio `Title`, `Notes`e così via.

### <a name="ekreminder"></a>EKReminder

 *EKReminder* rappresenta un elemento promemoria.

### <a name="ekspan"></a>EKSpan

*EKSpan* è un'enumerazione che descrive l'intervallo di eventi quando si modificano gli eventi che possono ricorrere e dispone di due valori: *ThisEvent* e *FutureEvents*. `ThisEvent` indica che tutte le modifiche verranno eseguito solo all'evento specifico nella serie a cui fa riferimento, mentre `FutureEvents` avrà effetto su tale evento e tutte le ricorrenze future.

## <a name="tasks"></a>Attività

Per semplicità, EventKit utilizzo è stato suddiviso in attività comuni, descritte nelle sezioni seguenti.

### <a name="enumerate-calendars"></a>Enumerare i calendari

Per enumerare i calendari che l'utente è configurato nel dispositivo, chiamare `GetCalendars` sul `EventStore` e passare il tipo di calendari (promemoria o eventi) che si desiderano ricevere:

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Aggiungere o modificare un evento tramite il Controller incorporato

Il *EKEventEditViewController* gran parte delle operazioni necessarie per l'utente se si desidera creare o modificare un evento con la stessa interfaccia utente che l'utente viene visualizzato quando si utilizza l'applicazione di calendario:

 [![](eventkit-images/02.png "L'interfaccia utente che l'utente viene visualizzato quando si utilizza l'applicazione di calendario")](eventkit-images/02.png#lightbox)

Per utilizzarla, è necessario dichiararlo come una variabile a livello di classe in modo che non ottenere sottoposto a garbage collection se viene dichiarata all'interno di un metodo:

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

Quindi, per avviarlo: crearne un'istanza, assegnargli un riferimento al `EventStore`, associare un *EKEventEditViewDelegate* delegati a esso e quindi visualizzarlo in `PresentViewController`:

```csharp
EventKitUI.EKEventEditViewController eventController = 
        new EventKitUI.EKEventEditViewController ();

// set the controller's event store - it needs to know where/how to save the event
eventController.EventStore = App.Current.EventStore;

// wire up a delegate to handle events from the controller
eventControllerDelegate = new CreateEventEditViewDelegate ( eventController );
eventController.EditViewDelegate = eventControllerDelegate;

// show the event controller
PresentViewController ( eventController, true, null );
```

Facoltativamente, se si desidera pre-popolare l'evento, è possibile creare un nuovo evento (come illustrato di seguito) oppure è possibile recuperare un evento salvato:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and exercise!";
newEvent.Notes = "This is your reminder to go and exercise for 30 minutes.”;
```

Se si desidera pre-popolare l'interfaccia utente, assicurarsi di impostare la proprietà dell'evento nel controller:

```csharp
eventController.Event = newEvent;
```

Per utilizzare un evento esistente, vedere il *recuperare un evento dall'ID* sezione in un secondo momento.

Il delegato deve eseguire l'override di `Completed` metodo, che viene chiamato dal controller quando l'utente termina la finestra di dialogo:

```csharp
protected class CreateEventEditViewDelegate : EventKitUI.EKEventEditViewDelegate
{
        // we need to keep a reference to the controller so we can dismiss it
        protected EventKitUI.EKEventEditViewController eventController;

        public CreateEventEditViewDelegate (EventKitUI.EKEventEditViewController eventController)
        {
                // save our controller reference
                this.eventController = eventController;
        }

        // completed is called when a user eith
        public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
        {
                eventController.DismissViewController (true, null);
                }
        }
}
```

Nel delegato, facoltativamente, è possibile controllare il *azione* nel `Completed` metodo per modificare l'evento e nuovamente i dati o eseguire altre operazioni, viene annullato e così via:

```csharp
public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
{
        eventController.DismissViewController (true, null);

        switch ( action ) {

        case EKEventEditViewAction.Canceled:
                break;
        case EKEventEditViewAction.Deleted:
                break;
        case EKEventEditViewAction.Saved:
                // if you wanted to modify the event you could do so here,
// and then save:
                //App.Current.EventStore.SaveEvent ( controller.Event, )
                break;
        }
}
```

### <a name="creating-an-event-programmatically"></a>Creazione di un evento a livello di codice

Per creare un evento nel codice, utilizzare il *FromStore* metodo factory di `EKEvent` e impostare tutti i dati su di esso:

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and do some exercise!";
newEvent.Notes = "This is your motivational event to go and do 30 minutes of exercise. Super important. Do this.";
```

È necessario impostare il calendario che si desidera che l'evento salvato in, ma se non si dispone di alcuna preferenza, è possibile utilizzare il valore predefinito:

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Per salvare l'evento, chiamare il *SaveEvent* metodo il `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

Dopo il salvataggio, il *EventIdentifier* proprietà verrà aggiornata con un identificatore univoco che può essere utilizzato in un secondo momento per recuperare l'evento:

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` è che una stringa di formato GUID.

### <a name="create-a-reminder-programmatically"></a>Creare un promemoria a livello di codice

La creazione di un promemoria nel codice è analogo a quello di creazione di un evento di calendario:

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

Per salvare, chiamare il *SaveReminder* metodo il `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>Recupero di un evento in base all'ID

Per recuperare un evento da tale ID, utilizzare il *EventFromIdentifier* metodo il `EventStore` e passare il `EventIdentifier` che è stato estratto dall'evento:

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

Per gli eventi, è disponibile due altre proprietà di identificatore, ma `EventIdentifier` è l'unico utilizzabile per questo oggetto.

### <a name="retrieving-a-reminder-by-id"></a>Recupero di un promemoria da ID

Per recuperare un promemoria, utilizzare il *GetCalendarItem* metodo il `EventStore` e passare il *CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

Poiché `GetCalendarItem` restituisce un `EKCalendarItem`, è necessario eseguirne il cast `EKReminder` se è necessario accedere ai dati di promemoria oppure usare l'istanza come un `EKReminder` in un secondo momento.

Non utilizzare `GetCalendarItem` per eventi del calendario, al momento della scrittura, non funziona.

### <a name="deleting-an-event"></a>Eliminazione di un evento

Per eliminare un evento del calendario, chiamare *RemoveEvent* sul `EventStore` e passare un riferimento all'evento e appropriata `EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

Si noti, tuttavia, dopo l'eliminazione di un evento, il riferimento dell'evento sarà `null`.

### <a name="deleting-a-reminder"></a>Eliminare un promemoria

Per eliminare un promemoria, chiamare *RemoveReminder* sul `EventStore` e passare un riferimento per il promemoria:

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Si noti che nel codice precedente è un cast a `EKReminder`perché `GetCalendarItem` è stata utilizzata l'operazione di recupero

### <a name="searching-for-events"></a>Ricerca di eventi

Per cercare gli eventi del calendario, è necessario creare un *NSPredicate* oggetto tramite il *PredicateForEvents* metodo il `EventStore`. Un `NSPredicate` è una query di oggetto dati di tale iOS viene utilizzato per individuare le corrispondenze:

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

Dopo aver creato il `NSPredicate`, utilizzare il *EventsMatching* metodo il `EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Si noti che le query sono sincrona (bloccante) e potrebbero richiedere molto tempo, a seconda della query, pertanto è possibile creare rapidamente un nuovo thread o un'attività per eseguire l'operazione.

### <a name="searching-for-reminders"></a>La ricerca di promemoria

La ricerca di promemoria è simile agli eventi; richiede un predicato, ma la chiamata è asincrona, pertanto è necessario che preoccuparsi di bloccare il thread:

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>Riepilogo

Questo documento ha fornito una panoramica di entrambe le parti importanti di framework EventKit e un numero di attività più comuni. Tuttavia, il framework EventKit è molto grandi e più potente e include funzionalità che non sono stati introdotti in questo caso, ad esempio: gli aggiornamenti, la configurazione di avvisi, la configurazione di ricorrenza sugli eventi, la registrazione e l'ascolto delle modifiche nel database di calendario, in blocco l'impostazione GeoFences e altro ancora.  Per ulteriori informazioni, vedere Apple [calendario e Guida per programmatori promemoria](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html).


## <a name="related-links"></a>Collegamenti correlati

- [Calendari (esempio)](https://developer.xamarin.com/samples/monotouch/Calendars/)
- [Introduzione a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Introduzione ai calendari e i promemoria](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
