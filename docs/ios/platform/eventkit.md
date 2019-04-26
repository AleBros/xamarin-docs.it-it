---
title: EventKit in xamarin. IOS
description: Questo documento descrive EventKit e come usarlo in xamarin. IOS. Vengono illustrati i calendari, gli eventi del calendario e i promemoria, esamina le classi comunemente usate durante la programmazione con EventKit e altro ancora.
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: ea03c8b382e2de29bd20ab1d696d7abb7733e182
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369248"
---
# <a name="eventkit-in-xamarinios"></a>EventKit in xamarin. IOS

iOS è incorporate due applicazioni relativi al calendario: l'applicazione di calendario e l'applicazione di promemoria. È abbastanza semplice comprendere come l'applicazione di calendario gestisce i dati del calendario, ma l'applicazione dei promemoria è meno ovvia. Promemoria possono verificarsi una associate date in termini di quando si trovano a causa, quando vengono completate, e così via. Di conseguenza, iOS archivia tutti i dati di calendario, che si tratti di eventi del calendario o promemoria, in un'unica posizione, denominato il *calendario Database*.

Il framework EventKit offre un modo per accedere la *calendari*, *gli eventi del calendario*, e *promemoria* calendario Database vengono archiviati i dati. Accesso ai calendari e calendario degli eventi è stato reso disponibile poiché iOS 4, ma l'accesso per i promemoria è stato introdotto in iOS 6.

In questa guida verranno per coprire:

-   **Nozioni di base EventKit** – questo presenterà gli elementi fondamentali dell'EventKit tramite le classi principali e offre una comprensione del loro utilizzo. In questa sezione è necessaria leggere prima di affrontare la parte successiva del documento. 
-   **Attività comuni** : la sezione attività comuni deve essere un riferimento rapido su come eseguire operazioni comuni, ad esempio, l'enumerazione dei calendari, creazione, salvataggio e il recupero del calendario eventi e i promemoria, nonché tramite il controller predefinito per creazione e modifica gli eventi del calendario. In questa sezione non debbano essere leggere da primo a ultimo, come è fatta per essere un riferimento per attività specifiche. 


Tutte le attività in questa guida sono disponibili nell'applicazione di esempio complementare:

 [![](eventkit-images/01.png "Le schermate dell'applicazione di esempio complementare")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>Requisiti

EventKit è stato introdotto in iOS 4.0, ma l'accesso ai dati di promemoria è stato introdotto in iOS 6.0. Di conseguenza, a scopo di sviluppo EventKit generale, è necessario almeno come destinazione versioni 4.0 e 6.0 per i promemoria.

Inoltre, l'applicazione di promemoria non è disponibile nel simulatore, il che significa che i promemoria dati inoltre non saranno disponibili, a meno di aggiungerle prima di tutto. Inoltre, le richieste di accesso vengono visualizzate solo all'utente del dispositivo effettivo. Di conseguenza, lo sviluppo EventKit meglio viene testato nel dispositivo.

## <a name="event-kit-basics"></a>Nozioni fondamentali di Kit di eventi

Quando si lavora con EventKit, è importante avere appreso le classi comuni e il relativo utilizzo. Tutte queste classi sono reperibili nel `EventKit` e `EventKitUI` (per il `EKEventEditController`).

### <a name="eventstore"></a>EventStore

Il *EventStore* è la classe più importante in EventKit perché è necessaria per eseguire le operazioni in EventKit. Si può essere considerato come l'archiviazione persistente, o motore di database per tutti i dati EventKit. Da `EventStore` è possibile utilizzare sia i calendari e gli eventi del calendario nell'applicazione di calendario, nonché i promemoria nell'applicazione promemoria.

Poiché `EventStore` è, ad esempio un motore di database, deve essere lunga durata, vale a dire che deve essere creato e distrutto al minimo la durata di un'istanza dell'applicazione. In effetti, è consigliabile che dopo aver creato un'istanza di un `EventStore` in un'applicazione, si tiene una che fanno riferimento a tutto per l'intera durata dell'applicazione, a meno che non si è certi non sarà necessario. Inoltre, tutte le chiamate devono transitare a un singolo `EventStore` istanza. Per questo motivo, il modello Singleton è consigliato per mantenere una sola istanza disponibile.

#### <a name="creating-an-event-store"></a>Creazione di un evento di Store

Il codice seguente illustra un modo efficiente per creare una singola istanza del `EventStore` classe e renderlo disponibile in modo statico dall'interno di un'applicazione:

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

Il codice precedente Usa il modello Singleton per creare un'istanza di `EventStore` quando il caricamento dell'applicazione. Il `EventStore` è quindi possibile accedere a livello globale da all'interno dell'applicazione come indicato di seguito:

```csharp
App.Current.EventStore;
```

Si noti che tutti gli esempi in questo punto usano questo modello, in modo che facciano riferimento il `EventStore` tramite `App.Current.EventStore`.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Richiedere l'accesso al calendario e i dati di promemoria

Prima di poter accedere ai dati tramite il EventStore, un'applicazione deve prima richiedere l'accesso a dati di eventi del calendario o dati di promemoria, a seconda di quello che è necessario. Per facilitare questa operazione, il `EventStore` espone un metodo denominato `RequestAccess` che, quando viene chiamato, consentirà di visualizzare una visualizzazione avvisi per l'utente che comunica che l'applicazione richiede l'accesso a dati di calendario, o dati di promemoria, a seconda di quale `EKEntityType`viene passato al metodo. Poiché genera una visualizzazione avvisi, la chiamata è asincrona e chiama un gestore di completamento passato come un `NSAction` (o Lambda) a esso che riceveranno due parametri; un valore booleano di se è stato concesso l'accesso e un `NSError`che, se non null verrà contiene informazioni su eventuali errori nella richiesta. Ad esempio, quanto segue codificati richiede accesso ai dati dell'evento del calendario e Mostra un avviso consente di visualizzare se la richiesta non è stato concesso.

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

Dopo che la richiesta è stata concessa, verrà mantenuta fino a quando l'applicazione è installata nel dispositivo e non si aprirà un avviso all'utente.
Tuttavia, l'accesso solo viene assegnato il tipo di risorsa, gli eventi del calendario o promemoria concesso. Se un'applicazione deve accedere a entrambi, è necessario richiedere entrambe.

Poiché viene memorizzata l'autorizzazione, è relativamente conveniente effettuare la richiesta ogni volta, pertanto è consigliabile richiedere sempre l'accesso prima di eseguire un'operazione.

Inoltre, poiché il gestore di completamento viene chiamato su un thread separato (non dell'interfaccia utente), tutti gli aggiornamenti all'interfaccia utente nel gestore di completamento devono essere chiamati tramite `InvokeOnMainThread`, altrimenti verrà generata un'eccezione e se non rilevato, l'applicazione si arresterà.

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` è un'enumerazione che descrive il tipo di `EventKit` elemento o i dati. Ha due valori: `Event` e un promemoria. Viene usato in un numero di metodi, tra cui `EventStore.RequestAccess` per indicare a `EventKit` dal tipo di dati per ottenere l'accesso a o recuperare.

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* rappresenta un calendario, che contiene un gruppo di eventi del calendario. Calendari possono essere archiviati in molte posizioni diverse, ad esempio in locale, in *iCloud*, in un 3rd party sede del provider, ad esempio un' *Exchange Server* oppure *Google*e così via. Numero di volte `EKCalendar` viene usato per indicare `EventKit` la posizione in cui cercare gli eventi o dove salvarli.

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* reperibili nel `EventKitUI` dello spazio dei nomi e un controller predefinito che può essere utilizzato per modificare o creare gli eventi del calendario. In modo analogo compilato nel controller di fotocamera, `EKEventEditController` esegue il lavoro sporco automaticamente nella visualizzazione dell'interfaccia utente e gestisce il salvataggio.

### <a name="ekevent"></a>EKEvent

 *EKEvent* rappresenta un evento del calendario. Entrambe `EKEvent` e `EKReminder` ereditare `EKCalendarItem` e hanno, ad esempio campi `Title`, `Notes`e così via.

### <a name="ekreminder"></a>EKReminder

 *EKReminder* rappresenta un elemento del promemoria.

### <a name="ekspan"></a>EKSpan

*EKSpan* è un'enumerazione che descrive l'intervallo di eventi quando si modificano gli eventi che possono ricorrere e dispone di due valori: *ThisEvent* e *FutureEvents*. `ThisEvent` indica che tutte le modifiche si verificherà solo per l'evento specifico nella serie a cui viene fatto riferimento, mentre `FutureEvents` avrà effetto su tale evento e tutte le ricorrenze future.

## <a name="tasks"></a>Attività

Per semplicità d'uso, EventKit utilizzo è stato suddiviso in attività comuni, descritte nelle sezioni seguenti.

### <a name="enumerate-calendars"></a>Enumerare i calendari

Per enumerare i calendari che l'utente ha configurato nel dispositivo, chiamare `GetCalendars` nella `EventStore` e passare il tipo di calendari, i promemoria o eventi, che si desiderano ricevere:

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Aggiungere o modificare un evento usando il Controller predefinito

Il *EKEventEditViewController* esegue anche il lavoro sporco automaticamente se si desidera creare o modificare un evento con la stessa interfaccia utente che viene presentato all'utente quando si usa l'applicazione di calendario:

 [![](eventkit-images/02.png "L'interfaccia utente che viene presentato all'utente quando si usa l'applicazione di calendario")](eventkit-images/02.png#lightbox)

Per usarla, è necessario dichiararla come una variabile a livello di classe in modo che non ottengano sottoposto a garbage collection se è dichiarata all'interno di un metodo:

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

Quindi, per avviare il programma: crearne un'istanza, assegnargli un riferimento al `EventStore`, associare un' *EKEventEditViewDelegate* delegati a esso e quindi visualizzarli mediante `PresentViewController`:

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

Facoltativamente, se si desidera per il popolamento preliminare dell'evento, è possibile creare un nuovo evento (come mostrato di seguito) oppure è possibile recuperare un evento salvato:

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

Per usare un evento esistente, vedere la *recuperare un evento dall'ID* sezione in un secondo momento.

Il delegato deve eseguire l'override di `Completed` metodo, che viene chiamato dal controller quando l'utente ha terminato la finestra di dialogo:

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

Nel delegato, facoltativamente, è possibile controllare la *azione* nel `Completed` metodo per modificare l'evento e nuovamente i dati o eseguire altre operazioni, viene annullato, avanzati e:

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

Per creare un evento nel codice, usare il *FromStore* metodo factory sul `EKEvent` classe e impostare tutti i dati su di essa:

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

È necessario impostare il calendario che si desidera che l'evento salvato in, ma se non si dispone di alcuna preferenza, è possibile usare il valore predefinito:

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Per salvare l'evento, chiamare il *SaveEvent* metodo su di `EventStore`:

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

Creazione di un promemoria nel codice è analogo a quello di creazione di un evento del calendario:

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

Per salvare, chiamare il *SaveReminder* metodo su di `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>Recupero di un evento in base all'ID

Per recuperare un evento da tale ID, utilizzare il *EventFromIdentifier* metodo sul `EventStore` e passarlo di `EventIdentifier` che è stato effettuato il pull dall'evento:

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

Per gli eventi, è disponibile sono due proprietà identificatore di altro tipo, ma `EventIdentifier` è l'unico utilizzabile per questo oggetto.

### <a name="retrieving-a-reminder-by-id"></a>Recupero di un promemoria tramite ID

Per recuperare un promemoria, usare il *GetCalendarItem* metodo sulle `EventStore` e passarlo il *CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

In quanto `GetCalendarItem` restituisce un `EKCalendarItem`, è necessario eseguirne il cast a `EKReminder` se è necessario accedere ai dati di promemoria o usare l'istanza come un' `EKReminder` in un secondo momento.

Non usare `GetCalendarItem` per eventi del calendario, perché al momento della scrittura, non può essere usato.

### <a name="deleting-an-event"></a>Eliminazione di un evento

Per eliminare un evento del calendario, chiamare *RemoveEvent* nel `EventStore` e passare un riferimento per l'evento e appropriato `EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

Si noti, tuttavia, dopo un evento è stato eliminato, il riferimento dell'evento sarà `null`.

### <a name="deleting-a-reminder"></a>L'eliminazione di un promemoria

Per eliminare un promemoria, chiamare *RemoveReminder* nel `EventStore` e passare un riferimento per il promemoria:

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Si noti che nel codice precedente esiste il casting `EKReminder`, in quanto `GetCalendarItem` è stata usata l'operazione di recupero

### <a name="searching-for-events"></a>Ricerca degli eventi

Per cercare gli eventi del calendario, è necessario creare un *NSPredicate* dell'oggetto tramite il *PredicateForEvents* metodo su di `EventStore`. Un `NSPredicate` è una query dei dati dell'oggetto che iOS Usa per individuare le corrispondenze:

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

Dopo aver creato il `NSPredicate`, usare il *EventsMatching* metodo su di `EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Si noti che le query sincrona (bloccante) e possono richiedere molto tempo, a seconda della query, pertanto è consigliabile creare rapidamente un nuovo thread o attività per eseguire questa operazione.

### <a name="searching-for-reminders"></a>La ricerca di promemoria

La ricerca di promemoria è simile agli eventi; richiede un predicato, ma la chiamata è asincrona, già in modo che non preoccuparsi di bloccare il thread:

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

Questo documento ha offerto una panoramica di entrambe le parti importanti del framework EventKit e un numero di attività più comuni. Tuttavia, è molto grande e potente framework EventKit e include funzionalità che non sono state introdotte in questo caso, ad esempio: aggiornamenti, la configurazione di allarmi, configurazione di ricorrenza sugli eventi, la registrazione e l'ascolto delle modifiche nel database di calendario, in batch l'impostazione recinti virtuali e altro ancora.  Per altre informazioni, vedere di Apple [Guida alla programmazione di promemoria e calendario](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html).


## <a name="related-links"></a>Collegamenti correlati

- [Calendari (esempio)](https://developer.xamarin.com/samples/monotouch/Calendars/)
- [Introduzione a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Introduzione a calendari e promemoria](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
