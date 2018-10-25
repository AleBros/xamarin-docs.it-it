---
title: Informazioni sui tracciati ed enumerazione
description: Questo articolo illustra come ottenere informazioni sui percorsi di SkiaSharp ed enumerare il contenuto e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
ms.openlocfilehash: 6efefe11b31428f41bfa945aff93aa70aa764870
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "39615275"
---
# <a name="path-information-and-enumeration"></a>Informazioni sui tracciati ed enumerazione

_Ottenere informazioni sui percorsi ed enumerare il contenuto_

Il [ `SKPath` ](xref:SkiaSharp.SKPath) classe definisce diverse proprietà e metodi che consentono di ottenere informazioni sul percorso. Il [ `Bounds` ](xref:SkiaSharp.SKPath.Bounds) e [ `TightBounds` ](xref:SkiaSharp.SKPath.TightBounds) (proprietà e metodi correlati) ottengono la metrica: dimensioni di un percorso. Il [ `Contains` ](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single)) metodo consente di determinare se un particolare punto è all'interno di un percorso.

È talvolta utile determinare la lunghezza totale di tutte le linee e curve che costituiscono un percorso. Calcolare la lunghezza non è un'attività semplice modo algoritmico, in modo che un'intera classe denominata [ `PathMeasure` ](xref:SkiaSharp.SKPathMeasure) è dedicato ad esso.

È anche talvolta utile ottenere tutte le operazioni e punti che compongono un percorso di disegno. Inizialmente, questa funzionalità potrebbe sembrare non necessaria: se il programma ha creato il percorso, il programma è già noto il contenuto. Tuttavia, si è visto che i percorsi possono essere creati anche da [effetti per il tracciato](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) e dalla conversione [stringhe di testo in tracciati](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md). È anche possibile ottenere tutte le operazioni e punti che compongono queste percorsi di disegno. Una possibilità è per applicare una trasformazione algoritmica per tutti i punti, ad esempio, per disporre testo intorno a un emisfero:

![](information-images/pathenumerationsample.png "Testo disposto su un emisfero")

## <a name="getting-the-path-length"></a>Ottenere la lunghezza del percorso

In questo articolo [ **tracciati e testo** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) è stato illustrato come usare il [ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) metodo per la creazione di una stringa di testo la cui base segue il corso di un percorso. Ma cosa accade se si vuole ridimensionare il testo in modo che quest'ultima rientri il percorso con precisione? Disegno di testo intorno a un cerchio è facile quanto è semplice da calcolare la circonferenza del cerchio. Ma non è così semplice la circonferenza di un'ellisse o la lunghezza di una curva di Bézier.

Il [ `SKPathMeasure` ](xref:SkiaSharp.SKPathMeasure) classe può rivelarsi utile. Il [costruttore](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single)) accetta una `SKPath` argomento e il [ `Length` ](xref:SkiaSharp.SKPathMeasure.Length) proprietà rivela la relativa lunghezza.

Questa classe è illustrata nel **lunghezza del percorso** esempio, in cui si basa sul **curva di Bézier** pagina. Il [ **PathLengthPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml) file deriva da `InteractivePage` e include un'interfaccia touch:

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

Il [ **PathLengthPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs) file code-behind consente di spostare quattro punti di tocco per definire gli endpoint e punti di controllo della curva di Bezier cubica. Tre campi definiscono una stringa di testo, un `SKPaint` oggetto e una larghezza calcolata del testo:

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

Il `baseTextWidth` campo è la larghezza del testo basato su un `TextSize` impostazione pari a 10.

Il `PaintSurface` gestore consente di disegnare la curva di Bézier e quindi ridimensiona il testo da adattare lungo la sua lunghezza totale:

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

Il `Length` proprietà dell'oggetto appena creato `SKPathMeasure` oggetto Ottiene la lunghezza del percorso. La lunghezza del percorso viene diviso per il `baseTextWidth` valore (ovvero la larghezza del testo basato su una dimensione del testo di 10) e quindi viene moltiplicato per la dimensione del testo di base di 10. Il risultato è un nuovo formato di testo per la visualizzazione del testo lungo tale percorso:

[![](information-images/pathlength-small.png "Tripla screenshot della pagina della lunghezza del percorso")](information-images/pathlength-large.png#lightbox "tripla screenshot della pagina della lunghezza del percorso")

Come la curva di Bézier Ottiene superiori o inferiori, è possibile visualizzare le dimensioni del testo di modifica.

## <a name="traversing-the-path"></a>Che attraversa il percorso

`SKPathMeasure` può eseguire più della semplice misura la lunghezza del percorso. Per qualsiasi valore compreso tra zero e la lunghezza del percorso, un `SKPathMeasure` oggetto è possibile ottenere la posizione nel percorso e la tangente della curva di percorso di quel punto. La tangente è disponibile come un vettore sotto forma di un' `SKPoint` dell'oggetto o come una rotazione incapsulato in un `SKMatrix` oggetto. Ecco i metodi di `SKPathMeasure` che ottenere queste informazioni in modi diversi e flessibile:

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

I membri del [ `SKPathMeasureMatrixFlags` ](xref:SkiaSharp.SKPathMeasureMatrixFlags) enumerazione sono:

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

Il **Pipe con metà del monociclo si trova** pagina consente di animare una figura stilizzata su del monociclo si trova che sembra a recarsi avanti e indietro lungo la curva di Bézier cubica:

[![](information-images/unicyclehalfpipe-small.png "Tripla screenshot della pagina del monociclo si trova Pipe con metà")](information-images/unicyclehalfpipe-large.png#lightbox "tripla screenshot della pagina di Pipe con metà del monociclo si trova")

Il `SKPaint` oggetto utilizzato per che traccia la metà-pipe sia del monociclo si trova è definito come un campo il [ `UnicycleHalfPipePage` ]() classe. È anche definito il `SKPath` oggetto per del monociclo si trova:

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

La classe contiene gli override standard della `OnAppearing` e `OnDisappearing` metodi per l'animazione. Il `PaintSurface` gestore crea il percorso per la pipe di metà e quindi lo disegna. Un `SKPathMeasure` oggetto quindi viene creato in questo percorso di base:

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

Il `PaintSurface` gestore calcola un valore di `t` che va da 0 a 1 ogni cinque secondi. Quindi, utilizza il `Math.Cos` funzione per convertirlo in un valore di `t` compreso nell'intervallo da 0 a 1 e nuovamente su 0, dove 0 corrisponde al del monociclo si trova all'inizio in alto a sinistra, mentre 1 corrisponde al del monociclo si trova in alto a destra. La funzione coseno fa sì che la velocità a essere più lenti nella parte superiore della pipe e più veloce nella parte inferiore.

Si noti che questo valore di `t` deve essere moltiplicato con la lunghezza del percorso per il primo argomento `GetMatrix`. La matrice viene quindi applicata al `SKCanvas` oggetto per il percorso del monociclo si trova di disegno.

## <a name="enumerating-the-path"></a>L'enumerazione di percorso

Due incorporati di classi di `SKPath` consentono di enumerare il contenuto del percorso. Queste classi sono [ `SKPath.Iterator` ](xref:SkiaSharp.SKPath.Iterator) e [ `SKPath.RawIterator` ](xref:SkiaSharp.SKPath.RawIterator). Le due classi sono molto simili, ma `SKPath.Iterator` possono eliminare gli elementi del percorso con lunghezza zero o più vicino di lunghezza zero. Il `RawIterator` viene usato nell'esempio seguente.

È possibile ottenere un oggetto di tipo `SKPath.RawIterator` chiamando il [ `CreateRawIterator` ](xref:SkiaSharp.SKPath.CreateRawIterator) metodo `SKPath`. L'enumerazione tramite il percorso viene eseguito chiamando più volte la [ `Next` ](xref:SkiaSharp.SKPath.RawIterator.Next*) (metodo). Passare a esso una matrice di quattro `SKPoint` valori:

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

Il `Next` metodo viene restituito un membro del [ `SKPathVerb` ](xref:SkiaSharp.SKPathVerb) tipo di enumerazione. Questi valori indicano al comando disegno nel percorso. Il numero di punti validi inserito nella matrice dipende questo verbo:

- `Move` con un singolo punto
- `Line` con due punti
- `Cubic` con quattro punti
- `Quad` con tre punti
- `Conic` con tre punti (e anche chiamare il [ `ConicWeight` ](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*) metodo per il peso)
- `Close` con un solo punto
- `Done`

Il `Done` verbo indica che l'enumerazione di percorso è stato completato.

Si noti che esistono nessun `Arc` verbi. Ciò indica che tutti gli archi vengono convertiti in curve di Bézier quando aggiunto al percorso.

Alcune delle informazioni nel `SKPoint` matrice è ridondante. Ad esempio, se un `Move` verbo è seguita da un `Line` verbo, quindi il primo dei due punti che accompagnano il `Line` equivale al `Move` punto. In pratica, questa ridondanza è molto utile. Quando si raggiunge un `Cubic` verbo, accompagnato da tutti i quattro punti che definiscono la curva di Bézier cubica. Non è necessario mantenere la posizione corrente stabilita per il verbo precedente.

Il verbo problematico, è tuttavia `Close`. Questo comando consente di disegnare una linea retta dalla posizione corrente all'inizio del contorno stabilito in precedenza dal `Move` comando. In teoria, il `Close` verbo deve fornire i due punti piuttosto che solo un punto. Che cos'è peggiore è che il punto che accompagna il `Close` verbo è sempre (0, 0). Quando si enumerano attraverso un percorso, si sarà probabilmente necessario conservare il `Move` punto e la posizione corrente.

## <a name="enumerating-flattening-and-malforming"></a>L'enumerazione, rendere flat e Malforming

In alcuni casi è preferibile applicare un algoritmico trasformarlo in un percorso malform in qualche modo:

![](information-images/pathenumerationsample.png "Testo disposto su un emisfero")

La maggior parte di queste lettere sono costituiti da linee rette, ma tali linee rette hanno stati twisted apparentemente in curve. Come è possibile?

La chiave è che le linee rette originale sono suddivisi in una serie di linee rette più piccole. Quindi queste singole linee rette più piccole possono essere modificate in modi diversi in modo da formare una curva. 

Per facilitare questo processo, il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio contiene un valore statico [ `PathExtensions` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) classe con un `Interpolate` metodo che suddivide un linea retta in numerose le righe brevi che sono solo un'unità di lunghezza. Inoltre, la classe contiene diversi metodi che convertono i tre tipi di curve di Bézier in una serie di linee rette piccole dimensioni che si avvicinano della curva. (Nell'articolo sono state presentate le formule parametriche [ **tre tipi di curve di Bézier**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md).) Questo processo è detto _bidimensionalità_ curva:

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

Tutti questi metodi vengono fatto riferimento dal metodo di estensione `CloneWithTransform` anche incluso in questa classe e illustrato di seguito. Questo metodo consente di duplicare un percorso enumerando i comandi di percorso e la costruzione di un nuovo percorso in base ai dati. Tuttavia, il nuovo percorso è costituito solo `MoveTo` e `LineTo` chiamate. Tutte le curve e linee rette vengono ridotti a una serie di linee di piccole dimensioni.

Quando si chiama `CloneWithTransform`, passa al metodo un `Func<SKPoint, SKPoint>`, che è una funzione con un `SKPaint` parametro che restituisce un `SKPoint` valore. Questa funzione viene chiamata per ogni punto di applicare una trasformazione algoritmica personalizzata:

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

Si noti che il metodo mantiene il primo punto di ogni profilo nella variabile denominata `firstPoint` e la posizione corrente dopo ogni comando di disegno nella variabile `lastPoint`. Queste variabili sono necessari per costruire la parentesi finale riga quando un `Close` verbo viene rilevato.

Il **GlobularText** esempio utilizza questo metodo di estensione per apparentemente zabalit Do testo intorno a un emisfero un effetto 3D:

[![](information-images/globulartext-small.png "Tripla screenshot della pagina di testo Globular")](information-images/globulartext-large.png#lightbox "tripla screenshot della pagina di testo Globular")

Il [ `GlobularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) questa trasformazione viene eseguita dal costruttore di classe. Crea un' `SKPaint` dell'oggetto per il testo e quindi Ottiene un `SKPath` dall'oggetto di `GetTextPath` (metodo). Si tratta del percorso passato al `CloneWithTransform` metodo di estensione insieme a una funzione di trasformazione:

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

La funzione di trasformazione Calcola prima di due valori denominati `longitude` e `latitude` che vanno da 2 – π nella parte superiore sinistra del testo, a π/2 a destra e inferiore del testo. L'intervallo di questi valori non è visivamente soddisfacente, in modo sono vengono ridotti mediante la moltiplicazione per 0,75. (Provare a eseguire il codice senza tali modifiche. Il testo diventa troppo poco chiaro a nord e Sud poli e troppo magri lungo i lati.) Queste coordinate sferiche tridimensionale vengono convertiti in a bidimensionale `x` e `y` coordinate dalle formule standard.

Il nuovo percorso viene archiviato come un campo. Il `PaintSurface` gestore semplicemente deve quindi centrare e ridimensionare il percorso in cui viene visualizzato sullo schermo:

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

Questa è una tecnica molto versatile. Se la matrice degli effetti di percorso descritto nel [ **effetti per il tracciato** ](effects.md) articolo non abbastanza encompass qualcosa è ritenuto deve essere inclusa, questo è un modo per colmare le lacune.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
