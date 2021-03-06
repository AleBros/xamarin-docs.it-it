---
title: Polilinee ed equazioni parametriche
description: Questo articolo illustra come qualsiasi riga che è possibile definire con equazioni parametriche e questo concetto è illustrato con esempio di codice per l'uso di SkiaSharp per eseguire il rendering.
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: f635e6e20a4cec9b8cc735bc733b678263cd024a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759170"
---
# <a name="polylines-and-parametric-equations"></a>Polilinee ed equazioni parametriche

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Consente di eseguire il rendering di tutte le righe che è possibile definire con equazioni parametriche SkiaSharp_

Nel [ **SkiaSharp curve e tracciati** ](../curves/index.md) sezione di questa Guida, si noterà i vari metodi che [ `SKPath` ](xref:SkiaSharp.SKPath) definisce per eseguire il rendering di determinati tipi di curve. Tuttavia, è talvolta necessario disegnare un tipo di curva che non è supportata direttamente da `SKPath`. In tal caso, è possibile usare una polilinea (una raccolta di linee collegate) per disegnare qualsiasi curva che è possibile definire matematicamente. Se si imposta le righe è piccolo e numerosi sufficiente, il risultato sarà simile una curva. Questo spirale è effettivamente 3.600 piccole linee:

![](polylines-images/spiralexample.png "Una spirale")

In genere è preferibile definire una curva in termini di una coppia di equazioni parametriche. Queste sono le equazioni per X e Y coordinate che dipendono da una terza variabile, talvolta denominata `t` per volta. Ad esempio, di definire un cerchio con raggio pari a 1 centrato al punto (0, 0) per le equazioni parametriche seguenti *t* compreso tra 0 e 1:

`x = cos(2πt)`

`y = sin(2πt)`

 Se si desidera un raggio di dimensioni superiori a 1, è possibile moltiplicare i seno e il coseno di valori per tale radius semplicemente e se è necessario passare al centro in un'altra posizione, aggiungere tali valori:

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

Per un'ellisse con parallelo di assi orizzontale e verticale, sono coinvolti due raggi:

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

È quindi possibile inserire il codice equivalente di SkiaSharp in un ciclo che calcola i vari punti e aggiunge quelle per un percorso. Il codice di SkiaSharp seguente crea un `SKPath` oggetto per un'ellisse che riempie l'area di visualizzazione. Il ciclo scorre 360 gradi direttamente. Il centro è metà della larghezza e altezza dell'area di visualizzazione, e quindi i due raggi:

```csharp
SKPath path = new SKPath();

for (float angle = 0; angle < 360; angle += 1)
{
    double radians = Math.PI * angle / 180;
    float x = info.Width / 2 + (info.Width / 2) * (float)Math.Cos(radians);
    float y = info.Height / 2 + (info.Height / 2) * (float)Math.Sin(radians);

    if (angle == 0)
    {
        path.MoveTo(x, y);
    }
    else
    {
        path.LineTo(x, y);
    }
}
path.Close();
```

Di conseguenza un'ellisse definita da 360 piccole linee. Quando ne viene eseguito il rendering, viene visualizzato senza problemi.

Naturalmente, non è necessario creare una forma ellipse utilizzando una polilinea in quanto `SKPath` include un `AddOval` metodo che esegue tutto automaticamente. Ma è possibile disegnare un oggetto visivo che non viene fornito da `SKPath`.

Il **spirale Archimedean** è incluso il codice simile al codice dell'ellisse ma con una differenza essenziale. Esegue un ciclo intorno a 360 gradi di cerchio 10 volte, in modo continuativo la regolazione del raggio:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(center.X, center.Y);

    using (SKPath path = new SKPath())
    {
        for (float angle = 0; angle < 3600; angle += 1)
        {
            float scaledRadius = radius * angle / 3600;
            double radians = Math.PI * angle / 180;
            float x = center.X + scaledRadius * (float)Math.Cos(radians);
            float y = center.Y + scaledRadius * (float)Math.Sin(radians);
            SKPoint point = new SKPoint(x, y);

            if (angle == 0)
            {
                path.MoveTo(point);
            }
            else
            {
                path.LineTo(point);
            }
        }

        SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 5
        };

        canvas.DrawPath(path, paint);
    }
}
```

Il risultato è l'acronimo di un' *spirale aritmetico* perché l'offset tra ogni ciclo è costante:

[![](polylines-images/archimedeanspiral-small.png "Tripla screenshot della pagina Archimedean spirale")](polylines-images/archimedeanspiral-large.png#lightbox "tripla screenshot della pagina Archimedean spirale")

Si noti che il `SKPath` viene creato in un `using` blocco. Ciò `SKPath` consuma più memoria per il `SKPath` gli oggetti nei programmi precedenti, che suggerisce che un `using` blocco risulta più appropriato per l'eliminazione delle risorse non gestite.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
