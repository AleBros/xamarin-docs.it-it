---
title: Notifiche remote con Google Cloud Messaging
description: Questa procedura dettagliata fornisce informazioni dettagliate su come usare Google Cloud Messaging per implementare le notifiche remote (detta anche le notifiche push) in un'applicazione xamarin. Android. Descrive le varie classi che è necessario implementare per comunicare con Google Cloud Messaging (GCM), viene spiegato come impostare le autorizzazioni nel manifesto Android per l'accesso a GCM e viene illustrato l'invio di messaggi end-to-end con un programma di test di esempio.
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: e5a5e44a61d352b5de05564ebb7192d21ed83dfa
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668894"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Notifiche remote con Google Cloud Messaging

_Questa procedura dettagliata fornisce informazioni dettagliate su come usare Google Cloud Messaging per implementare le notifiche remote (detta anche le notifiche push) in un'applicazione xamarin. Android. Descrive le varie classi che è necessario implementare per comunicare con Google Cloud Messaging (GCM), viene spiegato come impostare le autorizzazioni nel manifesto Android per l'accesso a GCM e viene illustrato l'invio di messaggi end-to-end con un programma di test di esempio._

> [!NOTE]
> GCM è stata sostituita da [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM server e le API client [sono state deprecate](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) e non sarà disponibile appena 11 aprile 2019.

## <a name="gcm-notifications-overview"></a>Panoramica delle notifiche GCM

In questa procedura dettagliata, si creerà un'applicazione xamarin. Android che usa Google Cloud Messaging (GCM) per implementare le notifiche remote (noto anche come *notifiche push*). Che verrà implementato i vari servizi intent e il listener usano GCM per messaggistica remote e verrà testata la nostra implementazione con un programma della riga di comando che simula un server applicazioni. 

Si noti che Firebase Cloud Messaging (FCM) è la nuova versione di GCM &ndash; Google consiglia di utilizzare FCM anziché a GCM. Se attualmente si usa GCM, è consigliabile eseguire l'aggiornamento da FCM. Per altre informazioni sulle FCM, vedere [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

Prima di continuare con questa procedura dettagliata, è necessario acquisire le credenziali necessarie per usare i server GCM di Google; Questo processo è illustrato in [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md). In particolare, è necessario un *API Key* e una *ID mittente* per inserire il codice di esempio presentato in questa procedura dettagliata. 

Si userà la procedura seguente per creare un'app client GCM abilitati xamarin. Android:

1.  Installare i pacchetti aggiuntivi necessari per le comunicazioni con i server GCM.
2.  Configurare le autorizzazioni dell'app per l'accesso al server GCM.
3.  Implementare il codice per verificare la presenza di Google Play Services. 
4.  Implementare un servizio intent di registrazione che negozia con GCM per un token di registrazione.
5.  Implementare un servizio listener instance ID che è in ascolto per gli aggiornamenti di token di registrazione da GCM.
6.  Implementare un servizio di listener GCM che riceve i messaggi remoti dal server di app tramite GCM.

Questa app utilizzerà una nuova funzionalità GCM detta *messaggistica argomento*. Nell'argomento di messaggistica, il server app invia un messaggio a un argomento, anziché a un elenco di singoli dispositivi. I dispositivi che sottoscrivono tale argomento possono ricevere i messaggi dell'argomento come notifiche push. Per altre informazioni sulla messaggistica argomento GCM, vedere di Google [implementazione di messaggistica argomento](https://developers.google.com/cloud-messaging/topic-messaging). 

Quando l'app client è pronto, abbiamo implementato una riga di comando C# dell'applicazione che invia una notifica push all'app client tramite GCM. 

## <a name="walkthrough"></a>Procedura dettagliata

Per iniziare, è possibile creare una nuova soluzione vuota chiamata **RemoteNotifications**. Successivamente, è possibile aggiungere un nuovo progetto Android alla soluzione basata sul **App per Android** modello. È possibile chiamare questo progetto **ClientApp**. (Se non si ha familiarità con la creazione di progetti xamarin. Android, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).) Il **ClientApp** progetto conterrà il codice per l'applicazione client di xamarin. Android che riceve le notifiche remote tramite GCM. 

### <a name="add-required-packages"></a>Aggiungere i pacchetti necessari

Prima di possiamo implementare codice dell'app client, è necessario installare alcuni pacchetti che verranno utilizzate per la comunicazione con GCM. Inoltre, è necessario aggiungere l'applicazione di Google Play Store per il dispositivo se non è già installato.

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>Aggiungere il pacchetto GCM Xamarin Google Play Services

Per ricevere messaggi da Google Cloud Messaging, la [Google Play Services](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/) framework deve essere presente nel dispositivo. Senza questo quadro operativo, un'applicazione Android non è possibile ricevere messaggi da server GCM. Google Play Services viene eseguito in background mentre viene acceso il dispositivo Android, in modalità non interattiva in ascolto dei messaggi da GCM. Quando arrivano i messaggi, Google Play Services converte i messaggi in Intent e trasmette quindi questi Intent per le applicazioni che hanno registrato per loro. 

In Visual Studio, fare doppio clic **riferimenti > Gestisci pacchetti NuGet...** ; in Visual Studio per Mac, fare doppio clic su **pacchetti > Aggiungi pacchetti...** . Cercare **Xamarin Google Play Services - GCM** e installare questo pacchetto nel **ClientApp** progetto: 

[![Installazione di Google Play Services](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

Quando si installa **Xamarin Google Play Services - GCM**, **Xamarin Google Play Services - Base** viene installato automaticamente. Se si verifica un errore, modificare il progetto *minima di Android di destinazione* impostazione su un valore diverso da **la compilazione con versione del SDK** e provare a ripetere l'installazione di NuGet. 

Successivamente, modificare **MainActivity.cs** e aggiungere quanto segue `using` istruzioni:

```csharp
using Android.Gms.Common;
using Android.Util;
```

In questo modo i tipi nel pacchetto di Google Play Services GMS disponibile al codice e aggiunge funzionalità di registrazione che verrà usato per tenere traccia di varie transazioni con GMS. 

#### <a name="google-play-store"></a>Google Play Store

Per ricevere messaggi da GCM, è necessario installare l'applicazione di Google Play Store sul dispositivo. (Ogni volta che in un dispositivo è installata un'applicazione di Google Play, Google Play Store viene installato anche, dunque è probabile che sia già installato nel dispositivo di test.) Senza Google Play, un'applicazione Android non è possibile ricevere messaggi da GCM. Se non si dispone ancora di Google Play Store installi l'app nel dispositivo, visitare il [Google Play](https://support.google.com/googleplay) sito web per scaricare e installare Google Play. 

In alternativa, è possibile usare un emulatore Android che eseguono Android 2.2 o versioni successive, invece di un dispositivo di test (non è necessario installare Google Play Store in un emulatore Android). Tuttavia, se si usa un emulatore, è necessario usare Wi-Fi per connettersi a GCM ed è necessario aprire diverse porte nel firewall Wi-Fi, come illustrato più avanti in questa procedura dettagliata. 

### <a name="set-the-package-name"></a>Impostare il nome del pacchetto

Nelle [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md), è stato specificato un nome di pacchetto per l'app abilitata per GCM (il nome del pacchetto funge anche dal *ID applicazione* associato con la chiave API e l'ID mittente). È possibile aprire le proprietà per il **ClientApp** del progetto e impostare il nome del pacchetto per questa stringa. In questo esempio, il nome del pacchetto è impostata su `com.xamarin.gcmexample`:

[![Impostare il nome del pacchetto](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

Si noti che l'app client in grado di ricevere un token di registrazione da GCM se il nome del pacchetto non dispone *esattamente* corrisponde al nome del pacchetto che abbiamo inseriti la console per sviluppatori di Google. 

### <a name="add-permissions-to-the-android-manifest"></a>Aggiungere autorizzazioni al manifesto Android

Un'applicazione Android debba avere le autorizzazioni seguenti configurate per poter ricevere le notifiche da Google Cloud Messaging: 

-   `com.google.android.c2dm.permission.RECEIVE` &ndash; Concede l'autorizzazione per l'App nell'app per registrare e ricevere messaggi da Google Cloud Messaging. (Cosa `c2dm` significa? Questo è l'acronimo _dispositivo messaggistica da Cloud a_, che è il predecessore attualmente deprecata a GCM. 
    Usa ancora GCM `c2dm` in molte delle relative stringhe di autorizzazione.) 

-   `android.permission.WAKE_LOCK` &ndash; (Facoltativo) Impedisce che il dispositivo CPU lo stato di sospensione mentre era in ascolto di un messaggio. 

-   `android.permission.INTERNET` &ndash; Concede l'accesso a internet in modo che l'app client può comunicare con GCM. 

-   *package_name* `.permission.C2D_MESSAGE` &ndash; registra l'applicazione con Android e chiede l'autorizzazione per ricevere esclusivamente C2D tutti i messaggi (da cloud a dispositivo). Il *package_name* prefisso è quello utilizzato per l'ID dell'applicazione. 

Queste autorizzazioni verrà impostata nel manifesto Android. È possibile modificare **androidmanifest. XML** e sostituire il contenuto con il codice XML seguente: 

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

Nel codice XML precedente, modificare *YOUR_PACKAGE_NAME* al nome del pacchetto per il progetto di app client. Ad esempio `com.xamarin.gcmexample`. 

### <a name="check-for-google-play-services"></a>Verifica per Google Play Services

In questa procedura dettagliata viene creata un'app di base con un singolo `TextView` nell'interfaccia utente. Questa app non indica direttamente l'interazione con GCM. Al contrario, si terrà sotto la finestra di output per vedere come l'handshake di app in GCM, e verificheremo il cassetto di notifica per le notifiche di nuove man mano che arrivano. 

In primo luogo, è possibile creare un layout per l'area dei messaggi. Modificare **Resources.layout.Main.axml** e sostituire il contenuto con il codice XML seguente: 

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

Salvare **Main. axml** e chiuderlo.

All'avvio dell'app client, si desidera verificare che Google Play Services sia disponibile prima di tentare di contattare GCM. Modificare **MainActivity.cs** e sostituire il ``count`` dichiarazione di variabile con la seguente dichiarazione di variabile di istanza dell'istanza: 

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

Questo codice verifica il dispositivo per verificare se è installato Google Play Services APK. Se non è installato, viene visualizzato un messaggio nell'area dei messaggi che chiede all'utente di scaricare un file APK dalla Store di Google Play (o abilitarlo in Impostazioni sistema del dispositivo). Poiché si vuole eseguire questa verifica all'avvio dell'app client, verrà aggiunto alla fine di una chiamata al metodo `OnCreate`. 


Sostituire quindi il `OnCreate` metodo con il codice seguente:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();
}
```

Questo codice verifica la presenza di Google Play Services APK e scrive il risultato per l'area dei messaggi. 

Verrà ora completamente ricompilare ed eseguire l'app. Si verrà visualizzata una schermata simile alla seguente: 

[![Google Play Services è disponibile](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

Se non si ottiene questo risultato, verificare che l'APK Google Play Services sia installato nel dispositivo e che il **Xamarin Google Play Services - GCM** pacchetto viene aggiunto alle **ClientApp** progetto come spiegato in precedenza. Se si verifica un errore di compilazione, provare la soluzione di pulizia e compilare nuovamente il progetto. 

Successivamente, si verrà scritto codice per contattare GCM e ottenere un token di registrazione.

### <a name="register-with-gcm"></a>Registrazione con GCM

Prima che l'app può ricevere notifiche remote dal server di app, è necessario registrazione con GCM e ottenere un token di registrazione. Le operazioni di registrazione dell'applicazione con GCM viene gestita da un `IntentService` che creiamo. Nostro `IntentService` esegue i passaggi seguenti: 

1.  Usa il [InstanceID](https://developers.google.com/instance-id/) API per generare i token di sicurezza che autorizzano l'app client per accedere al server di app. In cambio, otteniamo una registrazione di token da GCM.

2.  Inoltra il token di registrazione al server di app (se il server app lo richiede).

3.  Sottoscrive una o più canali di argomento di notifica.

Dopo questo Implementiamo `IntentService`, verrà eseguito un test per verificare che venga restituito registrazione di un token da GCM.

Aggiungere un nuovo file denominato **RegistrationIntentService.cs** e sostituire il codice del modello con il codice seguente:


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

Nel codice di esempio precedente, modificare *YOUR_SENDER_ID* al numero ID del mittente per il progetto di app client. Per ottenere l'ID del mittente per il progetto: 

1.  Accedere al [Google Cloud Console](https://console.cloud.google.com/) e selezionare il nome del progetto dal menu a discesa. Nel **progetto info** riquadro visualizzato per il progetto, fare clic su **Vai alle impostazioni del progetto**:

    [![Se si seleziona progetto XamarinGCM](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2.  Nel **le impostazioni** individuare la sezione la **numero del progetto** &ndash; questo è l'ID del mittente per il progetto:

    [![Numero di progetto visualizzato](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

Si vuole avviare nostro `RegistrationIntentService` quando l'app viene avviata l'esecuzione. Modificare **MainActivity.cs** e modificare il `OnCreate` metodo in modo che i `RegistrationIntentService` viene avviato dopo che viene verificata la presenza di Google Play Services: 

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

Ora diamo uno sguardo in ogni sezione di `RegistrationIntentService` per comprenderne il funzionamento. 

Prima di tutto è annotare i nostri `RegistrationIntentService` con l'attributo seguente per indicare che il servizio non deve essere creata dal sistema: 

```csharp
[Service (Exported = false)]
```

Il `RegistrationIntentService` costruttore denomina il thread di lavoro *RegistrationIntentService* per semplificare il debug. 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

Le funzionalità principali di `RegistrationIntentService` in cui risiede il `OnHandleIntent` (metodo). Di seguito viene illustrato questo codice per vedere come l'App nell'app registra con GCM.


##### <a name="request-a-registration-token"></a>Richiedere un Token di registrazione

`OnHandleIntent` prima di tutto le chiamate di Google [InstanceID.GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;) metodo per richiedere un token di registrazione da GCM. Abbiamo racchiudere il codice in un `lock` proteggersi contro la possibilità di più Intent di registrazione che si verificano contemporaneamente &ndash; il `lock` assicura che questi Intent vengono elaborati in sequenza. Se si non è possibile ottenere un token di registrazione, viene generata un'eccezione e si registra un errore. Se la registrazione ha esito positivo, `token` è impostato sul token di registrazione abbiamo ottenuto da GCM: 

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

##### <a name="forward-the-registration-token-to-the-app-server"></a>Inoltrare il Token di registrazione al Server di App

Se si riceve un token di registrazione (vale a dire, viene generata alcuna eccezione), chiamiamo `SendRegistrationToAppServer` registrazione dell'utente di associare token con l'account sul lato server (se presente) che viene mantenuto dall'applicazione. Poiché questa implementazione dipende dalla progettazione del server app, è possibile che un metodo vuoto è disponibile qui: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

In alcuni casi, il server di app non è necessario il token di registrazione dell'utente. In tal caso, questo metodo può essere omessa. Quando un token di registrazione viene inviato al server di app, `SendRegistrationToAppServer` devono mantenere un valore booleano che indica se il token è stato inviato al server. Se è false, il valore booleano `SendRegistrationToAppServer` invia il token al server di app &ndash; in caso contrario, il token è già stato inviato al server di app in una chiamata precedente. 


##### <a name="subscribe-to-the-notification-topic"></a>Sottoscrivere l'argomento di notifica

Quindi, chiamiamo il `Subscribe` metodo per indicare a GCM che si vuole sottoscrivere un argomento di notifica. Nelle `Subscribe`, chiamiamo il [GcmPubSub.Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) API per la sottoscrizione di app client per tutti i messaggi in `/topics/global`:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

Il server app deve inviare messaggi di notifica `/topics/global` per riceverli. Si noti che l'argomento di assegnare un nome in `/topics` può essere qualsiasi operazione desiderata, purché il server di app e l'app client concordare questi nomi. (In questo caso, è stato scelto il nome `global` per indicare che si desidera ricevere i messaggi in tutti gli argomenti supportati dal server di app.) 

Per informazioni sull'argomento GCM messaggistica sul lato server, vedere di Google [inviare messaggi agli argomenti](https://developers.google.com/cloud-messaging/topic-messaging). 


#### <a name="implement-an-instance-id-listener-service"></a>Implementare un servizio di Listener di ID di istanza

Token di registrazione sono univoci e protetti; Tuttavia, l'app client (o GCM) potrebbe essere necessario aggiornare il token di registrazione in caso di reinstallazione di app o un problema di sicurezza. Per questo motivo, è necessario implementare un `InstanceIdListenerService` che risponde alle richieste di aggiornamento del token da GCM. 

Aggiungere un nuovo file denominato **InstanceIdListenerService.cs** e sostituire il codice del modello con il codice seguente: 

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

Aggiungere annotazioni `InstanceIdListenerService` con l'attributo seguente per indicare che il servizio non viene possibile creare un'istanza dal sistema e che può ricevere token di registrazione GCM (detto anche *ID istanza*) richieste di aggiornamento: 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

Il `OnTokenRefresh` metodo nel servizio avvia il `RegistrationIntentService` affinché che consente di intercettare il nuovo token di registrazione.


#### <a name="test-registration-with-gcm"></a>Testare la registrazione con GCM

Verrà ora completamente ricompilare ed eseguire l'app. Se si riceve correttamente un token di registrazione da GCM, il token di registrazione dovrebbe comparire nella finestra di output. Ad esempio: 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>Gestire i messaggi a valle 

Il codice che è stato implementato finora è solo codice di "configurazione". Controlla se Google Play Services sia installato e negozia con GCM e il server di app per preparare l'app client per la ricezione di notifiche remote. Tuttavia, dobbiamo ancora implementare il codice che in realtà riceve ed elabora i messaggi di notifica a valle. A tale scopo, è necessario implementare una *al servizio di Listener GCM*. Questo servizio riceve i messaggi dell'argomento dal server di app e in locale trasmette loro invio delle notifiche. Dopo che si implementa questo servizio, verrà creato un programma di test per inviare messaggi a GCM in modo che possiamo vedere se la nostra implementazione funziona correttamente. 


#### <a name="add-a-notification-icon"></a>Aggiungere un'icona di notifica

Aggiungere innanzitutto una piccola icona che verrà visualizzato nell'area di notifica quando viene avviata la notifica. È possibile copiare [questa icona](remote-notifications-with-gcm-images/ic-stat-ic-notification.png) al progetto o creare la propria icona personalizzata. Denominiamo il file dell'icona **ic_stat_button_click.png** e copiarlo le **risorse/drawable** cartella. Ricordarsi di usare **Aggiungi > elemento esistente...**  per includere il file icona nel progetto.


#### <a name="implement-a-gcm-listener-service"></a>Implementare un servizio di Listener GCM

Aggiungere un nuovo file denominato **GcmListenerService.cs** e sostituire il codice del modello con il codice seguente:

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

Diamo un'occhiata a ciascuna sezione del nostro `GcmListenerService` per comprenderne il funzionamento. 

Innanzitutto, abbiamo annotare `GcmListenerService` con un attributo per indicare che questo servizio non è possibile creare un'istanza dal sistema, e includere un filtro intent per indicare che riceve i messaggi GCM: 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

Quando `GcmListenerService` riceve un messaggio da GCM, il `OnMessageReceived` metodo viene richiamato. Questo metodo consente di estrarre il contenuto del messaggio dal passato `Bundle`, registra il contenuto del messaggio (perché è possibile visualizzarla nella finestra di output) e chiama `SendNotification` per avviare una notifica locale con il contenuto del messaggio ricevuto: 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

Il `SendNotification` metodo viene utilizzato `Notification.Builder` per creare la notifica e quindi si usa il `NotificationManager` per avviare la notifica. In effetti, si converte il messaggio di notifica remota in una notifica locale da presentare all'utente.
Per altre informazioni sull'uso `Notification.Builder` e `NotificationManager`, vedere [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md).


#### <a name="declare-the-receiver-in-the-manifest"></a>Dichiarare il ricevitore nel manifesto

Prima che è possibile ricevere messaggi da GCM, è necessario dichiarare il listener GCM nel manifesto Android. È possibile modificare **androidmanifest. XML** e sostituire il `<application>` sezione con il codice XML seguente: 

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

Nel codice XML precedente, modificare *YOUR_PACKAGE_NAME* al nome del pacchetto per il progetto di app client. In questo esempio di questa procedura dettagliata, è il nome del pacchetto `com.xamarin.gcmexample`. 

Verrà ora esaminata del funzionamento di ogni impostazione nel codice XML:

|Impostazione|Descrizione|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|Dichiara che l'app implementi un ricevitore GCM che consente di acquisire ed elabora i messaggi di notifica push in ingresso.|
|`com.google.android.c2dm.permission.SEND`|Dichiara che solo i server GCM possono inviare messaggi direttamente all'app.|
|`com.google.android.c2dm.intent.RECEIVE`|Filtro Intent pubblicitari che l'app gestisce i messaggi trasmessi da GCM.|
|`com.google.android.c2dm.intent.REGISTRATION`|Filtro Intent pubblicitari che l'app gestisce nuovo Intent di registrazione (vale a dire, è stato implementato un servizio Listener Instance ID).|

In alternativa, è possibile decorare `GcmListenerService` con questi attributi invece di specificarli nel codice XML; in questo caso specifichiamo nella **androidmanifest. XML** in modo che gli esempi di codice sono più facili da seguire. 


### <a name="create-a-message-sender-to-test-the-app"></a>Creare il mittente del messaggio per testare l'App

È possibile aggiungere un C# applicazione console desktop progetto alla soluzione e denominarlo **MessageSender**. Si userà questa applicazione console per simulare un server applicazioni &ndash; invierà messaggi di notifica **ClientApp** tramite GCM. 


#### <a name="add-the-jsonnet-package"></a>Aggiungere il pacchetto Json.NET

In questa app console, poiché si sta sviluppando un payload JSON che contiene il messaggio di notifica da inviare all'app client. Si userà il **Json.NET** del pacchetto nel **MessageSender** per renderne più semplice creare l'oggetto JSON richiesto da GCM. In Visual Studio, fare doppio clic **riferimenti > Gestisci pacchetti NuGet...** ; in Visual Studio per Mac, fare doppio clic su **pacchetti > Aggiungi pacchetti...** . 

È possibile cercare i **Json.NET** del pacchetto e installarlo nel progetto: 

[![Installare il pacchetto Json.NET](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)


#### <a name="add-a-reference-to-systemnethttp"></a>Aggiungere un riferimento a System.NET. http

È necessario anche aggiungere un riferimento a `System.Net.Http` in modo che è possibile creare un'istanza di un `HttpClient` per inviare il messaggio di prova a GCM. Nel **MessageSender** del progetto, pulsante destro del mouse **riferimenti > Aggiungi riferimento** e scorrere verso il basso fino a quando non viene visualizzato **System.NET. HTTP**. Inserire un segno di spunta accanto a **System.NET. HTTP** e fare clic su **OK**. 


#### <a name="implement-code-that-sends-a-test-message"></a>Implementare il codice che invia un messaggio di Test

Nelle **MessageSender**, modificare **Program.cs** e sostituire il contenuto con il codice seguente:

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

Nel codice precedente, modificare *YOUR_API_KEY* per la chiave API per il progetto di app client. 

Il server di app di test invia il messaggio in formato JSON seguente GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM, a sua volta, inoltra questo messaggio per l'app client. Creiamo **MessageSender** e aprire una finestra della console in cui è possibile eseguire dalla riga di comando.



### <a name="try-it"></a>Prova

A questo punto siamo pronti per testare l'app client. Se si usa un emulatore o dispositivo per comunicare con GCM Wi-Fi, è necessario aprire le seguenti porte TCP nel firewall per i messaggi GCM ottenere: 5228 5229 e 5230.

Avviare l'app client e osservare la finestra di output. Dopo il `RegistrationIntentService` riceva correttamente una registrazione di token da GCM, la finestra di output deve visualizzare il token di output di registro simile a quello riportato di seguito:

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

A questo punto l'app client è pronto per ricevere un messaggio di notifica remota. Dalla riga di comando, eseguire la **MessageSender.exe** programma per inviare un messaggio di notifica "Hello, Xamarin" all'app client.
Se non è stata ancora compilata la **MessageSender** progetto, farlo ora.

Per eseguire **MessageSender.exe** in Visual Studio, aprire un prompt dei comandi, il **MessageSender/bin/Debug** directory ed eseguire il comando direttamente:

```cmd
MessageSender.exe
```

Per eseguire **MessageSender.exe** in Visual Studio per Mac, aprire una sessione Terminal, passare alla **MessageSender/bin/Debug** la directory e l'uso di mono per eseguire **MessageSender.exe** 

```bash
mono MessageSender.exe
```

Può richiedere fino a un minuto per il messaggio per la propagazione tramite GCM e tornando all'app client. Se il messaggio viene ricevuto correttamente, si dovrebbe essere visualizzato un output simile a quanto segue nella finestra di output: 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

Inoltre, si noterà che una nuova icona di notifica è stato visualizzato nella barra di notifica: 

[![Icona di notifica visualizzato nel dispositivo](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

Quando si apre la barra di notifica per visualizzare le notifiche, verrà visualizzata la notifica remota:

[![Viene visualizzato il messaggio di notifica](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

Complimenti, l'app ha ricevuto la prima notifica remota.

Si noti che i messaggi GCM non verranno ricevuti non sono più se l'app è arrestata force. Per riprendere le notifiche dopo un forzare l'arresto, è necessario riavviare manualmente l'app. Per altre informazioni su questi criteri di Android, vedere [avviare controlli per le applicazioni arrestate](https://developer.android.com/about/versions/android-3.1.html#launchcontrols) e ciò [stack overflow post](https://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267). 

 
## <a name="summary"></a>Riepilogo

Questa procedura dettagliata dettagliati i passaggi per l'implementazione di notifiche remote in un'applicazione xamarin. Android. È stato descritto come installare altri pacchetti necessari per le comunicazioni di GCM e spiega come configurare le autorizzazioni dell'app per l'accesso al server GCM. Viene fornito codice di esempio che illustra come controllare la presenza di Google Play Services, come implementare un servizio listener instance ID che negozia con GCM per un token di registrazione e un servizio di registrazione di intent e come implementare un listener GCM servizio che riceve ed elabora i messaggi di notifica remota. Infine, abbiamo implementato un programma di test della riga di comando per inviare notifiche di prova per l'app client tramite GCM. 


## <a name="related-links"></a>Collegamenti correlati

- [GCM RemoteNotifications (esempio)](https://developer.xamarin.com/samples/monodroid/RemoteNotifications)
- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
