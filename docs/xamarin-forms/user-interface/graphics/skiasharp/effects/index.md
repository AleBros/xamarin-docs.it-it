---
title: Effetti SkiaSharp
description: Informazioni su come modificare la visualizzazione normale della grafica con gradienti, affiancamento bitmap, modalità Blend, sfocatura e altri effetti.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B3E06572-8E2A-49FA-90D1-444C394CD516
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9fa710f5dfc61c2892b8fc409a39b37cf449018
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136305"
---
# <a name="skiasharp-effects"></a>Effetti SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La [`SKPaint`](xref:SkiaSharp.SKPaint) classe SkiaSharp definisce sei proprietà che possono essere classificate nel termine generale degli _effetti_. Si tratta di proprietà che modificano in qualche modo la visualizzazione normale della grafica. Gli effetti SkiaSharp rientrano in sei categorie:

## <a name="path-effects"></a>[Effetti per il tracciato](../curves/effects.md)

Impostare la [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) proprietà di `SKPaint` su un oggetto di tipo [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) per visualizzare le linee tratteggiate oppure per tracciare o riempire un'area con un criterio creato da percorsi. L'effetto del percorso è stato analizzato in precedenza in questa serie negli effetti sul percorso degli articoli [**in SkiaSharp**](../curves/effects.md).

## <a name="shaders"></a>[Shader](shaders/index.md)

Impostare la [`Shader`](xref:SkiaSharp.SKPaint.Shader) proprietà di `SKPaint` su un oggetto di tipo [`SKShader`](xref:SkiaSharp.SKShader) per visualizzare sfumature lineari o circolari, bitmap affiancate e modelli di disturbo Perlin.

## <a name="blend-modes"></a>[Modalità di fusione](blend-modes/index.md)

Impostare la [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) proprietà di `SKPaint` su un membro dell' [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) enumerazione per controllare cosa accade quando una rappresentazione grafica di origine viene visualizzata in una destinazione. SkiaSharp supporta tutte le modalità di composizione CSS e Blend, incluse le modalità Porter-Duff, le modalità di Blend separabili e le modalità di Blend non separabili.

## <a name="mask-filters"></a>[Filtri con maschera](mask-filters.md)

Impostare la [`MaskFilter`](xref:SkiaSharp.SKPaint.MaskFilter) proprietà di `SKPaint` su un oggetto di tipo [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) per le sfocature e altri effetti alfa.

## <a name="image-filters"></a>[Filtri di immagini](image-filters.md)

Impostare la [`ImageFilter`](xref:SkiaSharp.SKPaint.ImageFilter) proprietà di `SKPaint` su un oggetto di tipo [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) per la sfocatura delle bitmap e la creazione degli effetti di ombreggiatura, goffratura o incisione.

## <a name="color-filters"></a>[Filtri di colore](color-filters.md)

Impostare la [`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) proprietà di `SKPaint` su un oggetto di tipo [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) per modificare i colori utilizzando le tabelle o le trasformazioni di matrici.

Tutto il codice di esempio per questi articoli si trova in [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos). Dal home page selezionare **effetti SkiaSharp**.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
