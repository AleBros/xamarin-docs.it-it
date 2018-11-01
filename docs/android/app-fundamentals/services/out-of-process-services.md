---
title: Servizi di Android in esecuzione in processi remoti
description: In generale, tutti i componenti in un'applicazione Android verranno eseguito nello stesso processo. Servizi Android sono un'eccezione a questo in quanto possono essere configurati per l'esecuzione in processi propri e condivisa con altre applicazioni, inclusi quelli di altri sviluppatori di Android. Questa guida illustra come creare e usare un servizio remoto Android usando Xamarin.
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 8514d3b2c423e524d03a800f5f56359f3aee4b75
ms.sourcegitcommit: 650fd5813e243d67eea13c4bc76683c0f8134123
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50737193"
---
# <a name="running-android-services-in-remote-processes"></a>Servizi di Android in esecuzione in processi remoti

_In generale, tutti i componenti in un'applicazione Android verranno eseguito nello stesso processo. Servizi Android sono un'eccezione a questo in quanto possono essere configurati per l'esecuzione in processi propri e condivisa con altre applicazioni, inclusi quelli di altri sviluppatori di Android. Questa guida illustra come creare e usare un servizio remoto Android usando Xamarin._

## <a name="out-of-process-services-overview"></a>Out-of Cenni preliminari sui servizi di processo

All'avvio di un'applicazione, Android consente di creare un processo in cui eseguire l'applicazione. In genere, tutti i componenti dell'applicazione verrà eseguito in questa unico processo. Servizi Android sono un'eccezione a questo in quanto possono essere configurati per l'esecuzione in processi propri e condivisa con altre applicazioni, inclusi quelli di altri sviluppatori di Android. Questi tipi di servizi sono detti _servizi remoti_ oppure _out-of-process servizi_. Il codice per questi servizi sarà contenuto nell'APK stesso come applicazione principale. Tuttavia, quando viene avviato il servizio Android creerà un nuovo processo per il proprio servizio. Al contrario, un servizio che viene eseguito nello stesso processo del resto dell'applicazione viene talvolta definito come un _servizio locale_.

Non è in genere necessario per un'applicazione implementare un servizio remoto. Un servizio locale è sufficiente (e consigliabile) per i requisiti di un'app in molti casi. Un out-of-process ha proprio spazio di memoria che deve essere gestito da Android. Sebbene questa operazione comporta un rallentamento delle prestazioni per l'applicazione globale, esistono alcuni scenari in cui può risultare vantaggioso per l'esecuzione di un servizio in un proprio processo:

1. **Funzionalità di condivisione** &ndash; alcuni sviluppatori di applicazioni possono avere più App e le funzionalità condivise tra tutte le applicazioni. Creazione del pacchetto che la funzionalità in un servizio di Android che viene eseguito in un processo può semplificare la manutenzione dell'applicazione. È anche possibile creare un pacchetto del servizio in un proprio file APK autonomo e distribuirlo separatamente dal resto dell'applicazione.
2. **Miglioramento dell'esperienza utente** &ndash; esistono due modi possibili che un servizio out-of-process può migliorare l'esperienza utente dell'applicazione. Nel primo caso riguarda la gestione della memoria. Quando un'operazione di garbage collection (GC) si verifica ciclo Android sospenderà tutte le attività del processo fino a quando il Garbage Collector è stato completato. L'utente potrà percepire questa pausa come "segnalazione" o "jank". Quando un servizio è in esecuzione proprio processo, è il processo del servizio che è stata sospesa, non il processo dell'applicazione. Questa pausa sarà molto meno evidente all'utente come il processo dell'applicazione (e pertanto l'interfaccia utente) non viene sospesa.

    In secondo luogo, se i requisiti di memoria di un processo diventa troppo grande, Android potrebbe essere terminato il processo per liberare risorse per il dispositivo. Se un servizio ha un footprint di memoria di grandi dimensioni ed è in esecuzione nello stesso processo come l'interfaccia utente, quindi quando Android forzatamente viene recuperato da tali risorse l'interfaccia utente verrà arrestata, l'utente per avviare l'app. Tuttavia, se un servizio, in esecuzione in un processo viene arrestato da Android, il processo dell'interfaccia utente rimane invariato. L'interfaccia utente può eseguire l'associazione (e riavviare) del servizio, trasparente per l'utente e resume normale funzionamento.

3. **Miglioramento delle prestazioni dell'applicazione** &ndash; processo dell'interfaccia utente può essere terminata o arrestato, indipendentemente dal processo del servizio. Spostando le attività di avvio di lunga durata a un servizio out-of-process, è possibile che il tempo di avvio dell'interfaccia utente migliorata forse (presupponendo che il processo del servizio viene mantenuto attivo tra gli orari in cui viene avviata l'interfaccia utente).

In molti modi, associazione a un servizio in esecuzione in un altro processo è identico [associazione a un servizio locale](~/android/app-fundamentals/services/creating-a-service/bound-services.md). Il client richiamerà `BindService` associare (e avviare, se necessario) al servizio. Un `Android.OS.IServiceConnection` oggetto verrà creato per gestire la connessione tra il client e il servizio. Se il client esegue correttamente il binding al servizio, quindi Android restituirà un oggetto tramite il `IServiceConnection` che può essere utilizzato per richiamare metodi sul servizio. Il client interagisce quindi con il servizio utilizzando questo oggetto. Per esaminare, ecco i passaggi per l'associazione a un servizio:

* **Creare un Intent** &ndash; un intent esplicito deve essere usato per l'associazione al servizio.
* **Implementare e Instantiate un' `IServiceConnection` oggetti** &ndash; il `IServiceConnection` oggetto funge da intermediario tra il client e il servizio.  È responsabile per la connessione tra client e server di monitoraggio.
* **Richiamare il `BindService` metodo** &ndash; chiamata `BindService` invierà la finalità e la connessione al servizio creato nei passaggi precedenti per Android, che si occuperà di avvio del servizio e stabilire la comunicazione tra client e servizio.

La necessità di attraversare i limiti di processo introdurre complessità aggiuntive: la comunicazione è unidirezionale (client a server) e il client non è possibile richiamare direttamente metodi sulla classe del servizio. È importante ricordare che quando un servizio è in esecuzione lo stesso processo del client, Android offre un `IBinder` oggetto che potrebbe consentire la comunicazione bidirezionale. Ciò non avviene con il servizio in esecuzione nel proprio processo. Un client comunica con un servizio remoto con l'aiuto del `Android.OS.Messenger` classe.

Quando un client richiede di eseguire l'associazione con il servizio remoto, Android richiamerà il `Service.OnBind` metodo del ciclo di vita, che verrà restituito l'oggetto interno `IBinder` oggetto incapsulato dal `Messenger`. Il `Messenger` è un wrapper sottile per un particolare `IBinder` implementazione che viene fornito da Android SDK. Il `Messenger` si occupa della comunicazione tra i due processi diversi. Lo sviluppatore è ignorare il concatenando i dettagli di serializzazione di un messaggio, il marshalling del messaggio attraverso il limite di processo e quindi deserializzandola sul client. Questa operazione viene gestita dal `Messenger` oggetto. Questo diagramma mostra i componenti di Android lato client che sono coinvolti quando un client avvia associazione a un servizio out-of-process:

![Diagramma che mostra i componenti e passaggi per un'associazione a un servizio client](out-of-process-services-images/ipc-01.png "diagramma che mostra i componenti e passaggi per un'associazione a un servizio client.")

Il `Service` classe nel processo remoto verrà esaminati i callback del ciclo di vita stesso che un servizio associato in un processo locale verrà esaminate e numerose API coinvolte sono uguali. `Service.OnCreate` Consente di inizializzare una `Handler` e inserire in `Messenger` oggetto. Analogamente, `OnBind` viene sottoposto a override, ma anziché restituire un `IBinder` oggetto, il servizio restituirà il `Messenger`.  Questo diagramma illustra cosa accade nel servizio quando un client sta effettuando l'associazione a esso:

![Diagramma che illustra i passaggi e componenti di un servizio attraversa quando viene associato a un client remoto](out-of-process-services-images/ipc-02.png "diagramma che illustra i passaggi e componenti di un servizio attraversa quando viene associato a un client remoto.")

Quando un `Message` viene ricevuto da un servizio, viene elaborato da nell'istanza di `Android.OS.Handler`. Il servizio verrà implementata la propria `Handler` sottoclasse che deve eseguire l'override di `HandleMessage` (metodo). Questo metodo viene richiamato per la `Messenger` e riceve il `Message` come parametro. Il `Handler` analizzerà le `Message` meta-data e utilizzare tali informazioni per richiamare metodi sul servizio.

Una comunicazione unidirezionale si verifica quando un client crea un `Message` dell'oggetto e lo invia al servizio usando il `Messenger.Send` (metodo). `Messenger.Send` eseguirà la serializzazione di `Message` e mano che i dati serializzati per Android, che il messaggio verrà instradato attraverso il limite di processo e il servizio.  Il `Messenger` ospitato dal servizio creerà un `Message` oggetto dai dati in ingresso. Ciò `Message` viene inserito in una coda, in cui i messaggi vengono inviati uno alla volta per il `Handler`. Il `Handler` analizzerà i metadati contenuti nel `Message` e richiamare i metodi adatti sul `Service`. Il diagramma seguente illustra questi concetti diversi in azione:

![Diagramma che illustra il modo in cui i messaggi vengono passati tra processi](out-of-process-services-images/ipc-03.png "diagramma che illustra il modo in cui i messaggi vengono passati tra processi.")

Questa guida illustra i dettagli di implementazione di un servizio out-of-process. Illustra come implementare un servizio che deve essere eseguito nel proprio processo e modo in cui un client può comunicare con tale servizio utilizzando il `Messenger` framework. Inoltre descrive brevemente le comunicazioni bidirezionali: il client invia un messaggio a un servizio e il servizio invia un messaggio di risposta al client. Poiché i servizi possono essere condivisi tra diverse applicazioni, questa guida illustra anche una tecnica per la limitazione dell'accesso client al servizio mediante le autorizzazioni di Android.

> [!IMPORTANT]
> [Bugzilla 1950 51940/GitHub - servizi con processi isolati e classe di applicazione personalizzata non venga risolto correttamente gli overload](https://github.com/xamarin/xamarin-android/issues/1950) i report che un servizio di xamarin. Android non si avvia correttamente quando il `IsolatedProcess` è impostata su `true`. Questa guida viene fornita per un riferimento. Un'applicazione xamarin. Android debba ancora essere in grado di comunicare con un servizio out-of-process è scritto in Java.

## <a name="requirements"></a>Requisiti

Questa guida presuppone familiarità con la creazione di servizi.

Sebbene sia possibile usare gli Intent impliciti con le app destinate a versioni precedenti API Android, questa Guida verrà illustrato esclusivamente l'uso di Intent esplicito. Le app destinate ad Android 5.0 (livello API 21) o successiva è necessario usare un intent esplicito per l'associazione con un servizio. si tratta della tecnica che verrà illustrato più avanti in questa Guida...

## <a name="create-a-service-that-runs-in-a-separate-process"></a>Creare un servizio che viene eseguito in un processo separato

Come descritto in precedenza, il fatto che un servizio è in esecuzione nel proprio processo significa che alcune API diverse sono coinvolti. Una rapida panoramica, di seguito vengono indicati i passaggi per associare e utilizzare un servizio remoto:  

* **Creare il `Service` sottoclasse** &ndash; sottoclasse il `Service` digitare e implementare i metodi del ciclo di vita per un servizio associato. È anche necessario impostare i metadati che indicano che il servizio venga eseguito nel proprio processo Android.
* **Implementare una `Handler`**  &ndash; il `Handler` è responsabile per analizzare le richieste del client, l'estrazione di tutti i parametri passati dal client e richiamare i metodi appropriati nel servizio.
* **Creare un'istanza di un `Messenger`**  &ndash; come descritto in precedenza, ogni `Service` necessario mantenere un'istanza del `Messenger` classe che instraderà le richieste client per il `Handler` che è stato creato nel passaggio precedente.

Un servizio che deve essere eseguito nel proprio processo è, fondamentalmente, ancora un servizio associato. La classe del servizio verrà estesa di base `Service` classe ed è decorata con il `ServiceAttribute` contenente i metadati che Android è necessario creare un bundle nel manifesto Android. Per iniziare, le proprietà seguenti del `ServiceAttribute` che sono importanti per un servizio out-of-process:

1. `Exported` &ndash; Questa proprietà deve essere impostata su `true` per consentire ad altre applicazioni di interagire con il servizio. Il valore predefinito di questa proprietà è `false`.
2. `Process` &ndash; Questa proprietà deve essere impostata. Viene utilizzato per specificare il nome del processo di cui verrà eseguito il servizio.
3. `IsolatedProcess` &ndash; Questa proprietà abiliterà la protezione aggiuntiva, che informa di Android per eseguire il servizio in una sandbox isolata con autorizzazioni minime a iteract con il resto del sistema. Visualizzare [Bugzilla 51940 - servizi con processi isolati e classe di applicazione personalizzata non venga risolto correttamente overload](https://bugzilla.xamarin.com/show_bug.cgi?id=51940).
4. `Permission` &ndash; È possibile controllare l'accesso client al servizio specificando un'autorizzazione che i client devono richiedere (e ottenere).

Per eseguire un servizio un proprio processo, il `Process` proprietà il `ServiceAttribute` deve essere impostato sul nome del servizio. Per interagire con le applicazioni esterne, il `Exported` proprietà deve essere impostata su `true`. Se `Exported` è `false`, solo i client nella stesso APK (ad esempio la stessa applicazione) e in esecuzione nello stesso processo sarà in grado di interagire con il servizio.

Il tipo di processo in cui verrà eseguito il servizio dipende dal valore della `Process` proprietà. Android identifica tre diversi tipi di processi:

-   **Processo privato** &ndash; un processo privato è quello che è disponibile solo per l'applicazione che l'ha avviata. Per identificare un processo privato, il relativo nome deve iniziare con una **:** (virgola). Il servizio descritto nel precedente frammento di codice e screenshot è un processo privato. Il frammento di codice seguente è riportato un esempio del `ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

-   **Processo globale** &ndash; un servizio che viene eseguito in un processo globale sia accessibile a tutte le applicazioni in esecuzione nel dispositivo. Un processo globale deve essere un nome di classe completo che inizia con una lettera minuscola.
    (A meno che non vengono adottate per proteggere il servizio, altre applicazioni possono eseguire l'associazione e interagire con esso. Protezione del servizio in uso non autorizzato verrà discusso più avanti in questa Guida.)

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

-   **Isolamento processo** &ndash; un processo isolato è un processo che viene eseguito nella propria sandbox, isolati dal resto del sistema e senza autorizzazioni speciali propri. Per eseguire un servizio in un processo isolato, il `IsolatedProcess` proprietà del `ServiceAttribute` è impostata su `true` come illustrato nel frammento di codice seguente:
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> Vedere [Bugzilla 51940 - servizi con processi isolati e classe di applicazione personalizzata non venga risolto correttamente overload](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

Un servizio di tipo isolato è un modo semplice per proteggere un'applicazione e il dispositivo con codice non attendibile. Ad esempio, un'app può scaricare ed eseguire uno script da un sito Web. In questo caso, si esegue in un processo isolato offre un ulteriore livello di sicurezza con codice non attendibile di compromettere il dispositivo Android.

> [!IMPORTANT]
> Una volta che un servizio è stato esportato, il nome del servizio non deve modificare. La modifica del nome del servizio può interrompere altre applicazioni che usano il servizio.

Per visualizzare l'effetto che la `Process` dispone di proprietà, lo screenshot seguente illustra un servizio in esecuzione nel proprio processo privato:

![Screenshot che illustra un servizio in esecuzione in un processo privato](out-of-process-services-images/ipc-04.png "Screenshot che illustra un servizio in esecuzione in un processo privato.")

In questo screenshot successivo illustra `Process="com.xamarin.xample.messengerservice.timestampservice_process"` e il servizio in esecuzione in un processo globale:

![Screenshot di un servizio in esecuzione in un processo globale](out-of-process-services-images/ipc-05.png "Screenshot di un servizio in esecuzione in un processo globale.")

Una volta il `ServiceAttribute` è stata impostata, il servizio deve implementare un `Handler`.

### <a name="implementing-a-handler"></a>Implementazione di un gestore

Per elaborare le richieste client, il servizio deve implementare una `Handler` ed eseguire l'override di `HandleMessage` methodThis è il metodo accetta un `Message` istanza quali che incapsula la chiamata al metodo dal client e lo converte tale chiamata in un'azione o attività che esegue il servizio. Il `Message` oggetto espone una proprietà denominata `What` che è un valore intero, il cui significato è condiviso tra il client e il servizio e si riferisce a un'attività che il servizio deve eseguire per il client.

Il seguente frammento di codice dall'applicazione di esempio mostra un esempio di `HandleMessage`. In questo esempio, esistono due azioni che un client può richiedere del servizio:

* La prima azione è un _Hello, World_ messaggio, il client ha inviato un messaggio semplice al servizio.
* La seconda azione verrà richiamare un metodo sul servizio e recuperare una stringa, in questo caso la stringa è un messaggio che restituisce ora il servizio avviato e per quanto tempo che è in esecuzione:

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

È anche possibile ai parametri del pacchetto per il servizio nel `Message`. Questo argomento verrà discusso più avanti in questa Guida. L'argomento successivo da considerare consiste nel creare il `Messenger` oggetto per l'elaborazione in ingresso `Message`s.

### <a name="instantiating-the-messenger"></a>Creazione di un'istanza di Messenger

Come illustrato in precedenza, la deserializzazione il `Message` oggetto e richiamando `Handler.HandleMessage` è la competenza del `Messenger` oggetto. Il `Messenger` classe fornisce inoltre un `IBinder` dell'oggetto che il client utilizzerà per inviare messaggi al servizio.  

All'avvio del servizio, creerà un'istanza di `Messenger` e inserire il `Handler`. Un buon punto di eseguire tale inizializzazione è nel `OnCreate` metodo del servizio. Questo frammento di codice è un esempio di un servizio che inizializza la propria `Handler` e `Messenger`:

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

Tutti i servizi associati, se vengono eseguite nel proprio processo oppure No, devono implementare il `OnBind` (metodo). Il valore restituito di questo metodo è un oggetto che il client può usare per interagire con il servizio. Esattamente qual è l'oggetto dipende se il servizio è un servizio locale o un servizio remoto. Mentre un servizio locale restituirà una classe personalizzata `IBinder` implementazione, un servizio remoto restituisce la `IBinder` che è incapsulato ma il `Messenger` che è stato creato nella sezione precedente:

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

Una volta che vengono eseguiti questi tre passaggi, il servizio remoto può essere considerato completato.

## <a name="consuming-the-service"></a>Utilizzo del servizio

Tutti i client devono implementare il codice necessario per essere in grado di eseguire l'associazione e utilizzare il servizio remoto. Concettualmente, dal punto di vista del client, esistono poche differenze tra l'associazione al servizio locale o un servizio remoto. Il client richiama il `BindService` , passando un intent esplicito per identificare il servizio e un `IServiceConnection` che consente di gestire la connessione tra il client e il servizio.

Questo frammento di codice è un esempio di come creare un **intento esplicito** per l'associazione a un servizio remoto. Lo scopo è necessario identificare il pacchetto che contiene il servizio e il nome del servizio. Un modo per impostare queste informazioni consiste nell'usare un `Android.Content.ComponentName` oggetto e per fornire tale allo scopo. Questo frammento di codice è un esempio:  

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

Ecco un esempio di una base `IServiceConnection` implementazione che illustra come un client gestirà alla connessione e disconnessione da un servizio. Si noti che il `OnServiceConnected` metodo riceve e `IBinder`, e il client crea un `Messenger` rispetto a quello `IBinder`:

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

Dopo aver creato la connessione al servizio e lo scopo, è possibile che il client potrà chiamare `BindService` e avviare il processo di associazione:

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

Dopo che il client è correttamente associato al servizio e il `Messenger` è disponibile, è possibile che il client invii `Messages` al servizio.

## <a name="sending-messages-to-the-service"></a>L'invio di messaggi al servizio

Dopo che il client è connesso e ha un `Messenger` dell'oggetto, è possibile comunicare con il servizio con l'invio `Message` oggetti tramite il `Messenger`. Questa comunicazione è unidirezionale, il client invia il messaggio ma non vengono visualizzati messaggi restituiti dal servizio al client. A questo proposito, il `Message` è un meccanismo fire-and-forget.

Il modo migliore per creare un `Message` oggetto consiste nell'usare il [ `Message.Obtain` ](https://developer.xamarin.com/api/type/Android.OS.Message/#Public%20Methods) metodo factory. Questo metodo richiamerà un `Message` oggetto da un pool globale che viene mantenuto da Android. `Message.Obtain` anche con alcuni metodi di overload che consentono il `Message` oggetto da inizializzare con i valori e i parametri richiesti dal servizio.  Una volta il `Message` viene creata un'istanza, inviato al servizio chiamando `Messenger.Send`. Questo frammento di codice è un esempio di creazione e invio di un `Message` al processo del servizio:

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

Esistono diversi tipi diversi del `Message.Obtain` (metodo). L'esempio precedente Usa la [ `Message.Obtain(Handler h, Int32 what)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/). Poiché si tratta di una richiesta asincrona a un servizio out-of-process. non sarà presente alcuna risposta dal servizio, in modo che il `Handler` è impostata su `null`. Il secondo parametro, `Int32 what`, verranno archiviati nel `.What` proprietà del `Message` oggetto. Il `.What` proprietà viene utilizzata dal codice nel processo del servizio per richiamare metodi sul servizio.

Il `Message` classe espone inoltre due proprietà aggiuntive che potrebbero essere utili per il nome: `Arg1` e `Arg2`. Queste due proprietà sono valori integer che possono avere alcune speciali concordati valori che hanno un significato tra il client e il servizio. Ad esempio, `Arg1` può contenere un ID cliente e `Arg2` può contenere un numero di ordine di acquisto per quel cliente. Il [ `Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/System.Int32/System.Int32/) può essere utilizzato per impostare le due proprietà quando il `Message` viene creato. Un altro modo per popolare questi due valori consiste nell'impostare il `.Arg` e `.Arg2` direttamente nella proprietà di `Message` dopo che è stato creato l'oggetto.

### <a name="passing-additional-values-to-the-service"></a>Passaggio di valori aggiuntivi al servizio

È possibile passare i dati più complessi al servizio usando un `Bundle`. In questo caso, i valori aggiuntivi possono essere inseriti una `Bundle` e inviati con il `Message` impostando il [ `.Data` proprietà](https://developer.xamarin.com/api/property/Android.OS.Message.Data/) proprietà prima dell'invio.

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> In generale, un `Message` non deve contenere un payload maggiore di 1 MB. Il limite delle dimensioni può variare in base alla versione di Android e in qualsiasi proprietarie il fornitore potrebbe avere apportate modifiche alla loro implementazione del Android Open Source Project (AOSP) in bundle con il dispositivo.

## <a name="returning-values-from-the-service"></a>Restituzione di valori dal servizio

L'architettura di messaggistica che è stato trattato a questo punto è unidirezionale, il client invia un messaggio al servizio. Se è necessario per il servizio di restituire un valore a un client viene invertito tutto ciò che è stata discussa in questo punto. Il servizio deve creare un `Message`incluse in un pacchetto, tutti i valori restituiti e l'invio di `Message` tramite un `Messenger` al client. Tuttavia, il servizio non crea il propria `Messenger`; in alternativa, si affidano ai client di creazione di un'istanza e pacchetto un `Messenger` come parte della richiesta iniziale. Il servizio verrà `Send` il messaggio utilizzando questo fornite dal client `Messenger`.  

La sequenza di eventi per la comunicazione bidirezionale è la seguente:

1. Il client esegue il binding al servizio. Quando il servizio e il client di connettersi, il `IServiceConnection` che viene gestito dal client avrà un riferimento a un `Messenger` oggetto utilizzato per trasmettere `Message`s per il servizio. Per evitare confusione, ciò verrà indicato a come le _servizio Messenger_.
2. Client crea un'istanza una `Handler` (detta il _gestore Client_) che verrà usata per inizializzare il proprio `Messenger` (la _Messenger Client_). Si noti che il servizio Messenger e Messenger i Client sono due oggetti diversi che gestiscono il traffico in due direzioni diverse. Il servizio Messenger gestisce i messaggi dal client al servizio, mentre il Client di Messenger gestirà i messaggi dal servizio al client.
3. Il client crea un `Message` oggetto e imposta il `ReplyTo` proprietà con il servizio Client. Il messaggio viene quindi inviato al servizio usando il servizio Messenger.
4. Il servizio riceve il messaggio dal client ed esegue le operazioni necessarie.
5. Al momento per il servizio inviare la risposta al client, userà `Message.Obtain` per creare un nuovo `Message` oggetto.
6. Per inviare questo messaggio al client, il servizio estrae il Messenger Client dal `.ReplyTo` proprietà del client del messaggio e usarlo per `.Send` il `Message` al client.
7. Quando la risposta viene ricevuta dal client, ha il proprio `Handler` che elaborerà i `Message` controllando il `.What` proprietà (e se necessario, l'estrazione di tutti i parametri contenuti il `Message`).

Questo codice di esempio viene illustrata la modalità con cui verrà creata un'istanza di client di `Message` e creare un pacchetto una `Messenger` che il servizio deve usare per la risposta:

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

Il servizio è necessario apportare alcune modifiche per la propria `Handler` per estrarre il `Messenger` e usarlo per inviare risposte al client. Questo frammento di codice è un esempio di come il servizio `Handler` creerebbe un `Message` e inviarlo al client:  

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

Si noti che negli esempi di codice precedenti, il `Messenger` istanza creata dal client viene *non* lo stesso oggetto ricevuto dal servizio. Si tratta di due diversi `Messenger` gli oggetti in esecuzione in due processi distinti che rappresentano il canale di comunicazione.

## <a name="securing-the-service-with-android-permissions"></a>Protezione del servizio con autorizzazioni di Android

Un servizio in esecuzione in un processo globale è accessibile da tutte le applicazioni in esecuzione nel dispositivo Android. In alcune situazioni questo Open Source e la disponibilità non è opportuna ed è necessario proteggere il servizio dall'accesso dal client non autorizzati. Un modo per limitare l'accesso al servizio remoto è utilizzare delle autorizzazioni di Android.

Le autorizzazioni possono essere identificate tramite il `Permission` proprietà del `ServiceAttribute` che decora il `Service` sottoclasse. Questo nome viene definito un'autorizzazione che il client deve essere concessa durante l'associazione al servizio. Se il client non dispone delle autorizzazioni appropriate, quindi Android genererà un `Java.Lang.SecurityException` quando il client prova a eseguire l'associazione al servizio.

Esistono quattro diversi livelli di autorizzazione che Android offre:

* **Normal** &ndash; si tratta del livello di autorizzazione predefinita. Viene utilizzato per identificare le autorizzazioni a basso rischio che possono essere concesse automaticamente da Android per i client che lo richiedono. L'utente non dovrà concedere esplicitamente tali autorizzazioni, ma le autorizzazioni possono essere visualizzate nelle impostazioni dell'app.
* **firma** &ndash; si tratta di una categoria speciale di autorizzazione verrà concesse automaticamente da Android per le applicazioni firmate con lo stesso certificato. Questa autorizzazione è progettata per renderla facilmente per uno sviluppatore di applicazioni di condividere dati tra le proprie App senza richiedere l'intervento dell'utente per le approvazioni costante o i componenti.
* **signatureOrSystem** &ndash; ciò è molto simile al **firma** autorizzazioni descritte in precedenza. Oltre a essere automaticamente concesso alle App che sono firmate dallo stesso certificato, questa autorizzazione verrà concessa anche a App firmate lo stesso certificato usato per firmare le app installate con l'immagine del sistema Android. Questa autorizzazione in genere viene usata solo dagli sviluppatori ROM Android per consentire alle applicazioni lavorare con le app di terze parti. Non è comunemente utilizzato per le app che hanno lo scopo di distribuzione generale per il grande pubblico.
* **pericoloso** &ndash; autorizzazioni pericolose sono quelle che potrebbero causare problemi per l'utente. Per questo motivo **pericoloso** autorizzazioni devono essere approvate in modo esplicito dall'utente.

In quanto `signature` e `normal` le autorizzazioni vengono concesse automaticamente in fase di installazione per Android, è fondamentale che i file APK che ospita il servizio sia installato **prima** il file APK che include il client. Se il client viene installato prima di tutto, Android non concederà autorizzazioni. In questo caso, sarà necessario disinstallare il client di APK, installare il servizio file APK e quindi installare nuovamente il file APK di client.

Esistono due modi comuni per proteggere un servizio con autorizzazioni di Android:

1.  **Implementare la sicurezza a livello di firma** &ndash; sicurezza a livello di firma significa che l'autorizzazione viene concessa automaticamente alle applicazioni che sono firmate con la stessa chiave usata per firmare l'APK che contiene il servizio. Questo è un modo semplice per gli sviluppatori per proteggere i servizi ma mantenerli accessibili dalle rispettive applicazioni. Autorizzazioni a livello di firma vengono dichiarate impostando il `Permission` proprietà del `ServiceAttribute` a `signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2.  **Creare un'autorizzazione personalizzata** &ndash; è possibile che lo sviluppatore del servizio creare un'autorizzazione personalizzata per il servizio. Ciò è ideale per quando uno sviluppatore desidera condividere i servizi con le applicazioni da altri sviluppatori. Un'autorizzazione personalizzata richiede un po' più complessa da implementare, verrà illustrato di seguito.

Un esempio semplificato di creazione di un oggetto personalizzato `normal` autorizzazione verrà descritti nella sezione successiva. Per altre informazioni sulle autorizzazioni di Android, consultare la documentazione di Google per [procedure consigliate e sicurezza](https://developer.android.com/training/articles/security-tips.html). Per altre informazioni sulle autorizzazioni di Android, vedere la [sezione autorizzazioni](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) della documentazione di Android per il manifesto dell'applicazione per altre informazioni sulle autorizzazioni di Android.

> [!NOTE]
> In genere [Google, scoraggia l'impiego di autorizzazioni personalizzate](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) come che possono risultare poco chiaro per gli utenti.

### <a name="creating-a-custom-permission"></a>Creazione di un'autorizzazione personalizzata

Per usare un'autorizzazione personalizzata, che viene dichiarata dal servizio mentre il client richiede in modo esplicito l'autorizzazione.

Per creare un'autorizzazione nel servizio file APK, un `permission` elemento viene aggiunto al `manifest` nell'elemento **androidmanifest. XML**. Questa autorizzazione deve avere il `name`, `protectionLevel`, e `label` set di attributi. Il `name` attributo deve essere impostato su una stringa che identifica in modo univoco l'autorizzazione. Il nome verrà visualizzato nei **Info sull'App** visualizzare del **impostazioni Android** (come illustrato nella sezione successiva).

Il `protectionLevel` attributo deve essere impostato su uno dei valori di quattro stringa che sono stati descritti sopra.  Il `label` e `description` deve fare riferimento a risorse di tipo stringa e vengono usate per fornire un nome descrittivo e una descrizione per l'utente.

Questo frammento di codice è riportato un esempio di dichiarazione di una classe personalizzata `permission` dell'attributo **androidmanifest. XML** dell'APK che contiene il servizio:

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

Successivamente, il **androidmanifest. XML** del client APK deve richiedere in modo esplicito questa nuova autorizzazione. Questa operazione viene eseguita aggiungendo il `users-permission` dell'attributo per il **androidmanifest. XML**:

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

Per visualizzare le autorizzazioni concesse a un'applicazione, aprire l'app impostazioni per Android e selezionare **app**. Trovare e selezionare l'applicazione nell'elenco. Dal **informazioni sull'App** toccare **autorizzazioni** che verrà visualizzata una visualizzazione che mostra tutte le autorizzazioni concesse all'app:

[![Schermate da un dispositivo Android che illustra come trovare le autorizzazioni concesse a un'applicazione](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>Riepilogo

Questa guida è stata una discussione avanzata su come eseguire un servizio di Android in un processo remoto. Le differenze tra una variabile locale e un servizio remoto è stato illustrato, con alcuni dei motivi per cui un servizio remoto può essere utile per la stabilità e prestazioni di un'app per Android. Dopo la spiegazione su come implementare un servizio remoto e come un client può comunicare con il servizio, la Guida è verificato un errore fornire un modo per limitare l'accesso al servizio da solo i client autorizzati.


## <a name="related-links"></a>Collegamenti correlati

- [Gestore](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Messaggio](https://developer.xamarin.com/api/type/Android.OS.Message/)
- [Messenger](https://developer.xamarin.com/api/type/Android.OS.Messenger/)
- [ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)
- [L'attributo esportato](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [I servizi con processi isolati e classe di applicazione personalizzata non venga risolto correttamente overload](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [Processi e thread](https://developer.android.com/guide/components/processes-and-threads.html)
- [Manifesto Android - le autorizzazioni](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [Suggerimenti per la sicurezza](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/MessengerServiceDemo/)
