---
title: EventKit in Xamarin.iOS
description: Questo documento descrive EventKit e come usarlo in Xamarin.iOS. Vengono illustrati i calendari, gli eventi del calendario e i promemoria, vengono esaminate le classi utilizzate comunemente durante la programmazione con EventKit e altro ancora.
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 1be6da2bbaf4aeffe00d90945bd06867f929c334
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032544"
---
# <a name="eventkit-in-xamarinios"></a>EventKit in Xamarin.iOS

iOS include due applicazioni correlate al calendario: l'applicazione calendario e l'applicazione promemoria. È abbastanza semplice per comprendere in che modo l'applicazione Calendar gestisce i dati del calendario, ma l'applicazione promemoria è meno ovvia. I promemoria possono effettivamente avere date associate in termini di quando sono dovuti, quando vengono completati e così via. In questo modo, iOS archivia tutti i dati del calendario, sia che si tratti di eventi del calendario o promemoria, in un'unica posizione, denominato *database del calendario*.

EventKit Framework fornisce un modo per accedere ai *calendari*, *agli eventi del calendario*e ai *promemoria* dei dati archiviati nel database Calendar. L'accesso ai calendari e agli eventi di calendario è stato disponibile a partire da iOS 4, ma l'accesso ai promemoria è una novità di iOS 6.

In questa guida verranno illustrate le operazioni seguenti:

- **Nozioni di base su EventKit** : in questo modo si introdurranno le parti fondamentali di EventKit tramite le classi principali e ne viene fornita una conoscenza sul loro utilizzo. Questa sezione è obbligatoria per la lettura prima di affrontare la parte successiva del documento. 
- **Attività comuni** : la sezione attività comuni è concepita come riferimento rapido su come eseguire operazioni comuni, ad esempio. l'enumerazione dei calendari, la creazione, il salvataggio e il recupero di eventi e promemoria del calendario, nonché l'utilizzo dei controller predefiniti per la creazione e la modifica di eventi del calendario. Questa sezione non deve essere letta in primo piano perché è concepita come riferimento per determinate attività. 

Tutte le attività di questa guida sono disponibili nell'applicazione di esempio complementare:

 [![](eventkit-images/01.png "The companion sample application screens")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>Requisiti

EventKit è stato introdotto in iOS 4,0, ma l'accesso ai dati dei promemoria è stato introdotto in iOS 6,0. Di conseguenza, per lo sviluppo EventKit generale, è necessario avere come destinazione almeno la versione 4,0 e 6,0 per i promemoria.

Inoltre, l'applicazione promemoria non è disponibile nel simulatore, il che significa che i dati dei promemoria non saranno disponibili, a meno che non vengano aggiunti per primi. Inoltre, le richieste di accesso vengono visualizzate solo per l'utente sul dispositivo effettivo. Di conseguenza, lo sviluppo di EventKit è più testato sul dispositivo.

## <a name="event-kit-basics"></a>Nozioni fondamentali sul kit eventi

Quando si lavora con EventKit, è importante comprendere le classi comuni e il relativo utilizzo. Tutte queste classi sono disponibili nell'`EventKit` e `EventKitUI` (per il `EKEventEditController`).

### <a name="eventstore"></a>EventStore

La classe *EventStore* è la classe più importante in EventKit perché è necessaria per eseguire qualsiasi operazione in EventKit. Può essere considerato come l'archivio permanente o il motore di database per tutti i dati EventKit. Da `EventStore` si ha accesso sia ai calendari che agli eventi del calendario nell'applicazione calendario, oltre ai promemoria nell'applicazione promemoria.

Dal momento che `EventStore` è come un motore di database, deve essere di lunga durata, vale a dire che deve essere creato ed eliminato il minor tempo possibile durante la durata di un'istanza dell'applicazione. In realtà, è consigliabile che una volta creata un'istanza di un `EventStore` in un'applicazione, il riferimento venga mantenuto per tutta la durata dell'applicazione, a meno che non si sia certi che non sarà più necessario. Inoltre, tutte le chiamate devono passare a una singola istanza di `EventStore`. Per questo motivo, è consigliabile usare il modello singleton per mantenere disponibile una singola istanza.

#### <a name="creating-an-event-store"></a>Creazione di un archivio eventi

Il codice seguente illustra un modo efficiente per creare una singola istanza della classe `EventStore` e renderla disponibile in modo statico dall'interno di un'applicazione:

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

Il codice precedente usa il modello singleton per creare un'istanza del `EventStore` quando viene caricata l'applicazione. È quindi possibile accedere al `EventStore` a livello globale dall'interno dell'applicazione, come indicato di seguito:

```csharp
App.Current.EventStore;
```

Si noti che tutti gli esempi in qui usano questo modello, quindi fanno riferimento al `EventStore` tramite `App.Current.EventStore`.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Richiesta di accesso ai dati del calendario e del promemoria

Prima di poter accedere a tutti i dati tramite EventStore, un'applicazione deve prima richiedere l'accesso ai dati degli eventi del calendario o ai dati dei promemoria, a seconda di quale è necessario. Per facilitare questa operazione, il `EventStore` espone un metodo denominato `RequestAccess` che, quando chiamato, visualizzerà una visualizzazione avvisi per indicare all'utente che l'applicazione richiede l'accesso ai dati del calendario o ai dati di promemoria, a seconda del `EKEntityType` passato a è. Poiché genera una visualizzazione avvisi, la chiamata è asincrona e chiamerà un gestore di completamento passato come `NSAction` (o lambda) che riceverà due parametri. valore booleano che indica se l'accesso è stato concesso e un `NSError`, che, se diverso da null, conterrà eventuali informazioni sull'errore nella richiesta. Il codice seguente, ad esempio, richiede l'accesso ai dati degli eventi del calendario e visualizza una vista avvisi se la richiesta non è stata concessa.

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

Una volta concessa, la richiesta verrà memorizzata finché l'applicazione verrà installata nel dispositivo e non verrà visualizzato un avviso per l'utente.
Tuttavia, l'accesso viene assegnato solo al tipo di risorsa, ovvero eventi del calendario o promemoria concessi. Se un'applicazione deve accedere a entrambe, deve richiedere entrambe.

Poiché l'autorizzazione viene memorizzata, è relativamente conveniente eseguire ogni volta la richiesta, quindi è consigliabile richiedere sempre l'accesso prima di eseguire un'operazione.

Inoltre, poiché il gestore di completamento viene chiamato su un thread separato (non UI), tutti gli aggiornamenti dell'interfaccia utente nel gestore di completamento devono essere chiamati tramite `InvokeOnMainThread`. in caso contrario, verrà generata un'eccezione e, se non viene intercettata, l'applicazione verrà arrestata.

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` è un'enumerazione che descrive il tipo di `EventKit` elemento o i dati. Ha due valori: `Event` e promemoria. Viene usato in diversi metodi, tra cui `EventStore.RequestAccess` per indicare `EventKit` tipo di dati a cui ottenere l'accesso o il recupero.

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* rappresenta un calendario, che contiene un gruppo di eventi del calendario. I calendari possono essere archiviati in numerose posizioni diverse, ad esempio localmente, in *iCloud*, in una posizione del provider di terze parti, ad esempio un *server Exchange* o *Google*e così via. Molte volte `EKCalendar` viene usato per indicare `EventKit` dove cercare gli eventi o dove salvarli.

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* si trova nello spazio dei nomi `EventKitUI` ed è un controller incorporato che può essere utilizzato per modificare o creare eventi del calendario. Analogamente a quanto avviene per i controller incorporati della fotocamera, `EKEventEditController` esegue le attività più complesse per la visualizzazione dell'interfaccia utente e la gestione del salvataggio.

### <a name="ekevent"></a>EKEvent

 *EKEvent* rappresenta un evento del calendario. Sia `EKEvent` che `EKReminder` ereditano da `EKCalendarItem` e hanno campi quali `Title`, `Notes`e così via.

### <a name="ekreminder"></a>EKReminder

 *EKReminder* rappresenta un elemento di promemoria.

### <a name="ekspan"></a>EKSpan

*EKSpan* è un'enumerazione che descrive l'intervallo di eventi durante la modifica di eventi che possono ripetersi e ha due valori: *questa* e *FutureEvents*. `ThisEvent` significa che tutte le modifiche si verificheranno solo per l'evento specifico della serie a cui viene fatto riferimento, mentre `FutureEvents` influirà su tale evento e su tutte le ricorrenze future.

## <a name="tasks"></a>Attività

Per semplicità d'uso, l'utilizzo di EventKit è stato suddiviso in attività comuni, descritte nelle sezioni riportate di seguito.

### <a name="enumerate-calendars"></a>Enumera calendari

Per enumerare i calendari configurati dall'utente sul dispositivo, chiamare `GetCalendars` sul `EventStore` e passare il tipo di calendario (promemoria o eventi) che si desidera ricevere:

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Aggiungere o modificare un evento usando il controller incorporato

Il *EKEventEditViewController* esegue un elevato carico di lavoro se si desidera creare o modificare un evento con la stessa interfaccia utente presentata all'utente quando si utilizza l'applicazione calendario:

 [![](eventkit-images/02.png "The UI that is presented to the user when using the Calendar Application")](eventkit-images/02.png#lightbox)

Per usarlo, è necessario dichiararlo come variabile a livello di classe in modo che non venga sottoposta a Garbage Collection se è dichiarato all'interno di un metodo:

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

Quindi, per avviarlo: crearne un'istanza, assegnargli un riferimento al `EventStore`, associare un delegato *EKEventEditViewDelegate* a esso e quindi visualizzarlo usando `PresentViewController`:

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

Facoltativamente, se si vuole pre-popolare l'evento, è possibile creare un nuovo evento (come illustrato di seguito) oppure è possibile recuperare un evento salvato:

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

Se si desidera eseguire il popolamento preliminare dell'interfaccia utente, assicurarsi di impostare la proprietà dell'evento sul controller:

```csharp
eventController.Event = newEvent;
```

Per usare un evento esistente, vedere la sezione *recuperare un evento in base all'ID* in un secondo momento.

Il delegato deve eseguire l'override del metodo `Completed`, che viene chiamato dal controller quando l'utente ha terminato la finestra di dialogo:

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

Facoltativamente, nel delegato è possibile controllare l' *azione* nel metodo `Completed` per modificare l'evento e risalvarlo o eseguire altre operazioni, se è stato annullato, eccetera:

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

Per creare un evento nel codice, usare il metodo factory *FromStore* sulla classe `EKEvent` e impostare i dati su di esso:

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

È necessario impostare il calendario in cui si vuole salvare l'evento, ma se non si ha alcuna preferenza, è possibile usare il valore predefinito:

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Per salvare l'evento, chiamare il metodo *SaveEvent* sull'`EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

Dopo il salvataggio, la proprietà *EventIdentifier* verrà aggiornata con un identificatore univoco che può essere usato in un secondo momento per recuperare l'evento:

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` è un GUID formattato in formato stringa.

### <a name="create-a-reminder-programmatically"></a>Creare un promemoria a livello di codice

La creazione di un promemoria nel codice è molto simile alla creazione di un evento del calendario:

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

Per salvare, chiamare il metodo *SaveReminder* sull'`EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>Recupero di un evento in base all'ID

Per recuperare un evento in base all'ID, usare il metodo *EventFromIdentifier* sul `EventStore` e passargli il `EventIdentifier` estratto dall'evento:

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

Per gli eventi sono presenti altre due proprietà identificatore, ma `EventIdentifier` è l'unico che funziona a questo.

### <a name="retrieving-a-reminder-by-id"></a>Recupero di un promemoria in base all'ID

Per recuperare un promemoria, usare il metodo *GetCalendarItem* nel `EventStore` e passargli il *CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

Poiché `GetCalendarItem` restituisce un `EKCalendarItem`, è necessario eseguirne il cast in `EKReminder` se è necessario accedere ai dati di promemoria o usare l'istanza come `EKReminder` in un secondo momento.

Non usare `GetCalendarItem` per gli eventi del calendario, come al momento della stesura di questa operazione.

### <a name="deleting-an-event"></a>Eliminazione di un evento

Per eliminare un evento del calendario, chiamare *RemoveEvent* sul `EventStore` e passare un riferimento all'evento e il `EKSpan`appropriato:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

Si noti tuttavia che dopo l'eliminazione di un evento, il riferimento all'evento verrà `null`.

### <a name="deleting-a-reminder"></a>Eliminazione di un promemoria

Per eliminare un promemoria, chiamare *RemoveReminder* sul `EventStore` e passare un riferimento al promemoria:

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Si noti che nel codice precedente è presente un cast per `EKReminder`, perché `GetCalendarItem` è stato usato per recuperarlo

### <a name="searching-for-events"></a>Ricerca di eventi

Per cercare gli eventi del calendario, è necessario creare un oggetto *NSPredicate* tramite il metodo *PredicateForEvents* sul `EventStore`. Un `NSPredicate` è un oggetto dati della query usato da iOS per individuare le corrispondenze:

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

Dopo aver creato la `NSPredicate`, usare il metodo *EventsMatching* sul `EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Si noti che le query sono sincrone (bloccanti) e possono richiedere molto tempo, a seconda della query, quindi è possibile creare un nuovo thread o un'attività a tale scopo.

### <a name="searching-for-reminders"></a>Ricerca dei promemoria

La ricerca dei promemoria è simile agli eventi; richiede un predicato, ma la chiamata è già asincrona, quindi non è necessario preoccuparsi del blocco del thread:

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

Questo documento ha fornito una panoramica dei componenti importanti del Framework EventKit e di alcune delle attività più comuni. Tuttavia, il Framework EventKit è molto grande e potente e include funzionalità che non sono state introdotte in questo argomento, ad esempio aggiornamenti batch, configurazione degli allarmi, configurazione della ricorrenza sugli eventi, registrazione e ascolto delle modifiche nel database Calendar, impostazione di geobarriere e altro ancora.  Per ulteriori informazioni, vedere la [Guida alla programmazione del calendario e dei promemoria](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)di Apple.

## <a name="related-links"></a>Collegamenti correlati

- [Calendari (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/calendars)
- [Introduzione a iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Introduzione ai calendari e ai promemoria](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
