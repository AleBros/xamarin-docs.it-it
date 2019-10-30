---
title: Notifiche in Novell. iOS
description: Questa sezione illustra come implementare le notifiche locali in Novell. iOS. Verranno illustrati i vari elementi dell'interfaccia utente di una notifica iOS e verranno illustrate le API che interessano la creazione e la visualizzazione di una notifica.
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 0cd0937229e8679af46313d0bce4c62792c0f36b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031374"
---
# <a name="notifications-in-xamarinios"></a>Notifiche in Novell. iOS

> [!IMPORTANT]
> Le informazioni contenute in questa sezione riguardano iOS 9 e versioni precedenti. Per iOS 10 e versioni successive, vedere la [Guida per il Framework di notifica utente](~/ios/platform/user-notifications/index.md).

iOS offre tre modi per indicare all'utente che è stata ricevuta una notifica:

- **Audio o vibrazione** : iOS può riprodurre un suono per notificare agli utenti. Se il suono è disabilitato, il dispositivo può essere configurato per la vibrazione.
- **Avvisi** : è possibile visualizzare una finestra di dialogo sullo schermo con le informazioni sulla notifica.
- Notifiche **: quando** viene pubblicata una notifica, è possibile visualizzare un numero (con badge) nell'icona dell'applicazione.

iOS fornisce anche un *Centro notifiche* che consente di visualizzare tutte le notifiche, sia locali che remote, per l'utente. Gli utenti possono accedere a questa operazione scorrendo verso il basso dalla parte superiore della schermata:

![Centro notifiche](local-notifications-in-ios-images/image13.png "Centro notifiche")

## <a name="creating-local-notifications-in-ios"></a>Creazione di notifiche locali in iOS

iOS rende abbastanza semplice creare e gestire le notifiche locali.
Per prima cosa, iOS 8 richiede che le applicazioni chiedano all'utente l'autorizzazione per visualizzare le notifiche. Aggiungere il codice seguente all'app prima di provare a inviare una notifica locale. l' [esempio associato](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications) lo inserisce nel metodo **FinishedLaunching** di **AppDelegate**.

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![Conferma della possibilità di inviare una notifica locale](local-notifications-in-ios-images/image0-sml.png "Conferma della possibilità di inviare una notifica locale")](local-notifications-in-ios-images/image0.png#lightbox)

Per pianificare una notifica locale, creare un oggetto `UILocalNotification`, impostare il `FireDate`e pianificarlo tramite il metodo `ScheduleLocalNotification` sull'oggetto `UIApplication.SharedApplication`. Il frammento di codice seguente mostra come pianificare una notifica che verrà attivata un minuto in futuro e visualizzare un avviso con un messaggio:

```csharp
UILocalNotification notification = new UILocalNotification();
notification.FireDate = NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

Lo screenshot seguente mostra l'aspetto di questo avviso:

[![](local-notifications-in-ios-images/image2-sml.png "An example alert")](local-notifications-in-ios-images/image2.png#lightbox)

Si noti che se l'utente sceglie di *non consentire* le notifiche, non verrà visualizzato nulla.

Se si desidera applicare un badge all'icona dell'applicazione con un numero, è possibile impostarlo come illustrato nel codice di riga seguente:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

Per riprodurre un suono con l'icona, impostare la proprietà soundName nella notifica, come illustrato nel frammento di codice seguente:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

Se il suono della notifica è più lungo di 30 secondi, iOS riprodurrà invece l'audio predefinito.

> [!IMPORTANT]
> Nel simulatore iOS è presente un bug che attiverà due volte la notifica del delegato. Questo problema non si verifica quando si esegue l'applicazione in un dispositivo.

## <a name="handling-notifications"></a>Gestione delle notifiche

le applicazioni iOS gestiscono le notifiche remote e locali quasi esattamente allo stesso modo. Quando un'applicazione è in esecuzione, viene chiamato il metodo `ReceivedLocalNotification` o il metodo `ReceivedRemoteNotification` sulla classe `AppDelegate` e le informazioni di notifica vengono passate come parametro.

Un'applicazione può gestire una notifica in modi diversi. Ad esempio, l'applicazione può semplicemente visualizzare un avviso per ricordare agli utenti di un evento. In alternativa, è possibile che la notifica venga utilizzata per visualizzare un avviso all'utente al termine di un processo, ad esempio la sincronizzazione dei file in un server.

Il codice seguente illustra come gestire una notifica locale e visualizzare un avviso e reimpostare il numero di badge su zero:

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

Se l'applicazione non è in esecuzione, iOS riprodurrà il suono e/o aggiornerà il badge dell'icona come applicabile. Quando l'utente avvia l'applicazione associata all'avviso, l'applicazione viene avviata e viene chiamato il metodo `FinishedLaunching` sul delegato dell'app e le informazioni di notifica vengono passate tramite il parametro `launchOptions`. Se il dizionario delle opzioni contiene la chiave `UIApplication.LaunchOptionsLocalNotificationKey`, il `AppDelegate` sa che l'applicazione è stata avviata da una notifica locale. Il seguente frammento di codice illustra questo processo:

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

Per una notifica remota, `launchOptions` avrà una `LaunchOptionsRemoteNotificationKey` con un `NSDictionary` associato che contiene il payload di notifica remoto. È possibile estrarre il payload di notifica tramite le chiavi `alert`, `badge`e `sound`. Il frammento di codice seguente mostra come ottenere le notifiche remote:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>Riepilogo

In questa sezione è stato illustrato come creare e pubblicare una notifica in Novell. iOS. Viene illustrato il modo in cui un'applicazione può rispondere alle notifiche eseguendo l'override del metodo `ReceivedLocalNotification` o del metodo `ReceivedRemoteNotification` nel `AppDelegate`.

## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locali (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications)
- [Notifiche push e locali per gli sviluppatori](https://developer.apple.com/notifications/)
- [Guida alla programmazione di notifiche push e locali](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](https://docs.microsoft.com/dotnet/api/uikit.uiapplication)
- [UILocalNotification](https://docs.microsoft.com/dotnet/api/uikit.UILocalNotification)
