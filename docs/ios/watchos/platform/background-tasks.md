---
title: Attività in Background in Xamarin watchOS
description: Questo documento descrive come usare le attività in background con watchOS in Xamarin, come ad esempio tipi di attività in background, l'utilizzo delle risorse, che implementa le attività in background, pianificazione, consigliate e altro ancora.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/13/2017
ms.openlocfilehash: 45886d787ecc40c9e11ce0c713ffa22819e29db2
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528819"
---
# <a name="watchos-background-tasks-in-xamarin"></a>Attività in Background in Xamarin watchOS

Con watchOS 3, esistono tre modi principali che un'app può mantenere le informazioni aggiornate: 

- Usando una delle diverse nuove attività in background. 
- Con uno dei relativi complicazioni nel quadrante dell'orologio (indicando molto tempo per aggiornare). 
- Dover Dock di nuovo il pin utente all'app (in cui il mantenuto in memoria e aggiornati spesso). 

## <a name="keeping-an-app-up-to-date"></a>Un'App per stare al passo

Prima di esaminare tutti i modi in cui che uno sviluppatore può mantenere i dati di un'app per watchOS e interfaccia utente correnti e aggiornate, in questa sezione verrà esaminati in un set tipico di modelli di uso e come un utente potrebbe spostare tra proprio iPhone e loro Apple Watch nel corso della giornata di base  l'ora del giorno e l'attività attualmente eseguite (ad esempio di Guida).

Vedere l'esempio seguente:

[![](background-tasks-images/update00.png "Modo in cui un utente potrebbe spostare tra loro Apple Watch nel corso della giornata e proprio iPhone")](background-tasks-images/update00.png#lightbox)

1. Al mattino, durante l'attesa in linea per un caffè, l'utente visualizza le notizie correnti sul proprio iPhone per alcuni minuti.
2. Prima di lasciare la bar, essi verificare rapidamente il meteo con una complicazione nel loro quadrante dell'orologio.
3. Prima della pausa pranzo, usano l'app mappe su iPhone per trovare un ristoranti nelle vicinanze e prenotare una prenotazione per soddisfare un client.
4. Durante una trasferta al ristorante, ricevono una notifica nel loro Apple Watch e un rapido riepilogo, sanno nomina pranzo viene eseguito in ritardo.
5. Nelle ore serali, usano l'app mappe su iPhone per controllare il traffico prima casa di Guida.
6. Lungo il percorso home, ricevono una notifica iMessage nella loro Apple Watch chiedendogli per prelevare alcuni latte e usano la funzionalità di risposta rapida per inviare la risposta "OK".

A causa di "quick glance" (meno di tre secondi) natura del modo in cui un utente è che desiderano utilizzare un'app per Apple Watch, sono in genere non è sufficienti per l'app recuperare le informazioni desiderate e aggiornare la relativa interfaccia utente prima di visualizzarli all'utente.

Con la nuova API Apple ha incluso in watchOS 3, è possibile pianificare l'app per un _in Background Aggiorna_ e avere le informazioni desiderate pronte prima che l'utente lo richiede. Eseguire l'esempio della complicazione meteo illustrato in precedenza:

[![](background-tasks-images/update01.png "Un esempio della complicazione meteo")](background-tasks-images/update01.png#lightbox)

1. Le pianificazioni di app può essere riattivato dal sistema in un momento specifico. 
2. L'app recupera le informazioni che è necessario per generare l'aggiornamento.
3. L'app verrà rigenerato relativa interfaccia utente in modo da riflettere i nuovi dati.
4. Quando l'utente glances nella complicazione dell'app, ha informazioni aggiornate senza che l'utente di dover attendere l'aggiornamento.

Come illustrato sopra, il sistema di watchOS viene attivato l'app usando una o più attività, dei quali dispone di un pool molto limitato disponibile:

[![](background-tasks-images/update02.png "Il sistema di watchOS viene attivato l'app usando una o più attività")](background-tasks-images/update02.png#lightbox)

Apple consiglia di utilizzare al meglio questa attività (perché è questo tipo una risorsa limitata all'app) tenendo premuto su di esso fino a quando l'app ha terminato il processo di aggiornamento stesso.

Il sistema distribuisce le attività chiamando il nuovo `HandleBackgroundTasks` metodo di `WKExtensionDelegate` delegare. Ad esempio:

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

Quando l'app ha terminato l'attività specificata, lo restituisce al sistema contrassegnandolo completata:

[![](background-tasks-images/update03.png "L'attività restituisce al sistema contrassegnandolo completata")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Nuova attività in Background

watchOS 3 introduce diverse attività in background che un'app può usare per aggiornare le proprie informazioni assicurando che sia il contenuto, l'utente deve prima aprire l'app, ad esempio:

- **Aggiornamento di App di sfondo** - il [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) attività consente all'app per aggiornarne lo stato in background. In genere includerà un'altra attività, ad esempio download di nuovi contenuti da internet usando un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **In background aggiorna Snapshot** - il [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) attività consente all'app di aggiornare il contenuto e l'interfaccia utente prima che il sistema accetta uno snapshot che verrà usato per popolare l'ancoraggio.
- **Espressioni di controllo della connettività di sfondo** - il [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) attività viene avviata per l'app alla ricezione dei dati in background da iPhone associato.
- **URL di sessione di sfondo** - il [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) attività viene avviata per l'app quando un trasferimento in background richiede l'autorizzazione o completato (con esito positivo o errore).

Queste attività verranno descritto dettagliatamente nelle sezioni seguenti.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

Il `WKApplicationRefreshBackgroundTask` è un'attività generica che può essere pianificata per l'App è attivato in una data futura:

[![](background-tasks-images/update04.png "Un WKApplicationRefreshBackgroundTask riattivato in futuro")](background-tasks-images/update04.png#lightbox)

All'interno del runtime dell'attività, l'app può eseguire qualsiasi tipo di elaborazione locale, ad esempio update una sequenza temporale complicazione o recuperare alcuni dati necessari con un `NSUrlSession`.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

Il sistema invierà un `WKURLSessionRefreshBackgroundTask` quando i dati ha terminato il download e pronto per essere elaborato dall'app:

[![](background-tasks-images/update05.png "WKURLSessionRefreshBackgroundTask quando i dati ha completato il download")](background-tasks-images/update05.png#lightbox)

Un'app non venga lasciata in esecuzione mentre i dati si scarica in background. Al contrario, l'app consente di pianificare la richiesta di dati, quindi viene sospeso e il download dei dati, reawakening solo l'app una volta completato il download da parte del sistema.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

In watchOS 3, Apple ha aggiunto l'ancoraggio in cui gli utenti possono bloccare le app preferite e accedervi rapidamente. Quando l'utente preme il pulsante sul lato l'Apple Watch, verrà visualizzata una raccolta di snapshot delle app bloccate. L'utente può scorrere verso destra o sinistra per trovare l'app desiderata, quindi toccare l'app per avviare il programma sostituendo lo Snapshot con l'interfaccia dell'app in esecuzione.

[![](background-tasks-images/update06.png "Sostituzione dello Snapshot con l'interfaccia di App in esecuzione")](background-tasks-images/update06.png#lightbox)

Il sistema crea periodicamente snapshot dell'interfaccia utente dell'app (mediante l'invio di un `WKSnapshotRefreshBackgroundTask`) e Usa tali snapshot per popolare l'ancoraggio. watchOS fornisce all'app l'opportunità di aggiornare il contenuto e dell'interfaccia utente prima di questo Snapshot viene acquisito.

Gli snapshot sono molto importanti watchOS 3 poiché funzionano come le immagini di anteprima sia il lancio per l'app. Se l'utente permette a un'app nel Dock, verrà espandere a schermo intero, immettere il primo piano e avviare l'esecuzione, pertanto è fondamentale che lo Snapshot di essere aggiornati:

[![](background-tasks-images/update07.png "Se l'utente permette a un'app nel Dock, verrà ampliato a schermo intero")](background-tasks-images/update07.png#lightbox)

Anche in questo caso, il sistema genererà un `WKSnapshotRefreshBackgroundTask` in modo che l'app è possibile preparare (aggiornando i dati e l'interfaccia utente) prima dello snapshot:

[![](background-tasks-images/update08.png "L'app può preparare aggiornando i dati e l'interfaccia utente prima che viene creato lo snapshot")](background-tasks-images/update08.png#lightbox)

Quando l'app contrassegna il `WKSnapshotRefreshBackgroundTask` completato, il sistema verrà automaticamente uno snapshot dell'interfaccia utente dell'app.

> [!IMPORTANT]
> È importante pianificare sempre un ` WKSnapshotRefreshBackgroundTask` dopo che l'app ha ricevuto i nuovi dati e aggiornata relativa interfaccia utente o l'utente non visualizzerà le informazioni modificate.




Inoltre, quando l'utente riceve una notifica dall'app e tocca per portare l'app in primo piano, lo Snapshot deve essere aggiornato perché sta fungendo da anche la schermata di avvio:

[![](background-tasks-images/update09.png "L'utente riceve una notifica dall'app e tocca per portare l'app in primo piano")](background-tasks-images/update09.png#lightbox)

Se sono trascorsi più di un'ora perché l'utente ha interagito con un'app watchOS, sarà in grado di restituire lo stato predefinito. Lo stato predefinito può avere diversi significati per diverse App e, in base alla struttura di un'app, potrebbe non contenere uno stato predefinito affatto.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

In watchOS 3, Apple ha integrato la connettività di espressioni di controllo con l'API di aggiornamento in Background tramite il nuovo `WKWatchConnectivityRefreshBackgroundTask`. Con questa nuova funzionalità, un'app per iPhone consegnano dati aggiornati controparte app watch, mentre l'app watchOS è in esecuzione in background:

[![](background-tasks-images/update10.png "Un'app per iPhone consegnano dati aggiornati per la controparte di app watch, mentre l'app watchOS è in esecuzione in background")](background-tasks-images/update10.png#lightbox)

Avvia una complicazione Push, il contesto di App, l'invio di un file o l'aggiornamento delle informazioni sull'utente dall'app iPhone consente di riattivare l'app Apple Watch in background.

Quando l'app watch viene riattivato tramite un `WKWatchConnectivityRefreshBackgroundTask` sarà necessario usare i metodi dell'API standard per ricevere i dati dall'app iPhone.

[![](background-tasks-images/update11.png "Il flusso di dati WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Assicurarsi che la sessione è attivato.
2. Monitorare la nuova `HasContentPending` proprietà fino a quando il valore è `true`, l'app contiene ancora dati da elaborare. Come prima, l'app deve mantenere l'attività fino a quando non ha terminato l'elaborazione di tutti i dati.
3. Quando non sono presenti più dati da elaborare (`HasContentPending = false`), contrassegnare le attività completate per restituirlo al sistema. Impossibile eseguire questa operazione verrà esaurito runtime assegnato in background dell'app risultante in un report di arresto anomalo del sistema.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>Il ciclo di vita di API in Background

Inserimento degli elementi della nuova API le attività in Background insieme, un set tipico di interazioni avrebbe un aspetto simile al seguente:

[![](background-tasks-images/update12.png "Il ciclo di vita di API in Background")](background-tasks-images/update12.png#lightbox)

1. In primo luogo, l'app watchOS consente di pianificare uno sfondo a essere attivo in futuro come un certo punto dell'attività.
2. L'app viene riattivato dal sistema e inviata un'attività.
3. L'app elabora l'attività per completare qualsiasi lavoro è stato richiesto.
4. Come risultato dell'elaborazione dell'attività, l'app potrebbe essere necessario pianificare in background altre attività da completare altre operazioni in futuro, ad esempio il download più contenuto usando un `NSUrlSession`.
5. L'app contrassegna il completamento dell'attività e lo restituisce al sistema.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>Utilizzo delle risorse responsabilmente la risorsa

È fondamentale che un'app watchOS si comporti responsabilmente la risorsa all'interno di questo ecosistema, limitando lo svuotamento su risorse condivise del sistema.

Esaminare lo scenario seguente:

[![](background-tasks-images/update13.png "Un'app watchOS limita relativo svuotamento su risorse condivise del sistema")](background-tasks-images/update13.png#lightbox)

1. L'utente avvia un'app watchOS in ore 13:00.
2. L'app pianifica un'attività per riattivare e scaricare nuovo contenuto in un'ora a 2:00 PM.
3. Ore 1:50 l'utente apre nuovamente l'app che consente di aggiornare i dati e dell'interfaccia utente in questo momento.
4. Invece di consentire la riattivazione di attività dell'app nuovamente in 10 minuti, l'app deve ripianificare l'attività per l'esecuzione di un'ora in un secondo momento alle 14.00: 50.

Anche se ogni app è diversa, suggerite da Apple trovare i modelli di utilizzo, come quelle illustrate sopra, per risparmiare risorse di sistema.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Implementazione di attività in Background

Ai fini di esempio, questo documento verrà utilizzato l'app di sport MonkeySoccer fittizio che segnala i punteggi di calcio all'utente. 

Esaminare il seguente scenario di utilizzo tipico:

[![](background-tasks-images/update14.png "Lo scenario di utilizzo tipico")](background-tasks-images/update14.png#lightbox)

Squadra di calcio Preferiti dell'utente ricopre una corrispondenza big data da 7 del Pomeriggio ore 21:00 in modo che l'app deve prevedere l'utente a essere controllare regolarmente il punteggio e decide in un intervallo di aggiornamento di 30 minuti.

1. L'utente apre l'app e consente di pianificare un'attività di aggiornamento in background per 30 minuti in un secondo momento. L'API di Background consente un solo tipo di sfondo attività sia in esecuzione in un determinato momento.
2. L'app riceve l'attività e aggiorna i dati e dell'interfaccia utente, quindi le pianificazioni per un altro in background attività 30 minuti in un secondo momento. È importante che lo sviluppatore si ricorda di pianificare in background di un'altra attività o l'app non verrà mai essere nuovamente riattivato per ottenere ulteriori aggiornamenti.
3. Anche in questo caso, l'app riceve l'attività e aggiorna i dati, gli aggiornamenti dell'interfaccia utente e pianifica in background di un'altra attività 30 minuti in un secondo momento.
4. Lo stesso processo si ripete.
5. Lo sfondo ultima attività viene ricevuto e l'app aggiorna nuovamente la data e l'interfaccia utente. Poiché questo è il punteggio finale che non pianificare l'esecuzione di un nuovo aggiornamento in background. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Pianificazione per l'aggiornamento in Background

Dato lo scenario precedente, l'app MonkeySoccer può usare il codice seguente per pianificare un aggiornamento in background per:

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

Crea una nuova `NSDate` 30 minuti in futuro quando l'app deve essere riattivato e crea un `NSMutableDictionary` contenente i dettagli dell'attività di richiesta. Il `ScheduleBackgroundRefresh` metodo di `SharedExtension` viene usato per richiedere l'attività di essere pianificata.

Il sistema restituirà un `NSError` se non è riuscito a pianificare l'attività di richiesta.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Elaborazione dell'aggiornamento

Quindi, esaminiamo più da vicino la finestra di 5 minuti che mostra i passaggi necessari per aggiornare il punteggio:

[![](background-tasks-images/update15.png "La finestra di 5 minuti che mostra i passaggi necessari per l'aggiornamento del punteggio")](background-tasks-images/update15.png#lightbox)

1. 7:30:02 PM l'app è riattivato dal sistema e dato l'attività in background di aggiornamento. La prima priorità è ottenere i punteggi più recenti dal server. Visualizzare [pianificazione di un NSUrlSession](#Scheduling-a-NSUrlSession) sotto.
2. In 7:30:05 l'app completa l'operazione originale, il sistema inserisce l'app alla modalità di sospensione e continua a scaricare i dati richiesti in background.
3. Quando il sistema di completamento del download, crea una nuova attività per riattivare l'app in modo che sia possibile elaborare le informazioni scaricate. Visualizzare [gestisce le attività in Background](#Handling-Background-Tasks) e [gestisce il completamento del Download](#Handling-the-Download-Completing) sotto. 
4. L'app Salva le informazioni aggiornate e contrassegna l'attività è stata completata. Lo sviluppatore può essere tentato di aggiornare l'interfaccia utente dell'app in questo momento, tuttavia suggerite da Apple pianificazione di un'attività di Snapshot per la gestione di tale processo. Visualizzare [pianificazione di un aggiornamento di Snapshot](#Scheduling-a-Snapshot-Update) sotto.
5. L'app riceve l'attività di Snapshot, aggiorna l'interfaccia utente e contrassegna l'attività è stata completata. Visualizzare [la gestione di un aggiornamento di Snapshot](#Handling-a-Snapshot-Update) sotto.

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>Pianificazione di un NSUrlSession

Il codice seguente è utilizzabile per pianificare il download dei punteggi più recenti:

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

Configura e si crea un nuovo `NSUrlSession`, quindi utilizza tale sessione per creare un nuovo download di attività usando il `CreateDownloadTask` (metodo). Chiama il `Resume` metodo del download di attività per avviare la sessione.

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>Attività in Background di gestione

Eseguendo l'override di `HandleBackgroundTasks` metodo del `WKExtensionDelegate`, l'app può gestire le attività in background in ingresso:

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

Il `HandleBackgroundTasks` metodo scorre tutte le attività che il sistema ha inviato l'app (in `backgroundTasks`) la ricerca di un `WKUrlSessionRefreshBackgroundTask`. Se viene trovata, viene parteciperà di nuovo la sessione e collega una `NSUrlSessionDownloadDelegate` per gestire il completamento del download (vedere [gestisce il completamento di scaricare](#Handling-the-Download-Completing) sotto):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Handle per l'attività viene mantenuto fino a quando non è stata completata, aggiungerlo a una raccolta:

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Tutte le attività inviate all'app necessario per essere completate, per tutte le attività attualmente non gestite, contrassegnarlo come completato:

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>La gestione del completamento del Download

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

Una volta inizializzata, mantiene un handle per entrambi i `ExtensionDelegate` e il `WKRefreshBackgroundTask` che viene generato. Viene eseguito l'override di `DidFinishDownloading` metodo per gestire il completamento del download. Quindi Usa il `CompleteTask` metodo del `ExtensionDelegate` per informare l'attività che è stata completata e rimuoverlo dalla raccolta di attività in sospeso. Visualizzare [gestisce le attività in Background](#Handling-Background-Tasks) sopra.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Pianificare un aggiornamento di Snapshot

Il codice seguente può essere utilizzato per pianificare un'attività di Snapshot per aggiornare l'interfaccia utente con i punteggi più recenti:

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

Analogamente `ScheduleURLUpdateSession` metodo precedente, viene creato un nuovo `NSDate` per quando l'app deve essere riattivato e crea un `NSMutableDictionary` contenente i dettagli dell'attività di richiesta. Il `ScheduleSnapshotRefresh` metodo di `SharedExtension` viene usato per richiedere l'attività di essere pianificata.

Il sistema restituirà un `NSError` se non è riuscito a pianificare l'attività di richiesta.

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>La gestione di un aggiornamento di Snapshot

Per gestire l'attività di Snapshot, il `HandleBackgroundTasks` metodo (vedere [gestisce le attività in Background](#Handling-Background-Tasks) sopra) viene modificato in modo simile al seguente:

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

Il metodo di test per il tipo di attività in fase di elaborazione. Se si tratta di un `WKSnapshotRefreshBackgroundTask` riesce ad accedere all'attività:

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

Il metodo aggiorna l'interfaccia utente, quindi crea un `NSDate` per indicare al sistema quando lo Snapshot sarà non aggiornato. Crea un `NSMutableDictionary` con le informazioni utente per descrivere le virgolette l'attività di Snapshot completata con queste informazioni e un nuovo Snapshot:

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

Inoltre, indica inoltre l'attività di Snapshot che l'app non restituisce lo stato predefinito (nel primo parametro). Le app che non includono alcun concetto di uno stato predefinito devono sempre impostare questa proprietà su `true`.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Funziona in modo efficiente

Come illustrato nell'esempio precedente della finestra di cinque minuti che l'app MonkeySoccer impiegato per aggiornare i relativi punteggi, funziona in modo efficiente e usando il nuovo watchOS 3 attività in background, l'app era solo attiva per un totale di 15 secondi: 

[![](background-tasks-images/update16.png "Solo l'app è stata attiva per un totale di 15 secondi")](background-tasks-images/update16.png#lightbox)

Questo riduce l'impatto che l'app avrà le risorse disponibili di Apple Watch sia la durata della batteria e consente anche all'app funzionare meglio con altre App in esecuzione sulle espressioni di controllo.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Funzionamento della pianificazione

Anche se un'app watchOS 3 è in primo piano, viene sempre pianificata per eseguire e possono eseguire qualsiasi tipo di elaborazione necessaria, ad esempio i dati di aggiornamento o essere ridisegnato usando la relativa interfaccia utente. Quando l'app passa in background, in genere viene sospeso dal sistema e tutte le operazioni di runtime vengono interrotti. 

Mentre l'app è in background, è possibile impostarlo dal sistema per eseguire rapidamente un'attività specifica. Pertanto, in watchOS 2, il sistema potrebbe essere temporaneamente riattivazione un'app in background per eseguire operazioni come la gestione di una notifica estesa o aggiornare complicazione dell'app. In watchOS 3, esistono diversi modi di nuovo che un'app può essere eseguita in background.

Quando un'app è in background, il sistema impone limiti diversi su di essa:

- Per completare qualsiasi attività specificata viene assegnato solo pochi secondi. Il sistema prende in considerazione non solo la quantità di tempo passato, ma anche quanta potenza della CPU dell'app sta utilizzando per derivare questo limite.
- Tutte le app che superano i limiti verranno terminate con i codici di errore seguente:
    - **CPU** - 0xc51bad01
    - **Tempo** -0xc51bad02
- Il sistema verrà impone limiti diversi in base al tipo di attività in Background ne ha richiesto l'esecuzione dell'app. Ad esempio, `WKApplicationRefreshBackgroundTask` e `WKURLSessionRefreshBackgroundTask` attività figurano runtimes leggermente più tempo rispetto ad altri tipi di attività in Background.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Problemi e gli aggiornamenti delle App

Oltre alle nuove attività in Background che Apple ha aggiunto a watchOS 3, complicazioni di un'app watchOS possono avere un effetto su come e quando l'app riceve gli aggiornamenti in background.

Le complicazioni sono piccoli elementi visivi che forniscono informazioni utili a colpo. A seconda del quadrante dell'orologio selezionata, l'utente ha la possibilità di personalizzare una watch face con uno o più complicazione che può essere fornito da un'app in watchOS 3.

Se l'utente include uno dei problemi dell'app nel loro quadrante dell'orologio, offre l'app aggiornate seguenti vantaggi:

- Il sistema mantenere l'app in un pronti per avviare lo stato, in cui tenta di avviare l'app in background, li conserva in memoria e fornisce tempo extra per aggiornare.
- Complicazioni sono garantite almeno 50 aggiornamenti di push al giorno.

Lo sviluppatore deve sempre sforzarci di creare accattivanti complicazioni per le App per indurre l'utente a aggiungendoli al loro quadrante dell'orologio per i motivi elencati in precedenza.

In watchOS 2 le complicazioni sono il modo principale di un'app ricevuto runtime mentre è in background. In watchOS 3, un'app complicazione sarà comunque assicurata a ricevere più gli aggiornamenti all'ora, tuttavia, può usare `WKExtensions` per richiedere ulteriori runtime per aggiornare le complicazioni.

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

Usa il `RemainingComplicationUserInfoTransfers` proprietà del `WCSession` per vedere quante con priorità alta trasferisce l'app ha lasciato per il giorno e quindi esegue un'azione in base al numero. Se l'app inizia a rallentare trasferimenti in, consente di rimandare l'invio di aggiornamenti minori e inviare solo informazioni quando viene apportata una modifica significativa.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>Pianificazione e il Dock

In watchOS 3, Apple ha aggiunto l'ancoraggio in cui gli utenti possono bloccare le app preferite e accedervi rapidamente. Quando l'utente preme il pulsante sul lato l'Apple Watch, verrà visualizzata una raccolta di snapshot delle app bloccate. L'utente può scorrere verso destra o sinistra per trovare l'app desiderata, quindi toccare l'app per avviare il programma sostituendo lo snapshot con l'interfaccia dell'app in esecuzione.

[![](background-tasks-images/dock01.png "Dock")](background-tasks-images/dock01.png#lightbox)

Periodicamente, il sistema acquisisce snapshot dell'interfaccia utente dell'app e Usa tali snapshot per popolare i documenti. watchOS fornisce all'app l'opportunità di aggiornare il contenuto e dell'interfaccia utente prima di questo snapshot viene acquisito.

Le app che sono stati aggiunti al dock possono prevedere quanto segue:

- Si riceverà un minimo di un aggiornamento ogni ora. Ciò include un'attività di aggiornamento di App e un'attività di Snapshot.
- Il budget di aggiornamento viene distribuito tra tutte le app nel Dock. Quindi, minore App che ha aggiunto l'utente, gli aggiornamenti più potenziali ogni app riceverà.
- L'app verrà conservato in memoria in modo che l'app verrà ripresa rapidamente quando si seleziona di ancoraggio.

L'ultima app è stata eseguita l'utente viene considerato il _usati di recente_ app e occupa l'ultimo slot nel Dock. Da qui, vi utente può scegliere di aggiungerla in modo permanente al Dock. I più usati di recente viene considerato come qualsiasi altra app preferita dell'utente ha già aggiunti al Dock.

> [!IMPORTANT]
> Le app che sono state aggiunte solo alla schermata Home non verranno fornite una pianificazione regolare. Per ricevere una pianificazione regolare e in background aggiorna, un'app _necessario_ da aggiungere al Dock.

Come indicato in precedenza in questo documento, gli snapshot sono molto importanti watchOS 3 poiché funzionano come le immagini di anteprima sia il lancio per l'app. Se l'utente permette a un'app nel Dock, verrà espandere a schermo intero, immettere il primo piano e avviare l'esecuzione, pertanto è fondamentale che essere aggiornati per lo Snapshot.

Talvolta potrebbe essere quando il sistema decide che è necessario uno Snapshot aggiornato dell'interfaccia utente dell'app. In tali situazioni, la richiesta di Snapshot non verrà calcolata rispetto al budget di runtime dell'app. Di seguito verrà attivata una richiesta di Snapshot di sistema:

- Un aggiornamento della sequenza temporale complicazione.
- Interazione dell'utente con la notifica dell'app.
- Passaggio dal primo piano allo stato in Background.
- Dopo un'ora di essere in stato di Background, pertanto, l'app può restituire allo stato predefinito.
- Quando watchOS primo avvio.

<a name="Best-Practices" />

## <a name="best-practices"></a>Suggerimenti 

Le procedure consigliate seguenti suggerite da Apple quando si lavora con le attività in Background:

- Pianificare le volte che l'app deve essere aggiornato. Ogni volta che viene eseguita l'app dovrebbe valutare nuovamente le esigenze future e regolare la pianificazione in base alle esigenze.
- Se un'attività di aggiornamento in Background viene inviato dal sistema e l'app non richiede un aggiornamento, posticipare il lavoro fino a quando non è effettivamente necessario un aggiornamento.
- Prendere in considerazione tutte le opportunità di runtime disponibili per un'app:
    - Attivazione di ancoraggio e di primo piano.
    - Notifiche.
    - Aggiornamenti complicazione.
    - Aggiornamenti in background.
- Usare `ScheduleBackgroundRefresh` per la fase di esecuzione in background per utilizzo generico, ad esempio:
    - Il sistema per le informazioni di polling.
    - Pianificare future `NSURLSessions` di richiedere i dati in background. 
    - Transizioni di tempo noto.
    - Attivazione degli aggiornamenti complicazione.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Le procedure consigliate per gli snapshot

Quando si lavora con gli aggiornamenti di Snapshot, Apple rende i suggerimenti seguenti:

- Invalidare gli snapshot solo quando necessario, ad esempio, quando viene apportata una modifica significativa del contenuto.
- Evitare di invalidamento dei dati ad alta frequenza dello Snapshot. Ad esempio, un'app di timer non deve aggiornare lo Snapshot di ogni secondo, deve essere eseguita solo quando il timer è terminata.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Flusso di dati delle App

Apple consiglia quanto segue per l'uso di flusso di dati:

[![](background-tasks-images/update17.png "Diagramma di flusso di dati delle App")](background-tasks-images/update17.png#lightbox)

Un evento esterno (ad esempio, la connettività di espressioni di controllo) viene attivato l'app. In tal modo l'app per aggiornare il modello di dati (che rappresenta lo stato corrente delle App). Di conseguenza della modifica del modello di dati dell'app sarà necessario aggiornare le complicazioni, richiedere un nuovo Snapshot, possibilmente avviare uno sfondo `NSURLSession` pull più dati e pianificare ulteriormente in background viene aggiornato.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>Il ciclo di vita App

A causa di ancoraggio e la possibilità di aggiungere App preferite su di esso, Apple a ritenere che gli utenti verranno spostate tra più App, maggior parte dei casi, quindi supportavano con watchOS 2. Di conseguenza, l'app deve essere pronta a gestire questa modifica e spostarsi rapidamente tra gli stati di primo piano e sfondo.

Apple ha i suggerimenti seguenti:

- Assicurarsi che l'app termina qualsiasi attività in background presto in base all'immissione di attivazione di primo piano.
- Assicurarsi di completare tutto il lavoro in primo piano prima di entrare in background chiamando `NSProcessInfo.PerformExpiringActivity`.
- Durante il test di un'app nel simulatore watchOS, nessuno dei budget di attività verranno applicate in modo che un'app può aggiornare come necessario per verificare correttamente una funzionalità.
- Testare sempre sulll'hardware effettivo di Apple Watch per assicurarsi che l'app non è in esecuzione oltre i relativi budget prima della pubblicazione a iTunes Connect.
- Mantenere l'Apple Watch on caricabatterie durante il test e debug suggerite da Apple.
- Assicurarsi che sia ad accesso sporadico l'avvio e ripresa di un'app viene testate.
- Verificare che vengono completate tutte le attività di app.
- Variare il numero di App che sono stati bloccati nel Dock per testare il migliori e peggiori caso di scenari.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i miglioramenti di che Apple ha tentato di watchOS e su come possono essere usati per mantenere aggiornato una app watch. In primo luogo, sia nascosto tutte le nuove aggiunte nella watchOS 3 Apple di attività in Background. Quindi, illustrato il ciclo di vita di API in Background e come implementare le attività in Background in un'app Xamarin di watchOS. Infine, trattati come pianificazione works e ha alcune procedure consigliate.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
