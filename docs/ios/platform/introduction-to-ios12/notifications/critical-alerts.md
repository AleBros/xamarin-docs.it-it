---
title: Avvisi critici in Xamarin.iOS
description: Questo documento descrive come usare gli avvisi critici con Xamarin.iOS. Gli avvisi critici, introdotti con iOS 12, sono notifiche di disturbo che rivestono un suono indipendentemente dal fatto che l'opzione non sia disturbata o che l'interruttore squillo sia disattivato.
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 43b810b95e4da2927030617e68c0ade824a0beaa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031982"
---
# <a name="critical-alerts-in-xamarinios"></a>Avvisi critici in Xamarin.iOS

Con iOS 12, le app possono inviare avvisi critici. Gli avvisi critici rivestono un suono indipendentemente dall'abilitazione o meno della funzionalità di disturbo o dall'opzione della suoneria. Queste notifiche sono problematiche e devono essere usate solo quando gli utenti devono intraprendere un'azione immediata.

## <a name="custom-critical-alert-entitlement"></a>Diritti personalizzati per gli avvisi critici

Per visualizzare gli avvisi critici nell'app, richiedere prima di tutto [un diritto personalizzato](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/) per le notifiche di avviso da Apple.

Dopo aver ricevuto questo diritto da Apple e aver seguito le istruzioni associate su come configurare l'app per l'uso, aggiungere il [diritto](~/ios/deploy-test/provisioning/entitlements.md) personalizzato ai file con **estensione plist dei diritti** dell'app. Configurare quindi le opzioni di **firma del bundle iOS** in modo da usare i **diritti. plist** quando si firma l'app sia nel simulatore che nel dispositivo.

## <a name="request-authorization"></a>Richiedi autorizzazione

La richiesta di autorizzazione per le notifiche di un'app richiede all'utente di consentire o impedire le notifiche di un'app. Se la richiesta di autorizzazione della notifica richiede l'autorizzazione per l'invio di avvisi critici, l'app consentirà anche all'utente di acconsentire esplicitamente agli avvisi critici.

Il codice seguente richiede l'autorizzazione per inviare avvisi critici e notifiche standard e suoni passando il [`UNAuthorizationOptions`](xref:UserNotifications.UNAuthorizationOptions) appropriato
valori da [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*):

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
e impostare la relativa proprietà `Sound` su uno dei seguenti valori:

- `UNNotificationSound.DefaultCriticalSound`, che usa il suono di notifica critico predefinito.
- `UNNotificationSound.GetCriticalSound`, che consente di specificare un suono personalizzato che viene incluso nell'app e in un volume.

Quindi, creare una `UNNotificationRequest` dal contenuto della notifica e aggiungerla al centro notifiche:

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
> Gli avvisi critici non verranno recapitati se non sono abilitati per l'app. Insieme alla richiesta visualizzata la prima volta che un'app richiede l'autorizzazione per l'invio di avvisi critici, un utente può anche abilitare o disabilitare gli avvisi critici nella sezione **notifiche** dell'app dell'app **Impostazioni** iOS.

## <a name="remote-critical-alerts"></a>Avvisi critici in remoto

Per informazioni sugli avvisi critici in remoto, vedere le [novità della sessione notifiche utente](https://developer.apple.com/videos/play/wwdc2018/710/) da WWDC 2018 e il documento [generazione di una notifica remota](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) .

## <a name="related-links"></a>Collegamenti correlati

- [Framework delle notifiche utente in Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novità delle notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Procedure consigliate e novità delle notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
