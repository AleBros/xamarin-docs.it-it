---
title: Procedura dettagliata - uso delle notifiche locali in xamarin. IOS
description: In questa sezione illustra come usare le notifiche locali in un'applicazione xamarin. IOS. Illustra le nozioni di base di creazione e pubblicazione di una notifica che verrà visualizzato un avviso quando ricevuti dall'app.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: cf1e44ba4176922234fc1b6b9bfe5c463611cc7b
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403429"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Procedura dettagliata - uso delle notifiche locali in xamarin. IOS

_In questa sezione illustra come usare le notifiche locali in un'applicazione xamarin. IOS. Illustra le nozioni di base di creazione e pubblicazione di una notifica che verrà visualizzato un avviso quando ricevuti dall'app._

> [!IMPORTANT]
> Le informazioni in questa sezione si riferiscono ai dispositivi iOS 9 e precedenti, è stato lasciato in questo campo per supportare versioni precedenti di iOS. Per iOS 10 e versioni successive, vedere la [Guida utente notifica Framework](~/ios/platform/user-notifications/index.md) per il supporto sia locali che notifica remota in un dispositivo iOS.

## <a name="walkthrough"></a>Procedura dettagliata

Consentire di creare un'applicazione semplice che mostra le notifiche locali in azione. Questa applicazione avrà un solo pulsante su di esso. Quando si fa clic sul pulsante, creerà una notifica locale. Dopo aver specificato il periodo di tempo, si vedrà la notifica visualizzata.


1. In Visual Studio per Mac, creare una nuova soluzione di visualizzazione singola iOS e chiamarlo `Notifications`.
1. Aprire il `Main.storyboard` file e trascinare un pulsante di visualizzazione. Denominare il pulsante **sul pulsante**e specificare il titolo **Aggiungi notifica**. È anche possibile impostare alcune [vincoli](~/ios/user-interface/designer/designer-auto-layout.md) al pulsante a questo punto: 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "L'impostazione di alcuni vincoli sul pulsante")
1. Modificare il `ViewController` classe e aggiungere il gestore eventi seguente al metodo ViewDidLoad:

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

    Questo codice verrà creata una notifica che utilizza un suono, imposta il valore del badge sull'icona a 1 e viene visualizzato un avviso all'utente.

1. Modificare quindi il file `AppDelegate.cs`, aggiungere prima di tutto il codice seguente al `FinishedLaunching` (metodo). È stato controllato per verificare se il dispositivo è in esecuzione iOS 8, se siamo **obbligatorio** per richiedere l'autorizzazione dell'utente ricevere le notifiche:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
            );

            application.RegisterUserNotificationSettings (notificationSettings);
        }
    ```

1. Sempre nella `AppDelegate.cs`, aggiungere il metodo seguente che verrà chiamato quando viene ricevuta una notifica:

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

1. È necessario gestire il caso in cui la notifica è stata avviata a causa di una notifica locale. Modificare il metodo `FinishedLaunching` nella `AppDelegate` per includere il frammento di codice seguente:


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

1. Infine, eseguire l'applicazione. In iOS 8 sarà per consentire le notifiche. Fare clic su **OK** e quindi fare clic sui **aggiungere notifica** pulsante. Dopo una breve pausa dovrebbe essere la finestra di dialogo di avviso, come illustrato negli screenshot seguenti:

    ![](local-notifications-in-ios-walkthrough-images/image0.png "Per confermare la possibilità di inviare notifiche") ![](local-notifications-in-ios-walkthrough-images/image1.png "pulsante di notifica aggiungere") ![](local-notifications-in-ios-walkthrough-images/image2.png "la finestra di dialogo Avviso di notifica")

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata ha illustrato come usare l'API diverse per la creazione e pubblicazione di notifiche in iOS. È stato anche illustrato come aggiornare l'icona dell'applicazione con una notifica per fornire un feedback specifici dell'applicazione all'utente.


## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locali (esempio)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Locale e Guida alla programmazione di notifiche Push](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
