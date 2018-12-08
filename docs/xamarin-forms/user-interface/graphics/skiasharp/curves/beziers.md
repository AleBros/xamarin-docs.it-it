---
title: Tre tipi di curve di Bézier
description: Questo articolo illustra come usare SkiaSharp per eseguire il rendering le curve di Bézier cubica continua, quadratiche e conica nelle applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
ms.openlocfilehash: 58cf11b2a88e0c399ee197e9c8365d7deafd0f39
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055477"
---
# <a name="three-types-of-bzier-curves"></a>Tre tipi di curve di Bézier

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Scopri come usare SkiaSharp per eseguire il rendering di curve di Bézier cubica continua, quadratiche e conica_

La curva di Bézier denominata dopo Pierre Bézier (1910: 1999), un francese engineer presso l'azienda automobilistica europea alleanza Renault, che hanno usato la curva per la progettazione assistita da computer di corpi di automobili.

Curve di Bézier sono noti per essere particolarmente adatta alla finestra di progettazione interattiva: funzionano bene &mdash; in altre parole, non ci siano singolarità che causano la curva diventare difficile da gestire o infinito &mdash; e sono in genere esteticamente :

![](beziers-images/beziersample.png "Una curva di Bézier di esempio")

Contorni dei caratteri dei tipi di carattere basati su computer sono in genere definite con curve di Bézier.

L'articolo di Wikipedia relativo [ **curva di Bézier** ](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contiene alcune informazioni utili in background. Il termine *curva di Bézier* fa effettivamente riferimento è una famiglia di curve simile. SkiaSharp supporta tre tipi di curve di Bézier, denominate il *cubica*, il *quadratica*e il *conica*. Conica è noto anche come il *quadratica razionale*.

## <a name="the-cubic-bzier-curve"></a>Curva di Bézier cubica

La radice cubica è il tipo di curva di Bézier che molti sviluppatori ritengono di quando l'oggetto di curve di Bézier viene visualizzata.

È possibile aggiungere una curva di Bézier cubica continua a un `SKPath` utilizzando il [ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) metodo con tre `SKPoint` parametri, o il [ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) overload con separato `x` e `y` parametri:

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

La curva inizia nel punto corrente per il contorno. La curva di Bézier cubica completata è definita da quattro punti:

- punto di inizio: punto corrente nel contorno, o (0, 0) se `MoveTo` non è stato chiamato
- punto di controllo prima di tutto: `point1` nella `CubicTo` chiamare
- secondo punto di controllo: `point2` nella `CubicTo` chiamare
- punto di fine: `point3` nella `CubicTo` chiamare

La curva risultante inizia in corrispondenza del punto di inizio e termina in corrispondenza del punto di fine. La curva in genere non passa attraverso due punti di controllo; il controllo punta invece molto simile a magneti per eseguire il pull della curva conseguirli (funzione).

Il modo migliore per farsi un'idea della curva di Bézier cubica è dalle sperimentazioni. Questo è lo scopo del **curva di Bézier** pagina, che deriva da `InteractivePage`. Il [ **BezierCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) file creare un'istanza di `SKCanvasView` e un `TouchEffect`. Il [ **BezierCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) consente di creare quattro file code-behind `TouchPoint` gli oggetti nel relativo costruttore. Il `PaintSurface` gestore eventi crea un' `SKPath` per eseguire il rendering di una curva di Bézier basata su quattro `TouchPoint` gli oggetti e disegna inoltre linee punteggiate tangente dai punti di controllo per i punti finali:

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

[![](beziers-images/beziercurve-small.png "Tripla screenshot della pagina della curva di Bézier")](beziers-images/beziercurve-large.png#lightbox "tripla screenshot della pagina della curva di Bezier")

Matematica, la curva è una polinomiale cubica. La curva interseca al massimo una linea retta in tre punti. In corrispondenza del punto di inizio, la curva è sempre una linea retta dall'inizio tangente e nella stessa direzione, puntare al primo punto di controllo. In corrispondenza del punto di fine, la curva è sempre una linea retta nel secondo controllo tangente e nella stessa direzione, puntare al punto finale.

Curva di Bézier cubica è sempre limitata da un quadrilatero convesso che collega i quattro punti. Questa operazione viene definita un' *struttura convessa*. Se si trovano i punti di controllo sulla linea retta tra i punti iniziale e finale, quindi la curva di Bézier esegue il rendering come una linea retta. Ma la curva può inoltre superare stesso, come illustrato di seguito nella terza schermata.

Una distribuzione del percorso può contenere più curve di Bézier cubiche collegate, ma la connessione tra due curve di Bézier cubiche verrà smooth solo se i seguenti tre punti sono collineari (vale a dire si trovano su una linea retta):

- il secondo punto di controllo della curva prima
- il punto finale della curva prima, che è anche il punto iniziale della curva secondo
- il primo punto di controllo della curva secondo

Nel prossimo articolo sul [ **dati del tracciato SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md), sarà possibile osservare che una funzionalità che semplificano la definizione di curve di Bézier collegate smooth.

In alcuni casi è utile conoscere le equazioni parametriche sottostanti che eseguono il rendering di una curva di Bézier cubica. Per la *t* compreso tra 0 e 1, equazioni parametriche sono i seguenti:

x(t) = (1-t) ³x₀ 3t + (1-t) ²x₁ 3t² + (1-t) x₂ t³x₃ +

y(t) = (1-t) ³y₀ 3t + (1-t) ²y₁ 3t² + (1-t) y₂ t³y₃ +

L'esponente massima pari a 3 conferma che si tratta di polynomials cubica. È facile verificare che, quando `t` è uguale a 0, il punto (x₀, y₀), ovvero il punto di inizio e quando `t` è uguale a 1, il punto (x₃, y₃), ovvero il punto finale. In prossimità del punto iniziale (per valori bassi di `t`), il primo punto di controllo (x₁, y₁) ha un nome sicuro effetto e quasi il punto finale (valori elevati di l ' ') secondo punto di controllo (x₂, y₂) ha un effetto sicuro.

## <a name="bezier-curve-approximation-to-circular-arcs"></a>Curva di Bézier approssimazione per gli archi circolari

In alcuni casi è consigliabile usare una curva di Bezier per il rendering di un arco circolare. Una curva di Bézier cubica approssimare un arco circolare molto bene fino a un quarto di cerchio, in modo da quattro curve di Bézier collegate possono definire un cerchio intero. Viene illustrata questa approssimazione nei due articoli pubblicati più di 25 anni fa:

> Tor Dokken, et al., "Buona approssimazione dei cerchi da curve di Bézier curvatura continua," *Computer Aided geometrica progettazione 7* (1990), 33: 41.

> "Approssimazione di archi circolari da cubica Polynomials,", Michael Goldapp *geometrica progettazione 8 assistita* (1991), 227 238.

Il diagramma seguente mostra quattro punti etichettati `pto`, `pt1`, `pt2`, e `pt3` la definizione di una curva di Bézier (mostrata in rosso) che simula un arco circolare:

![](beziers-images/bezierarc45.png "Approssimazione di un arco circolare con una curva di Bézier")

Le righe all'inizio e fine per i punti di controllo sono tangente al controllo circle e la curva di Bézier e hanno una lunghezza pari *L*. Il primo articolo citato in precedenza indica che la curva di Bézier migliore offre un'approssimazione di un arco circolare quando tale lunghezza *L* viene calcolato come segue:

L = 4 × tan(α / 4) / 3

La figura mostra un angolo di 45 gradi, in modo 0.265 è uguale a L. Nel codice, tale valore viene moltiplicato il raggio del cerchio desiderato.

Il **arco circolare Bezier** pagina consente di sperimentare la definizione di una curva di Bezier per simulare un arco circolare per gli angoli che vanno fino a 180 gradi. Il [ **BezierCircularArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) file creare un'istanza il `SKCanvasView` e un `Slider` per la selezione angolo. Il `PaintSurface` gestore dell'evento nel [ **BezierCircularArgPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) file code-behind Usa una trasformazione per impostare il punto (0, 0) al centro dell'area di disegno. Consente di disegnare un cerchio centrato su quel punto per il confronto e quindi calcola i due punti di controllo della curva di Bézier:

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

I punti iniziale e finale (`point0` e `point3`) vengono calcolati in base a equazioni parametriche normale per il controllo circle. Poiché il cerchio è centrato al (0, 0), questi aspetti possono anche essere considerati come vettori radiali dal centro del cerchio per la circonferenza. I punti di controllo sono nelle righe che vengono tangente al controllo circle, pertanto sono ad angolo retto per questi vettori radiali. Un vettore ad angolo retto a un altro è semplicemente il vettore originale con le coordinate X e Y scambiate e uno di essi effettuate negativo.

Ecco il programma in esecuzione con diverse angolazioni:

[![](beziers-images/beziercirculararc-small.png "Tripla screenshot della pagina di arco circolare Bezier")](beziers-images/beziercirculararc-large.png#lightbox "tripla screenshot della pagina di arco circolare curva di Bezier")

Esaminare attentamente la terza schermata e si noterà che la curva di Bézier in particolare si differenzia da un semicerchio quando l'angolo è 180 gradi, mentre la schermata di iOS che indica che sembra contenere un cerchio di quarto correttamente durante l'angolo è 90 gradi.

Calcolare le coordinate di due punti di controllo è piuttosto semplice, quando il quarto di cerchio è orientato simile al seguente:

![](beziers-images/bezierarc90.png "Approssimazione di un quarto di cerchio con una curva di Bézier")

Se il raggio del cerchio è 100, allora *L* è 55 e che è un numero facile da ricordare.

Il **elevazione al quadrato del cerchio** pagina consente di animare una figura tra un cerchio e un quadrato. Il cerchio viene approssimato da cui coordinate vengono visualizzati nella prima colonna di questa definizione di matrice in quattro curve di Bézier il [ `SquaringTheCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) classe:

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

La seconda colonna contiene le coordinate dei quattro curve di Bézier che definiscono un quadrato con area equivale a circa l'area del cerchio. (Disegno un quadrato con il *esatta* area sotto forma di cerchio specificato consiste nello classico irrisolvibile geometrica [elevazione al quadrato del cerchio](https://en.wikipedia.org/wiki/Squaring_the_circle).) Per il rendering di un quadrato con curve di Bézier, due punti di controllo per ogni curva sono uguali e sono collineari con i punti iniziale e finale, in modo che la curva di Bézier viene eseguito il rendering come una linea retta.

La terza colonna della matrice è per i valori interpolati per un'animazione. La pagina consente di impostare un timer per 16 millisecondi e il `PaintSurface` gestore viene chiamato con che frequenza:

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

I punti sono interpolati basata su un valore sinusoidally intermittenti di `t`. I punti interpolati vengono quindi utilizzati per costruire una serie di quattro curve di Bézier collegate. Di seguito è riportata l'animazione in esecuzione:

[![](beziers-images/squaringthecircle-small.png "Schermata triplo del Squaring pagina cerchio")](beziers-images/squaringthecircle-large.png#lightbox "tripla screenshot del Squaring pagina cerchio")

Un'animazione di questo tipo sarebbe impossibile senza le curve che sono modo algoritmico sufficientemente flessibile da sottoporre a rendering come gli archi circolari e linee rette.

Il **Bezier infinito** pagina consente anche di sfruttare la capacità di una curva di Bézier per simulare un arco circolare. Di seguito è riportato il `PaintSurface` gestore il [ `BezierInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) classe:

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

Potrebbe essere un esercizio ottimo per tracciare queste coordinate su carta grafico per visualizzare il tipo di correlazione. Il simbolo di infinito è centrato intorno al punto (0, 0) e dispone di due cicli centri di (–150, 0) e (150, 0) e raggio pari a 100. Nella serie di `CubicTo` comandi, è possibile visualizzare coordinate dei punti di controllo in modo semplice i valori –95 e –205 X, tali valori sono –150 + /-55, 205 e 95 (150 + /-55), oltre 250 e –250 per i lati destro e sinistro. L'unica eccezione è quando il simbolo di infinito incrocia al centro. In tal caso, i punti di controllo hanno coordinate con una combinazione di 50 e da – 50 per appiattire la curva vicino al centro.

Ecco il simbolo di infinito:

[![](beziers-images/bezierinfinity-small.png "Tripla screenshot della pagina di Bézier Infinity")](beziers-images/bezierinfinity-large.png#lightbox "tripla screenshot della pagina del numero infinito di Bézier")

È un po' più efficiente verso il centro di accesso di un numero infinito sottoposto a rendering dal **arco infinito** pagina dalle [ **tre modi per disegnare un arco** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) articolo.

## <a name="the-quadratic-bzier-curve"></a>La curva di Bézier quadratica

La curva di Bézier quadratica ha solo un punto di controllo e la curva è definita dai tre semplici punti: il punto iniziale, il punto di controllo e il punto finale. Le equazioni parametriche sono molto simili alla curva di Bézier cubica continua, ad eccezione del fatto che l'esponente massima è 2, in modo che la curva è una quadratica polinomiale:

x(t) = (1-t) ²x₀ 2t + (1-t) x₁ t²x₂ +

y(t) = (1-t) ²y₀ 2t + (1-t) y₁ t²y₂ +

Per aggiungere una curva di Bézier quadratica in un percorso, usare il [ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) metodo o il [ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) overload con separato `x` e `y` coordinate:

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

I metodi di aggiungono una curva dalla posizione corrente per `point2` con `point1` come punto di controllo.

È possibile sperimentare curve di Bézier quadratiche con il **quadratica** pagina, che è molto simile al **curva di Bézier** pagina ma contiene solo tre punti di tocco. Di seguito è riportato il `PaintSurface` gestore nel [ **QuadraticCurve.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) file code-behind:

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

[![](beziers-images/quadraticcurve-small.png "Tripla screenshot della pagina della curva quadratica")](beziers-images/quadraticcurve-large.png#lightbox "tripla screenshot della pagina della curva quadratica")

Le linee punteggiate sono tangente della curva nel punto di inizio e fine e soddisfare in corrispondenza del punto di controllo.

Se necessaria una curva di una forma generale, ma si preferisce la praticità di un solo punto di controllo anziché due, è consigliabile di Bézier quadratica. Esegue il rendering di Bézier quadratica in modo più efficiente rispetto a qualsiasi altra curva, motivo per cui viene usato internamente nel Skia per eseguire il rendering ellittici.

Tuttavia, la forma di una curva di Bézier quadratica non ellittica, motivo per cui sono necessari più Béziers quadratica per simulare un arco ellittico. Di Bézier quadratica è invece un segmento di un parabolica.

## <a name="the-conic-bzier-curve"></a>La curva di Bézier conica

La curva di Bézier conica &mdash; noto anche come il razionale curva di Bézier quadratica &mdash; è un'aggiunta relativamente recente della famiglia di curve di Bézier. Ad esempio la curva di Bézier quadratica, la curva di Bézier quadratica razionale implica un punto di partenza, un punto finale e un punto di controllo. Ma la curva di Bézier quadratica razionale richiede anche un *peso* valore. Viene chiamato un *razionali* quadratica poiché le formule parametriche coinvolgono rapporti.

Le equazioni parametriche per X e Y sono percentuali che condividono il denominatore stesso. Di seguito è riportato l'equazione per il denominatore *t* compreso tra 0 a 1 e il valore di peso *w*:

Ät = (1-t) ² + 2wt(1 – t) + t²

In teoria, un quadratica razionale possono includere tre valori di peso separati, uno per ciascuna delle tre condizioni, ma queste possono essere semplificate per un solo valore di peso il termine intermedio.

Le equazioni parametriche per le coordinate X e Y sono simili per le equazioni parametriche per di Bézier quadratica ad eccezione del fatto che il termine intermedio include anche il valore di peso e l'espressione viene diviso per il denominatore:

x(t) = ((1 – t)²x₀ + 2wt(1 – t)x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ 2wt + (1-t) y₁ t²y₂ +)) Ät ÷

Vengono inoltre chiamate razionali curve di Bézier quadratiche *coniche* perché possono rappresentare esattamente i segmenti di una sezione conica &mdash; hyperbolas, parabolas, sui puntini di sospensione e cerchi.

Per aggiungere una curva di Bézier quadratica rational a un percorso, usare il [ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) metodo o il [ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) overload con separato `x` e `y` coordinate:

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Si noti che l'elemento finale `weight` parametro.

Il **curva conica** pagina consente di sperimentare queste curve. La classe `ConicCurvePage` deriva da `InteractivePage`. Il [ **ConicCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) crea un'istanza di file un `Slider` per selezionare un valore di ponderazione compreso tra -2 e 2. Il [ **ConicCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) vengono creati tre file code-behind `TouchPoint` oggetti e il `PaintSurface` gestore esegue semplicemente il rendering la curva risultante con le righe al controllo della tangente punti:

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

[![](beziers-images/coniccurve-small.png "Tripla screenshot della pagina della curva conica")](beziers-images/coniccurve-large.png#lightbox "tripla screenshot della pagina della curva conica")

Come può notare, il punto di controllo sembra pull la curva per lo più quando il peso è elevato. Quando il peso è uguale a zero, la curva diventa una linea retta tra il punto iniziale per il punto finale.

In teoria, i pesi negativi sono consentiti e causano la curva a piegare *assente* dal punto di controllo. Tuttavia, ne vengono ponderati-1 o di sotto causa il denominatore in equazioni parametriche diventare negativo per particolari valori di *t*. Probabilmente per questo motivo, i pesi negativi vengono ignorati nel `ConicTo` metodi. Il **curva conica** programma consente di impostare pesi negativi, ma come si può vedere eseguendo più esperimenti, pesi negativi hanno lo stesso effetto un peso pari a zero e causano una linea retta da sottoporre a rendering.

È molto semplice derivare il punto di controllo e il peso da usare il `ConicTo` metodo consente di disegnare un arco circolare fino a (ma non con) un semicerchio. Nel diagramma seguente, le tangenti dai punti iniziale e finale soddisfano in corrispondenza del punto di controllo.

![](beziers-images/conicarc.png "A seguito del rendering arco conica di un arco circolare")

È possibile usare trigonometria per determinare la distanza del punto di controllo dal centro del cerchio: è il raggio del cerchio diviso per metà dell'angolo α il coseno. Per disegnare un arco circolare tra i punti iniziale e finale, impostare il peso su tale stesso coseno metà dell'angolo. Si noti che se l'angolo è 180 gradi, quindi le righe tangente mai conformi e il peso è uguale a zero. Ma per gli angoli minore di 180 gradi, i calcoli matematici funzionano correttamente.

Il **arco circolare conica** pagina viene illustrata questa. Il [ **ConicCircularArc.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) crea un'istanza di file un `Slider` per la selezione angolo. Il `PaintSurface` gestore nel [ **ConicCircularArc.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) file code-behind viene calcolato il punto di controllo e il peso:

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

Come si può notare, non vi è alcuna differenza tra visual il `ConicTo` percorso mostrati in rosso e il cerchio sottostante visualizzati per riferimento:

[![](beziers-images/coniccirculararc-small.png "Tripla screenshot della pagina di arco circolare conica")](beziers-images/coniccirculararc-large.png#lightbox "tripla screenshot della pagina conica arco circolare")

Ma impostare l'angolo di 180 gradi e il matematica hanno esito negativo.

Rappresenta un problema in questo caso che `ConicTo` nepodporuje pesi negativi, poiché in teoria (basato su equazioni parametriche), è possibile eseguire il cerchio con un'altra chiamata a `ConicTo` con gli stessi punti, ma un valore negativo del peso. In tal modo la creazione di un cerchio intero con appena due `ConicTo` curve basato su qualsiasi angolatura tra (ma non inclusa) zero gradi e 180 gradi.


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
