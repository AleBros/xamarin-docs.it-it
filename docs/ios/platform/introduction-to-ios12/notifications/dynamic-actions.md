---
title: Pulsanti di azione di notifica dinamica in Xamarin.iOS
description: Con iOS 12, un'estensione per il contenuto delle notifiche può aggiungere, rimuovere e aggiornare i pulsanti di azione visualizzati insieme a una notifica. Questo documento descrive come usare i pulsanti di azione di notifica dinamica con Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 6B34AD78-5117-42D0-B6E7-C8B4B453EAFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: cb38d222cecd1a6c5bb65b0fb376888770dd0e49
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031965"
---
# <a name="dynamic-notification-action-buttons-in-xamarinios"></a>Pulsanti di azione di notifica dinamica in Xamarin.iOS

In iOS 12, le notifiche possono aggiungere, rimuovere e aggiornare in modo dinamico i pulsanti di azione associati. Tale personalizzazione consente di fornire agli utenti azioni direttamente rilevanti per il contenuto della notifica e l'interazione dell'utente.

## <a name="sample-app-redgreennotifications"></a>App di esempio: RedGreenNotifications

I frammenti di codice in questa guida provengono dall'app di esempio [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) , che illustra come usare Xamarin.iOS per lavorare con i pulsanti di azione di notifica in iOS 12.

Questa app di esempio invia due tipi di notifiche locali: rosso e verde.
Dopo che l'app ha inviato una notifica, usare 3D Touch per visualizzare l'interfaccia utente personalizzata. Usare quindi i pulsanti di azione della notifica per ruotare l'immagine visualizzata. Quando l'immagine ruota, viene visualizzato un pulsante di **rotazione della reimpostazione** e scompare se necessario.

I frammenti di codice in questa guida provengono da questa applicazione di esempio.

## <a name="default-action-buttons"></a>Pulsanti di azione predefiniti

La categoria di una notifica determina i pulsanti di azione predefiniti.

Consente di creare e registrare le categorie di notifiche durante l'avvio di un'applicazione.
Nell' [app di esempio](#sample-app-redgreennotifications), ad esempio, il `FinishedLaunching` metodo di `AppDelegate` esegue le operazioni seguenti:

- Definisce una categoria per le notifiche rosse e un'altra per le notifiche verdi
- Registra queste categorie chiamando il [`SetNotificationCategories`](xref:UserNotifications.UNUserNotificationCenter.SetNotificationCategories*)
Metodo di `UNUserNotificationCenter`
- Connette un singolo [`UNNotificationAction`](xref:UserNotifications.UNNotificationAction)
per ogni categoria

Il codice di esempio seguente illustra come funziona:

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

In base a questo codice, qualsiasi notifica il cui [`Content.CategoryIdentifier`](xref:UserNotifications.UNNotificationContent.CategoryIdentifier)
è "Red-Category" o "Green-Category", per impostazione predefinita, viene visualizzato un pulsante di azione **ruota 20 °** .

## <a name="in-app-handling-of-notification-action-buttons"></a>Gestione in-app dei pulsanti di azione di notifica

`UNUserNotificationCenter` dispone di una proprietà `Delegate` di tipo [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate).

Nell'app di esempio `AppDelegate` si imposta come delegato del centro notifiche utente in `FinishedLaunching`:

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
per gestire i rubinetti del pulsante di azione:

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

Questa implementazione di `DidReceiveNotificationResponse` non gestisce il pulsante di azione **ruota di 20 °** della notifica. Al contrario, l'estensione del contenuto della notifica gestisce i rubinetti su questo pulsante. Nella sezione successiva viene illustrata la gestione dei pulsanti di azione di notifica.

## <a name="action-buttons-in-the-notification-content-extension"></a>Pulsanti di azione nell'estensione per il contenuto delle notifiche

Un'estensione per il contenuto delle notifiche contiene un controller di visualizzazione che definisce l'interfaccia personalizzata per una notifica.

Il controller di visualizzazione può utilizzare i metodi `GetNotificationActions` e `SetNotificationActions` sul [`ExtensionContext`](xref:UIKit.UIViewController.ExtensionContext)
per accedere e modificare i pulsanti di azione della notifica.

Nell'app di esempio, il controller di visualizzazione dell'estensione del contenuto della notifica modifica i pulsanti di azione solo quando risponde a un tocco in un pulsante di azione già esistente.

> [!NOTE]
> Un'estensione per il contenuto delle notifiche può rispondere al tocco di un pulsante di azione nel metodo [`DidReceiveNotificationResponse`](xref:UserNotificationsUI.UNNotificationContentExtension_Extensions.DidReceiveNotificationResponse*) del controller di visualizzazione, dichiarato come parte di [IUNNotificationContentExtension](xref:UserNotificationsUI.IUNNotificationContentExtension).
>
> Anche se condivide un nome con il metodo `DidReceiveNotificationResponse` [descritto in precedenza](#in-app-handling-of-notification-action-buttons), si tratta di un metodo diverso.
>
> Al termine dell'elaborazione di un tocco di un pulsante, un'estensione del contenuto della notifica può scegliere se indicare all'applicazione principale di gestire lo stesso tocco del pulsante. A tale scopo, deve passare un valore appropriato di [UNNotificationContentExtensionResponseOption](xref:UserNotificationsUI.UNNotificationContentExtensionResponseOption) al gestore di completamento:
>
> - `Dismiss` indica che l'interfaccia di notifica deve essere rilasciata e che l'app principale non deve gestire il tocco del pulsante.
> - `DismissAndForwardAction` indica che l'interfaccia di notifica deve essere rilasciata e che l'app principale deve anche gestire il tocco del pulsante.
> - `DoNotDismiss` indica che l'interfaccia di notifica non deve essere rilasciata e che l'app principale non deve gestire il tocco del pulsante.

Il metodo di `DidReceiveNotificationResponse` dell'estensione di contenuto determina quale pulsante di azione è stato toccato, ruota l'immagine nell'interfaccia della notifica e visualizza o nasconde un pulsante di azione di **reimpostazione** :

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

In questo caso, il metodo passa `UNNotificationContentExtensionResponseOption.DoNotDismiss` al relativo gestore di completamento. Ciò significa che l'interfaccia della notifica resterà aperta.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio-RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Framework delle notifiche utente in Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [Dichiarazione dei tipi di notifica di utilità pratica](https://developer.apple.com/documentation/usernotifications/declaring_your_actionable_notification_types?language=objc)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novità delle notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Procedure consigliate e novità delle notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
