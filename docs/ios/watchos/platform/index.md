---
title: funzionalità della piattaforma watchOS
description: Questo documento include collegamenti alle varie guide che descrivono le funzionalità della piattaforma di watchOS, ad esempio Apple Pay, notifiche, complicazioni, suggerimenti proattivi, le App per gli allenamenti e altro ancora.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: 09200ba5968838edf829b30a50a8ad0f4a3ab3aa
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "39030678"
---
# <a name="watchos-platform-features"></a>funzionalità della piattaforma watchOS

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[Introduzione a watchOS 5](introduction-to-watchos5/index.md)

Questo documento fornisce una panoramica generale delle funzionalità nuove e aggiornate in watchOS 5 che possono essere utilizzati durante la compilazione di App watchOS con Xamarin.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introduzione a watchOS 4](introduction-to-watchos4.md)

Questo documento offre una panoramica generale delle funzionalità aggiunti e aggiornati watchOS 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introduzione a watchOS 3](introduction-to-watchos3/index.md)

Questo articolo descrive le API di nuove e aggiornate in watchOS 3.

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Miglioramenti di Apple Pay](~/ios/watchos/platform/apple-pay.md)

In watchOS 3, il framework di PassKit è stato ampliato per consentire al supporto per i pagamenti sicuri, in-app (di beni e servizi) per le App in esecuzione sul Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Attività in background](~/ios/watchos/platform/background-tasks.md)

watchOS 3 introduce diverse attività in background che un'app può usare per aggiornare le proprie informazioni assicurando che sia il contenuto, l'utente deve prima di aprirlo.

## <a name="notificationsnotificationsmd"></a>[Notifiche](notifications.md)

Informazioni su come fornire la notifica personalizzata, la gestione nell'app watch.

## <a name="complicationscomplicationsmd"></a>[Complicazioni](complications.md)

Aggiungere il supporto complicazione per visualizzare dati aggiornati nel quadrante dell'orologio.

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Suggerimenti proattivi](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 consente all'app di presentare in modo proattivo informazioni all'utente all'interno di contesti di base. Per supportare questa funzionalità, il [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) include ora il `MapItem` proprietà che consente all'app di fornire informazioni sul percorso per un utilizzo successivo da altre app.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Tecniche per interazioni rapide](~/ios/watchos/platform/quick-interaction-techniques.md)

Fornire interazioni dell'utente rapida sono essenziali per la creazione di applicazioni Apple Watch accattivanti e complessità. Nuovo a watchOS 3, Apple ha aggiunto il supporto per i riconoscitori di movimento, accesso ai Crown digitale e nuove tecniche di navigazione e notifica all'utente. Questa operazione, insieme al supporto aggiunto per SceneKit sia SpriteKit, consentono allo sviluppatore di creare con facilità interfacce avanzate e glanceable veloce e reattivo.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Miglioramenti delle app per gli allenamenti](~/ios/watchos/platform/workout-apps.md)

Nuovo per watchOS 3, per gli allenamenti correlati App hanno la possibilità di eseguire in background nel Apple Watch. Per abilitare questa funzionalità (e ottenere l'accesso ai dati di HealthKit), l'app deve includere il `WKBackgroundModes` chiavi nel `Info.plist` file con il valore `workout-processing`.
