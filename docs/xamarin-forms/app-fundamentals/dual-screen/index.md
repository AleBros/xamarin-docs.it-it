---
title: Funzionalità per doppio schermo di Xamarin.Forms
description: Questa guida spiega in che modo Xamarin.Forms consente di ottimizzare facilmente le app per dispositivi con doppio schermo.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 1f648297a608c592d90f2c70494ae4496fe73c0f
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145515"
---
# <a name="xamarinforms-dual-screen"></a>Doppio schermo Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) e Surface Neo (Windows 10X) introducono nuovi modelli per le applicazioni touch. Xamarin.Forms introduce `TwoPaneView` e `DualScreenInfo` per sfruttare appieno queste nuove esperienze.

## <a name="dual-screen-patternsdesign-patternsmd"></a>[Modelli per doppio schermo](design-patterns.md)

Quando si valuta come usare al meglio più schermi in un dispositivo con doppio schermo, fare riferimento alle linee guida per i modelli per trovare la soluzione migliore per l'interfaccia dell'applicazione.

## <a name="twopaneviewtwopaneviewmd"></a>[TwoPaneView](twopaneview.md)

La classe `TwoPaneView` Xamarin.Forms, ispirata dal controllo UWP con lo stesso nome, introduce un layout ottimizzato per i dispositivi con doppio schermo in modalità multipiattaforma.

## <a name="dualscreeninfodual-screen-infomd"></a>[DualScreenInfo](dual-screen-info.md)

Per sfruttare al meglio i dispositivi con doppio schermo, può essere utile conoscere il riquadro in cui si trova la visualizzazione, le dimensioni del riquadro, l'orientamento del dispositivo, l'angolo della cerniera e altro ancora. `DualScreenInfo` fornisce queste informazioni.

## <a name="dualscreenhelperdual-screen-helpermd"></a>[DualScreenHelper](dual-screen-helper.md)
`DualScreenHelper` consente di controllare se la piattaforma supporta l'apertura di una nuova finestra in modalità PIP (Picture-in-Picture). Per Neo questo consente di aprire una finestra che verrà visualizzata nella Wonder Bar quando il dispositivo è in modalità composizione.