---
title: Disegno 2D
description: Cross Platform disegno 2D con SkiaSharp
ms.prod: xamarin
ms.assetid: A8A61421-4544-422A-A7E0-9355C67DF21E
author: charlespetzold
ms.author: chape
ms.date: 09/14/2017
ms.openlocfilehash: f1a40b48ecfb7244aae77cf1b4110ae53490d98c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="2d-drawing"></a>Disegno 2D

SkiaSharp fornisce un'API di potenti c# per l'esecuzione di grafica 2D. È alimentato dalle [libreria Skia Google](http://skia.org), la stessa libreria che alimenta stack grafico Google Chrome, Firefox e di Android.

[![](images/ide-sml.png "SkiaSharp fornisce un'API di potenti c# per l'esecuzione di grafica 2D")](images/ide.png#lightbox)

È una libreria portabile SkiaSharp e viene fornita per praticità come un [pacchetto NuGet multipiattaforma](https://www.nuget.org/packages/SkiaSharp)e supporta le seguenti piattaforme predefinita: macOS, Desktop di Windows, xamarin. IOS e xamarin.

## <a name="introduction-to-skiasharpgraphics-gamesskiasharpintroductionmd"></a>[Introduzione a SkiaSharp](~/graphics-games/skiasharp/introduction.md)

Una panoramica dei concetti principali di esempio e SkiaSharp codice per eseguire il rendering di grafica, testo, bitmap e utilizzare i filtri di immagine.

## <a name="skiasharp-tutorials-for-xamarinformsxamarin-formsuser-interfacegraphicsskiasharpindexmd"></a>[Esercitazioni SkiaSharp per xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)

Acquisire familiarità con cross platform grafica che eseguono il rendering in xamarin. Forms:

- [Nozioni fondamentali di disegno](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)
  * [Disegnare un cerchio semplice](~/xamarin-forms/user-interface/graphics/skiasharp/basics/circle.md)
  * [Integrazione con Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)
  * [Pixel e unità indipendenti dal dispositivo](~/xamarin-forms/user-interface/graphics/skiasharp/basics/pixels.md)
  * [Animazione di base](~/xamarin-forms/user-interface/graphics/skiasharp/basics/animation.md)
  * [L'integrazione di testo e grafica](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)
  * [Nozioni fondamentali di bitmap](~/xamarin-forms/user-interface/graphics/skiasharp/basics/bitmaps.md)
- [Le righe e percorsi](~/xamarin-forms/user-interface/graphics/skiasharp/paths/index.md)
  * [Le righe e maiuscole tratto](~/xamarin-forms/user-interface/graphics/skiasharp/paths/lines.md)
  * [Nozioni fondamentali di percorso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)
  * [I tipi di riempimento del percorso](~/xamarin-forms/user-interface/graphics/skiasharp/paths/fill-types.md)
  * [Polilinea ed equazioni parametriche](~/xamarin-forms/user-interface/graphics/skiasharp/paths/polylines.md)
  * [Punti e trattini](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)
  * [Disegno con le dita](~/xamarin-forms/user-interface/graphics/skiasharp/paths/finger-paint.md)
- [Trasformazioni](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/index.md)
  * [La trasformazione di conversione](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)
  * [La trasformazione di scala](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/scale.md)
  * [La trasformazione rotativa](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)
  * [La trasformazione di inclinazione](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/skew.md)
  * [Trasformazioni di matrice](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md)
  * [Manipolazioni tocco](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)
  * [Trasformazioni affini non](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)
  * [Rotazione 3D](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)
- [Curve e percorsi](~/xamarin-forms/user-interface/graphics/skiasharp/curves/index.md)
  * [Tre modi per disegnare un arco](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)
  * [Tre tipi di curve di Bézier](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)
  * [Dati del tracciato SVG](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)
  * [Ritaglio con tracciati e aree](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md)
  * [Effetti per il tracciato](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)
  * [Tracciati e testo](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)
  * [Informazioni sui tracciati ed enumerazione](~/xamarin-forms/user-interface/graphics/skiasharp/curves/information.md)

## <a name="platform-specific-notesgraphics-gamesskiasharpplatformmd"></a>[Note specifiche della piattaforma](~/graphics-games/skiasharp/platform.md)

Questa pagina vengono descritti le istruzioni di installazione per SkiaSharp su piattaforme diverse, tra cui iOS, Android, macOS e Windows.

## <a name="api-documentationhttpsdeveloperxamarincomapinamespaceskiasharp"></a>[Documentazione dell'API](https://developer.xamarin.com/api/namespace/SkiaSharp/)

È possibile esplorare il [documentazione dell'API](https://developer.xamarin.com/api/namespace/SkiaSharp/) per SkiaSharp sul sito web.

## <a name="work-in-progress"></a>Stato di avanzamento lavoro

SkiaSharp è un lavoro in corso che si sta condividendo con la nostra community. Anche se è stato associato importanti componenti dell'API Skia, lavoro rimanente da eseguire. Si sta usando l'API C stabile viene rilevata dal Skia e il piano è parte del lavoro per le associazioni di C di Skia per fornire una copertura completa per le API di continuare.

Per consentire agli utenti guidato dedicare i nostri sforzi associazione, lasciare commenti o suggerimenti come problemi nel repository GitHub [ http://github.com/mono/SkiaSharp ](http://github.com/mono/SkiaSharp).
