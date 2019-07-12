---
title: Bound Services in Xamarin.Android
description: Servizi associati sono servizi Android che forniscono un'interfaccia client-server in grado di interagire con un client (ad esempio, un'attività Android). Questa guida illustra i componenti fondamentali coinvolti nella creazione di un servizio associato e come usarlo in un'applicazione xamarin. Android.
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/04/2018
ms.openlocfilehash: 490331663d94a1e3130fc794a11a52acdacca014
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829750"
---
# <a name="bound-services-in-xamarinandroid"></a>Bound Services in Xamarin.Android

_Servizi associati sono servizi Android che forniscono un'interfaccia client-server in grado di interagire con un client (ad esempio, un'attività Android). Questa guida illustra i componenti fondamentali coinvolti nella creazione di un servizio associato e come usarlo in un'applicazione xamarin. Android._

## <a name="bound-services-overview"></a>Panoramica di servizi associati

Servizi che forniscono un'interfaccia client-server per i client interagire direttamente con il servizio sono dette _servizi associati_.  Possono essere presenti più client connessi a una singola istanza di un servizio nello stesso momento. Il servizio associato e i client sono isolate tra loro. Al contrario, Android offre una serie di oggetti intermedi che gestiscono lo stato della connessione tra i due. Questo stato viene mantenuto da un oggetto che implementa il [ `Android.Content.IServiceConnection` ](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/) interfaccia.  Questo oggetto viene creato dal client e passato come parametro per il [ `BindService` ](https://developer.xamarin.com/api/member/Android.Content.Context.BindService/) (metodo). Il `BindService` è disponibile in qualsiasi [ `Android.Content.Context` ](https://developer.xamarin.com/api/type/Android.Content.Context) oggetto (ad esempio un'attività). È una richiesta per il sistema operativo Android per avviare il servizio e un client a esso associati. Esistono tre modi per un client possono eseguire l'associazione a un servizio usando il `BindService` metodo:

* **Uno strumento di associazione di servizio** &ndash; uno strumento di associazione di servizio è una classe che implementa il [ `Android.OS.IBinder` ](https://developer.xamarin.com/api/type/Android.OS.IBinder) interfaccia. La maggior parte delle applicazioni non verranno implementata direttamente questa interfaccia, ma si estenderà il [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) classe. Questo è l'approccio più comune ed è adatto quando il servizio e i client presenti nello stesso processo.
* **Utilizzo di un Messenger** &ndash; questa tecnica è ideale per quando il servizio esista in un processo separato. Al contrario, le richieste di servizio vengono eseguito il marshalling tra il client e il servizio tramite un [ `Android.OS.Messenger` ](https://developer.xamarin.com/api/type/Android.OS.Messenger). Un' [ `Android.OS.Handler` ](https://developer.xamarin.com/api/type/Android.OS.Handler) viene creato nel servizio che gestirà il `Messenger` richieste. Questo argomento verrà trattato in un'altra Guida.
* **Usando Android AIDL Interface Definition Language ()** &ndash; [AIDL](https://developer.android.com/guide/components/aidl) è una tecnica avanzata non trattate in questa Guida.

Una volta che un client è stato associato a un servizio, la comunicazione tra i due è avviene tramite `Android.OS.IBinder` oggetto.  Questo oggetto è responsabile per l'interfaccia che consentirà al client interagire con il servizio. Non è necessario per ogni applicazione xamarin. Android implementare questa interfaccia da zero, il SDK Android fornisce il [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) classe che si occupa della maggior parte del codice necessaria con il marshalling dell'oggetto tra il client e il servizio.

Quando un client viene eseguito con il servizio, è necessario disassociare da quest'ultimo chiamando il `UnbindService` (metodo). Dopo l'ultimo client è stata annullata da un servizio, Android arresterà e smaltire il servizio associato.

Questo diagramma illustra come l'attività, connessione del servizio, strumento di associazione e servizio correlate tra loro:

![Diagramma che mostra come i componenti del servizio correlati tra loro](bound-services-images/bound-services-02.png "diagramma che mostra come i componenti del servizio correlati tra loro.")

Questa guida illustra come estendere il `Service` classe per implementare un servizio associato. Verrà inoltre spiegato che implementa `IServiceConnection` e l'estensione `Binder` per consentire a un client comunicare con il servizio. Un'app di esempio fornito con questa Guida, che contengono una soluzione con un singolo progetto xamarin. Android chiamato **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** . Si tratta di un'applicazione molto semplice che illustra come implementare un servizio e come associare un'attività ad esso. Il servizio associato è un'API molto semplice con un solo metodo, `GetFormattedTimestamp`, che restituisce una stringa che indica all'utente quando il servizio sia avviato e quanto tempo è in esecuzione. L'app consente anche all'utente disassociare manualmente e associare al servizio.

[![Schermata dell'applicazione in esecuzione in un telefono Android](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>Implementazione e utilizzo di un servizio associato

Esistono tre componenti che devono essere implementati in modo che un'applicazione Android per usare un servizio associato:

1. **Estendere il `Service` classe e implementare i metodi di Callback del ciclo di vita** &ndash; questa classe conterrà il codice che eseguirà le operazioni che verranno richieste del servizio. Questo argomento verrà trattato in dettaglio più avanti.
2. **Creare una classe che implementa `IServiceConnection`**  &ndash; questa interfaccia fornisce i metodi di callback verranno richiamato da Android per notificare al client quando la connessione al servizio è stato modificato, ad esempio il client è connesso o disconnesso per il servizio. La connessione al servizio fornirà anche un riferimento a un oggetto che il client può utilizzare per interagire direttamente con il servizio. Questo riferimento è noto come il _dello strumento di associazione_.
3. **Creare una classe che implementa `IBinder`**  &ndash; oggetto _Binder_ implementazione fornisce API che un client usa per comunicare con il servizio. Lo strumento di associazione può fornire un riferimento al servizio associato, consentendo di metodi da richiamare direttamente o lo strumento di associazione può fornire un'API che incapsula e consente di nascondere il servizio associato dall'applicazione client. Un `IBinder` deve fornire il codice necessario per le chiamate di procedura remota. Non è necessario (o consigliati) per implementare il `IBinder` interfaccia direttamente. Ma devono estendere le applicazioni di `Binder` tipo che fornisce la maggior parte delle funzionalità di base richieste da un `IBinder`.
4. **Avvio e associazione a un servizio** &ndash; dopo avere creati la connessione del servizio dello strumento di associazione e servizio di applicazione Android è responsabile dell'avvio del servizio e il binding a esso.

Ognuno di questi passaggi verrà descritte nelle sezioni seguenti in modo più dettagliato.

### <a name="extend-the-service-class"></a>Estendere il `Service` classe

Per creare un servizio usando xamarin. Android, è necessario per creare una sottoclasse `Service` e per decorare la classe con il [ `ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute). L'attributo viene utilizzato dagli strumenti di compilazione xamarin. Android per registrare correttamente il servizio dell'app **androidmanifest. XML** molto simile a un'attività di file, un servizio associato ha un proprio ciclo di vita e callback metodi associati il eventi significativi nel ciclo di vita. Nell'elenco seguente è riportato un esempio di alcuni dei metodi di callback più comuni che consente di implementare un servizio:

* `OnCreate` &ndash; Questo metodo viene richiamato da Android, come lo è creare un'istanza del servizio. E viene usato per inizializzare qualsiasi variabile o gli oggetti che vengono richiesti dal servizio durante il ciclo di vita. È facoltativo.
* `OnBind` &ndash; Questo metodo deve essere implementato da tutti i servizi associati. Viene richiamato quando il primo client prova a connettersi al servizio. Restituirà un'istanza di `IBinder` in modo che il client può interagire con il servizio. Fino a quando il servizio è in esecuzione, il `IBinder` oggetto verrà utilizzato per soddisfare le richieste client future per l'associazione al servizio.
* `OnUnbind` &ndash; Questo metodo viene chiamato quando non è avranno associato tutti i client associati. Restituendo `true` da questo metodo, il servizio in un secondo momento chiamerà `OnRebind` con l'intento passato a `OnUnbind` quando i nuovi client associare ad esso. Si potrebbe eseguire questa operazione quando un servizio continua l'esecuzione dopo che è stato non associato. Questa situazione si verifica se il servizio di recente non associati sono stati anche un servizio avviato, e `StopService` o `StopSelf` non era stato chiamato. In tale scenario, `OnRebind` consente l'intento da recuperare. Restituisce il valore predefinito `false` , che non esegue alcuna operazione. facoltativo.
* `OnDestroy` &ndash; Questo metodo viene chiamato quando il servizio è eliminazione definitiva di Android. Pulizia eventualmente necessarie, ad esempio il rilascio delle risorse, deve essere eseguita in questo metodo. facoltativo.

Gli eventi del ciclo di vita di chiavi di un servizio associato sono illustrati nella figura seguente:

![Diagramma che mostra l'ordine in cui vengono chiamati i metodi del ciclo di vita](bound-services-images/bound-services-01.png "diagramma che mostra l'ordine in cui vengono chiamati i metodi del ciclo di vita.")

Il seguente frammento di codice, dall'applicazione che accompagna questa guida complementare viene illustrato come implementare un servizio associato in xamarin. Android:

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

Nell'esempio, il `OnCreate` metodo inizializza un oggetto che contiene la logica per il recupero e la formattazione di un timestamp che potrebbe essere richiesto da un client. Se il primo client tenta di eseguire l'associazione al servizio, Android richiameranno il `OnBind` (metodo). Verrà creata un'istanza di questo servizio un `TimestampBinder` che consentirà ai client di accedere a questa istanza del servizio in esecuzione. Il `TimestampBinder` classe è descritto nella sezione successiva.

### <a name="implementing-ibinder"></a>Implementazione IBinder

Come accennato, un `IBinder` oggetto fornisce il canale di comunicazione tra un client e un servizio. Le applicazioni Android non devono implementare il `IBinder` interfaccia, il [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder/) dovrebbe essere estesa. Il `Binder` classe fornisce gran parte dell'infrastruttura necessaria che è necessario effettuare il marshalling l'oggetto strumento di associazione del servizio (che potrebbero essere in esecuzione in un processo separato) al client. Nella maggior parte dei casi, il `Binder` sottoclasse è solo poche righe di codice e ne esegue il wrapping di un riferimento al servizio. In questo esempio `TimestampBinder` ha una proprietà che espone il `TimestampService` al client:

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

Ciò `Binder` rende possibile richiamare i metodi pubblici nel servizio; ad esempio:

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

Una volta `Binder` è stato esteso, è necessario implementare `IServiceConnection` per connettere tutti gli elementi.

### <a name="creating-the-service-connection"></a>La creazione della connessione del servizio

Il `IServiceConnection` presenterà | introdurre | esporre | connettersi il `Binder` oggetto al client. Oltre all'implementazione di `IServiceConnection` interfaccia, è necessario estendere la classe `Java.Lang.Object`. Connessione del servizio deve anche fornire un modo che il client può accedere il `Binder` (e pertanto comunicare con il servizio associato).

Questo codice è di accompagnamento progetto di esempio è uno dei modi per implementare `IServiceConnection`:

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

Come parte del processo di associazione, richiamerà Android il `OnServiceConnected` metodo, fornendo il `name` del servizio che viene associato e `binder` che contiene un riferimento al servizio stesso. In questo esempio, la connessione al servizio ha due proprietà, uno che contiene un riferimento per lo strumento di associazione e un flag booleano per se il client è connesso al servizio o meno.

Il `OnServiceDisconnected` metodo viene richiamato solo quando la connessione tra un client e un servizio è in modo imprevisto smarrita o danneggiata. Questo metodo consente al client invia la risposta per l'interruzione del servizio.  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>Avvio e associazione a un servizio con un Intent esplicito

Per usare un servizio associato, è necessario creare un client (ad esempio, un'attività) istanze di un oggetto che implementa `Android.Content.IServiceConnection` e richiamare il `BindService` (metodo). `BindService` restituirà `true` se il servizio è associato, `false` in caso contrario. Il metodo `BindService` accetta tre parametri:

* **Un' `Intent`**  &ndash; l'intento deve identificare in modo esplicito quali servizio a cui connettersi.
* **Un' `IServiceConnection` oggetti** &ndash; questo oggetto è un intermediario che fornisce i metodi di callback per notificare al client quando il servizio associato viene avviato e arrestato.
* **[`Android.Content.Bind`](https://developer.xamarin.com/api/type/Android.Content.Bind/) enum** &ndash; questo parametro è un set di flag usati dal sistema quando associare l'oggetto. È il valore più diffuso [ `Bind.AutoCreate` ](https://developer.xamarin.com/api/field/Android.Content.Bind.AutoCreate/), che avvierà automaticamente il servizio se non è già in esecuzione.

Il frammento di codice seguente è un esempio di come avviare un servizio associato in un'attività tramite un intent esplicito:

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
> Inizia con Android 5.0 (livello API 21), è possibile solo per l'associazione a un servizio con un intent esplicito.

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>Architettura note sulla connessione del servizio e lo strumento di associazione.

Alcuni puristi OOP potrebbero disapprovare dell'implementazione precedente del `TimestampBinder` classe perché si tratta di una violazione del [forze di Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter) che, nel relativo formato più semplice indica "non può comunicare con onde; solo parlare agli amici". In questa particolare implementazione espone concrete `TimestampService` classe a tutti i client.

Non è in teoria, necessario per il client di conoscere il `TimestampService` e l'esposizione di tale classe concreta per i client possono rendere un'applicazione più delicato e difficile da gestire tutta la durata. Un approccio alternativo consiste nell'usare un'interfaccia che espone il `GetFormattedTimestamp()` metodo e le chiamate al servizio tramite proxy la `Binder` (o una possibile classe di connessione del servizio):  

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

Questo particolare esempio permettono un'attività richiamare metodi sul servizio stesso:

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
