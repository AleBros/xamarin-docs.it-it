---
title: 'Procedura dettagliata: uso delle notifiche locali in Novell. iOS'
description: Questa sezione illustra come usare le notifiche locali in un'applicazione Novell. iOS. Vengono illustrate le nozioni di base per la creazione e la pubblicazione di una notifica che visualizza un avviso quando viene ricevuta dall'app.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 764be6319e95b16dc043bebd2abfb27ba0696457
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031410"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Procedura dettagliata: uso delle notifiche locali in Novell. iOS

_Questa sezione illustra come usare le notifiche locali in un'applicazione Novell. iOS. Vengono illustrate le nozioni di base per la creazione e la pubblicazione di una notifica che visualizza un avviso quando viene ricevuta dall'app._

> [!IMPORTANT]
> Le informazioni contenute in questa sezione sono relative a iOS 9 e versioni precedenti, ma sono state lasciate qui per supportare le versioni precedenti di iOS. Per iOS 10 e versioni successive, vedere la [Guida per il Framework di notifica utente](~/ios/platform/user-notifications/index.md) per il supporto di notifiche locali e remote in un dispositivo iOS.

## <a name="walkthrough"></a>Procedura dettagliata

Consente di creare un'applicazione semplice che visualizzerà le notifiche locali in azione. Questa applicazione avrà un solo pulsante. Quando si fa clic sul pulsante, viene creata una notifica locale. Una volta trascorso il periodo di tempo specificato, verrà visualizzata la notifica.

1. In Visual Studio per Mac creare una nuova soluzione iOS di visualizzazione singola e chiamarla `Notifications`.
1. Aprire il file di `Main.storyboard` e trascinare un pulsante nella visualizzazione. Assegnare un nome al **pulsante**e assegnargli il titolo **Aggiungi notifica**. A questo punto, è anche possibile impostare alcuni [vincoli](~/ios/user-interface/designer/designer-auto-layout.md) sul pulsante: 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "Setting some constraints on the button")
1. Modificare la classe `ViewController` e aggiungere il gestore eventi seguente al metodo ViewDidLoad:

    ```csharp
    button.TouchUpInside += (sender, e) =>
    {
        // create the notification
        var notification = new UILocalNotification();

        // set the fire date (the date time in which it will fire)
        notification.FireDate = NSDate.FromTimeIntervalSinceNow(60);

        // configure the alert
        notification.AlertAction = "View Alert";
        notification.AlertBody = "Your one minute alert has fired!";

        // modify the badge
        notification.ApplicationIconBadgeNumber = 1;

        // set the sound to be the default sound
        notification.SoundName = UILocalNotification.DefaultSoundName;

        // schedule it
        UIApplication.SharedApplication.ScheduleLocalNotification(notification);
    };
    ```

    Questo codice creerà una notifica che usa un suono, imposta il valore del badge icona su 1 e visualizza un avviso per l'utente.

1. Successivamente, modificare il file `AppDelegate.cs`aggiungere il codice seguente al metodo `FinishedLaunching`. Per verificare se il dispositivo esegue iOS 8, è **necessario** chiedere all'utente l'autorizzazione per la ricezione delle notifiche:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
        var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
            UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
        );

        application.RegisterUserNotificationSettings (notificationSettings);
    }
    ```

1. Sempre in `AppDelegate.cs`aggiungere il metodo seguente che verrà chiamato quando viene ricevuta una notifica:

    ```csharp
    public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
    {
        // show an alert
        UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(okayAlertController, true, null);

        // reset our badge
        UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
    }
    ```

1. È necessario gestire il caso in cui la notifica è stata avviata a causa di una notifica locale. Modificare il metodo `FinishedLaunching` nel `AppDelegate` per includere il frammento di codice seguente:

    ```csharp
    // check for a notification

    if (launchOptions != null)
    {
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
    }
    ```

1. Infine, eseguire l'applicazione. In iOS 8 verrà richiesto di consentire le notifiche. Fare clic su **OK** , quindi fare clic sul pulsante **Aggiungi notifica** . Dopo una breve pausa dovrebbe essere visualizzata la finestra di dialogo di avviso, come illustrato nelle schermate seguenti:

    ![](local-notifications-in-ios-walkthrough-images/image0.png "Conferma della possibilità di inviare notifiche") ![](local-notifications-in-ios-walkthrough-images/image1.png "Pulsante Aggiungi notifica")
    ![](local-notifications-in-ios-walkthrough-images/image2.png "The notification alert dialog")

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata ha illustrato come usare le varie API per la creazione e la pubblicazione di notifiche in iOS. È stato inoltre illustrato come aggiornare l'icona dell'applicazione con una notifica per fornire all'utente alcuni commenti specifici dell'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locali (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications)
- [Guida alla programmazione di notifiche push e locali](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
