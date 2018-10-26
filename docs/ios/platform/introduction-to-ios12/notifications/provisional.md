---
title: Notifiche provvisorie in xamarin. IOS
description: Questo documento descrive come usare xamarin. IOS per lavorare con le notifiche provvisorie. Notifiche provvisorie, introdotte in iOS 12, consentono alle applicazioni di inviare notifiche non interattiva senza autorizzazione esplicita dell'utente.
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: 31b4c6e98945cd7b5dd4cea8be6f5e3857444f78
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131678"
---
# <a name="provisional-notifications-in-xamarinios"></a>Notifiche provvisorie in xamarin. IOS

Notifiche provisional consentono alle App per la ricezione delle notifiche senza il consenso iniziali di un utente esplicito. Queste notifiche arrivano in modalità non interattiva e visualizzare solo nel centro notifiche, che consente agli utenti di visualizzarle in anteprima prima di accettare o rifiutare il recapito continuo.

Nel centro notifiche, gli utenti possono specificare che un'app deve interrompere il recapito delle notifiche provvisorie, continuare recapitarli temporaneamente o avviare recapitarli in modo più evidente.

## <a name="sample-app-redgreennotifications"></a>App di esempio: RedGreenNotifications

Esaminiamo il [RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications) app di esempio, che consente di inviare notifiche provvisorie.

## <a name="sending-provisional-notifications"></a>L'invio di notifiche provvisorio

Per inviare notifiche provvisorie, fornire `UNAuthorizationOptions.Provisional` come un'opzione per il [`RequestAuthorization`](https://developer.xamarin.com/api/member/UserNotifications.UNUserNotificationCenter.RequestAuthorization/)
metodo `UNUserNotificationCenter`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

Se l'utente promuove provisional notifiche di recapito notificate all'utente, il `UNAuthorizationOptions` i valori passati a `RequestAuthorization` determineranno le nuove impostazioni di recapito notifica (nel codice precedente `UNAuthorizationOptions.Alert` e `UNAuthorizationOptions.Sound`).

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio: RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [Framework per le notifiche utente in xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novità in notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Le procedure consigliate e quali sono le novità per le notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
