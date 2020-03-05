---
title: Informazioni sui tracciati ed enumerazione
description: Questo articolo illustra come ottenere informazioni sui percorsi di SkiaSharp ed enumerare il contenuto e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
ms.openlocfilehash: 6f4f4e6253c14d86e2057f13d6232a07a83b4d26
ms.sourcegitcommit: ae5557c5024d4b7bd52b2f33cb96114ce2b8e086
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2020
ms.locfileid: "78292518"
---
# <a name="path-information-and-enumeration"></a>Informazioni sui tracciati ed enumerazione

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Ottenere informazioni sui percorsi ed enumerare il contenuto_

La classe [`SKPath`](xref:SkiaSharp.SKPath) definisce diverse proprietà e metodi che consentono di ottenere informazioni sul percorso. Le proprietà [`Bounds`](xref:SkiaSharp.SKPath.Bounds) e [`TightBounds`](xref:SkiaSharp.SKPath.TightBounds) (e i metodi correlati) ottengono le dimensioni metriche di un percorso. Il metodo [`Contains`](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single)) consente di determinare se un particolare punto si trova all'interno di un percorso.

È talvolta utile determinare la lunghezza totale di tutte le linee e curve che costituiscono un percorso. Il calcolo di questa lunghezza non è un'attività semplice algoritmicamente, quindi è dedicata un'intera classe denominata [`PathMeasure`](xref:SkiaSharp.SKPathMeasure) .

È anche talvolta utile ottenere tutte le operazioni e punti che compongono un percorso di disegno. Inizialmente, questa funzionalità potrebbe sembrare non necessaria: se il programma ha creato il percorso, il programma è già noto il contenuto. Si è tuttavia visto che i percorsi possono essere creati anche dagli [effetti del percorso](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) e dalla conversione di [stringhe di testo in percorsi](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md). È anche possibile ottenere tutte le operazioni e punti che compongono queste percorsi di disegno. Una possibilità è per applicare una trasformazione algoritmica per tutti i punti, ad esempio, per disporre testo intorno a un emisfero:

![](information-images/pathenumerationsample.png "Text wrapped on a hemisphere")

## <a name="getting-the-path-length"></a>Ottenere la lunghezza del percorso

Nei [**percorsi e nel testo**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) dell'articolo è stato illustrato come usare il metodo [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) per creare una stringa di testo la cui Baseline segue il corso di un percorso. Ma cosa accade se si vuole ridimensionare il testo in modo che quest'ultima rientri il percorso con precisione? Disegno di testo intorno a un cerchio è facile quanto è semplice da calcolare la circonferenza del cerchio. Ma non è così semplice la circonferenza di un'ellisse o la lunghezza di una curva di Bézier.

La classe [`SKPathMeasure`](xref:SkiaSharp.SKPathMeasure) può essere utile. Il [Costruttore](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single)) accetta un argomento `SKPath` e la proprietà [`Length`](xref:SkiaSharp.SKPathMeasure.Length) ne rivela la lunghezza.

Questa classe è illustrata nell'esempio relativo alla **lunghezza del percorso** , che si basa sulla pagina della **curva di Bezier** . Il file [**PathLengthPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml) deriva da `InteractivePage` e include un'interfaccia touch:

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

Il file code-behind [**PathLengthPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) consente di spostare quattro punti di tocco per definire gli endpoint e i punti di controllo di una curva di Bézier cubica. Tre campi definiscono una stringa di testo, un oggetto `SKPaint` e una larghezza calcolata del testo:

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

Il campo `baseTextWidth` corrisponde alla larghezza del testo in base a un'impostazione di `TextSize` di 10.

Il gestore `PaintSurface` disegna la curva di Bézier e quindi ridimensiona il testo per adattarsi alla lunghezza completa:

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

La proprietà `Length` dell'oggetto `SKPathMeasure` appena creato ottiene la lunghezza del percorso. La lunghezza del percorso è divisa per il valore `baseTextWidth` (ovvero la larghezza del testo in base alla dimensione del testo 10) e quindi moltiplicato per le dimensioni del testo di base di 10. Il risultato è un nuovo formato di testo per la visualizzazione del testo lungo tale percorso:

[![](information-images/pathlength-small.png "Triple screenshot of the Path Length page")](information-images/pathlength-large.png#lightbox "Triple screenshot of the Path Length page")

Come la curva di Bézier Ottiene superiori o inferiori, è possibile visualizzare le dimensioni del testo di modifica.

## <a name="traversing-the-path"></a>Che attraversa il percorso

`SKPathMeasure` possibile non solo misurare la lunghezza del percorso. Per qualsiasi valore compreso tra zero e la lunghezza del percorso, un `SKPathMeasure` oggetto può ottenere la posizione sul percorso e la tangente alla curva del percorso in quel punto. La tangente è disponibile come vettore sotto forma di oggetto `SKPoint` o come rotazione incapsulata in un oggetto `SKMatrix`. Ecco i metodi di `SKPathMeasure` che ottengono queste informazioni in modi diversi e flessibili:

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

I membri dell'enumerazione [`SKPathMeasureMatrixFlags`](xref:SkiaSharp.SKPathMeasureMatrixFlags) sono:

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

La pagina di **half pipe a monocycle** anima una figura di bastone su un monociclo che sembra spostarsi avanti e indietro lungo una curva di Bézier cubica:

[![](information-images/unicyclehalfpipe-small.png "Triple screenshot of the Unicycle Half-Pipe page")](information-images/unicyclehalfpipe-large.png#lightbox "Triple screenshot of the Unicycle Half-Pipe page")

L'oggetto `SKPaint` usato per segnare la metà pipe e il monocycle è definito come campo nella classe `UnicycleHalfPipePage`. Definito anche come oggetto `SKPath` per il monocycle:

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

La classe contiene gli override standard delle `OnAppearing` e `OnDisappearing` metodi per l'animazione. Il gestore di `PaintSurface` crea il percorso per la metà pipe e quindi lo disegna. Viene quindi creato un oggetto `SKPathMeasure` in base a questo percorso:

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

Il gestore di `PaintSurface` calcola un valore di `t` che va da 0 a 1 ogni cinque secondi. USA quindi la funzione `Math.Cos` per convertirla in un valore di `t` compreso tra 0 e 1 e viceversa, dove 0 corrisponde all'uniciclo all'inizio nella parte superiore sinistra, mentre 1 corrisponde all'uniciclo in alto a destra. La funzione coseno fa sì che la velocità a essere più lenti nella parte superiore della pipe e più veloce nella parte inferiore.

Si noti che questo valore di `t` deve essere moltiplicato per la lunghezza del percorso per il primo argomento `GetMatrix`. La matrice viene quindi applicata all'oggetto `SKCanvas` per disegnare il percorso Unicycle.

## <a name="enumerating-the-path"></a>L'enumerazione di percorso

Due classi incorporate di `SKPath` consentono di enumerare il contenuto di Path. Queste classi sono [`SKPath.Iterator`](xref:SkiaSharp.SKPath.Iterator) e [`SKPath.RawIterator`](xref:SkiaSharp.SKPath.RawIterator). Le due classi sono molto simili, ma `SKPath.Iterator` possono eliminare gli elementi nel percorso con una lunghezza zero o vicino a una lunghezza zero. Nell'esempio seguente viene usato il `RawIterator`.

È possibile ottenere un oggetto di tipo `SKPath.RawIterator` chiamando il metodo [`CreateRawIterator`](xref:SkiaSharp.SKPath.CreateRawIterator) di `SKPath`. L'enumerazione tramite il percorso viene eseguita chiamando ripetutamente il metodo [`Next`](xref:SkiaSharp.SKPath.RawIterator.Next*) . Passare a esso una matrice di quattro valori `SKPoint`:

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

Il metodo `Next` restituisce un membro del tipo di enumerazione [`SKPathVerb`](xref:SkiaSharp.SKPathVerb) . Questi valori indicano al comando disegno nel percorso. Il numero di punti validi inserito nella matrice dipende questo verbo:

- `Move` con un solo punto
- `Line` con due punti
- `Cubic` con quattro punti
- `Quad` con tre punti
- `Conic` con tre punti (e chiamare anche il metodo [`ConicWeight`](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*) per il peso)
- `Close` con un punto
- `Done`

Il verbo `Done` indica che l'enumerazione del percorso è completa.

Si noti che non sono presenti verbi `Arc`. Ciò indica che tutti gli archi vengono convertiti in curve di Bézier quando aggiunto al percorso.

Alcune informazioni della matrice `SKPoint` sono ridondanti. Se, ad esempio, un verbo `Move` è seguito da un verbo `Line`, il primo dei due punti che accompagnano il `Line` corrisponde al punto di `Move`. In pratica, questa ridondanza è molto utile. Quando si ottiene un verbo di `Cubic`, esso è accompagnato da tutti e quattro i punti che definiscono la curva di Bézier cubica. Non è necessario mantenere la posizione corrente stabilita per il verbo precedente.

Il verbo problematico, tuttavia, è `Close`. Questo comando disegna una linea retta dalla posizione corrente fino all'inizio del contorno stabilito in precedenza dal comando `Move`. Idealmente, il verbo `Close` deve fornire questi due punti anziché un solo punto. Ciò che è peggio è che il punto associato al verbo `Close` è sempre (0, 0). Quando si enumera un percorso, è probabile che sia necessario mantenere il punto di `Move` e la posizione corrente.

## <a name="enumerating-flattening-and-malforming"></a>L'enumerazione, rendere flat e Malforming

In alcuni casi è preferibile applicare un algoritmico trasformarlo in un percorso malform in qualche modo:

![](information-images/pathenumerationsample.png "Text wrapped on a hemisphere")

La maggior parte di queste lettere sono costituiti da linee rette, ma tali linee rette hanno stati twisted apparentemente in curve. Come è possibile?

La chiave è che le linee rette originale sono suddivisi in una serie di linee rette più piccole. Quindi queste singole linee rette più piccole possono essere modificate in modi diversi in modo da formare una curva.

Per semplificare questo processo, l'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contiene una classe [`PathExtensions`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) statica con un metodo `Interpolate` che suddivide una linea retta in numerose righe brevi che sono una sola unità di lunghezza. Inoltre, la classe contiene diversi metodi che convertono i tre tipi di curve di Bézier in una serie di linee rette piccole dimensioni che si avvicinano della curva. (Le formule parametriche sono state presentate nell'articolo [**tre tipi di curve di Bézier**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)). Questo processo viene chiamato _appiattimento_ della curva:

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
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

A tutti questi metodi viene fatto riferimento dal metodo di estensione `CloneWithTransform` incluso anche in questa classe e mostrato di seguito. Questo metodo consente di duplicare un percorso enumerando i comandi di percorso e la costruzione di un nuovo percorso in base ai dati. Tuttavia, il nuovo percorso è costituito solo da chiamate `MoveTo` e `LineTo`. Tutte le curve e linee rette vengono ridotti a una serie di linee di piccole dimensioni.

Quando si chiama `CloneWithTransform`, si passa al metodo un `Func<SKPoint, SKPoint>`, che è una funzione con un parametro `SKPaint` che restituisce un valore `SKPoint`. Questa funzione viene chiamata per ogni punto di applicare una trasformazione algoritmica personalizzata:

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

Poiché il percorso clonato viene ridotto in linee rette di piccole dimensioni, la funzione di trasformazione ha la capacità di conversione di linee rette alle curve.

Si noti che il metodo mantiene il primo punto di ogni contorno nella variabile denominata `firstPoint` e la posizione corrente dopo ogni comando di disegno nella variabile `lastPoint`. Queste variabili sono necessarie per costruire la linea di chiusura finale quando viene rilevato un verbo `Close`.

L'esempio **GlobularText** usa questo metodo di estensione per eseguire apparentemente il wrapping del testo intorno a un emisfero in un effetto 3D:

[![](information-images/globulartext-small.png "Triple screenshot of the Globular Text page")](information-images/globulartext-large.png#lightbox "Triple screenshot of the Globular Text page")

Il costruttore della classe [`GlobularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) esegue questa trasformazione. Viene creato un oggetto `SKPaint` per il testo, quindi viene ottenuto un oggetto `SKPath` dal metodo `GetTextPath`. Si tratta del percorso passato al metodo di estensione `CloneWithTransform` insieme a una funzione Transform:

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

La funzione Transform calcola innanzitutto due valori denominati `longitude` e `latitude` compresi tra-π/2 nella parte superiore e a sinistra del testo, fino a π/2 a destra e alla fine del testo. L'intervallo di questi valori non è visivamente soddisfacente, in modo sono vengono ridotti mediante la moltiplicazione per 0,75. (Provare a eseguire il codice senza tali modifiche. Il testo diventa troppo nascosto nei pali Nord e sud e troppo sottile sui lati. Queste coordinate sferiche tridimensionali vengono convertite in `x` bidimensionali e coordinate `y` in base alle formule standard.

Il nuovo percorso viene archiviato come un campo. Il gestore di `PaintSurface` deve quindi semplicemente centrare e ridimensionare il percorso per visualizzarlo sullo schermo:

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

Questa è una tecnica molto versatile. Se la matrice di effetti del percorso descritti nell'articolo [**sugli effetti del percorso**](effects.md) non include un elemento che si ritiene debba essere incluso, questo è un modo per colmare le lacune.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
