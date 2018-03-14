---
title: Notifiche di xamarin
description: "In questa sezione viene illustrato come implementare le notifiche locale in xamarin. IOS. Verrà spiegare i vari elementi dell'interfaccia utente di una notifica di iOS e illustrano l'API del coinvolto nella creazione e visualizzazione di una notifica."
ms.topic: article
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 2a8ae55f9cc3e2dd4818dec96a35017c76cc9623
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="notifications-in-xamarinios"></a>Notifiche di xamarin

_In questa sezione viene illustrato come implementare le notifiche locale in xamarin. IOS. Verrà spiegare i vari elementi dell'interfaccia utente di una notifica di iOS e illustrano l'API del coinvolto nella creazione e visualizzazione di una notifica._

> [!IMPORTANT]
> **Nota:** le informazioni in questa sezione si riferiscono a iOS 9 e precedenti, è stato lasciato in questo campo per supportare le versioni precedenti di iOS. Per iOS 10 e versioni successive, vedere il [Guida utente notifica Framework](~/ios/platform/user-notifications/index.md) per il supporto sia locali che Remote notifica in un dispositivo iOS.

iOS presenta tre modi per indicare all'utente che è stata ricevuta una notifica:

-  **Audio o resistenza alle vibrazioni** -iOS è possibile riprodurre un suono per informare gli utenti. Se il suono è disabilitato, il dispositivo può essere configurato per vibrare.
-  **Avvisi** -è possibile visualizzare una finestra di dialogo sullo schermo con informazioni relative alla notifica.
-  **Badge** : quando viene pubblicata una notifica, può essere visualizzato un numero (con badge) sull'icona dell'applicazione.


iOS fornisce inoltre un *centro notifiche* che verranno visualizzate tutte le notifiche, locali e remote, all'utente. Gli utenti possono accedere scorrendo verso il basso dalla parte superiore dello schermo:

 ![](local-notifications-in-ios-images/image13.png "Il centro notifiche")

## <a name="creating-local-notifications-in-ios"></a>Creazione di notifiche locale in iOS

iOS rende più semplice creare e gestire le notifiche locale.
Innanzitutto, iOS 8 richiede alle applicazioni di richiedere l'autorizzazione dell'utente per visualizzare le notifiche. Aggiungere il codice seguente all'applicazione prima di tentare di inviare una notifica locale - esempio allegato viene inserita nella **AppDelegate**del **FinishedLaunching** metodo.

```csharp
var settings = UIUserNotificationSettings.GetSettingsForTypes(
  UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound
  , null);
UIApplication.SharedApplication.RegisterUserNotificationSettings (settings);
```

  [![](local-notifications-in-ios-images/image0-sml.png "Per confermare la possibilità di inviare una notifica locale")](local-notifications-in-ios-images/image0.png#lightbox)

Per pianificare una notifica locale si crea un `UILocalNotification` dell'oggetto, impostare il `FireDate`e di pianificarne tramite il `ScheduleLocalNotification` metodo il `UIApplication.SharedApplication` oggetto. Frammento di codice seguente viene illustrato come pianificare una notifica che verrà generato un minuto in futuro e visualizzare un avviso con un messaggio:

```csharp
UILocalNotification notification = new UILocalNotification();
NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

Nella schermata seguente mostra l'aspetto di questo avviso:

  [![](local-notifications-in-ios-images/image2-sml.png "Un avviso di esempio")](local-notifications-in-ios-images/image2.png#lightbox)

Si noti che se l'utente ha scelto di *non consentire* notifiche nulla verrà visualizzata.

Se si desidera applicare un badge sull'icona dell'applicazione con un numero, è possibile impostarlo come illustrato nel codice seguente riga:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

In play ordine un suono con l'icona, impostare la proprietà SoundName sulla notifica, come illustrato nel frammento di codice seguente:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Per indicazioni dell'interfaccia umana Apple, se una notifica riproduce un suono, si deve inoltre essere corredato da una notifica o un avviso per consentire all'utente di identificare l'applicazione che ha generato l'avviso. Inoltre, se il suono è più di 30 secondi, iOS riprodurrà il valore predefinito invece.

> [!IMPORTANT]
> **Nota**: è presente un bug nel simulatore iOS che verrà attivato due volte la notifica del delegato. Questo problema non viene eseguito quando si esegue l'applicazione in un dispositivo.

## <a name="handling-notifications"></a>Gestione delle notifiche

le applicazioni iOS gestiscono le notifiche di locali e remote quasi esattamente allo stesso modo. Quando un'applicazione è in esecuzione, il `ReceivedLocalNotification` metodo o `ReceivedRemoteNotification` metodo sulla classe AppDelegate verrà chiamato e le informazioni di notifica verranno passate come parametro.

Un'applicazione può gestire una notifica in modi diversi. Ad esempio, l'applicazione potrebbe semplicemente visualizzare un avviso per segnalare agli utenti alcuni eventi di invio. O la notifica potrebbe essere utilizzata per visualizzare un avviso all'utente che ha completato un processo, ad esempio i file di sincronizzazione a un server.

Il codice seguente viene illustrato come gestire una notifica locale e visualizzare un avviso e reimpostare il numero di badge zero:

```csharp
 public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
        {
            // show an alert
            UIAlertController okayAlertController = UIAlertController.Create (notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
            okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
            viewController.PresentViewController (okayAlertController, true, null);

            // reset our badge
            UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
        }
```

Se l'applicazione non è in esecuzione, iOS verrà riprodurre l'audio e/o aggiornare il badge icona come applicabile. Quando l'utente viene avviata l'applicazione associata all'avviso, l'applicazione verrà avviata e verrà chiamato il metodo FinishedLaunching sul delegato app e le informazioni di notifica verranno passate tramite il parametro options. Se il dizionario di opzioni contiene la chiave `UIApplication.LaunchOptionsLocalNotificationKey`, il AppDelegate sa che l'applicazione è stata avviata da una notifica locale. Frammento di codice riportato di seguito viene illustrato questo processo:

```csharp
// check for a local notification
if (options.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
 {
     var localNotification = options[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
     if (localNotification != null)
     {
        UIAlertController okayAlertController = UIAlertController.Create (localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
        viewController.PresentViewController (okayAlertController, true, null);

         // reset our badge
         UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
     }
 }
```

In caso di una notifica remota dizionario opzioni invece conterrebbe un `LaunchOptionsRemoteNotificationKey`, e il valore per la chiave risultante sarebbe un `NSDictionary` oggetto con il payload di notifica remoto. È possibile estrarre il payload di notifica tramite l'avviso, badge e chiavi audio. Frammento di codice seguente viene illustrato come ottenere notifiche remote:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Riepilogo

In questa sezione viene illustrato come creare e pubblicare una notifica in xamarin. IOS. Verrà visualizzato come un'applicazione in grado di reagire alle notifiche eseguendo l'override di `ReceivedLocalNotification` (metodo) o `ReceivedRemoteNotification` metodo nel `AppDelegate`.


## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locale (esempio)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Locale e inviare notifiche per gli sviluppatori](https://developer.apple.com/notifications/)
- [Locale e inviare notifica Guida per programmatori](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
