---
title: " Xamarin.Forms notifiche locali" Descrizione: "in questo articolo viene illustrato come inviare e ricevere notifiche locali in Xamarin.Forms ".
ms. prod: Novell MS. AssetID: 60460F57-63C6-4916-BBB5-A870F1DF53D7 ms. Technology: Novell-Forms Author: profexorgeek ms. Author: jusjohns ms. Date: 10/10/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="local-notifications-in-xamarinforms"></a>Notifiche locali inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)

Le notifiche locali sono avvisi inviati dalle applicazioni installate in un dispositivo mobile. Le notifiche locali vengono spesso usate per le funzionalità di, ad esempio:

- Eventi del calendario
- Reminders
- Trigger basati sulla posizione

Ogni piattaforma gestisce la creazione, la visualizzazione e l'utilizzo di notifiche locali in modo diverso. Questo articolo illustra come creare un'astrazione multipiattaforma per inviare e ricevere notifiche locali con Xamarin.Forms .

[![Applicazione di notifiche locali in iOS e Android](local-notifications-images/local-notifications-msg-cropped.png)](local-notifications-images/local-notifications-msg.png#lightbox)

## <a name="create-a-cross-platform-interface"></a>Creare un'interfaccia multipiattaforma

L' Xamarin.Forms applicazione deve creare e utilizzare le notifiche senza alcuna preoccupazione per le implementazioni della piattaforma sottostanti. La seguente `INotificationManager` interfaccia viene implementata nella libreria di codice condivisa e definisce un'API multipiattaforma che l'applicazione può usare per interagire con le notifiche:

```csharp
public interface INotificationManager
{
    event EventHandler NotificationReceived;

    void Initialize();

    int ScheduleNotification(string title, string message);

    void ReceiveNotification(string title, string message);
}
```

Questa interfaccia verrà implementata in ogni progetto di piattaforma. L' `NotificationReceived` evento consente all'applicazione di gestire le notifiche in ingresso. Il `Initialize` metodo deve eseguire qualsiasi logica della piattaforma nativa necessaria per preparare il sistema di notifica. Il `ScheduleNotification` metodo deve inviare una notifica. Il `ReceiveNotification` metodo deve essere chiamato dalla piattaforma sottostante quando viene ricevuto un messaggio.

## <a name="consume-the-interface-in-xamarinforms"></a>Utilizzare l'interfaccia inXamarin.Forms

Una volta creata, un'interfaccia può essere utilizzata nel Xamarin.Forms progetto condiviso anche se le implementazioni della piattaforma non sono ancora state create. L'applicazione di esempio contiene un oggetto `ContentPage` denominato **MainPage. XAML** con il contenuto seguente:

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

Il layout contiene un `Label` elemento con le istruzioni per l'utente e un `Button` che deve pianificare una notifica quando viene toccato.

La `MainPage` classe code-behind gestisce l'invio e la ricezione delle notifiche:

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

Il `MainPage` costruttore della classe utilizza Xamarin.Forms `DependencyService` per recuperare un'istanza specifica della piattaforma dell'oggetto `INotificationManager` . Il `OnScheduleClicked` metodo utilizza l' `INotificationManager` istanza di per pianificare una nuova notifica. Il `ShowNotification` metodo viene chiamato dal gestore eventi associato all' `NotificationReceived` evento e inserisce un nuovo oggetto `Label` nella pagina quando viene richiamato l'evento.

Per ulteriori informazioni su Xamarin.Forms `DependencyService` , vedere [ Xamarin.Forms DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md).

## <a name="create-the-android-interface-implementation"></a>Creare l'implementazione dell'interfaccia Android

Per Xamarin.Forms consentire all'applicazione di inviare e ricevere notifiche in Android, l'applicazione deve fornire un'implementazione dell' `INotificationManager` interfaccia.

### <a name="create-the-androidnotificationmanager-class"></a>Creazione della classe AndroidNotificationManager

La `AndroidNotificationManager` classe implementa l' `INotificationManager` interfaccia:

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

L' `assembly` attributo sopra lo spazio dei nomi registra l' `INotificationManager` implementazione dell'interfaccia con `DependencyService` .

Android consente alle applicazioni di definire più canali per le notifiche. Il `Initialize` metodo crea un canale di base utilizzato dall'applicazione di esempio per inviare le notifiche. Il `ScheduleNotification` metodo definisce la logica specifica della piattaforma necessaria per creare e inviare una notifica. Infine, il `ReceiveNotification` metodo viene chiamato dal sistema operativo Android quando viene ricevuto un messaggio e richiama il gestore eventi.

> [!NOTE]
> La `Application` classe è definita in entrambi gli `Xamarin.Forms` `Android.App` spazi dei nomi e, pertanto l' `AndroidApp` alias viene definito nelle `using` istruzioni per distinguere i due.

### <a name="handle-incoming-notifications-on-android"></a>Gestire le notifiche in ingresso in Android

La `MainActivity` classe deve rilevare le notifiche in ingresso e inviare una notifica all' `AndroidNotificationManager` istanza. L' `Activity` attributo nella `MainActivity` classe deve specificare un `LaunchMode` valore `LaunchMode.SingleTop` :

```csharp
[Activity(
        //...
        LaunchMode = LaunchMode.SingleTop]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        // ...
    }
```

La `SingleTop` modalità impedisce l'avvio di più istanze di un `Activity` mentre l'applicazione è in primo piano. Questo `LaunchMode` potrebbe non essere appropriato per le applicazioni che avviano più attività in scenari di notifica più complessi. Per altre informazioni sui `LaunchMode` valori di enumerazione, vedere [attività Android LaunchMode](https://developer.android.com/guide/topics/manifest/activity-element#lmode).

Nella `MainActivity` classe viene modificato per ricevere le notifiche in ingresso:

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

Il `CreateNotificationFromIntent` metodo estrae i dati di notifica dall' `intent` argomento e li fornisce all'oggetto `AndroidNotificationManager` usando il `ReceiveNotification` metodo. Il `CreateNotificationFromIntent` metodo viene chiamato sia dal `OnCreate` metodo che dal `OnNewIntent` Metodo:

- Quando l'applicazione viene avviata dai dati di notifica, i `Intent` dati verranno passati al `OnCreate` metodo.
- Se l'applicazione è già in primo piano, i `Intent` dati verranno passati al `OnNewIntent` metodo.

Android offre molte opzioni avanzate per le notifiche. Per altre informazioni, vedere [notifiche in Novell. Android](~/android/app-fundamentals/notifications/index.md).

## <a name="create-the-ios-interface-implementation"></a>Creare l'implementazione dell'interfaccia iOS

Per Xamarin.Forms consentire all'applicazione di inviare e ricevere notifiche in iOS, l'applicazione deve fornire un'implementazione di `INotificationManager` .

### <a name="create-the-iosnotificationmanager-class"></a>Creazione della classe iOSNotificationManager

La `iOSNotificationManager` classe implementa l' `INotificationManager` interfaccia:

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

L' `assembly` attributo sopra lo spazio dei nomi registra l' `INotificationManager` implementazione dell'interfaccia con `DependencyService` .

In iOS è necessario richiedere l'autorizzazione per l'uso delle notifiche prima di provare a pianificare una notifica. Il `Initialize` metodo richiede l'autorizzazione per l'utilizzo delle notifiche locali. Il `ScheduleNotification` metodo definisce la logica necessaria per creare e inviare una notifica. Infine, il `ReceiveNotification` metodo verrà chiamato da iOS quando viene ricevuto un messaggio e richiama il gestore eventi.

### <a name="handle-incoming-notifications-on-ios"></a>Gestire le notifiche in ingresso in iOS

In iOS è necessario creare un delegato che sottoclassi `UNUserNotificationCenterDelegate` per gestire i messaggi in ingresso. L'applicazione di esempio definisce una `iOSNotificationReceiver` classe:

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

Questa classe utilizza `DependencyService` per ottenere un'istanza della `iOSNotificationManager` classe e fornisce i dati di notifica in ingresso al `ReceiveNotification` metodo.

La `AppDelegate` classe deve specificare il delegato personalizzato durante l'avvio dell'applicazione. La `AppDelegate` classe deve specificare un `iOSNotificationReceiver` oggetto come `UNUserNotificationCenter` delegato durante l'avvio dell'applicazione. Questo errore si verifica nel `FinishedLaunching` Metodo:

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    global::Xamarin.Forms.Forms.Init();

    UNUserNotificationCenter.Current.Delegate = new iOSNotificationReceiver();

    LoadApplication(new App());
    return base.FinishedLaunching(app, options);
}
```

iOS offre molte opzioni avanzate per le notifiche. Per altre informazioni, vedere [notifiche in Novell. iOS](~/ios/platform/user-notifications/index.md).

## <a name="test-the-application"></a>Test dell'applicazione

Una volta che i progetti della piattaforma contengono un'implementazione registrata dell' `INotificationManager` interfaccia, l'applicazione può essere testata su entrambe le piattaforme. Eseguire l'applicazione e fare clic sul pulsante **Pianifica Notifica** per creare una notifica.

In Android la notifica verrà visualizzata nell'area di notifica. Quando la notifica viene toccata, l'applicazione riceve la notifica e visualizza un messaggio al di sotto del pulsante **Pianifica Notifica** :

![Notifiche locali in Android](local-notifications-images/local-notifications-android.png)

In iOS le notifiche in ingresso vengono ricevute automaticamente dall'applicazione senza richiedere l'input dell'utente. L'applicazione riceve la notifica e visualizza un messaggio al di sotto del pulsante **Pianifica Notifica** :

![Notifiche locali in iOS](local-notifications-images/local-notifications-ios.png)

## <a name="related-links"></a>Collegamenti correlati

- [Progetto di esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/local-notifications)
- [Notifiche in Novell. Android](~/android/app-fundamentals/notifications/index.md)
- [Notifiche in Novell. iOS](~/ios/platform/user-notifications/index.md)
- [Xamarin.FormsDependency. Service](~/xamarin-forms/app-fundamentals/dependency-service/introduction.md)
