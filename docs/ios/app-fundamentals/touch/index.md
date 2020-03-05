---
title: Gestione del tocco nelle app Novell. iOS
description: Questo documento contiene collegamenti a guide che descrivono come usare touch, multitouch, movimenti e 3D touch in un'app Novell. iOS.
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/23/2017
ms.openlocfilehash: edf5f1c82857930a2647f306195a4c82f4338db6
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78293078"
---
# <a name="handling-touch-in-xamarinios-apps"></a>Gestione del tocco nelle app Novell. iOS

Analogamente ad altre piattaforme per dispositivi mobili, iOS offre diversi modi per gestire il tocco. Può supportare la funzionalità multitocco, ovvero molti punti di contatto sullo schermo, e movimenti complessi. Questa guida presenta alcuni concetti, nonché le peculiarità dell'implementazione di tocco e movimenti in iOS.

iOS incapsula i dati di tocco nella classe `UITouch`, che viene resa disponibile per le applicazioni tramite una serie di `UIResponder` metodi. Le applicazioni possono eseguire l'override di questi metodi nelle sottoclassi di `UIView` e `UIViewController`, entrambi ereditano da `UIResponder`.

Oltre a acquisire i dati di tocco, iOS fornisce i mezzi per interpretare i modelli di tocchi nei movimenti. Questi riconoscitori di movimento possono a sua volta essere usati per interpretare i comandi specifici dell'applicazione, ad esempio una rotazione di un'immagine o una volta una pagina. iOS fornisce un'ampia raccolta di classi per gestire i movimenti comuni con codice aggiunto minimo.

La scelta tra i riconoscitori dei movimenti e i riconoscitori di movimento può essere confusa. Questa guida suggerisce che in generale è necessario assegnare una preferenza ai riconoscitori di movimento. I riconoscitori di movimento vengono implementati come classi discrete, che offrono una maggiore separazione dei problemi e una migliore incapsulamento. Questo rende più semplice condividere la logica tra visualizzazioni diverse, riducendo al minimo la quantità di codice scritto.

Tuttavia, in alcuni casi è necessario usare l'elaborazione del tocco di basso livello e persino tenere traccia di più dita, ad esempio per creare un programma di disegno con dito.

## <a name="sections"></a>Sezioni

- [Tocco in iOS](touch-in-ios.md)
- [Procedura dettagliata: uso del tocco in iOS](ios-touch-walkthrough.md)
- [Rilevamento del multitouch](touch-tracking.md)

Questa guida funge da introduzione al tocco in iOS. Per ulteriori informazioni sull'utilizzo di commenti e suggerimenti tattili 3D in iOS, introdotti rispettivamente in iOS 9 e 10, consultare le guide specifiche seguenti:

- [3D Touch](~/ios/platform/3d-touch.md)
- [Implementazione del feedback aptico](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>Collegamenti correlati

- [iOS touch finale (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
- [FingerPaint (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
