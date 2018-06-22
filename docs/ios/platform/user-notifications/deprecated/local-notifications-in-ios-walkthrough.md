---
title: 'Procedura dettagliata: utilizzo delle notifiche locale in xamarin. IOS'
description: In questa sezione verrà esaminato come utilizzare le notifiche locale in un'applicazione di xamarin. IOS. Illustra le nozioni di base di creazione e pubblicazione di una notifica che verrà visualizzato un avviso quando ricevuti dall'app.
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: bb133d16f12249cbd31e4fce2b227162b4b28333
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776781"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>Procedura dettagliata: utilizzo delle notifiche locale in xamarin. IOS

_In questa sezione verrà esaminato come utilizzare le notifiche locale in un'applicazione di xamarin. IOS. Illustra le nozioni di base di creazione e pubblicazione di una notifica che verrà visualizzato un avviso quando ricevuti dall'app._

> [!IMPORTANT]
> Le informazioni in questa sezione si riferiscono a iOS 9 e precedenti, è stato lasciato qui per supportare le versioni precedenti di iOS. Per iOS 10 e versioni successive, vedere il [Guida utente notifica Framework](~/ios/platform/user-notifications/index.md) per il supporto sia locali che Remote notifica in un dispositivo iOS.

## <a name="walkthrough"></a>Procedura dettagliata

Consente di creare un'applicazione semplice che mostra le notifiche locale nell'azione. Questa applicazione sarà necessario un solo pulsante su di esso. Quando si fa clic sul pulsante, viene creato una notifica locale. Specificato periodo di tempo trascorso, si verrà visualizzata la notifica.


1. In Visual Studio per Mac, creare una nuova soluzione iOS singola visualizzazione e chiamarlo `Notifications`.
1. Aprire il `Main.storyboard` file e trascinare la visualizzazione di un pulsante. Denominare il pulsante **pulsante**e assegnargli il titolo **notifica aggiungere**. È anche possibile impostare alcune [vincoli](~/ios/user-interface/designer/designer-auto-layout.md) al pulsante a questo punto: 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "L'impostazione di alcuni vincoli sul pulsante")
1. Modificare la `ViewController` classe e aggiungere il seguente gestore eventi per il metodo ViewDidLoad:

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

    Questo codice verrà creata una notifica che utilizza un suono, imposta il valore del badge icona su 1 e viene visualizzato un avviso all'utente.

1. Successivamente, modificare il file `AppDelegate.cs`, innanzitutto aggiungere il codice seguente per il `FinishedLaunching` (metodo). È stata selezionata per vedere se il dispositivo esegue iOS 8, se così **obbligatorio** per richiedere l'autorizzazione dell'utente ricevere le notifiche:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
            );

            application.RegisterUserNotificationSettings (notificationSettings);
        }
    ```

1. Ancora in `AppDelegate.cs`, aggiungere il seguente metodo che verrà chiamato quando viene ricevuta una notifica:

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

1. Infine, eseguire l'applicazione. Nelle versioni iOS 8 è richiesto per consentire le notifiche. Fare clic su **OK** e quindi fare clic su di **aggiungere notifica** pulsante. Dopo una breve pausa la finestra di dialogo di avviso, verrà visualizzato come illustrato nelle schermate seguenti:

    ![](local-notifications-in-ios-walkthrough-images/image0.png "Per confermare la possibilità di inviare notifiche") ![ ] (local-notifications-in-ios-walkthrough-images/image1.png "pulsante di notifica aggiungere") ![ ] (local-notifications-in-ios-walkthrough-images/image2.png "la finestra di dialogo Avviso di notifica")

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata viene illustrato come utilizzare le varie API per la creazione e pubblicazione di notifiche in iOS. Inoltre illustrato come aggiornare l'icona dell'applicazione con un badge per fornire un feedback specifico dell'applicazione all'utente.


## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locale (esempio)](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [Locale e le notifiche Push Guida per programmatori](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
