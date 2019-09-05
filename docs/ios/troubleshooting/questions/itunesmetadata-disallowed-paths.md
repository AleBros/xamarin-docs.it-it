---
title: "Perché l'invio app non riesce con l'errore: Percorsi non consentiti (iTunesMetadata. plist) trovati in...?"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: ad663c47520826cc549011c9e5fc7cbb078d6214
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291025"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>Perché l'invio app non riesce con l'errore: Percorsi non consentiti (iTunesMetadata. plist) trovati in...?

> ERROR: ERROR ITMS-90047: "Percorsi non consentiti (" iTunesMetadata. plist ") trovati in: Payload/iPhoneApp1. app "

Questo errore è il risultato di una modifica nel processo di verifica dell'app Store di Apple per impedire agli utenti di colpire problemi come il [Bug 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180). Questo errore specifico _non_ è correlato alla versione specifica di Novell installata, quindi il downgrade _non_ sarà utile.

Per informazioni sulla soluzione alternativa e sugli aggiornamenti più recenti per questo problema, vedere [la discussione del](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1) forum.
