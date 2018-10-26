---
title: Movimenti di xamarin. Forms
description: Questa guida illustra come i riconoscitori di movimento di xamarin. Forms possono essere usati per rilevare l'interazione dell'utente con le visualizzazioni in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
ms.openlocfilehash: 33968fb935e8b69736ac338bfa0479e4f278e64a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106182"
---
# <a name="xamarinforms-gestures"></a>Movimenti di xamarin. Forms

_I riconoscitori di movimento possono essere utilizzati per rilevare l'interazione dell'utente con le visualizzazioni in un'applicazione xamarin. Forms._

Xamarin. Forms [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer) classe supporta tap, zoom indietro, pan e i movimenti di scorrere rapidamente su [ `View` ](xref:Xamarin.Forms.View) istanze.

## <a name="adding-a-tap-gesture-recognizertapmd"></a>[Aggiunta di un riconoscimento di movimento tap](tap.md)

Un gesto tocco viene usato per il rilevamento di tocco e viene riconosciuto con il [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) classe.

## <a name="adding-a-pinch-gesture-recognizerpinchmd"></a>[Aggiunta di un sistema di riconoscimento di movimento zoom indietro](pinch.md)

Un movimento zoom indietro viene utilizzato per l'esecuzione interattivo zoom e viene riconosciuto con il [ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer) classe.

## <a name="adding-a-pan-gesture-recognizerpanmd"></a>[Aggiunta di un riconoscitore di movimento di zoom](pan.md)

Un movimento di zoom viene utilizzato per rilevare il movimento della dita sullo schermo e applicare lo spostamento al contenuto e viene riconosciuto con il [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) classe.

## <a name="adding-a-swipe-gesture-recognizerswipemd"></a>[Aggiunta di un riconoscimento di movimento scorrimento rapido](swipe.md)

Un gesto di scorrimento rapido si verifica quando un dito viene spostato sullo schermo in senso orizzontale o verticale e viene spesso usato per avviare la navigazione tra il contenuto. Vengono riconosciuti i movimenti di scorrere rapidamente con il [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) classe.
