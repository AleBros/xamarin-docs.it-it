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
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106182"
---
# <a name="xamarinforms-gestures"></a>Movimenti di Xamarin.Forms

_È possibile usare i sistemi di riconoscimento del movimento per rilevare l'interazione dell'utente con le viste in un'applicazione Xamarin.Forms._

La classe [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) di Xamarin.Forms supporta i movimenti di tocco, avvicinamento delle dita, panoramica e scorrimento rapido nelle istanze di [`View`](xref:Xamarin.Forms.View).

## <a name="adding-a-tap-gesture-recognizertapmd"></a>[Aggiunta di un sistema di riconoscimento del movimento di tocco](tap.md)

Il movimento di tocco viene usato per il rilevamento del tocco e viene riconosciuto con la classe [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).

## <a name="adding-a-pinch-gesture-recognizerpinchmd"></a>[Aggiunta di un sistema di riconoscimento del movimento di avvicinamento delle dita](pinch.md)

Il movimento di avvicinamento delle dita viene usato per lo zoom avanti interattivo e viene riconosciuto con la classe [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer).

## <a name="adding-a-pan-gesture-recognizerpanmd"></a>[Aggiunta di un sistema di riconoscimento del movimento di panoramica](pan.md)

Il movimento di panoramica viene usato per rilevare il movimento delle dita sullo schermo e applicare tale movimento al contenuto; viene riconosciuto con la classe [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer).

## <a name="adding-a-swipe-gesture-recognizerswipemd"></a>[Aggiunta di un sistema di riconoscimento del movimento di scorrimento rapido](swipe.md)

Il movimento di scorrimento rapido si verifica quando si muove un dito sullo schermo in senso orizzontale o verticale e viene spesso usato per avviare la navigazione nel contenuto. I movimenti di scorrimento rapido vengono riconosciuti con la classe [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer).
