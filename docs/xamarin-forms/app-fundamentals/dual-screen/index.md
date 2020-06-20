---
title: Xamarin.FormsDoppio schermo
description: Questa guida illustra come creare Xamarin.Forms app per dispositivi a doppio schermo.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aeaaeb732adaea45446d6baf833027801abf4d2a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138905"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Formsdoppio schermo

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) e Surface Neo (Windows 10X) introducono nuovi modelli per le applicazioni touch. Xamarin.Formsinclude `TwoPaneView` `DualScreenInfo` le classi e, in modo che sia possibile sviluppare app per questi dispositivi.

## <a name="dual-screen-design-patterns"></a>[Modelli di progettazione a doppio schermo](design-patterns.md)

Quando si valuta come usare al meglio più schermi in un dispositivo con doppio schermo, fare riferimento a queste linee guida per i modelli per trovare la soluzione migliore per l'interfaccia dell'applicazione.

## <a name="dual-screen-layout"></a>[Layout per doppio schermo](twopaneview.md)

La Xamarin.Forms `TwoPaneView` classe, ispirata dal controllo UWP con lo stesso nome, è un layout multipiattaforma ottimizzato per i dispositivi a doppio schermo.

## <a name="dual-screen-device-capabilities"></a>[Funzionalità del dispositivo per doppio schermo](dual-screen-info.md)

La classe `DualScreenInfo` consente di determinare il riquadro in cui si trova la visualizzazione, le dimensioni del riquadro, l'orientamento del dispositivo, l'angolo della cerniera e altro ancora.

## <a name="dual-screen-platform-helpers"></a>[Helper per la piattaforma per doppio schermo](dual-screen-helper.md)

La classe `DualScreenHelper` consente di controllare se la piattaforma supporta l'apertura di una nuova finestra in modalità PIP (Picture-in-Picture). Per Neo questo consente di aprire una finestra che verrà visualizzata nella Wonder Bar quando il dispositivo è in modalità composizione.

## <a name="dual-screen-triggers"></a>[Trigger per doppio schermo](triggers.md)

Lo [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) spazio dei nomi include due trigger di stato che attivano una [`VisualState`](xref:Xamarin.Forms.VisualState) modifica quando viene modificata la modalità di visualizzazione del layout collegato o della finestra.
