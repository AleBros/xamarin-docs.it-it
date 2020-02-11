---
title: funzionalità della piattaforma watchos
description: Questo documento contiene collegamenti a diverse guide che descrivono le funzionalità della piattaforma watchos, ad esempio Apple Pay, notifiche, complicazioni, suggerimenti proattivi, app per gli allenamenti e altro ancora.
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: e571132b5f1e30bececb8302f2dacfcd908ad42e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028285"
---
# <a name="watchos-platform-features"></a>funzionalità della piattaforma watchos

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[Introduzione a watchOS 5](introduction-to-watchos5/index.md)

Questo documento fornisce una panoramica di alto livello delle funzionalità nuove e aggiornate in watchos 5 disponibili per la creazione di app watchos con Xamarin.

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[Introduzione a watchOS 4](introduction-to-watchos4.md)

Questo documento fornisce una panoramica di alto livello delle funzionalità aggiunte e aggiornate in watchos 4.

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[Introduzione a watchOS 3](introduction-to-watchos3/index.md)

Questo articolo descrive le API nuove e aggiornate in watchos 3.

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Miglioramenti di Apple Pay](~/ios/watchos/platform/apple-pay.md)

In watchos 3, il Framework PassKit è stato espanso per consentire il supporto per i pagamenti in-app sicuri (sia dei beni fisici che dei servizi) per le app in esecuzione sul Apple Watch.

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[Attività in background](~/ios/watchos/platform/background-tasks.md)

watchos 3 introduce diverse attività in background che un'app può usare per aggiornare le informazioni assicurando che il contenuto dell'utente sia necessario prima di aprirlo.

## <a name="notificationsnotificationsmd"></a>[Notifiche](notifications.md)

Informazioni su come fornire la gestione delle notifiche personalizzata nell'app Watch.

## <a name="complicationscomplicationsmd"></a>[Complicazioni](complications.md)

Aggiungere il supporto per la complicazione per visualizzare i dati aggiornati sul quadrante dell'orologio.

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[Suggerimenti proattivi](~/ios/watchos/platform/proactive-suggestions.md)

watchos 3 consente all'app di presentare in modo proattivo le informazioni all'utente entro i contesti specificati. Per supportare questa funzionalità, [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) include ora la proprietà `MapItem` che consente all'app di fornire le informazioni sulla posizione per un uso successivo da parte di altre app.

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[Tecniche di interazione rapida](~/ios/watchos/platform/quick-interaction-techniques.md)

Fornire interazioni utente rapide è essenziale per la creazione di applicazioni e complicazioni di Apple Watch accattivanti. Una novità di watchos 3, Apple ha aggiunto il supporto per i riconoscitori di movimento, l'accesso alla Digital Crown e le nuove tecniche di notifica e navigazione degli utenti. Questo, insieme al supporto aggiunto sia per SceneKit che per SpriteKit, consente allo sviluppatore di creare con facilità interfacce accattivanti e accattivanti, rapide e reattive.

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[Miglioramenti delle app di allenamento](~/ios/watchos/platform/workout-apps.md)

Nuovo per watchos 3, le app correlate all'allenamento hanno la possibilità di essere eseguite in background sul Apple Watch. Per abilitare questa funzionalità e ottenere l'accesso ai dati di HealthKit, l'app deve includere la chiave `WKBackgroundModes` nel file di `Info.plist` con il valore `workout-processing`.
