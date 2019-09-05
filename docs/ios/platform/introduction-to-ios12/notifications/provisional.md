---
title: Notifiche provvisorie in Novell. iOS
description: Questo documento descrive come usare Novell. iOS per lavorare con le notifiche provvisorie. Le notifiche provvisorie, introdotte in iOS 12, consentono alle applicazioni di inviare notifiche non autorizzate senza autorizzazione esplicita per l'utente.
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/04/2018
ms.openlocfilehash: d321e8061d3091abeaa3cff6a6af9172c981cb60
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291193"
---
# <a name="provisional-notifications-in-xamarinios"></a>Notifiche provvisorie in Novell. iOS

Le notifiche provvisorie consentono alle app di recapitare le notifiche senza il consenso esplicito iniziale di un utente. Queste notifiche arrivano in modo silenzioso e vengono visualizzate solo nel centro notifiche, che consente agli utenti di visualizzarle in anteprima prima di acconsentire o uscire dalla consegna continua.

Nel centro notifiche gli utenti possono specificare che un'app deve interrompere la distribuzione di notifiche provvisorie, continuare a distribuirle in modo provisionale oppure iniziare a distribuirle in modo più evidente.

## <a name="sample-app-redgreennotifications"></a>App di esempio: RedGreenNotifications

Esaminare l'app di esempio [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) , che invia le notifiche provvisorie.

## <a name="sending-provisional-notifications"></a>Invio di notifiche provvisorie

Per inviare le notifiche provvisorie, fornire `UNAuthorizationOptions.Provisional` l'opzione[`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
Metodo di `UNUserNotificationCenter`:

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

Se l'utente promuove le notifiche provvisorie per il recapito preminente `UNAuthorizationOptions` , i valori `RequestAuthorization` passati a determineranno le nuove impostazioni di recapito delle notifiche `UNAuthorizationOptions.Alert` ( `UNAuthorizationOptions.Sound`nel codice precedente e).

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio-RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Framework delle notifiche utente in Novell. iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novità delle notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Procedure consigliate e novità delle notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
