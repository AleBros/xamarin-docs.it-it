---
title: Attività di background
description: Utilizzare la nuova attività in Background watchOS 3 per garantire che un'applicazione di espressioni di controllo è sempre i dati e l'ancoraggio snapshot più recente.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/13/2017
ms.openlocfilehash: 0279aed1e694e3b38dcb8189819d88740c37b6ad
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="background-tasks"></a>Attività di background

_Utilizzare la nuova attività in Background watchOS 3 per garantire che un'applicazione di espressioni di controllo è sempre i dati e l'ancoraggio snapshot più recente._

WatchOS 3, non vi sono tre modi principali che un'applicazione di espressioni di controllo può aggiornare le informazioni: 

- Utilizzando uno dei diversi nuove attività in background. 
- Con uno dei relativi problemi sul quadrante dell'espressione di controllo (che molto tempo per aggiornare). 
- Dovere Dock di nuovo il pin utente per app (in cui il relativo mantenuti in memoria e aggiornati spesso). 

## <a name="keeping-an-app-up-to-date"></a>Aggiornamento di un'App

Prima di esaminare tutte le modalità in cui che uno sviluppatore può mantenere dati e interfaccia utente di un'app di watchOS costantemente aggiornato, in questa sezione verrà analizzati in un set tipico di modelli di utilizzo e come un utente potrebbe spostare tra loro iPhone e i relativi Apple Watch per tutto il giorno in base a  l'ora del giorno e l'attività attualmente eseguite (ad esempio piedi).

Vedere l'esempio seguente:

[![](background-tasks-images/update00.png "Come un utente potrebbe spostare tra loro iPhone e i relativi Apple Watch per tutto il giorno")](background-tasks-images/update00.png#lightbox)

1. Il mattino, durante l'attesa in linea per un tipo di caffè, l'utente visualizza le notizie corrente loro iPhone per alcuni minuti.
2. Prima di lasciare il bar, archivieranno rapidamente il meteo con una complicazione sul quadrante espressioni di controllo.
3. Prima di pranzo, usano l'app esegue il mapping su iPhone per trovare un ristorante nelle vicinanze e una prenotazione per soddisfare un client di una cartella di lavoro.
4. Anche in viaggio al ristorante, ricevono una notifica nel loro Apple Watch e con un riepilogo rapido, sappiano nomina lunch è in ritardo.
5. Nelle ore serali, usano l'app esegue il mapping su iPhone per controllare il traffico prima guida home.
6. La modalità di home, ricevono una notifica di iMessage nella loro Apple Watch chiede prelevati alcuni milk e usano la funzionalità di risposta rapida per inviare la risposta "OK".

A causa di "quick glance" (meno di tre secondi) natura come un utente desideri usare un'app di Apple Watch, sono in genere non è un tempo sufficiente per l'applicazione recuperare informazioni desiderate e aggiornare la relativa interfaccia utente prima di visualizzarli all'utente.

Tramite la nuova API Apple è incluso in watchOS 3, è possibile pianificare l'app per un _aggiornamento in Background_ e le informazioni desiderate pronte prima che l'utente lo richiede. Prendere in considerazione la complicazione Weather discussi in precedenza:

[![](background-tasks-images/update01.png "Un esempio della complicazione Weather")](background-tasks-images/update01.png#lightbox)

1. Le pianificazioni di app per una riattivati dal sistema in un momento specifico. 
2. L'app recupera le informazioni che è necessario per generare l'aggiornamento.
3. L'app rigenera l'interfaccia utente in modo da riflettere i nuovi dati.
4. Quando l'utente glances nella complicazione dell'app, ha informazioni aggiornate senza che sia in attesa per l'aggiornamento.

Nell'esempio precedente, il sistema watchOS viene attivato l'app usando uno o più attività, di cui dispone di un pool molto limitato disponibile:

[![](background-tasks-images/update02.png "Il sistema watchOS viene attivato l'app usando una o più attività")](background-tasks-images/update02.png#lightbox)

Apple consiglia di utilizzare al meglio questa attività (perché tale una risorsa limitata all'app) da mantenere fino a quando l'applicazione ha completato il processo di aggiornamento automatico.

Il sistema invia tali attività chiamando il nuovo `HandleBackgroundTasks` metodo il `WKExtensionDelegate` delegato. Ad esempio:

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeyWatch.MonkeySeeExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Constructors
        public ExtensionDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request here
            ...
        }
        #endregion
    }
}
```

Quando l'applicazione ha completato l'attività specificata, viene restituito solo per il sistema contrassegnandolo completata:

[![](background-tasks-images/update03.png "L'attività restituisce al sistema contrassegnandolo completata")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Nuova attività in Background

watchOS 3 introduce diverse attività in background che un'app è possibile utilizzare per aggiornare le informazioni di garantire che sia il contenuto l'utente deve prima di aprire l'app, ad esempio:

- **Aggiornamento di App in background** - [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) attività consente all'app di aggiornare il proprio stato in background. In genere ciò comprende un'altra attività, ad esempio download di nuovo contenuto da internet utilizzando un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Snapshot di aggiornamento in background** - [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) attività consente all'app di aggiornare il contenuto e l'interfaccia utente prima che il sistema accetta uno snapshot che verrà utilizzato per popolare l'ancoraggio.
- **Connettività di espressioni di controllo in background** - [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) attività viene avviata per l'app quando riceve i dati in background da iPhone associati.
- **URL di sessione in background** - [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) attività viene avviata per l'app quando un trasferimento in background richiede l'autorizzazione o completato (esito positivo o errore).

Queste attività verranno descritta in dettaglio nelle sezioni riportate di seguito.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

Il `WKApplicationRefreshBackgroundTask` è un'attività generica che può essere pianificata per l'App riattivati in un secondo momento:

[![](background-tasks-images/update04.png "Un WKApplicationRefreshBackgroundTask riattivati in un secondo momento")](background-tasks-images/update04.png#lightbox)

In fase di esecuzione dell'attività, l'app può eseguire qualsiasi tipo di elaborazione locale, ad esempio l'aggiornamento di una sequenza temporale complicazione o recuperare alcuni dati obbligatori con un `NSUrlSession`.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

Il sistema invierà un `WKURLSessionRefreshBackgroundTask` quando i dati sono stata terminata download e pronto per essere eseguito dall'app:

[![](background-tasks-images/update05.png "WKURLSessionRefreshBackgroundTask quando i dati al termine del download")](background-tasks-images/update05.png#lightbox)

Un'applicazione non venga lasciata in esecuzione durante il download di dati in background. Al contrario, l'app pianifica la richiesta di dati, quindi viene sospeso e il sistema gestisce il download dei dati, reawakening solo l'app durante il download è completato.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

In watchOS 3, Apple ha aggiunto ancoraggio in cui gli utenti possono aggiungere le proprie App preferite e accedervi rapidamente. Quando l'utente preme il pulsante sul lato nel Apple Watch, verrà visualizzata una raccolta di app bloccate snapshot. L'utente può scorrere verso sinistra o destra per cercare l'app desiderata, quindi toccare l'app per avviarla sostituendo lo Snapshot con l'interfaccia dell'app in esecuzione.

[![](background-tasks-images/update06.png "Sostituzione dello Snapshot con l'interfaccia di App in esecuzione")](background-tasks-images/update06.png#lightbox)

Il sistema accetta periodicamente gli snapshot dell'interfaccia utente dell'applicazione (inviando un `WKSnapshotRefreshBackgroundTask`) e utilizza questi snapshot per popolare l'ancoraggio. watchOS consente all'app l'opportunità di aggiornare il contenuto e dell'interfaccia utente prima di questo Snapshot è stato creato.

Gli snapshot sono molto importanti per watchOS 3 poiché funzionano come le immagini di anteprima e l'avvio per l'app. Se l'utente liquida in un'app di ancoraggio, venga espanso a schermo intero, immettere il primo piano e avviare l'esecuzione, pertanto è fondamentale che lo Snapshot può essere aggiornata:

[![](background-tasks-images/update07.png "Se l'utente liquida in un'app di ancoraggio, verrà espanso a schermo intero")](background-tasks-images/update07.png#lightbox)

Nuovamente, verrà generato un `WKSnapshotRefreshBackgroundTask` in modo che l'app è possibile preparare (aggiornando i dati e l'interfaccia utente) prima dello snapshot:

[![](background-tasks-images/update08.png "L'app è possibile preparare aggiornando i dati e l'interfaccia utente prima dello snapshot")](background-tasks-images/update08.png#lightbox)

Quando l'app contrassegna il `WKSnapshotRefreshBackgroundTask` completato, il sistema verrà automaticamente uno snapshot di interfaccia utente dell'applicazione.

> [!IMPORTANT]
> È importante pianificare sempre un ` WKSnapshotRefreshBackgroundTask` dopo che l'app ha ricevuto nuovi dati e aggiornato relativa interfaccia utente o l'utente non verrà visualizzate le informazioni modificate.




Inoltre, quando l'utente riceve una notifica dall'app e tocca per portare l'app in primo piano, lo Snapshot deve essere aggiornato perché sta fungendo da nonché la schermata di avvio:

[![](background-tasks-images/update09.png "L'utente riceve una notifica dall'app e tocca per portare l'app in primo piano")](background-tasks-images/update09.png#lightbox)

Se sono trascorsi più di un'ora dall'utente ha interagito con un'applicazione watchOS, sia in grado di restituire lo stato predefinito. Lo stato predefinito può avere significati diversi a diverse App e, in base alla struttura di un'app, potrebbe non contenere uno stato predefinito affatto.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

In watchOS 3, Apple ha integrato watch connettività con l'API di aggiornamento in Background tramite il nuovo `WKWatchConnectivityRefreshBackgroundTask`. Con questa nuova funzionalità, un'app iPhone possibile distribuire dati aggiornati per la controparte app espressioni di controllo, mentre l'app watchOS è in esecuzione in background:

[![](background-tasks-images/update10.png "Un'app iPhone può fornire dati aggiornati per la controparte app espressioni di controllo, mentre l'app watchOS è in esecuzione in background")](background-tasks-images/update10.png#lightbox)

Avvia una complicazione Push, il contesto di App, l'invio di un file o l'aggiornamento delle informazioni utente dall'app iPhone consente di riattivare l'app dell'Apple Watch in background.

Quando l'applicazione di espressioni di controllo viene riattivato tramite un `WKWatchConnectivityRefreshBackgroundTask` sarà necessario utilizzare i metodi dell'API standard per ricevere i dati dall'app iPhone.

[![](background-tasks-images/update11.png "Il flusso di dati WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Verificare che la sessione è attivato.
2. Monitorare il nuovo `HasContentPending` , purché il valore è di proprietà `true`, l'app è ancora dati da elaborare. Come prima, l'app deve conservare l'attività fino a quando non ha terminato l'elaborazione di tutti i dati.
3. Quando non sono presenti ulteriori dati da elaborare (`HasContentPending = false`), contrassegnare l'attività è stata completata per tornare al sistema. In caso contrario, questo verrà scarico runtime sfondo assegnato dell'applicazione risultante in un report di arresto anomalo del sistema.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>Il ciclo di vita di API di sfondo

Tutte le parti dell'API di attività in Background nuovo inserirle, un set tipico di interazioni avrà un aspetto simile al seguente:

[![](background-tasks-images/update12.png "Il ciclo di vita di API di sfondo")](background-tasks-images/update12.png#lightbox)

1. In primo luogo, l'app watchOS pianifica uno sfondo a essere attivo in futuro come un certo punto dell'attività.
2. L'applicazione viene riattivato dal sistema e inviato a un'attività.
3. L'app elabora l'attività per completare il lavoro è stato richiesto.
4. Di conseguenza di elaborazione dell'attività, l'applicazione potrebbe essere necessario pianificare in background altre attività da completare più operazioni in futuro, ad esempio il download di contenuto più mediante un `NSUrlSession`.
5. L'app contrassegna il completamento dell'attività e lo restituisce al sistema.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>Utilizzo delle risorse in modo responsabile

È fondamentale che un'app watchOS si comporta in modo responsabile all'interno di questo ecosistema limitando il relativo svuotamento nelle risorse condivise del sistema.

Esaminare lo scenario seguente:

[![](background-tasks-images/update13.png "Un'app watchOS limita relativo svuotamento nelle risorse condivise del sistema")](background-tasks-images/update13.png#lightbox)

1. L'utente avvia un'applicazione watchOS 1:00 PM.
2. L'app pianifica un'attività per riattivare e scaricare nuovo contenuto in un'ora a 2:00 PM.
3. 1:50 PM l'utente apre nuovamente l'app che consente di aggiornare i dati e dell'interfaccia utente in questo momento.
4. Anziché consentire la riattivazione di attività dell'app tra 10 minuti, l'app deve ripianificare l'attività per l'esecuzione di un'ora in un secondo momento alle 2:50 PM.

Mentre ogni app è diversa, Apple suggerisce di trovare i modelli di utilizzo, ad esempio quelli indicati sopra, per risparmiare risorse di sistema.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Implementazione di attività in Background

Ai fini di esempio, questo documento verrà utilizzare l'app sportivi MonkeySoccer FALSO punteggi palloni segnala all'utente. 

Un quadro dello scenario di utilizzo tipico seguenti:

[![](background-tasks-images/update14.png "Lo scenario di utilizzo tipico")](background-tasks-images/update14.png#lightbox)

Team palloni Preferiti dell'utente la riproduzione di una grande corrispondenza da 7:00 PM 9:00 PM in modo che l'app dovrebbero essere verifica regolarmente il punteggio all'utente e decide in un intervallo di aggiornamento di 30 minuti.

1. L'utente apre l'app e consente di pianificare un'attività per l'aggiornamento in background 30 minuti in un secondo momento. L'API di Background consente un solo tipo di background attività sia in esecuzione in un determinato momento.
2. L'applicazione riceve l'attività e aggiorna i dati e dell'interfaccia utente, quindi le pianificazioni per un altro in background attività 30 minuti in un secondo momento. È importante che lo sviluppatore si ricorda di sfondo di un'altra attività di pianificazione o l'app non verrà mai essere nuovamente riattivato per ottenere ulteriori aggiornamenti.
3. Nuovamente, l'app riceve l'attività e aggiorna i dati, aggiorna la relativa interfaccia utente e pianifica sfondo di un'altra attività 30 minuti in un secondo momento.
4. Lo stesso processo si ripete di nuovo.
5. Lo sfondo ultima attività viene ricevuto e l'app nuovamente per aggiornare dati e dell'interfaccia utente. Poiché questo è il punteggio finale che non pianificare l'esecuzione di un nuovo aggiornamento in background. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Pianificazione per l'aggiornamento in Background

Dato lo scenario precedente, l'app MonkeySoccer possibile utilizzare il codice seguente per pianificare un aggiornamento in background:

```csharp
private void ScheduleNextBackgroundUpdate ()
{
    // Create a fire date 30 minutes into the future
    var fireDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

    // Create 
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("LastActiveDate"), NSDate.FromTimeIntervalSinceNow(0));
    userInfo.Add (new NSString ("Reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleBackgroundRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

Viene creato un nuovo `NSDate` 30 minuti in futuro quando l'app desidera essere riattivato e crea un `NSMutableDictionary` contenente i dettagli dell'attività di richiesta. Il `ScheduleBackgroundRefresh` metodo il `SharedExtension` viene usato per richiedere di essere pianificata l'attività.

Il sistema restituirà un `NSError` se non è in grado di pianificare l'attività richiesta.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Elaborazione dell'aggiornamento

Disconnettere la finestra di 5 minuti che mostra i passaggi necessari per l'aggiornamento da vicino:

[![](background-tasks-images/update15.png "La finestra di 5 minuti che mostra i passaggi necessari per l'aggiornamento")](background-tasks-images/update15.png#lightbox)

1. Al 19:30:02: 00 l'app è riattivato dal sistema e lo sfondo di aggiornamento attività specificato. La priorità di primo consiste nell'ottenere i punteggi più recenti dal server. Vedere [pianificazione un NSUrlSession](#Scheduling-a-NSUrlSession) sotto.
2. In 7:30:05 app completata nell'attività originale, il sistema consente di passare all'app di sospensione e continua scaricare i dati richiesti in background.
3. Quando il sistema viene completato il download, crea una nuova attività per riattivare l'app in modo che può elaborare le informazioni scaricate. Vedere [gestisce le attività in Background](#Handling-Background-Tasks) e [gestisce il completamento del Download](#Handling-the-Download-Completing) sotto. 
4. L'applicazione salva le informazioni aggiornate e contrassegna l'attività è stata completata. Lo sviluppatore può essere tentato di aggiornare l'interfaccia utente dell'app in questo momento, tuttavia Apple suggerisce di pianificazione di un'attività di Snapshot per la gestione del processo. Vedere [la pianificazione di un aggiornamento di Snapshot](#Scheduling-a-Snapshot-Update) sotto.
5. L'applicazione riceve l'attività di Snapshot, aggiorna l'interfaccia utente e contrassegna l'attività è stata completata. Vedere [la gestione di un aggiornamento di Snapshot](#Handling-a-Snapshot-Update) sotto.

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>Pianificazione di un NSUrlSession

Il codice seguente consente di pianificare il download dei punteggi più recenti:

```csharp
private void ScheduleURLUpdateSession ()
{
    // Create new configuration
    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.example.urlsession");

    // Create new session
    var backgroundSession = NSUrlSession.FromConfiguration (configuration);

    // Create and start download task
    var downloadTask = backgroundSession.CreateDownloadTask (new NSUrl ("https://example.com/gamexxx/currentScores.json"));
    downloadTask.Resume ();
}
```

Consente di configurare e crea un nuovo `NSUrlSession`, quindi utilizza tale sessione per creare un nuovo download attività utilizzando la `CreateDownloadTask` metodo. Chiama il `Resume` metodo del download di attività per avviare la sessione.

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>Gestione delle attività in Background

Eseguendo l'override di `HandleBackgroundTasks` metodo il `WKExtensionDelegate`, l'applicazione può gestire le attività in background in ingresso:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
        #endregion

        ...
        
        #region Public Methods
        public void CompleteTask (WKRefreshBackgroundTask task)
        {
            // Mark the task completed and remove from the collection
            task.SetTaskCompleted ();
            PendingTasks.Remove (task);
        }
        #endregion 

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request
            foreach (WKRefreshBackgroundTask task in backgroundTasks) {
                // Is this a background session task?
                var urlTask = task as WKUrlSessionRefreshBackgroundTask;
                if (urlTask != null) {
                    // Create new configuration
                    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

                    // Create new session
                    var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

                    // Keep track of all pending tasks
                    PendingTasks.Add (task);
                } else {
                    // Ensure that all tasks are completed
                    task.SetTaskCompleted ();
                }
            }
        }
        #endregion
        
        ...
    }
}
```

Il `HandleBackgroundTasks` metodo consente di scorrere tutte le attività che il sistema ha inviato l'app (in `backgroundTasks`) cercando un `WKUrlSessionRefreshBackgroundTask`. Se viene trovato uno, si riconnette alla sessione e allega un `NSUrlSessionDownloadDelegate` per gestire il completamento del download (vedere [gestisce il completamento di scaricare](#Handling-the-Download-Completing) sotto):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Handle per l'attività viene mantenuto fino al completamento aggiungendolo a una raccolta:

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Tutte le attività inviate all'app devono essere completato, per qualsiasi attività non è attualmente gestita, contrassegnarla come completata:

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>Gestisce il completamento del Download

L'app MonkeySoccer utilizza il seguente `NSUrlSessionDownloadDelegate` delegato per gestire il completamento del download ed elaborare i dati richiesti:

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class BackgroundSessionDelegate : NSUrlSessionDownloadDelegate
    {
        #region Computed Properties
        public ExtensionDelegate WatchExtensionDelegate { get; set; }

        public WKRefreshBackgroundTask Task { get; set; }
        #endregion

        #region Constructors
        public BackgroundSessionDelegate (ExtensionDelegate extensionDelegate, WKRefreshBackgroundTask task)
        {
            // Initialize
            this.WatchExtensionDelegate = extensionDelegate;
            this.Task = task;
        }
        #endregion

        #region Override Methods
        public override void DidFinishDownloading (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, NSUrl location)
        {
            // Handle the downloaded data
            ...

            // Mark the task completed
            WatchExtensionDelegate.CompleteTask (Task);

        }
        #endregion
    }
}
```

Durante l'inizializzazione, conserva un handle per entrambi i `ExtensionDelegate` e `WKRefreshBackgroundTask` che viene generato. Esegue l'override di `DidFinishDownloading` metodo per gestire il completamento del download. Utilizza quindi il `CompleteTask` metodo il `ExtensionDelegate` per informare il l'attività che è stata completata e rimuoverlo dalla raccolta di attività in sospeso. Vedere [gestisce le attività in Background](#Handling-Background-Tasks) sopra.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Pianificazione di un aggiornamento degli Snapshot

Il codice seguente consente di pianificare un'attività di Snapshot per aggiornare l'interfaccia utente con i punteggi più recenti:

```csharp
private void ScheduleSnapshotUpdate ()
{
    // Create a fire date of now
    var fireDate = NSDate.FromTimeIntervalSinceNow (0);

    // Create user info dictionary
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
    userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleSnapshotRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

Analogamente `ScheduleURLUpdateSession` metodo precedente, viene creato un nuovo `NSDate` desidera essere riattivato l'app e crea un `NSMutableDictionary` contenente i dettagli dell'attività di richiesta. Il `ScheduleSnapshotRefresh` metodo il `SharedExtension` viene usato per richiedere di essere pianificata l'attività.

Il sistema restituirà un `NSError` se non è in grado di pianificare l'attività richiesta.

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>La gestione di un aggiornamento degli Snapshot

Per gestire l'attività di Snapshot, il `HandleBackgroundTasks` (metodo) (vedere [gestisce le attività in Background](#Handling-Background-Tasks) sopra) viene modificato in modo simile al seguente:

```csharp
public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
{
    // Handle background request
    foreach (WKRefreshBackgroundTask task in backgroundTasks) {
        // Take action based on task type
        if (task is WKUrlSessionRefreshBackgroundTask) {
            var urlTask = task as WKUrlSessionRefreshBackgroundTask;

            // Create new configuration
            var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

            // Create new session
            var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

            // Keep track of all pending tasks
            PendingTasks.Add (task);
        } else if (task is WKSnapshotRefreshBackgroundTask) {
            var snapshotTask = task as WKSnapshotRefreshBackgroundTask;

            // Update UI
            ...

            // Create a expiration date 30 minutes into the future
            var expirationDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

            // Create user info dictionary
            var userInfo = new NSMutableDictionary ();
            userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
            userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

            // Mark task complete
            snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
        } else {
            // Ensure that all tasks are completed
            task.SetTaskCompleted ();
        }
    }
}
```

Il metodo di test per il tipo di attività in fase di elaborazione. Se si tratta di un `WKSnapshotRefreshBackgroundTask` accede all'attività:

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

Il metodo aggiorna l'interfaccia utente, quindi crea un `NSDate` per comunicare al sistema quando lo Snapshot verrà aggiornato. Crea un `NSMutableDictionary` con informazioni utente per descrivere le virgolette Snapshot operazione completata con queste informazioni e un nuovo Snapshot:

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

Inoltre, indica inoltre l'attività di Snapshot che non restituisca l'app allo stato predefinito (nel primo parametro). Le applicazioni che non includono alcun concetto di uno stato predefinito devono sempre essere impostata questa proprietà su `true`.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Funziona in modo efficiente

Come illustrato nell'esempio precedente della finestra di cinque minuti l'app MonkeySoccer impiegato per aggiornare i relativi punteggi, funziona in modo efficiente e utilizzando il nuovo watchOS 3 attività in background, l'app solo era attivo per un totale di 15 secondi: 

[![](background-tasks-images/update16.png "Solo l'app è stato attivo per un totale di 15 secondi")](background-tasks-images/update16.png#lightbox)

Ciò riduce l'impatto che avrà l'app sia le risorse disponibili Apple Watch che la durata della batteria e consente inoltre all'app di funzionano meglio con altre App in esecuzione su orologio.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Funzionamento della pianificazione

Mentre un'app watchOS 3 è in primo piano, è sempre pianificata per l'esecuzione e può eseguire qualsiasi tipo di elaborazione necessaria, ad esempio dati di aggiornamento o ridisegnare relativa interfaccia utente. Quando l'app passa in background, in genere viene sospeso dal sistema e tutte le operazioni di runtime sono interrotti. 

Mentre l'app è in background, può essere oggetto dal sistema per eseguire velocemente un'attività specifica. In tal caso, in watchOS 2, il sistema potrebbe essere temporaneamente riattivare un'app in background per eseguire operazioni come la gestione di una notifica di aspetto lungo o per aggiornare complicazione dell'app. In watchOS 3, esistono diversi modi di nuovo che un'applicazione può essere eseguita in background.

Mentre un'app è in background, il sistema impone limiti diversi su di essa:

- Per completare le attività, gli viene assegnato solo pochi secondi. Il sistema prende in considerazione non solo la quantità di tempo trascorso, ma anche quanta capacità di CPU dell'app viene utilizzato per derivare questo limite.
- Qualsiasi app che supera i limiti verranno terminate con i codici di errore seguente:
    - **CPU** - 0xc51bad01
    - **Time** - 0xc51bad02
- Il sistema verrà impone limiti diversi in base al tipo di cui è richiesta l'app per eseguire attività in Background. Ad esempio, `WKApplicationRefreshBackgroundTask` e `WKURLSessionRefreshBackgroundTask` attività vengono assegnate un runtime più lente rispetto ad altri tipi di attività in Background.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Problemi e gli aggiornamenti delle App

Oltre alle nuove attività in Background che Apple ha aggiunto a watchOS 3, complessità dell'applicazione un watchOS può avere un effetto su come e quando l'applicazione riceve gli aggiornamenti in background.

Le complicazioni sono piccoli elementi visivi che forniscono informazioni utili a colpo d'occhio. A seconda della faccia di espressioni di controllo selezionata, l'utente ha la possibilità di personalizzare un tipo di carattere di controllo con uno o più complicazione che può essere fornito da un'applicazione di espressioni di controllo in watchOS 3.

Se l'utente include uno dei problemi dell'app sul loro quadrante dell'orologio, offre l'app aggiornate seguenti vantaggi:

- Il sistema mantenere l'app in pronti per avviare lo stato, in cui tenta di avviare l'applicazione in background, li mantiene in memoria e fornisce tempo extra per aggiornare.
- Complicazioni sono garantiti almeno 50 aggiornamenti push al giorno.

Lo sviluppatore deve cercare sempre di creare accattivanti complicazioni per le App per indurre l'utente di aggiungerli al loro quadrante dell'orologio per i motivi elencati in precedenza.

In watchOS 2, complicazioni sono il mezzo principale di un'app ricevuto runtime mentre in background. In watchOS 3, un'app complicazione ancora essere garantita per più aggiornamenti all'ora di ricezione, tuttavia, può utilizzare `WKExtensions` per richiedere ulteriori runtime per aggiornare i relativi problemi.

Esaminare il codice seguente consente di aggiornare la complicazione dall'app iPhone connessa:

```csharp
using System;
using WatchConnectivity;
using UIKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
...

private void UpdateComplication ()
{

    // Get session and the number of remaining transfers
    var session = WCSession.DefaultSession;
    var transfers = session.RemainingComplicationUserInfoTransfers;

    // Create user info dictionary
    var iconattrs = new Dictionary<NSString, NSObject>
        {
            {new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0)},
            {new NSString ("reason"), new NSString ("UpdateScore")}
        };

    var userInfo = NSDictionary<NSString, NSObject>.FromObjectsAndKeys (iconattrs.Values.ToArray (), iconattrs.Keys.ToArray ());

    // Take action based on the number of transfers left
    if (transfers < 1) {
        // No transfers left, either attempt to send or inform
        // user of situation.
        ...
    } else if (transfers < 11) {
        // Running low on transfers, only send on important updates
        // else conserve for a significant change.
        ...
    } else {
        // Send data
        session.TransferCurrentComplicationUserInfo (userInfo);
    }
}
```

Usa il `RemainingComplicationUserInfoTransfers` proprietà del `WCSession` per visualizzare il numero di priorità alta trasferisce l'app ha lasciato il giorno e accetta azione in base al numero. Se l'app inizia a esaurirsi sui trasferimenti, può contenere sull'invio di aggiornamenti secondari e inviare solo informazioni quando viene apportata una modifica significativa.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>Pianificazione e ancoraggio

In watchOS 3, Apple ha aggiunto ancoraggio in cui gli utenti possono aggiungere le proprie App preferite e accedervi rapidamente. Quando l'utente preme il pulsante sul lato nel Apple Watch, verrà visualizzata una raccolta di snapshot di app bloccate. L'utente può scorrere verso sinistra o destra per cercare l'app desiderata, quindi toccare l'app per avviarla sostituendo lo snapshot con l'interfaccia dell'app in esecuzione.

[![](background-tasks-images/dock01.png "Ancoraggio")](background-tasks-images/dock01.png#lightbox)

Periodicamente, il sistema accetta gli snapshot dell'interfaccia utente dell'applicazione e utilizza questi snapshot per popolare i documenti. watchOS consente all'app l'opportunità di aggiornare il contenuto e dell'interfaccia utente prima di questo snapshot è stato creato.

App aggiunti ancoraggio possibile prevedere le operazioni seguenti:

- Riceveranno un minimo di un aggiornamento ogni ora. Questo include un'attività di aggiornamento di App e un'attività di Snapshot.
- I budget di aggiornamento viene distribuito tra tutte le app di ancoraggio. Pertanto minore App che è stato aggiunto l'utente, gli aggiornamenti più potenziali ogni app riceverà.
- L'app verrà mantenuto in memoria in modo l'app riprenderà rapidamente selezionato dall'alloggiamento di espansione.

L'ultimo app è stato eseguito l'utente verrà considerato il _usati di recente_ app e occupa l'ultimo slot nell'alloggiamento di espansione. Da qui, si può scegliere di aggiungere in modo permanente di ancoraggio. L'usati di recente verrà considerato come qualsiasi altra app preferita all'utente ha già aggiunti al ancoraggio.

> [!IMPORTANT]
> Le app che sono stati aggiunti solo alla schermata Home non verranno fornite una pianificazione regolare. Per ricevere una pianificazione regolare e in background aggiorna, un'app _deve_ da aggiungere al Dock.

Come indicato in precedenza in questo documento, gli snapshot sono molto importanti per watchOS 3 poiché funzionano come le immagini di anteprima e l'avvio per l'app. Se l'utente liquida in un'app di ancoraggio, venga espanso a schermo intero, immettere il primo piano e avviare l'esecuzione, pertanto è fondamentale che lo Snapshot può essere aggiornato.

Talvolta potrebbe essere quando il sistema decide che è necessario uno Snapshot aggiornato dell'interfaccia utente dell'applicazione. In tali situazioni, la richiesta di Snapshot verrà non inclusi nel conteggio per i budget di runtime dell'applicazione. Di seguito verrà attivata una richiesta di Snapshot di sistema:

- Un aggiornamento della sequenza temporale complicazione.
- Interazione dell'utente con la notifica di un'app.
- Passaggio dal primo piano per lo stato di Background.
- Dopo un'ora di essere in stato di Background, pertanto l'app può restituire lo stato predefinito.
- Quando watchOS primo avvio.

<a name="Best-Practices" />

## <a name="best-practices"></a>Suggerimenti 

Quando si lavora con attività in Background, Apple suggerisce le procedure consigliate seguenti:

- Pianificare le volte che l'app deve essere aggiornato. Ogni volta che viene eseguita l'app deve valutare nuovamente le esigenze future e modificare la pianificazione in base alle esigenze.
- Se il sistema invia un'attività di aggiornamento in Background e l'applicazione non richiede un aggiornamento, posticipare l'operazione fino a quando non è effettivamente necessario un aggiornamento.
- Prendere in considerazione tutte le possibilità di runtime disponibili in un'app:
    - Attivazione di ancoraggio e di primo piano.
    - Notifiche.
    - Aggiornamenti complicazione.
    - Aggiornamenti in background.
- Utilizzare `ScheduleBackgroundRefresh` per il runtime di uso generale in background, ad esempio:
    - Il polling del sistema per informazioni.
    - Pianificare futuro `NSURLSessions` di richiedere i dati in background. 
    - Transizioni tempo noto.
    - Attivare gli aggiornamenti complicazione.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Procedure consigliate per gli snapshot

Quando si lavora con gli aggiornamenti di Snapshot, Apple rende i suggerimenti seguenti:

- Invalida lo snapshot solo se è necessario, ad esempio, quando una modifica significativa del contenuto.
- Evitare di invalidamento Snapshot ad alta frequenza. Ad esempio, un'app di timer non deve aggiornare lo Snapshot al secondo, deve essere eseguita solo quando il timer è stata terminata.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Flusso di dati dell'App

Apple vengono indicate le operazioni seguenti per l'utilizzo di flusso di dati:

[![](background-tasks-images/update17.png "Diagramma di flusso di dati dell'App")](background-tasks-images/update17.png#lightbox)

Un evento esterno (ad esempio, la connettività di espressioni di controllo) viene attivato l'app. In questo modo l'app per aggiornare il modello di dati (che rappresenta lo stato corrente di App). Come risultato della modifica del modello di dati dell'app sarà necessario aggiornare la complessità, richiedere un nuovo Snapshot, avviare probabilmente uno sfondo `NSURLSession` pull più dati e pianificare ulteriormente in background viene aggiornato.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>Il ciclo di vita App

A causa di ancoraggio e la possibilità di aggiungere App preferite su di esso, presume Apple che gli utenti verranno spostati tra più App, molto più frequente, quindi quanto avveniva con watchOS 2. Di conseguenza, l'app deve essere pronto per gestire la modifica e spostarsi rapidamente tra gli stati di primo piano e sfondo.

Apple ha i suggerimenti seguenti:

- Verificare che l'app termina qualsiasi attività in background appena possibile dopo l'immissione di attivazione di primo piano.
- Assicurarsi di completare tutto il lavoro di primo piano prima di immettere lo sfondo chiamando `NSProcessInfo.PerformExpiringActivity`.
- Durante il test di un'app in watchOS simulatore, nessuno dei budget attività verranno applicate in modo che può aggiornare un'app come necessari per testare correttamente una funzionalità.
- Eseguire sempre test sull'hardware effettivo di Apple Watch per assicurarsi che l'app non è in esecuzione oltre il budget prima della pubblicazione a iTunes Connect.
- Apple suggerisce mantenendo l'Apple Watch in caricabatterie durante il test e debug.
- Verificare che sia freddo avvio e ripresa di un'app vengono testati accurati.
- Verificare che tutte le app attività da completare.
- Variare il numero di App che sono stati bloccati nella finestra di ancoraggio per testare le procedure e peggiori scenari casi.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati i miglioramenti che Apple ha watchOS e come possono essere utilizzati per aggiornare un'app di espressioni di controllo. In primo luogo, tutti i nuovi coperto Apple attività in Background sono state aggiunte nel watchOS 3. Quindi, interessato il ciclo di vita di API in Background e come implementare le attività in Background in un'app Xamarin di watchOS. Infine, coperto works pianificazione e assegnato alcune procedure consigliate.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
