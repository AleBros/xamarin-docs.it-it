---
title: Trasformazioni di SkiaSharp
description: Questo articolo esamina le trasformazioni per la visualizzazione grafica di SkiaSharp in applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: a57e50f098c92dbfcdcaa3139565d2ba0e291e3d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347765"
---
# <a name="skiasharp-transforms"></a>Trasformazioni di SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Informazioni sulle trasformazioni per la visualizzazione grafica di SkiaSharp_

SkiaSharp supporta trasformazioni di grafica tradizionale che vengono implementate come metodi del [ `SKCanvas` ](xref:SkiaSharp.SKCanvas) oggetto. Matematica, le trasformazioni modificano le coordinate e le dimensioni che specificano in `SKCanvas` disegno funzioni come gli oggetti grafici vengono sottoposti a rendering. Trasformazioni sono spesso utili per il disegno di grafica ripetitiva o per l'animazione. Alcune tecniche &mdash; , ad esempio la rotazione bitmap o text &mdash; non sono possibili senza l'uso di trasformazioni.

Trasformazioni di SkiaSharp supportano le operazioni seguenti:

- *Tradurre* alle coordinate di spostamento da una posizione a un'altra
- *Scalabilità* per aumentare o ridurre le coordinate e le dimensioni
- *Ruotare* per ruotare le coordinate intorno a un punto
- *Una differenza* da spostare coordina orizzontalmente o verticalmente in modo che un rettangolo diventa un parallelogramma

Si parla *affini* Trasforma. Le trasformazioni affini mantenere sempre linee parallele e non generano mai una coordinata o una dimensione diventi infinito. Un quadrato mai viene trasformato in un valore diverso da un parallelogramma, e un cerchio mai viene trasformato in un valore diverso da un'ellisse.

SkiaSharp supporta anche le trasformazioni non affini (chiamato anche *le divisioni proiettive delle* oppure *prospettiva* Trasforma) basato su una matrice di trasformazione da 3-3 standard. Una trasformazione non affine consente un quadrato da trasformare in qualsiasi quadrilatero convesso che è una figura i quattro lati con tutti gli angoli interni minori di 180 gradi. Le trasformazioni non affini possono causare coordinate o dimensioni diventare infinito, ma sono fondamentali per gli effetti 3D.

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>Differenze tra SkiaSharp e trasformazioni di xamarin. Forms

Xamarin. Forms supporta anche le trasformazioni che sono simili a quelle di SkiaSharp. Xamarin. Forms [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe definisce le proprietà di trasformazione seguenti:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation), [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX), e [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

Il `RotationX` e `RotationY` trasformazioni prospettiva che creano effetti poi-3D sono di proprietà.

Esistono alcune differenze fondamentali tra trasformazioni di SkiaSharp e trasformazioni di xamarin. Forms:

La prima differenza è che le trasformazioni di SkiaSharp vengono applicate all'intera `SKCanvas` dell'oggetto durante le trasformazioni di xamarin. Forms sono applicate al singolo `VisualElement` derivati. (È possibile applicare le trasformazioni di xamarin. Forms per il `SKCanvasView` dell'oggetto stesso, poiché `SKCanvasView` deriva da `VisualElement`, ma all'interno che `SKCanvasView`, applicano le trasformazioni SkiaSkarp.)

Le trasformazioni di SkiaSharp sono rispetto all'angolo superiore sinistro del `SKCanvas` durante le trasformazioni di xamarin. Forms sono rispetto all'angolo superiore sinistro del `VisualElement` al quale vengono applicati. Questa differenza è importante quando si applica la scalabilità e rotazione Trasforma perché queste trasformazioni sono sempre relativi a un momento specifico.

La realtà grande differenza è che sono trasformazioni di SKiaSharp *metodi* anche se sono le trasformazioni di xamarin. Forms *proprietà*. Questa è una differenza semantica oltre la differenza sintattica: Trasformazioni di SkiaSharp eseguono un'operazione mentre xamarin. Forms consente di trasformare set di uno stato. Trasformazioni di SkiaSharp si applicano agli oggetti di grafica disegnati successivamente, ma non agli oggetti di grafica disegnati prima che venga applicata la trasformazione. Al contrario, una trasformazione di xamarin. Forms viene applicato a un elemento precedentemente sottoposti a rendering, non appena la proprietà è impostata. Trasformazioni di SkiaSharp sono cumulative, come i metodi vengono chiamati; Trasformazioni di xamarin. Forms vengono sostituite quando la proprietà è impostata con un altro valore.

Tutti i programmi di esempio in questa sezione vengono visualizzati nella **trasformazioni di SkiaSharp** sezione il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programma. Codice sorgente è reperibile nel [ **trasforma** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms) cartella della soluzione.

## <a name="the-translate-transformtranslatemd"></a>[Trasformazione di traslazione](translate.md)

Informazioni su come usare la trasformazione di traslazione da spostare in SkiaSharp grafica.

## <a name="the-scale-transformscalemd"></a>[Trasformazione di ridimensionamento](scale.md)

Scopri la trasformazione di scala di SkiaSharp per ridimensionare gli oggetti per diverse dimensioni.

## <a name="the-rotate-transformrotatemd"></a>[Trasformazione di rotazione](rotate.md)

Esplorare gli effetti e animazioni possibili con la trasformazione di rotazione SkiaSharp.

## <a name="the-skew-transformskewmd"></a>[Trasformazione di inclinazione](skew.md)

Vedere come la trasformazione di inclinazione può creare inclinato gli oggetti grafici.

## <a name="matrix-transformsmatrixmd"></a>[Trasformazioni con matrice](matrix.md)

Approfondimenti trasformazioni di SkiaSharp con la matrice di trasformazione versatili.

## <a name="touch-manipulationstouchmd"></a>[Manipolazioni tramite tocco](touch.md)

Matrice di utilizzare le trasformazioni da implementare manipolazioni di tocco per il trascinamento, ridimensionamento e rotazione.

## <a name="non-affine-transformsnon-affinemd"></a>[Trasformazioni non affini](non-affine.md)

Andare oltre le oridinary con effetti di trasformazione non affine.

## <a name="3d-rotation3d-rotationmd"></a>[Rotazione 3D](3d-rotation.md)

Usare le trasformazioni non affini ruotare gli oggetti 2D nello spazio 3D.


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
