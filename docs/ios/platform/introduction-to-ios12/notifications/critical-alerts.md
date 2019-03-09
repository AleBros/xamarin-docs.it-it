---
title: Avvisi critici in xamarin. IOS
description: Questo documento descrive come usare gli avvisi critici con xamarin. IOS. Avvisi critici, introdotti con iOS 12, sono le notifiche di arresto improvviso che riprodurre un suono indipendentemente dal fatto che se non disturbare è in o il commutatore suoneria è disattivata.
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 264b1935aefe6f5ddf4bfcd17c491f6858bd4ffb
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672274"
---
# <a name="critical-alerts-in-xamarinios"></a>Avvisi critici in xamarin. IOS

Con iOS 12, le app possono inviare gli avvisi critici. Avvisi critici riprodurre un suono indipendentemente dal fatto o meno su non disturbare è abilitata e il commutatore suoneria sia disattivato. Queste notifiche sono problematici e devono essere usate solo quando gli utenti devono eseguire un'azione immediata.

## <a name="custom-critical-alert-entitlement"></a>Personalizzato entitlement avvisi critici

Per visualizzare gli avvisi critici nell'app per la prima di tutto [richiedere un diritto personalizzato notifiche di avviso critico](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/) da Apple.

Dopo la ricezione di questo diritto da Apple e seguire le istruzioni su come configurare l'app associate per utilizzarlo, aggiunto personalizzato [entitlement](~/ios/deploy-test/provisioning/entitlements.md) all'app **entitlements. plist** file. Quindi, configurare le **firma del Bundle iOS** le opzioni da utilizzare **entitlements. plist** durante l'accesso dell'app nel simulatore sia al dispositivo.

## <a name="request-authorization"></a>Richiedere l'autorizzazione

Richiesta di autorizzazione di notifica di un'app chiede all'utente di attivare o disattivare le notifiche dell'app. Se la richiesta di autorizzazione notifica chiede l'autorizzazione per inviare gli avvisi critici, l'app anche fornirà all'utente la possibilità di fornire il consenso esplicito per gli avvisi critici.

Il codice seguente richiede l'autorizzazione per inviare gli avvisi critici e notifiche standard e suoni passando appropriato [`UNAuthorizationOptions`](xref:UserNotifications.UNAuthorizationOptions)
i valori per [ `RequestAuthorization` ](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*):

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.CriticalAlert;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

## <a name="local-critical-alerts"></a>Avvisi critici locali

Per inviare un avviso critico locale, creare un [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
e impostare il `Sound` a una delle due proprietà:

- `UNNotificationSound.DefaultCriticalSound`, che utilizza il segnale acustico predefinito critici.
- `UNNotificationSound.GetCriticalSound`, che consente di specificare un oggetto personalizzato sembrare che è in bundle con l'app e un volume.

Creare quindi un `UNNotificationRequest` dalla notifica del contenuto e aggiungerlo all'area di notifica:

```csharp
var content = new UNMutableNotificationContent()
{
    Title = "Critical alert title",
    Body = "Text of the critical alert",
    CategoryIdentifier = "my-critical-alert-category",
    // Sound = UNNotificationSound.DefaultCriticalSound
    Sound = UNNotificationSound.GetCriticalSound("my_critical_sound.m4a", 1.0f)
};

var request = UNNotificationRequest.FromIdentifier(
    Guid.NewGuid().ToString(),
    content,
    UNTimeIntervalNotificationTrigger.CreateTrigger(3, false)
);

var center = UNUserNotificationCenter.Current;
center.AddNotificationRequest(request, null);
```

> [!IMPORTANT]
> Avvisi critici non verrà recapitati se non sono abilitati per l'app. Con il messaggio di richiesta viene visualizzata la prima volta un'app chiede l'autorizzazione per inviare gli avvisi critici, un utente può anche abilitare o disabilitare gli avvisi critici dell'app **notifiche** sezione di iOS **impostazioni**app.

## <a name="remote-critical-alerts"></a>Avvisi critici remoti

Per informazioni sugli avvisi critici remoti, vedere la [What ' s nuove In notifiche utente](https://developer.apple.com/videos/play/wwdc2018/710/) sessione WWDC 2018 e il [generata una notifica remota](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) documento.

## <a name="related-links"></a>Collegamenti correlati

- [Framework per le notifiche utente in xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novità in notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Le procedure consigliate e quali sono le novità per le notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
