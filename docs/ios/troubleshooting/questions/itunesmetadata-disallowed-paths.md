---
title: "Perché l'invio dell'app ha esito negativo con: percorsi non consentiti (iTunesMetadata. plist) trovati in...?"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 0255c918f7d6e984c9af2b396d9a2a00286286e4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031000"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>Perché l'invio dell'app ha esito negativo con: percorsi non consentiti (iTunesMetadata. plist) trovati in...?

> ERRORE: errore ITMS-90047: "percorsi non consentiti (" iTunesMetadata. plist ") trovati in: payload/iPhoneApp1. app"

Questo errore è il risultato di una modifica nel processo di verifica dell'app Store di Apple per impedire agli utenti di colpire problemi come il [Bug 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180). Questo errore specifico _non_ è correlato alla versione specifica di Xamarin installata, quindi il downgrade _non_ sarà utile.

Per informazioni sulla soluzione alternativa e sugli aggiornamenti più recenti per questo problema, vedere [la discussione del](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1) forum.
