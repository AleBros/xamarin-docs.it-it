---
title: Inviare e ricevere notifiche push con hub di notifica di Azure e Novell. Forms
description: Questo articolo illustra come usare hub di notifica di Azure per inviare notifiche push multipiattaforma a applicazioni Novell. Forms.
ms.prod: xamarin
ms.assetid: 07D13195-3A0D-4C95-ACF0-143A9084973C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/27/2019
ms.openlocfilehash: 778f56ec844e2802c1e1bc783824d55218678761
ms.sourcegitcommit: e9d88587aafc912124b87732d81c3910247ad811
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78337296"
---
# <a name="send-and-receive-push-notifications-with-azure-notification-hubs-and-xamarinforms"></a>Inviare e ricevere notifiche push con hub di notifica di Azure e Novell. Forms

[![scaricare l'esempio](~/media/shared/download.png)scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurenotificationhub/)

Le notifiche push forniscono informazioni da un sistema back-end a un'applicazione per dispositivi mobili. Apple, Google e altre piattaforme hanno ciascuno il proprio servizio di notifica push (PNS). Hub di notifica di Azure consente di centralizzare le notifiche tra le piattaforme, in modo che l'applicazione back-end possa comunicare con un singolo hub, che si occupi di distribuire le notifiche a ciascun PNS specifico della piattaforma.

Integrare Hub di notifica di Azure in app per dispositivi mobili attenendosi alla procedura seguente:

1. [Configurare Notification Services push e hub di notifica di Azure](#set-up-push-notification-services-and-azure-notification-hub).
1. [Informazioni su come usare i modelli e i tag](#register-templates-and-tags-with-the-azure-notification-hub).
1. [Creare un'applicazione Novell. Forms multipiattaforma](#xamarinforms-application-functionality).
1. [Configurare il progetto Android nativo per le notifiche push](#configure-the-android-application-for-notifications).
1. [Configurare il progetto iOS nativo per le notifiche push](#configure-ios-for-notifications).
1. [Testare le notifiche usando l'hub di notifica di Azure](#test-notifications-in-the-azure-portal).
1. [Creare un'applicazione back-end per l'invio di notifiche](#create-a-notification-dispatcher).

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurare Notification Services push e hub di notifica di Azure

L'integrazione di hub di notifica di Azure con un'app per dispositivi mobili Novell. Forms è simile all'integrazione di hub di notifica di Azure con un'applicazione Novell nativa. Configurare un' **applicazione FCM** attenendosi alla procedura della console Firebase in [notifiche push a Novell. Android usando hub di notifica di Azure](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm#create-a-firebase-project-and-enable-firebase-cloud-messaging). Completare i passaggi seguenti usando l'esercitazione su Novell. Android:

1. Definire un nome di pacchetto Android, ad esempio `com.xamarin.notifysample`, che viene usato nell'esempio.
1. Scaricare **Google-Services. JSON** dalla console di Firebase. Questo file verrà aggiunto all'applicazione Android in un passaggio successivo.
1. Creare un'istanza di hub di notifica di Azure e assegnarle un nome. Questo articolo e l'esempio usano `xdocsnotificationhub` come nome dell'hub.
1. Copiare la **chiave del server** FCM e salvarla come **chiave API** in **Google (GCM/FCM)** nell'hub di notifica di Azure.

La schermata seguente mostra la configurazione della piattaforma Google nell'hub di notifica di Azure:

![Screenshot della configurazione di Google Hub di notifica di Azure](azure-notification-hub-images/fcm-notification-hub-config.png "Configurazione di Google Hub di notifica di Azure")

Per completare la configurazione per i dispositivi iOS, sarà necessario un computer macOS. Configurare APNS seguendo i passaggi iniziali in [notifiche push a Novell. iOS usando hub di notifica di Azure](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started#generate-the-certificate-signing-request-file). Completare i passaggi seguenti usando l'esercitazione su Novell. iOS:

1. Definire un identificatore del bundle iOS. Questo articolo e l'esempio usano `com.xamarin.notifysample` come identificatore del bundle.
1. Creare un file di richiesta di firma del certificato (CSR) e usarlo per generare un certificato di notifica push.
1. Caricare il certificato di notifica push in **Apple (APNS)** nell'hub di notifica di Azure.

La schermata seguente mostra la configurazione della piattaforma Apple nell'hub di notifica di Azure:

![Screenshot della configurazione Apple dell'hub di notifica di Azure](azure-notification-hub-images/apns-notification-hub-config.png "Configurazione Apple dell'hub di notifica di Azure")

## <a name="register-templates-and-tags-with-the-azure-notification-hub"></a>Registrare modelli e tag con l'hub di notifica di Azure

Hub di notifica di Azure richiede che le applicazioni per dispositivi mobili si registrino con l'hub, definiscono i modelli e sottoscrivono i tag. La registrazione collega un handle PNS specifico della piattaforma a un identificatore nell'hub di notifica di Azure. Per ulteriori informazioni sulle registrazioni, vedere [gestione delle registrazioni](/azure/notification-hubs/notification-hubs-push-notification-registration-management).

I modelli consentono ai dispositivi di specificare modelli di messaggio con parametri. I messaggi in ingresso possono essere personalizzati per ogni dispositivo, per ogni tag. Per ulteriori informazioni sui modelli, vedere [modelli](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

I tag possono essere usati per sottoscrivere categorie di messaggi quali notizie, sport e meteo. Per semplicità, l'applicazione di esempio definisce un modello predefinito con un solo parametro denominato `messageParam` e un tag singolo chiamato `default`. Nei sistemi più complessi, i tag specifici dell'utente possono essere usati per inviare messaggi a un utente tra dispositivi per ricevere notifiche personalizzate. Per ulteriori informazioni sui tag, vedere [routing ed espressioni Tag](/azure/notification-hubs/notification-hubs-tags-segment-push-message).

Per ricevere correttamente i messaggi, ogni applicazione nativa deve eseguire i passaggi seguenti:

1. Ottenere un handle o un token PNS dalla piattaforma PNS.
1. Registrare il punto di gestione PNS con l'hub di notifica di Azure.
1. Consente di specificare un modello che contiene gli stessi parametri dei messaggi in uscita.
1. Sottoscrivere il tag di destinazione dei messaggi in uscita.

Questi passaggi sono descritti in dettaglio per ogni piattaforma nelle sezioni [configurare l'applicazione Android per le notifiche](#configure-the-android-application-for-notifications) e [configurare iOS per le notifiche](#configure-ios-for-notifications) .

## <a name="xamarinforms-application-functionality"></a>Funzionalità dell'applicazione Novell. Forms

Nell'applicazione Novell. Forms di esempio viene visualizzato un elenco di messaggi di notifica push. Questa operazione viene eseguita con il metodo `AddMessage`, che aggiunge il messaggio di notifica push specificato all'interfaccia utente. Questo metodo impedisce inoltre l'aggiunta di messaggi duplicati all'interfaccia utente e viene eseguito sul thread principale, in modo che possa essere chiamato da qualsiasi thread. Nel codice seguente viene illustrato il metodo `AddMessage`.

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

L'applicazione di esempio contiene un file **appconstants.cs** , che definisce le proprietà utilizzate dai progetti della piattaforma. Questo file deve essere personalizzato con i valori dall'hub di notifica di Azure. Il codice seguente mostra il file **appconstants.cs** :

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

Personalizzare i valori seguenti in `AppConstants` per connettere l'applicazione di esempio all'hub di notifica di Azure:

* `NotificationHubName`: usare il nome dell'hub di notifica di Azure creato nell'portale di Azure.
* `ListenConnectionString`: questo valore si trova nell'hub di notifica di Azure in **criteri di accesso**.

Lo screenshot seguente mostra dove si trovano questi valori nel portale di Azure:

![Screenshot dei criteri di accesso dell'hub di notifica di Azure](azure-notification-hub-images/notification-hub-access-policy.png "Criteri di accesso dell'hub di notifica di Azure")

## <a name="configure-the-android-application-for-notifications"></a>Configurare l'applicazione Android per le notifiche

Completare i passaggi seguenti per configurare l'applicazione Android per la ricezione e l'elaborazione delle notifiche:

1. Configurare il **nome del pacchetto** Android in modo che corrisponda al nome del pacchetto nella console di Firebase.
1. Installare i pacchetti NuGet seguenti per interagire con Google Play, Firebase e hub di notifica di Azure:
    1. Xamarin.GooglePlayServices.Base.
    1. Xamarin.Firebase.Messaging.
    1. Xamarin.Azure.NotificationHubs.Android.
1. Copiare il file di `google-services.json` scaricato durante l'installazione di FCM nel progetto e impostare l'azione di compilazione su `GoogleServicesJson`.
1. [Configurare file AndroidManifest. XML per comunicare con Firebase](#configure-android-manifest).
1. [Eseguire l'override di FirebaseMessagingService per gestire i messaggi](#override-firebasemessagingservice-to-handle-messages).
1. [Aggiungere notifiche in ingresso all'interfaccia utente di Novell. Forms](#add-incoming-notifications-to-the-xamarinforms-ui).

> [!NOTE]
> L'azione di compilazione **GoogleServicesJson** fa parte del pacchetto NuGet **Novell. GooglePlayServices. base** . Visual Studio 2019 imposta le azioni di compilazione disponibili durante l'avvio. Se **GoogleServicesJson** non viene visualizzato come azione di compilazione, riavviare Visual Studio 2019 dopo l'installazione dei pacchetti NuGet.

### <a name="configure-android-manifest"></a>Configurare il manifesto Android

Gli elementi `receiver` all'interno dell'elemento `application` consentono all'app di comunicare con Firebase. Gli elementi `uses-permission` consentono all'app di gestire i messaggi e registrarli con l'hub di notifica di Azure. Il **file file AndroidManifest. XML** completo dovrebbe essere simile all'esempio seguente:

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

### <a name="override-firebasemessagingservice-to-handle-messages"></a>Eseguire l'override di FirebaseMessagingService per gestire i messaggi

Per eseguire la registrazione con Firebase e gestire i messaggi, sottoclassare la classe `FirebaseMessagingService`. Nell'applicazione di esempio viene definita una classe `FirebaseService` che sottoclassa la `FirebaseMessagingService`. Questa classe è contrassegnata con un attributo `IntentFilter`, che include il filtro `com.google.firebase.MESSAGING_EVENT`. Questo filtro consente a Android di passare i messaggi in ingresso a questa classe per la gestione:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseService : FirebaseMessagingService
{
    // ...
}

```

All'avvio dell'applicazione, Firebase SDK richiederà automaticamente un identificatore univoco del token dal server Firebase. Al completamento della richiesta, il metodo `OnNewToken` verrà chiamato sulla classe `FirebaseService`. Il progetto di esempio esegue l'override di questo metodo e registra il token con hub di notifica di Azure:

```csharp
public override void OnNewToken(string token)
{
    // NOTE: save token instance locally, or log if desired

    SendRegistrationToServer(token);
}

void SendRegistrationToServer(string token)
{
    try
    {
        NotificationHub hub = new NotificationHub(AppConstants.NotificationHubName, AppConstants.ListenConnectionString, this);

        // register device with Azure Notification Hub using the token from FCM
        Registration registration = hub.Register(token, AppConstants.SubscriptionTags);

        // subscribe to the SubscriptionTags list with a simple template.
        string pnsHandle = registration.PNSHandle;
        TemplateRegistration templateReg = hub.RegisterTemplate(pnsHandle, "defaultTemplate", AppConstants.FCMTemplateBody, AppConstants.SubscriptionTags);
    }
    catch (Exception e)
    {
        Log.Error(AppConstants.DebugTag, $"Error registering device: {e.Message}");
    }
}
```

Il metodo `SendRegistrationToServer` registra il dispositivo con l'hub di notifica di Azure e sottoscrive i tag con un modello. L'applicazione di esempio definisce un singolo tag denominato `default` e un modello con un solo parametro denominato `messageParam` nel file **appconstants.cs** . Per altre informazioni su registrazione, tag e modelli, vedere [registrare modelli e tag con l'hub di notifica di Azure](#register-templates-and-tags-with-the-azure-notification-hub).

Quando viene ricevuto un messaggio, viene chiamato il metodo `OnMessageReceived` sulla classe `FirebaseService`:

```csharp
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
    
    //Unique request code to avoid PendingIntent collision.
    var requestCode = new Random().Next();
    var pendingIntent = PendingIntent.GetActivity(this, requestCode, intent, PendingIntentFlags.OneShot);

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
```

I messaggi in arrivo vengono convertiti in una notifica locale con il metodo `SendLocalNotification`. Questo metodo crea un nuovo `Intent` e inserisce il contenuto del messaggio nel `Intent` come `Extra``string`. Quando l'utente tocca la notifica locale, se l'app è in primo piano o in background, l'`MainActivity` viene avviata e ha accesso al contenuto del messaggio tramite l'oggetto `Intent`.

Per la notifica locale e il `Intent` esempio è necessario che l'utente intraprenda l'azione di toccare la notifica. Questa operazione è utile quando l'utente deve intervenire prima che lo stato dell'applicazione venga modificato. Tuttavia, potrebbe essere necessario accedere ai dati del messaggio senza richiedere un'azione dell'utente in alcuni casi. Nell'esempio precedente il messaggio viene inoltre inviato direttamente all'istanza di `MainPage` corrente con il metodo `SendMessageToMainPage`. Nell'ambiente di produzione, se si implementano entrambi i metodi per un singolo tipo di messaggio, l'oggetto `MainPage` otterrà messaggi duplicati se l'utente tocca la notifica.

> [!NOTE]
> L'applicazione Android riceverà notifiche push solo se è in esecuzione in background o in primo piano. Per ricevere notifiche push quando il `Activity` principale non è in esecuzione, è necessario implementare un servizio che esula dall'ambito di questo esempio. Per altre informazioni, vedere [creazione di servizi Android](/xamarin/android/app-fundamentals/services/)

### <a name="add-incoming-notifications-to-the-xamarinforms-ui"></a>Aggiungere notifiche in ingresso all'interfaccia utente di Novell. Forms

La classe `MainActivity` deve ottenere l'autorizzazione per gestire le notifiche e gestire i dati del messaggio in arrivo. Il codice seguente illustra l'implementazione di `MainActivity` completa:

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

        if (!IsPlayServiceAvailable())
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

L'attributo `Activity` imposta l'`LaunchMode` dell'applicazione `SingleTop`. Questa modalità di avvio indica al sistema operativo Android di consentire solo una singola istanza di questa attività. Con questa modalità di avvio, i dati `Intent` in ingresso vengono indirizzati al metodo `OnNewIntent`, che estrae i dati del messaggio e li invia all'istanza di `MainPage` tramite il metodo `AddMessage`. Se l'applicazione usa una modalità di avvio diversa, deve gestire `Intent` dati in modo diverso.

Il metodo `OnCreate` usa un metodo helper denominato `IsPlayServiceAvailable` per garantire che il dispositivo supporti Google Play servizio. Gli emulatori o i dispositivi che non supportano Google Play servizio non possono ricevere notifiche push da Firebase.

## <a name="configure-ios-for-notifications"></a>Configurare iOS per le notifiche

Il processo per la configurazione dell'applicazione iOS per la ricezione delle notifiche è:

1. Configurare l' **identificatore del bundle** nel file **info. plist** in modo che corrisponda al valore usato nel profilo di provisioning.
1. Aggiungere l'opzione **Abilita notifiche push** al file **titles. plist** .
1. Aggiungere il pacchetto NuGet **Novell. Azure. NotificationHubs. iOS** al progetto.
1. Eseguire [la registrazione per le notifiche con APNs](#register-for-notifications-with-apns).
1. [Registrare l'applicazione con l'hub di notifica di Azure e sottoscrivere i tag](#register-with-azure-notification-hub-and-subscribe-to-tags).
1. [Aggiungere notifiche APNs all'interfaccia utente di Novell. Forms](#add-apns-notifications-to-xamarinforms-ui).

La schermata seguente mostra l'opzione **Abilita notifiche push** selezionata nel file **titles. plist** in Visual Studio:

![Screenshot del diritto delle notifiche push](azure-notification-hub-images/push-notification-entitlement.png "Diritto notifiche push")

### <a name="register-for-notifications-with-apns"></a>Eseguire la registrazione per le notifiche con APNS

È necessario eseguire l'override del metodo `FinishedLaunching` nel file **AppDelegate.cs** per eseguire la registrazione per le notifiche remote. La registrazione varia a seconda della versione di iOS usata nel dispositivo. Il progetto iOS nell'applicazione di esempio esegue l'override del metodo `FinishedLaunching` per chiamare `RegisterForRemoteNotifications`, come illustrato nell'esempio seguente:

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

### <a name="register-with-azure-notification-hub-and-subscribe-to-tags"></a>Eseguire la registrazione con l'hub di notifica di Azure e sottoscrivere i tag

Quando il dispositivo è stato registrato correttamente per le notifiche remote durante il `FinishedLaunching` metodo, iOS chiamerà il metodo `RegisteredForRemoteNotifications`. È necessario eseguire l'override di questo metodo per eseguire le azioni seguenti:

1. Creare un'istanza del `SBNotificationHub`.
1. Annulla la registrazione di tutte le registrazioni esistenti.
1. Registrare il dispositivo nell'hub di notifica.
1. Sottoscrivere tag specifici con un modello.

Per altre informazioni sulla registrazione del dispositivo, i modelli e i tag, vedere [registrare modelli e tag con l'hub di notifica di Azure](#register-templates-and-tags-with-the-azure-notification-hub). Il codice seguente illustra la registrazione del dispositivo e dei modelli:

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    Hub = new SBNotificationHub(AppConstants.ListenConnectionString, AppConstants.NotificationHubName);

    // update registration with Azure Notification Hub
    Hub.UnregisterAll(deviceToken, (error) =>
    {
        if (error != null)
        {
            Debug.WriteLine($"Unable to call unregister {error}");
            return;
        }

        var tags = new NSSet(AppConstants.SubscriptionTags.ToArray());
        Hub.RegisterNative(deviceToken, tags, (errorCallback) =>
        {
            if (errorCallback != null)
            {
                Debug.WriteLine($"RegisterNativeAsync error: {errorCallback}");
            }
        });

        var templateExpiration = DateTime.Now.AddDays(120).ToString(System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));
        Hub.RegisterTemplate(deviceToken, "defaultTemplate", AppConstants.APNTemplateBody, templateExpiration, tags, (errorCallback) =>
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
> La registrazione per le notifiche remote può non riuscire in situazioni come nessuna connessione di rete. È possibile scegliere di eseguire l'override del metodo `FailedToRegisterForRemoveNotifications` per gestire gli errori di registrazione.

### <a name="add-apns-notifications-to-xamarinforms-ui"></a>Aggiungere notifiche APNS all'interfaccia utente di Novell. Forms

Quando un dispositivo riceve una notifica remota, iOS chiama il metodo `ReceivedRemoteNotification`. Il codice JSON del messaggio in arrivo viene convertito in un oggetto `NSDictionary` e il metodo `ProcessNotification` estrae i valori dal dizionario e li invia all'istanza `MainPage` di Novell. Forms. Viene eseguito l'override del metodo `ReceivedRemoteNotifications` per chiamare `ProcessNotification`, come illustrato nel codice seguente:

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

## <a name="test-notifications-in-the-azure-portal"></a>Notifiche di test nella portale di Azure

Hub di notifica di Azure consente di verificare che l'applicazione possa ricevere messaggi di prova. La sezione **test Send** nell'hub di notifica consente di scegliere la piattaforma di destinazione e di inviare un messaggio. Se si imposta l' **espressione Invia a tag** su `default`, i messaggi vengono inviati alle applicazioni che hanno registrato un modello per il tag di `default`. Facendo clic sul pulsante **Invia** viene generato un report che include il numero di dispositivi raggiunti con il messaggio. La schermata seguente mostra un test di notifica Android nella portale di Azure:

![Screenshot di un messaggio di prova di hub di notifica di Azure](azure-notification-hub-images/azure-notification-hub-test-send.png "Messaggio di prova Hub di notifica di Azure")

### <a name="testing-tips"></a>Suggerimenti per il test

1. Quando si verifica che un'applicazione può ricevere notifiche push, è necessario usare un dispositivo fisico. I dispositivi virtuali Android e iOS potrebbero non essere configurati correttamente per ricevere notifiche push.
1. L'applicazione Android di esempio registra il token e i modelli una volta quando viene emesso il token Firebase. Durante i test potrebbe essere necessario richiedere un nuovo token e ripetere la registrazione con l'hub di notifica di Azure. Il modo migliore per forzare questo problema consiste nel pulire il progetto, eliminare le cartelle `bin` e `obj` e disinstallare l'applicazione dal dispositivo prima di ricompilarla e distribuirla.
1. Molte parti del flusso di notifiche push vengono eseguite in modo asincrono. Ciò può comportare un mancato raggiungimento di punti di interruzione o il raggiungimento di un ordine imprevisto. Usare la registrazione del dispositivo o del debug per tracciare l'esecuzione senza interrompere il flusso dell'applicazione. Filtrare il log del dispositivo Android usando il `DebugTag` specificato in `Constants`.
1. Quando il debug viene arrestato in Visual Studio, l'app viene forzata. Tutti i ricevitori di messaggi o altri servizi avviati come parte del processo di debug verranno chiusi e non risponderanno agli eventi del messaggio.

## <a name="create-a-notification-dispatcher"></a>Creazione di un dispatcher di notifiche

Hub di notifica di Azure consente all'applicazione back-end di inviare notifiche a dispositivi su più piattaforme. Nell'esempio viene illustrata la distribuzione delle notifiche con l'applicazione console **NotificationDispatcher** . L'applicazione include il file **DispatcherConstants.cs** , che definisce le proprietà seguenti:

```csharp
public static class DispatcherConstants
{
    public static string[] SubscriptionTags { get; set; } = { "default" };
    public static string NotificationHubName { get; set; } = "< Insert your Azure Notification Hub name >";
    public static string FullAccessConnectionString { get; set; } = "< Insert your DefaultFullSharedAccessSignature >";
}
```

È necessario configurare il **DispatcherConstants.cs** in modo che corrisponda alla configurazione dell'hub di notifica di Azure. Il valore della proprietà `SubscriptionTags` deve corrispondere ai valori utilizzati nelle app client. La proprietà `NotificationHubName` è il nome dell'istanza di hub di notifica di Azure. La proprietà `FullAccessConnectionString` è la chiave di accesso presente nei criteri di **accesso**dell'hub di notifica. Lo screenshot seguente mostra la posizione delle proprietà `NotificationHubName` e `FullAccessConnectionString` nel portale di Azure:

![Screenshot del nome dell'hub di notifica di Azure e FullAccessConnectionString](azure-notification-hub-images/notification-hub-full-access-policy.png "Nome Hub di notifica di Azure e FullAccessConnectionString")

L'applicazione console esegue il ciclo di ogni valore `SubscriptionTags` e invia notifiche ai Sottoscrittori utilizzando un'istanza della classe `NotificationHubClient`. Il codice seguente illustra la classe di `Program` dell'applicazione console:

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

Quando viene eseguita l'applicazione console di esempio, è possibile premere la barra spaziatrice per inviare messaggi. I dispositivi che eseguono le applicazioni client dovrebbero ricevere notifiche numerate, purché siano configurate correttamente.

## <a name="related-links"></a>Collegamenti correlati

* [Modelli di notifiche push](/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).
* [Gestione della registrazione del dispositivo](/azure/notification-hubs/notification-hubs-push-notification-registration-management).
* [Espressioni di routing e tag](/azure/notification-hubs/notification-hubs-tags-segment-push-message).
* [Esercitazione su Hub di notifica di Azure per Novell. Android](/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm).
* [Esercitazione su Hub di notifica di Azure per Novell. iOS](/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started).
