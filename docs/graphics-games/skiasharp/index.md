---
title: 2D disegno con SkiaSharp
description: Questo documento viene fornita una panoramica di 2D multipiattaforma disegno con SkiaSharp. È collegato a diverse guide che descrivono SkiaSharp e le relative API diverse.
ms.prod: xamarin
ms.assetid: A8A61421-4544-422A-A7E0-9355C67DF21E
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: 0c8cbc14308c8c4131e5aaa2bcc0ddfa798af610
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130920"
---
# <a name="2d-drawing-with-skiasharp"></a>2D disegno con SkiaSharp

SkiaSharp fornisce un'API c# ed efficace per l'esecuzione di grafica 2D. È alimentato dalle [libreria Skia Google](http://skia.org), la stessa libreria che consente il funzionamento gli stack di grafici di Google Chrome, Firefox e di Android.

[![](images/ide-sml.png "SkiaSharp fornisce un'API c# ed efficace per l'esecuzione di grafica 2D")](images/ide.png#lightbox)

SkiaSharp è una libreria portabile e viene fornito per praticità come un [pacchetto NuGet multipiattaforma](https://www.nuget.org/packages/SkiaSharp)e supporta le seguenti piattaforme predefinite: macOS, xamarin. Android, xamarin. IOS e Windows Desktop.

## <a name="introduction-to-skiasharpgraphics-gamesskiasharpintroductionmd"></a>[Introduzione a SkiaSharp](~/graphics-games/skiasharp/introduction.md)

Una panoramica dei concetti di base di SkiaSharp ed esempio di codice per eseguire il rendering della grafica, testo, bitmap e usare i filtri di immagine.

## <a name="skiasharp-tutorials-for-xamarinformsxamarin-formsuser-interfacegraphicsskiasharpindexmd"></a>[Esercitazioni di SkiaSharp per xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)

Informazioni su come lavorare con cross-platform grafica che eseguono il rendering in xamarin. Forms:

- [Nozioni di base di disegno](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)
  * [Disegno di un cerchio semplice](~/xamarin-forms/user-interface/graphics/skiasharp/basics/circle.md)
  * [Integrazione con Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)
  * [Pixel e unità indipendenti dal dispositivo](~/xamarin-forms/user-interface/graphics/skiasharp/basics/pixels.md)
  * [Animazione di base](~/xamarin-forms/user-interface/graphics/skiasharp/basics/animation.md)
  * [L'integrazione di testo e grafica](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)
  * [Nozioni di base di bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/basics/bitmaps.md)
- [Linee e tracciati](~/xamarin-forms/user-interface/graphics/skiasharp/paths/index.md)
  * [Linee ed estremità dei tratti](~/xamarin-forms/user-interface/graphics/skiasharp/paths/lines.md)
  * [Nozioni di base di percorso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)
  * [I tipi di riempimento del percorso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/fill-types.md)
  * [Polilinee ed equazioni parametriche](~/xamarin-forms/user-interface/graphics/skiasharp/paths/polylines.md)
  * [Punti e trattini](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)
  * [Disegno con le dita](~/xamarin-forms/user-interface/graphics/skiasharp/paths/finger-paint.md)
- [Trasformazioni](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/index.md)
  * [La trasformazione di traslazione](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)
  * [Trasformazione di ridimensionamento](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/scale.md)
  * [Trasformazione di rotazione](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)
  * [Trasformazione di inclinazione](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/skew.md)
  * [Trasformazioni con matrice](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md)
  * [Manipolazioni tramite tocco](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)
  * [Trasformazioni non affini](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)
  * [Rotazione 3D](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)
- [Curve e tracciati](~/xamarin-forms/user-interface/graphics/skiasharp/curves/index.md)
  * [Tre modi per disegnare un arco](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)
  * [Tre tipi di curve di Bézier](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)
  * [Dati del tracciato SVG](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)
  * [Ritaglio con tracciati e aree](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md)
  * [Effetti per il tracciato](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)
  * [Tracciati e testo](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)
  * [Informazioni sui tracciati ed enumerazione](~/xamarin-forms/user-interface/graphics/skiasharp/curves/information.md)
- [Bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/index.md)
  * [Visualizzazione di bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/displaying.md)
  * [Creazione e disegnare sulla bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/drawing.md)
  * [Il ritaglio di bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/cropping.md)
  * [Visualizzazione segmentata di bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/segmented.md)
  * [Salvataggio di bitmap per i file](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/saving.md)
  * [L'accesso ai bit di Pixel Bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/pixel-bits.md)
  * [Animazione di bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/animating.md)

## <a name="platform-specific-notesgraphics-gamesskiasharpplatformmd"></a>[Note specifiche della piattaforma](~/graphics-games/skiasharp/platform.md)

Questa pagina descrive le istruzioni di installazione di SkiaSharp in diverse piattaforme, tra cui iOS, Android, macOS e Windows.

## <a name="api-documentationhttpsdeveloperxamarincomapinamespaceskiasharp"></a>[Documentazione dell'API](https://developer.xamarin.com/api/namespace/SkiaSharp/)

È possibile esplorare le [documentazione dell'API](https://developer.xamarin.com/api/namespace/SkiaSharp/) per SkiaSharp sul nostro sito web.

## <a name="work-in-progress"></a>Stato di avanzamento lavoro

SkiaSharp è in corso che viene condiviso con la community. Anche se è stata associata parti importanti dell'API Skia, lavoro rimanente per essere eseguita. Si sta usando l'API C stabile presentato dal Skia e il nostro piano consiste nel continuare aggiunta come contributo nostro lavoro per le associazioni di C di Skia per fornire una copertura completa per le API.

Per aiutarci a guidare i nostri sforzi di associazione, inviaci commenti o suggerimenti come problemi nel repository di GitHub [ http://github.com/mono/SkiaSharp ](http://github.com/mono/SkiaSharp).
