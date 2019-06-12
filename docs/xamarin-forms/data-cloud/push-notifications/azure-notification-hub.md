---
title: Inviare e ricevere notifiche push con hub di notifica di Azure e xamarin. Forms
description: Questo articolo illustra come usare hub di notifica per inviare notifiche push multipiattaforma per applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 05/23/2019
ms.openlocfilehash: 474398922bf00e3a430166d8b2e073d200e6ed6e
ms.sourcegitcommit: c2bba24233624c2ec0e9ee9827310ca022212a2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66835412"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-xamarinforms"></a>Inviare e ricevere notifiche push con hub di notifica di Azure e xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png)scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureNotificationHub)

Eseguire il push delle notifiche offrono informazioni da un sistema back-end a un'applicazione per dispositivi mobili. Apple, Google e altre piattaforme hanno i propri servizi di notifica Push (PNS). Hub di notifica di Azure consentono di centralizzare le notifiche tra piattaforme in modo che l'applicazione back-end può comunicare con un singolo hub, che si occupa di distribuzione per le comunicazioni e ogni PNS specifici della piattaforma.

Integrare gli hub di notifica di Azure in App per dispositivi mobili seguendo questa procedura:

1. [Configurare Hub di notifica di Azure e servizi notifica Push](#set-up-push-notification-services-and-azure-notification-hub).
1. [Informazioni su come usare i modelli e i tag](#register-templates-and-tags-with-the-azure-notification-hub).
1. [Creare un'applicazione xamarin. Forms multipiattaforma](#xamarinforms-application-functionality).
1. [Configurare il progetto Android nativo per le notifiche push](#configure-the-android-application-for-notifications).
1. [Configurare il progetto iOS nativo per le notifiche push](#configure-ios-for-notifications).
1. [Testare le notifiche tramite Hub di notifica di Azure](#test-notifications-in-the-azure-portal).
1. [Creare un'applicazione back-end per inviare notifiche](#create-a-notification-dispatcher).

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurare Hub di notifica di Azure e servizi notifica Push

L'integrazione di hub di notifica di Azure con un'app per dispositivi mobili xamarin. Forms è simile a l'integrazione di hub di notifica di Azure con un'applicazione nativa con Xamarin. Configurare un **applicazione FCM** seguendo la Console di Firebase passaggi [inviare notifiche Push a xamarin. Android con hub di notifica di Azure](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging). Completare i passaggi seguenti usando l'esercitazione di xamarin. Android:

1. Definire, ad esempio un nome di pacchetto Android `com.xamarin.notifysample`, che viene usato nell'esempio.
1. Scaricare **google-Services. JSON** dalla console Firebase. Si aggiungerà questo file per l'applicazione Android in un passaggio successivo.
1. Creare un'istanza di Hub di notifica di Azure e assegnargli un nome. Questo articolo ed esempio di utilizzo `xdocsnotificationhub` come nome dell'hub.
1. Copiare il FCM **chiave Server** e salvarlo come le **chiave API** sotto **Google (GCM/FCM)** nell'Hub di notifica di Azure.

Lo screenshot seguente illustra la configurazione di piattaforma di Google nell'Hub di notifica di Azure:

![Screenshot della configurazione di Azure Notification Hub Google](azure-notification-hub-images/fcm-notification-hub-config.png "configurazione di Google Hub notifica di Azure")

È necessario un computer macOS per completare la configurazione per i dispositivi iOS. Configurare APNS eseguendo i passaggi di iniziale nel [notifiche Push a xamarin. IOS con hub di notifica di Azure](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file). Completare i passaggi seguenti usando l'esercitazione di xamarin. ios:

1. Definire un identificatore del bundle iOS. Questo articolo ed esempio di utilizzo `com.xamarin.notifysample` come l'identificatore del bundle.
1. Creare un file di firma richiesta certificato (CSR) e usarlo per generare un certificato di notifica push.
1. Caricare il certificato di notifica push sotto **Apple (APNS)** nell'Hub di notifica di Azure.

Lo screenshot seguente illustra la configurazione di piattaforma di Apple in Hub di notifica di Azure:

![Screenshot della configurazione di Azure Notification Hub Apple](azure-notification-hub-images/apns-notification-hub-config.png "configurazione Apple Hub notifica di Azure")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>Registrare i modelli e i tag con l'Hub di notifica di Azure

L'Hub di notifica di Azure richiede alle applicazioni per dispositivi mobili di registrazione con l'hub, definire i modelli e sottoscrivere i tag. Registrazione collega un handle PNS specifici della piattaforma a un identificatore nell'Hub di notifica di Azure. Per altre informazioni sulle registrazioni, vedere [gestione delle registrazioni](/azure/notification-hubs/notification-hubs-push-notification-registration-management).

I modelli di consentono ai dispositivi di specificare i modelli di messaggio con parametri. È possibile personalizzare i messaggi in ingresso per ogni dispositivo, per ogni tag. Per altre informazioni sui modelli, vedere [modelli](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

Tag possono essere utilizzati per sottoscrivere le categorie dei messaggi, ad esempio novità, sport e meteo. Per semplicità, l'applicazione di esempio definisce un modello predefinito con un singolo parametro denominato `messageParam` e un singolo tag chiamato `default`. Nei sistemi più complessi, tag specifici dell'utente possono essere utilizzati per un utente del messaggio tra i dispositivi per notifiche personalizzate. Per altre informazioni sui tag, vedere [Routing ed espressioni tag](/azure/notification-hubs/notification-hubs-tags-segment-push-message).

Per ricevere correttamente messaggi, ogni applicazione nativa deve seguire questi passaggi:

1. Ottenere un token o Handle PNS dal PNS della piattaforma.
1. Registrare l'Handle PNS dell'Hub di notifica di Azure.
1. Specificare un modello che contiene gli stessi parametri di messaggi in uscita.
1. Sottoscrivere il tag di destinazione per i messaggi in uscita.

Questi passaggi sono descritti dettagliatamente più avanti per ogni piattaforma nel [configurare l'applicazione Android per le notifiche](#configure-the-android-application-for-notifications) e [configurazione iOS per le notifiche](#configure-ios-for-notifications) sezioni.

## <a name="xamarinforms-application-functionality"></a>Funzionalità dell'applicazione xamarin. Forms

L'applicazione di esempio xamarin. Forms visualizza un elenco dei messaggi di notifica push. Questo risultato viene ottenuto con il `AddMessage` metodo, che aggiunge il messaggio di notifica push specificato all'interfaccia utente. Anche questo metodo impedisce l'aggiunta all'interfaccia utente di messaggi duplicati e viene eseguito sul thread principale, pertanto può essere chiamato da qualsiasi thread. Nel codice seguente viene illustrato il metodo `AddMessage`.

```csharp
public void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        if (messageDisplay.Children.OfType<Label>().Where(c => c.Text == message).Any())
        {
            // Do nothing, an identical message already exists
        }
        else
        {
            Label label = new Label()
            {
                Text = message,
                HorizontalOptions = LayoutOptions.CenterAndExpand,
                VerticalOptions = LayoutOptions.Start
            };
            messageDisplay.Children.Add(label);
        }
    });
}
```

L'applicazione di esempio contiene un' **AppConstants.cs** file che definisce le proprietà utilizzate per i progetti di piattaforma. Questo file deve essere personalizzato con i valori di Hub di notifica di Azure. Il codice seguente illustra il **AppConstants.cs** file:

```csharp
public static class AppConstants
{
    public static string NotificationChannelName { get; set; } = "XamarinNotifyChannel";
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string ListenConnectionString { get; set; } = "< Insert your DefaultListenSharedAccessSignature >";
    public static string DebugTag { get; set; } = "XamarinNotify";
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string FCMTemplateBody { get; set; } = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    public static string APNTemplateBody { get; set; } = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";
}
```

Personalizzare i valori seguenti in `AppConstants` per connettere l'applicazione di esempio per l'Hub di notifica di Azure:

* `NotificationHubName`: Usare il nome dell'Hub di notifica di Azure creato nel portale di Azure.
* `ListenConnectionString`: Questo valore è disponibile nell'Hub di notifica di Azure sotto **criteri di accesso**.

La schermata seguente mostra dove questi valori si trovano nel portale di Azure:

![Screenshot dei criteri di accesso dell'Hub di notifica di Azure](azure-notification-hub-images/notification-hub-access-policy.png "i criteri di accesso dell'Hub di notifica di Azure")

## <a name="configure-the-android-application-for-notifications"></a>Configurare l'applicazione Android per le notifiche

Completare i passaggi seguenti per configurare l'applicazione Android per ricevere ed elaborare le notifiche:

1. Configurazione di Android **nome del pacchetto** corrisponda al nome del pacchetto nella console di Firebase.
1. Installare i seguenti pacchetti NuGet per interagire con Google Play, Firebase e hub di notifica di Azure:
    1. Xamarin.GooglePlayServices.Base.
    1. Xamarin.Firebase.Messaging.
    1. Xamarin.Azure.NotificationHubs.Android.
1. Copia il `google-services.json` file scaricata durante l'installazione FCM al progetto e impostare l'azione di compilazione su `GoogleServicesJson`.
1. [Configurare androidmanifest. XML per comunicare con Firebase](#configure-android-manifest).
1. [Registrare l'applicazione con Hub di notifica di Azure e Firebase usando un `FirebaseInstanceIdService` ](#register-using-a-custom-firebaseinstanceidservice).
1. [Elaborare i messaggi con una `FirebaseMessagingService` ](#process-messages-with-a-firebasemessagingservice).
1. [Aggiungere le notifiche in ingresso all'interfaccia utente di xamarin. Forms](#add-incoming-notifications-to-the-xamarinforms-ui).

> [!NOTE]
> Il **GoogleServicesJson** azione di compilazione fa parte il **Xamarin.GooglePlayServices.Base** pacchetto NuGet. Visual Studio 2019 imposta le azioni di compilazione disponibili durante l'avvio. Se non viene visualizzata **GoogleServicesJson** come azione di compilazione, riavviare Visual Studio 2019 dopo l'installazione dei pacchetti NuGet.

### <a name="configure-android-manifest"></a>Configurare manifesto Android

Il `receiver` elementi all'interno di `application` elemento consentire all'app di comunicare con Firebase. Il `uses-permission` elementi consentono all'app di gestire i messaggi e registrazione con Hub di notifica di Azure. L'intero **androidmanifest. XML** dovrebbe essere simile all'esempio seguente:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="YOUR_PACKAGE_NAME" android:installLocation="auto">
  <uses-sdk android:minSdkVersion="21" />
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
  <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
  <application android:label="Notification Hub Sample">
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
      <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
      </intent-filter>
    </receiver>
  </application>
</manifest>
```

### <a name="register-using-a-custom-firebaseinstanceidservice"></a>La registrazione usando un FirebaseInstanceIdService personalizzato

Firebase rilascia i token che identificano in modo univoco un dispositivo nel PNS. I token hanno una lunga durata, ma vengono talvolta aggiornati. Quando un token viene rilasciato o aggiornato, l'applicazione deve registrare il nuovo token con l'Hub di notifica di Azure. La registrazione viene gestita da un'istanza di una classe che deriva da `FirebaseInstanceIdService`.

Nell'applicazione di esempio, `FirebaseRegistrationService` classe eredita da `FirebaseInstanceIdService`. Questa classe ha un `IntentFilter` che include `com.google.firebase.INSTANCE_ID_EVENT`, consentendo il sistema operativo Android chiamare automaticamente `OnTokenRefresh` quando viene rilasciato un token da Firebase.

Il codice seguente mostra l'oggetto personalizzato `FirebaseInstanceIdService` dall'applicazione di esempio:

```csharp
[Service]
[IntentFilter(new [] { "com.google.firebase.INSTANCE_ID_EVENT"})]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    public override void OnTokenRefresh()
    {
        string token = FirebaseInstanceId.Instance.Token;

        // NOTE: logging the token is not recommended in production but during
        // development it is useful to test messages directly from Firebase
        Log.Info(AppConstants.DebugTag, $"Token received: {token}");

        SendRegistrationToServer(token);
    }

    void SendRegistrationToServer(string token)
    {
        try
        {
            NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

            // register device with Azure Notification Hub using the token from FCM
            Registration reg = hub.Register(token, AppConstants.SubscriptionTags);

            // subscribe to the SubscriptionTags list with a simple template.
            string pnsHandle = reg.PNSHandle;
            var cats = string.Join(", ", reg.Tags);
            var temp = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
        }
        catch (Exception e)
        {
            Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
        }
    }
}
```

Il `SendRegistrationToServer` nel metodo il `FirebaseRegistrationClass` registra il dispositivo con l'Hub di notifica di Azure e sottoscrive i tag con un modello. L'applicazione di esempio definisce un singolo tag chiamato `default` e un modello con un singolo parametro denominato `messageParam` nel **AppConstants.cs** file. Per altre informazioni sulla registrazione, i tag e i modelli, vedere [registrare modelli e i tag con l'Hub di notifica di Azure](#register-templates-and-tags-with-the-azure-notification-hub)

### <a name="process-messages-with-a-firebasemessagingservice"></a>Elaborare i messaggi con un FirebaseMessagingService

I messaggi in ingresso vengono indirizzati a un `FirebaseMessagingService` istanza, in cui possono essere convertiti in una notifica locale. Il progetto Android nell'applicazione di esempio contiene una classe denominata `FirebaseService` che eredita da `FirebaseMessagingService`. Questa classe ha un `IntentFilter` che include `com.google.firebase.MESSAGING_EVENT`, consentendo il sistema operativo Android chiamare automaticamente `OnMessageReceived` quando viene ricevuto un messaggio di notifica push.

L'esempio seguente illustra il `FirebaseService` dall'applicazione di esempio:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    public override void OnMessageReceived(RemoteMessage message)
    {
        base.OnMessageReceived(message);
        string messageBody = string.Empty;

        if (message.GetNotification() != null)
        {
            messageBody = message.GetNotification().Body;
        }

        // NOTE: test messages sent via the Azure portal will be received here
        else
        {
            messageBody = message.Data.Values.First();
        }

        // convert the incoming message to a local notification
        SendLocalNotification(messageBody);

        // send the incoming message directly to the MainPage
        SendMessageToMainPage(messageBody);
    }

    void SendLocalNotification(string body)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        intent.PutExtra("message", body);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetContentTitle("XamarinNotify Message")
            .SetSmallIcon(Resource.Drawable.ic_launcher)
            .SetContentText(body)
            .SetAutoCancel(true)
            .SetShowWhen(false)
            .SetContentIntent(pendingIntent);

        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            notificationBuilder.SetChannelId(AppConstants.NotificationChannelName);
        }

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }

    void SendMessageToMainPage(string body)
    {
        (App.Current.MainPage as MainPage)?.AddMessage(body);
    }
}
```

I messaggi in arrivo vengono convertiti in una notifica locale con il `SendLocalNotification` (metodo). Questo metodo crea un nuovo `Intent` e inserisce il messaggio contenuto nel `Intent` come una `string` `Extra`. Quando l'utente tocca la notifica locale, se l'app si trova in foreground o background, il `MainActivity` viene avviata e ha accesso al contenuto del messaggio tramite la `Intent` oggetto.

La notifica locale e `Intent` esempio richiede all'utente di eseguire l'azione di toccare la notifica. Ciò è utile quando l'utente deve intervenire prima che le modifiche di stato dell'applicazione. È possibile, tuttavia, i dati del messaggio di accesso senza richiedere l'intervento dell'utente in alcuni casi. Nell'esempio precedente invia anche messaggio direttamente all'oggetto corrente `MainPage` dell'istanza con il `SendMessageToMainPage` (metodo). Nell'ambiente di produzione, se si implementano entrambi i metodi per un singolo tipo di messaggio, il `MainPage` oggetto otterranno i messaggi duplicati se l'utente tocca la notifica.

> [!NOTE]
> L'applicazione Android riceverà le notifiche push solo se è in esecuzione in primo piano o in background. Per ricevere le notifiche push quando principale `Activity` è non in esecuzione, è necessario implementare un servizio, che esula dall'ambito di questo esempio. Per altre informazioni, vedere [la creazione di servizi di Android](/xamarin/android/app-fundamentals/services/)

### <a name="add-incoming-notifications-to-the-xamarinforms-ui"></a>Aggiungere le notifiche in ingresso all'interfaccia utente di xamarin. Forms

Il `MainActivity` classe deve ottenere le autorizzazioni per gestire le notifiche e gestire i dati del messaggio in ingresso. Il codice seguente illustra l'intero `MainActivity` implementazione:

```csharp
[Activity(Label = "NotificationHubSample", Icon = "@mipmap/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation, LaunchMode = LaunchMode.SingleTop)]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());

        if (IsPlayServiceAvailable() == false)
        {
            throw new Exception("This device does not have Google Play Services and cannot receive push notifications.");
        }

        CreateNotificationChannel();
    }

    protected override void OnNewIntent(Intent intent)
    {
        if (intent.Extras != null)
        {
            var message = intent.GetStringExtra("message");
            (App.Current.MainPage as MainPage)?.AddMessage(message);
        }

        base.OnNewIntent(intent);
    }

    bool IsPlayServiceAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(AppConstants.DebugTag, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(AppConstants.DebugTag, "This device is not supported");
            }
            return false;
        }
        return true;
    }

    void CreateNotificationChannel()
    {
        // Notification channels are new as of "Oreo".
        // There is no need to create a notification channel on older versions of Android.
        if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
        {
            var channelName = AppConstants.NotificationChannelName;
            var channelDescription = String.Empty;
            var channel = new NotificationChannel(channelName, channelName, NotificationImportance.Default)
            {
                Description = channelDescription
            };

            var notificationManager = (NotificationManager)GetSystemService(NotificationService);
            notificationManager.CreateNotificationChannel(channel);
        }
    }
}
```

Il `Activity` l'applicazione viene impostato dall'attributo `LaunchMode` a `SingleTop`. Questa modalità di avvio indica al sistema operativo Android per consentire solo una singola istanza di questa attività. In questa modalità di avvio in ingresso `Intent` i dati vengono instradati al `OnNewIntent` metodo, che estrae i dati del messaggio e lo invia al `MainPage` istanza tramite la `AddMessage` (metodo). Se l'applicazione usa una modalità di avvio diverse, è necessario gestire `Intent` dati in modo diverso.

Il `OnCreate` metodo utilizza un metodo helper denominato `IsPlayServiceAvailable` per assicurarsi che il dispositivo supporta Google Play Services. Gli emulatori o dispositivi che non supportano Google Play Service non possono ricevere le notifiche push da Firebase.

## <a name="configure-ios-for-notifications"></a>Configurare iOS per le notifiche

Il processo di configurazione dell'applicazione iOS per ricevere le notifiche è:

1. Configurare il **Bundle Identifier** nel **Info. plist** file in base al valore usato nel profilo di provisioning.
1. Aggiungere il **Abilita notifiche Push** opzione per il **entitlements. plist** file.
1. Aggiungere il **Xamarin.Azure.NotificationHubs.iOS** pacchetto NuGet al progetto.
1. [Registrazione per le notifiche con il servizio APN](#register-for-notifications-with-apns).
1. [Registrare l'applicazione con l'Hub di notifica di Azure e sottoscrivere i tag](#register-with-azure-notification-hub-and-subscribe-to-tags).
1. [Aggiungere notifiche APNS all'interfaccia utente di xamarin. Forms](#add-apns-notifications-to-xamarinforms-ui).

La schermata seguente mostra le **Abilita notifiche Push** opzione selezionata nel **entitlements. plist** file all'interno di Visual Studio:

![Screenshot il diritto di notifiche Push](azure-notification-hub-images/push-notification-entitlement.png "il diritto di notifiche Push")

### <a name="register-for-notifications-with-apns"></a>Registrarsi per le notifiche con il servizio APN

Il `FinishedLaunching` metodo di **AppDelegate.cs** file deve essere sostituito per registrarsi per le notifiche remote. Registrazione varia a seconda della versione di iOS in uso nel dispositivo. Il progetto iOS nell'applicazione di esempio esegue l'override di `FinishedLaunching` metodo da chiamare `RegisterForRemoteNotifications` come illustrato nell'esempio seguente:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();
    LoadApplication(new App());

    base.FinishedLaunching(app, options);

    RegisterForRemoteNotifications();

    return true;
}

void RegisterForRemoteNotifications()
{
    // register for remote notifications based on system version
    if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
    {
        UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert |
            UNAuthorizationOptions.Sound |
            UNAuthorizationOptions.Sound,
            (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
    }
    else if (UIDevice.CurrentDevice.CheckSystemVersion(8, 0))
    {
        var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
        new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();
    }
    else
    {
        UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
        UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);
    }
}
```

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>Registrazione con Hub di notifica di Azure e la sottoscrizione ai tag

Quando il dispositivo è registrato correttamente per le notifiche remote durante la `FinishedLaunching` metodo iOS chiamerà il `RegisteredForRemoteNotifications` (metodo). Deve eseguire l'override di questo metodo per eseguire le azioni seguenti:

1. Creare un'istanza di `SBNotificationHub`.
1. Annullare le registrazioni esistenti.
1. Registrare il dispositivo nell'hub di notifica.
1. Eseguire la sottoscrizione a tag specifici con un modello.

Per altre informazioni sulla registrazione del dispositivo, i modelli e i tag, vedere [registrare i modelli e i tag con l'Hub di notifica di Azure](#register-templates-and-tags-with-the-azure-notification-hub). Il codice seguente illustra la registrazione del dispositivo e i modelli:

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAllAsync(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplateAsync(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                if (errorCallback != null)
                {
                    Debug.WriteLine($"RegisterTemplateAsync error: {errorCallback}");
                }
            }
        });
    });
}
```

> [!NOTE]
> La registrazione per le notifiche remote può non riuscire in situazioni, ad esempio alcuna connessione di rete. È possibile scegliere di eseguire l'override di `FailedToRegisterForRemoveNotifications` metodo per gestire gli errori di registrazione.

### <a name="add-apns-notifications-to-xamarinforms-ui"></a>Aggiungere notifiche APNS all'interfaccia utente di xamarin. Forms

Quando un dispositivo riceve una notifica remota, le chiamate di iOS il `ReceivedRemoteNotification` (metodo). Messaggio in arrivo JSON viene convertito in un `NSDictionary` oggetti e il `ProcessNotification` metodo estrae i valori dal dizionario e li invia a xamarin. Forms `MainPage` istanza. Il `ReceivedRemoteNotifications` è sottoposto a override per chiamare `ProcessNotification` come illustrato nel codice seguente:

```csharp
public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
{
    ProcessNotification(userInfo, false);
}

void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
{
    // make sure we have a payload
    if (options != null && options.ContainsKey(new NSString("aps")))
    {
        // get the APS dictionary and extract message payload. Message JSON will be converted
        // into a NSDictionary so more complex payloads may require more processing
        NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
        string payload = string.Empty;
        NSString payloadKey = new NSString("alert");
        if (aps.ContainsKey(payloadKey))
        {
            payload = aps[payloadKey].ToString();
        }

        if (!string.IsNullOrWhiteSpace(payload))
        {
            (App.Current.MainPage as MainPage)?.AddMessage(payload);
        }

    }
    else
    {
        Debug.WriteLine($"Received request to process notification but there was no payload.");
    }
}
```

## <a name="test-notifications-in-the-azure-portal"></a>Testare le notifiche nel portale di Azure

Hub di notifica di Azure consentono di verificare che l'applicazione può ricevere messaggi di prova. Il **invio di prova** sezione nell'hub di notifica consente di scegliere la piattaforma di destinazione e inviare un messaggio. Impostando il **inviare a espressione Tag** al `default` invierà messaggi alle applicazioni che hanno registrato un modello per il `default` tag. Facendo clic sui **inviare** pulsante genera un report che include il numero di dispositivi raggiunto con il messaggio. Lo screenshot seguente mostra un test di notifica di Android nel portale di Azure:

![Screenshot di un messaggio di test di Hub di notifica di Azure](azure-notification-hub-images/azure-notification-hub-test-send.png "messaggio di prova di Hub di notifica di Azure")

### <a name="testing-tips"></a>Suggerimenti per la verifica

1. Durante il test che un'applicazione può ricevere le notifiche push, è necessario usare un dispositivo fisico. Dispositivi virtuali Android e iOS non possono essere configurati correttamente per la ricezione di notifiche push.
1. L'applicazione di Android di esempio i modelli e i token vengono registrati una volta quando viene rilasciato il token di Firebase. Durante il test è necessario richiedere un nuovo token e ripetere la registrazione con l'Hub di notifica di Azure. Il modo migliore per forzare il consiste nella pulizia del progetto, eliminare il `bin` e `obj` cartelle e disinstallare l'applicazione dal dispositivo prima di ricompilare e distribuire.
1. Molte parti del flusso di notifica push vengono eseguite in modo asincrono. Questo può comportare i punti di interruzione non viene raggiunto o sottoposta a hit in un ordine imprevisto. Usare la registrazione di dispositivi o di debug per tracciare l'esecuzione senza interrompere il flusso dell'applicazione. Filtrare il log di dispositivo Android usando il `DebugTag` specificati in `Constants`.

## <a name="create-a-notification-dispatcher"></a>Creare un dispatcher di notifica

Hub di notifica di Azure consentono all'applicazione back-end inviare notifiche a dispositivi sulle piattaforme. Nell'esempio viene illustrato il recapito di notifiche con le **NotificationDispatcher** applicazione console. L'applicazione include il **DispatcherConstants.cs** file che definisce le proprietà seguenti:

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

È necessario configurare il **DispatcherConstants.cs** in modo che corrispondano corrispondano alla configurazione di Hub di notifica di Azure. Il valore della `SubscriptionTags` proprietà deve corrispondere a quelli utilizzati nelle App client. Il `NotificationHubName` proprietà è il nome dell'istanza di Hub di notifica di Azure. Il `FullAccessConnectionString` proprietà è la chiave di accesso disponibile nell'hub di notifica **i criteri di accesso**. Lo screenshot seguente mostra la posizione del `NotificationHubName` e `FullAccessConnectionString` proprietà nel portale di Azure:

![Schermata del nome dell'Hub di notifica di Azure e FullAccessConnectionString](azure-notification-hub-images/notification-hub-full-access-policy.png "FullAccessConnectionString e nome dell'Hub di notifica di Azure")

L'applicazione console scorre in ciclo ogni `SubscriptionTags` il valore e invia notifiche ai sottoscrittori utilizzando un'istanza di `NotificationHubClient` classe. Il codice seguente illustra l'applicazione console `Program` classe:

``` csharp
class Program
{
    static int messageCount = 0;

    static void Main(string[] args)
    {
        Console.WriteLine($"Press the spacebar to send a message to each tag in {string.Join(", ", DispatcherConstants.SubscriptionTags)}");
        WriteSeparator();
        while (Console.ReadKey().Key == ConsoleKey.Spacebar)
        {
            SendTemplateNotificationsAsync().GetAwaiter().GetResult();
        }
    }

    private static async Task SendTemplateNotificationsAsync()
    {
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(DispatcherConstants.FullAccessConnectionString, DispatcherConstants.NotificationHubName);
        Dictionary<string, string> templateParameters = new Dictionary<string, string>();

        messageCount++;

        // Send a template notification to each tag. This will go to any devices that
        // have subscribed to this tag with a template that includes "messageParam"
        // as a parameter
        foreach (var tag in DispatcherConstants.SubscriptionTags)
        {
            templateParameters["messageParam"] = $"Notification #{messageCount} to {tag} category subscribers!";
            try
            {
                await hub.SendTemplateNotificationAsync(templateParameters, tag);
                Console.WriteLine($"Sent message to {tag} subscribers.");
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Failed to send template notification: {ex.Message}");
            }
        }

        Console.WriteLine($"Sent messages to {DispatcherConstants.SubscriptionTags.Length} tags.");
        WriteSeparator();
    }

    private static void WriteSeparator()
    {
        Console.WriteLine("==========================================================================");
    }
}
```

Quando viene eseguita l'applicazione console di esempio, è possibile premere la barra spaziatrice per inviare messaggi. I dispositivi che eseguono il client devono ricevere le applicazioni notifiche numerate, forniti siano configurati correttamente.

## <a name="related-links"></a>Collegamenti correlati

* [Modelli di notifica push](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).
* [Gestione di registrazione dispositivi](/azure/notification-hubs/notification-hubs-push-notification-registration-management).
* [Routing e Tag espressioni](/azure/notification-hubs/notification-hubs-tags-segment-push-message).
* [Esercitazione di hub di notifica di Azure di xamarin. Android](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm).
* [Esercitazione di hub di notifica di Azure di xamarin. IOS](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started).
