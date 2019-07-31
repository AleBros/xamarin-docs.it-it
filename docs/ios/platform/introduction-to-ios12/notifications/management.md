---
title: Gestione delle notifiche in Novell. iOS
description: Questo documento descrive come usare Novell. iOS per sfruttare le nuove funzionalità di gestione delle notifiche introdotte in iOS 12.
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 69b6876a22e511d1c14a795d7b81c3a638492468
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652393"
---
# <a name="notification-management-in-xamarinios"></a>Gestione delle notifiche in Novell. iOS

In iOS 12 il sistema operativo è in grado di collegarsi in modo approfondito dal centro notifiche e dall'app impostazioni alla schermata di gestione delle notifiche di un'app. Questa schermata deve consentire agli utenti di acconsentire esplicitamente e in uscita dai vari tipi di notifiche inviate dall'app.

## <a name="sample-app-redgreennotifications"></a>App di esempio: RedGreenNotifications

Per un esempio di come funziona la gestione delle notifiche, consultare l'app di esempio [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) .

Questa app di esempio invia due tipi di notifiche, rosso e verde, e fornisce una schermata che consente agli utenti di acconsentire esplicitamente o meno.

I frammenti di codice in questa guida provengono da questa applicazione di esempio.

## <a name="notification-management-screen"></a>Schermata di gestione notifiche

Nell'app di esempio, `ManageNotificationsViewController` definisce un'interfaccia utente che consente agli utenti di abilitare e disabilitare in modo indipendente le notifiche rosse e le notifiche verdi. È uno standard[`UIViewController`](xref:UIKit.UIViewController)
contenente un [`UISwitch`](xref:UIKit.UISwitch) oggetto per ogni tipo di notifica. Attivare o disattivare l'opzione per uno dei due tipi di salvataggio delle notifiche, in impostazioni predefinite utente, le preferenze dell'utente per quel tipo di notifica:

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> La schermata di gestione delle notifiche controlla anche se l'utente ha completamente disattivato le notifiche per l'app. In tal caso, nasconde gli elementi attivati per i singoli tipi di notifica. A tale scopo, la schermata di gestione delle notifiche:
>
> - Chiama [`UNUserNotificationCenter.Current.GetNotificationSettingsAsync`](xref:UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync) ed esamina la [`AuthorizationStatus`](xref:UserNotifications.UNNotificationSettings.AuthorizationStatus) proprietà.
> - Nasconde gli interruttori per i singoli tipi di notifica se le notifiche sono state completamente disabilitate per l'app.
> - Controlla di nuovo se le notifiche sono state disabilitate ogni volta che l'applicazione viene spostata in primo piano, poiché l'utente può abilitare o disabilitare le notifiche nelle impostazioni iOS in qualsiasi momento.

La `ViewController` classe dell'app di esempio, che invia le notifiche, verifica la preferenza dell'utente prima di inviare una notifica locale per assicurarsi che la notifica sia di un tipo che l'utente vuole effettivamente ricevere:

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>Collegamento profondo

collegamenti profondi iOS alla schermata di gestione delle notifiche di un'app dal centro notifiche e le impostazioni di notifica dell'app nell'app Impostazioni. Per semplificare questa operazione, un'app deve:

- Indicare che è disponibile una schermata di gestione delle notifiche `UNAuthorizationOptions.ProvidesAppNotificationSettings` passando alla richiesta di autorizzazione per le notifiche dell'app.
- Implementare il `OpenSettings` metodo da [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate).

### <a name="authorization-request"></a>Richiesta di autorizzazione

Per indicare al sistema operativo che è disponibile una schermata di gestione delle notifiche, un'applicazione deve passare `UNAuthorizationOptions.ProvidesAppNotificationSettings` l'opzione (insieme alle altre opzioni `RequestAuthorization` di recapito delle notifiche necessarie) al metodo in `UNUserNotificationCenter`.

Ad esempio, nell'app di `AppDelegate`esempio:

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

Il `OpenSettings` metodo, chiamato dal sistema per il collegamento diretto alla schermata di gestione delle notifiche di un'app, deve passare direttamente all'utente a tale schermata.

Nell'app di esempio, questo metodo esegue segue in `ManageNotificationsViewController` se necessario:

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

- [App di esempio-RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Framework delle notifiche utente in Novell. iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novità delle notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Procedure consigliate e novità delle notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
