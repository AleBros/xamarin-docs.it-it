---
title: Interfacce utente interattive notifica in xamarin. IOS
description: Con iOS 12, è possibile creare interfacce utente interattive per le notifiche locali e remote. Questa guida descrive come usare queste funzionalità con xamarin. IOS.
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: e6dc2f14b36c9d6f67f1df5ad3d118fa423e0d4d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669397"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>Interfacce utente interattive notifica in xamarin. IOS

[Le estensioni del contenuto di notifica](~/ios/platform/user-notifications/advanced-user-notifications.md), introdotto in iOS 10, consentono di creare interfacce utente personalizzate per le notifiche. A partire da iOS 12, le interfacce utente di notifica possono contenere elementi interattivi quali pulsanti e i dispositivi di scorrimento.

## <a name="sample-app-redgreennotifications"></a>App di esempio: RedGreenNotifications

Il [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) app di esempio contiene un'estensione per contenuto notifiche con un'interfaccia utente interattiva.

Frammenti di codice in questa guida provengono da questo esempio.

## <a name="notification-content-extension-infoplist-file"></a>File Info. plist di estensione per contenuto notifiche

Nell'app di esempio, il **Info. plist** del file nei **RedGreenNotificationsContentExtension** progetto contiene la configurazione seguente:

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

Tenere presente le seguenti funzionalità:

- Il `UNNotificationExtensionCategory` matrice indica il tipo delle categorie di notifica gestisce l'estensione di contenuto.
- Per supportare il contenuto interattivo, imposta l'estensione per contenuto notifiche il `UNNotificationExtensionUserInteractionEnabled` chiave `true`.
- Il `UNNotificationExtensionInitialContentSizeRatio` chiave specifica il rapporto tra larghezza/altezza iniziale per l'interfaccia dell'estensione per il contenuto.

## <a name="interactive-interface"></a>Interfaccia interattiva

**MainInterface.storyboard**, che definisce l'interfaccia per un'estensione per contenuto notifiche, è uno standard storyboard che contiene un controller di visualizzazione singola. Nell'app di esempio, il controller di visualizzazione è di tipo `NotificationViewController`, e contiene una visualizzazione immagine, tre pulsanti e un dispositivo di scorrimento. Lo storyboard associa questi controlli con gestori definiti nel **NotificationViewController.cs**:

- Il **App avviare** pulsante chiamate del gestore di `PerformNotificationDefaultAction` metodo di azione per `ExtensionContext`, che consente di avviare l'app:

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    In, il centro notifiche utente dell'app `Delegate` (nell'app di esempio, il `AppDelegate`) può rispondere all'interazione nel `DidReceiveNotificationResponse` metodo:

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- Il **Ignora notifica** pulsante chiamate del gestore `DismissNotificationContentExtension` su `ExtensionContext`, che chiude la notifica:

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- Il **una notifica di rimozione** gestore del pulsante Ignora la notifica e lo rimuove dall'area di notifica:

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- Il metodo che gestisce le modifiche dei valori sul dispositivo di scorrimento aggiorna il valore alfa dell'immagine visualizzata nell'interfaccia della notifica:

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio: RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Framework per le notifiche utente in xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novità in notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Le procedure consigliate e quali sono le novità per le notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Rich Notifications (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/817/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
