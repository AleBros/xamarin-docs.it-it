---
title: Informazioni sul percorso e enumerazione
description: Ottenere informazioni sui percorsi e di enumerazione del contenuto
ms.topic: article
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 09/12/2017
ms.openlocfilehash: c7edf0c8e563dad25693d184d3a44a3e66466126
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="path-information-and-enumeration"></a>Informazioni sul percorso e enumerazione

_Ottenere informazioni sui percorsi e di enumerazione del contenuto_

Il [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) classe definisce molte proprietà e metodi che consentono di ottenere informazioni sul percorso. Il [ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/) e [ `TightBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.TightBounds/) (proprietà e metodi correlati) ottengono le dimensioni di un percorso di metrica. Il [ `Contains` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Contains/p/System.Single/System.Single/) metodo consente di determinare se un particolare punto è all'interno di un percorso.

È talvolta utile determinare la lunghezza totale di tutte le linee e curve che costituiscono un percorso. Questo non è un'attività semplice modo algoritmico, pertanto un'intera classe denominata [ `PathMeasure` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasure/) è dedicata ad esso.

È talvolta utile ottenere tutte le operazioni e punti che compongono un percorso di disegno. Inizialmente, questa funzionalità potrebbe sembrare non necessaria: se il programma ha creato il percorso, il programma conosce già il contenuto. Tuttavia, si è visto che è possono anche creare percorsi da [effetti percorso](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) e convertendo [stringhe di testo in tracciati](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md). È anche possibile ottenere tutte le operazioni e punti che compongono questi percorsi di disegno. Una possibilità consiste nell'applicare una trasformazione algoritmica a tutti i punti. In questo modo le tecniche, ad esempio il testo intorno a un emisfero:

![](information-images/pathenumerationsample.png "Testo disposto su un emisfero.")

## <a name="getting-the-path-length"></a>La lunghezza del percorso di recupero

Nell'articolo [ **percorsi e testo** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) è stato illustrato come utilizzare il [ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/) metodo per creare una stringa di testo con linea di base segue il corso di un percorso. Ma cosa accade se si desidera il testo ridimensionato in modo da adattarlo con precisione il percorso? Per la creazione di testo intorno a un cerchio, questo è facile quanto semplice per calcolare la circonferenza del cerchio. Ma non è così semplice la circonferenza di un'ellisse o la lunghezza di una curva di Bézier. 

Il [ `SKPathMeasure` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasure/) consente di classe. Il [costruttore](https://developer.xamarin.com/api/constructor/SkiaSharp.SKPathMeasure.SKPathMeasure/p/SkiaSharp.SKPath/System.Boolean/System.Single/) accetta un `SKPath` argomento e [ `Length` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPathMeasure.Length/) proprietà indica la lunghezza.

Come illustrato nel **lunghezza del percorso** esempio, in cui si basa sul **curva di Bezier** pagina. Il [ **PathLengthPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml) file deriva da `InteractivePage` e include un'interfaccia touch:

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Curves.PathLengthPage"
                       Title="Path Length">
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</local:InteractivePage>
```

Il [ **PathLengthPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) file code-behind consente di spostare quattro punti di tocco per definire i punti finali e punti di controllo della curva di Bézier cubica. Tre campi definiscono una stringa di testo, un `SKPaint` oggetto e una larghezza calcolata del testo:

```csharp
public partial class PathLengthPage : InteractivePage
{
    const string text = "Compute length of path";

    static SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Black,
        TextSize = 10,
    };

    static readonly float baseTextWidth = textPaint.MeasureText(text);
    ...
}
```

Il `baseTextWidth` campo corrisponde alla larghezza del testo in base a un `TextSize` l'impostazione di 10.

Il `PaintSurface` gestore Disegna la curva di Bézier e quindi di adattare lungo interamente il testo di dimensioni:

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

        // Get path length
        SKPathMeasure pathMeasure = new SKPathMeasure(path, false, 1);

        // Find new text size
        textPaint.TextSize = pathMeasure.Length / baseTextWidth * 10;

        // Draw text on path
        canvas.DrawTextOnPath(text, path, 0, 0, textPaint);
    }
    ...
}
```

Il `Length` proprietà dell'oggetto appena creato `SKPathMeasure` oggetto Ottiene la lunghezza del percorso. Questo viene diviso per il `baseTextWidth` valore (che è la larghezza del testo in base a una dimensione del testo di 10) e quindi moltiplicato per la dimensione del testo di base di 10. Il risultato è una nuova dimensione del testo per la visualizzazione del testo lungo tale percorso:

[![](information-images/pathlength-small.png "Schermata di triplo della pagina di lunghezza del percorso")](information-images/pathlength-large.png#lightbox "tripla schermata della pagina di lunghezza del percorso")

Come la curva di Bézier Ottiene superiori o inferiori, è possibile visualizzare le dimensioni del testo di modifica.

## <a name="traversing-the-path"></a>Il percorso di attraversamento

`SKPathMeasure` effettuare più misura la lunghezza del percorso. Per qualsiasi valore compreso tra zero e la lunghezza del percorso, un `SKPathMeasure` oggetto è possibile ottenere la posizione, il percorso e la tangente alla curva di percorso a questo punto. La tangente è disponibile come un vettore sotto forma di un `SKPoint` dell'oggetto o come una rotazione incapsulate in un `SKMatrix` oggetto. Ecco i metodi di `SKPathMeasure` che ottenere queste informazioni in modi diversi e flessibile:

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

Il [ `SKPathMeasureMatrixFlags` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasureMatrixFlags/) sono:

- [`GetPosition`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPosition/)
- [`GetTangent`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPositionAndTangent/)
- [`GetPositionAndTangent`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPositionAndTangent/)

Il **Pipe metà del monociclo si trova** pagina aggiunge un'animazione una figura stilizzata su del monociclo si trova a sostituire avanti e indietro lungo la curva di Bézier cubica:

[![](information-images/unicyclehalfpipe-small.png "Schermata di triplo della pagina Pipe metà del monociclo si trova")](information-images/unicyclehalfpipe-large.png#lightbox "tripla schermata della pagina Pipe metà del monociclo si trova")

Il `SKPaint` oggetto utilizzato per la traccia sia half-pipe e del monociclo si trova è definito come un campo di [ `UnicycleHalfPipePage` ]() classe. È anche definito il `SKPath` oggetto per del monociclo si trova:

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };

    SKPath unicyclePath = SKPath.ParseSvgPathData(
        "M 0 0" + 
        "A 25 25 0 0 0 0 -50" +
        "A 25 25 0 0 0 0 0 Z" +
        "M 0 -25 L 0 -100" +
        "A 15 15 0 0 0 0 -130" +
        "A 15 15 0 0 0 0 -100 Z" +
        "M -25 -85 L 25 -85");
    ...
}
```

La classe contiene le sostituzioni di standard di `OnAppearing` e `OnDisappearing` metodi per l'animazione. Il `PaintSurface` gestore crea il percorso per la pipe di metà e quindi disegnato. Un `SKPathMeasure` oggetto viene quindi creato in base a questo percorso:

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath pipePath = new SKPath())
        {
            pipePath.MoveTo(50, 50);
            pipePath.CubicTo(0, 1.25f * info.Height, 
                             info.Width - 0, 1.25f * info.Height,
                             info.Width - 50, 50);

            canvas.DrawPath(pipePath, strokePaint);

            using (SKPathMeasure pathMeasure = new SKPathMeasure(pipePath))
            {
                float length = pathMeasure.Length;

                // Animate t from 0 to 1 every three seconds
                TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
                float t = (float)(timeSpan.TotalSeconds % 5 / 5);

                // t from 0 to 1 to 0 but slower at beginning and end
                t = (float)((1 - Math.Cos(t * 2 * Math.PI)) / 2);

                SKMatrix matrix;
                pathMeasure.GetMatrix(t * length, out matrix, 
                                      SKPathMeasureMatrixFlags.GetPositionAndTangent);

                canvas.SetMatrix(matrix);
                canvas.DrawPath(unicyclePath, strokePaint);
            }
        }
    }
}
```

Il `PaintSurface` gestore calcola un valore di `t` che va da 0 a 1 ogni cinque secondi. Viene quindi utilizzato il `Math.Cos` funzione per convertirli in un valore di `t` che va da 0 a 1 e reimpostarlo su 0, dove 0 corrisponde al del monociclo si trova all'inizio in alto a sinistra, mentre 1 corrisponde al del monociclo si trova in alto a destra. La funzione coseno causa la velocità di essere più lenti nella parte superiore della pipe e più rapido nella parte inferiore.

Si noti che questo valore di `t` deve essere moltiplicato per la lunghezza del percorso per il primo argomento per `GetMatrix`. La matrice viene quindi applicata per la `SKCanvas` oggetto per il disegno il percorso del monociclo si trova.

## <a name="enumerating-the-path"></a>Il percorso di enumerazione

Due incorporati di classi di `SKPath` consentono di enumerare il contenuto del percorso. Queste classi sono [ `SKPath.Iterator` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath+Iterator/) e [ `SKPath.RawIterator` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath+RawIterator/). Le due classi sono molto simili, ma `SKPath.Iterator` può eliminare elementi nel percorso di lunghezza zero o vicino a lunghezza zero. Il `RawIterator` viene utilizzato nell'esempio seguente.

È possibile ottenere un oggetto di tipo `SKPath.RawIterator` chiamando il [ `CreateRawIterator` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CreateRawIterator()/) metodo `SKPath`. L'enumerazione tramite il percorso viene eseguito più volte chiamando il [ `Next` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath+RawIterator.Next/p/SkiaSharp.SKPoint[]/) metodo. Passare una matrice di quattro `SKPoint` valori:

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

Il `Next` il metodo restituisce un membro del [ `SKPathVerb` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathVerb/) enumerazione. Questi valori indicano il comando di disegno particolare nel percorso. Il numero di punti validi inseriti nella matrice dipende da questo verbo:

- [`Move`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Move/) con un singolo punto
- [`Line`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Line/) con due punti
- [`Cubic`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Cubic/) con quattro punti
- [`Quad`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Quad/) con tre punti
- [`Conic`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Conic/) con tre punti (e di chiamare anche il [ `ConicWeight` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath+RawIterator.ConicWeight/) metodo per il peso)
- [`Close`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Close/) con un punto
- [`Done`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Done/)

Il `Done` verbo indica che l'enumerazione è stata completata.

Si noti che esistono alcun `Arc` verbi. Per indicare che tutti gli archi vengono convertiti in caso di aggiunta al percorso di Bézier.

Alcune delle informazioni di `SKPoint` matrice è ridondante. Ad esempio, se un `Move` verbo è seguita da un `Line` verbo, quindi il primo dei due punti che accompagnano il `Line` equivale il `Move` punto. In pratica, questa ridondanza è molto utile. Quando si ottengono un `Cubic` verbo, accompagnato da tutti i quattro punti che definiscono la curva di Bézier cubica. Non è necessario mantenere la posizione corrente stabilita per il verbo precedente.

Il verbo problematico, tuttavia, è `Close`. Questo comando Disegna una linea retta dalla posizione corrente all'inizio del contorno stabilito in precedenza per il `Move` comando. In teoria, il `Close` verbo deve fornire i due punti, anziché un unico punto. Peggiore dei casi è che il punto che accompagna il `Close` verbo è sempre (0, 0). Ciò significa che durante l'enumerazione di un percorso, sarà probabilmente necessario mantenere il `Move` punto e la posizione corrente.

Il metodo statico [ `PathExtensions` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) classe contiene vari metodi che convertono i tre tipi di curve di Bézier in una serie di linee rette leggera che approssimativa della curva. (Le formule parametriche sono state presentate nell'articolo [ **tre tipi di curve di Bézier**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md).) Il `Interpolate` metodo suddivide una linea retta in numerose righe brevi che sono di sola unità di lunghezza:

```csharp
static class PathExtensions
{
    ...
    static SKPoint[] Interpolate(SKPoint pt0, SKPoint pt1)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * pt0.X + t * pt1.X;
            float y = (1 - t) * pt0.Y + t * pt1.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenCubic(SKPoint pt0, SKPoint pt1, SKPoint pt2, SKPoint pt3)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2) + Length(pt2, pt3));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * (1 - t) * pt0.X +
                        3 * t * (1 - t) * (1 - t) * pt1.X +
                        3 * t * t * (1 - t) * pt2.X +
                        t * t * t * pt3.X;
            float y = (1 - t) * (1 - t) * (1 - t) * pt0.Y +
                        3 * t * (1 - t) * (1 - t) * pt1.Y +
                        3 * t * t * (1 - t) * pt2.Y +
                        t * t * t * pt3.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenQuadratic(SKPoint pt0, SKPoint pt1, SKPoint pt2)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenConic(SKPoint pt0, SKPoint pt1, SKPoint pt2, float weight)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float denominator = (1 - t) * (1 - t) + 2 * weight * t * (1 - t) + t * t;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * weight * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * weight * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            x /= denominator;
            y /= denominator;
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

Tutti questi metodi vengono fatto riferimento dal metodo di estensione `CloneWithTransform` illustrato di seguito. Questo metodo consente di duplicare un percorso enumerando i comandi di percorso e la creazione di un nuovo percorso in base ai dati. Tuttavia, il nuovo percorso è composta solo da `MoveTo` e `LineTo` chiamate. Tutte le curve e linee rette vengono ridotti a una serie di linee di piccole dimensioni.

Quando si chiama `CloneWithTransform`, si passa al metodo un `Func<SKPoint, SKPoint>`, che è una funzione con un `SKPaint` parametro che restituisce un `SKPoint` valore. Questa funzione viene chiamata per ogni punto applicare una trasformazione personalizzata algoritmica:

```csharp
static class PathExtensions
{
    public static SKPath CloneWithTransform(this SKPath pathIn, Func<SKPoint, SKPoint> transform)
    {
        SKPath pathOut = new SKPath();

        using (SKPath.RawIterator iterator = pathIn.CreateRawIterator())
        {
            SKPoint[] points = new SKPoint[4];
            SKPathVerb pathVerb = SKPathVerb.Move;
            SKPoint firstPoint = new SKPoint();
            SKPoint lastPoint = new SKPoint();

            while ((pathVerb = iterator.Next(points)) != SKPathVerb.Done)
            {
                switch (pathVerb)
                {
                    case SKPathVerb.Move:
                        pathOut.MoveTo(transform(points[0]));
                        firstPoint = lastPoint = points[0];
                        break;

                    case SKPathVerb.Line:
                        SKPoint[] linePoints = Interpolate(points[0], points[1]);

                        foreach (SKPoint pt in linePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[1];
                        break;

                    case SKPathVerb.Cubic:
                        SKPoint[] cubicPoints = FlattenCubic(points[0], points[1], points[2], points[3]);

                        foreach (SKPoint pt in cubicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[3];
                        break;

                    case SKPathVerb.Quad:
                        SKPoint[] quadPoints = FlattenQuadratic(points[0], points[1], points[2]);

                        foreach (SKPoint pt in quadPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Conic:
                        SKPoint[] conicPoints = FlattenConic(points[0], points[1], points[2], iterator.ConicWeight());

                        foreach (SKPoint pt in conicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Close:
                        SKPoint[] closePoints = Interpolate(lastPoint, firstPoint);

                        foreach (SKPoint pt in closePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        firstPoint = lastPoint = new SKPoint(0, 0);
                        pathOut.Close();
                        break;
                }
            }
        }
        return pathOut;
    }
    ...
}
```

Poiché il percorso duplicato viene ridotto a linee rette di piccole dimensioni, la funzione di trasformazione è in grado di conversione linee rette in curve.

Si noti che il metodo mantiene il primo punto di ogni profilo nella variabile denominata `firstPoint` e la posizione corrente dopo ogni comando di disegno nella variabile `lastPoint`. Questi sono necessari per costruire la chiusura finali riga quando un `Close` verbo viene rilevato.

Il **GlobularText** esempio utilizza questo metodo di estensione per apparentemente includere testo intorno a un emisfero un effetto 3D:

[![](information-images/globulartext-small.png "Schermata triplo della pagina di testo Globular")](information-images/globulartext-large.png#lightbox "tripla schermata della pagina di testo Globular")

Il [ `GlobularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) questa trasformazione viene eseguita dal costruttore di classe. Crea un `SKPaint` dell'oggetto per il testo e quindi Ottiene un `SKPath` dall'oggetto di `GetTextPath` metodo. Si tratta del percorso passato per il `CloneWithTransform` il metodo di estensione insieme a una funzione di trasformazione: 

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;

    public GlobularTextPage()
    {
        Title = "Globular Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.Typeface = SKTypeface.FromFamilyName("Times New Roman");
            textPaint.TextSize = 100;

            using (SKPath textPath = textPaint.GetTextPath("HELLO", 0, 0))
            {
                SKRect textPathBounds;
                textPath.GetBounds(out textPathBounds);

                globePath = textPath.CloneWithTransform((SKPoint pt) =>
                {
                    double longitude = (Math.PI / textPathBounds.Width) * 
                                            (pt.X - textPathBounds.Left) - Math.PI / 2;
                    double latitude = (Math.PI / textPathBounds.Height) * 
                                            (pt.Y - textPathBounds.Top) - Math.PI / 2;

                    longitude *= 0.75;
                    latitude *= 0.75;

                    float x = (float)(Math.Cos(latitude) * Math.Sin(longitude));
                    float y = (float)Math.Sin(latitude);

                    return new SKPoint(x, y);
                });
            }
        }
    }
    ...
}
```

La funzione di trasformazione Calcola prima di due valori denominati `longitude` e `latitude` compresi tra-π/2 all'inizio e alla sinistra del testo, e π/2 destro e inferiore del testo. L'intervallo di questi valori non visivamente soddisfacente, in modo che si ottiene moltiplicando 0,75. (Provare il codice senza tali modifiche. Il testo diventa troppo poco settentrionale e meridionale poli e thin troppo lungo i lati.) Queste coordinate sferiche tridimensionale vengono convertite a bidimensionale `x` e `y` coordinate dalle formule standard.

Il nuovo percorso viene archiviato come un campo. Il `PaintSurface` gestore semplicemente deve quindi al centro e ridimensionare il percorso per la visualizzazione sullo schermo:

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint pathPaint = new SKPaint())
        {
            pathPaint.Style = SKPaintStyle.Fill;
            pathPaint.Color = SKColors.Blue;
            pathPaint.StrokeWidth = 3;
            pathPaint.IsAntialias = true;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(0.45f * Math.Min(info.Width, info.Height));     // radius
            canvas.DrawPath(globePath, pathPaint);
        }
    }
}
```

Questa è una tecnica molto versatile. Se la matrice degli effetti di percorso è descritto nel [ **effetti percorso** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) articolo piuttosto non include un valore è ritenuto deve essere incluso, questo è un modo per colmare le lacune.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
