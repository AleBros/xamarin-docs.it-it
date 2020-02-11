---
title: Esempi indipendenti dalla piattaforma SkiaSharp
description: In questo documento viene fornita una breve introduzione ai concetti di base di SkiaSharp. In particolare, viene illustrato come ottenere e disegnare in un SKCanvas.
ms.prod: xamarin
ms.techonology: xamarin-skiasharp
ms.assetid: 19506F08-2603-465E-A806-6BD01638DE90
author: davidbritch
ms.author: dabritch
ms.date: 10/03/2018
ms.openlocfilehash: 4d0e57b98a479112b9fdf4f9c503418f3966cc73
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "64749931"
---
# <a name="skiasharp-platform-independent-examples"></a>Esempi indipendenti dalla piattaforma SkiaSharp

_In questo modo viene fornita una breve introduzione indipendente dalla piattaforma ai concetti alla base di SkiaSharp_

SkiaSharp offre un'API grafica 2D avanzata e potente che può essere usata per eseguire il rendering in buffer 2D.  È possibile usarli per implementare gli elementi dell'interfaccia utente e le immagini 2D personalizzate che possono essere incorporati nell'applicazione. SkiaSharp è un binding .NET alla libreria [Skia](https://skia.org) ed eredita le funzionalità e la potenza della libreria.

La libreria è attualmente disponibile come [pacchetto NuGet](https://www.nuget.org/packages/SkiaSharp)multipiattaforma. è possibile aggiungerlo al progetto aggiungendo il riferimento a NuGet.

Per disegnare, il codice creerà un `SkCanvas` che descrive la superficie in cui verranno eseguite le operazioni di disegno.

## <a name="obtaining-an-skcanvas"></a>Ottenere un SKCanvas

```csharp
using (var surface = SKSurface.Create (width: 640, height: 480, SKImageInfo.PlatformColorType, SKAlphaType.Premul)) {
    SKCanvas myCanvas = surface.Canvas;

    // Your drawing code goes here.
}
```

## <a name="drawing-on-skcanvas"></a>Disegno su SKCanvas

Il `SKCanvas` utilizza un modello di disegno simile allo spirito per altri modelli di disegno con cui si ha familiarità, utilizza colori con un canale di trasparenza facoltativo e può disegnare linee, archi, testo e immagini.

Di seguito sono riportate solo alcune delle numerose operazioni che è possibile eseguire con SkiaSharp.  Negli esempi seguenti la variabile `canvas` è di tipo SKCanvas.

### <a name="drawing-xamagon"></a>Disegno Xamagon

Questo esempio Mostra come disegnare il logo di Xamarin Xamagon:

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

### <a name="drawing-text"></a>Disegnare testo

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

### <a name="drawing-bitmaps"></a>Disegno di bitmap

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

### <a name="drawing-with-image-filters"></a>Disegno con filtri immagine

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

## <a name="more-information"></a>Altre informazioni

Altre informazioni sull'uso di SkiaSharp sono disponibili nella [documentazione dell'API](https://docs.microsoft.com/dotnet/api/skiasharp)
