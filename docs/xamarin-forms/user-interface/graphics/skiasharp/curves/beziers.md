---
title: Tre tipi di curve di Bézier
description: Questo articolo illustra come usare SkiaSharp per eseguire il rendering le curve di Bézier cubica continua, quadratiche e conica nelle applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
ms.openlocfilehash: 1cf061f2ff27720ad78567bc26f00d99c5456f04
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915910"
---
# <a name="three-types-of-bzier-curves"></a>Tre tipi di curve di Bézier

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Scopri come usare SkiaSharp per eseguire il rendering di curve di Bézier cubiche, quadratiche e coniche_

La curva di Bézier denominata dopo Pierre Bézier (1910: 1999), un francese engineer presso l'azienda automobilistica europea alleanza Renault, che hanno usato la curva per la progettazione assistita da computer di corpi di automobili.

Le curve di Bézier sono note per la progettazione interattiva. si tratta di un comportamento corretto &mdash; in altre parole, non ci sono singolarità che comportano la curva di diventare infinite o difficili da &mdash; e sono in genere gradevoli dal punto di vista estetico:

![Curva di Bezier di esempio](beziers-images/beziersample.png)

Contorni dei caratteri dei tipi di carattere basati su computer sono in genere definite con curve di Bézier.

L'articolo di Wikipedia sulla [**curva di Bézier**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contiene alcune informazioni di base utili. Il termine *curva di Bézier* fa effettivamente riferimento a una famiglia di curve simili. SkiaSharp supporta tre tipi di curve di Bézier, denominate *cubiche*, *quadratiche*e *coniche*. La conica è nota anche come *quadratico razionale*.

## <a name="the-cubic-bzier-curve"></a>Curva di Bézier cubica

La radice cubica è il tipo di curva di Bézier che molti sviluppatori ritengono di quando l'oggetto di curve di Bézier viene visualizzata.

È possibile aggiungere una curva di Bézier cubica a un oggetto `SKPath` usando il metodo [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) con tre parametri di `SKPoint` oppure l'overload di [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) con i parametri `x` e `y` distinti:

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

La curva inizia nel punto corrente per il contorno. La curva di Bézier cubica completata è definita da quattro punti:

- punto iniziale: punto corrente nel contorno o (0,0) se non è stato chiamato `MoveTo`
- primo punto di controllo: `point1` nella chiamata `CubicTo`
- secondo punto di controllo: `point2` nella chiamata `CubicTo`
- endpoint: `point3` nella chiamata `CubicTo`

La curva risultante inizia in corrispondenza del punto di inizio e termina in corrispondenza del punto di fine. La curva in genere non passa attraverso due punti di controllo; il controllo punta invece molto simile a magneti per eseguire il pull della curva conseguirli (funzione).

Il modo migliore per farsi un'idea della curva di Bézier cubica è dalle sperimentazioni. Questo è lo scopo della pagina della **curva di Bezier** che deriva da `InteractivePage`. Il file [**BezierCurvePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) crea un'istanza del `SKCanvasView` e di un `TouchEffect`. Il file code-behind [**BezierCurvePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) crea quattro oggetti `TouchPoint` nel relativo costruttore. Il gestore dell'evento `PaintSurface` crea un `SKPath` per eseguire il rendering di una curva di Bézier in base ai quattro oggetti `TouchPoint` e disegna anche le linee tangente punteggiate dai punti di controllo agli endpoint:

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

Qui è in esecuzione:

[![schermata tripla della pagina curva di Bezier](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

Matematica, la curva è una polinomiale cubica. La curva interseca al massimo una linea retta in tre punti. In corrispondenza del punto di inizio, la curva è sempre una linea retta dall'inizio tangente e nella stessa direzione, puntare al primo punto di controllo. In corrispondenza del punto di fine, la curva è sempre una linea retta nel secondo controllo tangente e nella stessa direzione, puntare al punto finale.

Curva di Bézier cubica è sempre limitata da un quadrilatero convesso che collega i quattro punti. Si tratta di una struttura *convessa*. Se si trovano i punti di controllo sulla linea retta tra i punti iniziale e finale, quindi la curva di Bézier esegue il rendering come una linea retta. Ma la curva può inoltre superare stesso, come illustrato di seguito nella terza schermata.

Una distribuzione del percorso può contenere più curve di Bézier cubiche collegate, ma la connessione tra due curve di Bézier cubiche verrà smooth solo se i seguenti tre punti sono collineari (vale a dire si trovano su una linea retta):

- il secondo punto di controllo della curva prima
- il punto finale della curva prima, che è anche il punto iniziale della curva secondo
- il primo punto di controllo della curva secondo

Nell'articolo successivo sui [**dati del percorso SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)si scoprirà una funzionalità per semplificare la definizione di curve di Bézier con connessione uniforme.

In alcuni casi è utile conoscere le equazioni parametriche sottostanti che eseguono il rendering di una curva di Bézier cubica. Per *t* compreso tra 0 e 1, le equazioni parametriche sono le seguenti:

x(t) = (1-t) ³x₀ 3t + (1-t) ²x₁ 3t² + (1-t) x₂ t³x₃ +

y(t) = (1-t) ³y₀ 3t + (1-t) ²y₁ 3t² + (1-t) y₂ t³y₃ +

L'esponente massima pari a 3 conferma che si tratta di polynomials cubica. È facile verificare che quando `t` è uguale a 0, il punto è (x ₀, y ₀), che è il punto iniziale e quando `t` è uguale a 1, il punto è (x ₃, y ₃), ovvero il punto finale. In prossimità del punto iniziale (per i valori bassi di `t`), il primo punto di controllo (x ₁, y ₁) ha un effetto forte e, in prossimità del punto finale (valori elevati di ' t'), il secondo punto di controllo (x ₂, y ₂) ha un effetto forte.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Curva di Bézier approssimazione per gli archi circolari

A volte è utile usare una curva di Bézier per eseguire il rendering di un arco circolare. Una curva di Bézier cubica può approssimare un arco circolare molto bene fino a un cerchio di quarto, quindi quattro curve di Bézier connesse possono definire un cerchio intero. Viene illustrata questa approssimazione nei due articoli pubblicati più di 25 anni fa:

> Tor Dokken, et al, "una corretta approssimazione dei cerchi per curvare-curve di Bézier continue", " *Computer Aided Geometric Design 7* (1990), 33-41.

> Michael Goldapp, "approssimazione di archi circolari in base ai polinomi cubici", " *Computer Aided Geometric Design 8* (1991), 227-238.

Il diagramma seguente mostra quattro punti con etichetta `pto`, `pt1`, `pt2`e `pt3` la definizione di una curva di Bézier (mostrata in rosso) che si avvicina a un arco circolare:

![Approssimazione di un arco circolare con una curva di Bézier](beziers-images/bezierarc45.png)

Le linee dall'inizio e dalla fine puntano ai punti di controllo sono tangenti al cerchio e alla curva di Bézier e hanno una lunghezza pari a *L*. Il primo articolo citato sopra indica che la curva di Bézier è più simile a un arco circolare quando tale lunghezza *L* viene calcolata nel modo seguente:

L = 4 × tan(α / 4) / 3

La figura mostra un angolo di 45 gradi, in modo 0.265 è uguale a L. Nel codice, tale valore viene moltiplicato il raggio del cerchio desiderato.

La pagina di **arco circolare di Bezier** consente di provare a definire una curva di Bézier per approssimare un arco circolare per gli angoli compresi tra un massimo di 180 gradi. Il file [**BezierCircularArcPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) crea un'istanza del `SKCanvasView` e di un `Slider` per la selezione dell'angolo. Il gestore dell'evento `PaintSurface` nel file code-behind [**BezierCircularArgPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) usa una trasformazione per impostare il punto (0,0) sul centro dell'area di disegno. Consente di disegnare un cerchio centrato su quel punto per il confronto e quindi calcola i due punti di controllo della curva di Bézier:

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

I punti iniziale e finale (`point0` e `point3`) vengono calcolati in base alle equazioni parametriche normali per il cerchio. Poiché il cerchio è centrato al (0, 0), questi aspetti possono anche essere considerati come vettori radiali dal centro del cerchio per la circonferenza. I punti di controllo sono nelle righe che vengono tangente al controllo circle, pertanto sono ad angolo retto per questi vettori radiali. Un vettore ad angolo retto a un altro è semplicemente il vettore originale con le coordinate X e Y scambiate e uno di essi effettuate negativo.

Ecco il programma in esecuzione con diverse angolazioni:

[![schermata tripla della pagina di arco circolare di Bezier](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

Esaminare attentamente la terza schermata e si noterà che la curva di Bézier in particolare si differenzia da un semicerchio quando l'angolo è 180 gradi, mentre la schermata di iOS che indica che sembra contenere un cerchio di quarto correttamente durante l'angolo è 90 gradi.

Calcolare le coordinate di due punti di controllo è piuttosto semplice, quando il quarto di cerchio è orientato simile al seguente:

![Approssimazione di un cerchio del trimestre con una curva di Bézier](beziers-images/bezierarc90.png)

Se il raggio del cerchio è 100, *L* è 55 e questo è un numero semplice da ricordare.

La **quadratura della pagina del cerchio** anima una figura tra un cerchio e un quadrato. Il cerchio è approssimato da quattro curve di Bézier le cui coordinate sono mostrate nella prima colonna di questa definizione di matrice nella classe [`SquaringTheCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) :

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

La seconda colonna contiene le coordinate dei quattro curve di Bézier che definiscono un quadrato con area equivale a circa l'area del cerchio. (Il disegno di un quadrato con l'area *esatta* come un determinato cerchio è il problema geometrico solubile classico per [la quadratura del cerchio](https://en.wikipedia.org/wiki/Squaring_the_circle)). Per il rendering di un quadrato con curve di Bézier, i due punti di controllo per ogni curva sono gli stessi e sono colineari con i punti iniziale e finale, quindi la curva di Bézier viene sottoposta a rendering come linea retta.

La terza colonna della matrice è per i valori interpolati per un'animazione. La pagina imposta un timer per 16 millisecondi e il gestore `PaintSurface` viene chiamato alla velocità seguente:

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

I punti vengono interpolati in base a un valore con oscillazione sinusoidale del `t`. I punti interpolati vengono quindi utilizzati per costruire una serie di quattro curve di Bézier collegate. Di seguito è riportata l'animazione in esecuzione:

[![triplice screenshot del quadrato della pagina del cerchio](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

Un'animazione di questo tipo sarebbe impossibile senza le curve che sono modo algoritmico sufficientemente flessibile da sottoporre a rendering come gli archi circolari e linee rette.

La pagina **Infinity di Bezier** sfrutta anche la capacità di una curva di Bézier di approssimare un arco circolare. Ecco il gestore `PaintSurface` dalla classe [`BezierInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) :

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

Potrebbe essere un esercizio ottimo per tracciare queste coordinate su carta grafico per visualizzare il tipo di correlazione. Il simbolo di infinito è centrato intorno al punto (0, 0) e dispone di due cicli centri di (–150, 0) e (150, 0) e raggio pari a 100. Nella serie di comandi di `CubicTo`, è possibile visualizzare le coordinate X dei punti di controllo che assumono valori di-95 e-205 (tali valori sono – 150 Plus e meno 55), 205 e 95 (150 Plus e meno 55), oltre a 250 e-250 per il lato destro e sinistro. L'unica eccezione è quando il simbolo di infinito incrocia al centro. In tal caso, i punti di controllo hanno coordinate con una combinazione di 50 e da – 50 per appiattire la curva vicino al centro.

Ecco il simbolo di infinito:

[![schermata tripla della pagina dell'infinito di Bézier](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

È leggermente più uniforme verso il centro rispetto al segno di infinito sottoposto a rendering dalla pagina **Infinity Arc** dei [**tre modi per creare un articolo di arco**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) .

## <a name="the-quadratic-bzier-curve"></a>La curva di Bézier quadratica

La curva di Bézier quadratica ha solo un punto di controllo e la curva è definita dai tre semplici punti: il punto iniziale, il punto di controllo e il punto finale. Le equazioni parametriche sono molto simili alla curva di Bézier cubica continua, ad eccezione del fatto che l'esponente massima è 2, in modo che la curva è una quadratica polinomiale:

x(t) = (1-t) ²x₀ 2t + (1-t) x₁ t²x₂ +

y(t) = (1-t) ²y₀ 2t + (1-t) y₁ t²y₂ +

Per aggiungere una curva di Bézier quadratica a un tracciato, usare il metodo [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) o l'overload [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) con coordinate `x` e `y` separate:

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

I metodi aggiungono una curva dalla posizione corrente a `point2` con `point1` come punto di controllo.

È possibile sperimentare curve di Bézier quadratiche con la pagina della **curva quadratica** , che è molto simile alla pagina della **curva di Bezier** , ad eccezione del fatto che contiene solo tre punti di tocco. Ecco il gestore `PaintSurface` nel file code-behind [**QuadraticCurve.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) :

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

[![schermata tripla della pagina della curva quadratica](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

Le linee punteggiate sono tangente della curva nel punto di inizio e fine e soddisfare in corrispondenza del punto di controllo.

Se necessaria una curva di una forma generale, ma si preferisce la praticità di un solo punto di controllo anziché due, è consigliabile di Bézier quadratica. Esegue il rendering di Bézier quadratica in modo più efficiente rispetto a qualsiasi altra curva, motivo per cui viene usato internamente nel Skia per eseguire il rendering ellittici.

Tuttavia, la forma di una curva di Bézier quadratica non è ellittica, motivo per cui sono necessari più Bézier quadratici per approssimare un arco ellittico. La Bézier quadratica è invece un segmento di una parabola.

## <a name="the-conic-bzier-curve"></a>La curva di Bézier conica

La curva di Bézier conica &mdash; nota anche come curva di Bézier quadratica razionale &mdash; è un'aggiunta relativamente recente alla famiglia di curve di Bézier. Ad esempio la curva di Bézier quadratica, la curva di Bézier quadratica razionale implica un punto di partenza, un punto finale e un punto di controllo. Tuttavia, la curva di Bézier quadratica razionale richiede anche un valore di *peso* . Viene chiamato un quadratino *razionale* perché le formule parametriche coinvolgono i rapporti.

Le equazioni parametriche per X e Y sono percentuali che condividono il denominatore stesso. Di seguito è illustrata l'equazione per il denominatore per *t* compreso tra 0 e 1 e il valore di peso *w*:

Ät = (1-t) ² + 2wt(1 – t) + t²

In teoria, un quadratica razionale possono includere tre valori di peso separati, uno per ciascuna delle tre condizioni, ma queste possono essere semplificate per un solo valore di peso il termine intermedio.

Le equazioni parametriche per le coordinate X e Y sono simili per le equazioni parametriche per di Bézier quadratica ad eccezione del fatto che il termine intermedio include anche il valore di peso e l'espressione viene diviso per il denominatore:

x(t) = ((1 – t)²x₀ + 2wt(1 – t)x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ 2wt + (1-t) y₁ t²y₂ +)) Ät ÷

Anche le curve di Bézier quadratiche razionali sono denominate *coniche* , in quanto possono rappresentare esattamente segmenti di qualsiasi sezione conica &mdash; iperboli, parabole, ellissi e cerchi.

Per aggiungere una curva di Bézier quadratica razionale a un tracciato, usare il metodo [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) o l'overload [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) con coordinate `x` e `y` separate:

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Si noti il parametro finale `weight`.

La pagina della **curva conica** consente di sperimentare le curve. La classe `ConicCurvePage` deriva da `InteractivePage`. Il file [**ConicCurvePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) crea un'istanza di un `Slider` per selezionare un valore di peso compreso tra-2 e 2. Il file code-behind [**ConicCurvePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) crea tre oggetti `TouchPoint` e il gestore `PaintSurface` esegue semplicemente il rendering della curva risultante con le linee tangente nei punti di controllo:

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

Qui è in esecuzione:

[![schermata tripla della pagina a curva conica](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

Come può notare, il punto di controllo sembra pull la curva per lo più quando il peso è elevato. Quando il peso è uguale a zero, la curva diventa una linea retta tra il punto iniziale per il punto finale.

In teoria, i pesi negativi sono consentiti e determinano la curvatura della *curva dal punto di controllo* . Tuttavia, i pesi di-1 o di seguito fanno sì che il denominatore nelle equazioni parametriche diventi negativo per determinati valori di *t*. Per questo motivo, è probabile che i pesi negativi vengano ignorati nei metodi `ConicTo`. Il programma di **curva conica** consente di impostare pesi negativi, ma come è possibile vedere sperimentando, i pesi negativi hanno lo stesso effetto di un peso di zero e determinano il rendering di una linea retta.

È molto facile derivare il punto di controllo e il peso per usare il metodo `ConicTo` per creare un arco circolare fino a un semicerchio, ma non includerlo. Nel diagramma seguente, le tangenti dai punti iniziale e finale soddisfano in corrispondenza del punto di controllo.

![Rendering di Arco conico di un arco circolare](beziers-images/conicarc.png)

È possibile usare trigonometria per determinare la distanza del punto di controllo dal centro del cerchio: è il raggio del cerchio diviso per metà dell'angolo α il coseno. Per disegnare un arco circolare tra i punti iniziale e finale, impostare il peso su tale stesso coseno metà dell'angolo. Si noti che se l'angolo è 180 gradi, quindi le righe tangente mai conformi e il peso è uguale a zero. Ma per gli angoli minore di 180 gradi, i calcoli matematici funzionano correttamente.

Questa operazione viene illustrata nella pagina di **arco circolare conica** . Il file [**ConicCircularArc. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) crea un'istanza di un `Slider` per la selezione dell'angolo. Il gestore `PaintSurface` nel file code-behind [**ConicCircularArc.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) calcola il punto di controllo e il peso:

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

Come si può notare, non esiste alcuna differenza visiva tra il percorso di `ConicTo` mostrato in rosso e il cerchio sottostante visualizzato come riferimento:

[![schermata tripla della pagina di arco circolare conica](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

Ma impostare l'angolo di 180 gradi e il matematica hanno esito negativo.

In questo caso, in questo caso, `ConicTo` non supporta i pesi negativi, perché in teoria, in base alle equazioni parametriche, il cerchio può essere completato con un'altra chiamata a `ConicTo` con gli stessi punti ma con un valore negativo del peso. Ciò consentirebbe la creazione di un cerchio intero con solo due curve `ConicTo` basate su qualsiasi angolo compreso tra (ma senza includere) zero gradi e 180 gradi.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
