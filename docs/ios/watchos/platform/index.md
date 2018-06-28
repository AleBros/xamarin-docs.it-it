---
title: funzionalità della piattaforma watchOS
description: Questo documento include collegamenti ad varie guide che descrivono le funzionalità della piattaforma watchOS, ad esempio Apple Pay, notifiche, complessità, suggerimenti proattivi, allenamenti App e molto altro.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 16d10dd69223f404aac7c933302992a1544461e9
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066614"
---
# <a name="watchos-platform-features"></a>funzionalità della piattaforma watchOS

Questo documento include collegamenti ad varie guide che descrivono le funzionalità della piattaforma watchOS, ad esempio Apple Pay, notifiche, complessità, suggerimenti proattivi, allenamenti App e molto altro.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introduzione a watchOS 4](introduction-to-watchos4.md)

Questo documento viene fornita una panoramica delle funzionalità aggiunti e aggiornati watchOS 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introduzione a watchOS 3](introduction-to-watchos3/index.md)

Questo articolo descrive le API di nuove e aggiornate in watchOS 3.

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Apple Pay miglioramenti](~/ios/watchos/platform/apple-pay.md)

In watchOS 3, il framework PassKit è stato espanso per consentire al supporto per i pagamenti protetto, nell'applicazione (di merci fisiche e servizi) per le App in esecuzione su di Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Attività in background](~/ios/watchos/platform/background-tasks.md)

watchOS 3 introduce diverse attività in background che un'app è possibile utilizzare per aggiornare le informazioni di garantire che sia il contenuto l'utente deve prima di aprirlo.

## <a name="notificationsnotificationsmd"></a>[Notifiche](notifications.md)

Informazioni su come fornire la gestione dell'app di espressioni di controllo di notifica personalizzata.

## <a name="complicationscomplicationsmd"></a>[Complicazioni](complications.md)

Aggiungere il supporto complicazione per visualizzare dati aggiornati sul quadrante dell'espressione di controllo.

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Suggerimenti proattivi](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 consente all'app in modo proattivo presentare informazioni all'utente all'interno di dato contesti. Per supportare questa funzionalità, il [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) include ora il `MapItem` proprietà che consente all'app di fornire informazioni sul percorso per un utilizzo successivo da altre app.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Tecniche di interazione rapido](~/ios/watchos/platform/quick-interaction-techniques.md)

Che fornisce le interazioni degli utenti rapido sono essenziali per la creazione di App di Apple Watch accattivanti e complessità. Nuovo per watchOS 3, Apple ha aggiunto il supporto per i riconoscitori di movimento, l'accesso alla chioma digitali e nuove tecniche di navigazione e notifica all'utente. Questa operazione, nonché il supporto aggiunto per SceneKit e SpriteKit, consentire allo sviluppatore di creare facilmente avanzate e concise interfacce che sono rapide e reattive.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Miglioramenti di app allenamenti](~/ios/watchos/platform/workout-apps.md)

Nuovo per watchOS 3, allenamenti correlati App hanno la possibilità di eseguire in background nel Apple Watch. Per abilitare questa funzionalità (e ottenere l'accesso ai dati HealthKit), l'app deve includere il `WKBackgroundModes` chiave nel `Info.plist` file con il valore `workout-processing`.
