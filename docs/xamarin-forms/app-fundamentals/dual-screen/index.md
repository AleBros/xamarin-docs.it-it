---
title: Doppio schermo Xamarin.Forms
description: Questa guida spiega in che modo creare app Xamarin.Forms per dispositivi con doppio schermo.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 41a7ef4e447bb71582264b4e73629566d3ffd4e7
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77480515"
---
# <a name="xamarinforms-dual-screen"></a>Doppio schermo Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) e Surface Neo (Windows 10X) introducono nuovi modelli per le applicazioni touch. Xamarin.Forms include le classi `TwoPaneView` e `DualScreenInfo`, in modo che sia possibile sviluppare app per questi dispositivi.

## <a name="dual-screen-patterns"></a>[Modelli per doppio schermo](design-patterns.md)

Quando si valuta come usare al meglio più schermi in un dispositivo con doppio schermo, fare riferimento a queste linee guida per i modelli per trovare la soluzione migliore per l'interfaccia dell'applicazione.

## <a name="twopaneview"></a>[TwoPaneView](twopaneview.md)

La classe `TwoPaneView` Xamarin.Forms, ispirata dal controllo UWP con lo stesso nome, è un layout multipiattaforma ottimizzato per i dispositivi con doppio schermo.

## <a name="dualscreeninfo"></a>[DualScreenInfo](dual-screen-info.md)

La classe `DualScreenInfo` consente di determinare il riquadro in cui si trova la visualizzazione, le dimensioni del riquadro, l'orientamento del dispositivo, l'angolo della cerniera e altro ancora.

## <a name="dualscreenhelper"></a>[DualScreenHelper](dual-screen-helper.md)

La classe `DualScreenHelper` consente di controllare se la piattaforma supporta l'apertura di una nuova finestra in modalità PIP (Picture-in-Picture). Per Neo questo consente di aprire una finestra che verrà visualizzata nella Wonder Bar quando il dispositivo è in modalità composizione.
