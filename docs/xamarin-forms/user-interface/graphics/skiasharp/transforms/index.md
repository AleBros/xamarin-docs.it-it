---
title: ''
description: In questo articolo vengono esaminate le trasformazioni per la visualizzazione della grafica SkiaSharp nelle Xamarin.Forms applicazioni e viene illustrato il codice di esempio.
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e20ea5d1d3f813b04a927601fbe1180ff39ed176
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140192"
---
# <a name="skiasharp-transforms"></a>Trasformazioni di SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Informazioni sulle trasformazioni per la visualizzazione di immagini SkiaSharp_

SkiaSharp supporta le trasformazioni grafiche tradizionali implementate come metodi dell' [`SKCanvas`](xref:SkiaSharp.SKCanvas) oggetto. In matematica, le trasformazioni modificano le coordinate e le dimensioni specificate nelle `SKCanvas` funzioni di disegno durante il rendering degli oggetti grafici. Le trasformazioni sono spesso utili per disegnare grafica ripetitiva o per l'animazione. Alcune tecniche &mdash; , ad esempio la rotazione di bitmap o testo, &mdash; non sono possibili senza l'uso di trasformazioni.

Le trasformazioni SkiaSharp supportano le operazioni seguenti:

- *Trasla* per spostare le coordinate da una posizione a un'altra
- *Ridimensionamento* per aumentare o ridurre le coordinate e le dimensioni
- *Ruota* per ruotare le coordinate intorno a un punto
- *Asimmetria* per spostare le coordinate orizzontalmente o verticalmente in modo che un rettangolo diventi un parallelogramma

Queste sono note come trasformazioni *affini* . Le trasformazioni affini mantengono sempre le linee parallele senza mai causare una coordinata o una dimensione infinita. Un quadrato non viene mai trasformato in un valore diverso da un parallelogramma e un cerchio non viene mai trasformato in un valore diverso da un'ellisse.

SkiaSharp supporta anche trasformazioni non affini, *denominate* anche trasformazioni *prospettiche o prospettiche* , basate su una matrice di trasformazione standard 3 per 3. Una trasformazione non affine consente la trasformazione di un quadrato in qualsiasi quadrilatero convesso, ovvero una figura a quattro lati con tutti gli angoli interni inferiori a 180 gradi. Le trasformazioni non affini possono comportare l'infinito delle coordinate o delle dimensioni, ma sono fondamentali per gli effetti 3D.

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>Differenze tra SkiaSharp e Xamarin.Forms transforms

Xamarin.Formssupporta inoltre le trasformazioni simili a quelle presenti in SkiaSharp. La Xamarin.Forms [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe definisce le proprietà di trasformazione seguenti:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)e[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation), [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) e[`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

Le `RotationX` `RotationY` proprietà e sono trasformazioni prospettiche che creano effetti quasi 3D.

Esistono diverse differenze cruciali tra le trasformazioni e le trasformazioni di SkiaSharp Xamarin.Forms :

La prima differenza è che le trasformazioni SkiaSharp vengono applicate all'intero `SKCanvas` oggetto mentre le Xamarin.Forms trasformazioni vengono applicate a singoli `VisualElement` derivati. È possibile applicare le Xamarin.Forms trasformazioni all' `SKCanvasView` oggetto stesso, dal momento `SKCanvasView` che deriva da `VisualElement` , ma all'interno di esse si `SKCanvasView` applicano le trasformazioni SkiaSkarp.

Le trasformazioni SkiaSharp sono relative all'angolo superiore sinistro del `SKCanvas` mentre le Xamarin.Forms trasformazioni sono relative all'angolo superiore sinistro dell'oggetto `VisualElement` a cui vengono applicate. Questa differenza è importante quando si applicano trasformazioni di ridimensionamento e rotazione perché queste trasformazioni sono sempre relative a un particolare punto.

La differenza principale consiste nel fatto che le trasformazioni SKiaSharp sono *Metodi* mentre le Xamarin.Forms trasformazioni sono *proprietà*. Si tratta di una differenza semantica oltre la differenza sintattica: le trasformazioni SkiaSharp eseguono un'operazione mentre le Xamarin.Forms trasformazioni impostano uno stato. Le trasformazioni SkiaSharp si applicano agli oggetti grafici creati successivamente, ma non agli oggetti grafici disegnati prima dell'applicazione della trasformazione. Al contrario, una Xamarin.Forms trasformazione si applica a un elemento precedentemente sottoposto a rendering non appena la proprietà è impostata. Le trasformazioni SkiaSharp sono cumulative quando vengono chiamati i metodi; Xamarin.Formsle trasformazioni vengono sostituite quando la proprietà è impostata con un altro valore.

Tutti i programmi di esempio in questa sezione vengono visualizzati nella sezione **SkiaSharp Transformations** del programma [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Il codice sorgente è reperibile nella cartella [**Transformations**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms) della soluzione.

## <a name="the-translate-transform"></a>[Trasformazione di traslazione](translate.md)

Informazioni su come usare la trasformazione translate per spostare la grafica SkiaSharp.

## <a name="the-scale-transform"></a>[Trasformazione di ridimensionamento](scale.md)

Individuare la trasformazione scalabilità SkiaSharp per ridimensionare gli oggetti a diverse dimensioni.

## <a name="the-rotate-transform"></a>[Trasformazione di rotazione](rotate.md)

Esplorare gli effetti e le animazioni possibili con la trasformazione rotazione SkiaSharp.

## <a name="the-skew-transform"></a>[Trasformazione di inclinazione](skew.md)

Vedere in che modo la trasformazione asimmetria può creare un oggetto grafico inclinato.

## <a name="matrix-transforms"></a>[Trasformazioni con matrice](matrix.md)

Approfondimenti sulle trasformazioni SkiaSharp con la matrice di trasformazione versatile.

## <a name="touch-manipulations"></a>[Manipolazioni tramite tocco](touch.md)

Usare le trasformazioni matrice per implementare le manipolazioni del tocco per il trascinamento, il ridimensionamento e la rotazione.

## <a name="non-affine-transforms"></a>[Trasformazioni non affini](non-affine.md)

Superare la oridinary con effetti di trasformazione non affini.

## <a name="3d-rotation"></a>[Rotazione 3D](3d-rotation.md)

Utilizzare trasformazioni non affini per ruotare gli oggetti 2D nello spazio 3D.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
