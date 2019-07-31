---
title: Tre modi per disegnare un arco
description: Questo articolo illustra come usare SkiaSharp per definire gli archi in tre modi diversi e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2017
ms.openlocfilehash: 97c168460b091b9ada954cacd895a670c31805b2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655188"
---
# <a name="three-ways-to-draw-an-arc"></a>Tre modi per disegnare un arco

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Informazioni su come usare SkiaSharp per definire gli archi in tre modi diversi_

Un arco è una curva sulla circonferenza di un'ellisse, ad esempio le parti di questo simbolo di infinito arrotondate:

![](arcs-images/arcsample.png "Simbolo di infinito")

Nonostante la semplicità di tale definizione, non è possibile definire una funzione di disegno da parte di arco che soddisfa ogni esigenza, e di conseguenza, non il consenso dei sistemi di grafica del modo migliore per disegnare un arco. Per questo motivo, il `SKPath` classe non limita la stessa di un solo approccio.

`SKPath` definisce un [ `AddArc` ](xref:SkiaSharp.SKPath.AddArc*) metodo, cinque diversi [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo*) metodi e relativi ai due [ `RArcTo` ](xref:SkiaSharp.SKPath.RArcTo*) metodi. Questi metodi rientrano in tre categorie, che rappresenta tre diversi approcci alla specifica di un arco. Il metodo scelto dipende dalle informazioni disponibili per definire l'arco e come questo arco si integra con gli altri elementi grafici che si sta creando.

## <a name="the-angle-arc"></a>L'angolo arco

L'approccio di arco angolo al disegno di archi richiede di specificare un rettangolo che delimita un'ellisse. Base ad angoli dal centro dell'ellisse che indicano l'inizio dell'arco e la relativa lunghezza è indicato l'arco nella circonferenza di questa ellissi. Due metodi diversi per tracciare gli archi angolo. Questi sono i [ `AddArc` ](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) metodo e il [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean)) metodo:

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

Questi metodi sono identici ai metodi [`AddArc`](xref:Android.Graphics.Path.AddArc*) Android e`ArcTo`[] xrif: Android. graphics. Path. ArcTo *). IOS [ `AddArc` ](xref:CoreGraphics.CGPath.AddArc(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.Boolean)) metodo è simile, ma è limitato agli archi sulla circonferenza del cerchio anziché generalizzata da un'ellisse.

Entrambi i metodi iniziano con un `SKRect` valore che definisce la posizione e dimensioni di un'ellisse:

![](arcs-images/anglearcoval.png "La forma ovale che inizia un arco angolo")

Punto finale dell'arco è una parte tra la circonferenza di questa ellissi.

Il `startAngle` argomento è un angolo in senso orario espresso in gradi relativa a una riga orizzontale disegnata dal centro dell'ellisse a destra. Il `sweepAngle` argomento è relativo al `startAngle`. Ecco `startAngle` e `sweepAngle` valori di 60 gradi e 100 gradi, rispettivamente:

![](arcs-images/anglearcangles.png "Gli angoli che definiscono un arco angolo")

In cui inizia l'angolo iniziale dell'arco. La sua lunghezza è disciplinata dall'angolo di apertura. Seguito è riportato l'arco in rosso:

![](arcs-images/anglearchighlight.png "Punto finale dell'arco angolo evidenziato")

La curva aggiunta al percorso con il `AddArc` o `ArcTo` metodo è semplicemente quella parte di circonferenza dell'ellisse:

![](arcs-images/anglearc.png "L'arco angolo da solo")

Gli `startAngle` argomenti `sweepAngle` o possono essere negativi: L'arco è in senso orario per i `sweepAngle` valori positivi e in senso antiorario per i valori negativi.

Tuttavia `AddArc` viene *non* definiscono un contorno chiuso. Se si chiama `LineTo` dopo aver `AddArc`, viene tracciata una linea dalla fine dell'arco al punto nel `LineTo` metodo e lo stesso vale per `ArcTo`.

`AddArc` inizia un nuovo contorno ed è funzionalmente equivalente a una chiamata a automaticamente `ArcTo` con un argomento finale di `true`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

Ultimo argomento è detto `forceMoveTo`, e si verifica in modo efficace un `MoveTo` chiamare all'inizio dell'arco. Che avvia una nuova distribuzione. Vale a dire non avviene con un ultimo argomento di `false`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

Questa versione di `ArcTo` Disegna una linea dalla posizione corrente all'inizio dell'arco. Ciò significa che l'arco può essere un punto qualsiasi all'interno di una distribuzione più grande.

Il **angolo dell'arco** pagina è possibile usare due dispositivi di scorrimento per specificare l'inizio e durante lo sweep degli angoli. Il file XAML crea un'istanza di due `Slider` elementi e un `SKCanvasView`. Il `PaintCanvas` gestore nel [ **AngleArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs) file consente di disegnare la forma ovale sia l'arco usando due `SKPaint` gli oggetti definiti come campi:

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

Come può notare, i valori negativi può assumere entrambi l'angolo iniziale e angolo di apertura:

[![](arcs-images/anglearc-small.png "Tripla screenshot della pagina angolo dell'arco")](arcs-images/anglearc-large.png#lightbox "tripla screenshot della pagina angolo dell'arco")

Questo approccio per la generazione di un arco è modo algoritmico la più semplice ed è facile derivare le equazioni parametriche che descrivono l'arco. Conoscendo le dimensioni e posizione dei puntini di sospensione e gli angoli di avvio e durante lo sweep, i punti iniziale e finale dell'arco può essere calcolati usando trigonometria semplice:

`x = oval.MidX + (oval.Width / 2) * cos(angle)`

`y = oval.MidY + (oval.Height / 2) * sin(angle)`

Il `angle` valore è `startAngle` o `startAngle + sweepAngle`.

L'uso di due degli angoli per definire un arco è ideale per i casi in cui si conosce la lunghezza angular dell'arco da disegnare, ad esempio, per creare un grafico a torta. Il **grafico a torta esplosa** pagina viene illustrata questa. Il [ `ExplodedPieChartPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs) classe utilizza una classe interna per definire alcuni dati creati e i colori:

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

Il `PaintSurface` gestore prima scorre in ciclo gli elementi da calcolare un `totalValues` numero. Tramite la quale è possibile determinare le dimensioni di ogni elemento come la frazione del totale e convertirlo in un angolo:

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

Un nuovo `SKPath` oggetto viene creato per ogni sezione della torta. Il percorso è costituito da una riga dal centro di un' `ArcTo` per tracciare l'arco e un'altra riga nuovamente i risultati di centro del `Close` chiamare. Questo programma consente di visualizzare sezioni della torta "esploso" spostandoli tutte le risorse dal centro per 50 pixel. Questa attività richiede un vettore nella direzione del punto centrale dell'angolo di apertura per ogni sezione:

[![](arcs-images/explodedpiechart-small.png "Tripla screenshot della pagina del grafico a torta esplosa")](arcs-images/explodedpiechart-large.png#lightbox "tripla screenshot della pagina del grafico a torta esplosa")

Per visualizzare l'aspetto senza l'esplosione del "generale", è sufficiente impostare come commento il `Translate` chiamare:

[![](arcs-images/explodedpiechartunexploded-small.png "Tripla screenshot della pagina del grafico a torta esplosa senza l'esplosione")](arcs-images/explodedpiechartunexploded-large.png#lightbox "tripla screenshot della pagina del grafico a torta esplosa senza l'esplosione")

## <a name="the-tangent-arc"></a>L'arco tangente

Il secondo tipo di arc supportati da `SKPath` è il *arco tangente*, pertanto chiamato quanto l'arco viene la circonferenza del cerchio tangente a due linee collegate.

Un arco tangente viene aggiunto a un percorso con una chiamata ai [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) metodo con due `SKPoint` parametri, o la [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single)) overload con separato `Single` parametri per il punti:

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

Ciò `ArcTo` metodo è simile al PostScript [ `arct` ](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) funzione (pagina 532) e iOS [ `AddArcToPoint` ](xref:CoreGraphics.CGPath.AddArcToPoint(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat)) (metodo).

Il `ArcTo` metodo prevede tre punti:

- Punto di corrente di contorno e il punto (0, 0) se `MoveTo` non è stato chiamato
- Il primo argomento punto per il `ArcTo` metodo, denominato il *angolo punto*
- Il secondo argomento punto `ArcTo`, definita come il *punto di destinazione*:

![](arcs-images/tangentarcthreepoints.png "Tre punti che iniziano un arco tangente")

Queste tre punti definiscono due linee collegate:

![](arcs-images/tangentarcconnectinglines.png "Linee che collegano i tre punti di un arco tangente")

Se i tre punti sono collineari &mdash; , ovvero se esse si trovino nella stessa linea retta &mdash; non verrà disegnato alcun arco.

Il `ArcTo` metodo include anche un `radius` parametro. Definisce il raggio del cerchio:

![](arcs-images/tangentarccircle.png "Il cerchio di un arco tangente")

L'arco tangente non è generalizzata per un'ellisse.

Se le due righe soddisfano qualsiasi angolo di rotazione, tale cerchio può essere inserita tra le righe in modo che risulti tangente in entrambe le righe:

![](arcs-images/tangentarctangentcircle.png "Il cerchio arco tangente tra le due righe")

Non hai uno dei punti specificati nella curva che viene aggiunto per il contorno di `ArcTo` (metodo). È costituito da una linea retta dal punto corrente per il primo punto tangente e un arco che termina in corrispondenza del secondo punto tangente, indicato in rosso:

![](arcs-images/tangentarchighlight.png "L'arco tangente evidenziato tra le due righe")

Ecco la linea retta finale e un arco che viene aggiunto per la distribuzione:

![](arcs-images/tangentarc.png "L'arco tangente evidenziato tra le due righe")

La distribuzione possa essere ripreso da secondo punto tangente.

Il **arco tangente** pagina consente di sperimentare l'arco tangente. Questo è il primo di diverse pagine che derivano da [ `InteractivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs), che definisce alcune utili `SKPaint` oggetti ed esegue `TouchPoint` elaborazione:

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

La classe `TangentArcPage` deriva da `InteractivePage`. Il costruttore nel [ **TangentArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs) è responsabile della creazione di istanze e inizializzazione di file il `touchPoints` array e impostazione `baseCanvasView` (in `InteractivePage`) per il `SKCanvasView` creata un'istanza di oggetto nel [ **TangentArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml) file:

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

Il `PaintSurface` gestore utilizza la `ArcTo` metodo consente di disegnare l'arco basato sui punti di tocco e un `Slider`, ma anche modo algoritmico calcola il cerchio l'angolo di base:

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

Di seguito è riportato il **arco tangente** pagina in esecuzione:

[![](arcs-images/tangentarc-small.png "Tripla screenshot della pagina arco tangente")](arcs-images/tangentarc-large.png#lightbox "tripla screenshot della pagina arco tangente")

L'arco tangente è ideale per la creazione e angoli arrotondati, ad esempio un rettangolo arrotondato. Poiché `SKPath` include già un' `AddRoundedRect` metodo, il **arrotondato Heptagon** pagina illustra come usare `ArcTo` per arrotondare gli angoli di un poligono sette lati. (Il codice è generalizzato per qualsiasi poligono normale).

Il `PaintSurface` gestore del [ `RoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) classe contiene una `for` loop per calcolare le coordinate dei vertici del heptagon e la seconda per calcolare i punti centrali dei sette lati da questi sette vertici. Questi punti centrali vengono quindi usati per creare il percorso:

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

[![](arcs-images/roundedheptagon-small.png "Tripla screenshot della pagina arrotondato Heptagon")](arcs-images/roundedheptagon-large.png#lightbox "tripla screenshot della pagina Heptagon arrotondato")

## <a name="the-elliptical-arc"></a>Punto finale dell'arco ellittico

Arco ellittico viene aggiunto a un percorso con una chiamata ai [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint)) metodo che presenta due `SKPoint` parametri, o la [ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single)) overload con separato X e Y coordinate:

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

Punto finale dell'arco ellittico è coerente con il [arco ellittico](http://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands) incluso in Scalable Vector Graphics (SVG) e la piattaforma Windows universale [ `ArcSegment` ](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) classe.

Questi `ArcTo` metodi disegnano un arco tra due punti, ovvero il punto corrente per il contorno, e l'ultimo parametro per il `ArcTo` (metodo) (le `xy` parametro o le varie `x` e `y` parametri):

![](arcs-images/ellipticalarcpoints.png "I due punti definiti un arco ellittico")

Il primo parametro punto per il `ArcTo` metodo (`r`, o `rx` e `ry`) non è considerato un punto, ma vengono invece specificati i raggi orizzontali e verticali di un'ellisse.

![](arcs-images/ellipticalarcellipse.png "Ellisse definiti un arco ellittico")

Il `xAxisRotate` parametro indica il numero di gradi in senso orario di rotazione di questa ellissi:

![](arcs-images/ellipticalarctiltedellipse.png "Ellisse inclinato definiti un arco ellittico")

Se questa ellisse inclinata quindi viene posizionata in modo che interessa i due punti, i punti sono uniti da due archi diversi:

![](arcs-images/ellipticalarcellipse1.png "Il primo set di archi ellittici")

Questi due archi possono essere distinti in due modi: L'arco superiore è maggiore dell'arco inferiore e, quando l'arco viene disegnato da sinistra a destra, l'arco superiore viene disegnato in senso orario mentre l'arco inferiore viene disegnato in senso antiorario.

È anche possibile adattare i puntini di sospensione tra i due punti in un altro modo:

![](arcs-images/ellipticalarcellipse2.png "Il secondo set di archi ellittici")

Ora è presente un arco più piccolo che viene disegnato in senso orario nella parte superiore e un arco più grande nella parte inferiore che viene disegnato in senso antiorario.

Questi due punti possono quindi essere collegati da un arco definito dai puntini di sospensione inclinato un totale di quattro modi:

![](arcs-images/ellipticalarccolors.png "Tutti i quattro archi ellittici")

Questi quattro archi si distinguono dalle quattro combinazioni dei [ `SKPathArcSize` ](xref:SkiaSharp.SKPathArcSize) e [ `SKPathDirection` ](xref:SkiaSharp.SKPathDirection) argomenti di tipo di enumerazione per il `ArcTo` metodo:

- Red SKPathArcSize. Large e SKPathDirection. senso orario
- verde SKPathArcSize. Small e SKPathDirection. senso orario
- blu SKPathArcSize. Small e SKPathDirection. in senso antiorario
- Magenta SKPathArcSize. Large e SKPathDirection. in senso antiorario

Se l'ellisse inclinato non è sufficientemente grande da posizionarla tra i due punti, quindi viene ridimensionato in modo uniforme fino a quando non è sufficientemente grande. Solo due archi univoci connettono in questo caso i due punti. Questi possono essere distinti con il `SKPathDirection` parametro.

Sebbene questo approccio per la definizione di un arco suona complesso nel primo incontro, è l'unico approccio che consente di definire un arco con un ellisse ruotata ed è spesso l'approccio più semplice quando è necessario integrare archi con altre parti del contorno.

Il **arco ellittico** pagina consente di impostare in modo interattivo i due punti e la dimensione e la rotazione dell'ellisse. Il `EllipticalArcPage` deriva dalla classe `InteractivePage`e il `PaintSurface` gestore nel [ **EllipticalArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs) file code-behind crea quattro archi:

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

Qui è in esecuzione:

[![](arcs-images/ellipticalarc-small.png "Tripla screenshot della pagina arco ellittico")](arcs-images/ellipticalarc-large.png#lightbox "tripla screenshot della pagina arco ellittico")

Il **arco infinito** pagina utilizza l'arco ellittico per disegnare un segno di un numero infinito. Il simbolo di infinito si basa su due cerchi con un raggio di separati da 100 unità di 100 unità:

![](arcs-images/infinitycircles.png "Due cerchi")

Due perpendicolari tra loro sono tangente per entrambi i cerchi:

![](arcs-images/infinitycircleslines.png "Due cerchi con tangenti")

Il simbolo di infinito è una combinazione delle parti di tali cerchi e le due righe. Per usare l'arco ellittico per disegnare il simbolo di infinito, è necessario determinare le coordinate in cui le due righe sono tangente ai cerchi.

Creare un rettangolo a destra in uno dei cerchi:

![](arcs-images/infinitytriangle.png "Due cerchi con linee tangente e cerchio incorporato")

Il raggio del cerchio è 100 unità e l'ipotenusa del triangolo è 150 unità, in modo che l'angolo α è l'arcoseno (seno inverso) pari a 100 diviso da 150, o gradi 41.8. La lunghezza di altro lato del triangolo è 150 volte il coseno di gradi 41.8 o 112, che possono essere calcolati anche il teorema pitagorica.

Le coordinate del punto tangente possono quindi essere calcolate usando queste informazioni:

`x = 112·cos(41.8) = 83`

`y = 112·sin(41.8) = 75`

I quattro punti tangenti sono tutto ciò che è necessario disegnare un segno di un numero infinito centrato sul punto (0, 0) con raggi cerchio pari a 100:

![](arcs-images/infinitycoordinates.png "Due cerchi con le coordinate e le tangenti")

Il `PaintSurface` gestore nel [ `ArcInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) classe posiziona il simbolo di infinito in modo che il (0, 0) punto viene posizionata al centro della pagina e ridimensiona il percorso alle dimensioni dello schermo:

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

Il codice Usa il `Bounds` proprietà di `SKPath` per determinare le dimensioni del seno infinito per la scalabilità per le dimensioni dell'area di disegno:

[![](arcs-images/arcinfinity-small.png "Tripla screenshot della pagina di un numero infinito arco")](arcs-images/arcinfinity-large.png#lightbox "tripla screenshot della pagina di un numero infinito arco")

Il risultato sembra un po' piccolo, che suggerisce che il `Bounds` proprietà di `SKPath` Invia report di dimensioni maggiori rispetto al percorso.

Internamente, Skia approssima l'arco usando più curve di Bézier quadratiche. Queste curve (come si vedrà nella sezione successiva) contengono i punti di controllo che controllano come viene disegnata la curva ma che non fanno parte della curva sottoposto a rendering. Il `Bounds` proprietà include i punti di controllo.

Per ottenere un migliore adattamento, usare il `TightBounds` proprietà, che consente di escludere i punti di controllo. Ecco il programma in esecuzione in modalità orizzontale e l'utilizzo di `TightBounds` proprietà per ottenere i limiti di percorso:

[![](arcs-images/arcinfinitytightbounds-small.png "Tripla screenshot della pagina arco infinito con limiti rigidi")](arcs-images/arcinfinitytightbounds-large.png#lightbox "tripla screenshot della pagina arco infinito con limiti rigidi")

Anche se le connessioni tra le linee rette e gli archi sono matematicamente smooth, la modifica da arco linea retta potrebbe sembrare un po' improvvisa. Un simbolo di infinito migliore viene presentato nel prossimo articolo sul [ **tre tipi di curve di Bézier**](beziers.md).

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
