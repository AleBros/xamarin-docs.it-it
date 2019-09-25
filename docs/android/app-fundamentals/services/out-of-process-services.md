---
title: Esecuzione di servizi Android in processi remoti
description: In genere, tutti i componenti di un'applicazione Android vengono eseguiti nello stesso processo. I servizi Android rappresentano un'eccezione significativa in quanto possono essere configurati per l'esecuzione nei propri processi e condivisi con altre applicazioni, incluse quelle di altri sviluppatori Android. Questa guida illustra come creare e usare un servizio remoto Android usando Novell.
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 5429f260399602b7ef15e8263bc74cb8ae940f4f
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "70754879"
---
# <a name="running-android-services-in-remote-processes"></a>Esecuzione di servizi Android in processi remoti

_In genere, tutti i componenti di un'applicazione Android vengono eseguiti nello stesso processo. I servizi Android rappresentano un'eccezione significativa in quanto possono essere configurati per l'esecuzione nei propri processi e condivisi con altre applicazioni, incluse quelle di altri sviluppatori Android. Questa guida illustra come creare e usare un servizio remoto Android usando Novell._

## <a name="out-of-process-services-overview"></a>Panoramica di servizi out-of-process

All'avvio di un'applicazione, Android crea un processo in cui eseguire l'applicazione. In genere, tutti i componenti dell'applicazione vengono eseguiti in questo processo. I servizi Android rappresentano un'eccezione significativa in quanto possono essere configurati per l'esecuzione nei propri processi e condivisi con altre applicazioni, incluse quelle di altri sviluppatori Android. Questi tipi di servizi sono definiti servizi _remoti_ o _Servizi out-of-process_. Il codice per questi servizi sarà contenuto nello stesso APK dell'applicazione principale; Tuttavia, quando il servizio viene avviato, Android creerà un nuovo processo solo per quel servizio. Al contrario, un servizio che viene eseguito nello stesso processo del resto dell'applicazione viene talvolta definito _servizio locale_.

In generale, non è necessario che un'applicazione implementi un servizio remoto. Un servizio locale è sufficiente (e auspicabile) per i requisiti di un'app in molti casi. Un out-of-process ha il proprio spazio di memoria che deve essere gestito da Android. Anche se ciò comporta un sovraccarico per l'applicazione complessiva, esistono alcuni scenari in cui può risultare vantaggioso eseguire un servizio nel proprio processo:

1. **Funzionalità di condivisione** &ndash; Alcuni sviluppatori di applicazioni possono avere più app e funzionalità condivise tra tutte le applicazioni. Il packaging di tale funzionalità in un servizio Android che viene eseguito in un processo può semplificare la manutenzione dell'applicazione. È anche possibile creare un pacchetto del servizio nel proprio APK autonomo e distribuirlo separatamente dal resto dell'applicazione.
2. **Miglioramento dell'esperienza utente** &ndash; Esistono due possibili modi per migliorare l'esperienza utente dell'applicazione da parte di un servizio out-of-process. Il primo metodo riguarda la gestione della memoria. Quando si verifica un ciclo di Garbage Collection (GC), Android sospenderà tutte le attività del processo fino al completamento del Garbage Collector. L'utente potrebbe percepire questa pausa come "balbuzie" o "Jank". Quando un servizio è in esecuzione nel proprio processo, è il processo del servizio che viene sospeso, non del processo dell'applicazione. Questa pausa sarà molto meno nota all'utente perché il processo dell'applicazione (e quindi l'interfaccia utente) non è sospeso.

    In secondo luogo, se i requisiti di memoria di un processo diventano troppo grandi, è possibile che Android uccida il processo per liberare risorse per il dispositivo. Se un servizio ha un footprint di memoria di grandi dimensioni e viene eseguito nello stesso processo dell'interfaccia utente, quando Android Recupera forzatamente le risorse, l'interfaccia utente verrà arrestata, forzando l'utente ad avviare l'app. Tuttavia, se un servizio, in esecuzione nel proprio processo, viene arrestato da Android, il processo dell'interfaccia utente rimane inalterato. L'interfaccia utente può associare (e riavviare) il servizio, trasparenti all'utente e riprendere il funzionamento normale.

3. **Miglioramento delle prestazioni dell'applicazione** &ndash; Il processo dell'interfaccia utente può essere terminato o arrestato indipendentemente dal processo del servizio. Spostando le attività di avvio lunghe in un servizio out-of-process, è possibile che sia stato migliorato il tempo di avvio dell'interfaccia utente (presupponendo che il processo del servizio venga mantenuto attivo tra i tempi in cui l'interfaccia utente viene avviata).

In molti casi, l'associazione a un servizio in esecuzione in un altro processo equivale all' [associazione a un servizio locale](~/android/app-fundamentals/services/creating-a-service/bound-services.md). Il client richiamerà `BindService` per associare (e avviare, se necessario) il servizio. Verrà `Android.OS.IServiceConnection` creato un oggetto per gestire la connessione tra il client e il servizio. Se il client viene associato correttamente al servizio, Android restituirà un oggetto tramite l' `IServiceConnection` oggetto che può essere usato per richiamare i metodi nel servizio. Il client interagisce quindi con il servizio utilizzando questo oggetto. Per esaminare, di seguito sono riportati i passaggi da eseguire per l'associazione a un servizio:

- **Creare una finalità** &ndash; Per l'associazione al servizio è necessario utilizzare una finalità esplicita.
- **Implementare e creare un'istanza `IServiceConnection` di un oggetto** &ndash; l' `IServiceConnection` oggetto funge da intermediario tra il client e il servizio.  È responsabile del monitoraggio della connessione tra client e server.
- **Richiamare la `BindService`**  &ndash; chiamata al`BindService` metodo che invierà l'intento e la connessione del servizio creata nei passaggi precedenti ad Android, che si occuperà di avviare il servizio e stabilire la comunicazione tra client e servizio.

La necessità di attraversare i limiti del processo introduce una maggiore complessità: la comunicazione è unidirezionale (da client a server) e il client non può richiamare direttamente i metodi sulla classe del servizio. Ricordare che quando un servizio esegue lo stesso processo del client, Android fornisce un `IBinder` oggetto che può consentire la comunicazione bidirezionale. Questo non avviene con il servizio in esecuzione nel proprio processo. Un client comunica con un servizio remoto con il supporto della `Android.OS.Messenger` classe.

Quando un client richiede l'associazione al servizio remoto, Android richiamerà il `Service.OnBind` metodo Lifecycle, che restituirà l'oggetto `IBinder` interno incapsulato da `Messenger`. È un thin wrapper su un'implementazione speciale `IBinder` fornita dal Android SDK. `Messenger` `Messenger` Si occupa della comunicazione tra i due diversi processi. Lo sviluppatore non è interessato ai dettagli della serializzazione di un messaggio, al marshalling del messaggio attraverso il limite del processo e alla relativa deserializzazione nel client. Questa operazione viene gestita dall' `Messenger` oggetto. Questo diagramma mostra i componenti Android sul lato client interessati quando un client avvia il binding a un servizio out-of-process:

![Diagramma che mostra i passaggi e i componenti per un'associazione client a un servizio](out-of-process-services-images/ipc-01.png "Diagramma che mostra i passaggi e i componenti per un'associazione client a un servizio.")

La `Service` classe nel processo remoto passerà attraverso le stesse richiamate del ciclo di vita che un servizio associato in un processo locale verrà attraversato e molte API interessano. `Service.OnCreate`viene usato per inizializzare `Handler` un oggetto e inserire `Messenger` l'oggetto nell'oggetto. Analogamente `OnBind` , viene sottoposto a override, ma anziché `IBinder` restituire un oggetto, `Messenger`il servizio restituirà.  In questo diagramma viene illustrato ciò che accade nel servizio quando un client viene associato a esso:

![Diagramma che mostra i passaggi e i componenti da cui viene eseguito il servizio quando viene associato da un client remoto](out-of-process-services-images/ipc-02.png "Diagramma che mostra i passaggi e i componenti da cui viene eseguito il servizio quando viene associato da un client remoto.")

Quando un `Message` servizio viene ricevuto da un servizio, viene elaborato da nell'istanza di `Android.OS.Handler`. Il servizio implementerà la propria `Handler` sottoclasse che deve eseguire `HandleMessage` l'override del metodo. Questo metodo viene richiamato da `Messenger` e `Message` riceve come parametro. Il `Handler` controllerà i `Message` metadati e utilizzerà tali informazioni per richiamare i metodi nel servizio.

La comunicazione unidirezionale si verifica quando un client crea `Message` un oggetto e lo invia al servizio usando il `Messenger.Send` metodo. `Messenger.Send`Serializza il e `Message` la mano che serializza i dati in Android, che instraderà il messaggio attraverso il limite del processo e il servizio.  La `Messenger` che è ospitata dal servizio creerà un `Message` oggetto dai dati in ingresso. Viene inserito in una coda, in cui i messaggi vengono inviati uno alla volta `Handler`a. `Message` Il `Handler` controllerà i metadati contenuti `Message` in e richiamerà `Service`i metodi appropriati in. Il diagramma seguente illustra i diversi concetti in azione:

![Diagramma che illustra come vengono passati i messaggi tra i processi](out-of-process-services-images/ipc-03.png "Diagramma che illustra il modo in cui i messaggi vengono passati tra i processi.")

In questa guida verranno illustrati i dettagli dell'implementazione di un servizio out-of-process. Verrà illustrato come implementare un servizio che deve essere eseguito nel proprio processo e come un client può comunicare con tale servizio usando il `Messenger` Framework. Verrà inoltre illustrata brevemente la comunicazione bidirezionale, ovvero il client che invia un messaggio a un servizio e il servizio che invia un messaggio al client. Poiché i servizi possono essere condivisi tra diverse applicazioni, in questa guida verrà illustrata anche una tecnica per limitare l'accesso client al servizio tramite le autorizzazioni di Android.

> [!IMPORTANT]
> [Bugzilla 51940/GitHub 1950: i servizi con processi isolati e la classe di applicazione personalizzata non riescono a risolvere correttamente gli overload](https://github.com/xamarin/xamarin-android/issues/1950) segnalano che un servizio Novell. Android non verrà `IsolatedProcess` avviato correttamente quando `true`l'oggetto è impostato su. Questa guida è disponibile per un riferimento. Un'applicazione Novell. Android deve comunque essere in grado di comunicare con un servizio out-of-process scritto in Java.

## <a name="requirements"></a>Requisiti

Questa guida presuppone una certa familiarità con la creazione di servizi.

Sebbene sia possibile usare gli Intent impliciti con le app destinate alle API Android precedenti, questa guida si concentra esclusivamente sull'uso di Intent espliciti. Per le app destinate a Android 5,0 (livello API 21) o versione successiva è necessario usare un preventivo esplicito per l'associazione a un servizio. Questa è la tecnica che verrà illustrata in questa guida.

## <a name="create-a-service-that-runs-in-a-separate-process"></a>Creare un servizio che viene eseguito in un processo separato

Come descritto in precedenza, il fatto che un servizio sia in esecuzione nel proprio processo significa che sono implicate alcune API diverse. Per una rapida panoramica, ecco i passaggi da eseguire per l'associazione e l'utilizzo di un servizio remoto:  

- **`Service`**  Creareunasottoclassedeltipoeimplementareimetodidelciclodi&ndash;vitaper `Service` un servizio associato. È anche necessario impostare metadati che comunicheranno a Android che il servizio deve essere eseguito in un processo.
- **Implementare un `Handler`**  èresponsabiledell'`Handler` analisi delle richieste client, dell'estrazione di tutti i parametri passati dal client e della chiamata dei metodi appropriati sul servizio. &ndash;
- **`Messenger` Creare** un'istanza di un &ndash; come descritto in `Service` precedenza, `Messenger` ognuno deve mantenere un'istanza della classe che instraderà le `Handler` richieste client all'oggetto creato nel passaggio precedente.

Un servizio che deve essere eseguito nel proprio processo è fondamentalmente ancora un servizio associato. La classe del servizio estenderà `Service` la classe di base e verrà `ServiceAttribute` decorata con l'oggetto contenente i metadati che Android deve raggruppare nel manifesto Android. Per iniziare, le seguenti proprietà di `ServiceAttribute` che sono importanti per un servizio out-of-process:

1. `Exported`Questa proprietà deve essere impostata su `true` per consentire ad altre applicazioni di interagire con il servizio. &ndash; Il valore predefinito di questa proprietà è `false`.
2. `Process`&ndash; Questa proprietà deve essere impostata. Viene utilizzato per specificare il nome del processo in cui verrà eseguito il servizio.
3. `IsolatedProcess`&ndash; Questa proprietà consente una maggiore sicurezza, indicando a Android di eseguire il servizio in una sandbox isolata con autorizzazioni minime per interagire con il resto del sistema. Vedere [Bugzilla 51940-i servizi con processi isolati e la classe di applicazione personalizzata non riescono a risolvere correttamente gli overload](https://bugzilla.xamarin.com/show_bug.cgi?id=51940).
4. `Permission`&ndash; È possibile controllare l'accesso client al servizio specificando un'autorizzazione che i client devono richiedere (ed essere concessi).

Per eseguire un servizio di un proprio processo, `Process` è `ServiceAttribute` necessario impostare la proprietà in sul nome del servizio. Per interagire con le applicazioni esterne, `Exported` la proprietà deve essere impostata `true`su. Se `Exported` è`false`, solo i client nello stesso apk (ovvero la stessa applicazione) e in esecuzione nello stesso processo saranno in grado di interagire con il servizio.

Il tipo di processo in cui il servizio viene eseguito dipende dal valore della `Process` proprietà. Android identifica tre diversi tipi di processi:

- **Processo privato** &ndash; Un processo privato è disponibile solo per l'applicazione che l'ha avviata. Per identificare un processo come privato, il nome deve iniziare con un carattere **:** (punto e virgola). Il servizio illustrato nel frammento di codice precedente e lo screenshot è un processo privato. Il frammento di codice seguente è un esempio `ServiceAttribute`di:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

- **Processo globale** &ndash; Un servizio che viene eseguito in un processo globale è accessibile a tutte le applicazioni in esecuzione nel dispositivo. Un processo globale deve essere un nome di classe completo che inizia con un carattere minuscolo.
    (A meno che non vengano eseguiti passaggi per proteggere il servizio, altre applicazioni possono associarsi e interagire con esso. La protezione del servizio da un utilizzo non autorizzato verrà descritta più avanti in questa guida.

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

- **Processo isolato** &ndash; Un processo isolato è un processo che viene eseguito nella propria sandbox, isolato dal resto del sistema e senza autorizzazioni speciali. Per eseguire un servizio in un processo isolato, la `IsolatedProcess` proprietà `ServiceAttribute` di viene impostata su `true` , come illustrato nel frammento di codice seguente:
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> Vedere [Bugzilla 51940-i servizi con processi isolati e la classe di applicazione personalizzata non riescono a risolvere correttamente gli overload](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

Un servizio isolato è un modo semplice per proteggere un'applicazione e il dispositivo da codice non attendibile. Ad esempio, un'app può scaricare ed eseguire uno script da un sito Web. In questo caso, l'esecuzione di questa operazione in un processo isolato fornisce un ulteriore livello di sicurezza contro il codice non attendibile che compromette il dispositivo Android.

> [!IMPORTANT]
> Una volta che un servizio è stato esportato, il nome del servizio non deve essere modificato. La modifica del nome del servizio può comportare l'interruzione di altre applicazioni che utilizzano il servizio.

Per verificare l'effetto della `Process` proprietà, lo screenshot seguente mostra un servizio in esecuzione nel proprio processo privato:

![Screenshot che mostra un servizio in esecuzione in un processo privato](out-of-process-services-images/ipc-04.png "Screenshot che mostra un servizio in esecuzione in un processo privato.")

Lo screenshot seguente mostra `Process="com.xamarin.xample.messengerservice.timestampservice_process"` e il servizio in esecuzione in un processo globale:

![Screenshot di un servizio in esecuzione in un processo globale](out-of-process-services-images/ipc-05.png "Screenshot di un servizio in esecuzione in un processo globale.")

Una volta impostato, il servizio deve implementare un `Handler`. `ServiceAttribute`

### <a name="implementing-a-handler"></a>Implementazione di un gestore

Per elaborare le richieste client, il servizio deve implementare `Handler` un oggetto ed `HandleMessage` eseguire l'override di methodThis è `Message` il metodo accetta un'istanza che incapsula la chiamata al metodo dal client e traduce la chiamata in un'azione o un'attività che il servizio eseguirà. L' `Message` oggetto espone una proprietà denominata `What` che è un valore integer, il cui significato è condiviso tra il client e il servizio e si riferisce a un'attività che il servizio deve eseguire per il client.

Il seguente frammento di codice dell'applicazione di esempio mostra un `HandleMessage`esempio di. In questo esempio sono disponibili due azioni che un client può richiedere per il servizio:

- La prima azione è un messaggio _Hello, World_ , che il client ha inviato un semplice messaggio al servizio.
- La seconda azione richiamerà un metodo sul servizio e recupererà una stringa, in questo caso la stringa è un messaggio che restituisce l'ora di avvio del servizio e il tempo di esecuzione:

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
                // Call methods on the service to retrieve a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

È anche possibile creare un pacchetto di parametri per il servizio in `Message`. Questo argomento verrà illustrato più avanti in questa guida. L'argomento successivo da considerare è la creazione `Messenger` dell'oggetto per elaborare le istanze `Message`di in ingresso.

### <a name="instantiating-the-messenger"></a>Creazione di un'istanza di Messenger

Come descritto in precedenza, la deserializzazione dell' `Message` oggetto e il `Handler.HandleMessage` richiamo `Messenger` è la responsabilità dell'oggetto. La `Messenger` classe fornisce inoltre un `IBinder` oggetto che il client utilizzerà per inviare messaggi al servizio.  

Quando il servizio viene avviato, crea un'istanza `Messenger` di e `Handler`inserisce. Un posto ideale per eseguire questa inizializzazione è il `OnCreate` metodo del servizio. Questo frammento di codice è un esempio di un servizio che inizializza `Handler` i `Messenger`propri e:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

A questo punto, il passaggio finale consiste `Service` nell'eseguire l'override `OnBind`di.

### <a name="implementing-serviceonbind"></a>Implementazione di Service. onBind

Tutti i servizi associati, indipendentemente dal fatto che vengano eseguiti nel proprio processo, devono `OnBind` implementare il metodo. Il valore restituito di questo metodo è un oggetto che il client può usare per interagire con il servizio. Esattamente l'oggetto dipende dal fatto che il servizio sia un servizio locale o un servizio remoto. Mentre un servizio locale restituisce un'implementazione personalizzata `IBinder` , un servizio remoto restituisce l'oggetto `IBinder` incapsulato, ma `Messenger` creato nella sezione precedente:

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

Una volta eseguiti questi tre passaggi, il servizio remoto può essere considerato completo.

## <a name="consuming-the-service"></a>Utilizzo del servizio

Tutti i client devono implementare codice per poter associare e utilizzare il servizio remoto. Concettualmente, dal punto di vista del client, esistono pochissime differenze tra l'associazione al servizio locale o un servizio remoto. Il client richiama il `BindService` metodo, passando un preventivo esplicito per identificare il servizio e un `IServiceConnection` che consente di gestire la connessione tra il client e il servizio.

Questo frammento di codice è un esempio di come creare una **finalità esplicita** per l'associazione a un servizio remoto. Lo scopo deve identificare il pacchetto che contiene il servizio e il nome del servizio. Un modo per impostare queste informazioni consiste nell'usare un `Android.Content.ComponentName` oggetto e fornire tale informazioni allo scopo. Questo frammento di codice è un esempio:  

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

Quando il servizio viene associato, il `IServiceConnection.OnServiceConnected` metodo viene richiamato e fornisce `IBinder` un oggetto a un client. Tuttavia, il client non utilizzerà `IBinder`direttamente. Bensì creerà un'istanza `Messenger` `IBinder`di un oggetto. Si tratta dell' `Messenger` oggetto che verrà utilizzato dal client per interagire con il servizio remoto.

Di seguito è riportato un esempio di un'implementazione `IServiceConnection` di base che illustra in che modo un client gestirà la connessione e la disconnessione da un servizio. Si noti che `OnServiceConnected` il metodo riceve `IBinder`e e il client crea un `Messenger` oggetto da `IBinder`tale:

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

Una volta create la connessione al servizio e lo scopo, è possibile che il client chiami `BindService` e avvii il processo di associazione:

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

Dopo che il client è stato associato correttamente al servizio e `Messenger` è disponibile, è possibile che il client invii `Messages` al servizio.

## <a name="sending-messages-to-the-service"></a>Invio di messaggi al servizio

Una volta che il client è connesso e `Messenger` contiene un oggetto, è possibile comunicare con il servizio inviando `Message` oggetti tramite `Messenger`. Questa comunicazione è unidirezionale, il client invia il messaggio, ma non viene restituito alcun messaggio dal servizio al client. In questo senso, `Message` è un meccanismo di Fire-and-Forget.

Il modo migliore per creare un `Message` oggetto consiste nell'usare il [`Message.Obtain`](xref:Android.OS.Message) metodo factory. Questo metodo effettuerà il `Message` pull di un oggetto da un pool globale gestito da Android. `Message.Obtain`dispone inoltre di alcuni metodi di overload che consentono l' `Message` inizializzazione dell'oggetto con i valori e i parametri richiesti dal servizio.  Quando viene `Message` creata un'istanza dell'oggetto, questo viene inviato al servizio `Messenger.Send`chiamando. Questo frammento di codice è un esempio di creazione e invio `Message` di un al processo del servizio:

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

Esistono diverse forme del `Message.Obtain` metodo. Nell'esempio precedente viene usato [`Message.Obtain(Handler h, Int32 what)`](xref:Android.OS.Message.Obtain). Poiché si tratta di una richiesta asincrona a un servizio out-of-process. il servizio non fornirà alcuna risposta, pertanto l'oggetto `Handler` è impostato su `null`. Il secondo parametro, `Int32 what`, verrà archiviato `.What` nella proprietà dell' `Message` oggetto. La `.What` proprietà viene utilizzata dal codice nel processo del servizio per richiamare i metodi nel servizio.

La `Message` classe espone inoltre due proprietà aggiuntive che possono essere utilizzate per il destinatario: `Arg1` e `Arg2`. Queste due proprietà sono valori integer che possono avere alcuni valori concordati speciali che hanno un significato tra il client e il servizio. Ad esempio, `Arg1` può disporre di un ID cliente `Arg2` e può conservare un numero di ordine di acquisto per quel cliente. È [`Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)`](xref:Android.OS.Message.Obtain) possibile utilizzare l'oggetto per impostare le due proprietà quando `Message` viene creato l'oggetto. Un altro modo per popolare questi due valori consiste nell'impostare `.Arg` le `.Arg2` proprietà e direttamente `Message` nell'oggetto dopo che è stato creato.

### <a name="passing-additional-values-to-the-service"></a>Passaggio di valori aggiuntivi al servizio

È possibile passare dati più complessi al servizio usando un `Bundle`. In questo caso, i valori aggiuntivi possono essere inseriti in `Bundle` un oggetto e inviati insieme `Message` a impostando la proprietà della [ `.Data` proprietà](xref:Android.OS.Message.Data) prima dell'invio.

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```

> [!NOTE]
> In generale, un `Message` non deve avere un payload maggiore di 1 MB. Il limite delle dimensioni può variare in base alla versione di Android e alle eventuali modifiche proprietarie che il fornitore potrebbe avere apportato all'implementazione del progetto open source Android (AOSP) in bundle con il dispositivo.

## <a name="returning-values-from-the-service"></a>Restituzione di valori dal servizio

L'architettura di messaggistica discussa a questo punto è unidirezionale, il client invia un messaggio al servizio. Se è necessario che il servizio restituisca un valore a un client, tutto ciò che è stato discusso a questo punto viene invertito. Il servizio deve creare un `Message`oggetto, creare un pacchetto di tutti i valori restituiti `Message` e inviare `Messenger` il tramite un al client. Tuttavia, il servizio non crea il proprio `Messenger`, bensì si basa sulla creazione di istanze del client e sul pacchetto a `Messenger` come parte della richiesta iniziale. Il servizio utilizzerà `Send` il messaggio fornito `Messenger`dal client.  

La sequenza di eventi per la comunicazione bidirezionale è la seguente:

1. Il client viene associato al servizio. Quando il servizio e il client si connettono, l' `IServiceConnection` oggetto gestito dal client avrà un riferimento a un `Messenger` oggetto utilizzato per trasmettere `Message`al servizio. Per evitare confusione, questo verrà definito _Messenger del servizio_.
2. Il client crea un' `Handler` istanza di (definita _gestore client_) e la usa per inizializzare il proprio `Messenger` (il _messaggero client_). Si noti che il messaggero del servizio e il client Messenger sono due oggetti diversi che gestiscono il traffico in due direzioni diverse. Il servizio Messenger gestisce i messaggi dal client al servizio, mentre il messaggero client gestirà i messaggi dal servizio al client.
3. Il client crea un `Message` oggetto e imposta la `ReplyTo` proprietà con il messaggero client. Il messaggio viene quindi inviato al servizio usando Service Messenger.
4. Il servizio riceve il messaggio dal client ed esegue il lavoro richiesto.
5. Quando è il momento in cui il servizio Invia la risposta al client, `Message.Obtain` utilizzerà per creare un nuovo `Message` oggetto.
6. Per inviare questo messaggio al client, il servizio estrae il messaggero client dalla `.ReplyTo` proprietà del messaggio client e lo usa `Message` per `.Send` tornare al client.
7. Quando la risposta viene ricevuta dal client, dispone `Handler` di un proprio oggetto che elaborerà l'oggetto `Message` esaminando la `.What` proprietà (e `Message`, se necessario, estraendo tutti i parametri contenuti in).

Questo codice di esempio illustra come il client creerà un'istanza `Message` di e il `Messenger` pacchetto a che il servizio deve usare per la risposta:

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

Il servizio deve apportare alcune modifiche al proprio `Handler` per estrarre il `Messenger` e usarlo per inviare risposte al client. Questo frammento di codice è un esempio del modo in `Handler` cui il servizio `Message` creerebbe un oggetto e lo invierà di nuovo al client:  

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

Si noti che negli esempi di codice precedenti, `Messenger` l'istanza creata dal client *non* è lo stesso oggetto ricevuto dal servizio. Si tratta di due `Messenger` oggetti diversi in esecuzione in due processi distinti che rappresentano il canale di comunicazione.

## <a name="securing-the-service-with-android-permissions"></a>Sicurezza del servizio con le autorizzazioni di Android

Un servizio che viene eseguito in un processo globale è accessibile da tutte le applicazioni in esecuzione su tale dispositivo Android. In alcune situazioni, questa apertura e disponibilità non è auspicabile ed è necessario proteggere il servizio dall'accesso da client non autorizzati. Un modo per limitare l'accesso al servizio remoto consiste nell'usare le autorizzazioni di Android.

Le autorizzazioni possono essere identificate `Permission` dalla proprietà dell' `ServiceAttribute` oggetto che decora la `Service` sottoclasse. Verrà denominata un'autorizzazione che il client deve concedere quando viene associato al servizio. Se il client non dispone delle autorizzazioni appropriate, Android genererà un' `Java.Lang.SecurityException` operazione quando il client tenterà di eseguire l'associazione al servizio.

Sono disponibili quattro diversi livelli di autorizzazione offerti da Android:

- **normale** &ndash; Questo è il livello di autorizzazione predefinito. Viene usato per identificare le autorizzazioni a basso rischio che possono essere concesse automaticamente da Android ai client che lo richiedono. L'utente non deve concedere esplicitamente queste autorizzazioni, ma le autorizzazioni possono essere visualizzate nelle impostazioni dell'app.
- **firma** di &ndash; Si tratta di una speciale categoria di autorizzazioni che verrà concessa automaticamente da Android per le applicazioni che sono tutte firmate con lo stesso certificato. Questa autorizzazione è progettata per semplificare la condivisione di componenti o dati tra le app da parte di uno sviluppatore di applicazioni senza infastidire l'utente per le approvazioni costanti.
- **signatureOrSystem** Questa operazione è molto simile alle autorizzazioni di firma descritte in precedenza. &ndash; Oltre a essere concesso automaticamente alle app firmate dallo stesso certificato, questa autorizzazione viene concessa anche alle app che hanno firmato lo stesso certificato usato per firmare le app installate con l'immagine del sistema Android. Questa autorizzazione viene in genere usata solo dagli sviluppatori di Android ROM per consentire alle applicazioni di usare app di terze parti. Non viene comunemente usato dalle app destinate a una distribuzione generale per il pubblico di grandi dimensioni.
- **rischioso** &ndash; Le autorizzazioni pericolose sono quelle che possono causare problemi per l'utente. Per questo motivo, le autorizzazioni **pericolose** devono essere approvate in modo esplicito dall'utente.

Poiché `signature` le `normal` autorizzazioni e vengono concesse automaticamente in fase di installazione da Android, è fondamentale che l'APK che ospita il servizio venga installata **prima** dell'APK contenente il client. Se il client viene installato per primo, Android non concederà le autorizzazioni. In questo caso, sarà necessario disinstallare l'apk client, installare l'APK del servizio e quindi reinstallare l'APK client.

Esistono due modi comuni per proteggere un servizio con le autorizzazioni di Android:

1. **Implementare la sicurezza a livello di firma** &ndash; La sicurezza a livello di firma indica che l'autorizzazione viene concessa automaticamente alle applicazioni firmate con la stessa chiave usata per firmare l'APK che contiene il servizio. Si tratta di un modo semplice per gli sviluppatori di proteggere il proprio servizio ma di mantenerli accessibili dalle proprie applicazioni. Le autorizzazioni `Permission` `ServiceAttribute` a livello di firma vengono dichiarate impostando `signature`la proprietà di su:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2. **Creare un'autorizzazione personalizzata** &ndash; È possibile che lo sviluppatore del servizio crei un'autorizzazione personalizzata per il servizio. Si tratta di una scelta ottimale quando uno sviluppatore desidera condividere il proprio servizio con le applicazioni di altri sviluppatori. Un'autorizzazione personalizzata richiede un po' più di impegno per implementare e verrà illustrata di seguito.

Un esempio semplificato della creazione di `normal` un'autorizzazione personalizzata verrà descritto nella sezione successiva. Per ulteriori informazioni sulle autorizzazioni di Android, consultare la documentazione di Google per le [procedure consigliate & sicurezza](https://developer.android.com/training/articles/security-tips.html). Per altre informazioni sulle autorizzazioni di Android, vedere la [sezione autorizzazioni](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) della documentazione di Android per il manifesto dell'applicazione per altre informazioni sulle autorizzazioni per Android.

> [!NOTE]
> In generale, [Google sconsiglia l'utilizzo di autorizzazioni personalizzate](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) , in quanto questi possono dimostrare confusione per gli utenti.

### <a name="creating-a-custom-permission"></a>Creazione di un'autorizzazione personalizzata

Per usare un'autorizzazione personalizzata, viene dichiarata dal servizio mentre il client richiede esplicitamente tale autorizzazione.

Per creare un'autorizzazione nell'apk del servizio, viene `permission` aggiunto un elemento `manifest` all'elemento in **file AndroidManifest. XML**. Questa autorizzazione deve avere gli `name`attributi `protectionLevel`, e `label` impostati. L' `name` attributo deve essere impostato su una stringa che identifica in modo univoco l'autorizzazione. Il nome verrà visualizzato nella visualizzazione **info app** delle **Impostazioni Android** , come illustrato nella sezione successiva.

L' `protectionLevel` attributo deve essere impostato su uno dei quattro valori stringa descritti in precedenza.  `label` E`description` devono fare riferimento alle risorse di stringa e vengono utilizzate per fornire all'utente un nome descrittivo e una descrizione.

Questo frammento di codice è un esempio di Dichiarazione `permission` di un attributo personalizzato in **file AndroidManifest. XML** dell'APK che contiene il servizio:

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

Quindi, **file AndroidManifest. XML** dell'APK client deve richiedere esplicitamente la nuova autorizzazione. Questa operazione viene eseguita aggiungendo l' `users-permission` attributo a **file AndroidManifest. XML**:

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

### <a name="view-the-permissions-granted-to-an-app"></a>Visualizzare le autorizzazioni concesse a un'app

Per visualizzare le autorizzazioni concesse a un'applicazione, aprire l'app Impostazioni Android e selezionare **app**. Trovare e selezionare l'applicazione nell'elenco. Dalla schermata delle **informazioni sull'app** , toccare **autorizzazioni** per visualizzare una visualizzazione che Mostra tutte le autorizzazioni concesse all'app:

[![Screenshot di un dispositivo Android che illustra come trovare le autorizzazioni concesse a un'applicazione](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>Riepilogo

Questa guida è stata una discussione avanzata su come eseguire un servizio Android in un processo remoto. Sono state illustrate le differenze tra un servizio locale e un servizio remoto, oltre ad alcuni motivi per cui un servizio remoto può essere utile per la stabilità e le prestazioni di un'app per Android. Dopo aver illustrato come implementare un servizio remoto e come un client può comunicare con il servizio, la guida è passata a fornire un modo per limitare l'accesso al servizio solo dai client autorizzati.

## <a name="related-links"></a>Collegamenti correlati

- [Gestore](xref:Android.OS.Handler)
- [Messaggio](xref:Android.OS.Message)
- [Messenger](xref:Android.OS.Messenger)
- [ServiceAttribute](xref:Android.App.ServiceAttribute)
- [Attributo esportato](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [I servizi con processi isolati e la classe di applicazione personalizzata non riescono a risolvere correttamente gli overload](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [Processi e thread](https://developer.android.com/guide/components/processes-and-threads.html)
- [Manifesto Android-autorizzazioni](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [Suggerimenti sulla sicurezza](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-messengerservicedemo)
