---
title: Prestazioni di Xamarin.Mac
description: Questo documento contiene alcune considerazioni sulle prestazioni per le app Xamarin.Mac. Vengono illustrati il moderno framework di destinazione, il linker, AOT, i delegati, le API Cocoa per il riutilizzo delle visualizzazioni e il codice asincrono.
ms.prod: xamarin
ms.assetid: 54B07DED-FDF2-49B2-A5FB-3A9357E65922
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 11/10/2017
ms.openlocfilehash: a5a759ae9f156eec71706d9681fac2a94995848e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73021682"
---
# <a name="xamarinmac-performance"></a>Prestazioni di Xamarin.Mac

## <a name="overview"></a>Panoramica

Le applicazioni Xamarin.Mac sono simili a Xamarin.iOS e molti degli stessi suggerimenti relativi alle prestazioni sono validi:

- [Prestazioni di Xamarin.iOS](~/ios/deploy-test/performance.md)
- [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)

ma esistono alcuni suggerimenti specifici per macOS che possono risultare utili.

## <a name="prefer-modern-target-framework"></a>Preferire il framework di destinazione moderno

Per l'applicazione Xamarin.Mac sono disponibili più [framework di destinazione](~/mac/platform/target-framework.md) con caratteristiche e funzionalità di prestazioni diverse.

Quando possibile, preferire l'approccio moderno e usare le librerie dipendenti per aggiungere il supporto. Solo il framework di destinazione moderno consente il collegamento che può ridurre in misura significativa le dimensioni degli assembly. Ciò diventa particolarmente importante quando si abilita AOT poiché la compilazione AOT di assembly completi può produrre bundle finali di grandi dimensioni.

## <a name="enable-the-linker"></a>Abilitare il linker

Il tempo di avvio, in caricamento e "Just-In-Time" (JIT), si mantiene piuttosto in linea con le dimensioni dei file binari finali. Il modo più semplice per migliorarlo è la rimozione del codice non utilizzato con il [linker](~/mac/deploy-test/linker.md).

Sebbene questo suggerimento sia rivolto principalmente agli utenti del framework di destinazione moderno, anche l'uso del [collegamento alla piattaforma](~/mac/deploy-test/linker.md) può contribuire a un piccolo aumento delle prestazioni.

## <a name="enable-aot-when-appropriate"></a>Abilitare AOT quando appropriato

Un altro aspetto delle prestazioni di avvio è la compilazione JIT di assembly nel codice macchina. La compilazione Ahead Of Time (AOT) può ridurre in modo significativo il tempo di avvio, ma comporta alcuni compromessi descritti nella [documentazione AOT](~/mac/internals/aot.md).

## <a name="ensure-performant-delegates"></a>Usare delegati con prestazioni elevate

Molte applicazioni Xamarin.Mac si basano sulle visualizzazioni Cocoa, ad esempio `NSCollectionView`, `NSOutlineView` o `NSTableView`. Queste visualizzazioni sono spesso supportate dalle classi `Delegate` e `DataSource` fornite a Cocoa che rispondono a domande sui contenuti da visualizzare.

Molti di questi punti di ingresso vengono richiamati spesso, anche più volte al secondo durante lo scorrimento.

Per evitare che l'interfaccia utente si blocchi, verificare che queste funzioni restituiscano valori facilmente calcolabili o che usino informazioni già memorizzate nella cache.

## <a name="use-cocoa-provided-apis-for-reusing-views"></a>Usare le API offerte da Cocoa per riusare le visualizzazioni

Molte visualizzazioni Cocoa contenenti molte visualizzazioni o celle figlio, ad esempio `NSCollectionView`, `NSOutlineView` e `NSTableView`, offrono API per la creazione e il riuso delle visualizzazioni. Queste creano pool di elementi condivisi ed evitano problemi di prestazioni durante lo scorrimento rapido delle visualizzazioni.

## <a name="use-async-and-do-not-block-the-ui"></a>Usare l'approccio asincrono e non bloccare l'interfaccia utente

Le applicazioni desktop spesso elaborano grandi quantità di dati e può facilmente accadere che il thread dell'interfaccia utente si blocchi in attesa di un'operazione sincrona.

Usare quando possibile l'approccio [asincrono](~/cross-platform/platform/async.md) e i thread per evitare di bloccare l'interfaccia utente.

Per le operazioni a esecuzione prolungata valutare l'uso di [NSProgressIndicator](https://docs.microsoft.com/samples/xamarin/mac-samples/progressbarexample) o di altre opzioni indicate nelle linee guida [HIG](https://developer.apple.com/macos/human-interface-guidelines/indicators/progress-indicators/) di Apple per la notifica agli utenti.

## <a name="related-links"></a>Collegamenti correlati

- [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Prestazioni di Xamarin.iOS](~/ios/deploy-test/performance.md)
