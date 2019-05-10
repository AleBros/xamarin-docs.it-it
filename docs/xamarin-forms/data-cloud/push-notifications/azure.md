---
title: L'invio di notifiche Push dall'App per dispositivi mobili di Azure
description: Questo articolo illustra come usare hub di notifica per inviare notifiche push da un'istanza di App per dispositivi mobili di Azure a un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: A1EF400F-73F4-43E9-A0C3-1569A0F34A3B
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 2cfb15222c33309101366273d5bc9c42db68b436
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2019
ms.locfileid: "64978112"
---
# <a name="sending-push-notifications-from-azure-mobile-apps"></a>L'invio di notifiche Push dall'App per dispositivi mobili di Azure

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)

_Hub di notifica di Azure forniscono un'infrastruttura push scalabile per l'invio di notifiche push mobili da qualsiasi back-end per tutte le piattaforme per dispositivi mobili, eliminando la complessità di un back-end di comunicare con diversi platform notification System. Questo articolo illustra come usare hub di notifica per inviare notifiche push da un'istanza di App per dispositivi mobili di Azure a un'applicazione xamarin. Forms._

> [!VIDEO https://youtube.com/embed/le2lDY22xwM]

**Video di xamarin. Forms e Hub di notifica Push di Azure**

Consente di fornire informazioni, ad esempio un messaggio da un sistema back-end a un'applicazione in un dispositivo mobile per migliorare l'uso ed engagement applicazione una notifica push. La notifica può essere inviata come in qualsiasi momento, anche quando l'utente non usa attivamente all'applicazione di destinazione.

Sistemi back-end inviano notifiche push ai dispositivi mobili tramite i sistemi PNS (Platform Notification), come illustrato nel diagramma seguente:

[![](azure-images/pns.png "Platform Notification System")](azure-images/pns-large.png#lightbox "Platform Notification System")

Per inviare una notifica push, il sistema back-end contatta il PNS specifici della piattaforma per inviare una notifica a un'istanza di applicazione client. Ciò aumenta notevolmente la complessità del back-end quando le notifiche push multipiattaforma sono necessarie, poiché il back-end devono utilizzare ogni PNS API specifiche della piattaforma e protocollo.

Hub di notifica di Azure per eliminare questa complessità, astraendo i dettagli dei diversi platform notification System, consentendo una notifica di cross-platform venga inviato con una singola chiamata API, come illustrato nel diagramma seguente:

[![](azure-images/notification-hub.png)](azure-images/notification-hub-large.png#lightbox)

Per inviare una notifica push, contatti solo il sistema back-end Azure Hub di notifica, che a sua volta comunica con diversi platform notification System, riducendo pertanto la complessità del back-end del codice che invia le notifiche push.

Le App per dispositivi mobili di Azure includono supporto incorporato per le notifiche push tramite hub di notifica. Il processo per l'invio di una notifica push da un'istanza di App per dispositivi mobili di Azure a un'applicazione xamarin. Forms è come segue:

1. Il PNS, che restituisce un handle registrato dall'applicazione xamarin. Forms.
1. L'istanza di App per dispositivi mobili di Azure invia una notifica all'Hub di notifica di Azure, che specifica l'handle del dispositivo di destinazione.
1. L'Hub di notifica di Azure invia la notifica al PNS appropriato per il dispositivo.
1. Il PNS invia la notifica al dispositivo specificato.
1. L'applicazione xamarin. Forms elabora la notifica e lo visualizza.

L'applicazione di esempio viene illustrata un'applicazione di elenco todo con dati archiviati in un'istanza di App per dispositivi mobili di Azure. Ogni volta che un nuovo elemento viene aggiunto all'istanza di App per dispositivi mobili di Azure, viene inviata una notifica push all'applicazione xamarin. Forms. Le schermate seguenti illustrano ogni piattaforma di visualizzare la notifica push ricevuta:

[![](azure-images/screenshots.png "Esempio di applicazione che riceve una notifica Push")](azure-images/screenshots-large.png#lightbox "applicazione riceve una notifica Push di esempio")

Per altre informazioni sugli hub di notifica di Azure, vedere [hub di notifica](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/) e [aggiungere notifiche push all'App xamarin. Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push/).

## <a name="azure-and-platform-notification-system-setup"></a>Azure e il programma di installazione di Platform Notification System

Il processo per l'integrazione di un Hub di notifica di Azure in un'istanza di App per dispositivi mobili di Azure è come segue:

1. Creare un'istanza di App per dispositivi mobili di Azure. Per altre informazioni, vedere [utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Configurare un hub di notifica. Per altre informazioni, vedere [configurare un hub di notifica](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-hub).
1. Aggiornare l'istanza dell'App per dispositivi mobili di Azure per inviare notifiche push. Per altre informazioni, vedere [aggiornare il progetto server per l'invio di notifiche push](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#update-the-server-project-to-send-push-notifications).
1. Registrare con ogni PNS.
1. Configurare l'hub di notifica per comunicare con ogni PNS.

Le sezioni seguenti forniscono istruzioni di configurazione aggiuntive per ogni piattaforma.

### <a name="ios"></a>iOS

I passaggi aggiuntivi seguenti devono essere effettuati da usare Apple Push Notification Service (APNS) da un Hub di notifica di Azure:

1. Generare un certificato di firma richiesta per il certificato push con lo strumento Accesso Portachiavi. Per altre informazioni, vedere [generare il file di richiesta di firma certificato per il certificato push](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#generate-the-certificate-signing-request-file-for-the-push-certificate) nel Centro documentazione di Azure.
1. Registrare l'applicazione xamarin. Forms per supporto per le notifiche push nel Centro sviluppatori Apple. Per altre informazioni, vedere [registrare l'app per le notifiche push](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-app-for-push-notifications) nel Centro documentazione di Azure.
1. Creare un push delle notifiche abilitata profilo di provisioning per l'applicazione xamarin. Forms nel Centro sviluppatori Apple. Per altre informazioni, vedere [creare un profilo di provisioning per l'app](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#create-a-provisioning-profile-for-the-app) nel Centro documentazione di Azure.
1. Configurare l'hub di notifica per comunicare con il servizio APN. Per altre informazioni, vedere [configurare l'hub di notifica per APNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-apns).
1. Configurare l'applicazione xamarin. Forms per usare il nuovo ID App e un profilo di provisioning. Per altre informazioni, vedere [configurazione del progetto iOS in Xamarin Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-xamarin-studio) oppure [configurazione del progetto iOS in Visual Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-visual-studio) nel Centro documentazione di Azure.

### <a name="android"></a>Android

I passaggi aggiuntivi seguenti devono essere effettuati per usare Firebase Cloud Messaging (FCM) da un Hub di notifica di Azure:

1. Registrazione di FCM. Una chiave API del Server e un ID Client viene automaticamente generato e raggruppate in un `google-services.json` file che viene scaricato. Per altre informazioni, vedere [abilitare Firebase Cloud Messaging (FCM)](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#enable-firebase-cloud-messaging-fcm).
1. Configurare l'hub di notifica per comunicare con FCM. Per altre informazioni, vedere [Configura terminare nuovamente l'App per dispositivi mobili per inviare richieste push usando FCM](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm).

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

I passaggi aggiuntivi seguenti devono essere effettuati da usare il servizio di notifica Windows (WNS) da un Hub di notifica di Azure:

1. La registrazione per il servizio di notifica Windows (WNS). Per altre informazioni, vedere [registrare l'app di Windows per le notifiche push con WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-windows-app-for-push-notifications-with-wns) nel Centro documentazione di Azure.
1. Configurare l'hub di notifica per comunicare con WNS. Per altre informazioni, vedere [configurare l'hub di notifica per WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-wns) nel Centro documentazione di Azure.

## <a name="adding-push-notification-support-to-the-xamarinforms-application"></a>Aggiunta di supporto per le notifiche Push all'applicazione xamarin. Forms

Le sezioni seguenti illustrano l'implementazione necessario in ogni progetto specifico della piattaforma per supportare le notifiche push.

### <a name="ios"></a>iOS

Il processo per l'implementazione del supporto per le notifiche push in un'applicazione iOS è come segue:

1. Registrazione con Apple Push Notification Service (APNS) nel `AppDelegate.FinishedLaunching` (metodo). Per altre informazioni, vedere [la registrazione con Apple Push Notification System](#ios_register).
1. Implementare il `AppDelegate.RegisteredForRemoteNotifications` metodo per gestire la risposta di registrazione. Per altre informazioni, vedere [gestisce la risposta di registrazione](#ios_registration_response).
1. Implementare il `AppDelegate.DidReceiveRemoteNotification` metodo per elaborare le notifiche push in ingresso. Per altre informazioni, vedere [elaborazione di notifiche Push in ingresso](#ios_process_incoming).

<a name="ios_register" />

### <a name="registering-with-the-apple-push-notification-service"></a>La registrazione con Apple Push Notification Service

Prima di un'applicazione iOS può ricevere le notifiche push, è necessario registrare con Apple Push Notification Service (APNS), che genera un token del dispositivo univoco e restituirlo all'applicazione. La registrazione viene richiamata nel `FinishedLaunching` esegue l'override nel `AppDelegate` classe:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    ...
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert, new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ...
}
```

Quando si registra un'applicazione iOS con il servizio APN è necessario specificare i tipi di notifiche push da ricevere. Il `RegisterUserNotificationSettings` metodo registra i tipi di notifiche può ricevere l'applicazione, con la `RegisterForRemoteNotifications` metodo la registrazione per ricevere notifiche push dal servizio APN.

> [!NOTE]
> Impossibilità di richiamare il `RegisterUserNotificationSettings` metodo determinerà le notifiche push in modo invisibile ricevute dall'applicazione.

<a name="ios_registration_response" />

### <a name="handling-the-registration-response"></a>Gestisce la risposta di registrazione

La richiesta di registrazione APNS avviene in background. Quando viene ricevuta la risposta, iOS chiama il `RegisteredForRemoteNotifications` esegue l'override nel `AppDelegate` classe:

```csharp
public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
{
    const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyAPNS}
    };

    // Register for push with the Azure mobile app
    Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
    push.RegisterAsync(deviceToken, templates);
}
```

Questo metodo crea un modello di messaggio di notifica semplice come JSON e registra il dispositivo per la ricezione di notifiche modello dall'hub di notifica.

> [!NOTE]
> Il `FailedToRegisterForRemoteNotifications` override deve essere implementato per gestire le situazioni, ad esempio alcuna connessione di rete. Questo è importante perché gli utenti potrebbero avviare l'applicazione mentre offline.

<a name="ios_process_incoming" />

### <a name="processing-incoming-push-notifications"></a>Elaborazione delle notifiche Push in ingresso

Il `DidReceiveRemoteNotification` esegue l'override nel `AppDelegate` classe viene utilizzata per elaborare le notifiche push in arrivo quando l'applicazione è in esecuzione e viene richiamata quando viene ricevuta una notifica:

```csharp
public override void DidReceiveRemoteNotification(
    UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
    NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

    string alert = string.Empty;
    if (aps.ContainsKey(new NSString("alert")))
        alert = (aps[new NSString("alert")] as NSString).ToString();

    // Show alert
    if (!string.IsNullOrEmpty(alert))
    {
      var notificationAlert = UIAlertController.Create("Notification", alert, UIAlertControllerStyle.Alert);
      notificationAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Cancel, null));
      UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(notificationAlert, true, null);
    }
}
```

Il `userInfo` dizionario contiene il `aps` chiave, il cui valore è il `alert` dizionario con i rimanenti dati di notifica. Questo dizionario viene recuperato, con la `string` messaggio di notifica viene visualizzata in una finestra di dialogo.

> [!NOTE]
> Se un'applicazione non è in esecuzione quando si riceve una notifica push, l'applicazione verrà avviata ma la `DidReceiveRemoteNotification` (metodo) non elabora la notifica. In alternativa, ottenere il payload di notifica e rispondere in modo appropriato dal `WillFinishLaunching` o `FinishedLaunching` esegue l'override.

Per altre informazioni sul servizio APN, vedere [notifiche Push in iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md).

### <a name="android"></a>Android

Il processo per l'implementazione del supporto per le notifiche push in un'applicazione Android è come segue:

1. Aggiungere il [firebase](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet pacchetti al progetto Android e impostare la versione di destinazione dell'applicazione per Android 7.0 o versione successiva.
1. Aggiungere il `google-services.json` file, scaricato dalla console Firebase, alla radice del progetto Android e impostare la relativa azione di compilazione **GoogleServicesJson**. Per altre informazioni, vedere [aggiungere il File di Google Services JSON](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).
1. Registrazione con Firebase Cloud Messaging (FCM) con la dichiarazione di un ricevitore nel manifesto Android del file e l'implementazione per il `FirebaseRegistrationService.OnTokenRefresh` (metodo). Per altre informazioni, vedere [la registrazione con Firebase Cloud Messaging](#android_register_fcm).
1. Registrare con l'Hub di notifica di Azure nel `AzureNotificationHubService.RegisterAsync` (metodo). Per altre informazioni, vedere [registrazione nell'Hub di notifica di Azure di](#android_register_azure).
1. Implementare il `FirebaseNotificationService.OnMessageReceived` metodo per elaborare le notifiche push in ingresso. Per altre informazioni, vedere [visualizzazione del contenuto di una notifica Push](#android_displaying_notification).

Per altre informazioni su Firebase Cloud Messaging, vedere [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) e [notifiche Remote con Firebase Cloud Messaging](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

<a name="android_register_fcm" />

#### <a name="registering-with-firebase-cloud-messaging"></a>La registrazione con Firebase Cloud Messaging

Prima di un'applicazione Android è possibile ricevere notifiche push, è necessario registrare con FCM, generare un token di registrazione e restituirlo all'applicazione. Per altre informazioni sui token di registrazione, vedere [registrazione in FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#registration).

Questa operazione viene eseguita da:

- Dichiarazione di un ricevitore nel manifesto Android. Per altre informazioni, vedere [dichiarazione ricevitore nel manifesto Android](#declaring_a_receiver).
- Implementa il servizio di Firebase Instance ID. Per altre informazioni, vedere [che implementa il servizio di Firebase Instance ID](#implementing-firebase-instance-id-service).

<a name="declaring_a_receiver" />

##### <a name="declaring-the-receiver-in-the-android-manifest"></a>Dichiarare il ricevitore nel manifesto Android

Modificare **androidmanifest. XML** e inserire il codice seguente `<receiver>` gli elementi nel `<application>` elemento:

```xml
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
<receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
  <intent-filter>
    <action android:name="com.google.android.c2dm.intent.RECEIVE" />
    <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
    <category android:name="${applicationId}" />
  </intent-filter>
</receiver>
```

Questo codice XML esegue le operazioni seguenti:

- Dichiara un interno `FirebaseInstanceIdInternalReceiver` implementazione utilizzata per avviare i servizi in modo sicuro.
- Dichiara un `FirebaseInstanceIdReceiver` implementazione che fornisce un identificatore univoco per ogni istanza dell'app. In questo ricevitore autentica e autorizza le azioni.

Il `FirebaseInstanceIdReceiver` riceve `FirebaseInstanceId` e `FirebaseMessaging` eventi e li recapita alla classe che deriva da `FirebasesInstanceIdService`.

<a name="implementing-firebase-instance-id-service" />

##### <a name="implementing-the-firebase-instance-id-service"></a>Implementa il servizio Instance ID di Firebase

La registrazione dell'applicazione con FCM avviene derivando una classe dalla classe di `FirebaseInstanceIdService` classe. Questa classe è responsabile della generazione di token di sicurezza che autorizzano l'applicazione client per accedere a FCM. Nell'applicazione di esempio il `FirebaseRegistrationService` deriva dalla classe di `FirebaseInstanceIdService` classe e viene mostrato nell'esempio di codice seguente:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
public class FirebaseRegistrationService : FirebaseInstanceIdService
{
    const string TAG = "FirebaseRegistrationService";

    public override void OnTokenRefresh()
    {
        var refreshedToken = FirebaseInstanceId.Instance.Token;
        Log.Debug(TAG, "Refreshed token: " + refreshedToken);
        SendRegistrationTokenToAzureNotificationHub(refreshedToken);
    }

    void SendRegistrationTokenToAzureNotificationHub(string token)
    {
        // Update notification hub registration
        Task.Run(async () =>
        {
            await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
        });
    }
}
```

Il `OnTokenRefresh` metodo viene richiamato quando l'applicazione riceve un token di registrazione da FCM. Il metodo recupera il token dal `FirebaseInstanceId.Instance.Token` proprietà, che viene aggiornato in modo asincrono da FCM. Il `OnTokenRefresh` viene richiamato raramente (metodo), perché il token viene aggiornato solo quando l'applicazione viene installata o disinstallata, quando l'utente elimina i dati dell'applicazione, l'applicazione Cancella l'ID dell'istanza o quando è stata la sicurezza del token compromessi. Inoltre, il servizio Instance ID di FCM richiederà che l'applicazione aggiornamento periodico del token, in genere ogni 6 mesi.

Il `OnTokenRefresh` metodo richiama anche il `SendRegistrationTokenToAzureNotificationHub` (metodo), che consente di associare token di registrazione dell'utente con l'Hub di notifica di Azure.

<a name="android_register_azure" />

#### <a name="registering-with-the-azure-notification-hub"></a>La registrazione con Hub di notifica di Azure

Il `AzureNotificationHubService` classe fornisce il `RegisterAsync` metodo, che consente di associare token di registrazione dell'utente con l'Hub di notifica di Azure. Nell'esempio di codice riportato di seguito viene illustrato il `RegisterAsync` metodo, che viene richiamato dal `FirebaseRegistrationService` classe quando viene modificato il token di registrazione dell'utente:

```csharp
public class AzureNotificationHubService
{
    const string TAG = "AzureNotificationHubService";

    public static async Task RegisterAsync(Push push, string token)
    {
        try
        {
            const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBody}
            };

            await push.RegisterAsync(token, templates);
            Log.Info("Push Installation Id: ", push.InstallationId.ToString());
        }
        catch (Exception ex)
        {
            Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
        }
    }
}
```

Questo metodo crea un modello di messaggio di notifica semplice come JSON e lo registra per ricevere le notifiche modello dall'hub di notifica, utilizzando il token di registrazione Firebase. Ciò garantisce che le notifiche inviate dall'Hub di notifica di Azure saranno destinate al dispositivo rappresentato dal token di registrazione.

<a name="android_displaying_notification" />

#### <a name="displaying-the-contents-of-a-push-notification"></a>Visualizzazione del contenuto di una notifica Push

Per visualizzare il contenuto di una notifica push è derivando una classe dalla classe di `FirebaseMessagingService` classe. Questa classe include il sottoponibile a override `OnMessageReceived` fornito il metodo, che viene richiamato quando l'applicazione riceve una notifica da FCM, che l'applicazione sia in esecuzione in primo piano. Nell'applicazione di esempio il `FirebaseNotificationService` deriva dalla classe di `FirebaseMessagingService` classe e viene illustrato nell'esempio di codice seguente:

```csharp
[Service]
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
public class FirebaseNotificationService : FirebaseMessagingService
{
    const string TAG = "FirebaseNotificationService";

    public override void OnMessageReceived(RemoteMessage message)
    {
        Log.Debug(TAG, "From: " + message.From);

        // Pull message body out of the template
        var messageBody = message.Data["message"];
        if (string.IsNullOrWhiteSpace(messageBody))
            return;

        Log.Debug(TAG, "Notification message body: " + messageBody);
        SendNotification(messageBody);
    }

    void SendNotification(string messageBody)
    {
        var intent = new Intent(this, typeof(MainActivity));
        intent.AddFlags(ActivityFlags.ClearTop);
        var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

        var notificationBuilder = new NotificationCompat.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
            .SetContentTitle("New Todo Item")
            .SetContentText(messageBody)
            .SetContentIntent(pendingIntent)
            .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
            .SetAutoCancel(true);

        var notificationManager = NotificationManager.FromContext(this);
        notificationManager.Notify(0, notificationBuilder.Build());
    }
}
```

Quando l'applicazione riceve una notifica da FCM, il `OnMessageReceived` metodo estrae il contenuto del messaggio e chiama il `SendNotification` (metodo). Questo metodo converte il contenuto del messaggio in una notifica locale che viene avviata durante l'applicazione è in esecuzione, visualizzata nell'area di notifica.

##### <a name="handling-notification-intents"></a>Gestione delle notifiche Intent

Quando un utente tocca una notifica, tutti i dati che accompagna il messaggio di notifica viene resa disponibili nel `Intent` funzionalità aggiuntive. Questi dati possono essere estratti con il codice seguente:

```csharp
if (Intent.Extras != null)
{
  foreach (var key in Intent.Extras.KeySet())
  {
    var value = Intent.Extras.GetString(key);
    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
  }
}
```

Utilità di avvio dell'applicazione `Intent` viene generato quando l'utente tocca il messaggio di notifica, pertanto questo codice verrà registrati tutti i relativi dati di `Intent` nella finestra di output.

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Prima di una piattaforma di Windows universale (UWP) l'applicazione può ricevere le notifiche push che deve eseguire la registrazione con il Windows Notification Service (WNS), che restituisce un canale di notifica. La registrazione viene richiamata dal `InitNotificationsAsync` metodo nel `App` classe:

```csharp
private async Task InitNotificationsAsync()
{
    var channel = await PushNotificationChannelManager
          .CreatePushNotificationChannelForApplicationAsync();

    const string templateBodyWNS =
        "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

    JObject headers = new JObject();
    headers["X-WNS-Type"] = "wns/toast";

    JObject templates = new JObject();
    templates["genericMessage"] = new JObject
    {
        {"body", templateBodyWNS},
        {"headers", headers} // Needed for WNS.
    };

    await TodoItemManager.DefaultManager.CurrentClient.GetPush()
          .RegisterAsync(channel.Uri, templates);
}
```

Questo metodo ottiene il canale di notifica push, viene creato un modello di messaggio di notifica come JSON e registra il dispositivo per la ricezione di notifiche modello dall'hub di notifica.

Il `InitNotificationsAsync` metodo viene richiamato dal `OnLaunched` esegue l'override nel `App` classe:

```csharp
protected override async void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    await InitNotificationsAsync();
}
```

Ciò garantisce che la registrazione della notifica push viene creata o aggiornata ogni volta che viene avviata l'applicazione, assicurando pertanto che il canale di push WNS sia sempre attivo.

Quando viene ricevuta una notifica push che verrà automaticamente visualizzato come un *toast* : una finestra non modale, che contiene il messaggio.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come usare hub di notifica di Azure per inviare notifiche push da un'istanza di App per dispositivi mobili di Azure a un'applicazione xamarin. Forms. Hub di notifica di Azure forniscono un'infrastruttura push scalabile per l'invio di notifiche push mobili da qualsiasi back-end per tutte le piattaforme per dispositivi mobili, eliminando la complessità di un back-end di comunicare con diversi platform notification System.


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Hub di notifica di Azure](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)
- [Aggiungere notifiche push all'app xamarin. Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/)
- [Notifiche push in iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [TodoAzurePush (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)
- [Azure Mobile Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
