---
title: Tocco
description: "Touch screen su molti dei dispositivi moderni consente agli utenti di interagire in modo rapido ed efficiente con i dispositivi in modo naturale e intuitivo. Questa interazione non è limitata solo per il rilevamento di tocco semplice: è possibile utilizzare anche i movimenti. Ad esempio, il movimento zoom con avvicinamento delle dita è un esempio molto comune: da una parte della schermata con due dita che l'utente è possibile ingrandire o ridurre di compressione. Questa guida viene esaminato tocco e i movimenti in iOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4A17FD28-313F-4AAC-B82B-3847B4D64A88
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/23/2017
ms.openlocfilehash: 8f6c26048bc0ece0d64acf069151ff1d67403ccc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="touch"></a>Tocco

_Touch screen su molti dei dispositivi moderni consente agli utenti di interagire in modo rapido ed efficiente con i dispositivi in modo naturale e intuitivo. Questa interazione non è limitata solo per il rilevamento di tocco semplice: è possibile utilizzare anche i movimenti. Ad esempio, il movimento zoom con avvicinamento delle dita è un esempio molto comune: da una parte della schermata con due dita che l'utente è possibile ingrandire o ridurre di compressione. Questa guida viene esaminato tocco e i movimenti in iOS._


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
