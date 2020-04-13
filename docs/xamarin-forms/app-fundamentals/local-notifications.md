---
title: Xamarin.Forms notifiche locali
description: In questo articolo viene illustrato come inviare e ricevere notifiche locali in Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/10/2019
ms.openlocfilehash: ef2ef004378212fac593179d7aa38b3688fa82c3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "72371571"
---
# <a name="local-notifications-in-xamarinforms"></a>Notifiche locali in Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

Le notifiche locali sono avvisi inviati dalle applicazioni installate su un dispositivo mobile. Le notifiche locali vengono spesso utilizzate per funzionalità quali:

- Eventi del calendario
- Reminders
- Trigger basati sulla posizione

Ogni piattaforma gestisce la creazione, la visualizzazione e l'utilizzo delle notifiche locali in modo diverso. In questo articolo viene illustrato come creare un'astrazione multipiattaforma per inviare e ricevere notifiche locali con Xamarin.Forms.This article explains how to create a cross-platform abstraction to send and receive local notifications with Xamarin.Forms.

[![Applicazione di notifiche locali su iOS e Android](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>Creare un'interfaccia multipiattaformaCreate a cross-platform interface

L'applicazione Xamarin.Forms deve creare e utilizzare le notifiche senza preoccuparsi per le implementazioni della piattaforma sottostante. L'interfaccia seguente `INotificationManager` viene implementata nella libreria di codice condiviso e definisce un'API multipiattaforma che l'applicazione può utilizzare per interagire con le notifiche:The following interface is implemented in the shared code library, and defines a cross-platform API that the application can use to interact with notifications:

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

Questa interfaccia verrà implementata in ogni progetto di piattaforma. L'evento `NotificationReceived` consente all'applicazione di gestire le notifiche in ingresso. Il `Initialize` metodo deve eseguire qualsiasi logica di piattaforma nativa necessaria per preparare il sistema di notifica. Il `ScheduleNotification` metodo deve inviare una notifica. Il `ReceiveNotification` metodo deve essere chiamato dalla piattaforma sottostante quando viene ricevuto un messaggio.

## <a name="consume-the-interface-in-xamarinforms"></a>Consumare l'interfaccia in Xamarin.FormsConsume the interface in Xamarin.Forms

Una volta creata, un'interfaccia può essere utilizzata nel progetto Xamarin.Forms condiviso anche se le implementazioni della piattaforma non sono ancora state create. L'applicazione di `ContentPage` esempio contiene un file MainPage.xaml denominato con il contenuto seguente:The sample application contains a called **MainPage.xaml** with the following content:

```xaml
<StackLayout Margin="0,35,0,0"
             x:Name="stackLayout">
    <Label Text="Click the button to create a local notification."
           TextColor="Red"
           HorizontalOptions="Center"
           VerticalOptions="Start" />
    <Button Text="Create Notification"
            HorizontalOptions="Center"
            VerticalOptions="Start"
            Clicked="OnScheduleClick"/>
</StackLayout>
```

Il layout `Label` contiene un elemento con `Button` le istruzioni per l'utente e un che deve pianificare una notifica quando viene toccato.

Il `MainPage` code-behind della classe gestisce l'invio e la ricezione di notifiche:The class code-behind handles the sending and receiving of notifications:

```csharp
public partial class MainPage : ContentPage
{
    INotificationManager notificationManager;
    int notificationNumber = 0;

    public MainPage()
    {
        InitializeComponent();

        notificationManager = DependencyService.Get<INotificationManager>();
        notificationManager.NotificationReceived += (sender, eventArgs) =>
        {
            var evtData = (NotificationEventArgs)eventArgs;
            ShowNotification(evtData.Title, evtData.Message);
        };
    }

    void OnScheduleClick(object sender, EventArgs e)
    {
        notificationNumber++;
        string title = $"Local Notification #{notificationNumber}";
        string message = $"You have now received {notificationNumber} notifications!";
        notificationManager.ScheduleNotification(title, message);
    }

    void ShowNotification(string title, string message)
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            var msg = new Label()
            {
                Text = $"Notification Received:\nTitle: {title}\nMessage: {message}"
            };
            stackLayout.Children.Add(msg);
        });
    }
}
```

Il `MainPage` costruttore della classe utilizza `DependencyService` Xamarin.Forms per `INotificationManager`recuperare un'istanza specifica della piattaforma dell'oggetto . Il `OnScheduleClicked` metodo `INotificationManager` utilizza l'istanza per pianificare una nuova notifica. Il `ShowNotification` metodo viene chiamato dal gestore `NotificationReceived` eventi associato all'evento e inserirà un nuovo `Label` nella pagina quando viene richiamato l'evento.

Per ulteriori informazioni su Xamarin.Forms `DependencyService`, vedere [Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md).

## <a name="create-the-android-interface-implementation"></a>Creare l'implementazione dell'interfaccia AndroidCreate the Android interface implementation

Per l'applicazione Xamarin.Forms per inviare e ricevere notifiche su `INotificationManager` Android, l'applicazione deve fornire un'implementazione dell'interfaccia.

### <a name="create-the-androidnotificationmanager-class"></a>Creare la classe AndroidNotificationManagerCreate the AndroidNotificationManager class

La `AndroidNotificationManager` classe `INotificationManager` implementa l'interfaccia :The class implements the interface:

```csharp
using Android.Support.V4.App;
using Xamarin.Forms;
using AndroidApp = Android.App.Application;

[assembly: Dependency(typeof(LocalNotifications.Droid.AndroidNotificationManager))]
namespace LocalNotifications.Droid
{
    public class AndroidNotificationManager : INotificationManager
    {
        const string channelId = "default";
        const string channelName = "Default";
        const string channelDescription = "The default channel for notifications.";
        const int pendingIntentId = 0;

        public const string TitleKey = "title";
        public const string MessageKey = "message";

        bool channelInitialized = false;
        int messageId = -1;
        NotificationManager manager;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            CreateNotificationChannel();
        }

        public int ScheduleNotification(string title, string message)
        {
            if (!channelInitialized)
            {
                CreateNotificationChannel();
            }

            messageId++;

            Intent intent = new Intent(AndroidApp.Context, typeof(MainActivity));
            intent.PutExtra(TitleKey, title);
            intent.PutExtra(MessageKey, message);

            PendingIntent pendingIntent = PendingIntent.GetActivity(AndroidApp.Context, pendingIntentId, intent, PendingIntentFlags.OneShot);

            NotificationCompat.Builder builder = new NotificationCompat.Builder(AndroidApp.Context, channelId)
                .SetContentIntent(pendingIntent)
                .SetContentTitle(title)
                .SetContentText(message)
                .SetLargeIcon(BitmapFactory.DecodeResource(AndroidApp.Context.Resources, Resource.Drawable.xamagonBlue))
                .SetSmallIcon(Resource.Drawable.xamagonBlue)
                .SetDefaults((int)NotificationDefaults.Sound | (int)NotificationDefaults.Vibrate);

            var notification = builder.Build();
            manager.Notify(messageId, notification);

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message,
            };
            NotificationReceived?.Invoke(null, args);
        }

        void CreateNotificationChannel()
        {
            manager = (NotificationManager)AndroidApp.Context.GetSystemService(AndroidApp.NotificationService);

            if (Build.VERSION.SdkInt >= BuildVersionCodes.O)
            {
                var channelNameJava = new Java.Lang.String(channelName);
                var channel = new NotificationChannel(channelId, channelNameJava, NotificationImportance.Default)
                {
                    Description = channelDescription
                };
                manager.CreateNotificationChannel(channel);
            }

            channelInitialized = true;
        }
    }
}
```

L'attributo `assembly` sopra lo `INotificationManager` spazio dei `DependencyService`nomi registra l'implementazione dell'interfaccia con l'oggetto .

Android consente alle applicazioni di definire più canali per le notifiche. Il `Initialize` metodo crea un canale di base utilizzato dall'applicazione di esempio per inviare notifiche. Il `ScheduleNotification` metodo definisce la logica specifica della piattaforma necessaria per creare e inviare una notifica. Infine, `ReceiveNotification` il metodo viene chiamato dal sistema operativo Android quando viene ricevuto un messaggio e richiama il gestore eventi.

> [!NOTE]
> La `Application` classe è definita `Xamarin.Forms` `Android.App` in entrambi `AndroidApp` gli spazi dei `using` nomi e in modo che l'alias sia definito nelle istruzioni per differenziare i due.

### <a name="handle-incoming-notifications-on-android"></a>Gestire le notifiche in arrivo su Android

La `MainActivity` classe deve rilevare `AndroidNotificationManager` le notifiche in ingresso e inviare una notifica all'istanza. `Activity` L'attributo `MainActivity` nella classe `LaunchMode` deve `LaunchMode.SingleTop`specificare un valore di :

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

La `SingleTop` modalità impedisce `Activity` l'avvio di più istanze di un oggetto mentre l'applicazione è in primo piano. Ciò `LaunchMode` potrebbe non essere appropriato per le applicazioni che avviano più attività in scenari di notifica più complessi. Per ulteriori `LaunchMode` informazioni sui valori di enumerazione, vedere [Android Activity LaunchMode](https://developer.android.com/guide/topics/manifest/activity-element#lmode).

Nella `MainActivity` classe viene modificato per ricevere le notifiche in arrivo:In the class is modified to receive incoming notifications:

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    // ...

    global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
    LoadApplication(new App());
    CreateNotificationFromIntent(Intent);
}

protected override void OnNewIntent(Intent intent)
{
    CreateNotificationFromIntent(intent);
}

void CreateNotificationFromIntent(Intent intent)
{
    if (intent?.Extras != null)
    {
        string title = intent.Extras.GetString(AndroidNotificationManager.TitleKey);
        string message = intent.Extras.GetString(AndroidNotificationManager.MessageKey);
        DependencyService.Get<INotificationManager>().ReceiveNotification(title, message);
    }
}
```

Il `CreateNotificationFromIntent` metodo estrae `intent` i dati di `AndroidNotificationManager` notifica `ReceiveNotification` dall'argomento e li fornisce all'oggetto using. Il `CreateNotificationFromIntent` metodo viene chiamato `OnCreate` sia `OnNewIntent` dal metodo che dal metodo :

- Quando l'applicazione viene avviata `Intent` dai dati di `OnCreate` notifica, i dati verranno passati al metodo.
- Se l'applicazione è già `Intent` in primo piano, `OnNewIntent` i dati verranno passati al metodo.

Android offre molte opzioni avanzate per le notifiche. Per ulteriori informazioni, vedere [Notifiche in Xamarin.Android](~/android/app-fundamentals/notifications/index.md).

## <a name="create-the-ios-interface-implementation"></a>Creare l'implementazione dell'interfaccia iOSCreate the iOS interface implementation

Affinché l'applicazione Xamarin.Forms invii e riceva notifiche su iOS, l'applicazione deve fornire un'implementazione del `INotificationManager`file .

### <a name="create-the-iosnotificationmanager-class"></a>Creare la classe iOSNotificationManagerCreate the iOSNotificationManager class

La `iOSNotificationManager` classe `INotificationManager` implementa l'interfaccia :The class implements the interface:

```csharp
[assembly: Dependency(typeof(LocalNotifications.iOS.iOSNotificationManager))]
namespace LocalNotifications.iOS
{
    public class iOSNotificationManager : INotificationManager
    {
        int messageId = -1;

        bool hasNotificationsPermission;

        public event EventHandler NotificationReceived;

        public void Initialize()
        {
            // request the permission to use local notifications
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert, (approved, err) =>
            {
                hasNotificationsPermission = approved;
            });
        }

        public int ScheduleNotification(string title, string message)
        {
            // EARLY OUT: app doesn't have permissions
            if(!hasNotificationsPermission)
            {
                return -1;
            }

            messageId++;

            var content = new UNMutableNotificationContent()
            {
                Title = title,
                Subtitle = "",
                Body = message,
                Badge = 1
            };

            // Local notifications can be time or location based
            // Create a time-based trigger, interval is in seconds and must be greater than 0
            var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger(0.25, false);

            var request = UNNotificationRequest.FromIdentifier(messageId.ToString(), content, trigger);
            UNUserNotificationCenter.Current.AddNotificationRequest(request, (err) =>
            {
                if (err != null)
                {
                    throw new Exception($"Failed to schedule notification: {err}");
                }
            });

            return messageId;
        }

        public void ReceiveNotification(string title, string message)
        {
            var args = new NotificationEventArgs()
            {
                Title = title,
                Message = message
            };
            NotificationReceived?.Invoke(null, args);
        }
    }
}
```

L'attributo `assembly` sopra lo `INotificationManager` spazio dei `DependencyService`nomi registra l'implementazione dell'interfaccia con l'oggetto .

In iOS, è necessario richiedere l'autorizzazione per utilizzare le notifiche prima di tentare di pianificare una notifica. Il `Initialize` metodo richiede l'autorizzazione per l'utilizzo delle notifiche locali. Il `ScheduleNotification` metodo definisce la logica necessaria per creare e inviare una notifica. Infine, `ReceiveNotification` il metodo verrà chiamato da iOS quando viene ricevuto un messaggio e richiama il gestore eventi.

### <a name="handle-incoming-notifications-on-ios"></a>Gestire le notifiche in arrivo su iOS

In iOS, è necessario creare `UNUserNotificationCenterDelegate` un delegato che sottoclassi per gestire i messaggi in arrivo. L'applicazione di `iOSNotificationReceiver` esempio definisce una classe:The sample application defines an class:

```csharp
public class iOSNotificationReceiver : UNUserNotificationCenterDelegate
{
    public override void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
    {
        DependencyService.Get<INotificationManager>().ReceiveNotification(notification.Request.Content.Title, notification.Request.Content.Body);

        // alerts are always shown for demonstration but this can be set to "None"
        // to avoid showing alerts if the app is in the foreground
        completionHandler(UNNotificationPresentationOptions.Alert);
    }
}
```

Questa classe `DependencyService` utilizza il per `iOSNotificationManager` ottenere un'istanza della `ReceiveNotification` classe e fornisce i dati di notifica in ingresso al metodo.

La `AppDelegate` classe deve specificare il delegato personalizzato durante l'avvio dell'applicazione. La `AppDelegate` classe deve `iOSNotificationReceiver` specificare `UNUserNotificationCenter` un oggetto come delegato durante l'avvio dell'applicazione. Ciò si `FinishedLaunching` verifica nel metodo:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

iOS offre molte opzioni avanzate per le notifiche. Per ulteriori informazioni, vedere [Notifiche in Xamarin.iOS](~/ios/platform/user-notifications/index.md).

## <a name="test-the-application"></a>Test dell'applicazione

Una volta che i progetti `INotificationManager` di piattaforma contengono un'implementazione registrata dell'interfaccia, l'applicazione può essere testata su entrambe le piattaforme. Eseguire l'applicazione e fare clic sul pulsante **Pianifica notifica** per creare una notifica.

Su Android, la notifica verrà visualizzata nell'area di notifica. Quando viene intercettata la notifica, l'applicazione riceve la notifica e visualizza un messaggio sotto il pulsante **Pianifica notifica:**

![Notifiche locali su Android](local-notifications-images/local-notifications-android.png)

In iOS, le notifiche in arrivo vengono ricevute automaticamente dall'applicazione senza richiedere l'input dell'utente. L'applicazione riceve la notifica e visualizza un messaggio sotto il pulsante **Pianifica notifica:**

![Notifiche locali su iOSLocal notifications on iOS](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>Collegamenti correlati

- [Progetto di esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Notifiche in Xamarin.Android](~/android/app-fundamentals/notifications/index.md)
- [Notifiche in Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [Xamarin.Forms Dependency.Service](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
