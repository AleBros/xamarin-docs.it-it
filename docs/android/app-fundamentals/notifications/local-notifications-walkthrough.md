---
title: 'Procedura dettagliata: utilizzo delle notifiche locale in xamarin'
description: Questa procedura dettagliata viene illustrato come utilizzare le notifiche locale nelle applicazioni di xamarin. Vengono illustrate le nozioni di base di creazione e pubblicazione di una notifica locale. Quando l'utente sceglie la notifica nell'area di notifica, all'avvio una seconda attività.
ms.prod: xamarin
ms.assetid: D8C6C9E2-3282-49D1-A2F6-78A4F3306E29
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/30/2018
ms.openlocfilehash: a2ca3755e3201263584447ba47ec36d2096386da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---using-local-notifications-in-xamarinandroid"></a>Procedura dettagliata: utilizzo delle notifiche locale in xamarin

_Questa procedura dettagliata viene illustrato come utilizzare le notifiche locale nelle applicazioni di xamarin. Vengono illustrate le nozioni di base di creazione e pubblicazione di una notifica locale. Quando l'utente sceglie la notifica nell'area di notifica, all'avvio una seconda attività._


## <a name="overview"></a>Panoramica

In questa procedura dettagliata, si creerà un'applicazione Android che genera una notifica quando l'utente sceglie un pulsante in un'attività. Quando l'utente sceglie la notifica, avvia una seconda attività che visualizza il numero di volte in cui che l'utente ha scelto il pulsante nella prima attività.

La schermata seguente vengono illustrati alcuni esempi di questa applicazione:

[![Schermate di esempio con la notifica](local-notifications-walkthrough-images/1-overview-sml.png)](local-notifications-walkthrough-images/1-overview.png#lightbox)



## <a name="walkthrough"></a>Procedura dettagliata

Per iniziare, creare un nuovo progetto Android utilizzando il **App Android** modello. Questo progetto verrà chiamato **LocalNotifications**. (Se non si ha familiarità con la creazione di progetti di xamarin, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).)


### <a name="add-the-androidsupportv4app-component"></a>Aggiungere il componente Android.Support.V4.App

In questa procedura dettagliata si sta usando `NotificationCompat.Builder` per generare la notifica locale. Come spiegato in [locale notifiche](~/android/app-fundamentals/notifications/local-notifications.md), è necessario includere il [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet nel progetto per utilizzare `NotificationCompat.Builder`.

Successivamente, modifica **Mainactivity** e aggiungere le seguenti `using` istruzione in modo che i tipi in `Android.Support.V4.App` sono disponibili per il codice:

```csharp
using Android.Support.V4.App;
```

Inoltre, è necessario renderlo deselezionare al compilatore che si sta usando il `Android.Support.V4.App` versione `TaskStackBuilder` anziché `Android.App` versione. Aggiungere il seguente `using` istruzione per risolvere qualsiasi ambiguità:

```csharp
using TaskStackBuilder = Android.Support.V4.App.TaskStackBuilder;
```


### <a name="define-the-notification-id"></a>Definire l'ID di notifica

È necessario un ID univoco per la notifica. Modifica **Mainactivity** e aggiungere la seguente variabile istanza statica di `MainActivity` classe:

```csharp
private static readonly int ButtonClickNotificationId = 1000;
```


### <a name="add-code-to-generate-the-notification"></a>Aggiungere il codice per generare la notifica

Successivamente, è necessario creare un nuovo gestore eventi per il pulsante `Click` evento. Aggiungere il seguente metodo alla `MainActivity`:

```csharp
private void ButtonOnClick (object sender, EventArgs eventArgs)
{
    // Pass the current button press count value to the next activity:
    Bundle valuesForActivity = new Bundle();
    valuesForActivity.PutInt ("count", count);

    // When the user clicks the notification, SecondActivity will start up.
    Intent resultIntent = new Intent(this, typeof (SecondActivity));

    // Pass some values to SecondActivity:
    resultIntent.PutExtras (valuesForActivity);

    // Construct a back stack for cross-task navigation:
    TaskStackBuilder stackBuilder = TaskStackBuilder.Create (this);
    stackBuilder.AddParentStack (Java.Lang.Class.FromType(typeof(SecondActivity)));
    stackBuilder.AddNextIntent (resultIntent);

    // Create the PendingIntent with the back stack:            
    PendingIntent resultPendingIntent =
        stackBuilder.GetPendingIntent (0, (int)PendingIntentFlags.UpdateCurrent);

    // Build the notification:
    NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
        .SetAutoCancel (true)                    // Dismiss from the notif. area when clicked
        .SetContentIntent (resultPendingIntent)  // Start 2nd activity when the intent is clicked.
        .SetContentTitle ("Button Clicked")      // Set its title
        .SetNumber (count)                       // Display the count in the Content Info
        .SetSmallIcon(Resource.Drawable.ic_stat_button_click)  // Display this icon
        .SetContentText (String.Format(
            "The button has been clicked {0} times.", count)); // The message to display.

    // Finally, publish the notification:
    NotificationManager notificationManager =
        (NotificationManager)GetSystemService(Context.NotificationService);
    notificationManager.Notify(ButtonClickNotificationId, builder.Build());

    // Increment the button press count:
    count++;
}
```

Nel `OnCreate` metodo, assegnare questo `ButtonOnClick` metodo il `Click` evento del pulsante (sostituire il gestore eventi delegati fornito dal modello):

```csharp
button.Click += ButtonOnClick;
```


### <a name="create-a-second-activity"></a>Creare una seconda attività

È necessario creare un'altra attività che consente di visualizzare Android quando l'utente sceglie la notifica. Aggiungere un'altra attività Android al progetto denominato **SecondActivity**. Aprire **SecondActivity.cs** e sostituirne il contenuto con questo codice:

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
        protected override void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Get the count value passed to us from MainActivity:
            int count = Intent.Extras.GetInt ("count", -1);

            // No count was passed? Then just return.
            if (count <= 0)
                return;

            // Display the count sent from the first activity:
            SetContentView (Resource.Layout.Second);
            TextView textView = FindViewById<TextView>(Resource.Id.textView);
            textView.Text = String.Format (
                "You clicked the button {0} times in the previous activity.", count);
        }
    }
}
```

È inoltre necessario creare un layout di risorse per **SecondActivity**. Aggiungere un nuovo **Layout Android** file al progetto denominato **Second.axml**. Modifica **Second.axml** e incollare il seguente codice di layout:

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
        android:id="@+id/textView" />
</LinearLayout>
```


### <a name="add-a-notification-icon"></a>Aggiungere un'icona di notifica

Infine, aggiungere una piccola icona che verrà visualizzato nell'area di notifica quando viene avviata la notifica. È possibile copiare [questa icona](local-notifications-walkthrough-images/ic-stat-button-click.png) al progetto o creare la propria icona personalizzata. Il file dell'icona sarà denominato **ic\_stat\_pulsante\_click.png** e copiarlo il **risorse/drawable** cartella. È necessario utilizzare **Aggiungi > elemento esistente...**  per includere il file icona nel progetto.


### <a name="run-the-application"></a>Esecuzione dell'applicazione

Consente di compilare ed eseguire l'applicazione. Verranno visualizzati con la prima attività, simile alla schermata riportata di seguito:

[![Schermata di attività prima](local-notifications-walkthrough-images/2-start-screen-sml.png)](local-notifications-walkthrough-images/2-start-screen.png#lightbox)

Mentre si fa clic sul pulsante, è opportuno notare l'icona di piccole dimensioni per la notifica vengono visualizzati nell'area di notifica:

[![Viene visualizzata l'icona di notifica](local-notifications-walkthrough-images/3-notification-icon-sml.png)](local-notifications-walkthrough-images/3-notification-icon.png#lightbox)

Se si espone il pannello di notifiche scorrere verso il basso, verrà visualizzato la notifica:

[![Messaggio di notifica](local-notifications-walkthrough-images/4-notifications-sml.png)](local-notifications-walkthrough-images/4-notifications.png#lightbox)

Quando si fare clic sulla notifica, deve essere nascosto e l'altra attività deve essere avviata &ndash; ricerca simile nella schermata seguente:

[![Seconda schermata di attività](local-notifications-walkthrough-images/5-second-activity-sml.png)](local-notifications-walkthrough-images/5-second-activity.png#lightbox)

La procedura è stata completata. A questo punto è stata completata la procedura dettagliata Android notifica locale e si dispone di un esempio funzionante che è possibile fare riferimento. Ci sono molte più alle notifiche che è stato illustrato in questo caso, pertanto se si desiderano altre informazioni, guardare [documentazione di Google sulle notifiche](http://developer.android.com/guide/topics/ui/notifiers/notifications.html) e di Android [notifiche](http://developer.android.com/design/patterns/notifications.html) Guida di progettazione.



## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stata utilizzata `NotificationCompat.Builder` per creare e visualizzare le notifiche. È stato illustrato un esempio di come avviare una seconda attività come un modo per rispondere all'interazione dell'utente con la notifica di base e viene illustrato il trasferimento dei dati dalla prima attività per la seconda attività.


## <a name="related-links"></a>Collegamenti correlati

- [LocalNotifications (esempio)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Modelli di Guida di progettazione delle notifiche](http://developer.android.com/design/patterns/notifications.html)
- [notifica](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
