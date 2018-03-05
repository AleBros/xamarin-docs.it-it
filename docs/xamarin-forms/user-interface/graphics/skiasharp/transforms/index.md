---
title: Trasformazioni SkiaSharp
description: Informazioni sulle trasformazioni per la visualizzazione della grafica SkiaSharp
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 06db59f5585671342ef7dbaa2d2cf14ec6830c41
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="skiasharp-transforms"></a>Trasformazioni SkiaSharp

_Informazioni sulle trasformazioni per la visualizzazione della grafica SkiaSharp_

SkiaSharp supporta le trasformazioni di grafica tradizionale che vengono implementate come metodi di [ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/) oggetto. Matematicamente, trasformazioni alter le coordinate e le dimensioni specificate nella `SKCanvas` funzioni di disegno, come gli oggetti grafici vengono sottoposti a rendering. Le trasformazioni sono spesso utile per la creazione di grafica ripetitive o per l'animazione. Alcune tecniche & #x 2014; ad esempio la rotazione di immagini bitmap o testo & #x 2014; non sono possibili senza l'utilizzo delle trasformazioni.

Trasformazioni SkiaSharp supportano le operazioni seguenti:

- *Tradurre* MAIUSC nelle coordinate da una posizione a un'altra
- *Scala* per aumentare o ridurre le coordinate e le dimensioni
- *Ruota* per ruotare le coordinate intorno a un punto
- *Inclinare* per spostare le coordinate orizzontalmente o verticalmente in modo che un rettangolo diventa un parallelogramma

Questi sono conosciuti come *affine* Trasforma. Le trasformazioni affini mantenere sempre linee parallele e non generano mai una coordinata o una dimensione diventi infinito. Un quadrato mai viene trasformato in un valore diverso da un parallelogramma, e un cerchio mai viene trasformato in un valore diverso da un'ellisse.

SkiaSharp supporta inoltre le trasformazioni non affini (detto anche *le divisioni proiettive delle* o *prospettiva* Trasforma) in base a una matrice di trasformazione di 3 per 3 standard. Una trasformazione non affini consente un quadrato con un qualsiasi quadrilatero convesso (quattro lati figura con tutti gli angoli interni minore di 180 gradi). Trasformazioni affini non è possono che le coordinate o dimensioni diventi infinito, ma sono fondamentali per ottenere effetti 3D.

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>Differenze tra SkiaSharp e trasformazioni di xamarin. Forms

Xamarin. Forms supporta inoltre le trasformazioni che sono simili a quelle di SkiaSharp. Di xamarin. Forms [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) classe definisce le proprietà di trasformazione seguenti:

- `TranslationX` e `TranslationY`
- `Scale`
- `Rotation`, `RotationX` e `RotationY`

Il `RotationX` e `RotationY` trasformazioni prospettiva che creano effetti quasi 3D sono di proprietà.

Esistono alcune differenze fondamentali tra SkiaSharp trasformazioni e trasformazioni di xamarin. Forms:

La prima è che SkiaSharp vengono applicate all'intero `SKCanvas` oggetto mentre si applicano le trasformazioni di xamarin. Forms per singoli `VisualElement` derivati. (È possibile applicare le trasformazioni di xamarin. Forms per il `SKCanvasView` oggetto stesso, in quanto `SKCanvasView` deriva da `VisualElement`, ma all'interno che `SKCanvasView`, si applicano le trasformazioni SkiaSkarp.)

Le trasformazioni SkiaSharp sono rispetto all'angolo superiore sinistro del `SKCanvas` durante le trasformazioni di xamarin. Forms rispetto all'angolo superiore sinistro del `VisualElement` a cui vengono applicati. Questa differenza è importante quando si applica il ridimensionamento e rotazione Trasforma perché queste trasformazioni sono sempre rispetto a un punto specifico.

La differenza di grandi dimensioni è che le trasformazioni SKiaSharp sono *metodi* durante le trasformazioni di xamarin. Forms *proprietà*. Si tratta di una differenza semantica oltre la differenza di sintassi: SkiaSharp trasformazioni eseguono un'operazione durante uno stato del set di trasformazioni di xamarin. Forms. SkiaSharp trasformazioni si applicano agli oggetti di grafica disegnati successivamente, ma non a oggetti di grafica disegnati prima che venga applicata la trasformazione. Al contrario, una trasformazione di xamarin. Forms si applica a un elemento precedentemente sottoposti a rendering come la proprietà è impostata. Trasformazioni SkiaSharp sono cumulative, come i metodi vengono chiamati; Quando la proprietà è impostata con un altro valore, vengono sostituite le trasformazioni di xamarin. Forms.

Tutti i programmi di esempio in questa sezione appaiono sotto l'intestazione **trasforma** nella home page del [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programma e il [ **Trasforma** ](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms) cartella della soluzione.

## <a name="the-translate-transformtranslatemd"></a>[Trasformazione di traslazione](translate.md)

Informazioni su come utilizzare la trasformazione di traslazione per spostare SkiaSharp grafica.

## <a name="the-scale-transformscalemd"></a>[Trasformazione di ridimensionamento](scale.md)

Individuare la trasformazione di scala SkiaSharp per ridimensionare gli oggetti per diverse dimensioni.

## <a name="the-rotate-transformrotatemd"></a>[Trasformazione di rotazione](rotate.md)

Esplorare gli effetti e animazioni possibili con la trasformazione di rotazione SkiaSharp.

## <a name="the-skew-transformskewmd"></a>[Trasformazione di inclinazione](skew.md)

Vedere come la trasformazione di inclinazione creare gli oggetti grafici inclinati in SkiaSharp.

## <a name="matrix-transformsmatrixmd"></a>[Trasformazioni con matrice](matrix.md)

Esaminare i SkiaSharp trasformazioni con la matrice di trasformazione versatile.

## <a name="touch-manipulationstouchmd"></a>[Manipolazioni tramite tocco](touch.md)

Matrice di utilizzare Trasforma per implementare le modifiche di tocco per il trascinamento, scalabilità e la rotazione.

## <a name="non-affine-transformsnon-affinemd"></a>[Trasformazioni non affini](non-affine.md)

Andare oltre il oridinary con effetti di trasformazione non affini.

## <a name="3d-rotation3d-rotationmd"></a>[Rotazione 3D](3d-rotation.md)

Utilizzare le trasformazioni non affini per ruotare gli oggetti 2D nello spazio 3D.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
