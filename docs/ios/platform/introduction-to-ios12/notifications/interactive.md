---
title: Interfacce utente di notifica interattiva in Xamarin.iOS
description: Con iOS 12, è possibile creare interfacce utente interattive per le notifiche locali e remote. Questa guida descrive come usare queste funzionalità con Xamarin.iOS.
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: e629cd8f481558991d02c7fb879502ebd54753bd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031934"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>Interfacce utente di notifica interattiva in Xamarin.iOS

Le [estensioni di contenuto delle notifiche](~/ios/platform/user-notifications/advanced-user-notifications.md), introdotte in iOS 10, consentono di creare interfacce utente personalizzate per le notifiche. A partire da iOS 12, le interfacce utente di notifica possono contenere elementi interattivi quali pulsanti e dispositivi di scorrimento.

## <a name="sample-app-redgreennotifications"></a>App di esempio: RedGreenNotifications

L'app di esempio [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) contiene un'estensione per il contenuto delle notifiche con un'interfaccia utente interattiva.

I frammenti di codice in questa guida provengono da questo esempio.

## <a name="notification-content-extension-infoplist-file"></a>File INFO. plist dell'estensione del contenuto delle notifiche

Nell'app di esempio, il file **info. plist** nel progetto **RedGreenNotificationsContentExtension** contiene la configurazione seguente:

```xml
<!-- ... -->
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>UNNotificationExtensionCategory</key>
        <array>
            <string>red-category</string>
            <string>green-category</string>
        </array>
        <key>UNNotificationExtensionUserInteractionEnabled</key>
        <true/>
        <key>UNNotificationExtensionDefaultContentHidden</key>
        <true/>
        <key>UNNotificationExtensionInitialContentSizeRatio</key>
        <real>0.6</real>
    </dict>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.usernotifications.content-extension</string>
    <key></key>
    <true/>
</dict>
<!-- ... -->
```

Si notino le funzionalità seguenti:

- La matrice di `UNNotificationExtensionCategory` specifica il tipo di categorie di notifiche gestite dall'estensione di contenuto.
- Per supportare il contenuto interattivo, l'estensione per il contenuto delle notifiche imposta la chiave di `UNNotificationExtensionUserInteractionEnabled` su `true`.
- La chiave di `UNNotificationExtensionInitialContentSizeRatio` specifica il rapporto altezza/larghezza iniziale per l'interfaccia dell'estensione di contenuto.

## <a name="interactive-interface"></a>Interfaccia interattiva

**MainInterface. Storyboard**, che definisce l'interfaccia per un'estensione del contenuto delle notifiche, è uno storyboard standard che contiene un singolo controller di visualizzazione. Nell'app di esempio, il controller di visualizzazione è di tipo `NotificationViewController`e contiene una visualizzazione immagine, tre pulsanti e un dispositivo di scorrimento. Lo storyboard associa questi controlli ai gestori definiti in **NotificationViewController.cs**:

- Il gestore del pulsante **Launch App** chiama il metodo di azione `PerformNotificationDefaultAction` su `ExtensionContext`, che avvia l'app:

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    Nell'app, il `Delegate` del centro notifiche utente (nell'app di esempio, il `AppDelegate`) può rispondere all'interazione nel metodo `DidReceiveNotificationResponse`:

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- Il gestore del pulsante **Ignora notifica** chiama `DismissNotificationContentExtension` su `ExtensionContext`, che chiude la notifica:

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- Il gestore del pulsante **Rimuovi notifica** respinge la notifica e la rimuove dal centro notifiche:

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- Il metodo che gestisce le modifiche dei valori sul dispositivo di scorrimento aggiorna l'alfa dell'immagine visualizzata nell'interfaccia della notifica:

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio-RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Framework delle notifiche utente in Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novità delle notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Procedure consigliate e novità delle notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Notifiche avanzate (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/817/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
