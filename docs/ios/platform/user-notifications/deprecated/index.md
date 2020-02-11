---
title: Tecnologie di notifica deprecate in Xamarin.iOS
description: Questo documento descrive le tecnologie di notifica iOS deprecate a favore del framework delle notifiche utente, introdotto in iOS 10.
ms.prod: xamarin
ms.assetid: 20C4F6E5-56DF-4A85-BBF0-E38C88586307
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/07/2016
ms.openlocfilehash: de9a46587a5d1de6f12dd54122b27e53694cdeb8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031398"
---
# <a name="deprecated-notification-technologies-in-xamarinios"></a>Tecnologie di notifica deprecate in Xamarin.iOS

Questa sezione illustra come implementare notifiche push e locali in Xamarin.iOS. Verranno illustrati i vari elementi dell'interfaccia utente di una notifica iOS e verranno illustrate le API che interessano la creazione e la visualizzazione di una notifica.

> [!IMPORTANT]
> Le informazioni contenute in questa sezione sono relative a iOS 9 e versioni precedenti, ma sono state lasciate qui per supportare le versioni precedenti di iOS. Per iOS 10 e versioni successive, vedere la [Guida per il Framework di notifica utente](~/ios/platform/user-notifications/index.md) per il supporto di notifiche locali e remote in un dispositivo iOS.

## <a name="sections"></a>Sezioni

<a name="Local Notifications In iOS" />

## <a name="local-notifications-in-ioslocal-notifications-in-iosmd"></a>[Notifiche locali in iOS](local-notifications-in-ios.md)

In questa sezione viene illustrato come implementare le notifiche locali in Xamarin.iOS. Verranno illustrati i vari elementi dell'interfaccia utente di una notifica iOS e verranno illustrate le API che interessano la creazione e la visualizzazione di una notifica.

<a name="Local Notifications Walkthrough" />

## <a name="walkthrough---using-local-notifications-in-xamarinioslocal-notifications-in-ios-walkthroughmd"></a>[Procedura dettagliata: Uso delle notifiche locali in Xamarin.iOS](local-notifications-in-ios-walkthrough.md)

Questa sezione illustra come usare le notifiche locali in un'applicazione Xamarin.iOS. Vengono illustrate le nozioni di base per la creazione e la pubblicazione di una notifica che visualizza un avviso quando viene ricevuta dall'app.

<a name="Remote Notifications In iOS" />

## <a name="remote-notifications-in-iosremote-notifications-in-iosmd"></a>[Notifiche remote in iOS](remote-notifications-in-ios.md)

Questa sezione includerà le notifiche push in iOS. Introduce il servizio gateway notifiche push di Apple (APNS) e il ruolo che svolge nella pubblicazione delle notifiche per le applicazioni iOS. Verrà illustrato come creare i certificati di sicurezza necessari per abilitare le notifiche push e per discutere. Infine, in questa sezione verranno illustrate alcune delle attività di manutenzione che i server applicazioni devono eseguire per tenere traccia dei dispositivi mobili client.

## <a name="related-links"></a>Collegamenti correlati

- [Notifiche (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/notifications)
