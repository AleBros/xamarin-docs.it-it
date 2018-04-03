---
title: Polilinea ed equazioni parametriche
description: Utilizzare SkiaSharp per il rendering di qualsiasi riga che è possibile definire con equazioni parametriche
ms.topic: article
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: dce14ead2de6a21b99209ed12fbb99729734149a
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="polylines-and-parametric-equations"></a>Polilinea ed equazioni parametriche

_Utilizzare SkiaSharp per il rendering di qualsiasi riga che è possibile definire con equazioni parametriche_

Nella parte successiva di questa Guida, è possibile visualizzare i vari metodi che `SKPath` definisce per eseguire il rendering di determinati tipi di curve. Talvolta, tuttavia, è necessario creare un tipo di curva che non è supportato direttamente da `SKPath`. In tal caso, è possibile utilizzare una polilinea (una raccolta di linee collegate) per disegnare qualsiasi curva che è possibile definire matematicamente. Se si crea le righe sufficientemente piccole numerosi sufficiente, il risultato sarà simile a una curva. Questa spirale è effettivamente 3.600 piccole linee:

![](polylines-images/spiralexample.png "Spirale")

In genere è preferibile definire una curva in termini di una coppia di equazioni parametriche. Si tratta di equazioni per X e Y coordinate che dipendono da una terza variabile, talvolta chiamata `t` per volta. Ad esempio, di definire un cerchio con un raggio pari a 1 centrato il punto (0, 0) per le equazioni parametriche seguente *t* da 0 a 1:

 x = y cos(2πt) = sin(2πt)

 Se si desidera un raggio di dimensioni maggiori di 1, è semplicemente possibile moltiplicare i valori seno e coseno del raggio e se è necessario spostare il centro di un'altra posizione, aggiungere tali valori:

 x = Centrox + radius·cos(2πt) y = Centroy + radius·sin(2πt)

Per un'ellisse con il parallelo assi orizzontale e verticale, sono coinvolti due raggi:

x = xCenter + xRadius·cos(2πt) y = yCenter + yRadius·sin(2πt)

È quindi possibile inserire il codice SkiaSharp equivalente in un ciclo che calcola i vari punti e aggiunge quelle a un percorso. Il codice SkiaSharp seguente crea un `SKPath` oggetto per un'ellisse che riempie l'area di visualizzazione. I ciclo scorre 360 gradi direttamente. Il centro è a metà larghezza e altezza dell'area di visualizzazione, e quindi i raggi di due:

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

Il risultato di un'ellisse definita da piccole 360 linee. Quando ne viene eseguito il rendering, viene visualizzato senza problemi.

Ovviamente, non è necessario creare un'ellisse utilizzando una polilinea perché `SKPath` include un `AddOval` metodo che si adatta automaticamente. Ma è possibile disegnare un oggetto visivo che non viene fornito da `SKPath`.

Il **Archimedean spirale** è incluso codice simile al codice ellisse ma con una differenza fondamentale. Esegue un ciclo intorno a 360 gradi di cerchio 10 volte, in modo continuo regolando il raggio:

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

Il risultato è l'acronimo un *spirale aritmetici* perché la differenza tra ogni ciclo è costante:

[![](polylines-images/archimedeanspiral-small.png "Schermata di triplo della pagina spirale Archimedean")](polylines-images/archimedeanspiral-large.png#lightbox "tripla schermata della pagina spirale Archimedean")

Si noti che il `SKPath` viene creato in un `using` blocco. Questo `SKPath` Usa più memoria rispetto al `SKPath` oggetti nei programmi precedenti, che suggerisce che un `using` blocco è più appropriato per tutte le risorse non gestite.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
