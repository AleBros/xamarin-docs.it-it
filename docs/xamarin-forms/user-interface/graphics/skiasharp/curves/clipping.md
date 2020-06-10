---
title: "ritaglio con percorsi e aree" Descrizione: "questo articolo spiega come usare i percorsi SkiaSharp per ritagliare i grafici in aree specifiche e per creare aree e illustrarlo con il codice di esempio".
ms. prod: Novell MS. Technology: Novell-skiasharp ms. AssetID: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F autore: davidbritch ms. Author: dabritch ms. Date: 06/16/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="clipping-with-paths-and-regions"></a>Ritaglio con tracciati e aree

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usare i percorsi per ritagliare i grafici in aree specifiche e per creare aree_

A volte è necessario limitare il rendering di elementi grafici a una determinata area. Questa operazione è nota come *ritaglio*. È possibile usare il ritaglio per gli effetti speciali, ad esempio l'immagine di una scimmia visualizzata attraverso una serratura:

![Scimmia attraverso una serratura](clipping-images/clippingsample.png)

L' *area di ridimensionamento* è l'area della schermata in cui viene eseguito il rendering della grafica. Non viene eseguito il rendering di qualsiasi elemento visualizzato al di fuori dell'area di visualizzazione. L'area di ridimensionamento è in genere definita da un rettangolo o da un [`SKPath`](xref:SkiaSharp.SKPath) oggetto, ma è possibile definire un'area di ritaglio utilizzando un [`SKRegion`](xref:SkiaSharp.SKRegion) oggetto. Questi due tipi di oggetti inizialmente sembrano correlati perché è possibile creare un'area da un percorso. Tuttavia, non è possibile creare un percorso da un'area e sono molto diversi internamente: un percorso è costituito da una serie di linee e curve, mentre un'area è definita da una serie di linee di analisi orizzontali.

L'immagine precedente è stata creata dalla **scimmia attraverso** la pagina della serratura. La [`MonkeyThroughKeyholePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) classe definisce un percorso usando i dati SVG e usa il costruttore per caricare una bitmap dalle risorse del programma:

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    SKBitmap bitmap;
    SKPath keyholePath = SKPath.ParseSvgPathData(
        "M 300 130 L 250 350 L 450 350 L 400 130 A 70 70 0 1 0 300 130 Z");

    public MonkeyThroughKeyholePage()
    {
        Title = "Monkey through Keyhole";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

Sebbene l' `keyholePath` oggetto descriva il contorno di una serratura, le coordinate sono completamente arbitrarie e riflettono ciò che è stato utile quando i dati del percorso sono stati elaborati. Per questo motivo, il `PaintSurface` gestore ottiene i limiti di questo percorso e chiama `Translate` e `Scale` per spostare il percorso al centro dello schermo e per renderlo quasi così alto come lo schermo:

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transform to center and enlarge clip path to window height
        SKRect bounds;
        keyholePath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.98f * info.Height / bounds.Height);
        canvas.Translate(-bounds.MidX, -bounds.MidY);

        // Set the clip path
        canvas.ClipPath(keyholePath);

        // Reset transforms
        canvas.ResetMatrix();

        // Display monkey to fill height of window but maintain aspect ratio
        canvas.DrawBitmap(bitmap,
            new SKRect((info.Width - info.Height) / 2, 0,
                       (info.Width + info.Height) / 2, info.Height));
    }
}
```

Ma non viene eseguito il rendering del percorso. Al contrario, dopo le trasformazioni, il percorso viene usato per impostare un'area di ritaglio con questa istruzione:

```csharp
canvas.ClipPath(keyholePath);
```

Il `PaintSurface` gestore reimposta quindi le trasformazioni con una chiamata a `ResetMatrix` e disegna la bitmap per estendere l'altezza massima dello schermo. Questo codice presuppone che la bitmap sia quadrata, che questa particolare bitmap è. Il rendering della bitmap viene eseguito solo all'interno dell'area definita dal tracciato di ritaglio:

[![Schermata tripla della scimmia attraverso la pagina della serratura](clipping-images/monkeythroughkeyhole-small.png)](clipping-images/monkeythroughkeyhole-large.png#lightbox)

Il tracciato di ritaglio è soggetto alle trasformazioni attive quando viene `ClipPath` chiamato il metodo e non alle trasformazioni attive quando viene visualizzato un oggetto grafico, ad esempio una bitmap. Il tracciato di ritaglio fa parte dello stato dell'area di disegno salvato con il `Save` metodo e ripristinato con il `Restore` metodo.

## <a name="combining-clipping-paths"></a>Combinazione di percorsi di ritaglio

In modo rigoroso, l'area di ritaglio non è "impostata" dal `ClipPath` metodo. Viene invece combinato con il tracciato di ritaglio esistente, che inizia come un rettangolo di dimensioni pari all'area di disegno. È possibile ottenere i limiti rettangolari dell'area di ritaglio utilizzando la [`ClipBounds`](xref:SkiaSharp.SKCanvas.ClipBounds) proprietà o la proprietà [`ClipDeviceBounds`](xref:SkiaSharp.SKCanvas.ClipDeviceBounds) . La `ClipBounds` proprietà restituisce un `SKRect` valore che riflette eventuali trasformazioni che potrebbero essere attive. La `ClipDeviceBounds` proprietà restituisce un `RectI` valore. Si tratta di un rettangolo con dimensioni Integer e descrive l'area di ridimensionamento nelle dimensioni effettive dei pixel.

Qualsiasi chiamata a `ClipPath` riduce l'area di ritaglio combinando l'area di ridimensionamento con una nuova area. La sintassi completa del [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath(SkiaSharp.SKPath,SkiaSharp.SKClipOperation,System.Boolean)) metodo è la seguente:

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Esiste anche un [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect(SkiaSharp.SKRect,SkiaSharp.SKClipOperation,System.Boolean)) metodo che combina l'area di ridimensionamento con un rettangolo:

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Per impostazione predefinita, l'area di ritaglio risultante è un'intersezione tra l'area di ritaglio esistente e l'oggetto `SKPath` o `SKRect` specificato nel `ClipPath` metodo o `ClipRect` . Questa operazione viene illustrata nella pagina della **clip Intersect a quattro cerchi** . Il `PaintSurface` gestore della [`FourCircleInteresectClipPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) classe riutilizza lo stesso `SKPath` oggetto per creare quattro cerchi sovrapposti, ciascuno dei quali riduce l'area di ritaglio tramite le chiamate successive a `ClipPath` :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float size = Math.Min(info.Width, info.Height);
    float radius = 0.4f * size;
    float offset = size / 2 - radius;

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    using (SKPath path = new SKPath())
    {
        path.AddCircle(-offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(-offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColors.Blue;
            canvas.DrawPaint(paint);
        }
    }
}
```

L'intersezione tra questi quattro cerchi è la seguente:

[![Schermata tripla della pagina di ritaglio a quattro cerchi](clipping-images//fourcircleintersectclip-small.png)](clipping-images/fourcircleintersectclip-large.png#lightbox)

L' [`SKClipOperation`](xref:SkiaSharp.SKClipOperation) enumerazione dispone solo di due membri:

- `Difference`rimuove il percorso o il rettangolo specificato dall'area di ritaglio esistente

- `Intersect`interseca il percorso o il rettangolo specificato con l'area di ritaglio esistente

Se si sostituiscono i quattro `SKClipOperation.Intersect` argomenti nella `FourCircleIntersectClipPage` classe con `SKClipOperation.Difference` , verrà visualizzato quanto segue:

[![Schermata tripla della pagina di ritaglio a quattro cerchi intersezione con operazione di differenza](clipping-images//fourcircledifferenceclip-small.png)](clipping-images/fourcircledifferenceclip-large.png#lightbox)

Quattro cerchi sovrapposti sono stati rimossi dall'area di ritaglio.

Nella pagina **operazioni di ritaglio** viene illustrata la differenza tra queste due operazioni con solo una coppia di cerchi. Il primo cerchio a sinistra viene aggiunto all'area di ritaglio con l'operazione di ritaglio predefinita `Intersect` , mentre il secondo cerchio a destra viene aggiunto all'area di ritaglio con l'operazione di ritaglio indicata dall'etichetta di testo:

[![Schermata tripla della pagina operazioni di ritaglio](clipping-images//clipoperations-small.png)](clipping-images/clipoperations-large.png#lightbox)

La [`ClipOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) classe definisce due `SKPaint` oggetti come campi e quindi divide lo schermo in due aree rettangolari. Queste aree sono diverse a seconda che il telefono sia in modalità verticale o orizzontale. La `DisplayClipOp` classe Visualizza quindi il testo e chiama `ClipPath` con i due percorsi del cerchio per illustrare ogni operazione di ritaglio:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;

    foreach (SKClipOperation clipOp in Enum.GetValues(typeof(SKClipOperation)))
    {
        // Portrait mode
        if (info.Height > info.Width)
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width, y + info.Height / 2), clipOp);
            y += info.Height / 2;
        }
        // Landscape mode
        else
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width / 2, y + info.Height), clipOp);
            x += info.Width / 2;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKClipOperation clipOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(clipOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    canvas.Save();

    using (SKPath path1 = new SKPath())
    {
        path1.AddCircle(xCenter - radius / 2, yCenter, radius);
        canvas.ClipPath(path1);

        using (SKPath path2 = new SKPath())
        {
            path2.AddCircle(xCenter + radius / 2, yCenter, radius);
            canvas.ClipPath(path2, clipOp);

            canvas.DrawPaint(fillPaint);
        }
    }

    canvas.Restore();
}
```

`DrawPaint`La chiamata in genere fa sì che l'intera area di disegno venga riempita con tale `SKPaint` oggetto, ma in questo caso il metodo dipinge solo nell'area di ridimensionamento.

## <a name="exploring-regions"></a>Esplorazione delle aree

È anche possibile definire un'area di ridimensionamento in termini di un [`SKRegion`](xref:SkiaSharp.SKRegion) oggetto.

Un oggetto appena creato `SKRegion` descrive un'area vuota. In genere, la prima chiamata all'oggetto è in [`SetRect`](xref:SkiaSharp.SKRegion.SetRect(SkiaSharp.SKRectI)) modo che l'area Descriva un'area rettangolare. Il parametro per `SetRect` è un `SKRectI` valore di &mdash; un rettangolo con coordinate integer perché specifica il rettangolo in termini di pixel. È quindi possibile chiamare [`SetPath`](xref:SkiaSharp.SKRegion.SetPath(SkiaSharp.SKPath,SkiaSharp.SKRegion)) con un `SKPath` oggetto. In questo modo viene creata un'area che corrisponde all'interno del tracciato, ma ritagliata nell'area rettangolare iniziale.

L'area può essere modificata anche chiamando uno degli overload del [`Op`](xref:SkiaSharp.SKRegion.Op*) metodo, ad esempio:

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

L' [`SKRegionOperation`](xref:SkiaSharp.SKRegionOperation) enumerazione è simile a `SKClipOperation` ma dispone di più membri:

- `Difference`

- `Intersect`

- `Union`

- `XOR`

- `ReverseDifference`

- `Replace`

L'area in cui si sta effettuando la `Op` chiamata viene combinata con l'area specificata come parametro in base al `SKRegionOperation` membro. Quando si ottiene infine un'area adatta per il ritaglio, è possibile impostarla come area di ridimensionamento dell'area di disegno usando il [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion(SkiaSharp.SKRegion,SkiaSharp.SKClipOperation)) metodo di `SKCanvas` :

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

Lo screenshot seguente mostra le aree di ritaglio basate sulle sei operazioni di area. Il cerchio sinistro è l'area in cui `Op` viene chiamato il metodo e il cerchio destro è l'area passata al `Op` Metodo:

[![Schermata tripla della pagina operazioni area](clipping-images//regionoperations-small.png)](clipping-images/regionoperations-large.png#lightbox)

Queste sono tutte le possibilità di combinare questi due cerchi? Si consideri l'immagine risultante come una combinazione di tre componenti, che da soli sono visualizzati `Difference` nelle `Intersect` operazioni, e `ReverseDifference` . Il numero totale di combinazioni è due alla terza potenza, ovvero otto. I due mancanti sono l'area originale (che risulta non chiamante `Op` ) e un'area completamente vuota.

È più difficile usare le aree per il ritaglio perché è necessario creare prima un percorso, quindi un'area da tale percorso e quindi combinare più aree. La struttura complessiva della pagina **operazioni area** è molto simile alle **operazioni di ritaglio** , ma la [`RegionOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) classe divide lo schermo in sei aree e Mostra il lavoro aggiuntivo necessario per usare le aree per questo processo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;
    float width = info.Height > info.Width ? info.Width / 2 : info.Width / 3;
    float height = info.Height > info.Width ? info.Height / 3 : info.Height / 2;

    foreach (SKRegionOperation regionOp in Enum.GetValues(typeof(SKRegionOperation)))
    {
        DisplayClipOp(canvas, new SKRect(x, y, x + width, y + height), regionOp);

        if ((x += width) >= info.Width)
        {
            x = 0;
            y += height;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKRegionOperation regionOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(regionOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    SKRectI recti = new SKRectI((int)rect.Left, (int)rect.Top,
                                (int)rect.Right, (int)rect.Bottom);

    using (SKRegion wholeRectRegion = new SKRegion())
    {
        wholeRectRegion.SetRect(recti);

        using (SKRegion region1 = new SKRegion(wholeRectRegion))
        using (SKRegion region2 = new SKRegion(wholeRectRegion))
        {
            using (SKPath path1 = new SKPath())
            {
                path1.AddCircle(xCenter - radius / 2, yCenter, radius);
                region1.SetPath(path1);
            }

            using (SKPath path2 = new SKPath())
            {
                path2.AddCircle(xCenter + radius / 2, yCenter, radius);
                region2.SetPath(path2);
            }

            region1.Op(region2, regionOp);

            canvas.Save();
            canvas.ClipRegion(region1);
            canvas.DrawPaint(fillPaint);
            canvas.Restore();
        }
    }
}
```

Ecco una grande differenza tra il `ClipPath` metodo e il `ClipRegion` Metodo:

> [!IMPORTANT]
> A differenza del `ClipPath` metodo, il `ClipRegion` metodo non è influenzato dalle trasformazioni.

Per comprendere la logica di questa differenza, è utile comprendere l'aspetto di un'area. Se si è pensato in che modo le operazioni di clip o area possono essere implementate internamente, è probabile che sia molto complicato. Molti percorsi potenzialmente complessi vengono combinati e la struttura del percorso risultante è probabilmente un incubo algoritmico.

Questo processo viene notevolmente semplificato se ogni percorso viene ridotto a una serie di linee di analisi orizzontali, ad esempio quelle nei televisori con tubi vuoti obsoleti. Ogni riga di analisi è semplicemente una linea orizzontale con un punto iniziale e un punto finale. Un cerchio con un raggio di 10 pixel, ad esempio, può essere suddiviso in 20 linee di analisi orizzontali, ognuna delle quali inizia nella parte sinistra del cerchio e termina nella parte destra. La combinazione di due cerchi con qualsiasi operazione di area diventa molto semplice perché è semplicemente una cosa che consiste nell'esaminare le coordinate di inizio e fine di ogni coppia di righe di analisi corrispondenti.

Si tratta di una regione: una serie di linee di analisi orizzontali che definiscono un'area.

Tuttavia, quando un'area viene ridotta a una serie di righe di analisi, queste righe di analisi sono basate su una determinata dimensione in pixel. In modo esplicito, l'area non è un oggetto grafico vettoriale. È più vicino alla natura a una bitmap compresso monocromatica rispetto a un percorso. Di conseguenza, le aree non possono essere ridimensionate o ruotate senza perdere la fedeltà e per questo motivo non vengono trasformate quando vengono utilizzate per le aree di ritaglio.

Tuttavia, è possibile applicare le trasformazioni alle aree a scopo di disegno. Il programma **Paint Region** Mostra vividamente la natura interna delle aree. La [`RegionPaintPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) classe crea un `SKRegion` oggetto in base a un `SKPath` cerchio di un raggio di 10 unità. Una trasformazione espande quindi il cerchio per riempire la pagina:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int radius = 10;

    // Create circular path
    using (SKPath circlePath = new SKPath())
    {
        circlePath.AddCircle(0, 0, radius);

        // Create circular region
        using (SKRegion circleRegion = new SKRegion())
        {
            circleRegion.SetRect(new SKRectI(-radius, -radius, radius, radius));
            circleRegion.SetPath(circlePath);

            // Set transform to move it to center and scale up
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(Math.Min(info.Width / 2, info.Height / 2) / radius);

            // Fill region
            using (SKPaint fillPaint = new SKPaint())
            {
                fillPaint.Style = SKPaintStyle.Fill;
                fillPaint.Color = SKColors.Orange;

                canvas.DrawRegion(circleRegion, fillPaint);
            }

            // Stroke path for comparison
            using (SKPaint strokePaint = new SKPaint())
            {
                strokePaint.Style = SKPaintStyle.Stroke;
                strokePaint.Color = SKColors.Blue;
                strokePaint.StrokeWidth = 0.1f;

                canvas.DrawPath(circlePath, strokePaint);
            }
        }
    }
}
```

La `DrawRegion` chiamata riempie l'area in arancione, mentre la `DrawPath` chiamata traccia il percorso originale in blu per il confronto:

[![Schermata tripla della pagina Paint area](clipping-images//regionpaint-small.png)](clipping-images/regionpaint-large.png#lightbox)

L'area è chiaramente una serie di coordinate discrete.

Se non è necessario usare le trasformazioni in connessione con le aree di ritaglio, è possibile usare le aree per il ritaglio, come illustrato nella pagina di **trifoglio a quattro foglie** . La [`FourLeafCloverPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) classe crea un'area composta da quattro aree circolari, imposta l'area composita come area di ridimensionamento, quindi disegna una serie di 360 linee rette che emanano dal centro della pagina:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float xCenter = info.Width / 2;
    float yCenter = info.Height / 2;
    float radius = 0.24f * Math.Min(info.Width, info.Height);

    using (SKRegion wholeScreenRegion = new SKRegion())
    {
        wholeScreenRegion.SetRect(new SKRectI(0, 0, info.Width, info.Height));

        using (SKRegion leftRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion rightRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion topRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion bottomRegion = new SKRegion(wholeScreenRegion))
        {
            using (SKPath circlePath = new SKPath())
            {
                // Make basic circle path
                circlePath.AddCircle(xCenter, yCenter, radius);

                // Left leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, 0));
                leftRegion.SetPath(circlePath);

                // Right leaf
                circlePath.Transform(SKMatrix.MakeTranslation(2 * radius, 0));
                rightRegion.SetPath(circlePath);

                // Make union of right with left
                leftRegion.Op(rightRegion, SKRegionOperation.Union);

                // Top leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, -radius));
                topRegion.SetPath(circlePath);

                // Combine with bottom leaf
                circlePath.Transform(SKMatrix.MakeTranslation(0, 2 * radius));
                bottomRegion.SetPath(circlePath);

                // Make union of top with bottom
                bottomRegion.Op(topRegion, SKRegionOperation.Union);

                // Exclusive-OR left and right with top and bottom
                leftRegion.Op(bottomRegion, SKRegionOperation.XOR);

                // Set that as clip region
                canvas.ClipRegion(leftRegion);

                // Set transform for drawing lines from center
                canvas.Translate(xCenter, yCenter);

                // Draw 360 lines
                for (double angle = 0; angle < 360; angle++)
                {
                    float x = 2 * radius * (float)Math.Cos(Math.PI * angle / 180);
                    float y = 2 * radius * (float)Math.Sin(Math.PI * angle / 180);

                    using (SKPaint strokePaint = new SKPaint())
                    {
                        strokePaint.Color = SKColors.Green;
                        strokePaint.StrokeWidth = 2;

                        canvas.DrawLine(0, 0, x, y, strokePaint);
                    }
                }
            }
        }
    }
}
```

Non ha un aspetto simile a un trifoglio a quattro foglie, ma è un'immagine che altrimenti potrebbe essere difficile eseguirne il rendering senza ritaglio:

[![Schermata tripla della pagina del trifoglio a quattro foglie](clipping-images//fourleafclover-small.png)](clipping-images/fourleafclover-large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
