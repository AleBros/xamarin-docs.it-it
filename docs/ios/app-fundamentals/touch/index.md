---
title: La gestione tocco nelle App xamarin. IOS
description: Questo documento include collegamenti alle guide che illustrano come lavorare con il tocco, multitocco, movimenti e funzionalità Touch 3D in un'app xamarin. IOS.
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/23/2017
ms.openlocfilehash: 5aabc3a3c2ffbcffc0e12379989f7eb43b03a902
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116628"
---
# <a name="handling-touch-in-xamarinios-apps"></a>La gestione tocco nelle App xamarin. IOS

Come altre piattaforme per dispositivi mobili, iOS offre numerosi modi per gestire il tocco. Può supportare Multi-touch, ovvero numero di punti di contatto nella schermata e i movimenti complessi. Questa guida presenta alcuni dei concetti, nonché particolarità di implementazione di tocco e movimenti in iOS.

iOS incapsula i dati di tocco nel `UITouch` classe, che viene reso disponibile alle applicazioni attraverso una serie di `UIResponder` metodi. Le applicazioni possono eseguire l'override di questi metodi in sottoclassi della `UIView` e `UIViewController`, che ereditano entrambe dalla `UIResponder`.

Oltre ad acquisire i dati di tocco, iOS fornisce i mezzi per l'interpretazione degli schemi di tocchi in movimenti. I riconoscitori di movimento a sua volta possono essere utilizzati per interpretare comandi specifici dell'applicazione, ad esempio una rotazione di un'immagine o un turno di una pagina. iOS offre un'ampia raccolta di classi per gestire i movimenti comuni senza codice aggiuntivo minimo.

La scelta tra i tocchi e i riconoscitori di movimento può essere un poco chiaro. Questa Guida suggerisce che in genere, dovrebbe dare la preferenza per i riconoscitori di movimento. I riconoscitori di movimento sono implementati come classi discrete, che forniscono una maggiore separazione delle competenze e un miglior incapsulamento. Ciò rende molto semplice condividere la logica tra diverse visualizzazioni, riducendo al minimo la quantità di codice scritto.

Tuttavia, esistono casi è necessario usare l'elaborazione di basso livello touch e anche tenere traccia più dita, ad esempio, per creare un programma finger-paint.

## <a name="sections"></a>Sezioni

-  [Tocco in iOS](touch-in-ios.md)
-  [Procedura dettagliata: uso del tocco in iOS](ios-touch-walkthrough.md)
-  [Rilevamento del multitouch](touch-tracking.md)

Questa guida serve come introduzione a tocco in iOS. Per altre informazioni sull'uso 3D Touch e del Feedback Aptico in iOS, che sono stati introdotti in iOS 9 e 10 rispettivamente, vedere le guide seguenti specifiche:

* [3D Touch](~/ios/platform/3d-touch.md)
* [Implementazione del feedback aptico](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>Collegamenti correlati

- [iOS Touch avviare (esempio)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS Touch finale (esempio)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
- [Pittura con le dita (esempio)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
