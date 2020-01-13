---
title: Notifiche provvisorie in Xamarin.iOS
description: Questo documento descrive come usare Xamarin.iOS per lavorare con le notifiche provvisorie. Le notifiche provvisorie, introdotte in iOS 12, consentono alle applicazioni di inviare notifiche non autorizzate senza autorizzazione esplicita per l'utente.
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 7d9fe2a651d8d75d8dd9d8c0dd1225350a58373d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031892"
---
# <a name="provisional-notifications-in-xamarinios"></a>Notifiche provvisorie in Xamarin.iOS

Le notifiche provvisorie consentono alle app di recapitare le notifiche senza il consenso esplicito iniziale di un utente. Queste notifiche arrivano in modo silenzioso e vengono visualizzate solo nel centro notifiche, che consente agli utenti di visualizzarle in anteprima prima di acconsentire o uscire dalla consegna continua.

Nel centro notifiche gli utenti possono specificare che un'app deve interrompere la distribuzione di notifiche provvisorie, continuare a distribuirle in modo provisionale oppure iniziare a distribuirle in modo più evidente.

## <a name="sample-app-redgreennotifications"></a>App di esempio: RedGreenNotifications

Esaminare l'app di esempio [RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications) , che invia le notifiche provvisorie.

## <a name="sending-provisional-notifications"></a>Invio di notifiche provvisorie

Per inviare le notifiche provvisorie, fornire `UNAuthorizationOptions.Provisional` come opzione al [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
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

Se l'utente promuove le notifiche provvisorie al recapito preminente, i valori `UNAuthorizationOptions` passati a `RequestAuthorization` determineranno le nuove impostazioni di recapito delle notifiche (nel codice precedente, `UNAuthorizationOptions.Alert` e `UNAuthorizationOptions.Sound`).

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio-RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Framework delle notifiche utente in Xamarin.iOS](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [Novità delle notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Procedure consigliate e novità delle notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
