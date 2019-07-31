---
title: Attività in background di watchos in Novell
description: Questo documento descrive come usare le attività in background con watchos in Novell, esaminando i tipi di attività in background, usando le risorse, implementando attività in background, pianificazione, procedure consigliate e altro ancora.
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/13/2017
ms.openlocfilehash: aa5d257fd08c82e17b4ecbc5ca9a9ab67742e8e7
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644568"
---
# <a name="watchos-background-tasks-in-xamarin"></a>Attività in background di watchos in Novell

Con watchos 3, è possibile tenere aggiornate le informazioni di un'app Watch in tre modi principali: 

- Utilizzando una delle numerose nuove attività in background. 
- Una delle sue complicazioni sul quadrante dell'orologio (con un tempo aggiuntivo per l'aggiornamento). 
- Il PIN dell'utente per l'app viene aggiunto al nuovo dock (dove è stato mantenuto in memoria e aggiornato spesso). 

## <a name="keeping-an-app-up-to-date"></a>Mantenimento di un'app aggiornata

Prima di illustrare tutti i modi in cui uno sviluppatore può tenere aggiornati e aggiornati i dati e l'interfaccia utente di un'app watchos, in questa sezione verrà esaminato un set tipico di modelli di utilizzo e il modo in cui un utente può spostarsi tra l'iPhone e i relativi Apple Watch durante il giorno in base al  ora del giorno e attività attualmente in esecuzione, ad esempio la guida.

Vedere l'esempio seguente:

[![](background-tasks-images/update00.png "Modo in cui un utente può spostarsi tra il proprio iPhone e i relativi Apple Watch durante la giornata")](background-tasks-images/update00.png#lightbox)

1. Al mattino, in attesa di un caffè, l'utente Sfoglia le notizie correnti sul proprio iPhone per diversi minuti.
2. Prima di uscire dalla caffetteria, i colleghi controllano rapidamente il meteo con una complicazione sul quadrante dell'orologio.
3. Prima del pranzo, usano l'app Maps sull'iPhone per trovare un ristorante vicino e prenotare una prenotazione per incontrare un client.
4. Durante il viaggio verso il ristorante, ricevono una notifica sul proprio Apple Watch e, con una rapida occhiata, conoscono l'appuntamento del pranzo in ritardo.
5. Nella sera, usano l'app Maps sull'iPhone per verificare il traffico prima di guidare la Home page.
6. Nel modo iniziale, ricevono una notifica di iMessage per il Apple Watch chiedere loro di prendere il latte e usano la funzionalità di risposta rapida per inviare la risposta "OK".

A causa della natura "Riepilogo rapido" (meno di tre secondi) del modo in cui un utente vuole usare un'app Apple Watch, in genere non è sufficiente tempo per consentire all'app di recuperare le informazioni desiderate e aggiornare la relativa interfaccia utente prima di visualizzarla all'utente.

Con le nuove API fornite da Apple in watchos 3, l'app può pianificare un aggiornamento in _background_ e avere le informazioni desiderate pronte prima che l'utente lo richieda. Prendere l'esempio della complicazione del tempo descritta sopra:

[![](background-tasks-images/update01.png "Esempio della complicazione del tempo")](background-tasks-images/update01.png#lightbox)

1. Pianificazioni dell'app che devono essere riattivate dal sistema in un momento specifico. 
2. L'app recupera le informazioni necessarie per generare l'aggiornamento.
3. L'app rigenera l'interfaccia utente in modo da riflettere i nuovi dati.
4. Quando l'utente Guarda la complicazione dell'app, presenta informazioni aggiornate senza che l'utente debba attendere l'aggiornamento.

Come illustrato in precedenza, il sistema watchos riattiva l'app utilizzando una o più attività, di cui è disponibile un pool molto limitato:

[![](background-tasks-images/update02.png "Il sistema watchos riattiva l'app utilizzando una o più attività")](background-tasks-images/update02.png#lightbox)

Apple suggerisce di sfruttare al meglio questa attività (poiché è una risorsa di questo tipo limitata per l'app) tenendola aggiornata fino a quando l'app non ha completato il processo di aggiornamento.

Il sistema recapita queste attività chiamando il nuovo `HandleBackgroundTasks` metodo `WKExtensionDelegate` del delegato. Ad esempio:

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

Al termine dell'attività specificata, l'app la restituisce al sistema contrassegnando il completamento:

[![](background-tasks-images/update03.png "L'attività ritorna al sistema contrassegnando l'operazione completata")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>Nuove attività in background

watchos 3 introduce diverse attività in background che un'app può usare per aggiornare le informazioni assicurando che il contenuto dell'utente sia necessario prima di aprire l'app, ad esempio:

- **Aggiornamento dell'app in background** : l'attività [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) consente all'app di aggiornare lo stato in background. Questo include in genere un'altra attività, ad esempio il download di nuovo contenuto da Internet usando un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Aggiornamento dello snapshot in background** : l'attività [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) consente all'app di aggiornare sia il contenuto che l'interfaccia utente prima che il sistema acquisisca uno snapshot che verrà usato per popolare il Dock.
- **Connettività di controllo in background** : l'attività [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) viene avviata per l'app quando riceve dati in background dall'iPhone abbinato.
- **Sessione URL in background** : l'attività [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) viene avviata per l'app quando un trasferimento in background richiede l'autorizzazione o il completamento (correttamente o in errore).

Queste attività verranno descritte in dettaglio nelle sezioni riportate di seguito.

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

`WKApplicationRefreshBackgroundTask` È un'attività generica che può essere pianificata in modo che l'app venga riattivata in una data futura:

[![](background-tasks-images/update04.png "Un WKApplicationRefreshBackgroundTask riattivato in una data futura")](background-tasks-images/update04.png#lightbox)

All'interno del runtime dell'attività, l'app può eseguire qualsiasi tipo di elaborazione locale, ad esempio aggiornare una sequenza temporale complicata o recuperare alcuni dati necessari `NSUrlSession`con un.


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

Il sistema invierà un' `WKURLSessionRefreshBackgroundTask` operazione al termine del download dei dati e sarà pronta per l'elaborazione da parte dell'app:

[![](background-tasks-images/update05.png "WKURLSessionRefreshBackgroundTask al termine del download dei dati")](background-tasks-images/update05.png#lightbox)

Un'app non viene lasciata in esecuzione mentre i dati vengono scaricati in background. L'app pianifica invece la richiesta di dati, quindi viene sospesa e il sistema gestisce il download dei dati, riattivando l'app solo al termine del download.

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

In watchos 3, Apple ha aggiunto il Dock in cui gli utenti possono aggiungere le proprie app preferite e accedervi rapidamente. Quando l'utente preme il pulsante laterale nella Apple Watch, viene visualizzata una raccolta di snapshot dell'app bloccati. L'utente può scorrere rapidamente verso sinistra o verso destra per trovare l'app desiderata, quindi toccare l'app per avviarla sostituendo lo snapshot con l'interfaccia dell'app in esecuzione.

[![](background-tasks-images/update06.png "Sostituzione dello snapshot con l'interfaccia delle app in esecuzione")](background-tasks-images/update06.png#lightbox)

Il sistema acquisisce periodicamente gli snapshot dell'interfaccia utente dell'app (inviando `WKSnapshotRefreshBackgroundTask`un oggetto) e usa tali snapshot per popolare il Dock. watchos offre all'app la possibilità di aggiornare il contenuto e l'interfaccia utente prima di eseguire lo snapshot.

Gli snapshot sono molto importanti in watchos 3 poiché funzionano sia come immagini di anteprima che di avvio per l'app. Se l'utente stabilisce un'app nel Dock, si espanderà a schermo intero, entrerà in primo piano e inizierà a funzionare, quindi è indispensabile che lo snapshot sia aggiornato:

[![](background-tasks-images/update07.png "Se l'utente stabilisce un'app nel Dock, verrà espansa a schermo intero")](background-tasks-images/update07.png#lightbox)

Anche in questo caso, il sistema `WKSnapshotRefreshBackgroundTask` emetterà un in modo che l'app possa preparare (aggiornando i dati e l'interfaccia utente) prima di eseguire lo snapshot:

[![](background-tasks-images/update08.png "L'app può prepararsi aggiornando i dati e l'interfaccia utente prima che venga eseguita la snapshot")](background-tasks-images/update08.png#lightbox)

Quando l'app contrassegna il `WKSnapshotRefreshBackgroundTask` completamento, il sistema crea automaticamente uno snapshot dell'interfaccia utente dell'app.

> [!IMPORTANT]
> È importante pianificare sempre un `WKSnapshotRefreshBackgroundTask` dopo che l'app ha ricevuto nuovi dati e aggiornato la relativa interfaccia utente oppure l'utente non visualizzerà le informazioni modificate.




Inoltre, quando l'utente riceve una notifica dall'app e la tocca per portare l'app in primo piano, lo snapshot deve essere aggiornato perché funge anche da schermata di avvio:

[![](background-tasks-images/update09.png "L'utente riceve una notifica dall'app e la tocca per portare l'app in primo piano")](background-tasks-images/update09.png#lightbox)

Se l'utente ha avuto più di un'ora dall'interazione con un'app watchos, sarà in grado di tornare al suo stato predefinito. Lo stato predefinito può essere diverso per le diverse app e, in base alla progettazione di un'app, potrebbe non avere uno stato predefinito.

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

In watchos 3, Apple ha integrato la connettività Watch con l'API di aggiornamento in background `WKWatchConnectivityRefreshBackgroundTask`tramite la nuova. Grazie a questa nuova funzionalità, un'app iPhone può fornire dati aggiornati alla controparte dell'app Watch, mentre l'app watchos viene eseguita in background:

[![](background-tasks-images/update10.png "Un'app iPhone può fornire dati aggiornati alla controparte dell'app Watch, mentre l'app watchos viene eseguita in background")](background-tasks-images/update10.png#lightbox)

L'avvio di un push di complicazione, il contesto dell'app, l'invio di un file o l'aggiornamento delle informazioni utente dall'app iPhone riattiveranno l'app Apple Watch in background.

Quando l'app Watch viene riattivata `WKWatchConnectivityRefreshBackgroundTask` tramite un, sarà necessario usare i metodi API standard per ricevere i dati dall'app iPhone.

[![](background-tasks-images/update11.png "Flusso di dati WKWatchConnectivityRefreshBackgroundTask")](background-tasks-images/update11.png#lightbox)

1. Verificare che la sessione sia stata attivata.
2. Monitorare la nuova `HasContentPending` proprietà purché il valore sia `true`, l'app dispone ancora di dati da elaborare. Come prima, l'app deve tenere l'attività fino a quando non termina l'elaborazione di tutti i dati.
3. Quando non sono più disponibili dati da elaborare (`HasContentPending = false`), contrassegnare l'attività completata per restituirla al sistema. In caso contrario, verrà esaurito il runtime di sfondo assegnato dall'app, ottenendo una segnalazione di arresto anomalo.

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>Ciclo di vita dell'API in background

Inserendo tutti i componenti della nuova API attività in background insieme, un set di interazioni tipico sarà simile al seguente:

[![](background-tasks-images/update12.png "Ciclo di vita dell'API in background")](background-tasks-images/update12.png#lightbox)

1. In primo luogo, l'app watchos pianifica un'attività in background da ridestare come un certo punto in futuro.
2. L'app viene riattivata dal sistema e invia un'attività.
3. L'app elabora l'attività per completare qualsiasi operazione richiesta.
4. In seguito all'elaborazione dell'attività, è possibile che l'app debba pianificare più attività in background per completare più lavoro in futuro, ad esempio per scaricare più contenuti usando `NSUrlSession`un.
5. L'app contrassegna l'attività completata e la restituisce al sistema.

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>Uso responsabile delle risorse

È fondamentale che un'app watchos si comportano in modo responsabile in questo ecosistema limitando lo svuotamento sulle risorse condivise del sistema.

Esaminare lo scenario seguente:

[![](background-tasks-images/update13.png "Un'app watchos limita lo svuotamento sulle risorse condivise del sistema")](background-tasks-images/update13.png#lightbox)

1. L'utente avvia un'app watchos alle ore 1:00.
2. L'app pianifica un'attività per la riattivazione e il download di nuovo contenuto in un'ora alle 2:00 PM.
3. Alle 1:50 PM l'utente riapre l'app che consente di aggiornare i dati e l'interfaccia utente in questo momento.
4. Anziché riattivare l'app in 10 minuti, l'app deve ripianificare l'esecuzione dell'attività in un secondo momento alle 2:50.

Anche se ogni app è diversa, Apple suggerisce di trovare modelli di utilizzo, come quelli illustrati in precedenza, per facilitare la conservazione delle risorse di sistema.

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>Implementazione di attività in background

Per questo esempio, in questo documento verrà usata la falsa app MonkeySoccer Sports che segnala i punteggi di calcio all'utente. 

Esaminare lo scenario di utilizzo tipico seguente:

[![](background-tasks-images/update14.png "Scenario di utilizzo tipico")](background-tasks-images/update14.png#lightbox)

Il team di calcio preferito dell'utente sta giocando una grande corrispondenza tra le 7:00 e 9:00 PM, quindi l'app dovrebbe aspettarsi che l'utente verifichi regolarmente il punteggio e decida un intervallo di aggiornamento di 30 minuti.

1. L'utente apre l'app e pianifica un'attività per l'aggiornamento in background 30 minuti più tardi. L'API in background consente l'esecuzione di un solo tipo di attività in background in un determinato momento.
2. L'app riceve l'attività e ne aggiorna i dati e l'interfaccia utente, quindi pianifica un'altra attività in background 30 minuti più tardi. È importante che lo sviluppatore ricordi di pianificare un'altra attività in background o che l'app non venga mai riattivata per ottenere altri aggiornamenti.
3. Anche in questo caso, l'app riceve l'attività e ne aggiorna i dati, aggiorna l'interfaccia utente e pianifica un'altra attività in background 30 minuti più tardi.
4. Lo stesso processo si ripete nuovamente.
5. Viene ricevuta l'ultima attività in background e l'app Aggiorna i dati e l'interfaccia utente. Poiché si tratta del punteggio finale, non pianifica per un nuovo aggiornamento in background. 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>Pianificazione per l'aggiornamento in background

Dato lo scenario precedente, l'app MonkeySoccer può usare il codice seguente per pianificare un aggiornamento in background:

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

Crea un nuovo `NSDate` 30 minuti in futuro quando l'app vuole essere risvegliata e crea un oggetto `NSMutableDictionary` per contenere i dettagli dell'attività richiesta. Il `ScheduleBackgroundRefresh` metodo`SharedExtension` di viene utilizzato per richiedere che l'attività venga pianificata.

Se non è stato possibile `NSError` pianificare l'attività richiesta, il sistema restituisce un valore.

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>Elaborazione dell'aggiornamento

A questo punto, esaminare più in dettaglio la finestra di 5 minuti che mostra i passaggi necessari per aggiornare il Punteggio:

[![](background-tasks-images/update15.png "La finestra di 5 minuti che mostra i passaggi necessari per aggiornare il Punteggio")](background-tasks-images/update15.png#lightbox)

1. Alle 7:30:02 PM l'app viene riattivata dal sistema e data l'attività Aggiorna in background. La prima priorità è ottenere i punteggi più recenti dal server. Vedere [pianificazione di un NSUrlSession di](#Scheduling-a-NSUrlSession) seguito.
2. Al 7:30:05 l'app completa l'attività originale, il sistema inserisce l'app in modalità sospensione e continua a scaricare i dati richiesti in background.
3. Al termine del download, il sistema crea una nuova attività per riattivare l'app in modo che possa elaborare le informazioni scaricate. Vedere [gestione delle attività in background](#Handling-Background-Tasks) e [gestione del download completato](#Handling-the-Download-Completing) di seguito. 
4. L'app salva le informazioni aggiornate e contrassegna l'attività come completata. Lo sviluppatore può essere tentato di aggiornare l'interfaccia utente dell'app in questo momento, ma Apple suggerisce di pianificare un'attività snapshot per gestire il processo. Vedere [pianificazione di un aggiornamento di uno snapshot di](#Scheduling-a-Snapshot-Update) seguito.
5. L'app riceve l'attività snapshot, aggiorna l'interfaccia utente e contrassegna l'attività completata. Vedere [gestione di un aggiornamento dello snapshot di](#Handling-a-Snapshot-Update) seguito.

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>Pianificazione di un NSUrlSession

Il codice seguente può essere usato per pianificare il download dei punteggi più recenti:

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

Configura e crea un nuovo `NSUrlSession`oggetto, quindi usa tale sessione per creare una nuova attività di download usando il `CreateDownloadTask` metodo. Viene chiamato il `Resume` metodo dell'attività di download per avviare la sessione.

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>Gestione delle attività in background

Eseguendo l'override del `HandleBackgroundTasks` metodo `WKExtensionDelegate`di, l'app può gestire le attività in background in ingresso:

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

Il `HandleBackgroundTasks` metodo esegue il ciclo di tutte le attività che il sistema ha inviato all'app ( `backgroundTasks`in) cercando `WKUrlSessionRefreshBackgroundTask`. Se ne viene individuato uno, viene riunito la sessione e viene collegato `NSUrlSessionDownloadDelegate` un per gestire il completamento del download (vedere [gestione del download completato](#Handling-the-Download-Completing) di seguito):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

Mantiene un handle dell'attività fino a quando non viene completato aggiungendolo a una raccolta:

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

Tutte le attività inviate all'app devono essere completate, per qualsiasi attività non gestita, contrassegnarla come completata:

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>Gestione del completamento del download

L'app MonkeySoccer usa il delegato `NSUrlSessionDownloadDelegate` seguente per gestire il completamento del download ed elaborare i dati richiesti:

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

Quando viene inizializzato, mantiene un handle per l' `ExtensionDelegate` oggetto e `WKRefreshBackgroundTask` l'oggetto che lo ha generato. Esegue l'override `DidFinishDownloading` del metodo per gestire il completamento del download. USA quindi il `CompleteTask` metodo `ExtensionDelegate` di per informare l'attività che è stata completata e rimuoverla dalla raccolta di attività in sospeso. Vedere [gestione delle attività in background](#Handling-Background-Tasks) sopra.

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>Pianificazione di un aggiornamento di snapshot

Il codice seguente può essere usato per pianificare un'attività snapshot per aggiornare l'interfaccia utente con i punteggi più recenti:

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

Analogamente al `NSDate` `NSMutableDictionary` metodo precedente, viene creato un nuovo oggetto per quando l'app vuole essere risvegliata e crea un oggetto per contenere i dettagli dell'attività richiesta. `ScheduleURLUpdateSession` Il `ScheduleSnapshotRefresh` metodo`SharedExtension` di viene utilizzato per richiedere che l'attività venga pianificata.

Se non è stato possibile `NSError` pianificare l'attività richiesta, il sistema restituisce un valore.

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>Gestione di un aggiornamento dello snapshot

Per gestire l'attività snapshot, il `HandleBackgroundTasks` metodo (vedere [gestione delle attività in background](#Handling-Background-Tasks) precedente) viene modificato in modo analogo al seguente:

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

Il metodo verifica il tipo di attività in fase di elaborazione. Se è un `WKSnapshotRefreshBackgroundTask` , ottiene l'accesso all'attività:

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

Il metodo aggiorna l'interfaccia utente, quindi crea `NSDate` un oggetto per indicare al sistema quando lo snapshot sarà non aggiornato. Viene creato un `NSMutableDictionary` oggetto con le informazioni sull'utente per descrivere il nuovo snapshot e viene contrassegnata l'attività snapshot completata con queste informazioni:

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

Inoltre, indica all'attività snapshot che l'app non ritorna allo stato predefinito (nel primo parametro). Le app che non hanno un concetto di stato predefinito devono sempre impostare questa proprietà `true`su.

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>Lavorare in modo efficiente

Come illustrato nell'esempio precedente della finestra di cinque minuti usata dall'app MonkeySoccer per aggiornare i punteggi, lavorando in modo efficiente e usando le nuove attività in background di watchos 3, l'app è stata attiva solo per un totale di 15 secondi: 

[![](background-tasks-images/update16.png "L'app è stata attiva solo per un totale di 15 secondi")](background-tasks-images/update16.png#lightbox)

In questo modo si riduce l'incidenza dell'app sulle risorse Apple Watch disponibili e sulla durata della batteria, oltre a consentire all'app di funzionare meglio con altre app in esecuzione nell'orologio.

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>Funzionamento della pianificazione

Mentre un'app watchos 3 è in primo piano, è sempre pianificata per l'esecuzione e può eseguire qualsiasi tipo di elaborazione, ad esempio aggiornare i dati o ricreare l'interfaccia utente. Quando l'app viene spostata in background, viene in genere sospesa dal sistema e tutte le operazioni di runtime vengono interrotte. 

Mentre l'app è in background, è possibile che il sistema sia indirizzato al sistema per eseguire rapidamente un'attività specifica. Quindi, in watchos 2, il sistema potrebbe riattivare temporaneamente un'app in background per eseguire operazioni come la gestione di una notifica a lungo aspetto o l'aggiornamento della complicazione dell'app. In watchos 3 sono disponibili diversi nuovi modi per eseguire un'app in background.

Mentre un'app è in background, il sistema impone diversi limiti:

- Per completare una determinata attività, vengono forniti solo pochi secondi. Il sistema prende in considerazione non solo la quantità di tempo passata, ma anche la quantità di energia CPU che l'app sta consumando per derivare questo limite.
- Qualsiasi app che supera i limiti verrà terminata con i codici di errore seguenti:
    - **CPU** - 0xc51bad01
    - **Time** -0xc51bad02
- Il sistema imposterà limiti diversi in base al tipo di attività in background che ha richiesto all'app di eseguire. Ad esempio, `WKApplicationRefreshBackgroundTask` le `WKURLSessionRefreshBackgroundTask` attività e vengono assegnate a runtime leggermente più lunghi rispetto ad altri tipi di attività in background.

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>Complicazioni e aggiornamenti delle app

Oltre alle nuove attività in background aggiunte da Apple a watchos 3, le complicazioni di un'app watchos possono avere un effetto su come e quando l'app riceve gli aggiornamenti in background.

Le complicazioni sono elementi visivi di piccole dimensioni che forniscono informazioni utili a colpo d'occhio. A seconda del quadrante dell'orologio selezionato, l'utente ha la possibilità di personalizzare un quadrante con una o più complicazioni che possono essere fornite da un'app Watch in watchos 3.

Se l'utente include una delle complicazioni dell'app sul quadrante dell'orologio, fornisce all'app i vantaggi aggiornati seguenti:

- Il sistema mantiene l'app in uno stato pronto per l'avvio, in cui tenta di avviare l'app in background, la mantiene in memoria e fornisce tempo aggiuntivo per l'aggiornamento.
- Le complicazioni sono garantite almeno 50 aggiornamenti push al giorno.

Lo sviluppatore deve sempre sforzarsi di creare complicazioni accattivanti per le proprie app in modo da indurre l'utente ad aggiungerle alla propria espressione di controllo per i motivi elencati in precedenza.

In watchos 2 le complicazioni erano il modo principale in cui un'app ha ricevuto il runtime in background. In watchos 3, un'app Complication sarà comunque garantita per ricevere più aggiornamenti all'ora, tuttavia, può usare `WKExtensions` per richiedere più runtime per aggiornare le sue complicazioni.

Esaminare il codice seguente usato per aggiornare la complicazione dall'app iPhone connessa:

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

Usa la `RemainingComplicationUserInfoTransfers` proprietà `WCSession` di per vedere il numero di trasferimenti con priorità alta lasciati dall'app per il giorno e quindi esegue un'azione in base a tale numero. Se l'avvio dell'app non è sufficiente, è possibile che si verifichino problemi di invio di aggiornamenti secondari e di inviare informazioni solo in caso di modifica significativa.

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>Pianificazione e ancoraggio

In watchos 3, Apple ha aggiunto il Dock in cui gli utenti possono aggiungere le proprie app preferite e accedervi rapidamente. Quando l'utente preme il pulsante laterale nella Apple Watch, viene visualizzata una raccolta di snapshot dell'app bloccati. L'utente può scorrere rapidamente verso sinistra o verso destra per trovare l'app desiderata, quindi toccare l'app per avviarla sostituendo lo snapshot con l'interfaccia dell'app in esecuzione.

[![](background-tasks-images/dock01.png "Ancoraggio")](background-tasks-images/dock01.png#lightbox)

Il sistema acquisisce periodicamente gli snapshot dell'interfaccia utente dell'app e usa tali snapshot per popolare i documenti. watchos offre all'app la possibilità di aggiornare il contenuto e l'interfaccia utente prima di eseguire lo snapshot.

Le app che sono state aggiunte al Dock possono prevedere quanto segue:

- Ne riceveranno almeno uno aggiornato all'ora. Sono incluse sia un'attività di aggiornamento dell'app che un'attività snapshot.
- Il budget di aggiornamento viene distribuito tra tutte le app nel Dock. Quindi, minore è il numero di app che l'utente ha aggiunto, più saranno gli aggiornamenti potenziali ricevuti da ogni app.
- L'app verrà mantenuta in memoria, in modo che l'app venga ripresa rapidamente quando viene selezionata dal Dock.

L'ultima app eseguita dall'utente sarà considerata l'app _usata più di recente_ e occuperà l'ultimo slot nel Dock. Da qui, l'utente può scegliere di aggiungerlo definitivamente al Dock. Il più recente usato verrà considerato come qualsiasi altra app preferita che l'utente ha già aggiunto al Dock.

> [!IMPORTANT]
> Alle app che sono state aggiunte solo alla schermata iniziale non verrà assegnata una pianificazione regolare. Per ricevere la pianificazione regolare e gli aggiornamenti in background, è _necessario_ aggiungere un'app al Dock.

Come indicato in precedenza in questo documento, gli snapshot sono molto importanti in watchos 3 poiché funzionano sia come immagini di anteprima che di avvio per l'app. Se l'utente stabilisce un'app nel Dock, si espanderà a schermo intero, entrerà in primo piano e inizierà a funzionare, quindi è indispensabile che lo snapshot sia aggiornato.

In alcuni casi è possibile che il sistema richieda un nuovo snapshot dell'interfaccia utente dell'app. In questa situazione, la richiesta di snapshot non verrà conteggiata rispetto al budget di runtime dell'app. Il codice seguente attiverà una richiesta di snapshot del sistema:

- Aggiornamento sequenza temporale complicazioni.
- Interazione dell'utente con la notifica di un'app.
- Spostamento dal primo piano allo stato di sfondo.
- Dopo un'ora di stato in background, l'app può tornare allo stato predefinito.
- Quando watchos viene avviato per la prima volta.

<a name="Best-Practices" />

## <a name="best-practices"></a>Suggerimenti 

Apple suggerisce le seguenti procedure consigliate per l'utilizzo delle attività in background:

- Pianificare la frequenza con cui è necessario aggiornare l'app. Ogni volta che l'app viene eseguita, deve rivalutare le proprie esigenze future e modificare la pianificazione in base alle esigenze.
- Se il sistema invia un'attività di aggiornamento in background e l'app non richiede un aggiornamento, rinviare il lavoro fino a quando non è necessario un aggiornamento.
- Prendere in considerazione tutte le opportunità di runtime disponibili per un'app:
    - Attivazione di ancoraggio e in primo piano.
    - Notifiche.
    - Aggiornamenti delle complicazioni.
    - Aggiornamenti in background.
- Usare `ScheduleBackgroundRefresh` per il runtime di background generale, ad esempio:
    - Polling del sistema per ottenere informazioni.
    - Pianificare future `NSURLSessions` per richiedere dati in background. 
    - Transizioni temporali note.
    - Attivazione degli aggiornamenti delle complicazioni.

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>Procedure consigliate per gli snapshot

Quando si lavora con gli aggiornamenti snapshot, Apple apporta i suggerimenti seguenti:

- Invalidare gli snapshot solo quando necessario, ad esempio quando si verifica una modifica significativa del contenuto.
- Evitare l'invalidamento di snapshot ad alta frequenza. Ad esempio, un'app timer non deve aggiornare lo snapshot ogni secondo, ma deve essere eseguita solo al termine del timer.

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>Flusso di dati dell'app

Apple suggerisce quanto segue per l'uso del flusso di dati:

[![](background-tasks-images/update17.png "Diagramma del flusso di dati dell'app")](background-tasks-images/update17.png#lightbox)

Un evento esterno, ad esempio connettività di controllo, riattiva l'app. In questo modo l'app deve aggiornare il modello di dati (che rappresenta lo stato corrente delle app). In seguito alla modifica del modello di dati, l'app dovrà aggiornare le sue complicazioni, richiedere un nuovo snapshot, probabilmente avviare uno `NSURLSession` sfondo per estrarre più dati e pianificare altri aggiornamenti in background.

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>Ciclo di vita dell'app

Grazie al Dock e alla possibilità di aggiungere app preferite, Apple ritiene che gli utenti si sposteranno tra molto più app, molto più spesso, quindi hanno fatto con watchos 2. Di conseguenza, l'app deve essere pronta a gestire questa modifica e spostarsi rapidamente tra gli stati in primo piano e in background.

Apple presenta i suggerimenti seguenti:

- Assicurarsi che l'app completi tutte le attività in background non appena possibile quando si entra in primo piano.
- Assicurarsi di completare tutte le operazioni in primo piano prima di accedere `NSProcessInfo.PerformExpiringActivity`allo sfondo chiamando.
- Quando si esegue il test di un'app nel simulatore watchos, non verrà applicato alcun budget per le attività, in modo che un'app possa aggiornare il più necessario per testare correttamente una funzionalità.
- Testare sempre l'hardware Apple Watch reale per assicurarsi che l'app non sia in esecuzione oltre i budget prima della pubblicazione in iTunes Connect.
- Apple consiglia di mantenere il Apple Watch sul caricabatterie durante i test e il debug.
- Assicurarsi che l'avvio a freddo e la ripresa di un'app siano testati accuratamente.
- Verificare che tutte le attività dell'app siano state completate.
- Variare il numero di app bloccate nel Dock per testare gli scenari migliori e peggiori del caso.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i miglioramenti apportati da Apple a watchos e come è possibile usarli per tenere aggiornata l'app Watch. In primo luogo, è stata analizzata tutta la nuova attività in background aggiunta da Apple in watchos 3. È stato quindi trattato il ciclo di vita dell'API in background e come implementare le attività in background in un'app Novell watchos. Infine, ha illustrato il funzionamento della pianificazione e alcune procedure consigliate.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
