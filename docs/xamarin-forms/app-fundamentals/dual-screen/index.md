---
title: Doppio schermo Xamarin.Forms
description: Questa guida spiega in che modo creare app Xamarin.Forms per dispositivi con doppio schermo.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 344b6293090ffa4281ea6351f7f176a5be37e5bd
ms.sourcegitcommit: 0e35d3eafad833d3f19768b001bd804ddda8b69b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165560"
---
# <a name="xamarinforms-dual-screen"></a>Doppio schermo Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) e Surface Neo (Windows 10X) introducono nuovi modelli per le applicazioni touch. Xamarin.Forms include le classi `TwoPaneView` e `DualScreenInfo`, in modo che sia possibile sviluppare app per questi dispositivi.

## <a name="dual-screen-design-patterns"></a>[Modelli di progettazione a doppio schermo](design-patterns.md)

Quando si valuta come usare al meglio più schermi in un dispositivo con doppio schermo, fare riferimento a queste linee guida per i modelli per trovare la soluzione migliore per l'interfaccia dell'applicazione.

## <a name="dual-screen-layout"></a>[Layout a doppio schermo](twopaneview.md)

La classe `TwoPaneView` Xamarin.Forms, ispirata dal controllo UWP con lo stesso nome, è un layout multipiattaforma ottimizzato per i dispositivi con doppio schermo.

## <a name="dual-screen-device-capabilities"></a>[Funzionalità del dispositivo dual screen](dual-screen-info.md)

La classe `DualScreenInfo` consente di determinare il riquadro in cui si trova la visualizzazione, le dimensioni del riquadro, l'orientamento del dispositivo, l'angolo della cerniera e altro ancora.

## <a name="dual-screen-platform-helpers"></a>[Helper della piattaforma dual screen](dual-screen-helper.md)

La classe `DualScreenHelper` consente di controllare se la piattaforma supporta l'apertura di una nuova finestra in modalità PIP (Picture-in-Picture). Per Neo questo consente di aprire una finestra che verrà visualizzata nella Wonder Bar quando il dispositivo è in modalità composizione.

## <a name="dual-screen-triggers"></a>[Trigger a doppio schermo](triggers.md)

Lo spazio dei nomi [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) include due trigger di stato che attivano una modifica [`VisualState`](xref:Xamarin.Forms.VisualState) quando viene modificata la modalità di visualizzazione del layout collegato o della finestra.
