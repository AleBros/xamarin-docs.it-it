---
title: Tre tipi di curve di Bézier
description: Imparare a utilizzare SkiaSharp per eseguire il rendering di Bézier cubica, quadratiche e conica
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: charlespetzold
ms.author: chape
ms.date: 05/25/2017
ms.openlocfilehash: c5142a3abcc6d461bc277faeb02e3aacd9727bca
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
# <a name="three-types-of-bzier-curves"></a>Tre tipi di curve di Bézier

_Imparare a utilizzare SkiaSharp per eseguire il rendering di Bézier cubica, quadratiche e conica_

Pierre Bézier (1910: 1999), un tecnico presso il settore automobilistico Renault, che utilizzano la curva per la progettazione assistita da computer dei corpi dei car francese denominata la curva di Bézier.

Curve di Bézier sono noti per essere particolarmente adatta alla finestra di progettazione interattiva: sono bene &mdash; in altre parole, non ci siano singolarità che causano la curva diventare difficile da gestire o infinito &mdash; e sono in genere esteticamente . Strutture di un carattere di tipi di carattere basata sul computer sono in genere definite con curve di Bézier:

![](beziers-images/beziersample.png "Una curva di Bezier di esempio")

L'articolo di Wikipedia relativo [curva di Bézier](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) contiene alcune informazioni utili. Il termine *curva di Bézier* effettivamente fa riferimento a una famiglia di curve simili. SkiaSharp supporta tre tipi di curve di Bézier, chiamate il *cubica*, *quadratica*e *conica*. Conica è noto anche come il *quadratica razionale*.

## <a name="the-cubic-bzier-curve"></a>Curva di Bézier cubica

La radice cubica è il tipo di curva di Bézier che molti sviluppatori ritengono di quando l'oggetto delle curve di Bézier viene visualizzata.

È possibile aggiungere una curva di Bézier cubica un `SKPath` utilizzando il [ `CubicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CubicTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/SkiaSharp.SKPoint/) metodo con tre `SKPoint` parametri, o [ `CubicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CubicTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/System.Single/) overload con separato `x` e `y` parametri:

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

La curva inizia nel punto corrente del contorno. Curva di Bézier cubica completezza è definita da quattro punti:

- punto di inizio: corrente punto di distribuzione, o (0, 0) se `MoveTo` non è stato chiamato
- primo punto di controllo: `point1` nel `CubicTo` chiamare
- secondo punto di controllo: `point2` nel `CubicTo` chiamare
- punto di fine: `point3` nel `CubicTo` chiamare

La curva risulta inizia nel punto di inizio e termina in corrispondenza del punto finale. La curva in genere non attraversa i due punti di controllo; invece, questi funzionano quantità grado attirare l'attenzione like per eseguire il pull della curva verso li.

Il modo migliore per acquisire familiarità con la curva di Bézier cubica è dalle sperimentazioni. È questo lo scopo del **curva di Bezier** pagina, che deriva da `InteractivePage`. Il [ **BezierCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml) file crea un'istanza di `SKCanvasView` e un `TouchEffect`. Il [ **BezierCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs) file code-behind crea quattro `TouchPoint` oggetti nel relativo costruttore. Il `PaintSurface` gestore eventi crea un `SKPath` per eseguire il rendering di una curva di Bézier in base a quattro `TouchPoint` oggetti e disegna inoltre punti tangenti dai punti di controllo per i punti finali:

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

Di seguito viene eseguita su tutti e tre le piattaforme:

[![](beziers-images/beziercurve-small.png "Schermata di triplo della pagina curva di Bezier")](beziers-images/beziercurve-large.png#lightbox "tripla schermata della pagina curva di Bezier")

Matematicamente, la curva è un polinomiale cubica. La curva interseca al massimo una linea retta tre punti. Corrispondenza del punto di inizio, la curva è sempre tangente, quindi nella stessa direzione, scegliere il primo punto di controllo di una linea retta dall'inizio. Corrispondenza del punto di fine, la curva è sempre tangente, quindi nella stessa direzione, punto e il punto finale di una linea retta dal secondo controllo.

Curva di Bézier cubica sempre è limitata da un quadrilatero convesso che collega i quattro punti. Si tratta di un *convessa*. Se i punti di controllo si trovano sulla linea retta tra i punti iniziale e finale, quindi la curva di Bézier esegue il rendering come una linea retta. Ma la curva può anche incrociare se stesso, come illustrato nella schermata dal dispositivo Windows Mobile.

Una distribuzione del percorso può contenere più curve di Bézier cubiche collegate, ma la connessione tra due curve di Bézier cubiche verrà smooth solo se i seguenti tre punti sono collineari (vale a dire si trovano su una linea retta):

- secondo punto di controllo della curva prima
- il punto finale della curva prima, che è anche il punto di inizio della seconda curva
- il primo punto di controllo della curva secondo

Nell'articolo successivo in [ **SVG percorso dati** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) sarà possibile osservare un ufficio per semplificare la definizione di Bézier connessi uniformi.

È talvolta utile conoscere le equazioni parametriche sottostante che eseguono il rendering di una curva di Bézier cubica. Per *t* compreso tra 0 e 1, le equazioni parametriche sono i seguenti:

x(t) = (1 – t) ³x₀ 3t + (1 – t) ²x₁ 3t² + (1 – t) x₂ + t³x₃

y(t) = (1 – t) ³y₀ 3t + (1 – t) ²y₁ 3t² + (1 – t) y₂ + t³y₃

L'esponente massimo di 3 conferma sono polynomials cubica. È facile verificare che quando `t` è uguale a 0, il punto è (x₀, y₀), che è il punto di inizio e quando `t` è uguale a 1, il punto è (x₃, y₃), che è il punto finale. In prossimità del punto iniziale (per valori bassi di `t`), il primo punto di controllo (x₁, y₁) è un nome sicuro avrà effetto e il punto finale di quasi (valori elevati di ' T' ') secondo punto di controllo (x₂, y₂) ha un effetto sicuro.

## <a name="bzier-curve-approximation-to-circular-arcs"></a>Curva di Bézier approssimato archi circolari

In alcuni casi è consigliabile usare una curva di Bézier per eseguire il rendering di un arco circolare. Una curva di Bézier cubica approssimare un arco circolare molto bene fino a un quarto di cerchio, in modo quattro curve di Bézier collegate è possono definire un cerchio intero. Questo tipo di approssimazione viene discussa nei due articoli pubblicati oltre 25 anni:

> Tor Dokken, et al., "Buona approssimazione dei cerchi dalla curvatura continua di Bézier," *Computer assistita geometrica progettazione 7* (1990), 41-33.

> Michael Goldapp, "Approssimazione di archi circolari da cubica Polynomials," *geometrica progettazione 8 assistita* (1991), 227 238.

Il diagramma seguente mostra quattro punti `pto`, `pt1`, `pt2`, e `pt3` la definizione di una curva di Bézier (mostrata in rosso) che offre un'approssimazione un arco circolare:

![](beziers-images/bezierarc45.png "Approssimazione di un arco circolare con una curva di Bézier")

Le righe i punti iniziale e finale per i punti di controllo sono tangente al cerchio e per la curva di Bézier, e hanno una lunghezza di *L*. Il primo articolo citato in precedenza indica che la curva di Bézier migliore è all'incirca un arco circolare quando tale lunghezza *L* viene calcolato come segue:

L = 4 × tan(α / 4) / 3

Nell'illustrazione di 45 gradi, in modo 0.265 è uguale a L. Nel codice, tale valore verrà moltiplicato per il raggio del cerchio desiderato.

Il **arco circolare Bezier** pagina consente di sperimentare la definizione di una curva di Bézier per simulare un arco circolare per gli angoli compreso un massimo di 180 gradi. Il [ **BezierCircularArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml) file crea un'istanza di `SKCanvasView` e un `Slider` per la selezione angolo. Il `PaintSurface` nel gestore dell'evento di [ **BezierCircularArgPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs) file code-behind utilizza una trasformazione per impostare il punto (0, 0) per il centro dell'area di disegno. Consente di disegnare un cerchio centrato su tale punto per il confronto e calcola quindi due punti di controllo della curva di Bézier:

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

I punti iniziale e finale (`point0` e `point3`) vengono calcolati in base alle normale parametriche equazioni per il cerchio. Poiché il cerchio viene centrato (0, 0), questi punti possono anche essere considerati come vettori radiali dal centro del cerchio per la circonferenza. I punti di controllo sono nelle righe che sono tangente al cerchio, in modo che vengano ad angolo retto per questi vettori radiali. Un vettore in corrispondenza di un angolo retto è semplicemente il vettore originale con le coordinate X e Y scambiate e uno di essi apportate negativo.

Di seguito è riportato il programma in esecuzione in tre piattaforme con tre diverse angolazioni:

[![](beziers-images/beziercirculararc-small.png "Schermata triplo della pagina di arco circolare Bezier")](beziers-images/beziercirculararc-large.png#lightbox "tripla schermata della pagina di arco circolare curva di Bezier")

Esaminare la schermata di Windows Mobile e si noterà che la curva di Bézier in particolare si differenzia da un semicerchio quando l'angolo è 180 gradi, ma la schermata di iOS mostra che sembra in base a un quarto cerchio correttamente quando l'angolo di 90 gradi.

Calcolare le coordinate di due punti di controllo è molto semplice quando il quarto di cerchio è orientato simile al seguente:

![](beziers-images/bezierarc90.png "Approssimazione di un quarto di cerchio con una curva di Bézier")

Se il raggio del cerchio è 100, quindi *L* è 55 e che è un numero facile da ricordare.

Il **elevazione al quadrato cerchio** pagina aggiunge un'animazione una figura tra un cerchio e un quadrato. Il cerchio viene approssimare le cui coordinate vengono visualizzati nella prima colonna di questa definizione di matrice in quattro curve di Bézier il [ `SquaringTheCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) classe:

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

La seconda colonna contiene le coordinate di quattro curve di Bézier che definiscono un quadrato la cui area equivale approssimativamente l'area del cerchio. (Creazione di un quadrato con il *esatta* area come un cerchio specificato è il problema geometrico irrisolvibile classico di [elevazione al quadrato cerchio](https://en.wikipedia.org/wiki/Squaring_the_circle).) Per il rendering di un quadrato con curve di Bézier, due punti di controllo per ogni curva sono uguali e sono collineari con i punti iniziale e finale, pertanto la curva di Bézier viene visualizzata come una linea retta.

La terza colonna della matrice è per i valori interpolati per un'animazione. La pagina consente di impostare un timer per 16 millisecondi e `PaintSurface` gestore viene chiamato con che frequenza:

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

I punti vengono interpolati basata su un valore sinusoidally intermittenti di `t`. I punti interpolati vengono quindi utilizzati per costruire una serie di quattro curve di Bézier collegate. Di seguito è riportato l'animazione in esecuzione in tre piattaforme che mostra lo stato di avanzamento da un cerchio un quadrato:

[![](beziers-images/squaringthecircle-small.png "Schermata triplo del Squaring pagina cerchio")](beziers-images/squaringthecircle-large.png#lightbox "schermata triplo del Squaring pagina cerchio")

Questo tipo un'animazione sarebbe impossibile senza curve di modo algoritmico sufficientemente flessibile da sottoporre a rendering come archi circolari e linee rette.

Il **Bezier infinito** pagina consente inoltre di sfruttare la possibilità di una curva di Bézier per simulare un arco circolare. Ecco il `PaintSurface` gestore di [ `BezierInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) classe:

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

Potrebbe essere un buon esercizio per tracciare queste coordinate su carta grafico per visualizzare il tipo di correlazione. Il simbolo di infinito viene centrato intorno al punto (0, 0) e dispone di due cicli centri di (–150, 0) e (150, 0) e un raggio di 100. Nella serie di `CubicTo` comandi, è possibile vedere coordinate dei punti di controllo in modo semplice i valori –95 e –205 X, tali valori sono –150 più e meno 55, 205 e 95 (150 più e meno 55), nonché 250 e –250 per i lati destro e sinistro. L'unica eccezione è quando il simbolo di infinito incrocia al centro. In tal caso, i punti di controllo hanno coordinate con una combinazione di 50 e da – 50 per appiattire la curva in prossimità del centro.

Ecco il simbolo di infinito in tutte e tre le piattaforme:

[![](beziers-images/bezierinfinity-small.png "Schermata triplo della pagina Bézier infinito")](beziers-images/bezierinfinity-large.png#lightbox "tripla schermata della pagina infinito di Bézier")

È leggermente più efficiente verso il centro il simbolo di infinito rendering il **arco infinito** pagina dal [ **tre modi per disegnare un arco** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) articolo.

## <a name="the-quadratic-bzier-curve"></a>La curva di Bézier quadratica

La curva di Bézier quadratica ha solo un punto di controllo e la curva è definita da solo tre punti: il punto di inizio, il punto di controllo e il punto finale. Le equazioni parametriche sono molto simili a curva di Bézier cubica, ad eccezione del fatto che l'esponente più elevato è 2, pertanto la curva è un polinomiale quadratica:

x(t) = (1 – t) ²x₀ 2t + (1 – t) x₁ + t²x₂

y(t) = (1 – t) ²y₀ 2t + (1 – t) y₁ + t²y₂

Per aggiungere una curva di Bézier quadratica a un percorso, utilizzare il [ `QuadTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.QuadTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/) metodo o [ `QuadTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.QuadTo/p/System.Single/System.Single/System.Single/System.Single/) overload con separato `x` e `y` coordinate:

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

I metodi di aggiungono una curva dalla posizione corrente a `point2` con `point1` come punto di controllo.

È possibile sperimentare le curve di Bézier quadratiche con il **quadratica** pagina, che è molto simile al **curva di Bezier** pagina ma contiene solo tre punti di tocco. Ecco il `PaintSurface` gestore di [ **QuadraticCurve.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) file code-behind:

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

E qui è in esecuzione in tutti e tre le piattaforme:

[![](beziers-images/quadraticcurve-small.png "Schermata triplo della pagina quadratica")](beziers-images/quadraticcurve-large.png#lightbox "tripla schermata della pagina quadratica")

Le linee punteggiate tangenti la curva nel punto di inizio e fine e soddisfare nel punto di controllo.

Di Bézier quadratica è utile se è necessaria una curva di una forma generale, ma si preferisce la praticità di un solo punto di controllo anziché due. Esegue il rendering di Bézier quadratica in modo più efficiente rispetto a qualsiasi altra curva, motivo per cui viene utilizzata internamente in Skia per eseguire il rendering archi ellittici.

Tuttavia, la forma di una curva di Bézier quadratica non ellittica, motivo per cui sono necessari più Béziers quadratica per simulare un arco ellittico. Di Bézier quadratica è invece un segmento di un parabolica.

## <a name="the-conic-bzier-curve"></a>La curva di Bézier conica

La curva di Bézier conica &mdash; noto anche come il razionale curva di Bézier quadratica &mdash; costituisce un'aggiunta relativamente più recente per la famiglia di curve di Bézier. Ad esempio la curva di Bézier quadratica, la curva di Bézier quadratica razionale implica un punto di inizio, un punto finale e un punto di controllo. Ma richiede anche la curva di Bézier quadratica razionale un *peso* valore. Viene chiamato un *razionale* quadratica perché le formule parametriche prevedono l'uso di rapporti.

Le equazioni parametriche per X e Y sono rapporti che condividono il denominatore stesso. Ecco l'equazione per il denominatore *t* compreso tra 0 a 1 e un valore di peso di *w*:

Ät = (1 – t) ² + 2wt(1 – t) + t²

In teoria, un quadratica razionale può includere tre valori di peso separati, uno per ognuno dei tre termini, ma queste possono essere semplificate per un solo valore di peso il termine intermedio.

Le equazioni parametriche per le coordinate X e Y sono simili alle equazioni parametriche per di Bézier quadratica ad eccezione del fatto che il termine intermedio include anche il valore di peso e l'espressione viene diviso per il denominatore:

x(t) = ((1 – t)²x₀ + 2wt(1 – t)x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ 2wt + (1 – t) y₁ t²y₂ +)) ÷ Ät

Sono detti anche razionale di Bézier quadratica *coniche* perché possono rappresentare esattamente i segmenti di ogni sezione conica &mdash; hyperbolas, parabolas, puntini di sospensione e cerchi.

Per aggiungere una curva di Bézier quadratica razionale a un percorso, utilizzare il [ `ConicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ConicTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/System.Single/) metodo o [ `ConicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ConicTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/) overload con separato `x` e `y` coordinate:

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

Si noti finale `weight` parametro.

Il **curva conica** pagina consente di sperimentare queste curve. La classe `ConicCurvePage` deriva da `InteractivePage`. Il [ **ConicCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml) file crea un `Slider` per selezionare un valore di peso tra – 2 e 2. Il [ **ConicCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs) file code-behind crea tre `TouchPoint` oggetti e `PaintSurface` gestore semplicemente il rendering la curva risulta con le righe al controllo della tangente punti:

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

Di seguito viene eseguita su tutti e tre le piattaforme:

[![](beziers-images/coniccurve-small.png "Schermata di triplo della pagina curva conica")](beziers-images/coniccurve-large.png#lightbox "tripla schermata della pagina curva conica")

Come si può notare, il punto di controllo sembra pull curva verso altre, il peso è più alto. Quando il peso è zero, la curva diventa una linea retta dal punto di inizio e il punto finale.

In teoria, pesi negativi sono consentiti e causare la curva di piegatura *lontano* dal punto di controllo. Tuttavia, pondera di -1 o di sotto di causa del denominatore nelle equazioni parametrici diventi negativo per determinati valori di *t*. Probabilmente per questo motivo, pesi negativi vengono ignorati i `ConicTo` metodi. Il **curva conica** programma consente di impostare i pesi negativi, ma come è possibile visualizzare la sperimentazione, pesi negativi hanno lo stesso effetto di un peso pari a zero e provocare una linea retta da sottoporre a rendering.

È molto semplice derivare il punto di controllo e il peso di utilizzare il `ConicTo` metodo consente di disegnare un arco circolare fino a (ma non inclusi) un semicerchio. Nel diagramma seguente, tangenti dai punti iniziale e finale riunisce il punto di controllo.

![](beziers-images/conicarc.png "A seguito del rendering conica arco di un arco circolare")

È possibile utilizzare trigonometriche per determinare la distanza del punto di controllo dal centro del cerchio: è il raggio del cerchio diviso per il coseno metà α. Per disegnare un arco circolare tra i punti iniziale e finale, impostare il peso di tale stesso coseno metà. Si noti che se l'angolo è 180 gradi, quindi le righe tangente mai conformi e il peso è uguale a zero. Ma per gli angoli minore di 180 gradi, di matematica funziona correttamente.

Il **conica arco circolare** pagina viene illustrata questa. Il [ **ConicCircularArc.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml) file crea un `Slider` per la selezione angolo. Il `PaintSurface` gestore di [ **ConicCircularArc.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs) file code-behind calcola il punto di controllo e il peso:

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

Come si può notare, non vi è alcuna differenza visual tra il `ConicTo` percorso visualizzato in rosso e il cerchio sottostante visualizzati per riferimento:

[![](beziers-images/coniccirculararc-small.png "Schermata di triplo della pagina conica arco circolare")](beziers-images/coniccirculararc-large.png#lightbox "tripla schermata della pagina conica arco circolare")

Ma, impostare l'angolo di 180 gradi e di errori matematici.

In questo caso è ingrato che `ConicTo` non supporta i pesi negativi, poiché in teoria (basato sulle equazioni parametriche), è possibile eseguire il cerchio con un'altra chiamata a `ConicTo` con gli stessi punti ma un valore negativo del peso. Ciò consente la creazione di un intero cerchio con solo due `ConicTo` curve in base a qualsiasi angolo tra (ma non inclusi) zero gradi e 180 gradi.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
