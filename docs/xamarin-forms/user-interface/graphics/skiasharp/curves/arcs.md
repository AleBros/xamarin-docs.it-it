---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4eea7d500876793357113453493fa2fe2ede6cc4
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140016"
---
# <a name="three-ways-to-draw-an-arc"></a>Tre modi per disegnare un arco

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Informazioni su come usare SkiaSharp per definire gli archi in tre modi diversi_

Un arco è una curva sulla circonferenza di un'ellisse, ad esempio le parti arrotondate del segno di infinito:

![Segno di infinito](arcs-images/arcsample.png)

Nonostante la semplicità di questa definizione, non esiste alcun modo per definire una funzione di disegno di arco che soddisfi ogni esigenza e, di conseguenza, nessun consenso tra i sistemi grafici del modo migliore per disegnare un arco. Per questo motivo, la `SKPath` classe non si limita a un solo approccio.

`SKPath`definisce un [`AddArc`](xref:SkiaSharp.SKPath.AddArc*) metodo, cinque [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) metodi diversi e due metodi relativi [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*) . Questi metodi rientrano in tre categorie, che rappresentano tre approcci molto diversi per specificare un arco. Il modo in cui si utilizza dipende dalle informazioni disponibili per definire l'arco e dal modo in cui questo arco si integra con gli altri elementi grafici che si sta disegnando.

## <a name="the-angle-arc"></a>Arco angolo

L'approccio dell'arco dell'angolo per disegnare gli archi richiede l'impostazione di un rettangolo che delimita un'ellisse. L'arco sulla circonferenza di questo ellisse è indicato dagli angoli dal centro dell'ellisse che indicano l'inizio dell'arco e la relativa lunghezza. Due metodi diversi delineano gli archi degli angoli. Si tratta del [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) metodo e del [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean)) Metodo:

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

Questi metodi sono identici ai [`AddArc`](xref:Android.Graphics.Path.AddArc*) Metodi Android e [ `ArcTo` ] xrif: Android. graphics. Path. ArcTo *). Il [`AddArc`](xref:CoreGraphics.CGPath.AddArc(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.Boolean)) Metodo iOS è simile, ma è limitato agli archi sulla circonferenza di un cerchio anziché generalizzato in un'ellisse.

Entrambi i metodi iniziano con un `SKRect` valore che definisce la posizione e le dimensioni di un'ellisse:

![Ovale che inizia un arco di angolo](arcs-images/anglearcoval.png)

L'arco fa parte della circonferenza di questa ellisse.

L' `startAngle` argomento è un angolo in senso orario in gradi rispetto a una linea orizzontale disegnata dal centro dell'ellisse a destra. L' `sweepAngle` argomento è relativo a `startAngle` . Di seguito `startAngle` sono `sweepAngle` riportati i valori e rispettivamente di 60 gradi e 100 gradi:

![Angoli che definiscono un arco angolo](arcs-images/anglearcangles.png)

L'arco inizia dall'angolo iniziale. La sua lunghezza è governata dall'angolo di sweep. L'arco viene visualizzato in rosso:

![Arco angolo evidenziato](arcs-images/anglearchighlight.png)

La curva aggiunta al percorso con il `AddArc` metodo o `ArcTo` è semplicemente quella parte della circonferenza dell'ellisse:

![Arco angolo da solo](arcs-images/anglearc.png)

Gli `startAngle` `sweepAngle` argomenti o possono essere negativi: l'arco è in senso orario per i valori positivi `sweepAngle` e in senso antiorario per i valori negativi.

Tuttavia, `AddArc` non *not* definisce un contorno chiuso. Se si chiama `LineTo` dopo `AddArc` , viene disegnata una linea dalla fine dell'arco al punto nel `LineTo` metodo e lo stesso vale per `ArcTo` .

`AddArc`avvia automaticamente un nuovo contorno ed è funzionalmente equivalente a una chiamata a `ArcTo` con un argomento finale di `true` :

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

L'ultimo argomento viene chiamato `forceMoveTo` e causa efficacemente una `MoveTo` chiamata all'inizio dell'arco. Inizia un nuovo contorno. Questo non avviene con l'ultimo argomento di `false` :

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

Questa versione di `ArcTo` Disegna una linea dalla posizione corrente fino all'inizio dell'arco. Ciò significa che l'arco può trovarsi in un punto all'interno di un contorno più grande.

La pagina **arco angolo** consente di usare due dispositivi di scorrimento per specificare gli angoli di inizio e di apertura. Il file XAML crea un'istanza `Slider` di due elementi e un oggetto `SKCanvasView` . Il `PaintCanvas` gestore nel file [**AngleArcPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs) disegna sia l'ovale che l'arco utilizzando due `SKPaint` oggetti definiti come campi:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
    float startAngle = (float)startAngleSlider.Value;
    float sweepAngle = (float)sweepAngleSlider.Value;

    canvas.DrawOval(rect, outlinePaint);

    using (SKPath path = new SKPath())
    {
        path.AddArc(rect, startAngle, sweepAngle);
        canvas.DrawPath(path, arcPaint);
    }
}
```

Come si può notare, l'angolo iniziale e l'angolo di sweep possono assumere valori negativi:

[![Schermata tripla della pagina arco angolo](arcs-images/anglearc-small.png)](arcs-images/anglearc-large.png#lightbox)

Questo approccio alla generazione di un arco è algoritmicamente il più semplice ed è facile derivare le equazioni parametriche che descrivono l'arco. Conoscendo le dimensioni e la posizione dell'ellisse e gli angoli di inizio e di apertura, i punti iniziale e finale dell'arco possono essere calcolati con la trigonometria semplice:

`x = oval.MidX + (oval.Width / 2) * cos(angle)`

`y = oval.MidY + (oval.Height / 2) * sin(angle)`

Il `angle` valore può essere `startAngle` o `startAngle + sweepAngle` .

L'utilizzo di due angoli per definire un arco è ottimale nei casi in cui si conosca la lunghezza angolare dell'arco che si desidera creare, ad esempio per creare un grafico a torta. Questa operazione viene illustrata nella pagina del **grafico a torta esplosa** . La [`ExplodedPieChartPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs) classe usa una classe interna per definire alcuni dati e colori fabbricati:

```csharp
class ChartData
{
    public ChartData(int value, SKColor color)
    {
        Value = value;
        Color = color;
    }

    public int Value { private set; get; }

    public SKColor Color { private set; get; }
}

ChartData[] chartData =
{
    new ChartData(45, SKColors.Red),
    new ChartData(13, SKColors.Green),
    new ChartData(27, SKColors.Blue),
    new ChartData(19, SKColors.Magenta),
    new ChartData(40, SKColors.Cyan),
    new ChartData(22, SKColors.Brown),
    new ChartData(29, SKColors.Gray)
};

```

Il `PaintSurface` gestore scorre innanzitutto gli elementi per calcolare un `totalValues` numero. Da questo, può determinare le dimensioni di ogni elemento come frazione del totale e convertirlo in un angolo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int totalValues = 0;

    foreach (ChartData item in chartData)
    {
        totalValues += item.Value;
    }

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float explodeOffset = 50;
    float radius = Math.Min(info.Width / 2, info.Height / 2) - 2 * explodeOffset;
    SKRect rect = new SKRect(center.X - radius, center.Y - radius,
                             center.X + radius, center.Y + radius);

    float startAngle = 0;

    foreach (ChartData item in chartData)
    {
        float sweepAngle = 360f * item.Value / totalValues;

        using (SKPath path = new SKPath())
        using (SKPaint fillPaint = new SKPaint())
        using (SKPaint outlinePaint = new SKPaint())
        {
            path.MoveTo(center);
            path.ArcTo(rect, startAngle, sweepAngle, false);
            path.Close();

            fillPaint.Style = SKPaintStyle.Fill;
            fillPaint.Color = item.Color;

            outlinePaint.Style = SKPaintStyle.Stroke;
            outlinePaint.StrokeWidth = 5;
            outlinePaint.Color = SKColors.Black;

            // Calculate "explode" transform
            float angle = startAngle + 0.5f * sweepAngle;
            float x = explodeOffset * (float)Math.Cos(Math.PI * angle / 180);
            float y = explodeOffset * (float)Math.Sin(Math.PI * angle / 180);

            canvas.Save();
            canvas.Translate(x, y);

            // Fill and stroke the path
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, outlinePaint);
            canvas.Restore();
        }

        startAngle += sweepAngle;
    }
}
```

`SKPath`Viene creato un nuovo oggetto per ogni sezione della torta. Il percorso è costituito da una linea dal centro, quindi da un oggetto `ArcTo` per tracciare l'arco e da un'altra riga fino al centro risultante dalla `Close` chiamata. Questo programma Visualizza le sezioni di torta "esplose" spostando tutte le sezioni dal centro di 50 pixel. Questa attività richiede un vettore nella direzione del punto medio dell'angolo di apertura per ogni sezione:

[![Schermata tripla della pagina del grafico a torta esplosa](arcs-images/explodedpiechart-small.png)](arcs-images/explodedpiechart-large.png#lightbox)

Per verificarne l'aspetto senza l'"esplosione", è sufficiente impostare come commento la `Translate` chiamata:

[![Schermata tripla della pagina del grafico a torta esplosa senza l'esplosione](arcs-images/explodedpiechartunexploded-small.png)](arcs-images/explodedpiechartunexploded-large.png#lightbox)

## <a name="the-tangent-arc"></a>Arco tangente

Il secondo tipo di arco supportato da `SKPath` è l' *arco tangente*, quindi chiamato perché l'arco è la circonferenza di un cerchio che è tangente a due linee connesse.

Un arco tangente viene aggiunto a un percorso con una chiamata al [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) metodo con due `SKPoint` parametri oppure l' [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single)) Overload con `Single` parametri distinti per i punti:

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

Questo `ArcTo` metodo è simile alla funzione PostScript [`arct`](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) (page 532) e al metodo iOS [`AddArcToPoint`](xref:CoreGraphics.CGPath.AddArcToPoint(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat)) .

Il `ArcTo` metodo prevede tre punti:

- Il punto corrente del contorno o il punto (0,0) se `MoveTo` non è stato chiamato
- Primo argomento punto del `ArcTo` metodo, denominato *punto d'angolo*
- Il secondo argomento punto per `ArcTo` , denominato *punto di destinazione*:

![Tre punti che iniziano un arco tangente](arcs-images/tangentarcthreepoints.png)

Questi tre punti definiscono due linee connesse:

![Linee che connettono i tre punti di un arco tangente](arcs-images/tangentarcconnectinglines.png)

Se i tre punti sono colineari &mdash; , ovvero se si trovano sulla stessa linea retta, &mdash; non verrà disegnato alcun arco.

Il `ArcTo` metodo include anche un `radius` parametro. Definisce il raggio di un cerchio:

![Cerchio di un arco tangente](arcs-images/tangentarccircle.png)

L'arco tangente non è generalizzato per un'ellisse.

Se le due righe soddisfano qualsiasi angolo, il cerchio può essere inserito tra le righe in modo che sia tangente a entrambe le righe:

![Cerchio di arco tangente tra le due righe](arcs-images/tangentarctangentcircle.png)

La curva aggiunta al contorno non tocca uno dei punti specificati nel `ArcTo` metodo. È costituito da una linea retta dal punto corrente al primo punto tangente e da un arco che termina in corrispondenza del secondo punto tangente, riportato in rosso:

![Arco tangente evidenziato tra le due righe](arcs-images/tangentarchighlight.png)

Ecco la linea retta finale e l'arco che viene aggiunto al contorno:

![Arco tangente evidenziato tra le due righe](arcs-images/tangentarc.png)

Il contorno può continuare dal secondo punto tangente.

La pagina **arco tangente** consente di provare l'arco tangente. Questo è il primo di numerose pagine che derivano da [`InteractivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs) , che definisce alcuni `SKPaint` oggetti pratici ed esegue l' `TouchPoint` elaborazione:

```csharp
public class InteractivePage : ContentPage
{
    protected SKCanvasView baseCanvasView;
    protected TouchPoint[] touchPoints;

    protected SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3
    };

    protected SKPaint redStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 15
    };

    protected SKPaint dottedStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    };

    protected void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = baseCanvasView.CanvasSize.Width / (float)baseCanvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            baseCanvasView.InvalidateSurface();
        }
    }
}
```

La classe `TangentArcPage` deriva da `InteractivePage`. Il costruttore nel file [**TangentArcPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs) è responsabile della creazione di un'istanza e dell'inizializzazione della `touchPoints` matrice e dell'impostazione di `baseCanvasView` (in `InteractivePage` ) sull'oggetto di cui è stata `SKCanvasView` creata un'istanza nel file [**TangentArcPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml) :

```csharp
public partial class TangentArcPage : InteractivePage
{
    public TangentArcPage()
    {
        touchPoints = new TouchPoint[3];

        for (int i = 0; i < 3; i++)
        {
            TouchPoint touchPoint = new TouchPoint
            {
                Center = new SKPoint(i == 0 ? 100 : 500,
                                     i != 2 ? 100 : 500)
            };
            touchPoints[i] = touchPoint;
        }

        InitializeComponent();

        baseCanvasView = canvasView;
        radiusSlider.Value = 100;
    }

    void sliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Il `PaintSurface` gestore usa il `ArcTo` metodo per creare l'arco in base ai punti di tocco e a `Slider` , ma anche algoritmicamente calcola il cerchio su cui si basa l'angolo:

```csharp
public partial class TangentArcPage : InteractivePage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw the two lines that meet at an angle
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.LineTo(touchPoints[1].Center);
            path.LineTo(touchPoints[2].Center);
            canvas.DrawPath(path, dottedStrokePaint);
        }

        // Draw the circle that the arc wraps around
        float radius = (float)radiusSlider.Value;

        SKPoint v1 = Normalize(touchPoints[0].Center - touchPoints[1].Center);
        SKPoint v2 = Normalize(touchPoints[2].Center - touchPoints[1].Center);

        double dotProduct = v1.X * v2.X + v1.Y * v2.Y;
        double angleBetween = Math.Acos(dotProduct);
        float hypotenuse = radius / (float)Math.Sin(angleBetween / 2);
        SKPoint vMid = Normalize(new SKPoint((v1.X + v2.X) / 2, (v1.Y + v2.Y) / 2));
        SKPoint center = new SKPoint(touchPoints[1].Center.X + vMid.X * hypotenuse,
                                     touchPoints[1].Center.Y + vMid.Y * hypotenuse);

        canvas.DrawCircle(center.X, center.Y, radius, this.strokePaint);

        // Draw the tangent arc
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.ArcTo(touchPoints[1].Center, touchPoints[2].Center, radius);
            canvas.DrawPath(path, redStrokePaint);
        }

        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }

    // Vector methods
    SKPoint Normalize(SKPoint v)
    {
        float magnitude = Magnitude(v);
        return new SKPoint(v.X / magnitude, v.Y / magnitude);
    }

    float Magnitude(SKPoint v)
    {
        return (float)Math.Sqrt(v.X * v.X + v.Y * v.Y);
    }
}
```

Di seguito è illustrata la pagina di **arco tangente** che esegue:

[![Schermata tripla della pagina di arco tangente](arcs-images/tangentarc-small.png)](arcs-images/tangentarc-large.png#lightbox)

L'arco tangente è ideale per la creazione di angoli arrotondati, ad esempio un rettangolo arrotondato. Poiché `SKPath` include già un `AddRoundedRect` metodo, nella pagina **ettagono arrotondata** viene illustrato come utilizzare `ArcTo` per arrotondare gli angoli di un poligono a sette lati. Il codice è generalizzato per qualsiasi poligono normale.

Il `PaintSurface` gestore della [`RoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) classe contiene un `for` ciclo per calcolare le coordinate dei sette vertici di ettagono e un secondo per calcolare il ai punti medi dei sette lati da questi vertici. Questi ai punti medi vengono quindi usati per costruire il percorso:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float cornerRadius = 100;
    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPoint[] vertices = new SKPoint[numVertices];
    SKPoint[] midPoints = new SKPoint[numVertices];

    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    // Coordinates of the midpoints of the sides connecting the vertices
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        int prevVertex = (vertex + numVertices - 1) % numVertices;
        midPoints[vertex] = new SKPoint((vertices[prevVertex].X + vertices[vertex].X) / 2,
                                        (vertices[prevVertex].Y + vertices[vertex].Y) / 2);
    }

    // Create the path
    using (SKPath path = new SKPath())
    {
        // Begin at the first midpoint
        path.MoveTo(midPoints[0]);

        for (int vertex = 0; vertex < numVertices; vertex++)
        {
            SKPoint nextMidPoint = midPoints[(vertex + 1) % numVertices];

            // Draws a line from the current point, and then the arc
            path.ArcTo(vertices[vertex], nextMidPoint, cornerRadius);

            // Connect the arc with the next midpoint
            path.LineTo(nextMidPoint);
        }
        path.Close();

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);
        }
    }
}

```

Ecco il programma in esecuzione:

[![Schermata tripla della pagina ettagono arrotondata](arcs-images/roundedheptagon-small.png)](arcs-images/roundedheptagon-large.png#lightbox)

## <a name="the-elliptical-arc"></a>Arco ellittico

L'arco ellittico viene aggiunto a un percorso con una chiamata al [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint)) metodo che dispone di due `SKPoint` parametri oppure l' [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single)) Overload con coordinate X e Y separate:

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

L'arco ellittico è coerente con l' [arco ellittico](https://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands) incluso in svg (Scalable Vector Graphics) e la [`ArcSegment`](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) classe piattaforma UWP (Universal Windows Platform).

Questi `ArcTo` metodi consentono di creare un arco tra due punti, ovvero il punto corrente del contorno e l'ultimo parametro del `ArcTo` Metodo (il `xy` parametro o i `x` parametri e separati `y` ):

![Due punti che definiscono un arco ellittico](arcs-images/ellipticalarcpoints.png)

Il primo parametro punto per il `ArcTo` Metodo ( `r` , o `rx` e `ry` ) non è un punto, ma specifica invece i raggi orizzontali e verticali di un'ellisse.

![Ellisse che ha definito un arco ellittico](arcs-images/ellipticalarcellipse.png)

Il `xAxisRotate` parametro è il numero di gradi in senso orario per ruotare l'ellisse:

![Ellisse inclinata che definisce un arco ellittico](arcs-images/ellipticalarctiltedellipse.png)

Se questa ellisse inclinata viene quindi posizionata in modo da toccare i due punti, i punti sono collegati da due archi diversi:

![Primo set di archi ellittici](arcs-images/ellipticalarcellipse1.png)

Questi due archi possono essere distinti in due modi: l'arco superiore è maggiore dell'arco inferiore e, quando l'arco viene disegnato da sinistra a destra, l'arco superiore viene disegnato in senso orario mentre l'arco inferiore viene disegnato in senso antiorario.

È anche possibile adattare l'ellisse tra i due punti in un altro modo:

![Secondo set di archi ellittici](arcs-images/ellipticalarcellipse2.png)

A questo punto è presente un arco più piccolo in alto che viene disegnato in senso orario e un arco più grande nella parte inferiore disegnata in senso antiorario.

Questi due punti possono pertanto essere connessi da un arco definito dall'ellisse inclinata in un totale di quattro modi:

![Tutti e quattro gli archi ellittici](arcs-images/ellipticalarccolors.png)

Questi quattro archi sono distinti dalle quattro combinazioni degli argomenti di [`SKPathArcSize`](xref:SkiaSharp.SKPathArcSize) [`SKPathDirection`](xref:SkiaSharp.SKPathDirection) tipo di enumerazione e al `ArcTo` Metodo:

- rosso: SKPathArcSize. Large e SKPathDirection. senso orario
- verde: SKPathArcSize. Small e SKPathDirection. senso orario
- blu: SKPathArcSize. Small e SKPathDirection. in senso antiorario
- Magenta: SKPathArcSize. Large e SKPathDirection. in senso antiorario

Se l'ellisse inclinata non è sufficientemente grande per adattarsi tra i due punti, viene ridimensionata in modo uniforme fino a quando non è sufficientemente grande. In tal caso, solo due archi univoci connettono i due punti. Questi possono essere distinti con il `SKPathDirection` parametro.

Sebbene questo approccio alla definizione di un arco risulti complesso al primo incontro, è l'unico approccio che consente di definire un arco con un'ellisse ruotata ed è spesso l'approccio più semplice quando è necessario integrare archi con altre parti del contorno.

La pagina **arco ellittico** consente di impostare in modo interattivo i due punti e le dimensioni e la rotazione dell'ellisse. La `EllipticalArcPage` classe deriva da `InteractivePage` e il `PaintSurface` gestore nel file code-behind [**EllipticalArcPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs) disegna i quattro archi:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        int colorIndex = 0;
        SKPoint ellipseSize = new SKPoint((float)xRadiusSlider.Value,
                                          (float)yRadiusSlider.Value);
        float rotation = (float)rotationSlider.Value;

        foreach (SKPathArcSize arcSize in Enum.GetValues(typeof(SKPathArcSize)))
            foreach (SKPathDirection direction in Enum.GetValues(typeof(SKPathDirection)))
            {
                path.MoveTo(touchPoints[0].Center);
                path.ArcTo(ellipseSize, rotation,
                           arcSize, direction,
                           touchPoints[1].Center);

                strokePaint.Color = colors[colorIndex++];
                canvas.DrawPath(path, strokePaint);
                path.Reset();
            }
    }

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}

```

In esecuzione:

[![Schermata tripla della pagina di arco ellittico](arcs-images/ellipticalarc-small.png)](arcs-images/ellipticalarc-large.png#lightbox)

La pagina **Infinity Arc** usa l'arco ellittico per tracciare un segno di infinito. Il segno di infinito si basa su due cerchi con raggi di 100 unità separate da 100 unità:

![Due cerchi](arcs-images/infinitycircles.png)

Due righe che si intersecano tra loro sono tangenti a entrambi i cerchi:

![Due cerchi con linee tangente](arcs-images/infinitycircleslines.png)

Il segno di infinito è una combinazione di parti di questi cerchi e le due righe. Per utilizzare l'arco ellittico per tracciare il segno di infinito, è necessario determinare le coordinate in cui le due righe sono tangenti ai cerchi.

Costruire un rettangolo a destra in uno dei cerchi:

![Due cerchi con linee tangente e cerchio incorporato](arcs-images/infinitytriangle.png)

Il raggio del cerchio è di 100 unità e l'ipotenusa del triangolo è 150 unità, quindi l'angolo α è arcoseno (inverso seno) di 100 diviso per 150 o 41,8 gradi. La lunghezza dell'altro lato del triangolo è 150 volte il coseno di 41,8 gradi, o 112, che può essere calcolato anche dal teorema di Pitagora.

Le coordinate del punto tangente possono quindi essere calcolate usando queste informazioni:

`x = 112·cos(41.8) = 83`

`y = 112·sin(41.8) = 75`

I quattro punti di tangente sono tutti necessari per tracciare un segno di infinito centrato sul punto (0, 0) con raggi cerchio di 100:

![Due cerchi con linee e coordinate tangente](arcs-images/infinitycoordinates.png)

Il `PaintSurface` gestore nella [`ArcInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) classe posiziona il segno di infinito in modo che il punto (0, 0) venga posizionato al centro della pagina e ridimensiona il percorso alla dimensione dello schermo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.LineTo(83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.CounterClockwise, 83, -75);
        path.LineTo(-83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.Clockwise, -83, -75);
        path.Close();

        // Use path.TightBounds for coordinates without control points
        SKRect pathBounds = path.Bounds;

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / pathBounds.Width,
                              info.Height / pathBounds.Height));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 5;

            canvas.DrawPath(path, paint);
        }
    }
}
```

Il codice usa la `Bounds` proprietà di `SKPath` per determinare le dimensioni del seno infinito per adattarle alla dimensione dell'area di disegno:

[![Schermata tripla della pagina Infinity Arc](arcs-images/arcinfinity-small.png)](arcs-images/arcinfinity-large.png#lightbox)

Il risultato è leggermente piccolo, il che suggerisce che la `Bounds` proprietà di `SKPath` sta segnalando una dimensione maggiore del percorso.

Internamente, Skia si avvicina all'arco usando più curve di Bézier quadratiche. Queste curve (come si vedrà nella sezione successiva) contengono punti di controllo che controllano il modo in cui la curva viene disegnata, ma non fanno parte della curva sottoposta a rendering. La `Bounds` proprietà include i punti di controllo.

Per ottenere una soluzione più restrittiva, usare la `TightBounds` proprietà, che esclude i punti di controllo. Ecco il programma in esecuzione in modalità orizzontale e usando la `TightBounds` proprietà per ottenere i limiti del percorso:

[![Schermata tripla della pagina Infinity Arc con limiti stretti](arcs-images/arcinfinitytightbounds-small.png)](arcs-images/arcinfinitytightbounds-large.png#lightbox)

Sebbene le connessioni tra gli archi e le linee rette siano matematicamente smussate, la modifica da Arc a linea retta potrebbe sembrare un po' improvvisa. Un segno di infinito migliore viene presentato nell'articolo successivo su [**tre tipi di curve di Bézier**](beziers.md).

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
