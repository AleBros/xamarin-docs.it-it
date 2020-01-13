---
title: Servizi associati in Xamarin.Android
description: I servizi associati sono servizi Android che forniscono un'interfaccia client-server che può interagire con un client, ad esempio un'attività Android. Questa guida illustra i componenti principali necessari per la creazione di un servizio associato e come usarlo in un'applicazione Xamarin.Android.
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/04/2018
ms.openlocfilehash: a3b0e8499d208f209de481163a236e5241c83ee6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024985"
---
# <a name="bound-services-in-xamarinandroid"></a>Servizi associati in Xamarin.Android

_I servizi associati sono servizi Android che forniscono un'interfaccia client-server che può interagire con un client, ad esempio un'attività Android. Questa guida illustra i componenti principali necessari per la creazione di un servizio associato e come usarlo in un'applicazione Xamarin.Android._

## <a name="bound-services-overview"></a>Panoramica dei servizi associati

I servizi che forniscono un'interfaccia client-server che consente ai client di interagire direttamente con il servizio sono detti _servizi associati_.  È possibile che più client siano connessi a una singola istanza di un servizio nello stesso momento. Il servizio associato e il client sono isolati l'uno dall'altro. Android fornisce invece una serie di oggetti intermedi che gestiscono lo stato della connessione tra i due. Questo stato viene gestito da un oggetto che implementa l'interfaccia [`Android.Content.IServiceConnection`](xref:Android.Content.IServiceConnection) .  Questo oggetto viene creato dal client e passato come parametro al metodo [`BindService`](xref:Android.Content.Context.BindService*) . Il `BindService` è disponibile per qualsiasi oggetto [`Android.Content.Context`](xref:Android.Content.Context) , ad esempio un'attività. Si tratta di una richiesta al sistema operativo Android per avviare il servizio e associarvi un client. È possibile eseguire l'associazione di un client a un servizio in tre modi utilizzando il metodo `BindService`:

- **Uno** strumento di associazione di servizi &ndash; uno strumento di associazione di servizi è una classe che implementa l'interfaccia [`Android.OS.IBinder`](xref:Android.OS.IBinder) . La maggior parte delle applicazioni non implementerà direttamente questa interfaccia, bensì estenderà la classe [`Android.OS.Binder`](xref:Android.OS.Binder) . Si tratta dell'approccio più comune ed è adatto quando il servizio e il client esistono nello stesso processo.
- L' **uso di un Messenger** &ndash; questa tecnica è adatto quando il servizio può esistere in un processo separato. Al contrario, le richieste di servizio vengono sottoposte a marshalling tra il client e il servizio tramite un [`Android.OS.Messenger`](xref:Android.OS.Messenger). Nel servizio viene creata un' [`Android.OS.Handler`](xref:Android.OS.Handler) che gestirà le richieste di `Messenger`. Questo verrà trattato in un'altra guida.
- **Uso di Android Interface Definition Language (AIDL)** &ndash; [AIDL](https://developer.android.com/guide/components/aidl) è una tecnica avanzata che non verrà descritta in questa guida.

Una volta che un client è stato associato a un servizio, la comunicazione tra le due si verifica tramite `Android.OS.IBinder` oggetto.  Questo oggetto è responsabile dell'interfaccia che consentirà al client di interagire con il servizio. Non è necessario che ogni applicazione Xamarin.Android implementi questa interfaccia da zero, il Android SDK fornisce la classe [`Android.OS.Binder`](xref:Android.OS.Binder) che occupa la maggior parte del codice necessario per il marshalling dell'oggetto tra il client e il servizio.

Quando un client viene eseguito con il servizio, è necessario annullare l'associazione chiamando il metodo `UnbindService`. Quando l'ultimo client non è associato a un servizio, Android arresterà ed eliminerà il servizio associato.

Questo diagramma illustra il modo in cui l'attività, la connessione del servizio, il Binder e il servizio sono correlati tra loro:

![Diagramma che mostra la relazione tra i componenti del servizio](bound-services-images/bound-services-02.png "Diagramma che mostra la relazione tra i componenti del servizio.")

In questa guida viene illustrato come estendere la classe `Service` per implementare un servizio associato. Verrà inoltre trattata l'implementazione di `IServiceConnection` ed estensione `Binder` per consentire a un client di comunicare con il servizio. Un'app di esempio accompagna questa guida, che contiene una soluzione con un singolo progetto Xamarin.Android denominato **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** . Si tratta di un'applicazione molto semplice che illustra come implementare un servizio e come associarvi un'attività. Il servizio associato ha un'API molto semplice con un solo metodo, `GetFormattedTimestamp`, che restituisce una stringa che indica all'utente quando il servizio è stato avviato e il tempo di esecuzione. L'app consente inoltre all'utente di annullare manualmente il binding e il binding al servizio.

[![screenshot dell'applicazione in esecuzione su un telefono Android](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>Implementazione e utilizzo di un servizio associato

Per consentire a un'applicazione Android di utilizzare un servizio associato, è necessario implementare tre componenti:

1. **Estendere la classe `Service` e implementare i metodi di callback del ciclo** di vita &ndash; questa classe conterrà il codice che eseguirà il lavoro che verrà richiesto dal servizio. Questa operazione verrà illustrata in dettaglio di seguito.
2. **Creare una classe che implementi `IServiceConnection`** &ndash; questa interfaccia fornisce metodi di callback richiamati da Android per notificare al client quando la connessione al servizio è stata modificata, ovvero se il client si è connesso o disconnesso al servizio. La connessione al servizio fornirà inoltre un riferimento a un oggetto che il client può utilizzare per interagire direttamente con il servizio. Questo riferimento è noto come _Binder_.
3. **Creare una classe che implementi `IBinder`** &ndash; un'implementazione del _Binder_ fornisce l'API utilizzata da un client per comunicare con il servizio. Il Binder può fornire un riferimento al servizio associato, consentendo la chiamata diretta dei metodi o il Binder può fornire un'API client che incapsula e nasconde il servizio associato dall'applicazione. Un `IBinder` deve fornire il codice necessario per le chiamate di procedure remote. Non è necessario (o consigliato) implementare direttamente l'interfaccia `IBinder`. Le applicazioni devono invece estendere il tipo di `Binder` che fornisce la maggior parte delle funzionalità di base richieste da un `IBinder`.
4. L' **avvio e il binding a un servizio** &ndash; una volta creati la connessione al servizio, il Binder e il servizio, l'applicazione Android è responsabile dell'avvio del servizio e dell'associazione al servizio.

Ognuno di questi passaggi verrà descritto più dettagliatamente nelle sezioni seguenti.

### <a name="extend-the-service-class"></a>Estendere la classe `Service`

Per creare un servizio con Xamarin.Android, è necessario sottoclassare `Service` e decorare la classe con l' [`ServiceAttribute`](xref:Android.App.ServiceAttribute). L'attributo viene usato dagli strumenti di compilazione Xamarin.Android per registrare correttamente il servizio nel file **file AndroidManifest. XML** dell'app, in modo analogo a un'attività, un servizio associato ha il ciclo di vita e i metodi di callback associati agli eventi significativi in il ciclo di vita. L'elenco seguente è un esempio di alcuni dei metodi di callback più comuni che verrà implementato da un servizio:

- `OnCreate` &ndash; questo metodo viene richiamato da Android poiché crea un'istanza del servizio. Viene usato per inizializzare eventuali variabili o oggetti richiesti dal servizio durante la relativa durata. È facoltativo.
- `OnBind` &ndash; questo metodo deve essere implementato da tutti i servizi associati. Viene richiamato quando il primo client tenta di connettersi al servizio. Verrà restituita un'istanza di `IBinder` in modo che il client possa interagire con il servizio. Fino a quando il servizio è in esecuzione, l'oggetto `IBinder` verrà usato per soddisfare le richieste client future da associare al servizio.
- `OnUnbind` &ndash; questo metodo viene chiamato quando tutti i client associati hanno un binding non associato. Restituendo `true` da questo metodo, il servizio chiamerà successivamente `OnRebind` con lo scopo passato a `OnUnbind` quando vengono associati nuovi client. Questa operazione viene eseguita quando un servizio continua l'esecuzione dopo che è stato annullato. Questo problema si verifica se il servizio di recente non associato era anche un servizio avviato e `StopService` o `StopSelf` non venivano chiamati. In uno scenario di questo tipo, `OnRebind` consente il recupero dello scopo. Il valore predefinito restituisce `false`, che non esegue alcuna operazione. Parametro facoltativo.
- `OnDestroy` &ndash; questo metodo viene chiamato quando Android sta eliminando il servizio. Tutte le operazioni di pulizia necessarie, ad esempio il rilascio di risorse, devono essere eseguite in questo metodo. Parametro facoltativo.

In questo diagramma vengono illustrati gli eventi del ciclo di vita delle chiavi di un servizio associato:

![Diagramma che mostra l'ordine in cui vengono chiamati i metodi del ciclo di vita](bound-services-images/bound-services-01.png "Diagramma che mostra l'ordine in cui vengono chiamati i metodi del ciclo di vita.")

Il frammento di codice seguente, dall'applicazione complementare che accompagna questa guida, Mostra come implementare un servizio associato in Xamarin.Android:

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

Nell'esempio, il metodo `OnCreate` Inizializza un oggetto che include la logica per il recupero e la formattazione di un timestamp che verrebbe richiesto da un client. Quando il primo client tenta di eseguire l'associazione al servizio, Android richiamerà il metodo `OnBind`. Questo servizio creerà un'istanza di un oggetto `TimestampBinder` che consentirà ai client di accedere a questa istanza del servizio in esecuzione. La classe `TimestampBinder` viene illustrata nella sezione successiva.

### <a name="implementing-ibinder"></a>Implementazione di IBinder

Come indicato, un oggetto `IBinder` fornisce il canale di comunicazione tra un client e un servizio. Le applicazioni Android non devono implementare l'interfaccia `IBinder`, il [`Android.OS.Binder`](xref:Android.OS.Binder) deve essere esteso. La classe `Binder` fornisce gran parte dell'infrastruttura necessaria che è necessario effettuare il marshalling dell'oggetto Binder dal servizio, che può essere eseguito in un processo separato, al client. Nella maggior parte dei casi, la sottoclasse `Binder` è solo poche righe di codice ed esegue il wrapping di un riferimento al servizio. In questo esempio `TimestampBinder` dispone di una proprietà che espone l'`TimestampService` al client:

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

Questa `Binder` rende possibile richiamare i metodi pubblici nel servizio; Per esempio:

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

Una volta che `Binder` è stato esteso, è necessario implementare `IServiceConnection` per connettere tutti gli elementi.

### <a name="creating-the-service-connection"></a>Creazione della connessione del servizio

Il `IServiceConnection` presenterà | introduce | Expose | connette l'oggetto `Binder` al client. Oltre all'implementazione dell'interfaccia `IServiceConnection`, è necessario che la classe estenda `Java.Lang.Object`. La connessione al servizio deve inoltre fornire un modo per consentire al client di accedere al `Binder` (e quindi comunicare con il servizio associato).

Questo codice del progetto di esempio associato è un modo possibile per implementare `IServiceConnection`:

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

Come parte del processo di associazione, Android richiama il metodo `OnServiceConnected`, fornendo la `name` del servizio associato e il `binder` che include un riferimento al servizio stesso. In questo esempio, la connessione al servizio dispone di due proprietà, una che contiene un riferimento al gestore di associazione e un flag booleano per se il client è connesso al servizio o no.

Il metodo `OnServiceDisconnected` viene richiamato solo quando la connessione tra un client e un servizio viene persa in modo imprevisto o viene interrotta. Questo metodo consente al client di rispondere all'interruzione del servizio.  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>Avvio e associazione a un servizio con finalità esplicite

Per usare un servizio associato, un client, ad esempio un'attività, deve creare un'istanza di un oggetto che implementa `Android.Content.IServiceConnection` e richiamare il metodo `BindService`. `BindService` restituirà `true` se il servizio è associato a, `false` in caso contrario. Il metodo `BindService` accetta tre parametri:

- **Un `Intent`** &ndash; lo scopo deve identificare in modo esplicito il servizio a cui connettersi.
- **Un oggetto `IServiceConnection`** &ndash; questo oggetto è un intermediario che fornisce metodi di callback per notificare al client quando il servizio associato viene avviato e arrestato.
- **[`Android.Content.Bind`](xref:Android.Content.Bind) enum** &ndash; questo parametro è un set di flag utilizzati dal sistema per l'associazione dell'oggetto. Il valore usato più di frequente è [`Bind.AutoCreate`](xref:Android.Content.Bind.AutoCreate), che avvierà automaticamente il servizio se non è già in esecuzione.

Il frammento di codice seguente è un esempio di come avviare un servizio associato in un'attività usando un preventivo esplicito:

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
> A partire da Android 5,0 (API level 21) è possibile eseguire l'associazione a un servizio solo con finalità esplicite.

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>Note sull'architettura relative alla connessione al servizio e al Binder.

Alcuni puristi di OOP possono disapprovare l'implementazione precedente della classe `TimestampBinder` perché si tratta di una violazione della [legge di Demetra](https://en.wikipedia.org/wiki/Law_of_Demeter) , che negli Stati di forma più semplici "non parla con estranei; parla solo con gli amici. Questa implementazione specifica espone la classe `TimestampService` concreta a tutti i client.

In modo rigoroso, non è necessario che il client riconosca il `TimestampService` e l'esposizione di tale classe concreta ai client possa rendere un'applicazione più fragile e più difficile da gestire per tutta la sua durata. Un approccio alternativo consiste nell'usare un'interfaccia che espone il metodo `GetFormattedTimestamp()` e le chiamate proxy al servizio tramite l'`Binder` (o possibile la classe di connessione del servizio):  

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

Questo particolare esempio consente a un'attività di richiamare metodi sul servizio stesso:

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```

## <a name="related-links"></a>Collegamenti correlati

- [Android. app. Service](xref:Android.App.Service)
- [Android. Content. Bind](xref:Android.Content.Bind)
- [Android. Content. Context](xref:Android.Content.Context)
- [Android. Content. IServiceConnection](xref:Android.Content.IServiceConnection)
- [Android. OS. Binder](xref:Android.OS.Binder)
- [Android. OS. IBinder](xref:Android.OS.IBinder)
- [BoundServiceDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-boundservicedemo)
