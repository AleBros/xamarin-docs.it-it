---
title: Tre metodi per disegnare un arco
description: Informazioni su come usare SkiaSharp per definire gli archi in tre modi diversi
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: charlespetzold
ms.author: chape
ms.date: 05/10/2017
ms.openlocfilehash: 86623886e2429cbf2f076fbe4583301edf684262
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="three-ways-to-draw-an-arc"></a>Tre metodi per disegnare un arco

_Informazioni su come usare SkiaSharp per definire gli archi in tre modi diversi_

Un arco è una curva sulla circonferenza di un'ellisse, ad esempio le parti di questo simbolo di infinito arrotondate:

![](arcs-images/arcsample.png "Simbolo di infinito")

Nonostante la semplicità della definizione, non è possibile definire una funzione di disegno dell'arco che soddisfa ogni esigenza, e di conseguenza, non il consenso dei sistemi grafici del modo migliore per disegnare l'arco. Per questo motivo, la `SKPath` classe non limita automaticamente a un unico approccio.

`SKPath` definisce un `AddArc` (metodo), cinque diverse `ArcTo` metodi e i relativi due `RArcTo` metodi. Questi metodi rientrano in tre categorie, che rappresenta tre diversi approcci per specificare un arco. Il metodo scelto dipende dalle informazioni disponibili per definire l'arco e adattamento l'arco con gli altri elementi grafici che si sta creando.

## <a name="the-angle-arc"></a>L'angolo di arco

L'approccio di arco angolo per archi di disegno è necessario specificare un rettangolo che delimita un'ellisse. L'arco nella circonferenza di questa ellisse è indicato da angoli dal centro dell'ellisse effettua l'inizio dell'arco e la relativa lunghezza. Due metodi diversi disegnano archi angolo. Si tratta di [ `AddArc` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/) (metodo) e il [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKRect/System.Single/System.Single/System.Boolean/) metodo:

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

Questi metodi sono identici per il Android [ `AddArc` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.AddArc/p/Android.Graphics.RectF/System.Single/System.Single/) e [ `ArcTo` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.ArcTo/p/Android.Graphics.RectF/System.Single/System.Single/System.Boolean/) metodi. IOS [ `AddArc` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArc/p/System.Boolean/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/) metodo è simile, ma è limitato a archi nella circonferenza del cerchio anziché generalizzato in un'ellisse.

Entrambi i metodi iniziano con un `SKRect` valore che definisce la posizione e dimensioni di un'ellisse:

![](arcs-images/anglearcoval.png "Oval che inizia un arco angolo")

L'arco fa parte la circonferenza di questa ellisse.

Il `startAngle` argomento è un angolo in senso orario espresso in gradi rispetto alla linea orizzontale disegnata dal centro dell'ellisse a destra. Il `sweepAngle` argomento è relativo al `startAngle`. Di seguito sono `startAngle` e `sweepAngle` i valori di 100 e 60 gradi, rispettivamente:

![](arcs-images/anglearcangles.png "Aspetti che definiscono un arco angolo")

Inizia l'angolo iniziale dell'arco. La lunghezza è disciplinata dall'angolo di apertura:

![](arcs-images/anglearchighlight.png "L'arco angolo evidenziato")

La curva aggiunta al percorso con il `AddArc` o `ArcTo` metodo è sufficiente che una parte della circonferenza dell'ellisse, illustrata di seguito in rosso:

![](arcs-images/anglearc.png "L'arco angolo da solo")

Il `startAngle` o `sweepAngle` gli argomenti possono essere negativi: l'arco è in senso orario per i valori positivi di `sweepAngle` e in senso antiorario per i valori negativi.

Tuttavia, `AddArc` does *non* definiscono un contorno chiuso. Se si chiama `LineTo` dopo `AddArc`, una linea viene tracciata dalla fine dell'arco al punto di `LineTo` (metodo) e lo stesso vale per `ArcTo`.

`AddArc` inizia un nuovo contorno automaticamente ed è funzionalmente equivalente a una chiamata a `ArcTo` con un argomento finale di `true`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

Che viene chiamato l'ultimo argomento `forceMoveTo`, e fa in modo efficace un `MoveTo` chiamare all'inizio dell'arco. Che inizia una nuova distribuzione. Ovvero non nel caso di un argomento di ultima `false`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

Questa versione di `ArcTo` Disegna una riga dalla posizione corrente all'inizio dell'arco. Ciò significa che l'arco può essere in qualche punto all'interno di una distribuzione più grande.

Il **arco angolo** pagina consente di usare due dispositivi di scorrimento per specificare l'inizio e durante lo sweep angoli. Il file XAML crea un'istanza di due `Slider` elementi e un `SKCanvasView`. Il `PaintCanvas` gestore di [ **AngleArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs) file disegna ovale sia l'arco con due `SKPaint` gli oggetti definiti come campi:

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

Come si può notare, sia l'angolo iniziale e l'angolo di apertura può assumere i valori negativi:

[![](arcs-images/anglearc-small.png "Schermata triplo della pagina arco angolo")](arcs-images/anglearc-large.png#lightbox "tripla schermata della pagina angolo arco")

Questo approccio per la generazione di un arco è modo algoritmico la più semplice e facile derivare le equazioni parametriche che descrivono l'arco. Conoscere le dimensioni e posizione dei puntini di sospensione e gli angoli di inizio e di apertura, i punti iniziale e finale dell'arco può essere calcolati con trigonometria semplice:

x = oval. MidX + (oval. Larghezza / 2) * cos(angle)

y = oval. MidY + (oval. Altezza / 2) * sin(angle)

Il `angle` il valore è `startAngle` o `startAngle + sweepAngle`.

L'utilizzo di due angoli per definire un arco è ideale per i casi in cui si conosce la lunghezza dell'arco da disegnare, ad esempio, per creare un grafico a torta angolare. Il **grafico a torta esplosa** pagina viene illustrata questa. Il [ `ExplodedPieChartPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs) classe utilizza una classe interna per definire alcuni dati creati e i colori:

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

Il `PaintSurface` gestore prima scorre in ciclo gli elementi da calcolare un `totalValues` numero. Tramite la quale può determinare la dimensione di ogni elemento come frazione del totale e convertirli in un angolo:

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

Un nuovo `SKPath` viene creato l'oggetto per ogni sezione della torta. Il percorso è costituito da una riga dal centro, quindi un `ArcTo` per tracciare l'arco e un'altra riga nuovamente i risultati center il `Close` chiamare. Questo programma consente di visualizzare le sezioni "esploso" spostandoli tutte le risorse dal centro di 50 pixel. L'attività richiede un vettore di direzione del punto centrale dell'angolo di apertura per ogni sezione:

[![](arcs-images/explodedpiechart-small.png "Schermata di triplo della pagina del grafico a torta esplosa")](arcs-images/explodedpiechart-large.png#lightbox "tripla schermata della pagina del grafico a torta esplosa")

Per visualizzare l'aspetto senza "esplosione", è sufficiente impostare come commento il `Translate` chiamare:

[![](arcs-images/explodedpiechartunexploded-small.png "Schermata triplo della pagina di grafico a torta esplosa senza l'esplosione")](arcs-images/explodedpiechartunexploded-large.png#lightbox "tripla schermata della pagina senza l'esplosione grafico a torta esplosa")

## <a name="the-tangent-arc"></a>L'arco tangente

Il secondo tipo di arco supportato da `SKPath` è il *arco tangente*, pertanto chiamato perché l'arco è la circonferenza del cerchio tangente a due linee collegate.

Un arco tangente viene aggiunto a un percorso con una chiamata al [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/System.Single/) metodo con due `SKPoint` parametri, o [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/) overload con separato `Single` parametri per il punti:

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

Questo `ArcTo` metodo è simile al PostScript [ `arct` ](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) funzione (pagina 532 nel documento PDF) e iOS [ `AddArcToPoint` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArcToPoint/p/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/) metodo.

Il `ArcTo` metodo prevede tre punti:

- Punto di corrente della distribuzione o il punto (0, 0) se `MoveTo` non è stato chiamato
- Il primo argomento punto il `ArcTo` metodo, denominato il *angolo punto*
- Il secondo argomento punto `ArcTo`, denominato il *punto di destinazione*:

![](arcs-images/tangentarcthreepoints.png "Tre punti che iniziano un arco tangente")

Questi tre punti definiscono due linee collegate:

![](arcs-images/tangentarcconnectinglines.png "Linee che collegano i tre punti di un arco tangente")

Se i tre punti sono collineari &mdash; , ovvero se esse si trovino nella stessa linea retta &mdash; non verrà disegnato alcun arco.

Il `ArcTo` metodo include anche un `radius` parametro. Definisce il raggio del cerchio:

![](arcs-images/tangentarccircle.png "Il cerchio di un arco tangente")

Non è stato generalizzato l'arco tangente per un'ellisse.

Se le due righe soddisfano angolazione, tale cerchio può essere inserita tra le righe in modo che sia tangente in entrambe le righe:

![](arcs-images/tangentarctangentcircle.png "Il cerchio arco tangente tra due linee")

Non tocca uno dei punti di specificato in curva che viene aggiunto per la distribuzione di `ArcTo` metodo. È costituito da una linea retta dal punto corrente per il primo punto di controllo di tangente e un arco che termina in corrispondenza del secondo punto tangente:

![](arcs-images/tangentarchighlight.png "L'arco tangente evidenziata tra le due linee")

Di seguito è la linea retta finale e un arco che viene aggiunto per la distribuzione:

![](arcs-images/tangentarc.png "L'arco tangente evidenziata tra le due linee")

È possibile continuare la distribuzione dal secondo punto tangente.

Il **arco tangente** pagina consente di sperimentare l'arco tangente. Questo è il primo di più pagine che derivano da [ `InteractivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/InteractivePage.cs), che definisce alcune utili `SKPaint` oggetti ed esegue `TouchPoint` elaborazione:

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

La classe `TangentArcPage` deriva da `InteractivePage`. Il costruttore nel [ **TangentArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs) è responsabile della creazione di un'istanza e l'inizializzazione di file di `touchPoints` matrice e impostazione `baseCanvasView` (in `InteractivePage`) per il `SKCanvasView` creata un'istanza di oggetto nel [ **TangentArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml) file:

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

Il `PaintSurface` gestore utilizza il `ArcTo` metodo per disegnare l'arco basato sui punti di tocco e un `Slider`, ma anche modo algoritmico calcola il cerchio in base all'angolo:

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

Ecco il **arco tangente** in esecuzione in tutti e tre le piattaforme di pagina:

[![](arcs-images/tangentarc-small.png "Schermata triplo della pagina arco tangente")](arcs-images/tangentarc-large.png#lightbox "tripla schermata della pagina arco tangente")

Nel dispositivo Windows Mobile, i tre punti sono collineari quasi e l'arco è molto piccolo.

L'arco tangente è ideale per la creazione di angoli arrotondati, ad esempio un rettangolo arrotondato. Poiché `SKPath` include già un `AddRoundedRect` (metodo), il **arrotondato Heptagon** pagina viene illustrato come utilizzare `ArcTo` per arrotondare gli angoli di un poligono sette lati. (Il codice è generalizzato per qualsiasi poligono regolare).

Il `PaintSurface` gestore del [ `RoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) classe contiene un `for` ciclo per calcolare le coordinate dei sette vertici il heptagon e un secondo per calcolare i punti centrali dei sette lati da queste vertici. Questi punti centrali vengono utilizzati per costruire il percorso:

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

Di seguito è riportato il programma in esecuzione su tre piattaforme:

[![](arcs-images/roundedheptagon-small.png "Schermata triplo della pagina arrotondato Heptagon")](arcs-images/roundedheptagon-large.png#lightbox "tripla schermata della pagina Heptagon arrotondato")

## <a name="the-elliptical-arc"></a>Arco ellittico

Arco ellittico viene aggiunto a un percorso con una chiamata al [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKPoint/System.Single/SkiaSharp.SKPathArcSize/SkiaSharp.SKPathDirection/SkiaSharp.SKPoint/) metodo che dispone di due `SKPoint` parametri, o [ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathArcSize/SkiaSharp.SKPathDirection/System.Single/System.Single/) eseguire l'overload con separato X e Y coordinate:

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

Arco ellittico è coerenza con il [arco ellittico](http://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands) incluso in Scalable Vector Graphics (SVG) e la piattaforma Windows universale [ `ArcSegment` ](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) classe.

Questi `ArcTo` metodi disegnano un arco tra due punti, ovvero il punto corrente del contorno, e l'ultimo parametro per il `ArcTo` metodo (il `xy` parametro o singole `x` e `y` parametri):

![](arcs-images/ellipticalarcpoints.png "I due punti definiti un arco ellittico")

Il primo parametro punto il `ArcTo` metodo (`r`, o `rx` e `ry`) non è considerato un punto, ma specifica invece il raggio orizzontale e verticale di un'ellisse.

![](arcs-images/ellipticalarcellipse.png "L'ellisse definita da un arco ellittico")

Il `xAxisRotate` è il numero di gradi in senso orario di rotazione questa ellisse:

![](arcs-images/ellipticalarctiltedellipse.png "L'ellisse inclinato definito un arco ellittico")

Se questa ellisse inclinata quindi non è posizionata in modo che tocca i due punti, i punti vengono collegati da due archi diversi:

![](arcs-images/ellipticalarcellipse1.png "Il primo set di arco ellittico")

È possibile distinguere questi due archi in due modi: l'arco superiore è maggiore dell'arco inferiore e come l'arco viene disegnato da sinistra a destra, l'arco superiore viene disegnato in senso orario, mentre l'arco inferiore viene disegnato in senso antiorario.

È inoltre possibile adattare l'ellisse tra i due punti in un altro modo:

![](arcs-images/ellipticalarcellipse2.png "Il secondo set di arco ellittico")

Ora è presente un arco più piccolo che viene disegnato in senso orario nella parte superiore e un arco più grande nella parte inferiore che viene disegnato in senso antiorario.

Questi due punti possono quindi essere collegati da un arco definito da ellisse inclinato in un totale di quattro modi:

![](arcs-images/ellipticalarccolors.png "Tutti e quattro gli archi ellittici")

Queste quattro archi si distinguono dalle combinazioni di quattro il [ `SKPathArcSize` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathArcSize/) e [ `SKPathDirection` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathDirection/) gli argomenti di tipo enumerazione il `ArcTo` metodo:

- rosso: SKPathArcSize.Large e SKPathDirection.Clockwise
- verde: SKPathArcSize.Small e SKPathDirection.Clockwise
- blu: SKPathArcSize.Small e SKPathDirection.CounterClockwise
- magenta: SKPathArcSize.Large e SKPathDirection.CounterClockwise

Se l'ellisse inclinato non è sufficientemente grande per tra i due punti, viene ridimensionato in modo uniforme fino a quando non è sufficientemente grande. Solo due archi univoci connettono in questo caso i due punti. Questi possono essere distinte con il `SKPathDirection` parametro.

Sebbene questo approccio per la definizione di un arco suoni complesso incontrata prima, è l'unico approccio che consente di definire un arco con un ellisse ruotata, ed è spesso l'approccio più semplice quando è necessario integrare archi con altre parti del contorno.

Il **arco ellittico** pagina consente di impostare in modo interattivo i due punti e la dimensione e la rotazione dell'ellisse. Il `EllipticalArcPage` deriva dalla classe `InteractivePage`e `PaintSurface` gestore il [ **EllipticalArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs) file code-behind disegna quattro archi:

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

Qui è in esecuzione su tre piattaforme:

[![](arcs-images/ellipticalarc-small.png "Schermata di triplo della pagina arco ellittico")](arcs-images/ellipticalarc-large.png#lightbox "tripla schermata della pagina arco ellittico")

Il **arco infinito** pagina utilizza l'arco ellittico per disegnare un simbolo di infinito. Il simbolo di infinito si basa su due cerchi con un raggio di 100 unità separati da 100 unità:

![](arcs-images/infinitycircles.png "Due cerchi")

Due perpendicolari tra loro sono tangente a entrambi i cerchi:

![](arcs-images/infinitycircleslines.png "Due cerchi con tangenti")

Il simbolo di infinito è una combinazione delle parti di questi cerchi e le due righe. Per usare l'arco ellittico per disegnare il simbolo di infinito, è necessario determinare le coordinate in cui le due righe sono tangente ai cerchi.

Creare un rettangolo a destra in uno dei cerchi:

![](arcs-images/infinitytriangle.png "Due cerchi con tangenti e cerchio incorporato")

Il raggio del cerchio è 100 unità e l'ipotenusa del triangolo è 150 unità, pertanto l'angolo α è l'arcoseno (funzione inversa del seno) pari a 100 diviso per 150, o gradi 41.8. La lunghezza di altro lato del triangolo è 150 volte il coseno di gradi 41.8 o 112, può anche essere calcolato per il teorema pitagorica.

Le coordinate del punto di controllo di tangente possono quindi essere calcolate con queste informazioni:

x = 112·cos(41.8) = 83

y = 112·sin(41.8) = 75

I quattro punti di controllo di tangente sono tutto ciò che è necessario per disegnare un segno di infinito centrato nel punto (0, 0) con un raggio cerchio di 100:

![](arcs-images/infinitycoordinates.png "Due cerchi con le coordinate e tangenti")

Il `PaintSurface` gestore di [ `ArcInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) classe posiziona il simbolo di infinito in modo che il (0, 0) punto viene posizionato al centro della pagina e scalare il percorso per le dimensioni dello schermo:

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

Il codice Usa il `Bounds` proprietà `SKPath` per determinare le dimensioni del seno infinito per ridimensionarlo per le dimensioni dell'area di disegno:

[![](arcs-images/arcinfinity-small.png "Schermata triplo della pagina arco infinito")](arcs-images/arcinfinity-large.png#lightbox "tripla schermata della pagina infinito arco")

Il risultato appare un po' di piccole dimensioni, che suggerisce che il `Bounds` proprietà `SKPath` è di dimensioni maggiori rispetto al percorso di reporting.

Internamente, Skia all'incirca l'arco con più di Bézier quadratica. Queste curve (come verrà visualizzato nella sezione successiva) contengono punti di controllo che determinano come viene disegnata la curva ma non fanno parte della curva sottoposto a rendering. Il `Bounds` proprietà include i punti di controllo.

Per ottenere un migliore adattamento, utilizzare il `TightBounds` proprietà, che consente di escludere i punti di controllo. Ecco il programma in esecuzione in modalità orizzontale e l'utilizzo di `TightBounds` proprietà per ottenere i limiti di percorso:

[![](arcs-images/arcinfinitytightbounds-small.png "Schermata triplo della pagina arco infinito con limiti di una stretta")](arcs-images/arcinfinitytightbounds-large.png#lightbox "tripla schermata della pagina arco infinito con limiti di una stretta")

Anche se le connessioni tra l'archi e linee rette sono matematicamente smooth, la modifica da arco linea retta potrebbe sembrare poco improvvisa. Nella pagina successiva, viene visualizzato un simbolo di infinito migliorato.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
