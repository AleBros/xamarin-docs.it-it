---
title: Notifiche locali di Xamarin.Forms
description: Questo articolo illustra come inviare e ricevere notifiche locali in Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 60460F57-63C6-4916-BBB5-A870F1DF53D7
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/10/2019
ms.openlocfilehash: ef2ef004378212fac593179d7aa38b3688fa82c3
ms.sourcegitcommit: 49afc8c6484c465bf5c9e1d64a8c9122a0bcf506
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72371571"
---
# <a name="local-notifications-in-xamarinforms"></a>Notifiche locali in Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

Le notifiche locali sono avvisi inviati dalle applicazioni installate in un dispositivo mobile. Le notifiche locali vengono spesso usate per le funzionalità di, ad esempio:

- Eventi del calendario
- Promemoria
- Trigger basati sulla posizione

Ogni piattaforma gestisce la creazione, la visualizzazione e l'utilizzo di notifiche locali in modo diverso. Questo articolo illustra come creare un'astrazione multipiattaforma per inviare e ricevere notifiche locali con Xamarin.Forms.

[![applicazione notifiche locali in iOS e Android](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>Creare un'interfaccia multipiattaforma

L'applicazione Xamarin.Forms deve creare e utilizzare le notifiche senza alcuna preoccupazione per le implementazioni della piattaforma sottostanti. La `INotificationManager` interfaccia seguente viene implementata nella libreria di codice condivisa e definisce un'API multipiattaforma che l'applicazione può usare per interagire con le notifiche:

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

Questa interfaccia verrà implementata in ogni progetto di piattaforma. L'evento `NotificationReceived` consente all'applicazione di gestire le notifiche in ingresso. Il metodo `Initialize` deve eseguire qualsiasi logica della piattaforma nativa necessaria per preparare il sistema di notifica. Il metodo `ScheduleNotification` deve inviare una notifica. Quando viene ricevuto un messaggio, il metodo di `ReceiveNotification` deve essere chiamato dalla piattaforma sottostante.

## <a name="consume-the-interface-in-xamarinforms"></a>Utilizzare l'interfaccia in Xamarin.Forms

Una volta creata, un'interfaccia può essere utilizzata nel progetto Xamarin.Forms condiviso anche se le implementazioni della piattaforma non sono ancora state create. L'applicazione di esempio contiene un `ContentPage` denominato **MainPage. XAML** con il contenuto seguente:

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

Il layout contiene un elemento `Label` con le istruzioni per l'utente e un `Button` che devono pianificare una notifica quando vengono toccati.

Il code-behind della classe `MainPage` gestisce l'invio e la ricezione delle notifiche:

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

Il costruttore della classe `MainPage` usa il `DependencyService` Xamarin.Forms per recuperare un'istanza specifica della piattaforma del `INotificationManager`. Il metodo `OnScheduleClicked` utilizza l'istanza `INotificationManager` per pianificare una nuova notifica. Il metodo `ShowNotification` viene chiamato dal gestore eventi associato all'evento `NotificationReceived` e inserisce un nuovo `Label` nella pagina quando viene richiamato l'evento.

Per ulteriori informazioni sul `DependencyService`Xamarin.Forms, vedere [Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md).

## <a name="create-the-android-interface-implementation"></a>Creare l'implementazione dell'interfaccia Android

Per consentire all'applicazione Xamarin.Forms di inviare e ricevere notifiche in Android, l'applicazione deve fornire un'implementazione dell'interfaccia `INotificationManager`.

### <a name="create-the-androidnotificationmanager-class"></a>Creazione della classe AndroidNotificationManager

La classe `AndroidNotificationManager` implementa l'interfaccia `INotificationManager`:

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

L'attributo `assembly` sopra lo spazio dei nomi registra l'implementazione dell'interfaccia `INotificationManager` con il `DependencyService`.

Android consente alle applicazioni di definire più canali per le notifiche. Il metodo `Initialize` crea un canale di base utilizzato dall'applicazione di esempio per inviare le notifiche. Il metodo `ScheduleNotification` definisce la logica specifica della piattaforma necessaria per creare e inviare una notifica. Infine, il metodo `ReceiveNotification` viene chiamato dal sistema operativo Android quando viene ricevuto un messaggio e richiama il gestore eventi.

> [!NOTE]
> La classe `Application` viene definita in entrambi gli spazi dei nomi `Xamarin.Forms` e `Android.App` in modo che l'alias `AndroidApp` venga definito nelle istruzioni `using` per distinguere i due.

### <a name="handle-incoming-notifications-on-android"></a>Gestire le notifiche in ingresso in Android

La classe `MainActivity` deve rilevare le notifiche in ingresso e inviare una notifica all'istanza di `AndroidNotificationManager`. L'attributo `Activity` nella classe `MainActivity` deve specificare un valore `LaunchMode` di `LaunchMode.SingleTop`:

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

La modalità `SingleTop` impedisce l'avvio di più istanze di un `Activity` mentre l'applicazione è in primo piano. Questo `LaunchMode` potrebbe non essere appropriato per le applicazioni che avviano più attività in scenari di notifica più complessi. Per altre informazioni sui valori di enumerazione `LaunchMode`, vedere [attività Android LaunchMode](https://developer.android.com/guide/topics/manifest/activity-element#lmode).

Nella classe `MainActivity` viene modificato per ricevere le notifiche in ingresso:

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

Il metodo `CreateNotificationFromIntent` estrae i dati di notifica dall'argomento `intent` e li fornisce al `AndroidNotificationManager` usando il metodo `ReceiveNotification`. Il metodo `CreateNotificationFromIntent` viene chiamato dal metodo `OnCreate` e dal metodo `OnNewIntent`:

- Quando l'applicazione viene avviata dai dati di notifica, i dati `Intent` verranno passati al metodo `OnCreate`.
- Se l'applicazione è già in primo piano, i dati `Intent` verranno passati al metodo `OnNewIntent`.

Android offre molte opzioni avanzate per le notifiche. Per altre informazioni, vedere [notifiche in Xamarin.Android](~/android/app-fundamentals/notifications/index.md).

## <a name="create-the-ios-interface-implementation"></a>Creare l'implementazione dell'interfaccia iOS

Per consentire all'applicazione Xamarin.Forms di inviare e ricevere notifiche in iOS, l'applicazione deve fornire un'implementazione del `INotificationManager`.

### <a name="create-the-iosnotificationmanager-class"></a>Creazione della classe iOSNotificationManager

La classe `iOSNotificationManager` implementa l'interfaccia `INotificationManager`:

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

L'attributo `assembly` sopra lo spazio dei nomi registra l'implementazione dell'interfaccia `INotificationManager` con il `DependencyService`.

In iOS è necessario richiedere l'autorizzazione per l'uso delle notifiche prima di provare a pianificare una notifica. Il metodo `Initialize` richiede l'autorizzazione per l'utilizzo delle notifiche locali. Il metodo `ScheduleNotification` definisce la logica necessaria per creare e inviare una notifica. Infine, il metodo `ReceiveNotification` verrà chiamato da iOS quando viene ricevuto un messaggio e richiama il gestore eventi.

### <a name="handle-incoming-notifications-on-ios"></a>Gestire le notifiche in ingresso in iOS

In iOS è necessario creare un delegato che sottoclassi `UNUserNotificationCenterDelegate` per gestire i messaggi in ingresso. L'applicazione di esempio definisce una classe `iOSNotificationReceiver`:

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

Questa classe usa il `DependencyService` per ottenere un'istanza della classe `iOSNotificationManager` e fornisce i dati di notifica in ingresso al metodo `ReceiveNotification`.

La classe `AppDelegate` deve specificare il delegato personalizzato durante l'avvio dell'applicazione. La classe `AppDelegate` deve specificare un oggetto `iOSNotificationReceiver` come delegato `UNUserNotificationCenter` durante l'avvio dell'applicazione. Questo errore si verifica nel metodo `FinishedLaunching`:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

iOS offre molte opzioni avanzate per le notifiche. Per altre informazioni, vedere [notifiche in Xamarin.iOS](~/ios/platform/user-notifications/index.md).

## <a name="test-the-application"></a>Test dell'applicazione

Una volta che i progetti della piattaforma contengono un'implementazione registrata dell'interfaccia `INotificationManager`, l'applicazione può essere testata su entrambe le piattaforme. Eseguire l'applicazione e fare clic sul pulsante **Pianifica Notifica** per creare una notifica.

In Android la notifica verrà visualizzata nell'area di notifica. Quando la notifica viene toccata, l'applicazione riceve la notifica e visualizza un messaggio al di sotto del pulsante **Pianifica Notifica** :

![Notifiche locali in Android](local-notifications-images/local-notifications-android.png)

In iOS le notifiche in ingresso vengono ricevute automaticamente dall'applicazione senza richiedere l'input dell'utente. L'applicazione riceve la notifica e visualizza un messaggio al di sotto del pulsante **Pianifica Notifica** :

![Notifiche locali in iOS](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>Collegamenti correlati

- [Progetto di esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Notifiche in Xamarin.Android](~/android/app-fundamentals/notifications/index.md)
- [Notifiche in Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [Dipendenza Xamarin.Forms. Service](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
