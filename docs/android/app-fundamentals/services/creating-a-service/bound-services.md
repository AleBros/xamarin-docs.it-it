---
title: Associare i servizi in xamarin
description: Servizi associati sono servizi Android che forniscono un'interfaccia client-server in grado di interagire con un client (ad esempio un'attività Android). Questa Guida verrà illustrati i componenti fondamentali coinvolti nella creazione di un servizio associato e come utilizzarla in un'applicazione di xamarin.
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 1cb151cc5c741a020fcbb398441ed4958ec5980b
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2018
---
# <a name="bound-services-in-xamarinandroid"></a>Associare i servizi in xamarin

_Servizi associati sono servizi Android che forniscono un'interfaccia client-server in grado di interagire con un client (ad esempio un'attività Android). Questa Guida verrà illustrati i componenti fondamentali coinvolti nella creazione di un servizio associato e come utilizzarla in un'applicazione di xamarin._

## <a name="bound-services-overview"></a>Cenni preliminari sui servizi associati

Servizi che forniscono un'interfaccia client-server per i client interagire direttamente con il servizio sono detti _associato servizi_.  Possono essere presenti più client connessi a una singola istanza di un servizio nello stesso momento. Il servizio associato e il client sono isolate una da altra. Android, invece, fornisce una serie di oggetti intermedi che gestiscono lo stato della connessione tra i due. Questo stato viene mantenuto da un oggetto che implementa il [ `Android.Content.IServiceConnection` ](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/) interfaccia.  Questo oggetto viene creato dal client e passato come parametro per il [ `BindService` ](https://developer.xamarin.com/api/member/Android.Content.Context.BindService/) metodo. Il `BindService` è disponibile in qualsiasi [ `Android.Content.Context` ](https://developer.xamarin.com/api/type/Android.Content.Context) oggetto (ad esempio un'attività). È una richiesta per il sistema operativo Android per avviare il servizio e associarvi un client. Esistono tre modi per un client possono eseguire il binding a un servizio utilizzando il `BindService` metodo:

* **Uno strumento di associazione del servizio** &ndash; uno strumento di associazione del servizio è una classe che implementa il [ `Android.OS.IBinder` ](https://developer.xamarin.com/api/type/Android.OS.IBinder) interfaccia. La maggior parte delle applicazioni non implementa questa interfaccia direttamente, invece verrà esteso il [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) classe. Questo è l'approccio più comune ed è adatto per quando il servizio e il client esistono nello stesso processo.
* **Utilizzo di un Messenger** &ndash; questa tecnica è adatta per quando il servizio esista in un processo separato. Al contrario, le richieste di servizio sono sottoposto a marshalling tra il client e il servizio tramite un [ `Android.OS.Messenger` ](https://developer.xamarin.com/api/type/Android.OS.Messenger). Un [ `Android.OS.Handler` ](https://developer.xamarin.com/api/type/Android.OS.Handler) viene creata nel servizio che consente di gestire il `Messenger` richieste. Questo argomento verrà descritto nella Guida di un altro.
* **Utilizzando AIDL** &ndash; questo colpisce terrore nel cuore della maggior parte degli sviluppatori di Android e non trattato in questa Guida.

Dopo l'associazione di un client a un servizio, la comunicazione tra i due è avviene tramite `Android.OS.IBinder` oggetto.  Questo oggetto è responsabile per l'interfaccia che consente al client di interagire con il servizio. Non è necessario per ogni applicazione di xamarin implementare questa interfaccia da zero, Android SDK fornisce il [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) classe che si occupa della maggior parte del codice necessario con l'oggetto tra marshalling il client e il servizio.

Quando un client viene eseguito con il servizio, è necessario disassociare da esso chiamando il `UnbindService` metodo. Dopo l'ultimo client non associati da un servizio, Android verrà arrestare e rimuovere il servizio associato.

Questo diagramma viene illustrato come l'attività, connessione del servizio, strumento di associazione e servizio correlati tra loro:

![Un diagramma che illustra come i componenti del servizio interagiscono tra di loro](bound-services-images/bound-services-02.png "un diagramma che illustra come i componenti del servizio collegano tra loro.")

Questa Guida verrà illustrate le procedure estendere la `Service` classe per implementare un servizio associato. Che verrà applicata anche a implementazione `IServiceConnection` ed estensione `Binder` per consentire a un client comunicare con il servizio. Un'app di esempio fornito con questa Guida, che contiene una soluzione con un singolo progetto xamarin chiamato **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** . Si tratta di un'applicazione molto semplice che illustra come implementare un servizio e come associare un'attività. Il servizio associato è un'API molto semplice con un solo metodo, `GetFormattedTimestamp`, che restituisce una stringa che indica all'utente quando il servizio sia avviato e il tempo è in esecuzione. L'app consente anche all'utente di separare manualmente e associazione al servizio.

[![Schermata dell'applicazione in esecuzione su un telefono Android](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>Implementazione e utilizzo di un servizio associato

Sono presenti tre componenti che devono essere implementati in modo che un'applicazione Android di usare un servizio associato:

1. **Estendere la `Service` classe e implementare i metodi di Callback del ciclo di vita** &ndash; questa classe conterrà il codice che eseguirà il lavoro che verrà richieste del servizio. Questo argomento verrà descritto in dettaglio più avanti.
2. **Creare una classe che implementa `IServiceConnection`**  &ndash; questo oggetto contiene i metodi di callback da notificano al client quando è connesso a (o la connessione è stata interrotta) dal servizio. La connessione al servizio fornirà anche un riferimento a un oggetto che il client può utilizzare per interagire direttamente con il servizio. Questo riferimento è noto come il _binder_.
3. **Creare una classe che implementa `IBinder`**  &ndash; A _Binder_ implementazione fornisce API che un client utilizza per comunicare con il servizio. Lo strumento di associazione può fornire un riferimento al servizio associato, consentendo di metodi da richiamare direttamente oppure il gestore di associazione può fornire un'API che incapsula e nascondere il servizio associato dall'applicazione client. Un `IBinder` deve fornire il codice necessario per le chiamate di procedura remota. Non è necessario (né consigliato) per implementare il `IBinder` interfaccia direttamente. Un `IBinder` devono estendere applicazioni Instead il `Binder` che fornisce la maggior parte della funzionalità di base necessaria per un `IBinder`.
4. **Avvio e l'associazione a un servizio** &ndash; dopo avere creati la connessione del servizio, strumento di associazione e servizio è responsabile dell'avvio del servizio e l'associazione a tale applicazione Android.

Ognuno di questi passaggi verrà descritte nelle sezioni seguenti in modo più dettagliato.

### <a name="extend-the-service-class"></a>Estendere la `Service` classe

Per creare un servizio con xamarin, è necessario per creare una sottoclasse `Service` e per applicare la classe con il [ `ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute). L'attributo viene utilizzato dagli strumenti di compilazione xamarin registrare correttamente il servizio nell'app **AndroidManifest.xml** file simile a un'attività, un servizio associato dispone del proprio ciclo di vita e il callback metodi associati il eventi significativi nel relativo ciclo di vita. Nell'elenco seguente è riportato un esempio di alcuni dei metodi di callback più comuni che consente di implementare un servizio:

* `OnCreate` &ndash; Questo metodo viene richiamato da Android come che viene creata l'istanza del servizio. E viene utilizzato per inizializzare le variabili o gli oggetti che sono richiesti dal servizio durante il ciclo di vita. È facoltativo.
* `OnBind` &ndash; Questo metodo deve essere implementato da tutti i servizi associati. Viene richiamato quando il primo client tenta di connettersi al servizio. Restituisce un'istanza di `IBinder` in modo che il client può interagire con il servizio. Fino a quando il servizio è in esecuzione, il `IBinder` verrà utilizzato l'oggetto per soddisfare le richieste client future per associazione al servizio.
* `OnUnbind` &ndash; Questo metodo viene chiamato quando tutti i client associati sono associati. Restituendo `true` da questo metodo in un secondo momento chiama il servizio `OnRebind` con l'intenzione passato a `OnUnbind` quando i nuovi client associare ad esso. A questo scopo quando un servizio continua l'esecuzione dopo che è stato associato. Questa situazione si verifica se il servizio di recente non associato sono stati anche un servizio avviato, e `StopService` o `StopSelf` non era stato chiamato. In tale scenario, `OnRebind` consente lo scopo da recuperare. Restituisce il valore predefinito `false` , che non esegue alcuna operazione. Facoltativo.
* `OnDestroy` &ndash; Questo metodo viene chiamato quando il servizio è eliminazione definitiva di Android. Le operazioni di pulitura necessarie, ad esempio il rilascio delle risorse, deve essere eseguita in questo metodo. Facoltativo.

In questo diagramma sono riportati gli eventi del ciclo di vita di chiavi di un servizio associato

![Un diagramma che illustra l'ordine in cui vengono chiamati i metodi del ciclo di vita](bound-services-images/bound-services-01.png "un diagramma che illustra l'ordine in cui vengono chiamati i metodi del ciclo di vita.")

Il seguente frammento di codice dall'applicazione complementare in questa Guida, viene illustrato come implementare un servizio associato in xamarin:

```csharp
using Android.App;
using Android.Util;
using Android.Content;
using Android.OS;

namespace BoundServiceDemo
{
    [Service(Name="com.xamarin.ServicesDemo1")]
    public class TimestampService : Service, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampService).FullName;
        IGetTimestamp timestamper;

        public IBinder Binder { get; private set; }

        public override void OnCreate()
        {
            // This method is optional to implement
            base.OnCreate();
            Log.Debug(TAG, "OnCreate");
            timestamper = new UtcTimestamper();
        }

        public override IBinder OnBind(Intent intent)
        {
            // This method must always be implemented
            Log.Debug(TAG, "OnBind");
            this.Binder = new TimestampBinder(this);
            return this.Binder;
        }

        public override bool OnUnbind(Intent intent)
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnUnbind");
            return base.OnUnbind(intent);
        }

        public override void OnDestroy()
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnDestroy");
            Binder = null;
            timestamper = null;
            base.OnDestroy();
        }

        /// <summary>
        /// This method will return a formatted timestamp to the client.
        /// </summary>
        /// <returns>A string that details what time the service started and how long it has been running.</returns>
        public string GetFormattedTimestamp()
        {
            return timestamper?.GetFormattedTimestamp();
        }
    }
}
```

Nell'esempio di `OnCreate` metodo inizializza un oggetto che contiene la logica per il recupero e la formattazione di un timestamp richiesto da un client. Quando il primo client tenta di eseguire l'associazione al servizio, Android richiamerà il `OnBind` metodo. Crea un'istanza di questo servizio un `TimestampServiceBinder` oggetto che consente ai client di accedere a questa istanza di servizio in esecuzione. La `TimestampServiceBinder` classe è descritto nella sezione successiva.

### <a name="implementing-ibinder"></a>Implementazione IBinder

Come accennato, un `IBinder` oggetto fornisce il canale di comunicazione tra un client e un servizio. Le applicazioni Android non devono implementare il `IBinder` interfaccia, il [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder/) dovrebbe essere estesa. La `Binder` classe fornisce gran parte dell'infrastruttura necessaria che è necessario effettuare il marshalling di oggetto gestore di associazione del servizio (che potrebbero essere in esecuzione in un processo separato) al client. Nella maggior parte dei casi, il `Binder` sottoclasse solo alcune righe di codice ed esegue il wrapping di un riferimento al servizio. In questo esempio, `TimestampBinder` dispone di una proprietà che espone il `TimestampService` al client:

```csharp
public class TimestampBinder : Binder
{
    public TimestampBinder(TimestampService service)
    {
        this.Service = service;
    }

    public TimestampService Service { get; private set; }
}
```

Questo `Binder` rende possibile richiamare i metodi pubblici nel servizio; ad esempio:

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

Una volta `Binder` è stato esteso, è necessario implementare `IServiceConnection` per connettere tutti gli elementi.

### <a name="creating-the-service-connection"></a>La creazione della connessione del servizio

Il `IServiceConnection` presenterà | introdurre | esporre | connettersi il `Binder` oggetto al client. Oltre all'implementazione di `IServiceConnection` interfaccia, è necessario estendere la classe `Java.Lang.Object`. Connessione del servizio deve fornire anche un modo che il client può accedere il `Binder` (e pertanto comunicare con il servizio associato).

Questo codice è tratto incluso con il progetto di esempio è uno dei modi per implementare `IServiceConnection`:

```csharp
using Android.Util;
using Android.OS;
using Android.Content;

namespace BoundServiceDemo
{
    public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampServiceConnection).FullName;

        MainActivity mainActivity;
        public TimestampServiceConnection(MainActivity activity)
        {
            IsConnected = false;
            Binder = null;
            mainActivity = activity;
        }

        public bool IsConnected { get; private set; }
        public TimestampBinder Binder { get; private set; }

        public void OnServiceConnected(ComponentName name, IBinder service)
        {
            Binder = service as TimestampBinder;
            IsConnected = this.Binder != null;

            string message = "onServiceConnected - ";
            Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

            if (IsConnected)
            {
                message = message + " bound to service " + name.ClassName;
                mainActivity.UpdateUiForBoundService();
            }
            else
            {
                message = message + " not bound to service " + name.ClassName;
                mainActivity.UpdateUiForUnboundService();
            }

            Log.Info(TAG, message);
            mainActivity.timestampMessageTextView.Text = message;

        }

        public void OnServiceDisconnected(ComponentName name)
        {
            Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
            IsConnected = false;
            Binder = null;
            mainActivity.UpdateUiForUnboundService();
        }

        public string GetFormattedTimestamp()
        {
            if (!IsConnected)
            {
                return null;
            }

            return Binder?.GetFormattedTimestamp();
        }
    }
}

```

Come parte del processo di associazione, Android richiamerà il `OnServiceConnected` (metodo), fornendo il `name` del servizio che viene associato e `binder` che contiene un riferimento al servizio stesso. In questo esempio, la connessione al servizio ha due proprietà, uno che contiene un riferimento per lo strumento di associazione e un flag booleano per se il client è connesso al servizio o meno.

Il `OnServiceDisconnected` metodo viene richiamato solo quando la connessione tra un client e un servizio viene smarrita o interrotta in modo imprevisto. Questo metodo consente al client la possibilità di rispondere per l'interruzione del servizio.  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>Avvio e l'associazione a un servizio con stato intento esplicito

Per utilizzare un servizio associato, un client (ad esempio un'attività) deve creare un'istanza di un oggetto che implementa `Android.Content.IServiceConnection` e richiamare il `BindService` metodo.` BindService` restituirà `true` se il servizio è associato a, `false` in caso contrario. Il metodo `BindService` accetta tre parametri:

* **Un `Intent`**  &ndash; l'intento deve identificare in modo esplicito il servizio a cui connettersi.
* **Un `IServiceConnection` oggetto** &ndash; questo oggetto è un intermediario che fornisce i metodi di callback per notificare al client quando l'avvio e arresto di servizio associato.
* **[`Android.Content.Bind`](https://developer.xamarin.com/api/type/Android.Content.Bind/) enumerazione** &ndash; questo parametro è un set di flag vengono utilizzate dal sistema per l'associazione quando l'oggetto. Il valore di uso più comune è [ `Bind.AutoCreate` ](https://developer.xamarin.com/api/field/Android.Content.Bind.AutoCreate/), che verrà automaticamente avviato il servizio se non è già in esecuzione.

Frammento di codice seguente è riportato un esempio di come avviare un servizio associato in un'attività mediante una finalità esplicita:

```csharp
protected override void OnStart ()
{
    base.OnStart ();

    if (serviceConnection == null)
    {
        this.serviceConnection = new TimestampServiceConnection(this);
    }

    Intent serviceToStart = new Intent(this, typeof(TimestampService));
    BindService(serviceToStart, this.serviceConnection, Bind.AutoCreate);

}
```

> [!IMPORTANT]
> Solo a partire da Android 5.0 (livello API 21), è possibile associare a un servizio con stato intento esplicito.

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>Architettura note sulla connessione del servizio e il gestore di associazione.

Alcuni puristi OOP potrebbero disapprovare dell'implementazione precedente del `TimestampBinder` classe poiché è una violazione del [diritto di Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter) , nella forma più semplice di stati cui "non può comunicare con sconosciuti; comunicare solo con i tuoi amici". In questa particolare implementazione espone cemento `TimestampService` classe a tutti i client.

In teoria, non è necessario per il client di conoscere il `TimestampService` e l'esposizione di tale classe concreta per i client possono rendere un'applicazione più fragile e difficile da gestire la durata. Un approccio alternativo consiste nell'utilizzare un'interfaccia che espone il `GetFormattedTimestamp()` (metodo) e proxy chiamate al servizio tramite il `Binder` (o possibile la classe di connessione del servizio):  

```csharp
public class TimestampBinder : Binder, IGetTimestamp
{
    TimestampService service;
    public TimestampBinder(TimestampService service)
    {
        this.service = service;
    }

    public string GetFormattedTimestamp()
    {
        return service?.GetFormattedTimestamp();
    }
}
```

Questo particolare esempio permettono un'attività richiamare i metodi del servizio stesso:

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```


## <a name="related-links"></a>Collegamenti correlati

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.Content.Bind](https://developer.xamarin.com/api/type/Android.Content.Bind/)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [Android.Content.IServiceConnection](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)
- [Android.OS.Binder](https://developer.xamarin.com/api/type/Android.OS.Binder/)
- [Android.OS.IBinder](https://developer.xamarin.com/api/type/Android.OS.IBinder)
- [BoundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/BoundServiceDemo/)
