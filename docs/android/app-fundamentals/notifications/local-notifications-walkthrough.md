---
title: Procedura dettagliata - uso delle notifiche locali in xamarin. Android
description: Questa procedura dettagliata illustra come usare le notifiche locali nelle applicazioni xamarin. Android. Vengono illustrate le nozioni di base di creazione e pubblicazione di una notifica locale. Quando l'utente sceglie la notifica nell'area di notifica, avvia una seconda attività.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: 7cf1dde6c65d2971cecd0a59a2e11d6c2d50ee2a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119189"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Procedura dettagliata - uso delle notifiche locali in xamarin. Android

_Questa procedura dettagliata illustra come usare le notifiche locali nelle applicazioni xamarin. Android. Vengono illustrate le nozioni di base di creazione e pubblicazione di una notifica locale. Quando l'utente sceglie la notifica nell'area di notifica, avvia una seconda attività._


## <a name="overview"></a>Panoramica

In questa procedura dettagliata, si creerà un'applicazione Android che genera una notifica quando l'utente sceglie un pulsante in un'attività. Quando si sceglie la notifica, avvia una seconda attività che visualizza il numero di volte in cui che l'utente avesse fatto clic sul pulsante nella prima attività.

Gli screenshot seguenti illustrano alcuni esempi di questa applicazione:

[![Screenshot di esempio con notifica](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> Questa guida è incentrata sul [NotificationCompat APIs](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) dal [libreria di supporto Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Queste API verranno garantire massima la compatibilità per Android 4.0 (livello API 14).

## <a name="creating-the-project"></a>Creazione del progetto

Per iniziare, è possibile creare un nuovo progetto Android usando il **App per Android** modello. È possibile chiamare questo progetto **LocalNotifications**. (Se non ha familiarità con la creazione di progetti xamarin. Android, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).)

Modificare il file di risorse **values/Strings.xml** in modo che contenga due risorse stringa aggiuntiva che verranno utilizzate al momento per creare il canale di notifica:


```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>Aggiungere il pacchetto NuGet Android.Support.V4

In questa procedura dettagliata, si userà `NotificationCompat.Builder` per compilare la notifica locale. Come illustrato in [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md), è necessario includere il [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet nel progetto usare `NotificationCompat.Builder`.

Successivamente, è possibile modificare **MainActivity.cs** e aggiungere quanto segue `using` istruzione in modo che i tipi in `Android.Support.V4.App` sono disponibili al codice:

```csharp
using Android.Support.V4.App;
```

Inoltre, è necessario apportare chiara per il compilatore che si sta usando il `Android.Support.V4.App` versione di `TaskStackBuilder` anziché il `Android.App` versione. Aggiungere il codice seguente `using` istruzione per risolvere qualsiasi ambiguità:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>Creare il canale di notifica

Successivamente, aggiungere un metodo per `MainActivity` che verrà creato un canale di notifica (se necessario):

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var name = Resources.GetString(Resource.String.channel_name);
    var description = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, name, NotificationImportance.Default)
                  {
                      Description = description
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Aggiornamento di `OnCreate` metodo da chiamare questo nuovo metodo:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```


### <a name="define-the-notification-id"></a>Definire l'ID di notifica

È necessario un ID univoco per il canale di notifica e le notifiche. È possibile modificare **MainActivity.cs** e aggiungere la seguente variabile istanza statica di `MainActivity` classe:

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>Aggiungere il codice per generare la notifica

Successivamente, è necessario creare un nuovo gestore eventi per il pulsante `Click` evento. Aggiungere il metodo seguente alla `MainActivity`:

```csharp
void ButtonOnClick(object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    var valuesForActivity = new Bundle();
    valuesForActivity.PutInt(COUNT_KEY, count);

    // When the user clicks the notification, SecondActivity will start up.
    var resultIntent = new Intent(this, typeof(SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras(valuesForActivity);

    // Construct a back stack for cross-task navigation:
    var stackBuilder = TaskStackBuilder.Create(this);
    stackBuilder.AddParentStack(Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent(resultIntent);

    // Create the PendingIntent with the back stack:
    var resultPendingIntent = stackBuilder.GetPendingIntent(0, (int) PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    var builder = new NotificationCompat.Builder(this, CHANNEL_ID)
                  .SetAutoCancel(true) // Dismiss the notification from the notification area when the user clicks on it
                  .SetContentIntent(resultPendingIntent) // Start up this activity when the user clicks the intent.
                  .SetContentTitle("Button Clicked") // Set the title
                  .SetNumber(count) // Display the count in the Content Info
                  .SetSmallIcon(Resource.Drawable.ic_stat_button_click) // This is the icon to display
                  .SetContentText($"The button has been clicked {count} times."); // the message to display.

    // Finally, publish the notification:
    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(NOTIFICATION_ID, builder.Build());

    // Increment the button press count:
    count++;
}
```

Il `OnCreate` metodo MainActivity deve effettuare la chiamata per creare il canale di notifica e assegnare le `ButtonOnClick` metodo per il `Click` evento del pulsante (sostituire il gestore eventi del delegato fornito dal modello):

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();

    // Display the "Hello World, Click Me!" button and register its event handler:
    var button = FindViewById<Button>(Resource.Id.MyButton);
    button.Click += ButtonOnClick;
}
```


### <a name="create-a-second-activity"></a>Creare una seconda attività

A questo punto è necessario creare un'altra attività che Android verrà visualizzato quando l'utente sceglie la notifica. Aggiungere un'altra attività Android al progetto denominato **SecondActivity**. Aprire **SecondActivity.cs** e sostituirne il contenuto con il seguente codice:

```csharp
using System;
using Android.App;
using Android.OS;
using Android.Widget;

namespace LocalNotifications
{
    [Activity(Label = "Second Activity")]
    public class SecondActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            // Get the count value passed to us from MainActivity:
            var count = Intent.Extras.GetInt(MainActivity.COUNT_KEY, -1);

            // No count was passed? Then just return.
            if (count <= 0)
            {
                return;
            }

            // Display the count sent from the first activity:
            SetContentView(Resource.Layout.Second);
            var txtView = FindViewById<TextView>(Resource.Id.textView1);
            txtView.Text = $"You clicked the button {count} times in the previous activity.";
        }
    }
}
```

È anche necessario creare un layout di risorsa per **SecondActivity**. Aggiungere un nuovo **Layout Android** file al progetto chiamato **Second.axml**. Modificare **Second.axml** e incollare il codice di layout seguenti:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text=""
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:id="@+id/textView1" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>Aggiungere un'icona di notifica

Infine, aggiungere una piccola icona che verrà visualizzato nell'area di notifica quando viene avviata la notifica. È possibile copiare [questa icona](local-notifications-walkthrough-images/ic-stat-button-click.png) al progetto o creare la propria icona personalizzata. Denominare il file di icona **ic\_stat\_pulsante\_click.png** e copiarlo il **risorse/drawable** cartella. Ricordarsi di usare **Aggiungi > elemento esistente...**  per includere il file icona nel progetto.


### <a name="run-the-application"></a>Esecuzione dell'applicazione

Compilare ed eseguire l'applicazione. Dovrebbe essere visualizzata la prima attività, simile allo screenshot seguente:

[![Primo screenshot di attività](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

Come si fa clic sul pulsante, si noterà che la piccola icona per la notifica viene visualizzata nell'area di notifica:

[![Viene visualizzata l'icona di notifica](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Se si scorrere verso il basso ed esporre il pannello di notifiche, si noterà la notifica:

[![Messaggio di notifica](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Quando si sceglie la notifica, deve essere nascosto e l'altra attività deve essere avviata &ndash; cercando in qualche modo simile allo screenshot seguente:

[![Seconda schermata di attività](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

La procedura è stata completata. A questo punto hai completato la procedura dettagliata di Android notifica locale e si dispone di un esempio funzionante che è possibile fare riferimento a. C'è molto più alle notifiche che sono stati mostrati in questo caso, pertanto, se si desiderano altre informazioni, dare un'occhiata [documentazione di Google in notifiche](http://developer.android.com/guide/topics/ui/notifiers/notifications.html).


## <a name="summary"></a>Riepilogo

Questa procedura dettagliata utilizzata `NotificationCompat.Builder` per creare e visualizzare le notifiche. È stato illustrato un esempio di base di come avviare una seconda attività come un modo per rispondere all'interazione dell'utente con la notifica e illustrato il trasferimento dei dati dalla prima attività per la seconda attività.


## <a name="related-links"></a>Collegamenti correlati

- [LocalNotifications (esempio)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [I canali di notifica di Android Oreo](https://blog.xamarin.com/android-oreo-notification-channels/)
- [Notifica](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
