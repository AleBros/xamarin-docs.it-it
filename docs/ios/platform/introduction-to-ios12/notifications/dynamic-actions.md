---
title: Pulsanti di azione di notifica dinamica in xamarin. IOS
description: Con iOS 12, un'estensione per contenuto notifiche può aggiungere, rimuovere e aggiornare i pulsanti di azione visualizzati insieme a una notifica. Questo documento descrive come usare i pulsanti di azione di notifica dinamica con xamarin. IOS.
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: edaf9d90e019729f69f721c0a92826c0148db8df
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669628"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>Pulsanti di azione di notifica dinamica in xamarin. IOS

In iOS 12, le notifiche possono aggiungere, rimuovere e aggiornare i pulsanti di azione associato in modo dinamico. Tale personalizzazione rende possibile offrire agli utenti con le azioni che sono direttamente pertinenti al contenuto della notifica e l'interazione dell'utente con esso.

## <a name="sample-app-redgreennotifications"></a>App di esempio: RedGreenNotifications

I frammenti di codice in questa guida provengono i [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) app di esempio che illustra come usare xamarin. IOS per lavorare con i pulsanti di azione di notifica in iOS 12.

Questa app di esempio invia due tipi di notifiche locali: rosso e verde.
Dopo aver recuperato l'app invia una notifica, usare le funzionalità Touch 3D per visualizzare l'interfaccia utente personalizzata. Quindi, usare i pulsanti di azione della notifica per ruotare l'immagine che viene visualizzata. Poiché consente di ruotare l'immagine, un **reimpostare rotazione** pulsante compare e scompare in base alle esigenze.

Frammenti di codice in questa guida provengono da questa app di esempio.

## <a name="default-action-buttons"></a>Pulsanti di azione predefinito

Categoria della notifica determina relativi pulsanti di azione predefinito.

Creare e registrare le categorie di notifica, mentre un'applicazione viene avviata.
Ad esempio, nel [app di esempio](#sample-app-redgreennotifications), il `FinishedLaunching` metodo `AppDelegate` esegue le operazioni seguenti:

- Definisce una categoria per le notifiche di colore rosso e l'altro per le notifiche di colore verde
- Registra queste categorie tramite una chiamata di [`SetNotificationCategories`](xref:UserNotifications.UNUserNotificationCenter.SetNotificationCategories*)
metodo di `UNUserNotificationCenter`
- Associa un singolo [`UNNotificationAction`](xref:UserNotifications.UNNotificationAction)
a ogni categoria

Esempio di codice seguente viene illustrato il funzionamento:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional | UNAuthorizationOptions.ProvidesAppNotificationSettings;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
        var rotateTwentyDegreesAction = UNNotificationAction.FromIdentifier("rotate-twenty-degrees-action", "Rotate 20°", UNNotificationActionOptions.None);

        var redCategory = UNNotificationCategory.FromIdentifier(
            "red-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var greenCategory = UNNotificationCategory.FromIdentifier(
            "green-category",
            new UNNotificationAction[] { rotateTwentyDegreesAction },
            new string[] { },
            UNNotificationCategoryOptions.CustomDismissAction
        );

        var set = new NSSet<UNNotificationCategory>(redCategory, greenCategory);
        center.SetNotificationCategories(set);
    });
    // ...
}
```

Basato su questo codice, qualsiasi notifica la cui proprietà [`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)
sarà "red-categoria di" o "verde-", per impostazione predefinita, Mostra un **ruotare 20°** pulsante di azione.

## <a name="in-app-handling-of-notification-action-buttons"></a>Gestione di in-app dei pulsanti di azione di notifica

`UNUserNotificationCenter` è un `Delegate` vlastnosti typu [ `IUNUserNotificationCenterDelegate` ](xref:UserNotifications.IUNUserNotificationCenterDelegate).

Nell'app di esempio, `AppDelegate` si configura come delegato del centro notifiche utente in `FinishedLaunching`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = // ...
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        center.Delegate = this;
        // ...
```

Quindi, `AppDelegate` implementa [`DidReceiveNotificationResponse`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.DidReceiveNotificationResponse*)
per gestire dispositivi Tap di sul pulsante di azione:

```csharp
[Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
{
    if (response.IsDefaultAction)
    {
        Console.WriteLine("ACTION: Default");
    }
    if (response.IsDismissAction)
    {
        Console.WriteLine("ACTION: Dismiss");
    }
    else
    {
        Console.WriteLine($"ACTION: {response.ActionIdentifier}");
    }

    completionHandler();
        }
```

Questa implementazione di `DidReceiveNotificationResponse` non consente di gestire la notifica **ruotare 20°** pulsante di azione. Al contrario, estensione di contenuto della notifica gestisce scelte su questo pulsante. Ulteriormente la sezione seguente illustra la gestione pulsante di azione di notifica.

## <a name="action-buttons-in-the-notification-content-extension"></a>L'estensione per contenuto notifiche sui pulsanti di azione

Un'estensione per contenuto notifiche contiene un controller di visualizzazione che definisce l'interfaccia personalizzata per una notifica.

Questo controller di visualizzazione è possibile usare la `GetNotificationActions` e `SetNotificationActions` metodi nel relativo [`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)
proprietà per accedere e modificare i pulsanti di azione della notifica.

Nell'app di esempio, il controller di visualizzazione dell'estensione per contenuto di notifica consente di modificare i pulsanti di azione solo quando si risponde a una scelta di un pulsante di azione già esistente.

> [!NOTE]
> Una notifica di estensione di contenuto può rispondere a un tocco del pulsante di azione nel relativo controller di visualizzazione [ `DidReceiveNotificationResponse` ](https://developer.xamarin.com/api/member/UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse/) metodo dichiarato come parte del [IUNNotificationContentExtension](https://developer.xamarin.com/api/type/UserNotificationsUI.IUNNotificationContentExtension/).
>
> Anche se condivide un nome con il `DidReceiveNotificationResponse` metodo [descritti in precedenza](#in-app-handling-of-notification-action-buttons), si tratta di un metodo diverso.
>
> Dopo che un'estensione per contenuto notifiche completa l'elaborazione di tocco di un pulsante, è possibile scegliere se visualizzare o meno indicare all'applicazione principale per gestire tale tocco del pulsante stesso. A tale scopo, è necessario passare un valore appropriato del [UNNotificationContentExtensionResponseOption](https://developer.xamarin.com/api/type/UserNotificationsUI.UNNotificationContentExtensionResponseOption/) al proprio gestore di completamento:
>
> - [`Dismiss`](https://developer.xamarin.com/api/field/UserNotificationsUI.UNNotificationContentExtensionResponseOption.Dismiss/) indica che l'interfaccia di notifica deve essere chiusa e che l'app principale non è necessario gestire il tocco del pulsante.
> - [`DismissAndForwardAction`](https://developer.xamarin.com/api/field/UserNotificationsUI.UNNotificationContentExtensionResponseOption.DismissAndForwardAction/) indica che l'interfaccia di notifica deve essere chiusa e che l'app principale deve gestire anche il tocco del pulsante.
> - [`DoNotDismiss`](https://developer.xamarin.com/api/field/UserNotificationsUI.UNNotificationContentExtensionResponseOption.DoNotDismiss/) indica che l'interfaccia di notifica non deve essere chiusa e che l'app principale non è necessario gestire il tocco del pulsante.

L'estensione di contenuto `DidReceiveNotificationResponse` metodo determina viene toccato il pulsante di azione, consente di ruotare l'immagine nell'interfaccia della notifica e Mostra o nasconde una **reimpostare** pulsante di azione:

```csharp
[Export("didReceiveNotificationResponse:completionHandler:")]
public void DidReceiveNotificationResponse(UNNotificationResponse response, Action<UNNotificationContentExtensionResponseOption> completionHandler)
{
    var rotationAction = ExtensionContext.GetNotificationActions()[0];

    if (response.ActionIdentifier == "rotate-twenty-degrees-action")
    {
        rotationButtonTaps += 1;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        // 9 rotations * 20 degrees = 180 degrees. No reason to
        // show the reset rotation button when the image is half
        // or fully rotated.
        if (rotationButtonTaps % 9 == 0)
        {
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
        }
        else if (rotationButtonTaps % 9 == 1)
        {
            var resetRotationAction = UNNotificationAction.FromIdentifier("reset-rotation-action", "Reset rotation", UNNotificationActionOptions.None);
            ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction, resetRotationAction });
        }
    }

    if (response.ActionIdentifier == "reset-rotation-action")
    {
        rotationButtonTaps = 0;

        double radians = (20 * rotationButtonTaps) * (2 * Math.PI / 360.0);
        Xamagon.Transform = CGAffineTransform.MakeRotation((float)radians);

        ExtensionContext.SetNotificationActions(new UNNotificationAction[] { rotationAction });
    }

    completionHandler(UNNotificationContentExtensionResponseOption.DoNotDismiss);
}
```

In questo caso, il metodo passa `UNNotificationContentExtensionResponseOption.DoNotDismiss` al gestore completamento. Ciò che significa che dell'interfaccia della notifica rimarrà aperto.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio: RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Framework per le notifiche utente in xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [La dichiarazione di tipi di notifica di utilità pratica](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novità in notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Le procedure consigliate e quali sono le novità per le notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
