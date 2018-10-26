---
title: Effetti di SkiaSharp
description: Informazioni su come modificare la visualizzazione normale della grafica con sfumature, bitmap per l'affiancamento, le modalità di blend, blur e altri effetti.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B3E06572-8E2A-49FA-90D1-444C394CD516
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
ms.openlocfilehash: 768fd0e1cd5ed08f42310d9aaf5993de7ec32e6b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131457"
---
# <a name="skiasharp-effects"></a>Effetti di SkiaSharp

Di SkiaSharp [ `SKPaint` ](xref:SkiaSharp.SKPaint) classe definisce sei proprietà che possono essere classificate con il termine generale del _effetti_. Queste sono proprietà che modificano la visualizzazione di grafica in qualche modo normale. Gli effetti di SkiaSharp rientrano in sei categorie:

## <a name="path-effectscurveseffectsmd"></a>[Effetti per il tracciato](../curves/effects.md)

Impostare il [ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect) proprietà della `SKPaint` a un oggetto di tipo [ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect) per visualizzare le linee tratteggiate, oppure per tracciare o un'area riempita con un modello creato da percorsi. L'effetto del percorso è stato analizzato in precedenza in questa serie in questo articolo [ **effetti per il tracciato in SkiaSharp**](../curves/effects.md).

## <a name="shadersshadersindexmd"></a>[Shader](shaders/index.md)

Impostare il [ `Shader` ](xref:SkiaSharp.SKPaint.Shader) proprietà della `SKPaint` a un oggetto di tipo [ `SKShader` ](xref:SkiaSharp.SKShader) per la visualizzazione delle sfumature lineari o circolare, bitmap affiancata e Perlin noise modelli.

## <a name="blend-modesblend-modesindexmd"></a>[Modalità di blend](blend-modes/index.md)

Impostare il [ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode) proprietà della `SKPaint` a un membro del [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode) enumerazione per gestire ciò che accade quando viene visualizzato un grafico di origine in una destinazione. SkiaSharp supporta tutte le CSS la composizione e blend modalità, tra cui la modalità blend non separabili, i metodi di fusione separabili e modalità Porter Duff.

## <a name="mask-filtersmask-filtersmd"></a>[Filtri di maschera](mask-filters.md)

Impostare il [ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter) proprietà della `SKPaint` a un oggetto di tipo [ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter) per le sfumature e altri effetti alfa.

## <a name="image-filtersimage-filtersmd"></a>[Filtri immagini](image-filters.md)

Impostare il [ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter) proprietà della `SKPaint` a un oggetto di tipo [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) per bitmap di sfocatura e la creazione di ombreggiature, rilievo o incisione effetti.

## <a name="color-filterscolor-filtersmd"></a>[Filtri di colore](color-filters.md)

Impostare il [ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter) proprietà della `SKPaint` a un oggetto di tipo [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) per modificare i colori usano tabelle o nelle trasformazioni di matrice.

Tutto il codice di esempio di codice per questi articoli sono nel [ **SkiaSharpFormsDemos**](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/). Dalla home page, selezionare **effetti SkiaSharp**.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
