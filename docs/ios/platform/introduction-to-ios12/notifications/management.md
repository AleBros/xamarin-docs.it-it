---
title: Gestione delle notifiche in xamarin. IOS
description: Questo documento descrive come usare xamarin. IOS per sfruttare i vantaggi delle nuove funzionalità di gestione notifica introdotto in iOS 12.
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 0157a685ac990c0626cd4d6001ef853c6a28b993
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035274"
---
# <a name="notification-management-in-xamarinios"></a>Gestione delle notifiche in xamarin. IOS

In iOS 12, il sistema operativo può collegamento diretto dall'area di notifica e l'app per le impostazioni alla schermata di gestione di notifica dell'app. Questa schermata deve consentire agli utenti di acconsentire esplicitamente e all'esterno di vari tipi di notifiche invia l'app.

## <a name="sample-app-redgreennotifications"></a>App di esempio: RedGreenNotifications

Per un esempio del funzionamento della gestione delle notifiche, esaminiamo il [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) app di esempio.

Questa app di esempio invia due tipi di notifiche, rosso e verde e fornisce una schermata che consente agli utenti di accettare o rifiutare qualsiasi tipo.

Frammenti di codice in questa guida provengono da questa app di esempio.

## <a name="notification-management-screen"></a>Schermata di gestione di notifica

Nell'app di esempio, `ManageNotificationsViewController` definisce un'interfaccia utente che consente agli utenti di abilitare e disabilitare il rosso e verde notifiche in modo indipendente. È uno standard [`UIViewController`](xref:UIKit.UIViewController)
che contiene un [ `UISwitch` ](xref:UIKit.UISwitch) per ogni tipo di notifica. Attivare e disattivare l'opzione per entrambi i tipi di notifica vengono salvati, nelle impostazioni predefinite dell'utente, alle preferenze dell'utente per il tipo di notifica:

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> Schermata di gestione della notifica verifica anche se l'utente abbia disabilitato completamente le notifiche per l'app. In questo caso, nasconde i controlli per i tipi di notifiche specifiche. A tale scopo, schermata di gestione della notifica:
>
> - Le chiamate [ `UNUserNotificationCenter.Current.GetNotificationSettingsAsync` ](xref:UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync) ed esamina il [ `AuthorizationStatus` ](xref:UserNotifications.UNNotificationSettings.AuthorizationStatus) proprietà.
> - Nasconde i controlli per i tipi di notifiche se le notifiche sono state completamente disabilitate per l'app.
> - Controlli nuovamente se sono state disabilitate le notifiche ogni volta che l'applicazione viene spostata in primo piano, poiché l'utente può abilitare o disabilitare le notifiche nelle impostazioni di iOS in qualsiasi momento.

L'app di esempio `ViewController` (classe), che consente di inviare le notifiche, controllo preferenza dell'utente prima di inviare una notifica locale per garantire che la notifica è di tipo utente effettivamente richiede la ricezione:

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>Collegamento diretto

iOS i collegamenti diretti alla schermata di gestione di notifica di un'app dal centro notifiche e le impostazioni di notifica dell'app nell'app impostazioni. Per semplificare questa operazione, un'app deve:

- Indicare che una schermata di gestione di notifica è disponibile tramite il passaggio `UNAuthorizationOptions.ProvidesAppNotificationSettings` alla richiesta di autorizzazione di notifica dell'app.
- Implementare il `OpenSettings` metodo dalla [ `IUNUserNotificationCenterDelegate` ](xref:UserNotifications.IUNUserNotificationCenterDelegate).

### <a name="authorization-request"></a>Richiesta di autorizzazione

Per indicare al sistema operativo che è disponibile una schermata di gestione di notifica, deve passare un'app per la `UNAuthorizationOptions.ProvidesAppNotificationSettings` opzione (insieme a eventuali altre notifiche opzioni di recapito necessarie) per il `RequestAuthorization` metodo sul `UNUserNotificationCenter`.

Ad esempio, nell'app di esempio `AppDelegate`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.ProvidesAppNotificationSettings | UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
```

### <a name="opensettings-method"></a>Metodo OpenSettings

Il `OpenSettings` metodo, chiamato dal sistema al collegamento diretto alla schermata di gestione di notifica dell'app, debba passare direttamente all'utente di tale schermata.

Nell'app di esempio, questo metodo esegue l'elemento segue per il `ManageNotificationsViewController` se necessario:

```csharp
[Export("userNotificationCenter:openSettingsForNotification:")]
public void OpenSettings(UNUserNotificationCenter center, UNNotification notification)
{
    var navigationController = Window.RootViewController as UINavigationController;
    if (navigationController != null)
    {
        var currentViewController = navigationController.VisibleViewController;
        if (currentViewController is ViewController)
        {
            currentViewController.PerformSegue(ManageNotificationsViewController.ShowManageNotificationsSegue, this);
        }

    }
}
```

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio: RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Framework per le notifiche utente in xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novità in notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Le procedure consigliate e quali sono le novità per le notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
