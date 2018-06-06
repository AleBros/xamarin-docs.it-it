---
title: Gestione tocco nelle App xamarin
description: Questo documento include collegamenti ad guide che descrivono come utilizzare i tocco, Multi-touch, movimenti e 3D Touch in un'app xamarin. IOS.
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/23/2017
ms.openlocfilehash: eb8dce8b13345c13a6f95ae7784bd135e7d1f1f5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784162"
---
# <a name="handling-touch-in-xamarinios-apps"></a>Gestione tocco nelle App xamarin

Come altre piattaforme per dispositivi mobili, iOS dispone di un numero di modi per gestire il tocco. Può supportare Multi-touch-numero di punti di contatto nella schermata e i movimenti complessi. Questa guida vengono introdotti alcuni concetti, nonché la specificità dell'implementazione di tocco e i movimenti in iOS.

iOS incapsula i dati di tocco di `UITouch` (classe), che viene reso disponibile alle applicazioni attraverso una serie di `UIResponder` metodi. Le applicazioni possono eseguire l'override di questi metodi in sottoclassi della `UIView` e `UIViewController`, che ereditano entrambe da `UIResponder`.

Oltre all'acquisizione dei dati di tocco, iOS fornisce i mezzi per l'interpretazione degli schemi di ritocchi in movimenti. Questi tipi di riconoscimento di movimento è a sua volta utilizzabile per interpretare i comandi specifici dell'applicazione, ad esempio una rotazione di un'immagine o una volta di una pagina. iOS fornisce una vasta raccolta di classi per gestire i movimenti comuni con codice aggiunto minimo.

La scelta tra elementi e i riconoscitori di movimento può essere uno confusione. È consigliabile che in genere, dovrebbe dare la preferenza per i riconoscitori di movimento. I riconoscitori di movimento sono implementati come classi discrete, che forniscono una maggiore separazione delle problematiche e migliore incapsulamento. Questo rende molto semplice condividere la logica tra diverse visualizzazioni, riducendo al minimo la quantità di codice scritto.

Tuttavia, esistono casi è necessario utilizzare l'elaborazione di basso livello tocco e tenere traccia anche delle dita più, ad esempio, per creare un programma finger-paint.

## <a name="sections"></a>Sezioni

-  [Tocco in iOS](touch-in-ios.md)
-  [Procedura dettagliata: Utilizzo di tocco in iOS](ios-touch-walkthrough.md)
-  [Rilevamento del multitouch](touch-tracking.md)

Questa guida viene utilizzato come introduzione al tocco in iOS. Per ulteriori informazioni sull'utilizzo di tocco e commenti e suggerimenti tattili 3D in iOS, che sono stati introdotti in iOS 9 e 10 rispettivamente, consultare le guide specifiche seguenti:

* [3D Touch](~/ios/platform/3d-touch.md)
* [Implementazione del feedback aptico](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>Collegamenti correlati

- [iOS tocco di avvio (esempio)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS tocco finale (esempio)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
- [Pittura con le dita (esempio)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
