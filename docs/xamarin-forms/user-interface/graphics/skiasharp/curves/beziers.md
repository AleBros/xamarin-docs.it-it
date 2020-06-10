---
title: "tre tipi di curve di Bézier" Description: "in questo articolo viene illustrato come utilizzare SkiaSharp per eseguire il rendering di curve di Bézier cubiche, quadratiche e coniche nelle Xamarin.Forms applicazioni e come illustrato nel codice di esempio".
ms. prod: Novell MS. Technology: Novell-skiasharp ms. AssetID: 8FE0F6DC-16BC-435F-9626-DD1790C0145A autore: davidbritch ms. Author: dabritch ms. Date: 05/25/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="three-types-of-bzier-curves"></a>Tre tipi di curve di Bézier

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Scopri come usare SkiaSharp per eseguire il rendering di curve di Bézier cubiche, quadratiche e coniche_

La curva di Bézier è denominata dopo Pierre Bézier (1910 – 1999), un ingegnere francese presso la società Automotive Renault, che ha usato la curva per la progettazione assistita da computer di carrozzerie.

Le curve di Bézier sono note per la progettazione interattiva. si tratta di un comportamento corretto &mdash; in altre parole, non ci sono singolarità che comportano la curva di diventare infinite o poco ingombranti &mdash; e sono in genere gradevoli da un punto di vista estetico:

![Curva di Bezier di esempio](beziers-images/beziersample.png)

Le strutture di caratteri dei tipi di carattere basati su computer vengono in genere definite con curve di Bézier.

L'articolo di Wikipedia sulla [**curva di Bézier**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contiene alcune informazioni di base utili. Il termine *curva di Bézier* fa effettivamente riferimento a una famiglia di curve simili. SkiaSharp supporta tre tipi di curve di Bézier, denominate *cubiche*, *quadratiche*e *coniche*. La conica è nota anche come *quadratico razionale*.

## <a name="the-cubic-bzier-curve"></a>Curva di Bézier cubica

Il cubo è il tipo di curva di Bézier che la maggior parte degli sviluppatori pensa quando viene visualizzato l'oggetto delle curve di Bézier.

È possibile aggiungere una curva di Bézier cubica a un `SKPath` oggetto usando il [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) metodo con tre `SKPoint` parametri o l' [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) Overload con `x` i `y` parametri e separati:

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

La curva inizia in corrispondenza del punto corrente del contorno. La curva di Bezier cubica completa è definita da quattro punti:

- punto iniziale: punto corrente nel contorno o (0, 0) se `MoveTo` non è stato chiamato
- primo punto di controllo: `point1` nella `CubicTo` chiamata
- secondo punto di controllo: `point2` nella `CubicTo` chiamata
- endpoint: `point3` nella `CubicTo` chiamata

La curva risultante inizia in corrispondenza del punto iniziale e termina in corrispondenza del punto finale. La curva in genere non passa attraverso i due punti di controllo; al contrario, i punti di controllo funzionano in modo molto simile ai magneti per tirare la curva verso di essi.

Il modo migliore per ottenere la curva di Bézier cubica è la sperimentazione. Questo è lo scopo della pagina della **curva di Bezier** , che deriva da `InteractivePage` . Il file [**BezierCurvePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) crea un'istanza di `SKCanvasView` e `TouchEffect` . Il file code-behind [**BezierCurvePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) crea quattro `TouchPoint` oggetti nel relativo costruttore. Il `PaintSurface` gestore eventi crea un oggetto `SKPath` per eseguire il rendering di una curva di Bézier in base ai quattro `TouchPoint` oggetti e disegna anche le linee tangente punteggiate dai punti di controllo agli endpoint:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[2].Center.X,
                    touchPoints[2].Center.Y,
                    touchPoints[3].Center.X,
                    touchPoints[3].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
       touchPoint.Paint(canvas);
    }
}
```

In esecuzione:

[![Schermata tripla della pagina curva di Bezier](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

Matematicamente, la curva è un polinomio cubico. La curva interseca una linea retta a tre punti al massimo. Nel punto iniziale la curva è sempre tangente a e nella stessa direzione di una linea retta dal punto iniziale al primo punto di controllo. Al punto finale, la curva è sempre tangente a e nella stessa direzione di una linea retta dal secondo punto di controllo al punto finale.

La curva di Bézier cubica è sempre vincolata da un quadrilatero convesso che connette i quattro punti. Si tratta di una struttura *convessa*. Se i punti di controllo si trovano sulla linea retta tra il punto iniziale e quello finale, la curva di Bézier viene visualizzata come linea retta. La curva può anche incrociare se stessa, come illustrato nella terza schermata.

Un contorno del percorso può contenere più curve di Bézier cubiche connesse, ma la connessione tra due curve di Bézier cubiche sarà uniforme solo se i tre punti seguenti sono colineari (ovvero si trovano su una linea retta):

- secondo punto di controllo della prima curva
- punto finale della prima curva, che rappresenta anche il punto iniziale della seconda curva
- primo punto di controllo della seconda curva

Nell'articolo successivo sui [**dati del percorso SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)si scoprirà una funzionalità per semplificare la definizione di curve di Bézier con connessione uniforme.

A volte è utile comprendere le equazioni parametriche sottostanti che eseguono il rendering di una curva di Bézier cubica. Per *t* compreso tra 0 e 1, le equazioni parametriche sono le seguenti:

x (t) = (1 – t) ³ x ₀ + 3T (1 – t) ² x ₁ + 3T ² (1 – t) x ₂ + t ³ x ₃

y (t) = (1 – t) ³ y ₀ + 3T (1 – t) ² y ₁ + 3T ² (1 – t) y ₂ + t ³ y ₃

L'esponente più alto di 3 conferma che questi sono polinomi cubici. È facile verificare che quando è `t` uguale a 0, il punto è (x ₀, y ₀), che è il punto iniziale e quando è `t` uguale a 1, il punto è (x ₃, y ₃), che corrisponde al punto finale. Accanto al punto iniziale (per i valori bassi di `t` ), il primo punto di controllo (x ₁, y ₁) ha un effetto forte e, in prossimità del punto finale (valori elevati di ' t'), il secondo punto di controllo (x ₂, y ₂) ha un effetto forte.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Approssimazione curva di Bezier a archi circolari

A volte è utile usare una curva di Bézier per eseguire il rendering di un arco circolare. Una curva di Bézier cubica può approssimare un arco circolare molto bene fino a un cerchio di quarto, quindi quattro curve di Bézier connesse possono definire un cerchio intero. Questa approssimazione viene discussa in due articoli pubblicati più di 25 anni fa:

> Tor Dokken, et al, "una corretta approssimazione dei cerchi per curvare-curve di Bézier continue", " *Computer Aided Geometric Design 7* (1990), 33-41.

> Michael Goldapp, "approssimazione di archi circolari in base ai polinomi cubici", " *Computer Aided Geometric Design 8* (1991), 227-238.

Il diagramma seguente illustra quattro punti con etichetta `pto` , `pt1` , `pt2` e la `pt3` definizione di una curva di Bézier (mostrata in rosso) che si avvicina a un arco circolare:

![Approssimazione di un arco circolare con una curva di Bézier](beziers-images/bezierarc45.png)

Le linee dall'inizio e dalla fine puntano ai punti di controllo sono tangenti al cerchio e alla curva di Bézier e hanno una lunghezza pari a *L*. Il primo articolo citato sopra indica che la curva di Bézier è più simile a un arco circolare quando tale lunghezza *L* viene calcolata nel modo seguente:

L = 4 × tan (α/4)/3

L'illustrazione mostra un angolo di 45 gradi, quindi L è uguale a 0,265. Nel codice, tale valore verrebbe moltiplicato per il raggio desiderato del cerchio.

La pagina di **arco circolare di Bezier** consente di provare a definire una curva di Bézier per approssimare un arco circolare per gli angoli compresi tra un massimo di 180 gradi. Il file [**BezierCircularArcPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) crea un'istanza `SKCanvasView` di e un oggetto `Slider` per la selezione dell'angolo. Il `PaintSurface` gestore eventi nel file code-behind [**BezierCircularArgPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) usa una trasformazione per impostare il punto (0,0) sul centro dell'area di disegno. Disegna un cerchio centrato su tale punto per il confronto, quindi calcola i due punti di controllo per la curva di Bézier:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 3;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate length of control point line
    float length = radius * 4 * (float)Math.Tan(Math.PI * angle / 180 / 4) / 3;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the end points
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point3 = new SKPoint(radius * sin, radius * cos);

    // Find the control points
    SKPoint point0Normalized = Normalize(point0);
    SKPoint point1 = point0 + new SKPoint(length * point0Normalized.Y,
                                          -length * point0Normalized.X);

    SKPoint point3Normalized = Normalize(point3);
    SKPoint point2 = point3 + new SKPoint(-length * point3Normalized.Y,
                                          length * point3Normalized.X);

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);
    canvas.DrawCircle(point3.X, point3.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point3.X, point3.Y, point2.X, point2.Y, dottedStroke);

    // Draw the Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.CubicTo(point1, point2, point3);
        canvas.DrawPath(path, redStroke);
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

```

I punti iniziale e finale ( `point0` e `point3` ) vengono calcolati in base alle equazioni parametriche normali per il cerchio. Poiché il cerchio è centrato su (0, 0), questi punti possono anche essere trattati come vettori radiali dal centro del cerchio alla circonferenza. I punti di controllo si trovano su righe che sono tangenti al cerchio, quindi sono ad angolo retto a questi vettori radiali. Un vettore a un angolo retto a un altro è semplicemente il vettore originale con le coordinate X e Y scambiate e una di esse ha reso negativo.

Ecco il programma in esecuzione con diversi angoli:

[![Schermata tripla della pagina di arco circolare di Bezier](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

Osservare attentamente il terzo screenshot. si noterà che la curva di Bézier è in particolare deviare da un semicerchio quando l'angolo è di 180 gradi, ma la schermata iOS Mostra che sembra adattarsi a un cerchio di quarti quando l'angolo è di 90 gradi.

Il calcolo delle coordinate dei due punti di controllo è piuttosto semplice quando il cerchio del trimestre è orientato come segue:

![Approssimazione di un cerchio del trimestre con una curva di Bézier](beziers-images/bezierarc90.png)

Se il raggio del cerchio è 100, *L* è 55 e questo è un numero semplice da ricordare.

La **quadratura della pagina del cerchio** anima una figura tra un cerchio e un quadrato. Il cerchio è approssimato da quattro curve di Bézier le cui coordinate sono mostrate nella prima colonna di questa definizione di matrice nella [`SquaringTheCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) classe:

```csharp
public class SquaringTheCirclePage : ContentPage
{
    SKPoint[,] points =
    {
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() },
        { new SKPoint(  55,  100), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,   55), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,    0), new SKPoint(   125,      0), new SKPoint() },
        { new SKPoint( 100,  -55), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(  55, -100), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(   0, -100), new SKPoint(     0,   -125), new SKPoint() },
        { new SKPoint( -55, -100), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,  -55), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,    0), new SKPoint(  -125,      0), new SKPoint() },
        { new SKPoint(-100,   55), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint( -55,  100), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() }
    };
    ...
}
```

La seconda colonna contiene le coordinate di quattro curve di Bézier che definiscono un quadrato la cui area è approssimativamente uguale all'area del cerchio. (Il disegno di un quadrato con l'area *esatta* come un determinato cerchio è il problema geometrico solubile classico per [la quadratura del cerchio](https://en.wikipedia.org/wiki/Squaring_the_circle)). Per il rendering di un quadrato con curve di Bézier, i due punti di controllo per ogni curva sono gli stessi e sono colineari con i punti iniziale e finale, quindi la curva di Bézier viene sottoposta a rendering come linea retta.

La terza colonna della matrice è per i valori interpolati per un'animazione. La pagina imposta un timer per 16 millisecondi e il `PaintSurface` gestore viene chiamato alla velocità seguente:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    canvas.Translate(info.Width / 2, info.Height / 2);
    canvas.Scale(Math.Min(info.Width / 300, info.Height / 300));

    // Interpolate
    TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
    float t = (float)(timeSpan.TotalSeconds % 3 / 3);   // 0 to 1 every 3 seconds
    t = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;     // 0 to 1 to 0 sinusoidally

    for (int i = 0; i < 13; i++)
    {
        points[i, 2] = new SKPoint(
            (1 - t) * points[i, 0].X + t * points[i, 1].X,
            (1 - t) * points[i, 0].Y + t * points[i, 1].Y);
    }

    // Create the path and draw it
    using (SKPath path = new SKPath())
    {
        path.MoveTo(points[0, 2]);

        for (int i = 1; i < 13; i += 3)
        {
            path.CubicTo(points[i, 2], points[i + 1, 2], points[i + 2, 2]);
        }
        path.Close();

        canvas.DrawPath(path, cyanFill);
        canvas.DrawPath(path, blueStroke);
    }
}
```

I punti vengono interpolati in base a un valore con oscillazione sinusoidale pari a `t` . I punti interpolati vengono quindi usati per costruire una serie di quattro curve di Bézier connesse. Ecco l'animazione in esecuzione:

[![Schermata tripla del quadrato della pagina del cerchio](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

Questa animazione potrebbe non essere possibile senza curve algoritmicamente sufficientemente flessibili da essere visualizzate come archi circolari e linee rette.

La pagina **Infinity di Bezier** sfrutta anche la capacità di una curva di Bézier di approssimare un arco circolare. Ecco il `PaintSurface` gestore della [`BezierInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) classe:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.MoveTo(0, 0);                                // Center
        path.CubicTo(  50,  -50,   95, -100,  150, -100); // To top of right loop
        path.CubicTo( 205, -100,  250,  -55,  250,    0); // To far right of right loop
        path.CubicTo( 250,   55,  205,  100,  150,  100); // To bottom of right loop
        path.CubicTo(  95,  100,   50,   50,    0,    0); // Back to center  
        path.CubicTo( -50,  -50,  -95, -100, -150, -100); // To top of left loop
        path.CubicTo(-205, -100, -250,  -55, -250,    0); // To far left of left loop
        path.CubicTo(-250,   55, -205,  100, -150,  100); // To bottom of left loop
        path.CubicTo( -95,  100,  -50,   50,    0,    0); // Back to center
        path.Close();

        SKRect pathBounds = path.Bounds;
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.9f * Math.Min(info.Width / pathBounds.Width,
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

Potrebbe essere utile tracciare queste coordinate sul grafico per vedere come sono correlate. Il segno di infinito è centrato intorno al punto (0, 0) e i due cicli hanno centri di (-150, 0) e (150, 0) e raggi di 100. Nella serie di `CubicTo` comandi, è possibile visualizzare le coordinate X dei punti di controllo che assumono i valori – 95 e – 205 (i valori sono – 150 Plus e meno 55), 205 e 95 (150 Plus e meno 55), oltre a 250 e-250 per i lati destro e sinistro. L'unica eccezione è quando il segno di infinito si incrocia al centro. In tal caso, i punti di controllo hanno coordinate con una combinazione di 50 e – 50 per raddrizzare la curva vicino al centro.

Ecco il segno di infinito:

[![Schermata tripla della pagina dell'infinito di Bézier](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

È leggermente più uniforme verso il centro rispetto al segno di infinito sottoposto a rendering dalla pagina **Infinity Arc** dei [**tre modi per creare un articolo di arco**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) .

## <a name="the-quadratic-bzier-curve"></a>Curva di Bézier quadratica

La curva di Bézier quadratica ha un solo punto di controllo e la curva è definita da soli tre punti: il punto iniziale, il punto di controllo e il punto finale. Le equazioni parametriche sono molto simili alla curva di Bézier cubica, tranne per il fatto che l'esponente più alto è 2, quindi la curva è un polinomio quadratico:

x (t) = (1 – t) ² x ₀ + 2T (1 – t) x ₁ + t ² x ₂

y (t) = (1 – t) ² y ₀ + 2T (1 – t) y ₁ + t ² y ₂

Per aggiungere una curva di Bézier quadratica a un tracciato, utilizzare il [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) metodo o l' [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) Overload con `x` le `y` coordinate e separate:

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

I metodi aggiungono una curva dalla posizione corrente a `point2` con `point1` come punto di controllo.

È possibile sperimentare curve di Bézier quadratiche con la pagina della **curva quadratica** , che è molto simile alla pagina della **curva di Bezier** , ad eccezione del fatto che contiene solo tre punti di tocco. Ecco il `PaintSurface` gestore nel file code-behind [**QuadraticCurve.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with quadratic Bezier
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.QuadTo(touchPoints[1].Center,
                    touchPoints[2].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

E qui è in esecuzione:

[![Schermata tripla della pagina della curva quadratica](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

Le linee tratteggiate sono tangenti alla curva in corrispondenza del punto iniziale e finale e vengono soddisfatte al punto di controllo.

La Bézier quadratica è utile se è necessaria una curva di forma generale, ma si preferisce la convenienza di un solo punto di controllo anziché di due. Il rendering di Bézier quadratico risulta più efficiente rispetto a qualsiasi altra curva, motivo per cui viene usato internamente in Skia per eseguire il rendering degli archi ellittici.

Tuttavia, la forma di una curva di Bézier quadratica non è ellittica, motivo per cui sono necessari più Bézier quadratici per approssimare un arco ellittico. La Bézier quadratica è invece un segmento di una parabola.

## <a name="the-conic-bzier-curve"></a>Curva di Bézier conica

La curva di Bézier conica &mdash; , nota anche come curva di Bézier quadratica razionale, &mdash; è un'aggiunta relativamente recente alla famiglia di curve di Bézier. Analogamente alla curva di Bézier quadratica, la curva di Bézier quadratica razionale include un punto iniziale, un punto finale e un punto di controllo. Tuttavia, la curva di Bézier quadratica razionale richiede anche un valore di *peso* . Viene chiamato un quadratino *razionale* perché le formule parametriche coinvolgono i rapporti.

Le equazioni parametriche per X e Y sono proporzioni che condividono lo stesso denominatore. Di seguito è illustrata l'equazione per il denominatore per *t* compreso tra 0 e 1 e il valore di peso *w*:

d (t) = (1 – t) ² + 2wt (1 – t) + t ²

In teoria, un quadratico razionale può coinvolgere tre valori di peso distinti, uno per ognuno dei tre termini, ma questi possono essere semplificati in un solo valore di peso nel periodo intermedio.

Le equazioni parametriche per le coordinate X e Y sono simili alle equazioni parametriche per la Bézier quadratica, tranne per il fatto che il termine intermedio include anche il valore del peso e l'espressione è divisa per il denominatore:

x (t) = ((1 – t) ² x ₀ + 2wt (1 – t) x ₁ + t ² x ₂)) ÷ d (t)

y (t) = ((1 – t) ² y ₀ + 2wt (1 – t) y ₁ + t ² y ₂)) ÷ d (t)

Anche le curve di Bézier quadratiche razionali sono denominate *coniche* , in quanto possono rappresentare esattamente segmenti di qualsiasi iperbole di sezione conica &mdash; , paraboliche, ellissi e cerchi.

Per aggiungere una curva di Bézier quadratica razionale a un tracciato, usare il [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) metodo o l' [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) Overload `x` con `y` coordinate e separate:

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Si noti il `weight` parametro Final.

La pagina della **curva conica** consente di sperimentare le curve. La classe `ConicCurvePage` deriva da `InteractivePage`. Il file [**ConicCurvePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) crea un'istanza `Slider` di per selezionare un valore di peso compreso tra-2 e 2. Il file code-behind [**ConicCurvePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) crea tre `TouchPoint` oggetti e il `PaintSurface` gestore esegue semplicemente il rendering della curva risultante con le linee della tangente nei punti di controllo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with conic curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.ConicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     (float)weightSlider.Value);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

In esecuzione:

[![Schermata tripla della pagina a curva conica](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

Come si può notare, il punto di controllo sembra tirare la curva verso l'alto quando il peso è superiore. Quando il peso è zero, la curva diventa una linea retta dal punto iniziale fino al punto finale.

In teoria, i pesi negativi sono consentiti e determinano la curvatura della *curva dal punto di controllo* . Tuttavia, i pesi di-1 o di seguito fanno sì che il denominatore nelle equazioni parametriche diventi negativo per determinati valori di *t*. Per questo motivo, è probabile che i pesi negativi vengano ignorati nei `ConicTo` metodi. Il programma di **curva conica** consente di impostare pesi negativi, ma come è possibile vedere sperimentando, i pesi negativi hanno lo stesso effetto di un peso di zero e determinano il rendering di una linea retta.

È molto facile derivare il punto di controllo e il peso per usare il `ConicTo` metodo per creare un arco circolare fino a un semicerchio, ma non includerlo. Nel diagramma seguente, le linee tangenti dei punti iniziale e finale vengono soddisfatte nel punto di controllo.

![Rendering di Arco conico di un arco circolare](beziers-images/conicarc.png)

È possibile usare la trigonometria per determinare la distanza del punto di controllo dal centro del cerchio: è il raggio del cerchio diviso per il coseno della metà dell'angolo α. Per creare un arco circolare tra i punti iniziale e finale, impostare il peso sullo stesso coseno della metà dell'angolo. Si noti che se l'angolo è 180 gradi, le linee tangente non vengono mai soddisfatte e il peso è zero. Ma per gli angoli inferiori a 180 gradi, la matematica funziona correttamente.

Questa operazione viene illustrata nella pagina di **arco circolare conica** . Il file [**ConicCircularArc. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) crea un'istanza `Slider` di un oggetto per la selezione dell'angolo. Il `PaintSurface` gestore nel file code-behind [**ConicCircularArc.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) calcola il punto di controllo e il peso:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 4;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the points and weight
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point1 = new SKPoint(0, radius / cos);
    SKPoint point2 = new SKPoint(radius * sin, radius * cos);
    float weight = cos;

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point2.X, point2.Y, point1.X, point1.Y, dottedStroke);

    // Draw the conic
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.ConicTo(point1, point2, weight);
        canvas.DrawPath(path, redStroke);
    }
}
```

Come si può notare, non esiste alcuna differenza visiva tra il `ConicTo` percorso visualizzato in rosso e il cerchio sottostante visualizzato come riferimento:

[![Schermata tripla della pagina di arco circolare conica](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

Tuttavia, impostare l'angolo su 180 gradi e la matematica ha esito negativo.

In questo caso, in questo caso, `ConicTo` non supporta i pesi negativi, perché in teoria, in base alle equazioni parametriche, il cerchio può essere completato con un'altra chiamata a `ConicTo` con gli stessi punti ma un valore negativo del peso. Ciò consentirebbe la creazione di un cerchio intero con solo due `ConicTo` curve basate su qualsiasi angolo tra (ma senza includere) zero gradi e 180 gradi.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
