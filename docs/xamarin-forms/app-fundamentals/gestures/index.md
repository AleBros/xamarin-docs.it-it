---
title: Movimenti di Xamarin.Forms
description: Questa guida spiega come usare i sistemi di riconoscimento del movimento di Xamarin.Forms per rilevare l'interazione dell'utente con le viste in un'applicazione Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 0E197A51-2304-4C09-A710-C7FF24A89F15
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2018
ms.openlocfilehash: 33968fb935e8b69736ac338bfa0479e4f278e64a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "61184572"
---
# <a name="xamarinforms-gestures"></a>Movimenti di Xamarin.Forms

_È possibile usare i sistemi di riconoscimento del movimento per rilevare l'interazione dell'utente con le viste in un'applicazione Xamarin.Forms._

La classe Xamarin.Forms [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) supporta i movimenti di tocco, avvicinamento delle dita, panoramica e scorrimento rapido sulle [`View`](xref:Xamarin.Forms.View) istanze.

## <a name="adding-a-tap-gesture-recognizer"></a>[Aggiunta di un sistema di riconoscimento del gesto tocco](tap.md)

Un gesto tocco viene utilizzato per [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) il rilevamento tramite tocco e viene riconosciuto con la classe.

## <a name="adding-a-pinch-gesture-recognizer"></a>[Aggiunta di un sistema di riconoscimento del movimento di avvicinamento delle dita](pinch.md)

Un gesto di avvicinamento delle dita [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer) viene utilizzato per eseguire lo zoom interattivo e viene riconosciuto con la classe.

## <a name="adding-a-pan-gesture-recognizer"></a>[Aggiunta di un sistema di riconoscimento del movimento di panoramica](pan.md)

Un gesto di panoramica viene utilizzato per rilevare il movimento delle dita [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) intorno allo schermo e applicare tale movimento al contenuto e viene riconosciuto con la classe.

## <a name="adding-a-swipe-gesture-recognizer"></a>[Aggiunta di un sistema di riconoscimento del movimento di scorrimento rapido](swipe.md)

Il movimento di scorrimento rapido si verifica quando si muove un dito sullo schermo in senso orizzontale o verticale e viene spesso usato per avviare la navigazione nel contenuto. I gesti di scorrimento [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) rapido vengono riconosciuti con la classe.
