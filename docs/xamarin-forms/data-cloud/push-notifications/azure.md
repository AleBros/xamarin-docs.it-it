---
title: Invio di notifiche Push da App per dispositivi mobili di Azure
description: "Hub di notifica di Azure forniscono un'infrastruttura push scalabile per l'invio di notifiche push da qualsiasi back-end per qualsiasi piattaforma mobile, eliminando la complessità di un back-end la necessità di comunicare con sistemi di notifica tramite piattaforma diversa. In questo articolo viene illustrato come utilizzare gli hub di notifica di Azure per inviare notifiche push da un'istanza di App mobili di Azure a un'applicazione di xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: A1EF400F-73F4-43E9-A0C3-1569A0F34A3B
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: f0f767179a9280d7a6c6d7ce8125696d5e664cba
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="sending-push-notifications-from-azure-mobile-apps"></a>Invio di notifiche Push da App per dispositivi mobili di Azure

_Hub di notifica di Azure forniscono un'infrastruttura push scalabile per l'invio di notifiche push da qualsiasi back-end per qualsiasi piattaforma mobile, eliminando la complessità di un back-end la necessità di comunicare con sistemi di notifica tramite piattaforma diversa. In questo articolo viene illustrato come utilizzare gli hub di notifica di Azure per inviare notifiche push da un'istanza di App mobili di Azure a un'applicazione di xamarin. Forms._

> [!VIDEO https://youtube.com/embed/le2lDY22xwM]

**Azure Push da Hub di notifica e xamarin. Forms, [University Xamarin](https://university.xamarin.com/)**

Una notifica push viene utilizzata per inviare le informazioni, ad esempio, un messaggio da un sistema back-end in un'applicazione in un dispositivo mobile per aumentare l'utilizzo e il coinvolgimento dell'applicazione. Può essere inviata la notifica in qualsiasi momento, anche quando l'utente non usa attivamente l'applicazione di destinazione.

I sistemi back-end di inviare notifiche push ai dispositivi mobili tramite piattaforma notifica sistemi PNS (), come illustrato nel diagramma seguente:

[![](azure-images/pns.png "Sistemi di notifica tramite piattaforma")](azure-images/pns-large.png#lightbox "sistemi di notifica tramite piattaforma")

Per inviare una notifica push, il sistema back-end contatta il PNS specifici della piattaforma per inviare una notifica a un'istanza dell'applicazione client. Questo aumenta significativamente la complessità del back-end quando sono necessari, le notifiche push multipiattaforma poiché il back-end deve usare ogni API PNS specifici della piattaforma e protocollo.

Hub di notifica di Azure per eliminare questa complessità, astraendo i dettagli dei sistemi di notifica piattaforma diversa, consentendo una notifica multipiattaforma venga inviato con una singola chiamata API, come illustrato nel diagramma seguente:

[![](azure-images/notification-hub.png)](azure-images/notification-hub-large.png#lightbox)

Per inviare una notifica push, i contatti solo Azure Hub di notifica, che a sua volta comunica con i sistemi di notifica piattaforma diversa, riducendo pertanto la complessità del back-end del sistema di back-end del codice che invia le notifiche push.

Azure App per dispositivi mobili con un supporto predefinito per le notifiche push tramite hub di notifica. Il processo per l'invio di una notifica push da un'istanza di App mobili di Azure a un'applicazione di xamarin. Forms è come segue:

1. L'applicazione di xamarin. Forms registra con il PNS, che restituisce un handle.
1. L'istanza di App mobili di Azure invia una notifica per il proprio Hub di notifica di Azure, che specifica l'handle del dispositivo di destinazione.
1. L'Hub di notifica di Azure invia la notifica al pns appropriato per il dispositivo.
1. Il PNS invia la notifica al dispositivo specificato.
1. L'applicazione di xamarin. Forms elabora la notifica e lo visualizza.

L'applicazione di esempio viene illustrata un'applicazione di elenco di attività i cui dati vengono archiviati in un'istanza di App mobili di Azure. Ogni volta che un nuovo elemento viene aggiunto all'istanza di App mobili di Azure, viene inviata una notifica di push per l'applicazione di xamarin. Forms. La schermata seguente mostra ogni piattaforma per visualizzare la notifica push ricevuto:

[![](azure-images/screenshots.png "Esempio di applicazione che riceve una notifica Push")](azure-images/screenshots-large.png#lightbox "applicazione che riceve una notifica Push di esempio")

Per ulteriori informazioni sugli hub di notifica di Azure, vedere [gli hub di notifica di Azure](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/) e [aggiungere notifiche push all'app xamarin. Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push/).

## <a name="azure-and-platform-notification-system-setup"></a>Azure e configurazione del sistema di notifica piattaforma

Il processo per l'integrazione di un Hub di notifica di Azure in un'istanza di App mobili di Azure è come segue:

1. Creare un'istanza di App mobili di Azure. Per ulteriori informazioni, vedere [utilizzo di un'App Mobile Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Configurare un hub di notifica. Per ulteriori informazioni, vedere [configurare un hub di notifica](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#create-hub).
1. Aggiornare l'istanza di App mobili di Azure per inviare notifiche push. Per ulteriori informazioni, vedere [aggiornare il progetto server per inviare notifiche push](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#update-the-server-project-to-send-push-notifications).
1. Registrare con ogni PNS.
1. Configurare l'hub di notifica per comunicare con ogni PNS.

Le sezioni seguenti forniscono istruzioni di configurazione aggiuntive per ogni piattaforma.

### <a name="ios"></a>iOS

I passaggi aggiuntivi seguenti devono essere effettuati per utilizzare Apple Push Notification Service (APNS) da un Hub di notifica di Azure:

1. Generare un certificato di firma richiesta per il certificato di push con lo strumento di accesso portachiavi. Per ulteriori informazioni, vedere [generare il file di richiesta di firma certificato per il certificato di push](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#generate-the-certificate-signing-request-file-for-the-push-certificate) nel centro di documentazione di Azure.
1. Registrare l'applicazione di xamarin. Forms per il supporto di notifica push in Apple Developer Center. Per ulteriori informazioni, vedere [registrare l'app per le notifiche push](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-app-for-push-notifications) nel centro di documentazione di Azure.
1. Creare un push notifiche abilitato profilo di provisioning per l'applicazione di xamarin. Forms in Apple Developer Center. Per ulteriori informazioni, vedere [creare un profilo di provisioning per l'app](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#create-a-provisioning-profile-for-the-app) nel centro di documentazione di Azure.
1. Configurare l'hub di notifica per comunicare con il servizio APN. Per ulteriori informazioni, vedere [configurare l'hub di notifica per il servizio APN](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-apns).
1. Configurare l'applicazione di xamarin. Forms per utilizzare il nuovo ID di App e il profilo di provisioning. Per ulteriori informazioni, vedere [configurazione progetto iOS in Xamarin Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-xamarin-studio) o [configura il progetto iOS in Visual Studio](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configuring-the-ios-project-in-visual-studio) nel centro di documentazione di Azure.

### <a name="android"></a>Android

I passaggi aggiuntivi seguenti devono essere effettuati per utilizzare messaggistica Cloud Firebase (FCM) da un Hub di notifica di Azure:

1. Registrare per FCM. Una chiave API del Server e un ID Client vengono automaticamente generati e compressi un `google-services.json` file che viene scaricato. Per ulteriori informazioni, vedere [abilitare Firebase Cloud Messaging (FCM)](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#enable-firebase-cloud-messaging-fcm).
1. Configurare l'hub di notifica per comunicare con FCM. Per ulteriori informazioni, vedere [Configura terminare nuovamente l'App per dispositivi mobili per inviare richieste di push utilizzando FCM](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm).

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

I passaggi aggiuntivi seguenti devono essere effettuati per utilizzare il servizio di notifica di Windows (WNS) da un Hub di notifica di Azure:

1. Registrazione per il servizio di notifica di Windows (WNS). Per ulteriori informazioni, vedere [registrare l'app di Windows per le notifiche push con WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#register-your-windows-app-for-push-notifications-with-wns) nel centro di documentazione di Azure.
1. Configurare l'hub di notifica per comunicare con WNS. Per ulteriori informazioni, vedere [configurare l'hub di notifica per i servizi WNS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/#configure-the-notification-hub-for-wns) nel centro di documentazione di Azure.

## <a name="adding-push-notification-support-to-the-xamarinforms-application"></a>Aggiunta del supporto di notifica Push all'applicazione di xamarin. Forms

Le sezioni seguenti illustrano l'implementazione necessaria in ogni progetto specifico della piattaforma per supportare le notifiche push.

### <a name="ios"></a>iOS

Il processo per l'implementazione del supporto di notifica push in un'applicazione iOS è come segue:

1. Registrazione con Apple Push Notification Service (APNS) nei `AppDelegate.FinishedLaunching` metodo. Per ulteriori informazioni, vedere [registrazione con il sistema di notifica Push Apple](#ios_register).
1. Implementare il `AppDelegate.RegisteredForRemoteNotifications` metodo per gestire la risposta di registrazione. Per ulteriori informazioni, vedere [gestisce la risposta di registrazione](#ios_registration_response).
1. Implementare il `AppDelegate.DidReceiveRemoteNotification` metodo per elaborare le notifiche push in ingresso. Per ulteriori informazioni, vedere [l'elaborazione in ingresso le notifiche Push](#ios_process_incoming).

<a name="ios_register" />

### <a name="registering-with-the-apple-push-notification-service"></a>Registrazione con Apple Push Notification Service

Prima di un'applicazione iOS può ricevere notifiche push, deve eseguire la registrazione con Apple Push Notification Service (APNS), che genera un token univoco del dispositivo e restituirlo all'applicazione. Registrazione viene richiamata il `FinishedLaunching` esegue l'override nel `AppDelegate` classe:

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

Quando si registra un'applicazione iOS con il servizio APN deve specificare i tipi di notifiche push da ricevere. Il `RegisterUserNotificationSettings` metodo registra i tipi di notifiche, l'applicazione può ricevere, con la `RegisterForRemoteNotifications` metodo di registrazione per ricevere le notifiche push da APNS.

> [!NOTE]
> La mancata chiamata di `RegisterUserNotificationSettings` genererà notifiche push automaticamente ricevute dall'applicazione.

<a name="ios_registration_response" />

### <a name="handling-the-registration-response"></a>Gestisce la risposta di registrazione

La richiesta di registrazione del servizio APN avviene in background. Quando viene ricevuta la risposta, iOS chiamerà il `RegisteredForRemoteNotifications` esegue l'override nel `AppDelegate` classe:

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

Questo metodo crea un modello di messaggio di notifica semplice come JSON e registra il dispositivo per ricevere una notifica modello dall'hub di notifica.

> [!NOTE]
> Il `FailedToRegisterForRemoteNotifications` override deve essere implementato per gestire le situazioni, ad esempio alcuna connessione di rete. Questo è importante perché gli utenti potrebbero avviare l'applicazione durante offline.

<a name="ios_process_incoming" />

### <a name="processing-incoming-push-notifications"></a>Elaborazione delle notifiche Push in ingresso

Il `DidReceiveRemoteNotification` esegue l'override nel `AppDelegate` classe viene utilizzata per elaborare le notifiche push in arrivo quando l'applicazione è in esecuzione e viene richiamato quando viene ricevuta una notifica:

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

Il `userInfo` dizionario contiene la `aps` chiave, il cui valore è il `alert` dizionario con i dati rimanenti di notifica. Il dizionario viene recuperato, con la `string` messaggio di notifica visualizzato in una finestra di dialogo.

> [!NOTE]
> Se un'applicazione non è in esecuzione quando si riceve una notifica push, l'applicazione verrà avviata ma la `DidReceiveRemoteNotification` metodo non elabora la notifica. In alternativa, ottenere il payload di notifica e rispondere in modo appropriato dal `WillFinishLaunching` o `FinishedLaunching` esegue l'override.

Per ulteriori informazioni su APN, vedere [le notifiche Push in iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md).

### <a name="android"></a>Android

Il processo per l'implementazione del supporto di notifica push in un'applicazione Android è come segue:

1. Aggiungere il [Xamarin.Firebase.Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) NuGet pacchetto per il progetto Android e imposta la versione di destinazione dell'applicazione per Android 7.0 o versione successiva.
1. Aggiungere il `google-services.json` file scaricato dalla console di Firebase, alla radice del progetto Android e impostare l'azione di compilazione **GoogleServicesJson**. Per ulteriori informazioni, vedere [aggiungere il File JSON di Google servizi](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).
1. Register con Firebase Cloud Messaging (FCM) dichiarando un ricevitore nel manifesto Android file e di implementazione di `FirebaseRegistrationService.OnTokenRefresh` metodo. Per ulteriori informazioni, vedere [registrazione con la messaggistica Cloud Firebase](#android_register_fcm).
1. Registrare con l'Hub di notifica di Azure nel `AzureNotificationHubService.RegisterAsync` metodo. Per ulteriori informazioni, vedere [registrazione con l'Hub di notifica di Azure](#android_register_azure).
1. Implementare il `FirebaseNotificationService.OnMessageReceived` metodo per elaborare le notifiche push in ingresso. Per ulteriori informazioni, vedere [la visualizzazione del contenuto di una notifica Push](#android_displaying_notification).

Per ulteriori informazioni sulla messaggistica Cloud Firebase, vedere [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) e [notifiche remoto con la messaggistica Cloud Firebase](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

<a name="android_register_fcm" />

#### <a name="registering-with-firebase-cloud-messaging"></a>Registrazione con Firebase Cloud Messaging

Prima di un'applicazione può ricevere notifiche push, è necessario registrare con FCM, che genera un token di registrazione e restituirlo all'applicazione. Per ulteriori informazioni sui token di registrazione, vedere [registrazione con FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#registration).

A questo scopo:

- Dichiarazione di un ricevitore nel manifesto di Android. Per ulteriori informazioni, vedere [dichiarando il ricevitore in cui il file manifesto Android](#declaring_a_receiver).
- Implementazione del servizio di ID di istanza Firebase. Per ulteriori informazioni, vedere [che implementa il servizio di ID di istanza Firebase](#implementing-firebase-instance-id-service).

<a name="declaring_a_receiver" />

##### <a name="declaring-the-receiver-in-the-android-manifest"></a>Dichiarare il ricevitore nel manifesto Android

Modifica **AndroidManifest.xml** e inserire il seguente `<receiver>` gli elementi di `<application>` elemento:

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

- Dichiara un interno `FirebaseInstanceIdInternalReceiver` implementazione che viene utilizzata per avviare i servizi in modo sicuro.
- Dichiara un `FirebaseInstanceIdReceiver` implementazione che fornisce un identificatore univoco per ogni istanza di applicazione. Il ricevitore autentica e autorizza azioni.

Il `FirebaseInstanceIdReceiver` riceve `FirebaseInstanceId` e `FirebaseMessaging` gli eventi e li recapita alla classe che deriva da `FirebasesInstanceIdService`.

<a name="implementing-firebase-instance-id-service" />

##### <a name="implementing-the-firebase-instance-id-service"></a>Implementazione del servizio di ID di istanza Firebase

Registrazione dell'applicazione con FCM scopo è necessario derivare una classe dalla `FirebaseInstanceIdService` classe. Questa classe è responsabile della generazione di token di sicurezza che autorizza l'applicazione client per accedere FCM. Nell'applicazione di esempio di `FirebaseRegistrationService` deriva dalla classe di `FirebaseInstanceIdService` classe e viene mostrato nell'esempio di codice seguente:

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

Il `OnTokenRefresh` metodo viene richiamato quando l'applicazione riceve un token di registrazione da FCM. Il metodo recupera il token dal `FirebaseInstanceId.Instance.Token` proprietà, che viene aggiornato in modo asincrono dal FCM. Il `OnTokenRefresh` metodo raramente viene richiamato, perché il token viene aggiornato solo quando l'applicazione viene installata o disinstallata, quando l'utente elimina i dati dell'applicazione, quando l'applicazione Cancella l'ID istanza o quando la protezione del token è stato compromesso. Inoltre, l'ID istanza FCM verrà richiesta di servizio che l'applicazione aggiorna periodicamente, il relativo token in genere ogni 6 mesi.

Il `OnTokenRefresh` metodo richiama anche il `SendRegistrationTokenToAzureNotificationHub` metodo, che viene utilizzato per associare il token di registrazione dell'utente con l'Hub di notifica di Azure.

<a name="android_register_azure" />

#### <a name="registering-with-the-azure-notification-hub"></a>Registrazione con l'Hub di notifica di Azure

Il `AzureNotificationHubService` classe fornisce il `RegisterAsync` metodo che associa il token di registrazione dell'utente con l'Hub di notifica di Azure. Nell'esempio di codice riportato di seguito viene illustrato il `RegisterAsync` (metodo), che viene richiamato dalla `FirebaseRegistrationService` classe quando cambia il token di registrazione dell'utente:

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

Questo metodo crea un modello di messaggio di notifica semplice come JSON e registri per ricevere le notifiche di modello dall'hub di notifica, utilizzando il token di registrazione Firebase. In questo modo si garantisce che le notifiche inviate dall'Hub di notifica di Azure verranno rappresentato dal token di registrazione dispositivo di destinazione.

<a name="android_displaying_notification" />

#### <a name="displaying-the-contents-of-a-push-notification"></a>Visualizzazione del contenuto di una notifica Push

Per visualizzare il contenuto di una notifica push è necessario derivare una classe dalla `FirebaseMessagingService` classe. Questa classe include il sottoponibile a override `OnMessageReceived` fornito di metodo, che viene richiamato quando l'applicazione riceve una notifica da FCM, che l'applicazione è in esecuzione in primo piano. Nell'applicazione di esempio di `FirebaseNotificationService` deriva dalla classe di `FirebaseMessagingService` classe e viene illustrato nell'esempio di codice seguente:

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

Quando l'applicazione riceve una notifica da FCM, il `OnMessageReceived` metodo estrae il contenuto del messaggio e chiama il `SendNotification` metodo. Questo metodo converte il contenuto del messaggio in una notifica locale che viene avviata durante l'esecuzione dell'applicazione, con la notifica visualizzato nell'area di notifica.

##### <a name="handling-notification-intents"></a>La gestione dei tipi di notifica

Quando un utente digita una notifica, tutti i dati che accompagna il messaggio di notifica viene resa disponibili nel `Intent` extra. Questi dati possono essere estratti con il codice seguente:

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

Avvio dell'applicazione `Intent` viene generato quando l'utente tocca il messaggio di notifica, pertanto questo codice verrà registrati tutti i relativi dati di `Intent` nella finestra di output.

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Prima di Universal Windows Platform (UWP) l'applicazione può ricevere notifiche push che è necessario registrare con notifica di servizi Windows (WNS), che restituirà un canale di notifica. La registrazione viene richiamata dal `InitNotificationsAsync` metodo la `App` classe:

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

Questo metodo ottiene il canale di notifica push, crea un modello di messaggio di notifica come JSON e registra il dispositivo per ricevere una notifica modello dall'hub di notifica.

Il `InitNotificationsAsync` metodo viene richiamato dal `OnLaunched` esegue l'override nel `App` classe:

```csharp
protected override async void OnLaunched(LaunchActivatedEventArgs e)
{
    ...
    await InitNotificationsAsync();
}
```

Ciò garantisce che la registrazione della notifica push viene creata o aggiornata ogni volta che viene avviata l'applicazione, assicurando pertanto che il canale di push WNS è sempre attivo.

Quando viene ricevuta una notifica push che verrà automaticamente visualizzato come un *toast* : una finestra non modale che contiene il messaggio.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare gli hub di notifica di Azure per inviare notifiche push da un'istanza di App mobili di Azure a un'applicazione di xamarin. Forms. Hub di notifica di Azure forniscono un'infrastruttura push scalabile per l'invio di notifiche push da qualsiasi back-end per qualsiasi piattaforma mobile, eliminando la complessità di un back-end la necessità di comunicare con sistemi di notifica tramite piattaforma diversa.


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Hub di notifica di Azure](https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/)
- [Aggiunta delle notifiche push all'App xamarin. Forms](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-forms-get-started-push/)
- [Notifiche push in iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [TodoAzurePush (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzurePush/)
- [Client per dispositivi mobili di Azure SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
