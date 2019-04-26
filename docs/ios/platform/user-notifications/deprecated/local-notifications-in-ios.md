---
title: Notifiche in xamarin. IOS
description: Questa sezione illustra come implementare le notifiche locali in xamarin. IOS. Verrà illustrano i diversi elementi dell'interfaccia utente di una notifica di iOS e illustrano l'API di coinvolti con la creazione e visualizzazione di una notifica.
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: f31490a683adfb46c609f14adf08b68de0abc375
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082984"
---
# <a name="notifications-in-xamarinios"></a>Notifiche in xamarin. IOS

> [!IMPORTANT]
> Le informazioni in questa sezione si riferiscono ai dispositivi iOS 9 e precedenti. Per iOS 10 e versioni successive, vedere la [manuale dell'utente notifica framework](~/ios/platform/user-notifications/index.md).

iOS ha tre modi per indicare all'utente che è stata ricevuta una notifica:

- **Suoni o vibrazioni** -iOS è possibile riprodurre un suono per informare gli utenti. Se l'audio è disattivato, il dispositivo può essere configurato per vibrazione.
- **Gli avvisi** -è possibile visualizzare una finestra di dialogo sullo schermo con informazioni sulla notifica.
- **Badge** : quando viene pubblicata una notifica, può essere visualizzato un numero (con badge) sull'icona dell'applicazione.

iOS offre anche un *centro notifiche* che mostrerà tutte le notifiche, locali e remote, all'utente. Gli utenti possono accedere scorrendo rapidamente verso il basso dalla parte superiore della schermata:

![Il centro notifiche](local-notifications-in-ios-images/image13.png "il centro notifiche")

## <a name="creating-local-notifications-in-ios"></a>Creazione di notifiche locali in iOS

iOS rende piuttosto semplice creare e gestire le notifiche locali.
Prima di tutto iOS 8 richiede alle applicazioni di richiedere l'autorizzazione dell'utente per visualizzare le notifiche. Aggiungere il codice seguente all'App prima di provare a inviare una notifica locale: la [collegati campione](https://developer.xamarin.com/samples/monotouch/LocalNotifications/) lo inserisce nel **AppDelegate**del **FinishedLaunching** (metodo).

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![Per confermare la possibilità di inviare una notifica locale](local-notifications-in-ios-images/image0-sml.png "confermando la possibilità di inviare una notifica locale")](local-notifications-in-ios-images/image0.png#lightbox)

Per pianificare una notifica locale, creare un `UILocalNotification` dell'oggetto, impostare il `FireDate`e di pianificarne tramite il `ScheduleLocalNotification` metodo sul `UIApplication.SharedApplication` oggetto. Il frammento di codice seguente illustra come pianificare una notifica che verrà generato un minuto in futuro e visualizzare un avviso con un messaggio:

```csharp
UILocalNotification notification = new UILocalNotification();
NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

Lo screenshot seguente mostra come appare l'avviso:

[![](local-notifications-in-ios-images/image2-sml.png "Un avviso di esempio")](local-notifications-in-ios-images/image2.png#lightbox)

Si noti che se l'utente ha scelto *non consentono* notifiche quindi nulla verrà visualizzato.

Se si desidera applicare un badge all'icona dell'applicazione con un numero, è possibile impostare, come illustrato nel codice seguente riga:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

In play ordine un suono con l'icona, impostare la proprietà SoundName sulla notifica, come illustrato nel frammento di codice seguente:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Se il segnale acustico è supera a 30 secondi, iOS riprodurrà l'impostazione predefinita invece.

> [!IMPORTANT]
> È presente un bug nel simulatore iOS che verrà attivato due volte la notifica di delegato. Questo problema non dovrebbe verificarsi quando si esegue l'applicazione in un dispositivo.

## <a name="handling-notifications"></a>Gestione delle notifiche

le applicazioni iOS gestiscono le notifiche locali e remote quasi esattamente allo stesso modo. Quando un'applicazione è in esecuzione, il `ReceivedLocalNotification` metodo o il `ReceivedRemoteNotification` metodo sul `AppDelegate` classe verrà chiamata e le informazioni di notifica verranno passate come parametro.

Un'applicazione può gestire una notifica in modi diversi. Ad esempio, l'applicazione potrebbe semplicemente visualizzare un avviso che ricorda agli utenti su un evento. O la notifica potrebbe essere utilizzata per visualizzare un avviso all'utente che ha completato un processo, ad esempio i file di sincronizzazione a un server.

Il codice seguente viene illustrato come gestire una notifica locale e visualizzare un avviso e reimpostare il numero di badge zero:

```csharp
public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
{
    // show an alert
    UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
    okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

    Window.RootViewController.PresentViewController(okayAlertController, true, null);

    // reset our badge
    UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
}
```

Se l'applicazione non è in esecuzione, iOS verrà riproduce l'audio e/o aggiornare il badge sull'icona a seconda dei casi. Quando l'utente avvia l'applicazione associata all'avviso, l'applicazione verrà avviata e il `FinishedLaunching` verrà chiamato nel delegato dell'app (metodo) e le informazioni di notifica verranno passate mediante la `launchOptions` parametro. Se il dizionario opzioni contiene la chiave `UIApplication.LaunchOptionsLocalNotificationKey`, quindi il `AppDelegate` sa che l'applicazione è stata avviata da una notifica locale. Il frammento di codice seguente illustra questo processo:

```csharp
// check for a local notification
if (launchOptions.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
{
    var localNotification = launchOptions[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
    if (localNotification != null)
    {
        UIAlertController okayAlertController = UIAlertController.Create(localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

        Window.RootViewController.PresentViewController(okayAlertController, true, null);

        // reset our badge
        UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
    }
}
```

Per una notifica remota `launchOptions` avrà un `LaunchOptionsRemoteNotificationKey` con un oggetto associato `NSDictionary` contenente il payload di notifica remota. È possibile estrarre il payload della notifica tramite il `alert`, `badge`, e `sound` chiavi. Il frammento di codice seguente viene illustrato come ottenere notifiche remote:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Riepilogo

In questa sezione ha illustrato come creare e pubblicare una notifica in xamarin. IOS. Visualizzate come un'applicazione può rispondere alle notifiche inviate mediante l'override la `ReceivedLocalNotification` metodo o il `ReceivedRemoteNotification` metodo nella `AppDelegate`.

## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locali (esempio)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Locale e notifiche Push per gli sviluppatori](https://developer.apple.com/notifications/)
- [Locale e Guida alla programmazione di notifiche Push](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
