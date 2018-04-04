---
title: Funzionalità della piattaforma
description: Funzionalità specifiche di Apple Watch da includere nelle App watchOS.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: 9b90c799f2635221a2c19bda426c501737600f88
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="platform-features"></a>Funzionalità della piattaforma

_Funzionalità specifiche di Apple Watch da includere nelle App watchOS._

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Miglioramenti di pagamento di Apple](~/ios/watchos/platform/apple-pay.md)

In watchOS 3, il framework PassKit è stata espansa per consentire il supporto per i pagamenti protetto, nell'applicazione (del fisici beni e servizi) per le App in esecuzione su di Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Attività in background](~/ios/watchos/platform/background-tasks.md)

watchOS 3 presenta diverse attività di background che un'app è possibile utilizzare per aggiornare le informazioni assicurandosi che include il contenuto, l'utente deve prima di aprirlo.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introduzione a watchOS 4](introduction-to-watchos4.md)

Nuove funzionalità di watchOS 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introduzione a watchOS 3](introduction-to-watchos3/index.md)

Questo articolo descrive tutte le API di nuove e modificate disponibili in watchOS 3 per gli sviluppatori di Xamarin.

##  <a name="notificationsnotificationsmd"></a>[Notifiche](notifications.md)

Informazioni su come fornire la gestione dell'app di espressioni di controllo di notifica personalizzata.

##  <a name="complicationscomplicationsmd"></a>[Complicazioni](complications.md)

Aggiungere il supporto di complicazione per visualizzare dati aggiornati sul quadrante dell'espressione di controllo.


## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Suggerimenti proattivi](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 consente all'app in modo proattivo presenti informazioni all'utente all'interno di contesti di base. Per supportare questa funzionalità, il [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) include ora il `MapItem` proprietà che consente all'app di fornire le informazioni sul percorso per un utilizzo successivo da altre app.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Tecniche per interazioni rapide](~/ios/watchos/platform/quick-interaction-techniques.md)

Fornisce le interazioni dell'utente rapido sono essenziali per la creazione di efficaci applicazioni Apple Watch e complessità. Nuovo per watchOS 3, Apple ha aggiunto il supporto per i riconoscitori di movimento, accedere alle nuove tecniche di notifica utente e la navigazione e chioma digitale. Questa operazione, nonché il supporto aggiunto per SceneKit e SpriteKit, consente allo sviluppatore di creare facilmente completo e concise interfacce che sono reattiva e rapido.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Miglioramenti delle app per gli allenamenti](~/ios/watchos/platform/workout-apps.md)

Nuovo per watchOS 3, allenamenti correlati App hanno la possibilità di eseguire in background nel Apple Watch. Per abilitare questa funzionalità (e accedere ai dati HealthKit), l'app deve includere il `WKBackgroundModes` chiave nel `Info.plist` file con il valore `workout-processing`.
