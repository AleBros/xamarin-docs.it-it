---
title: Notifiche remote con Google Cloud Messaging
description: Questa procedura dettagliata fornisce una spiegazione dettagliata di come usare Google Cloud Messaging per implementare notifiche remote (dette anche notifiche push) in un'applicazione Novell. Android. Descrive le varie classi che è necessario implementare per comunicare con Google Cloud Messaging (GCM), spiega come impostare le autorizzazioni nel manifesto Android per l'accesso a GCM e illustra la messaggistica end-to-end con un programma di test di esempio.
ms.prod: xamarin
ms.assetid: 4FC3C774-EF93-41B2-A81E-C6A08F32C09B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/02/2019
ms.openlocfilehash: fd34532e647f0595ed8afa5ef7ad044b84b7d918
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525802"
---
# <a name="remote-notifications-with-google-cloud-messaging"></a>Notifiche remote con Google Cloud Messaging

> [!WARNING]
> Google deprecato GCM a partire dal 10 aprile 2018. I documenti e i progetti di esempio seguenti potrebbero non essere più gestiti. Il server GCM di Google e le API client verranno rimossi non appena il 29 maggio 2019. Google consiglia di eseguire la migrazione di app GCM a Firebase Cloud Messaging (FCM). Per ulteriori informazioni sulla deprecazione e la migrazione di GCM, vedere [Google Cloud Messaging deprecato](https://developers.google.com/cloud-messaging/).
>
> Per iniziare a usare le notifiche remote tramite la messaggistica cloud Firebase con Novell, vedere [notifiche remote con FCM](remote-notifications-with-fcm.md).

_Questa procedura dettagliata fornisce una spiegazione dettagliata di come usare Google Cloud Messaging per implementare notifiche remote (dette anche notifiche push) in un'applicazione Novell. Android. Descrive le varie classi che è necessario implementare per comunicare con Google Cloud Messaging (GCM), spiega come impostare le autorizzazioni nel manifesto Android per l'accesso a GCM e illustra la messaggistica end-to-end con un programma di test di esempio._

## <a name="gcm-notifications-overview"></a>Panoramica delle notifiche GCM

In questa procedura dettagliata verrà creata un'applicazione Novell. Android che usa Google Cloud Messaging (GCM) per implementare le notifiche remote (note anche come *notifiche push*). Verranno implementati i vari servizi di Intent e listener che usano GCM per la messaggistica remota e l'implementazione verrà testata con un programma da riga di comando che simula un server applicazioni. 

Prima di procedere con questa procedura dettagliata, è necessario acquisire le credenziali necessarie per usare i server GCM di Google; Questo processo è illustrato in [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md). In particolare, sarà necessario disporre di una *chiave API* e di un *ID mittente* da inserire nel codice di esempio presentato in questa procedura dettagliata. 

Per creare un'app client Novell. Android abilitata per GCM, verranno usati i passaggi seguenti:

1. Installare i pacchetti aggiuntivi richiesti per le comunicazioni con i server GCM.
2. Configurare le autorizzazioni dell'app per l'accesso ai server GCM.
3. Implementare il codice per verificare la presenza di Google Play Services. 
4. Implementare un servizio di registrazione preventivo che negozia con GCM per un token di registrazione.
5. Implementare un servizio listener ID istanza che ascolta gli aggiornamenti del token di registrazione da GCM.
6. Implementare un servizio listener GCM che riceve messaggi remoti dal server app tramite GCM.

Questa app userà una nuova funzionalità di GCM nota come *messaggistica degli argomenti*. Nell'argomento messaggistica, il server app invia un messaggio a un argomento, anziché a un elenco di singoli dispositivi. I dispositivi che sottoscrivono questo argomento possono ricevere messaggi di argomento come notifiche push. Per ulteriori informazioni sulla messaggistica dell'argomento GCM, vedere la pagina relativa all' [implementazione della messaggistica](https://developers.google.com/cloud-messaging/topic-messaging)per l'argomento di Google. 

Quando l'app client è pronta, verrà implementata un'applicazione della riga C# di comando che invia una notifica push all'app client tramite GCM. 

## <a name="walkthrough"></a>Procedura dettagliata

Per iniziare, creare una nuova soluzione vuota denominata **RemoteNotifications**. Aggiungere quindi un nuovo progetto Android alla soluzione basata sul modello di **app Android** . Chiameremo questo progetto **ClientApp**. Se non si ha familiarità con la creazione di progetti Novell. Android, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md). Il progetto **ClientApp** conterrà il codice per l'applicazione client Novell. Android che riceve le notifiche remote tramite GCM. 

### <a name="add-required-packages"></a>Aggiungere i pacchetti necessari

Prima di poter implementare il codice dell'app client, è necessario installare diversi pacchetti che verranno usati per la comunicazione con GCM. Inoltre, è necessario aggiungere l'applicazione Google Play Store al dispositivo, se non è già installata.

#### <a name="add-the-xamarin-google-play-services-gcm-package"></a>Aggiungere il pacchetto Novell Google Play Services GCM

Per ricevere i messaggi da Google Cloud Messaging, il Framework di [Google Play Services](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Gcm/) deve essere presente nel dispositivo. Senza questo Framework, un'applicazione Android non può ricevere messaggi dai server GCM. Google Play Services viene eseguito in background mentre il dispositivo Android è acceso, in ascolto silenzioso dei messaggi provenienti da GCM. Quando arrivano questi messaggi, Google Play Services converte i messaggi in Intent e quindi trasmette tali Intent alle applicazioni registrate. 

In Visual Studio fare clic con il pulsante destro del mouse su **riferimenti > Gestisci pacchetti NuGet...** ; in Visual Studio per Mac fare clic con il pulsante destro del mouse su **pacchetti > Aggiungi pacchetti...** . Cercare **novell Google Play Services-GCM** e installare il pacchetto nel progetto **ClientApp** : 

[![Installazione di Google Play Services](remote-notifications-with-gcm-images/1-google-play-services-sml.png)](remote-notifications-with-gcm-images/1-google-play-services.png#lightbox)

Quando si installa **novell Google Play Services-GCM**, **Novell Google Play Services-base** viene installato automaticamente. Se viene ricevuto un errore, modificare l'impostazione *minima di Android per* il progetto in un valore diverso da **Compila con la versione SDK** e provare a eseguire di nuovo l'installazione di NuGet. 

Modificare quindi **MainActivity.cs** e aggiungere le istruzioni seguenti `using` :

```csharp
using Android.Gms.Common;
using Android.Util;
```

In questo modo, i tipi nel pacchetto Google Play Services GMS sono disponibili per il codice e aggiunge funzionalità di registrazione che verranno usate per tenere traccia delle transazioni con GMS. 

#### <a name="google-play-store"></a>Google Play Store

Per ricevere i messaggi da GCM, è necessario che l'applicazione Google Play Store sia installata nel dispositivo. Ogni volta che un'applicazione Google Play viene installata in un dispositivo, viene installato anche Google Play Store, quindi è probabile che sia già installato nel dispositivo di test. Senza Google Play, un'applicazione Android non può ricevere messaggi da GCM. Se nel dispositivo non è ancora installata l'app Google Play Store, visitare il sito Web [Google Play](https://support.google.com/googleplay) per scaricare e installare Google Play. 

In alternativa, è possibile usare un emulatore Android che esegue Android 2,2 o versione successiva anziché un dispositivo di test (non è necessario installare Google Play Store in un emulatore Android). Tuttavia, se si usa un emulatore, è necessario usare Wi-Fi per connettersi a GCM ed è necessario aprire diverse porte nel firewall Wi-Fi, come illustrato più avanti in questa procedura dettagliata. 

### <a name="set-the-package-name"></a>Imposta il nome del pacchetto

In [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)è stato specificato un nome di pacchetto per l'app abilitata per GCM (il nome del pacchetto funge anche da *ID applicazione* associato alla chiave API e all'ID mittente). Aprire le proprietà per il progetto **ClientApp** e impostare il nome del pacchetto su questa stringa. In questo esempio il nome del pacchetto viene impostato su `com.xamarin.gcmexample`:

[![Impostazione del nome del pacchetto](remote-notifications-with-gcm-images/2-package-name-sml.png)](remote-notifications-with-gcm-images/2-package-name.png#lightbox)

Si noti che l'app client non sarà in grado di ricevere un token di registrazione da GCM se il nome del pacchetto non corrisponde *esattamente* al nome del pacchetto immesso in Google Developer Console. 

### <a name="add-permissions-to-the-android-manifest"></a>Aggiungere autorizzazioni al manifesto Android

Un'applicazione Android deve avere le autorizzazioni seguenti configurate prima di poter ricevere notifiche da Google Cloud Messaging: 

- `com.google.android.c2dm.permission.RECEIVE`&ndash; Concede l'autorizzazione all'app per la registrazione e la ricezione di messaggi da Google Cloud Messaging. (Cosa `c2dm` significa? Si tratta della _messaggistica da cloud a dispositivo_, che è il predecessore deprecato per GCM. 
    GCM USA `c2dm` ancora in molte delle relative stringhe di autorizzazione. 

- `android.permission.WAKE_LOCK`&ndash; (Facoltativo) impedisce la sospensione della CPU del dispositivo durante l'attesa di un messaggio. 

- `android.permission.INTERNET`&ndash; Concede l'accesso a Internet in modo che l'app client possa comunicare con GCM. 

- *package_name registra l'applicazione* `.permission.C2D_MESSAGE` con Android e richiede l'autorizzazione per la ricezione esclusiva di tutti i messaggi C2D (da cloud a dispositivo). &ndash; Il prefisso *package_name* è uguale all'ID dell'applicazione. 

Queste autorizzazioni verranno impostate nel manifesto Android. Modificare **file AndroidManifest. XML** e sostituire il contenuto con il codice XML seguente: 

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

Nel codice XML precedente, modificare *YOUR_PACKAGE_NAME* con il nome del pacchetto per il progetto di app client. Ad esempio `com.xamarin.gcmexample`. 

### <a name="check-for-google-play-services"></a>Verifica Google Play Services

Per questa procedura dettagliata, si sta creando un'app Bare Bones con una singola `TextView` nell'interfaccia utente. Questa app non indica direttamente l'interazione con GCM. Si osserverà invece la finestra di output per vedere come l'handshake dell'app con GCM e si verificherà la barra di notifica per le nuove notifiche man mano che arrivano. 

Prima di tutto, creare un layout per l'area del messaggio. Modificare **Resources. layout. Main. aXML** e sostituire il contenuto con il codice XML seguente: 

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

Salvare **Main. aXML** e chiuderlo.

Quando viene avviata l'app client, è necessario verificare che Google Play Services sia disponibile prima di provare a contattare GCM. Modificare **MainActivity.cs** e sostituire la ``count`` dichiarazione della variabile di istanza con la dichiarazione di variabile di istanza seguente: 

```csharp
TextView msgText;
```

Aggiungere quindi il metodo seguente alla classe **MainActivity** : 

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

Questo codice controlla il dispositivo per verificare se è installato Google Play Services APK. Se non è installato, viene visualizzato un messaggio nell'area dei messaggi che indica all'utente di scaricare un file APK dal Google Play Store (o abilitarlo nelle impostazioni di sistema del dispositivo). Poiché si vuole eseguire questo controllo all'avvio dell'app client, si aggiungerà una chiamata a questo metodo alla fine di `OnCreate`. 


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

Questo codice controlla la presenza del Google Play Services APK e scrive il risultato nell'area del messaggio. 

Ricompilare ed eseguire l'app. Verrà visualizzata una schermata simile alla schermata seguente: 

[![Google Play Services disponibile](remote-notifications-with-gcm-images/3-first-screen-sml.png)](remote-notifications-with-gcm-images/3-first-screen.png#lightbox)

Se non si ottiene questo risultato, verificare che il Google Play Services APK sia installato nel dispositivo e che il pacchetto **novell Google Play Services-GCM** venga aggiunto al progetto **ClientApp** come descritto in precedenza. Se si riceve un errore di compilazione, provare a pulire la soluzione e a compilare nuovamente il progetto. 

Successivamente, verrà scritto il codice per contattare GCM e verrà restituito un token di registrazione.

### <a name="register-with-gcm"></a>Registra con GCM

Prima che l'app possa ricevere notifiche remote dal server app, è necessario registrarsi con GCM e ottenere un token di registrazione. Il lavoro di registrazione dell'applicazione con GCM è gestito da un `IntentService` creato dall'utente. Per `IntentService` eseguire i passaggi seguenti: 

1. Usa l'API [InstanceID](https://developers.google.com/instance-id/) per generare token di sicurezza che autorizzano l'app client ad accedere al server app. In return, viene restituito un token di registrazione da GCM.

2. Invia il token di registrazione al server app (se richiesto dal server app).

3. Sottoscrive uno o più canali di argomenti di notifica.

Dopo l' `IntentService`implementazione, verrà testato per verificare se viene restituito un token di registrazione da GCM.

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

Nel codice di esempio precedente, modificare *YOUR_SENDER_ID* con il numero di ID del mittente per il progetto di app client. Per ottenere l'ID mittente per il progetto: 

1. Accedere a [Google Cloud console](https://console.cloud.google.com/) e selezionare il nome del progetto dal menu a discesa. Nel riquadro **info progetto** visualizzato per il progetto fare clic su **Vai a Impostazioni progetto**:

    [![Selezione del progetto XamarinGCM](remote-notifications-with-gcm-images/7-choose-project-sml.png)](remote-notifications-with-gcm-images/7-choose-project.png#lightbox)

2. Nella pagina **Impostazioni** individuare il **numero** &ndash; di progetto che corrisponde all'ID mittente per il progetto:

    [![Numero progetto visualizzato](remote-notifications-with-gcm-images/9-project-number-sml.png)](remote-notifications-with-gcm-images/9-project-number.png#lightbox)

Si vuole avviare il `RegistrationIntentService` quando viene avviata l'esecuzione dell'app. Modificare **MainActivity.cs** e modificare il `OnCreate` `RegistrationIntentService` metodo in modo che venga avviato dopo il controllo della presenza di Google Play Services: 

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

Diamo ora un'occhiata a ogni sezione di `RegistrationIntentService` per comprenderne il funzionamento. 

In primo luogo, annotare `RegistrationIntentService` l'oggetto con l'attributo seguente per indicare che non è necessario creare un'istanza del servizio dal sistema: 

```csharp
[Service (Exported = false)]
```

Il `RegistrationIntentService` Costruttore denomina il thread di lavoro *RegistrationIntentService* per semplificare il debug. 

```csharp
public RegistrationIntentService() : base ("RegistrationIntentService") { }
```

La funzionalità di base `RegistrationIntentService` di risiede `OnHandleIntent` nel metodo. Esaminiamo il codice per vedere come registra l'app con GCM.


##### <a name="request-a-registration-token"></a>Richiedere un token di registrazione

`OnHandleIntent`chiama prima di tutto il metodo [InstanceID. GetToken](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID.html#getToken&#40;java.lang.String,%20java.lang.String&#41;) di Google per richiedere un token di registrazione da GCM. Questo codice viene incapsulato `lock` in un oggetto per evitare la possibilità di più Intent di registrazione che &ndash; si `lock` verificano simultaneamente, assicurando che questi Intent vengano elaborati in sequenza. Se non si riesce a ottenere un token di registrazione, viene generata un'eccezione e viene registrato un errore. Se la registrazione ha esito `token` positivo, viene impostato sul token di registrazione restituito da GCM: 

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

##### <a name="forward-the-registration-token-to-the-app-server"></a>Inviare il token di registrazione al server applicazioni

Se si ottiene un token di registrazione (ovvero non è stata generata alcuna eccezione), si `SendRegistrationToAppServer` chiama per associare il token di registrazione dell'utente all'account lato server, se presente, gestito dall'applicazione. Poiché questa implementazione dipende dalla progettazione del server applicazioni, viene fornito un metodo vuoto: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

In alcuni casi, il server app non richiede il token di registrazione dell'utente. in tal caso, questo metodo può essere omesso. Quando un token di registrazione viene inviato al server app, `SendRegistrationToAppServer` deve mantenere un valore booleano per indicare se il token è stato inviato al server. Se questo valore booleano è `SendRegistrationToAppServer` false, invia il token al server &ndash; app in caso contrario, il token è già stato inviato al server app in una chiamata precedente. 


##### <a name="subscribe-to-the-notification-topic"></a>Sottoscrivere l'argomento relativo alla notifica

Viene quindi chiamato il `Subscribe` metodo per indicare a GCM che si vuole sottoscrivere un argomento di notifica. In `Subscribe`viene chiamata l'API [GcmPubSub. Subscribe](https://developers.google.com/android/reference/com/google/android/gms/gcm/GcmPubSub.html#subscribe&#40;java.lang.String,%20java.lang.String,%20android.os.Bundle&#41;) per sottoscrivere l'app client a tutti i messaggi `/topics/global`in:

```csharp
void Subscribe (string token)
{
    var pubSub = GcmPubSub.GetInstance(this);
    pubSub.Subscribe(token, "/topics/global", null);
}
```

Il server app deve inviare messaggi di notifica `/topics/global` a se li riceverà. Si noti che il nome dell' `/topics` argomento in può essere qualsiasi elemento desiderato, purché il server app e l'app client accettino entrambi questi nomi. (In questo caso, è stato `global` scelto il nome per indicare che si desidera ricevere messaggi su tutti gli argomenti supportati dal server app). 

Per informazioni sulla messaggistica degli argomenti di GCM sul lato server, vedere l'argomento relativo [all'invio di messaggi a](https://developers.google.com/cloud-messaging/topic-messaging)Google. 


#### <a name="implement-an-instance-id-listener-service"></a>Implementare un servizio listener ID istanza

I token di registrazione sono univoci e sicuri. Tuttavia, è possibile che l'app client (o GCM) debba aggiornare il token di registrazione in caso di reinstallazione dell'app o di un problema di sicurezza. Per questo motivo, è necessario implementare un `InstanceIdListenerService` che risponde alle richieste di aggiornamento del token da GCM. 

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

Annotare con l'attributo seguente per indicare che non è necessario creare un'istanza del servizio da parte del sistema e che può ricevere richieste di aggiornamento del token di registrazione GCM (detto anche *ID istanza*): `InstanceIdListenerService` 

```csharp
[Service(Exported = false), IntentFilter(new[] { "com.google.android.gms.iid.InstanceID" })]
```

Il `OnTokenRefresh` metodo nel servizio avvia il `RegistrationIntentService` in modo che possa intercettare il nuovo token di registrazione.


#### <a name="test-registration-with-gcm"></a>Testare la registrazione con GCM

Ricompilare ed eseguire l'app. Se si riceve correttamente un token di registrazione da GCM, il token di registrazione dovrebbe essere visualizzato nella finestra output. Ad esempio: 

```shell
D/Mono    ( 1934): Assembly Ref addref ClientApp[0xb4ac2400] -> Xamarin.GooglePlayServices.Gcm[0xb4ac2640]: 2
I/RegistrationIntentService( 1934): Calling InstanceID.GetToken
I/RegistrationIntentService( 1934): GCM Registration Token: f8LdveCvXig:APA91bFIsjUAbP-V8TPQdLR89qQbEJh1SYG38AcCbBUf34z5gSdUc5OsXrgs93YFiGcRSRafPfzkz23lf3-LvYV1CwrFheMjHgwPeFSh12MywnRIhz

```

### <a name="handle-downstream-messages"></a>Gestire i messaggi downstream 

Il codice implementato finora è solo codice di "configurazione"; Verifica se Google Play Services è installato e negozia con GCM e il server app per preparare l'app client per la ricezione di notifiche remote. Tuttavia, è ancora necessario implementare il codice che effettivamente riceve ed elabora i messaggi di notifica downstream. A tale scopo, è necessario implementare un *servizio listener GCM*. Questo servizio riceve i messaggi di argomento dal server app e li trasmette localmente come notifiche. Dopo aver implementato questo servizio, verrà creato un programma di test per inviare messaggi a GCM in modo da poter verificare se l'implementazione funziona correttamente. 


#### <a name="add-a-notification-icon"></a>Aggiungere un'icona di notifica

Si aggiungerà prima di tutto un'icona piccola che verrà visualizzata nell'area di notifica quando viene avviata la notifica. È possibile copiare [questa icona](remote-notifications-with-gcm-images/ic-stat-ic-notification.png) nel progetto o creare un'icona personalizzata. Denominare il file di icona **ic_stat_button_click. png** e copiarlo nella cartella Resources **/disegnator** . Ricordarsi di usare **aggiungi > elemento esistente...** per includere il file icona nel progetto.


#### <a name="implement-a-gcm-listener-service"></a>Implementare un servizio listener GCM

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

Diamo un'occhiata a ogni sezione del nostro `GcmListenerService` per comprenderne il funzionamento. 

In primo luogo, viene `GcmListenerService` annotata con un attributo per indicare che non è necessario creare un'istanza del servizio da parte del sistema e si include un filtro preventivo per indicare che riceve i messaggi GCM: 

```csharp
[Service (Exported = false), IntentFilter (new [] { "com.google.android.c2dm.intent.RECEIVE" })]
```

Quando `GcmListenerService` riceve un messaggio da GCM, viene `OnMessageReceived` richiamato il metodo. Questo metodo estrae il contenuto del messaggio dall'oggetto passato `Bundle`, registra il contenuto del messaggio (per poterlo visualizzare nella finestra di output) e chiama `SendNotification` per avviare una notifica locale con il contenuto del messaggio ricevuto: 

```csharp
var message = data.GetString ("message");
Log.Debug ("MyGcmListenerService", "From:    " + from);
Log.Debug ("MyGcmListenerService", "Message: " + message);
SendNotification (message);
```

Il `SendNotification` metodo usa `Notification.Builder` per creare la notifica `NotificationManager` e quindi usa per avviare la notifica. In questo modo, il messaggio di notifica remoto viene convertito in una notifica locale da presentare all'utente.
Per ulteriori informazioni sull'utilizzo `Notification.Builder` di `NotificationManager`e, vedere [Local notifications](~/android/app-fundamentals/notifications/local-notifications.md).


#### <a name="declare-the-receiver-in-the-manifest"></a>Dichiarare il ricevitore nel manifesto

Prima di poter ricevere i messaggi da GCM, è necessario dichiarare il listener GCM nel manifesto Android. Modificare **file AndroidManifest. XML** e sostituire la `<application>` sezione con il codice XML seguente: 

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

Nel codice XML precedente, modificare *YOUR_PACKAGE_NAME* con il nome del pacchetto per il progetto di app client. Nell'esempio di procedura dettagliata il nome del pacchetto `com.xamarin.gcmexample`è. 

Di seguito viene ora esaminata la funzione di ogni impostazione in questo XML:

|Impostazione|Descrizione|
|---|---|
|`com.google.android.gms.gcm.GcmReceiver`|Dichiara che l'app implementa un ricevitore GCM che acquisisce ed elabora i messaggi di notifica push in arrivo.|
|`com.google.android.c2dm.permission.SEND`|Dichiara che solo i server GCM possono inviare messaggi direttamente all'app.|
|`com.google.android.c2dm.intent.RECEIVE`|Finalità del filtro pubblicitario che l'app gestisce i messaggi broadcast da GCM.|
|`com.google.android.c2dm.intent.REGISTRATION`|Finalità del filtro pubblicitario che l'app gestisce nuovi Intent di registrazione (ovvero, è stato implementato un servizio listener ID istanza).|

In alternativa, è possibile decorare `GcmListenerService` con questi attributi anziché specificarli in XML. in questo caso vengono specificati in **file AndroidManifest. XML** in modo che gli esempi di codice siano più facili da seguire. 


### <a name="create-a-message-sender-to-test-the-app"></a>Creare un mittente del messaggio per testare l'app

Aggiungere un C# progetto di applicazione console desktop alla soluzione e chiamarlo **MessageSender**. Questa applicazione console verrà usata per simulare un server &ndash; applicazioni che invierà i messaggi di notifica a **ClientApp** tramite GCM. 


#### <a name="add-the-jsonnet-package"></a>Aggiungere il pacchetto Json.NET

In questa app console viene creato un payload JSON che contiene il messaggio di notifica che si vuole inviare all'app client. Il pacchetto **JSON.NET** verrà usato in **MessageSender** per semplificare la compilazione dell'oggetto JSON richiesto da GCM. In Visual Studio fare clic con il pulsante destro del mouse su **riferimenti > Gestisci pacchetti NuGet...** ; in Visual Studio per Mac fare clic con il pulsante destro del mouse su **pacchetti > Aggiungi pacchetti...** . 

Cercare il pacchetto **JSON.NET** e installarlo nel progetto: 

[![Installazione del pacchetto Json.NET](remote-notifications-with-gcm-images/4-add-json.net-sml.png)](remote-notifications-with-gcm-images/4-add-json.net.png#lightbox)


#### <a name="add-a-reference-to-systemnethttp"></a>Aggiungere un riferimento a System .NET. http

Sarà inoltre necessario aggiungere un riferimento a in `System.Net.Http` modo che sia possibile creare un' `HttpClient` istanza di per l'invio del messaggio di prova a GCM. Nel progetto **MessageSender** fare clic con il pulsante destro del mouse su **riferimenti > Aggiungi riferimento** e scorrere verso il basso fino a quando non viene visualizzato **System .NET. http**. Inserire un segno di spunta accanto a **System .NET. http** e fare clic su **OK**. 


#### <a name="implement-code-that-sends-a-test-message"></a>Implementare codice che invia un messaggio di prova

In **MessageSender**modificare **Program.cs** e sostituire il contenuto con il codice seguente:

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

Questo server app di test invia il messaggio in formato JSON seguente a GCM:

```csharp
{
  "to": "/topics/global",
  "data": {
    "message": "Hello, Xamarin!"
  }
}
```

GCM, a sua volta, trasmette questo messaggio all'app client. Compilare **MessageSender** e aprire una finestra della console in cui è possibile eseguirla dalla riga di comando.



### <a name="try-it"></a>Prova

A questo punto è possibile testare l'app client. Se si usa un emulatore o se il dispositivo sta comunicando con GCM tramite Wi-Fi, è necessario aprire le seguenti porte TCP sul firewall per i messaggi GCM per ottenere il passaggio seguente: 5228, 5229 e 5230.

Avviare l'app client e guardare la finestra di output. `RegistrationIntentService` Una volta che riceve un token di registrazione da GCM, la finestra di output visualizzerà il token con l'output del log simile al seguente:

```shell
I/RegistrationIntentService(16103): GCM Registration Token: eX9ggabZV1Q:APA91bHjBnQXMUeBOT6JDiLpRt8m2YWtY ...
```

A questo punto l'app client è pronta per ricevere un messaggio di notifica remoto. Dalla riga di comando, eseguire il programma **MessageSender. exe** per inviare un messaggio di notifica "Hello, Novell" all'app client.
Se non è ancora stato compilato il progetto **MessageSender** , eseguirlo ora.

Per eseguire **MessageSender. exe** in Visual Studio, aprire un prompt dei comandi, passare alla directory **MessageSender/bin/debug** ed eseguire il comando direttamente:

```cmd
MessageSender.exe
```

Per eseguire **MessageSender. exe** in Visual Studio per Mac, aprire una sessione terminal, passare a **MessageSender/bin/debug** della directory e usare mono per eseguire **MessageSender. exe.** 

```bash
mono MessageSender.exe
```

Potrebbe essere necessario attendere fino a un minuto per la propagazione del messaggio attraverso GCM e per tornare all'app client. Se il messaggio viene ricevuto correttamente, nella finestra di output dovrebbe essere visualizzato un output simile al seguente: 

```shell
D/MyGcmListenerService(16103): From:    /topics/global
D/MyGcmListenerService(16103): Message: Hello, Xamarin!
```

Si noterà inoltre che nella barra delle notifiche è presente una nuova icona di notifica: 

[![Icona di notifica visualizzata sul dispositivo](remote-notifications-with-gcm-images/5-icon-appears-sml.png)](remote-notifications-with-gcm-images/5-icon-appears.png#lightbox)

Quando si apre la barra di notifica per visualizzare le notifiche, verrà visualizzata la notifica remota:

[![Viene visualizzato il messaggio di notifica](remote-notifications-with-gcm-images/6-notification-in-tray-sml.png)](remote-notifications-with-gcm-images/6-notification-in-tray.png#lightbox)

Congratulazioni, l'app ha ricevuto la prima notifica remota.

Si noti che i messaggi GCM non verranno più ricevuti se l'app viene arrestata forzatamente. Per riprendere le notifiche dopo un arresto forzato, l'app deve essere riavviata manualmente. Per altre informazioni su questo criterio Android, vedere [avviare i controlli sulle applicazioni arrestate](https://developer.android.com/about/versions/android-3.1.html#launchcontrols) e questo [post di stack overflow](https://stackoverflow.com/questions/5051687/broadcastreceiver-not-receiving-boot-completed/19856267#19856267). 

 
## <a name="summary"></a>Riepilogo

Questa procedura dettagliata illustra i passaggi per l'implementazione di notifiche remote in un'applicazione Novell. Android. Viene descritto come installare i pacchetti aggiuntivi necessari per le comunicazioni GCM e viene illustrato come configurare le autorizzazioni dell'app per l'accesso ai server GCM. Viene fornito codice di esempio che illustra come verificare la presenza di Google Play Services, come implementare un servizio per finalità di registrazione e un servizio listener ID istanza che negozia con GCM per un token di registrazione e come implementare un listener GCM servizio che riceve ed elabora i messaggi di notifica remota. Infine, è stato implementato un programma di test della riga di comando per inviare notifiche di prova all'app client tramite GCM. 


## <a name="related-links"></a>Collegamenti correlati

- [GCM RemoteNotifications (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/remotenotifications)
- [Google Cloud Messaging](~/android/data-cloud/google-messaging/google-cloud-messaging.md)
