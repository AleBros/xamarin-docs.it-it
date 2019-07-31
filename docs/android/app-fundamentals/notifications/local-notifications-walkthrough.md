---
title: 'Procedura dettagliata: uso delle notifiche locali in Novell. Android'
description: Questa procedura dettagliata illustra come usare le notifiche locali nelle applicazioni Novell. Android. Vengono illustrati i concetti di base per la creazione e la pubblicazione di una notifica locale. Quando l'utente fa clic sulla notifica nell'area di notifica, avvia una seconda attività.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: 61dedca1b89237bdd4669a63173b1c6e4fde072b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644302"
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Procedura dettagliata: uso delle notifiche locali in Novell. Android

_Questa procedura dettagliata illustra come usare le notifiche locali nelle applicazioni Novell. Android. Vengono illustrati i concetti di base per la creazione e la pubblicazione di una notifica locale. Quando l'utente fa clic sulla notifica nell'area di notifica, avvia una seconda attività._


## <a name="overview"></a>Panoramica

In questa procedura dettagliata verrà creata un'applicazione Android che genera una notifica quando l'utente fa clic su un pulsante in un'attività. Quando l'utente fa clic sulla notifica, avvia una seconda attività che Visualizza il numero di volte in cui l'utente ha fatto clic sul pulsante nella prima attività.

Gli screenshot seguenti illustrano alcuni esempi di questa applicazione:

[![Schermate di esempio con notifica](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)

> [!NOTE]
> Questa guida è incentrata sulle [API NotificationCompat](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) della [libreria del supporto Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Queste API garantiscono la massima compatibilità con le versioni precedenti di Android 4,0 (livello API 14).

## <a name="creating-the-project"></a>Creazione del progetto

Per iniziare, creare un nuovo progetto Android usando il modello di **app Android** . Chiameremo questo progetto **LocalNotifications**. Se non si ha familiarità con la creazione di progetti Novell. Android, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

Modificare il file di risorse **values/Strings. XML** in modo che contenga due risorse di stringa aggiuntive che verranno usate quando è il momento di creare il canale di notifica:


```xml
<?xml version="1.0" encoding="utf-8"?>

<resources>
  <string name="Hello">Hello World, Click Me!</string>
  <string name="ApplicationName">Notifications</string>

  <string name="channel_name">Local Notifications</string>
  <string name="channel_description">The count from MainActivity.</string>
</resources>
```

### <a name="add-the-androidsupportv4-nuget-package"></a>Aggiungere il pacchetto NuGet Android. support. v4

In questa procedura dettagliata viene usato `NotificationCompat.Builder` per compilare la notifica locale. Come illustrato nelle [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md), è necessario includere la [libreria di supporto Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet nel progetto da usare `NotificationCompat.Builder`.

Successivamente, modificare **MainActivity.cs** e aggiungere l'istruzione seguente `using` in modo che i tipi in `Android.Support.V4.App` siano disponibili per il codice:

```csharp
using Android.Support.V4.App;
```

Inoltre, è necessario chiarire al compilatore che si sta utilizzando la `Android.Support.V4.App` versione di `TaskStackBuilder` anziché la `Android.App` versione. Aggiungere l'istruzione `using` seguente per risolvere qualsiasi ambiguità:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```

### <a name="create-the-notification-channel"></a>Creare il canale di notifica

Aggiungere quindi un metodo a che `MainActivity` creerà un canale di notifica, se necessario:

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

Aggiornare il `OnCreate` metodo per chiamare il nuovo metodo:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    CreateNotificationChannel();
}
```


### <a name="define-the-notification-id"></a>Definire l'ID notifica

È necessario un ID univoco per il canale di notifica e notifica. Modificare **MainActivity.cs** e aggiungere la variabile di istanza statica seguente alla `MainActivity` classe:

```csharp
static readonly int NOTIFICATION_ID = 1000;
static readonly string CHANNEL_ID = "location_notification";
internal static readonly string COUNT_KEY = "count";
```

### <a name="add-code-to-generate-the-notification"></a>Aggiungere codice per generare la notifica

Successivamente, è necessario creare un nuovo gestore eventi per l'evento Button `Click` . Aggiungere il seguente metodo a `MainActivity`:

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

Il `OnCreate` metodo di MainActivity deve effettuare la chiamata per creare il canale di notifica e assegnare `ButtonOnClick` il metodo all' `Click` evento del pulsante (sostituire il gestore eventi del delegato fornito dal modello):

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

A questo punto è necessario creare un'altra attività che verrà visualizzata da Android quando l'utente fa clic sulla notifica. Aggiungere un'altra attività Android al progetto denominato **SecondActivity**. Aprire **SecondActivity.cs** e sostituirne il contenuto con il codice seguente:

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

È anche necessario creare un layout delle risorse per **SecondActivity**. Aggiungere al progetto un nuovo file di **layout Android** denominato **Second. aXML**. Modificare **Second. aXML** e incollare il codice di layout seguente:

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

Aggiungere infine un'icona di piccole dimensioni che verrà visualizzata nell'area di notifica quando viene avviata la notifica. È possibile copiare [questa icona](local-notifications-walkthrough-images/ic-stat-button-click.png) nel progetto o creare un'icona personalizzata. Denominare il file di icona **\_IC stat\_\_Button click. png** e copiarlo nella cartella Resources/di cui è possibile eseguire l' **estrazione** . Ricordarsi di usare **aggiungi > elemento esistente...** per includere il file icona nel progetto.


### <a name="run-the-application"></a>Esecuzione dell'applicazione

Compilare ed eseguire l'applicazione. Verrà visualizzata la prima attività, simile allo screenshot seguente:

[![Schermata prima attività](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

Quando si fa clic sul pulsante, si noterà che l'icona piccola per la notifica viene visualizzata nell'area di notifica:

[![Icona di notifica visualizzata](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Se si scorre verso il basso ed esporre il cassetto delle notifiche, verrà visualizzata la notifica:

[![Messaggio di notifica](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Quando si fa clic sulla notifica, questa dovrebbe scomparire e l'altra attività dovrebbe essere &ndash; avviata con un aspetto simile allo screenshot seguente:

[![Schermata seconda attività](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

Congratulazioni! A questo punto è stata completata la procedura dettagliata per la notifica locale Android ed è presente un esempio funzionante a cui è possibile fare riferimento. Le notifiche sono molto più diverse da quelle illustrate qui. Se si desiderano altre informazioni, vedere la [documentazione di Google relativa alle notifiche](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).


## <a name="summary"></a>Riepilogo

Questa procedura dettagliata `NotificationCompat.Builder` è stata usata per creare e visualizzare le notifiche. È stato illustrato un esempio di base su come avviare una seconda attività come metodo per rispondere all'interazione dell'utente con la notifica e ha dimostrato il trasferimento dei dati dalla prima attività alla seconda attività.


## <a name="related-links"></a>Collegamenti correlati

- [LocalNotifications (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Canali di notifica di Android Oreo](https://blog.xamarin.com/android-oreo-notification-channels/)
- [Notifica](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)
