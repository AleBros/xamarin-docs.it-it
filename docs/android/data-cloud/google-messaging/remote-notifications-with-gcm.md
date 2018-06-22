---
title: Notifiche remote con Google Cloud Messaging
description: Questa procedura dettagliata fornisce informazioni dettagliate su come usare Google Cloud Messaging per implementare le notifiche remote (detto anche le notifiche push) in un'applicazione di xamarin. Descrive le varie classi che è necessario implementare per comunicare con Google Cloud Messaging (GCM), viene spiegato come impostare le autorizzazioni nel manifesto dell'Android per l'accesso a GCM e viene illustrato come la messaggistica con un programma di test di esempio end-to-end.
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: f4a1451cb848f4da1f595c15d946f4e05292900d
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31045133"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Notifiche remote con Google Cloud Messaging

_Questa procedura dettagliata fornisce informazioni dettagliate su come usare Google Cloud Messaging per implementare le notifiche remote (detto anche le notifiche push) in un'applicazione di xamarin. Descrive le varie classi che è necessario implementare per comunicare con Google Cloud Messaging (GCM), viene spiegato come impostare le autorizzazioni nel manifesto dell'Android per l'accesso a GCM e viene illustrato come la messaggistica con un programma di test di esempio end-to-end._

> [!NOTE]
> GCM è stata sostituita da [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM server e le API client [sono state deprecate](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) e non sarà disponibile appena l'11 aprile 2019.

## <a name="gcm-notifications-overview"></a>Panoramica delle notifiche GCM

In questa procedura dettagliata, si creerà un'applicazione di xamarin che utilizza Google Cloud Messaging (GCM) per implementare le notifiche remote (noto anche come *notifiche push*). I diversi servizi finalità e listener da utilizzano GCM per la messaggistica remoto verrà implementato e verrà eseguito un test l'implementazione con un programma della riga di comando che simula un server applicazioni. 

Si noti che Firebase Cloud Messaging (FCM) è la nuova versione di GCM &ndash; Google consiglia di utilizzare FCM anziché GCM. Se si utilizza attualmente GCM, è consigliabile eseguire l'aggiornamento a FCM. Per ulteriori informazioni su FCM, vedere [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

Prima di procedere con questa procedura dettagliata, è necessario acquisire le credenziali necessarie per utilizzare i server GCM di Google. Questo processo è illustrato in [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md). In particolare, è necessario un *chiave API* e *ID mittente* per inserire il codice riportato di seguito viene presentato in questa procedura dettagliata. 

Si userà la procedura seguente per creare un'app client abilitati per GCM xamarin:

1.  Installare pacchetti aggiuntivi necessari per le comunicazioni con i server GCM.
2.  Configurare le autorizzazioni di app per accedere ai server GCM.
3.  Implementare il codice per verificare la presenza di Google Play Services. 
4.  Implementare un servizio di registrazione preventivo che negozia con GCM per un token di registrazione.
5.  Implementare un servizio di listener di ID istanza in attesa di aggiornamenti di token di registrazione da GCM.
6.  Implementare un servizio di listener GCM che riceve i messaggi remoti dal server di app tramite GCM.

Questa app utilizzerà una nuova funzionalità GCM nota come *argomento messaggistica*. Nella messaggistica di argomento, il server di app invia un messaggio a un argomento, anziché a un elenco di singoli dispositivi. I dispositivi che la sottoscrizione a tale argomento possono ricevere messaggi argomento come le notifiche push. Per ulteriori informazioni sulla messaggistica argomento GCM, vedere Google [implementazione messaggistica argomento](https://developers.google.com/cloud-messaging/topic-messaging). 

Quando l'applicazione client è pronto, è quindi implementato un'applicazione c# della riga di comando che invia una notifica push all'applicazione client tramite GCM. 

## <a name="walkthrough"></a>Procedura dettagliata

Per iniziare, creare una nuova soluzione vuota denominata **RemoteNotifications**. Successivamente, aggiungere un nuovo progetto Android a questa soluzione basata sul **App Android** modello. Questo progetto verrà chiamato **ClientApp**. (Se non si ha familiarità con la creazione di progetti di xamarin, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).) Il **ClientApp** progetto conterrà il codice per l'applicazione client di xamarin che riceve le notifiche remote tramite GCM. 

### <a name="add-required-packages"></a>Aggiungere pacchetti richiesti

Prima di è possibile implementare il codice di app client, è necessario installare diversi pacchetti che verranno utilizzate per la comunicazione con GCM. Inoltre, è necessario aggiungere l'applicazione di Google Play Store nel dispositivo se non è già installato.

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>Aggiungere il pacchetto GCM Xamarin Google Play Services

Per ricevere messaggi da Google Cloud Messaging il [Google Play Services](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/) framework deve essere presente nel dispositivo. Senza questo framework, un'applicazione Android non è possibile ricevere messaggi da server GCM. Google Play Services viene eseguito in background mentre il dispositivo Android è acceso, in modalità non interattiva in attesa di messaggi da GCM. Quando i messaggi ricevuti, Google Play Services converte i messaggi in tipi e trasmette quindi questi tipi per le applicazioni che sono registrati per loro. 

In Visual Studio, fare doppio clic su **riferimenti > Gestisci pacchetti NuGet...** ; in Visual Studio per Mac, fare doppio clic su **pacchetti > Aggiungi pacchetti...** . Cercare **Xamarin Google Play Services - GCM** e installare il pacchetto nel **ClientApp** progetto: 

[![Installazione di Google Play Services](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

Quando si installa **Xamarin Google Play Services - GCM**, **Xamarin Google Play Services - Base** viene installato automaticamente. Se si verifica un errore, modificare il progetto *minimo Android di destinazione* impostazione su un valore diverso da **compila con la versione SDK** e provare di nuovo l'installazione di NuGet. 

Successivamente, modificare **Mainactivity** e aggiungere le seguenti `using` istruzioni:

```csharp
using Android.Gms.Common;
using Android.Util;
```

In questo modo i tipi nel pacchetto di Google Play Services GMS disponibili per il codice e aggiunge la funzionalità di registrazione che verrà utilizzato per tenere traccia di varie transazioni con GMS. 

#### <a name="google-play-store"></a>Google Play Store

Per ricevere messaggi da GCM, è necessario installare l'applicazione di Google Play Store sul dispositivo. (Ogni volta che in un dispositivo è installata un'applicazione di Google Play, Google Play Store viene installato anche, dunque è probabile che sia già installato nel dispositivo di test.) Senza Google Play, un'applicazione Android non è possibile ricevere messaggi da GCM. Se non si dispone ancora dell'app di Google Play Store installata nel dispositivo, visitare il [Google Play](https://support.google.com/googleplay) sito web per scaricare e installare Google Play. 

In alternativa, è possibile utilizzare un emulatore Android che eseguono Android 2.2 o versione successiva anziché un dispositivo di test (non è necessario installarvi un emulatore Android di Google Play Store). Tuttavia, se si usa un emulatore, è necessario utilizzare Wi-Fi per connettersi a GCM ed è necessario aprire più porte nel firewall Wi-Fi, come descritto più avanti in questa procedura dettagliata. 

### <a name="set-the-package-name"></a>Impostare il nome del pacchetto

In [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md), è specificato un nome di pacchetto dell'App abilitata GCM (il nome del pacchetto funge anche dal *ID applicazione* associato con la chiave API e ID mittente). Aprire le proprietà per il **ClientApp** del progetto e impostare il nome del pacchetto a questa stringa. In questo esempio, il nome del pacchetto è impostata su `com.xamarin.gcmexample`:

[![L'impostazione del nome di pacchetto](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

Si noti che l'app client in grado di ricevere un token di registrazione da GCM se il nome del pacchetto non *esattamente* corrisponde al nome del pacchetto immesso nella console di Google Developer. 

### <a name="add-permissions-to-the-android-manifest"></a>Aggiungere le autorizzazioni per il manifesto Android

Un'applicazione Android deve disporre delle autorizzazioni seguenti configurate prima di poter ricevere notifiche da Google Cloud Messaging: 

-   `com.google.android.c2dm.permission.RECEIVE` &ndash; Concede l'autorizzazione per l'app per registrare e ricevere messaggi da Google Cloud Messaging. (Cosa `c2dm` significa? Questo è l'acronimo di _Cloud a dispositivo messaggistica_, che è il predecessore attualmente deprecato di GCM. 
    Utilizza ancora GCM `c2dm` in molte delle relative stringhe di autorizzazione.) 

-   `android.permission.WAKE_LOCK` &ndash; (Facoltativo) Impedisce la sospensione durante l'attesa di un messaggio CPU dal dispositivo. 

-   `android.permission.INTERNET` &ndash; Affinché l'applicazione client può comunicare con GCM, concede l'accesso a internet. 

-   *nome_pacchetto* `.permission.C2D_MESSAGE` &ndash; registra l'applicazione con Android e richiede l'autorizzazione per ricevere esclusivamente C2D tutti i messaggi (cloud a dispositivo). Il *nome_pacchetto* corrisponde al prefisso l'ID dell'applicazione. 

Queste autorizzazioni verranno stabilite nel manifesto di Android. Modifica **AndroidManifest.xml** e sostituire il contenuto con il codice XML seguente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
    package="YOUR_PACKAGE_NAME" 
    android:versionCode="1" 
    android:versionName="1.0" 
    android:installLocation="auto">
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" />
    <permission android:name="YOUR_PACKAGE_NAME.permission.C2D_MESSAGE" 
                android:protectionLevel="signature" />
    <application android:label="ClientApp" android:icon="@drawable/Icon">
    </application>
</manifest>
```

Nel codice XML precedente, modificare *YOUR_PACKAGE_NAME* per il nome del pacchetto per il progetto di app client. Ad esempio `com.xamarin.gcmexample`. 

### <a name="check-for-google-play-services"></a>Controllo per i servizi Google Play

Questa procedura dettagliata, si sta creando un'app di base con un singolo `TextView` nell'interfaccia utente. Questa app non indica direttamente l'interazione con GCM. È invece sarà guardare la finestra di output per vedere come l'handshake di app con GCM, e verificheremo il cassetto di notifica per le nuove notifiche man mano che arrivano. 

Innanzitutto, creare un layout per l'area dei messaggi. Modifica **Resources.layout.Main.axml** e sostituire il contenuto con il codice XML seguente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

Salvare **axml** e chiuderlo.

Quando viene avviata l'app client, si desidera verificare che Google Play Services sia disponibile prima di tentare di contattare GCM. Modifica **Mainactivity** e sostituire il ``count`` istanza dichiarazione di variabile con la dichiarazione di variabile di istanza seguenti: 

```csharp
TextView msgText;
```

Successivamente, aggiungere il metodo seguente per il **MainActivity** classe: 

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "Sorry, this device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

Questo codice controlla il dispositivo per verificare se è installato il Google Play Services APK. Se non è installato, viene visualizzato un messaggio nell'area del messaggio che chiede all'utente di scaricare un file APK da Google Play Store (o abilitarlo in Impostazioni sistema del dispositivo). Poiché si desidera eseguire questa verifica all'avvio dell'app client, verrà aggiunto alla fine di una chiamata a questo metodo `OnCreate`. 


Successivamente, sostituire il `OnCreate` (metodo) con il codice seguente:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

Questo codice verifica la presenza di Google Play Services file APK e scrive il risultato per l'area dei messaggi. 

Di seguito completamente ricompilare ed eseguire l'app. Verrà visualizzata una schermata simile nella schermata seguente: 

[![Google Play Services è disponibile](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

Se non si ottiene questo risultato, verificare che il Google Play Services APK sia installato nel dispositivo e che il **Xamarin Google Play Services - GCM** pacchetto viene aggiunto al **ClientApp** come spiegato di progetto in precedenza. Se si verifica un errore di compilazione, riprovare la pulizia della soluzione e compilare nuovamente il progetto. 

Successivamente, si verrà scritto il codice per contattare GCM e ottenere un token di registrazione.

### <a name="register-with-gcm"></a>Registrare con GCM

Prima che l'app può ricevere notifiche remote dal server di app, deve registrare con GCM e ottenere un token di registrazione. Le operazioni di registrazione dell'applicazione con GCM viene gestita da un `IntentService` che verranno creati. Il nostro `IntentService` esegue i passaggi seguenti: 

1.  Usa il [InstanceID](https://developers.google.com/instance-id/) API per generare i token di sicurezza che autorizzano l'app client per accedere al server di app. In cambio, otteniamo una registrazione di token da GCM.

2.  Inoltra il token di registrazione al server app (se il server app richiede).

3.  Sottoscrive una o più canali di argomento di notifica.

Dopo che è implementato `IntentService`, verrà eseguito un test per verificare che venga restituito una registrazione di token da GCM.

Aggiungere un nuovo file denominato **RegistrationIntentService.cs** e sostituire il codice del modello con le operazioni seguenti:


```csharp
using System;
using Android.App;
using Android.Content;
using Android.Util;
using Android.Gms.Gcm;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false)]
    class RegistrationIntentService : IntentService
    {
        static object locker = new object();

        public RegistrationIntentService() : base("RegistrationIntentService") { }

        protected override void OnHandleIntent (Intent intent)
        {
            try
            {
                Log.Info ("RegistrationIntentService", "Calling InstanceID.GetToken");
                lock (locker)
                {
                    var instanceID = InstanceID.GetInstance (this);
                    var token = instanceID.GetToken (
                        "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);

                    Log.Info ("RegistrationIntentService", "GCM Registration Token: " + token);
                    SendRegistrationToAppServer (token);
                    Subscribe (token);
                }
            }
            catch (Exception e)
            {
                Log.Debug("RegistrationIntentService", "Failed to get a registration token");
                return;
            }
        }

        void SendRegistrationToAppServer (string token)
        {
            // Add custom implementation here as needed.
        }

        void Subscribe (string token)
        {
            var pubSub = GcmPubSub.GetInstance(this);
            pubSub.Subscribe(token, "/topics/global", null);
        }
    }
}
```

Nell'esempio di codice precedente, modificare *YOUR_SENDER_ID* al numero ID del mittente per il progetto di app client. Per ottenere l'ID del mittente per il progetto: 

1.  Accedere al [Google Cloud Console](https://console.cloud.google.com/) e selezionare il nome del progetto dal menu a discesa. Nel **progetto info** riquadro che viene visualizzato per il progetto, fare clic su **Vai alle impostazioni del progetto**:

    [![Selezione progetto XamarinGCM](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2.  Nel **impostazioni** pagina, individuare il **numero progetto** &ndash; questo è l'ID del mittente per il progetto:

    [![Numero del progetto visualizzato](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

Si desidera avviare il nostro `RegistrationIntentService` quando l'app viene avviata l'esecuzione. Modifica **Mainactivity** e modificare il `OnCreate` metodo in modo che il nostro `RegistrationIntentService` viene avviato dopo verifica la presenza di Google Play Services: 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView(Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    if (IsPlayServicesAvailable ())
    {
        var intent = new Intent (this, typeof (RegistrationIntentService));
        StartService (intent);
    }
}
```

Ora esaminiamo ogni sezione di `RegistrationIntentService` per comprenderne il funzionamento. 

È annotare il nostro `RegistrationIntentService` con l'attributo seguente per indicare che il servizio non deve essere creata dal sistema: 

```csharp
[Service (Exported = false)]
```

Il `RegistrationIntentService` costruttore denomina il thread di lavoro *RegistrationIntentService* per semplificare il debug. 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

Le funzionalità principali di `RegistrationIntentService` in cui risiede il `OnHandleIntent` metodo. Si analizzerà il codice per vedere come registra l'app con GCM.


##### <a name="request-a-registration-token"></a>Richiedere un Token di registrazione

`OnHandleIntent` chiama innanzitutto Google [InstanceID.GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;) metodo per richiedere un token di registrazione da GCM. Abbiamo racchiudere il codice in un `lock` per impedire la possibilità di più tipi di registrazione che si verificano contemporaneamente &ndash; il `lock` assicura che questi tipi vengono elaborati in sequenza. Se è in grado di ottenere un token di registrazione, viene generata un'eccezione e viene registrato un errore. Se la registrazione ha esito positivo, `token` è impostato per il token di registrazione è tornati da GCM: 

```csharp
static object locker = new object ();
...
try
{
    lock (locker)
    {
        var instanceID = InstanceID.GetInstance (this);
        var token = instanceID.GetToken (
            "YOUR_SENDER_ID", GoogleCloudMessaging.InstanceIdScope, null);
        ...
    }
}
catch (Exception e)
{
    Log.Debug ...
```

##### <a name="forward-the-registration-token-to-the-app-server"></a>Inoltrare il Token di registrazione al server App

Se si ottiene un token di registrazione (ovvero, viene generata alcuna eccezione), viene chiamato `SendRegistrationToAppServer` per la registrazione dell'utente di associare token con l'account sul lato server (se presente) che viene gestita dall'applicazione. Poiché questa implementazione dipende dalla progettazione del server app, un metodo vuoto di seguito: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

In alcuni casi, il server di app non è necessario il token di registrazione dell'utente. In tal caso, questo metodo può essere omessa. Quando un token di registrazione viene inviato al server di app, `SendRegistrationToAppServer` devono mantenere un valore booleano per indicare se il token è stato inviato al server. Se il valore booleano è false, `SendRegistrationToAppServer` invia il token al server app &ndash; in caso contrario, il token è stato già inviato al server di app in una chiamata precedente. 


##### <a name="subscribe-to-the-notification-topic"></a>La sottoscrizione per l'argomento di notifica

Successivamente, viene chiamato il nostro `Subscribe` metodo per indicare a GCM che si desidera eseguire la sottoscrizione a un argomento di notifica. In `Subscribe`, definiamo la [GcmPubSub.Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) API per la sottoscrizione dell'app client per tutti i messaggi in `/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

Il server di app deve inviare messaggi di notifica `/topics/global` se li riceve. Si noti che l'argomento nome in `/topics` può essere qualsiasi, purché il server di app e l'app client concordare questi nomi. (In questo caso, è stato scelto il nome `global` per indicare che si desidera ricevere messaggi in tutti gli argomenti supportati dal server di app.) 

Per informazioni sull'argomento GCM messaggistica sul lato server, vedere Google [inviare messaggi agli argomenti](https://developers.google.com/cloud-messaging/topic-messaging). 


#### <a name="implement-an-instance-id-listener-service"></a>Implementare un servizio di Listener di ID di istanza

I token di registrazione siano univoci e sicura; Tuttavia, l'applicazione client (o GCM) potrebbe essere necessario aggiornare il token di registrazione in caso di reinstallazione di app o un problema di sicurezza. Per questo motivo, è necessario implementare un `InstanceIdListenerService` che risponde alle richieste di aggiornamento del token da GCM. 

Aggiungere un nuovo file denominato **InstanceIdListenerService.cs** e sostituire il codice del modello con le operazioni seguenti: 

```csharp
using Android.App;
using Android.Content;
using Android.Gms.Gcm.Iid;

namespace ClientApp
{
    [Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
    class MyInstanceIDListenerService : InstanceIDListenerService
    {
        public override void OnTokenRefresh()
        {
            var intent = new Intent (this, typeof (RegistrationIntentService));
            StartService (intent);
        }
    }
}
```

Annotare `InstanceIdListenerService` con l'attributo seguente per indicare che il servizio non è possibile creare un'istanza dal sistema e di ricevere il token di registrazione GCM (detto anche *ID istanza*) richieste di aggiornamento: 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

Il `OnTokenRefresh` metodo nel servizio avvia il `RegistrationIntentService` in modo che è possibile intercettare il nuovo token di registrazione.


#### <a name="test-registration-with-gcm"></a>Registrazione di test con GCM

Di seguito completamente ricompilare ed eseguire l'app. Se si riceve un token di registrazione da GCM, il token di registrazione deve essere visualizzato nella finestra di output. Ad esempio: 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>Gestione dei messaggi Downstream 

Il codice che è stata implementata finora è solo il codice di "configurazione"; viene verificato in Google Play Services viene installato e negozia con GCM e il server di app per preparare l'app client per la ricezione di notifiche remote. Tuttavia, abbiamo ancora implementare il codice che effettivamente riceve ed elabora i messaggi di notifica a valle. A tale scopo, è necessario implementare un *servizio Listener GCM*. Questo servizio riceve i messaggi di argomento dal server di app e localmente trasmette li come le notifiche. Dopo che si implementa questo servizio, si creerà un programma di test per inviare messaggi a GCM in modo che possiamo vedere se l'implementazione funziona correttamente. 


#### <a name="add-a-notification-icon"></a>Aggiungere un'icona di notifica

Aggiungere innanzitutto una piccola icona che verrà visualizzato nell'area di notifica quando viene avviata la notifica. È possibile copiare [questa icona](remote-notifications-with-gcm-images/ic-stat-ic-notification.png) al progetto o creare la propria icona personalizzata. Il file dell'icona sarà denominato **ic_stat_button_click.png** e copiarlo il **risorse/drawable** cartella. È necessario utilizzare **Aggiungi > elemento esistente...**  per includere il file icona nel progetto.


#### <a name="implement-a-gcm-listener-service"></a>Implementare un servizio di Listener GCM

Aggiungere un nuovo file denominato **GcmListenerService.cs** e sostituire il codice del modello con le operazioni seguenti:

```csharp
using Android.App;
using Android.Content;
using Android.OS;
using Android.Gms.Gcm;
using Android.Util;

namespace ClientApp
{
    [Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
    public class MyGcmListenerService : GcmListenerService
    {
        public override void OnMessageReceived (string from, Bundle data)
        {
            var message = data.GetString ("message");
            Log.Debug ("MyGcmListenerService", "From:    " + from);
            Log.Debug ("MyGcmListenerService", "Message: " + message);
            SendNotification (message);
        }

        void SendNotification (string message)
        {
            var intent = new Intent (this, typeof(MainActivity));
            intent.AddFlags (ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity (this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                .SetSmallIcon (Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle ("GCM Message")
                .SetContentText (message)
                .SetAutoCancel (true)
                .SetContentIntent (pendingIntent);

            var notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
            notificationManager.Notify (0, notificationBuilder.Build());
        }
    }
}
```

Esaminiamo ora ogni sezione del nostro `GcmListenerService` per comprenderne il funzionamento. 

È annotare `GcmListenerService` con un attributo per indicare che questo servizio non è possibile creare un'istanza dal sistema ed è stato incluso un filtro per indicare che riceve i messaggi di GCM preventivo: 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

Quando `GcmListenerService` riceve un messaggio da GCM, il `OnMessageReceived` metodo viene richiamato. Questo metodo estrae il contenuto del messaggio da passato `Bundle`, registra il contenuto del messaggio (quindi è possibile visualizzarlo nella finestra di output) e chiama `SendNotification` per avviare una notifica locale con il contenuto del messaggio ricevuto: 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

Il `SendNotification` metodo utilizza `Notification.Builder` per creare la notifica e quindi si utilizza il `NotificationManager` per avviare la notifica. In effetti, si converte il messaggio di notifica remoto in una notifica locale devono essere presentati all'utente.
Per ulteriori informazioni sull'utilizzo `Notification.Builder` e `NotificationManager`, vedere [notifiche locale](~/android/app-fundamentals/notifications/local-notifications.md).


#### <a name="declare-the-receiver-in-the-manifest"></a>Dichiarare il ricevitore nel manifesto

Prima di è possibile ricevere messaggi da GCM, è necessario dichiarare il listener GCM nel manifesto di Android. Modifica **AndroidManifest.xml** e sostituire il `<application>` sezione con il codice XML seguente: 

```xml
<application android:label="RemoteNotifications" android:icon="@drawable/Icon">
    <receiver android:name="com.google.android.gms.gcm.GcmReceiver" 
              android:exported="true" 
              android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="YOUR_PACKAGE_NAME" />
        </intent-filter>
    </receiver>
</application>
```

Nel codice XML precedente, modificare *YOUR_PACKAGE_NAME* per il nome del pacchetto per il progetto di app client. In questo esempio di questa procedura dettagliata, è il nome del pacchetto `com.xamarin.gcmexample`. 

Ecco cosa ogni impostazione nel codice XML:

|Impostazione|Descrizione|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|Dichiara che l'app implementa un ricevitore GCM che acquisisce ed elabora i messaggi di notifica push in ingresso.|
|`com.google.android.c2dm.permission.SEND`|Dichiara che solo i server GCM possono inviare messaggi direttamente all'app.|
|`com.google.android.c2dm.intent.RECEIVE`|Filtro preventivo pubblicità che l'applicazione gestisce messaggi trasmessi da GCM.|
|`com.google.android.c2dm.intent.REGISTRATION`|Filtro preventivo pubblicità che l'applicazione gestisce nuovi tipi di registrazione (ovvero, sono stati implementati a un servizio di Listener di ID di istanza).|

In alternativa, è possibile decorare `GcmListenerService` con questi attributi, invece di specificarli nella XML; in questo caso specifichiamo in **AndroidManifest.xml** in modo che gli esempi di codice sono più semplici da seguire. 


### <a name="create-a-message-sender-to-test-the-app"></a>Creare il mittente del messaggio per testare l'App

È ora possibile aggiungere un progetto di applicazione console desktop c# alla soluzione e chiamarlo **MessageSender**. Verrà utilizzata questa applicazione console per simulare un server applicazioni &ndash; invierà i messaggi di notifica **ClientApp** tramite GCM. 


#### <a name="add-the-jsonnet-package"></a>Aggiungere il pacchetto di Json.NET

In questa applicazione console, svilupperemo il un payload JSON che contiene il messaggio di notifica che si desidera inviare all'applicazione client. Si userà il **Json.NET** pacchetto **MessageSender** per renderne più semplice compilare l'oggetto JSON richiesto da GCM. In Visual Studio, fare doppio clic su **riferimenti > Gestisci pacchetti NuGet...** ; in Visual Studio per Mac, fare doppio clic su **pacchetti > Aggiungi pacchetti...** . 

Possibile cercare il **Json.NET** del pacchetto e installarlo nel progetto: 

[![L'installazione del pacchetto di Json.NET](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)


#### <a name="add-a-reference-to-systemnethttp"></a>Aggiungere un riferimento a System.NET. http

È inoltre necessario aggiungere un riferimento a `System.Net.Http` in modo che è possibile creare un'istanza di un `HttpClient` per inviare il messaggio di test a GCM. Nel **MessageSender** progetto rapida **riferimenti > Aggiungi riferimento** e scorrere verso il basso fino a visualizzare **System.NET. HTTP**. Inserire un segno di spunta accanto a **System.NET. HTTP** e fare clic su **OK**. 


#### <a name="implement-code-that-sends-a-test-message"></a>Implementare il codice che invia un messaggio di Test

In **MessageSender**, modificare **Program.cs** e sostituire il contenuto con il codice seguente:

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json.Linq;

namespace MessageSender
{
    class MessageSender
    {
        public const string API_KEY = "YOUR_API_KEY";
        public const string MESSAGE = "Hello, Xamarin!";

        static void Main (string[] args)
        {
            var jGcmData = new JObject();
            var jData = new JObject();

            jData.Add ("message", MESSAGE);
            jGcmData.Add ("to", "/topics/global");
            jGcmData.Add ("data", jData);

            var url = new Uri ("https://gcm-http.googleapis.com/gcm/send");
            try
            {
                using (var client = new HttpClient())
                {
                    client.DefaultRequestHeaders.Accept.Add(
                        new MediaTypeWithQualityHeaderValue("application/json"));

                    client.DefaultRequestHeaders.TryAddWithoutValidation (
                        "Authorization", "key=" + API_KEY);

                    Task.WaitAll(client.PostAsync (url,
                        new StringContent(jGcmData.ToString(), Encoding.Default, "application/json"))
                            .ContinueWith(response =>
                            {
                                Console.WriteLine(response);
                                Console.WriteLine("Message sent: check the client device notification tray.");
                            }));
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Unable to send GCM message:");
                Console.Error.WriteLine(e.StackTrace);
            }
        }
    }
}
```

Nel codice precedente, modificare *YOUR_API_KEY* con la chiave API per il progetto di app client. 

Il server di app di test invia il messaggio in formato JSON seguente GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM, a sua volta, inoltra il messaggio per l'app client. Creare ora **MessageSender** e aprire una finestra della console in cui è possibile eseguire dalla riga di comando.



### <a name="try-it"></a>Prova

Ora si è pronti per testare l'app client. Se si usa un emulatore o se il dispositivo sta comunicando con GCM tramite Wi-Fi, è necessario aprire le porte TCP seguenti sul firewall per i messaggi GCM ottenere: 5228 5229 e 5230.

Avviare l'app client e osservare la finestra di output. Dopo il `RegistrationIntentService` riceva correttamente una registrazione di token da GCM, la finestra di output deve visualizzare il token con l'output del log simile a quello riportato di seguito:

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

A questo punto l'app client è pronto a ricevere un messaggio di notifica remoto. Dalla riga di comando, eseguire il **MessageSender.exe** programma di inviare un messaggio di notifica "Hello, Xamarin" per l'applicazione client.
Se non è stata ancora compilata la **MessageSender** del progetto, questa operazione.

Per eseguire **MessageSender.exe** in Visual Studio, aprire un prompt dei comandi, modificare il **MessageSender/bin/Debug** directory ed eseguire direttamente il comando:

```cmd
MessageSender.exe
```

Per eseguire **MessageSender.exe** in Visual Studio per Mac, aprire una sessione Terminal, accedere alla **MessageSender/bin/Debug** la directory e utilizzare mono eseguire **MessageSender.exe** 

```bash
mono MessageSender.exe
```

Potrebbe richiedere fino a un minuto per il messaggio per la propagazione tramite GCM e nuovamente l'app client. Se il messaggio viene ricevuto correttamente, dovremmo vedere output analoga alla seguente nella finestra di output: 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

Inoltre, si noterà che una nuova icona di notifica è presente nella barra di notifica: 

[![Notiication viene visualizzata sul dispositivo](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

Quando si apre la barra di notifica per visualizzare le notifiche, verrà visualizzato il nostro notifica remota:

[![Viene visualizzato il messaggio di notifica](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

Complimenti, l'app ha ricevuto la prima notifica remoto.

Si noti che GCM non riceverà i messaggi se l'applicazione è arrestata force. Per riprendere le notifiche dopo un arresto forzato, l'app deve essere riavviato manully. Per ulteriori informazioni su questi criteri di Android, vedere [avviare controlli per le applicazioni arrestate](https://developer.android.com/about/versions/android-3.1.html#launchcontrols) e [dello stack overflow post](http://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267). 

 
## <a name="summary"></a>Riepilogo

Questa procedura dettagliata in dettaglio i passaggi per implementare le notifiche remote in un'applicazione di xamarin. Descritto come installare i pacchetti aggiuntivi necessari per le comunicazioni GCM e spiegato come configurare le autorizzazioni di app per l'accesso al server GCM. Viene fornito codice di esempio che illustra come controllare la presenza di Google Play Services come implementare un servizio intento di registrazione e il servizio di listener di ID istanza che negozia con GCM per un token di registrazione e come implementare un listener GCM servizio che riceve ed elabora i messaggi di notifica remoto. Infine, è implementato un programma di test della riga di comando per inviare notifiche di prova per l'applicazione client tramite GCM. 


## <a name="related-links"></a>Collegamenti correlati

- [RemoteNotifications GCM (esempio)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications)
- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
