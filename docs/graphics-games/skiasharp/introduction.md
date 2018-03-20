---
title: Introduzione a SkiaSharp
description: Fornisce una breve introduzione ai concetti di SkiaSharp
ms.topic: article
ms.prod: xamarin
ms.assetid: 19506F08-2603-465E-A806-6BD01638DE90
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 09/14/2017
ms.openlocfilehash: 747c158460b23b91e4c2986d212802528cdd3979
ms.sourcegitcommit: cc38757f56aab53bce200e40f873eb8d0e5393c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2018
---
# <a name="an-introduction-to-skiasharp"></a>Introduzione a SkiaSharp

_Fornisce una breve introduzione ai concetti di SkiaSharp_

SkiaSharp fornisce un grafico 2D potente e ricco di API che può essere usato per eseguire il rendering nei buffer 2D.  È possibile utilizzare questi implementare gli elementi dell'interfaccia utente personalizzata e grafici 2D che possono essere incorporati nell'applicazione.  SkiaSharp è un'associazione di .NET per la [Skia](https://skia.org) libreria ed eredita le funzionalità della raccolta.

La libreria è attualmente disponibile come una libreria multipiattaforma [pacchetto NuGet](https://www.nuget.org/packages/SkiaSharp), è possibile aggiungere al progetto aggiungendo il riferimento di NuGet.

Per disegnare, il codice verrà creato un `SkCanvas` che descrive l'area in cui le operazioni di disegno verrà eseguita.

## <a name="obtaining-an-skcanvas"></a>Ottenere un SKCanvas

```csharp
using (var surface = SKSurface.Create (width: 640, height: 480, SKImageInfo.PlatformColorType, SKAlphaType.Premul)) {
    SKCanvas myCanvas = surface.Canvas;

    // Your drawing code goes here.
}
```

## <a name="drawing-on-skcanvas"></a>Disegno su SKCanvas

Il `SKCanvas` utilizza un modello di disegno simile a un altro disegno bevande modelli che è possibile avere familiarità con, Usa i colori con un canale di trasparenza facoltativo e può disegnare linee, archi, testo e immagini.

Di seguito sono riportate alcune di molti elementi diversi che possono essere eseguite con SkiaSharp.  Negli esempi riportati di seguito la variabile `canvas` è di tipo SKCanvas.

### <a name="drawing-xamagon"></a>Disegno Xamagon

In questo esempio consente di tracciare il logo di Xamarin il Xamagon:

```csharp
// clear the canvas / fill with white
canvas.Clear (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x2c, 0x3e, 0x50);
  paint.StrokeCap = SKStrokeCap.Round;

  // create the Xamagon path
  using (var path = new SKPath ()) {
    path.MoveTo (71.4311121f, 56f);
    path.CubicTo (68.6763107f, 56.0058575f, 65.9796704f, 57.5737917f, 64.5928855f, 59.965729f);
    path.LineTo (43.0238921f, 97.5342563f);
    path.CubicTo (41.6587026f, 99.9325978f, 41.6587026f, 103.067402f, 43.0238921f, 105.465744f);
    path.LineTo (64.5928855f, 143.034271f);
    path.CubicTo (65.9798162f, 145.426228f, 68.6763107f, 146.994582f, 71.4311121f, 147f);
    path.LineTo (114.568946f, 147f);
    path.CubicTo (117.323748f, 146.994143f, 120.020241f, 145.426228f, 121.407172f, 143.034271f);
    path.LineTo (142.976161f, 105.465744f);
    path.CubicTo (144.34135f, 103.067402f, 144.341209f, 99.9325978f, 142.976161f, 97.5342563f);
    path.LineTo (121.407172f, 59.965729f);
    path.CubicTo (120.020241f, 57.5737917f, 117.323748f, 56.0054182f, 114.568946f, 56f);
    path.LineTo (71.4311121f, 56f);
    path.Close ();

    // draw the Xamagon path
    canvas.DrawPath (path, paint);
  }
}
```

### <a name="drawing-text"></a>Disegno di testo

```csharp
// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.TextSize = 64.0f;
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x42, 0x81, 0xA4);
  paint.IsStroke = false;

  // draw the text
  canvas.DrawText ("Skia", 0.0f, 64.0f, paint);
}
```

### <a name="drawing-bitmaps"></a>Creazione di bitmap

```csharp
Stream fileStream = File.OpenRead ("MyImage.png");

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  canvas.DrawBitmap(bitmap, SKRect.Create(Width, Height), paint);
}
```

### <a name="drawing-with-image-filters"></a>Disegno con filtri di immagini

```csharp
Stream fileStream = File.OpenRead ("MyImage.png"); // open a stream to an image file

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  // create the image filter
  using (var filter = SKImageFilter.CreateBlur(5, 5)) {
    paint.ImageFilter = filter;

    // draw the bitmap through the filter
    canvas.DrawBitmap(bitmap, SKRect.Create(width, height), paint);
  }
}
```

# <a name="more-information"></a>Altre informazioni

Ulteriori informazioni sull'utilizzo SkiaSharp possono trovarsi nel [online la documentazione dell'API](https://developer.xamarin.com/api/namespace/SkiaSharp/)


## <a name="related-links"></a>Collegamenti correlati

- [IOS SkiaSharp cartella di lavoro](https://developer.xamarin.com/workbooks/graphics/skiasharp/logo/skialogo-ios.workbook)
