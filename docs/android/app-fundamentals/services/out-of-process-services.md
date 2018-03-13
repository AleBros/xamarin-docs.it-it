---
title: Servizi in esecuzione Android nei processi remoti
description: "In genere, tutti i componenti in un'applicazione Android verranno eseguito nello stesso processo. Android servizi sono un'eccezione a questa può essere configurati per eseguire i propri processi, condiviso con altre applicazioni, comprese quelle di altri sviluppatori Android. Questa Guida verrà illustrato come creare e utilizzare un servizio remoto Android con Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 0f018aa666f08792a1693a79e22da150f82712f6
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="running-android-services-in-remote-processes"></a>Servizi in esecuzione Android nei processi remoti

_In genere, tutti i componenti in un'applicazione Android verranno eseguito nello stesso processo. Android servizi sono un'eccezione a questa può essere configurati per eseguire i propri processi, condiviso con altre applicazioni, comprese quelle di altri sviluppatori Android. Questa Guida verrà illustrato come creare e utilizzare un servizio remoto Android con Xamarin._

## <a name="out-of-process-services-overview"></a>Panoramica del processo di servizi fuori

Quando un'applicazione viene avviata, Android crea un processo in cui eseguire l'applicazione. In genere, tutti i componenti dell'applicazione verrà eseguito in un processo. Android servizi sono un'eccezione a questa può essere configurati per eseguire i propri processi, condiviso con altre applicazioni, comprese quelle di altri sviluppatori Android. Questi tipi di servizi sono detti _servizi remoto_ o _out-of-process services_. Il codice per questi servizi sarà contenuto nella stessa APK come applicazione principale. Tuttavia, quando il servizio viene avviato Android verrà creato un nuovo processo per solo quel servizio. Al contrario, un servizio in esecuzione nello stesso processo del resto dell'applicazione è talvolta detta un _servizio locale_.

In generale, non è necessario per un'applicazione implementare un servizio remoto. Un servizio locale non è sufficiente (e consigliabile) per i requisiti di un'app in molti casi. Un out-of-process ha proprio spazio di memoria che deve essere gestito da Android. Anche se ciò comporta un sovraccarico più all'applicazione globale, esistono alcuni scenari in cui può essere utile per eseguire un servizio in un processo:

1. **Funzionalità di condivisione** &ndash; alcuni sviluppatori di applicazioni possono avere più applicazioni e le funzionalità condivise tra tutte le applicazioni. Creazione di pacchetti che la funzionalità in un servizio di Android che viene eseguito in un processo può semplificare la manutenzione dell'applicazione. È anche possibile creare il pacchetto del servizio in un proprio APK autonomo e distribuirlo separatamente dal resto dell'applicazione.
2. **Migliorare l'esperienza utente** &ndash; esistono due modi che un servizio out-of-process può migliorare l'esperienza utente dell'applicazione. Il primo modo riguarda la gestione della memoria. Quando un'operazione di garbage collection (GC) si verifica ciclo Android sospenderà tutte le attività nel processo fino a quando il Garbage Collector è stato completato. L'utente potrà percepire questa pausa come "segnalazione" o "jank". Quando un servizio è in esecuzione un processo, è il processo del servizio che viene sospesa, non il processo dell'applicazione. Questa pausa è molto meno evidente all'utente perché il processo dell'applicazione (e pertanto l'interfaccia utente) non è sospesa.

    In secondo luogo, se i requisiti di memoria di un processo diventa troppo grande, Android potrebbe terminare il processo per liberare risorse per il dispositivo. Se un servizio dispone di un footprint di memoria di grandi dimensioni ed è in esecuzione nello stesso processo come l'interfaccia utente, quindi quando Android forzatamente recupera le risorse dell'interfaccia utente verrà arrestato, imporre l'utente per avviare l'app. Tuttavia, se un servizio, in esecuzione in un processo viene arrestato da Android, il processo dell'interfaccia utente rimane invariato. L'interfaccia utente può associare e riavviare il servizio, trasparente all'utente e riprendere normale funzionamento.

3. **Miglioramento delle prestazioni dell'applicazione** &ndash; processo dell'interfaccia utente può essere terminata o arrestare indipendentemente dal fatto che il processo del servizio. Spostando le attività di avvio di lunga durata a un servizio out-of-process, è possibile che il tempo di avvio dell'interfaccia utente migliorata forse (supponendo che il processo del servizio viene mantenuto attivo tra gli orari in cui viene avviata l'interfaccia utente).

In molti modi, associazione a un servizio in esecuzione in un altro processo è identico [associazione a un servizio locale](~/android/app-fundamentals/services/creating-a-service/bound-services.md). Il client richiamerà `BindService` associare (e avviare, se necessario) il servizio. Un `Android.OS.IServiceConnection` verrà creato l'oggetto per gestire la connessione tra il client e il servizio. Se il client esegue correttamente il binding al servizio, quindi Android, verrà restituito un oggetto tramite il `IServiceConnection` che può essere utilizzato per richiamare metodi sul servizio. Il client interagisce con il servizio utilizzando l'oggetto. Per esaminare, ecco i passaggi per associare a un servizio:

* **Creare un intento** &ndash; intento esplicito deve essere utilizzata per l'associazione al servizio.
* **Implementare e Instantiate un `IServiceConnection` oggetto** &ndash; il `IServiceConnection` oggetto funge da intermediario tra il client e il servizio.  È responsabile per la connessione tra client e server di monitoraggio.
* **Richiamare il `BindService` metodo** &ndash; chiamata `BindService` invierà la finalità e la connessione al servizio creato nei passaggi precedenti per Android, che si occuperà di avvio del servizio e stabilire la comunicazione tra client e servizio.

La necessità di attraversare i limiti di processo introdurre complessità aggiuntiva: la comunicazione è unidirezionale (da client a server) e il client non è possibile richiamare direttamente metodi nella classe del servizio. Tenere presente che quando un servizio è in esecuzione lo stesso processo del client, Android offre un `IBinder` oggetto che potrebbe consentire la comunicazione bidirezionale. Questo non avviene con il servizio in esecuzione in un processo. Un client comunica con un servizio remoto con l'aiuto del `Android.OS.Messenger` classe.

Quando un client richiede per l'associazione con il servizio remoto, Android richiamerà il `Service.OnBind` metodo del ciclo di vita, che restituirà interno `IBinder` oggetto che viene incapsulata dal `Messenger`. Il `Messenger` è un thin wrapper in una speciale `IBinder` implementazione fornito da Android SDK. Il `Messenger` si occupa della comunicazione tra i due processi diversi. Lo sviluppatore è unconcerned con i dettagli di serializzazione di un messaggio, il marshalling del messaggio attraverso il limite di processo e quindi la deserializza nel client. Questa operazione viene gestita dal `Messenger` oggetto. Questo diagramma illustra i componenti di Android sul lato client che vengono utilizzati quando un client avvia l'associazione a un servizio out-of-process:

![Diagramma che mostra i componenti e passaggi per un'associazione a un servizio client](out-of-process-services-images/ipc-01.png "diagramma che mostra i componenti e passaggi per un'associazione a un servizio client.")

La `Service` classe nel processo remoto verrà inviata tramite il callback stesso ciclo di vita che un servizio associato in un processo locale verrà esaminate e numerose API coinvolte sono uguali. `Service.OnCreate` viene utilizzato per inizializzare un `Handler` e inserire in `Messenger` oggetto. Analogamente, `OnBind` è sottoposto a override, ma anziché restituire un `IBinder` oggetto, il servizio restituirà il `Messenger`.  Questo diagramma viene illustrato cosa accade nel servizio quando è associato un client:

![Diagramma che illustra i passaggi e i componenti di un servizio attraversa quando viene associato a un client remoto](out-of-process-services-images/ipc-02.png "attraversa diagramma che illustra i passaggi e i componenti di un servizio quando viene associato a un client remoto.")

Quando un `Message` viene ricevuto da un servizio, viene elaborato da nell'istanza di `Android.OS.Handler`. Il servizio implementerà il proprio `Handler` sottoclasse che deve eseguire l'override di `HandleMessage` metodo. Questo metodo viene richiamato dal `Messenger` e riceve il `Message` come parametro. Il `Handler` analizzerà il `Message` metadati e utilizzare tali informazioni per richiamare metodi sul servizio.

Comunicazione unidirezionale si verifica quando un client crea un `Message` dell'oggetto e lo invia al servizio utilizzando il `Messenger.Send` metodo. `Messenger.Send` verrà serializzato il `Message` e a mano che i dati serializzati per Android, che il messaggio verrà instradato attraverso il limite di processo e il servizio.  Il `Messenger` che è ospitato dal servizio creerà un `Message` oggetto dai dati in ingresso. Questo `Message` viene inserito in una coda, in cui i messaggi vengono inviati uno alla volta per il `Handler`. Il `Handler` esamina i metadati contenuti nel `Message` e richiamare i metodi appropriati nel `Service`. Il diagramma seguente illustra questi vari concetti in azione:

![Diagramma che illustra come i messaggi vengono passati tra i processi](out-of-process-services-images/ipc-03.png "diagramma che illustra come i messaggi vengono passati tra processi.")

Questa Guida verrà illustrati i dettagli di implementazione di un servizio out-of-process. Vengono illustrati come implementare un servizio che deve essere eseguito in un processo e come un client può comunicare con tale servizio utilizzando il `Messenger` framework. Inoltre brevemente la comunicazione bidirezionale: il client invia un messaggio a un servizio e il servizio invia un messaggio al client. Poiché i servizi possono essere condivisi tra diverse applicazioni, questa Guida verrà descritte anche una tecnica per limitare l'accesso client al servizio utilizzando le autorizzazioni di Android.

> [!IMPORTANT]
> [Bugzilla 51940 - servizi con i processi isolati e la classe di applicazione personalizzato in grado di risolvere correttamente overload](https://bugzilla.xamarin.com/show_bug.cgi?id=51940) report che un servizio di xamarin non si avvia correttamente quando il `IsolatedProcess` è impostato su `true`. Questa guida è disponibile per un riferimento. Un'applicazione di xamarin deve ancora essere in grado di comunicare con un servizio out-of-process che viene scritto in linguaggio.

## <a name="requirements"></a>Requisiti

Questa guida si presuppone una certa familiarità con la creazione di servizi.

Sebbene sia possibile utilizzare intenti impliciti con App che hanno come destinazione precedente API Android, questa guida verranno concentrarsi esclusivamente sull'utilizzo di tipi espliciti. App destinate a Android 5.0 (livello API 21) o versione successiva deve utilizzare la finalità esplicita da associare a un servizio. Questa è la tecnica che verrà illustrato più avanti in questa Guida...

## <a name="create-a-service-that-runs-in-a-separate-process"></a>Creare un servizio che viene eseguito in un processo separato

Come descritto in precedenza, il fatto che un servizio è in esecuzione nel proprio processo significa che alcune API diverse sono coinvolti. Come una rapida panoramica, ecco i passaggi per associare e utilizzare un servizio remoto:  

* **Creare il `Service` sottoclasse** &ndash; sottoclasse il `Service` digitare e implementare i metodi del ciclo di vita per un servizio associato. È inoltre necessario impostare i metadati che saranno Android che il servizio venga eseguito in un processo.
* **Implementare un `Handler`**  &ndash; il `Handler` è responsabile per analizzare le richieste del client, l'estrazione di tutti i parametri passati dal client e richiamare i metodi appropriati nel servizio.
* **Creare un'istanza di un `Messenger`**  &ndash; come descritto in precedenza, ogni `Service` deve gestire un'istanza del `Messenger` classe indirizza le richieste del client di `Handler` che è stato creato nel passaggio precedente.

Un servizio che deve essere eseguito in un processo è, fondamentalmente, ancora un servizio associato. La classe del servizio verrà esteso della base `Service` classe ed è decorata con il `ServiceAttribute` contenente i metadati che Android è necessario aggregare nel manifesto di Android. Per iniziare, le proprietà seguenti del `ServiceAttribute` che sono importanti per un servizio out-of-process:

1. `Exported` &ndash; Questa proprietà deve essere impostata su `true` per consentire ad altre applicazioni di interagire con il servizio. Il valore predefinito di questa proprietà è `false`.
2. `Process` &ndash; Questa proprietà deve essere impostata. Consente di specificare il nome del processo di cui verrà eseguito il servizio.
3. `IsolatedProcess` &ndash; Questa proprietà verrà abilitata la protezione aggiuntiva, indicante Android per eseguire il servizio in un ambiente sandbox isolato con autorizzazioni minime per iteract con il resto del sistema. Vedere [Bugzilla 51940 - servizi con i processi isolati e la classe di applicazione personalizzato non venga risolto correttamente overload](https://bugzilla.xamarin.com/show_bug.cgi?id=51940).
4. `Permission` &ndash; È possibile controllare l'accesso client al servizio specificando un'autorizzazione per i client devono richiedere (concessi).

Per eseguire un processo distinto di un servizio di `Process` proprietà il `ServiceAttribute` è necessario impostare il nome del servizio. Per interagire con le applicazioni esterne, il `Exported` proprietà deve essere impostata su `true`. Se `Exported` è `false`, solo i client il file APK stesso (ad esempio la stessa applicazione) e in esecuzione nello stesso processo saranno in grado di interagire con il servizio.

Il tipo di processo in cui verrà eseguito il servizio dipende dal valore del `Process` proprietà. Android identifica tre diversi tipi di processi:

-   **Processo privato** &ndash; un processo privato è che è disponibile solo per l'applicazione che l'ha avviata. Per identificare un processo come privato, il nome deve iniziare con un **:** (punto e virgola). Il servizio illustrato nel frammento di codice precedente e schermata è un processo privato. Frammento di codice seguente viene illustrato l'utilizzo di `ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

-   **Processo globale** &ndash; un servizio che viene eseguito in un processo globale sia accessibile a tutte le applicazioni in esecuzione nel dispositivo. Un processo globale deve essere un nome di classe completo che inizia con un carattere minuscolo.
    (A meno che non vengono eseguite operazioni per proteggere il servizio, altre applicazioni possono associare e interagire con esso. Protezione del servizio da utilizzi non autorizzati verrà descritta più avanti in questa Guida.)

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

-   **Processo isolato** &ndash; un processo isolato è un processo che viene eseguito nel propria sandbox, isolata dal resto del sistema e senza autorizzazioni speciali di un proprio. Per eseguire un servizio in un processo isolato, il `IsolatedProcess` proprietà del `ServiceAttribute` è impostato su `true` come illustrato nel frammento di codice seguente:
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> Vedere [Bugzilla 51940 - servizi con i processi isolati e la classe di applicazione personalizzato non venga risolto correttamente overload](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

Un servizio di tipo isolato è un modo semplice per proteggere un'applicazione e il dispositivo con codice non attendibile. Ad esempio, un'app può scaricare ed eseguire uno script da un sito Web. In questo caso, si esegue in un processo isolato che fornisce un ulteriore livello di sicurezza con codice non attendibile di compromettere il dispositivo Android.

> [!IMPORTANT]
> Una volta che un servizio è stato esportato, non modificare il nome del servizio. La modifica del nome del servizio possono essere interrotte altre applicazioni che utilizzano il servizio.

Per visualizzare l'effetto che il `Process` dispone di proprietà, nella schermata seguente viene illustrato un servizio in esecuzione nel proprio processo privata:

![Schermata che illustra un servizio in esecuzione in un processo privato](out-of-process-services-images/ipc-04.png "schermata che illustra un servizio in esecuzione in un processo privato.")

In questo screenshot successivo illustra `Process="com.xamarin.xample.messengerservice.timestampservice_process"` e il servizio in esecuzione in un processo globale:

![Schermata di un servizio in esecuzione in un processo globale](out-of-process-services-images/ipc-05.png "schermata di un servizio in esecuzione in un processo globale.")

Una volta il `ServiceAttribute` è stata impostata, il servizio deve implementare un `Handler`.

### <a name="implementing-a-handler"></a>Implementazione di un gestore

Per elaborare le richieste client, il servizio deve implementare un `Handler` ed eseguire l'override di `HandleMessage` methodThis è il metodo accetta un `Message` istanza quale incapsula la chiamata al metodo dal client e converte tale chiamata in un'azione o attività che eseguirà il servizio. Il `Message` oggetto espone una proprietà denominata `What` che è un valore intero, il cui significato è condivisa tra il client e il servizio e si riferisce a un'attività che il servizio è in esecuzione per il client.

Il seguente frammento di codice dall'applicazione di esempio viene illustrato un esempio di `HandleMessage`. In questo esempio, sono disponibili due azioni che un client può richiedere del servizio:

* La prima azione è un _Hello, World_ messaggio, il client ha inviato un messaggio semplice al servizio.
* La seconda azione verrà richiamare un metodo del servizio e recuperare una stringa, in questo caso la stringa è un messaggio che restituisce l'ora viene avviata e il tempo che è in esecuzione:

```csharp
public class TimestampRequestHandler : Android.OS.Handler
{
    // other code omitted for clarity

    public override void HandleMessage(Message msg)
    {
        int messageType = msg.What;
        Log.Debug(TAG, $"Message type: {messageType}.");

        switch (messageType)
        {
            case Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE:
                // The client as sent a simple Hello, say in the Android Log.
                break;

            case Constants.GET_UTC_TIMESTAMP:
                // Call methods on the service to retrive a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

È inoltre possibile ai parametri del pacchetto per il servizio di `Message`. Questo argomento verrà descritto più avanti in questa Guida. L'argomento successivo da considerare è la creazione di `Messenger` oggetto da elaborare in ingresso `Message`s.

### <a name="instantiating-the-messenger"></a>Creazione di un'istanza di Messenger

Come spiegato in precedenza, la deserializzazione di `Message` oggetto e richiamando `Handler.HandleMessage` è di competenza del `Messenger` oggetto. Il `Messenger` classe fornisce anche un `IBinder` che il client verrà utilizzato per inviare messaggi al servizio.  

All'avvio del servizio, crea un'istanza di `Messenger` e inserire il `Handler`. È un ottimo strumento per eseguire l'inizializzazione di `OnCreate` metodo del servizio. Questo frammento di codice è un esempio di un servizio che consente di inizializzare il proprio `Handler` e `Messenger`:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

A questo punto, il passaggio finale consiste per il `Service` per eseguire l'override `OnBind`.

### <a name="implementing-serviceonbind"></a>Implementazione Service.OnBind

Tutti i servizi associati, se vengono eseguite nel proprio processo oppure No, devono implementare il `OnBind` metodo. Il valore restituito di questo metodo è un oggetto che il client può utilizzare per interagire con il servizio. Esattamente cosa che l'oggetto è varia a seconda se il servizio è un servizio locale o un servizio remoto. Mentre un servizio locale restituirà un oggetto personalizzato `IBinder` implementazione, verrà restituito un servizio remoto di `IBinder` che viene incapsulata ma la `Messenger` che è stato creato nella sezione precedente:

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

Dopo queste operazioni vengono effettuate, il servizio remoto può essere considerato completato.

## <a name="consuming-the-service"></a>Utilizzo del servizio

Tutti i client devono implementare il codice necessario per essere in grado di eseguire il binding e utilizzare il servizio remoto. Concettualmente, dal punto di vista del client, esistono poche differenze tra l'associazione al servizio locale o un servizio remoto. Il client richiama il `BindService` , passando un intento esplicito per identificare il servizio e un `IServiceConnection` che consente di gestire la connessione tra il client e il servizio.

Questo frammento di codice è riportato un esempio di come creare un **intento esplicito** per l'associazione a un servizio remoto. Lo scopo è necessario identificare il pacchetto che contiene il servizio e il nome del servizio. Per impostare queste informazioni è possibile utilizzare un `Android.Content.ComponentName` oggetto e di fornire allo scopo. Questo frammento di codice è un esempio:  

```csharp
// This is the package name of the APK, set in the Android manifest
const string REMOTE_SERVICE_COMPONENT_NAME = "com.xamarin.TimestampService";
// This is the name of the service, according the value of ServiceAttribute.Name
const string REMOTE_SERVICE_PACKAGE_NAME   = "com.xamarin.xample.messengerservice";

// Provide the package name and the name of the service with a ComponentName object.
ComponentName cn = new ComponentName(REMOTE_SERVICE_PACKAGE_NAME, REMOTE_SERVICE_COMPONENT_NAME);
Intent serviceToStart = new Intent();
serviceToStart.SetComponent(cn);
```

Quando il servizio è associato, il `IServiceConnection.OnServiceConnected` metodo viene richiamato e fornisce un `IBinder` a un client. Tuttavia, il client non direttamente utilizzerà il `IBinder`. Al contrario, creerà un'istanza di un `Messenger` oggetto da cui `IBinder`. Si tratta di `Messenger` che il client utilizzerà per interagire con il servizio remoto.

Ecco un esempio molto semplice `IServiceConnection` implementazione che illustra come un client si gestisce alla connessione e disconnessione da un servizio. Si noti che il `OnServiceConnected` metodo riceve e `IBinder`, e il client crea un `Messenger` da quello `IBinder`:

```csharp
public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection
{
    static readonly string TAG = typeof(TimestampServiceConnection).FullName;

    MainActivity mainActivity;
    Messenger messenger;

    public TimestampServiceConnection(MainActivity activity)
    {
        IsConnected = false;
        mainActivity = activity;
    }

    public bool IsConnected { get; private set; }
    public Messenger Messenger { get; private set; }

    public void OnServiceConnected(ComponentName name, IBinder service)
    {
        Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

        IsConnected = service != null
        Messenger = new Messenger(service);

        if (IsConnected)
        {
            // things to do when the connection is successful. perhaps notify the client? enable UI features?
        }
        else
        {
            // things to do when the connection isn't successful.
        }
    }

    public void OnServiceDisconnected(ComponentName name)
    {
        Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
        IsConnected = false;
        Messenger = null;

        // Things to do when the service disconnects. perhaps notify the client? disable UI features?

    }
}
```

Dopo aver creati la connessione al servizio e lo scopo, è possibile che il client di chiamare `BindService` e avviare il processo di associazione:

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

Dopo che il client è correttamente associato al servizio e `Messenger` è disponibile, è possibile che il client invia `Messages` al servizio.

## <a name="sending-messages-to-the-service"></a>L'invio di messaggi al servizio

Una volta che il client è connesso e ha un `Messenger` dell'oggetto, è possibile comunicare con il servizio con l'invio `Message` oggetti tramite il `Messenger`. Questa comunicazione è unidirezionale, il client invia il messaggio, ma nessun messaggio restituito dal servizio al client. In questo senso, il `Message` è un meccanismo fire and forget.

Il modo preferito per creare un `Message` oggetto consiste nell'utilizzare il [ `Message.Obtain` ](https://developer.xamarin.com/api/type/Android.OS.Message/#Public%20Methods) metodo factory. Questo metodo acquisisce un `Message` oggetto da un pool globale viene gestito da Android. `Message.Obtain` anche con alcuni metodi di overload che consentono di `Message` oggetto di essere inizializzata con i valori e i parametri richiesti dal servizio.  Una volta il `Message` viene creata un'istanza, inviato al servizio chiamando `Messenger.Send`. Questo frammento è un esempio di creazione e l'invio di un `Message` al processo del servizio:

```csharp
Message msg = Message.Obtain(null, Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE);
try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a error trying to send the message.");
}
```

Esistono diversi tipi diversi del `Message.Obtain` metodo. L'esempio precedente Usa la [ `Message.Obtain(Handler h, Int32 what)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/). Poiché si tratta di una richiesta asincrona a un servizio out-of-process. non sarà presente alcuna risposta dal servizio, pertanto la `Handler` è impostato su `null`. Il secondo parametro, `Int32 what`, verrà archiviato nel `.What` proprietà del `Message` oggetto. Il `.What` proprietà viene utilizzata dal codice nel processo del servizio per richiamare metodi sul servizio.

Il `Message` classe espone inoltre due proprietà aggiuntive che potrebbe essere utile per il recipent: `Arg1` e `Arg2`. Queste due proprietà sono valori interi che possono avere alcune speciali concordati valori che hanno un significato tra il client e il servizio. Ad esempio, `Arg1` può contenere un ID cliente e `Arg2` può contenere un numero di ordine di acquisto per tale cliente. Il [ `Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/System.Int32/System.Int32/) può essere utilizzato per impostare le due proprietà quando il `Message` viene creato. Un altro modo per inserire questi due valori consiste nell'impostare il `.Arg` e `.Arg2` direttamente sulla proprietà di `Message` oggetto dopo che è stato creato.

### <a name="passing-additional-values-to-the-service"></a>Passaggio di valori aggiuntivi al servizio

È possibile passare i dati più complessi al servizio utilizzando un `Bundle`. In questo caso, i valori aggiuntivi possono essere inseriti in un `Bundle` e inviato con il `Message` impostando il [ `.Data` proprietà](https://developer.xamarin.com/api/property/Android.OS.Message.Data/) proprietà prima dell'invio.

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> In generale, un `Message` non deve avere un payload maggiore di 1 MB. Il limite delle dimensioni può variare in base la versione di Android e alle modifiche apportate proprietarie eventualmente apportate al fornitore per l'implementazione di Android aprire origine progetto (AOSP) che viene fornito con il dispositivo.

## <a name="returning-values-from-the-service"></a>Restituzione di valori dal servizio

L'architettura di messaggistica che è stato trattato in questo punto è unidirezionale, il client invia un messaggio al servizio. Se è necessario che il servizio restituire un valore a un client viene invertita tutto ciò che è stato già a questo punto. Il servizio è necessario creare un `Message`, assemblati i valori e invio di `Message` tramite un `Messenger` al client. Tuttavia, il servizio non crea il proprio `Messenger`, bensì si basa sul client di creazione di un'istanza e il pacchetto un `Messenger` come parte della richiesta iniziale. Il servizio verrà `Send` il messaggio utilizzando questo fornite dal client `Messenger`.  

La sequenza di eventi per la comunicazione bidirezionale è la seguente:

1. Il client esegue il binding al servizio. Quando il servizio e il client si connettono, il `IServiceConnection` che mediante il client sarà necessario un riferimento a un `Messenger` oggetto utilizzato per trasmettere `Message`s per il servizio. Per evitare confusione, questo verrà essere indicato come la _servizio Messenger_.
2. Client crea un'istanza un `Handler` (detto di _gestore Client_) che verrà usata per inizializzare il proprio `Messenger` (il _Client Messenger_). Si noti che il servizio di Messenger e il servizio Client sono due oggetti diversi che gestiscono il traffico in due direzioni diverse. Il servizio di Messenger gestisce i messaggi dal client al servizio, mentre il servizio Client verranno gestiti i messaggi dal servizio al client.
3. Il client crea un `Message` oggetto e imposta il `ReplyTo` proprietà con il servizio Client. Il messaggio viene quindi inviato al servizio usando il servizio di Messenger.
4. Il servizio riceve il messaggio dal client ed esegue il lavoro richiesto.
5. Al momento per il servizio inviare la risposta al client, verrà utilizzato `Message.Obtain` per creare un nuovo `Message` oggetto.
6. Per inviare il messaggio al client, il servizio verrà estratto il servizio Client dal `.ReplyTo` proprietà del client del messaggio e utilizzarlo per `.Send` il `Message` al client.
7. Se la risposta viene ricevuta dal client, ha un proprio `Handler` che elaborerà il `Message` controllando il `.What` proprietà (e se necessario, l'estrazione di tutti i parametri contenuti di `Message`).

Questo codice di esempio viene illustrato come il client crea un'istanza di `Message` e pacchetto un `Messenger` che il servizio deve usare per la risposta:

```csharp
Handler clientHandler = new ActivityHandler();
Messenger clientMessenger = new Messenger(activityHandler);

Message msg = Message.Obtain(null, Constants.GET_UTC_TIMESTAMP);
msg.ReplyTo = clientMessenger;

try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a problem sending the message.");
}
```

Il servizio è necessario apportare alcune modifiche per il proprio `Handler` per estrarre il `Messenger` da utilizzare per inviare risposte al client. Questo frammento di codice è riportato un esempio di come il servizio `Handler` creerebbe un `Message` e inviarlo al client:  

```csharp
// This is the message that the service will send to the client.
Message responseMessage = Message.Obtain(null, Constants.RESPONSE_TO_SERVICE);
Bundle dataToReturn = new Bundle();
dataToReturn.PutString(Constants.RESPONSE_MESSAGE_KEY, "This is the result from the service.");
responseMessage.Data = dataToReturn;

// The msg object here is the message that was received by the service. The service will not instantiate a client,
// It will use the client that is encapsulated by the message from the client.
Messenger clientMessenger = msg.ReplyTo;
if (clientMessenger!= null)
{
    try
    {
        clientMessenger.Send(responseMessage);
    }
    catch (Exception ex)
    {
        Log.Error(TAG, ex, "There was a problem sending the message.");
    }
}
```

Si noti che negli esempi di codice precedenti, il `Messenger` istanza che viene creato dal client è *non* lo stesso oggetto ricevuto dal servizio. Si tratta di due diversi `Messenger` oggetti in esecuzione in due processi distinti che rappresentano il canale di comunicazione.

## <a name="securing-the-service-with-android-permissions"></a>Protezione del servizio con autorizzazioni di Android

Un servizio che viene eseguito in un processo globale è accessibile da tutte le applicazioni in esecuzione nel dispositivo Android. In alcuni casi, è desiderabile che l'apertura e la disponibilità e è necessario proteggere il servizio di accesso da client non autorizzati. Un modo per limitare l'accesso al servizio remoto consiste nell'utilizzare autorizzazioni Android.

Autorizzazioni possono essere identificate tramite il `Permission` proprietà del `ServiceAttribute` che decora il `Service` sottoclasse. Questo nome di un'autorizzazione che il client deve essere concesse durante l'associazione al servizio viene modificato. Se il client non dispone delle autorizzazioni appropriate, quindi Android genererà un `Java.Lang.SecurityException` quando il client tenta di associare il servizio.

Sono disponibili quattro diversi livelli di autorizzazione forniti Android:

* **normale** &ndash; si tratta del livello di autorizzazione predefiniti. Utilizzato per identificare le autorizzazioni a basso rischio che possono essere concesse automaticamente da Android ai client che lo richiedono. L'utente non dispone in modo esplicito concedere queste autorizzazioni, ma le autorizzazioni possono essere visualizzate nelle impostazioni dell'app.
* **firma** &ndash; si tratta di una categoria speciale di autorizzazione verrà concesse automaticamente da Android per le applicazioni firmate con lo stesso certificato. Questa autorizzazione è progettata per renderlo facilmente per uno sviluppatore di applicazioni condividere dati tra le proprie App senza richiedere l'intervento dell'utente per l'approvazione costante o componenti.
* **signatureOrSystem** &ndash; è molto simile al **firma** autorizzazioni descritte in precedenza. Oltre a essere automaticamente concesso per le app dallo stesso certificato di firma, questa autorizzazione verrà inoltre concesso l'App che sono firmati lo stesso certificato utilizzato per firmare le app installate con l'immagine del sistema Android. Questa autorizzazione in genere viene utilizzata solo dagli sviluppatori di Android ROM per consentire le proprie applicazioni per App di terze parti. Non è in genere utilizzato da applicazioni che sono destinate generali di distribuzione per il pubblico in generale.
* **pericoloso** &ndash; autorizzazioni pericolose sono quelle che potrebbero causare problemi per l'utente. Per questo motivo, **pericoloso** le autorizzazioni devono essere approvate dall'utente in modo esplicito.

Poiché `signature` e `normal` le autorizzazioni vengono concesse automaticamente in fase di installazione da Android, è fondamentale che APK che ospita il servizio sia installato **prima** il file APK che contiene il client. Se il client viene installato per primo, Android non comporterà la concessione di autorizzazioni. In questo caso, sarà necessario disinstallare il client APK, installare il servizio APK e quindi installare nuovamente il file APK di client.

Esistono due modi per proteggere un servizio con autorizzazioni Android:

1.  **Implementare la protezione a livello di firma** &ndash; sicurezza a livello di firma significa che autorizzazione viene concessa automaticamente per le applicazioni che sono firmate con la stessa chiave utilizzata per firmare il file APK che contiene il servizio. Questo è un modo semplice per gli sviluppatori per proteggere il servizio è ancora mantenerli accessibile dalle rispettive applicazioni. Le autorizzazioni a livello di firma vengono dichiarate mediante l'impostazione di `Permission` proprietà del `ServiceAttribute` per `signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2.  **Creare un'autorizzazione personalizzata** &ndash; è possibile che lo sviluppatore del servizio creare un'autorizzazione personalizzata per il servizio. È consigliabile utilizzare questo quando uno sviluppatore desidera condividere il servizio con applicazioni da altri sviluppatori. Un'autorizzazione personalizzata richiede maggiore impegno per implementare e verranno descritte di seguito.

Un esempio semplificato di creazione di un oggetto personalizzato `normal` autorizzazione verrà descritta nella sezione successiva. Per ulteriori informazioni sulle autorizzazioni per Android, consultare la documentazione di Google per [le procedure consigliate e sicurezza](https://developer.android.com/training/articles/security-tips.html). Per ulteriori informazioni sulle autorizzazioni Android, vedere il [sezione autorizzazioni](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) della documentazione di Android per il manifesto dell'applicazione per ulteriori informazioni sulle autorizzazioni per Android.

> [!NOTE]
> In generale, [Google sconsiglia l'uso di autorizzazioni personalizzate](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) come che possono risultare poco chiaro agli utenti.

### <a name="creating-a-custom-permission"></a>Creazione di un'autorizzazione personalizzata

Per utilizzare un'autorizzazione personalizzata, viene dichiarato dal servizio mentre il client richiede in modo esplicito l'autorizzazione.

Per creare un'autorizzazione nel servizio file, APK un `permission` elemento viene aggiunto al `manifest` elemento**AndroidManifest.xml**. Questa autorizzazione deve avere il `name`, `protectionLevel`, e `label` set di attributi. Il `name` attributo deve essere impostato su una stringa che identifica in modo univoco l'autorizzazione. Il nome verrà visualizzato nel **App Info** visualizzare il **impostazioni Android** (come illustrato nella sezione successiva).

Il `protectionLevel` attributo deve essere impostato su uno dei valori di stringa di quattro descritta in precedenza.  Il `label` e `description` deve fare riferimento alle risorse stringa e vengono utilizzati per fornire un nome descrittivo e una descrizione per l'utente.

Questo frammento è riportato un esempio di dichiarazione di un oggetto personalizzato `permission` attributo **AndroidManifest.xml** di APK che contiene il servizio:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerservice">

    <uses-sdk android:minSdkVersion="21" />

    <permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP"
                android:protectionLevel="signature"
                android:label="@string/permission_label"
                android:description="@string/permission_description"
                />

    <application android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">

    </application>
</manifest>
```

Quindi, **AndroidManifest.xml** del client APK deve richiedere in modo esplicito la nuova autorizzazione. Questa operazione viene eseguita aggiungendo il `users-permission` attributo la **AndroidManifest.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerclient">

    <uses-sdk android:minSdkVersion="21" />

    <uses-permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP" />

    <application
            android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">
    </application>
    </manifest>
```

### <a name="view-the-permissions-granted-to-an-app"></a>Visualizzare le autorizzazioni concesse a un'App

Per visualizzare le autorizzazioni concesse a un'applicazione, aprire l'app Android impostazioni e selezionare **app**. Individuare e selezionare l'applicazione nell'elenco. Dal **App Info** dello schermo, toccare **autorizzazioni** che verrà visualizzata una visualizzazione che mostra tutte le autorizzazioni concesse all'app:

[![Schermate da un dispositivo Android che mostra come trovare le autorizzazioni concesse a un'applicazione](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>Riepilogo

Questa guida è stata una discussione su come eseguire un servizio di Android in un processo remoto avanzata. Le differenze tra una variabile locale e un servizio remoto è stato illustrato, con alcuni motivi per cui un servizio remoto può essere utile per la stabilità e prestazioni di un'app Android. Dopo aver descritto come implementare un servizio remoto e come un client può comunicare con il servizio, la Guida non forniscono un modo per limitare l'accesso al servizio da solo ai client autorizzati.


## <a name="related-links"></a>Collegamenti correlati

- [gestore](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Messaggio](https://developer.xamarin.com/api/type/Android.OS.Message/)
- [Messenger](https://developer.xamarin.com/api/type/Android.OS.Messenger/)
- [ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)
- [L'attributo esportato](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [Servizi con i processi isolati e la classe di applicazione personalizzato non venga risolto correttamente overload](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [Processi e thread](https://developer.android.com/guide/components/processes-and-threads.html)
- [Manifesto Android - autorizzazioni](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [Suggerimenti per la protezione](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/MessengerServiceDemo/)
