---
title: Ritaglio con tracciati e aree
description: Questo articolo illustra come usare i percorsi di SkiaSharp della grafica di ritaglio per aree specifiche e per creare aree e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 87f1ad3956bdb43c82a7ab57ea9171e9a28dd558
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "39615288"
---
# <a name="clipping-with-paths-and-regions"></a>Ritaglio con tracciati e aree

_Usare i percorsi della grafica di ritaglio per aree specifiche e per creare aree_

In alcuni casi è necessario limitare il rendering della grafica per una determinata area. Questo è noto come *ritaglio*. È possibile usare il ritaglio per gli effetti speciali, ad esempio l'immagine di un meccanico visualizzato attraverso un microchirurgia:

![](clipping-images/clippingsample.png "Monkey tramite un microchirurgia")

Il *area di ritaglio* è l'area dello schermo in cui viene eseguito il rendering di grafica. Tutto ciò che viene visualizzato all'esterno dell'area di ritaglio non viene eseguito il rendering. L'area di ritaglio viene in genere definito da un rettangolo o un' [ `SKPath` ](xref:SkiaSharp.SKPath) oggetto, ma in alternativa possibile definire un'area di ritaglio utilizzando un' [ `SKRegion` ](xref:SkiaSharp.SKRegion) oggetto. Questi due tipi di oggetti con sembrino correlati poiché è possibile creare un'area da un percorso. Tuttavia, non è possibile creare un percorso da un'area e sono molto diversi internamente: un percorso include una serie di linee e curve, mentre una regione è definita da una serie di linee orizzontali analisi.

L'immagine precedente è stato creato per il **Monkey tramite microchirurgia** pagina. Il [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) classe definisce un percorso usando i dati SVG e utilizza il costruttore per caricare una bitmap da risorse del programma:

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

Sebbene il `keyholePath` oggetto descrive la struttura di un microchirurgia, le coordinate sono completamente arbitrari e riflettano ciò che è stato utile quando i dati del percorso è stati concepiti. Per questo motivo, il `PaintSurface` gestore ottiene i limiti di questo percorso e le chiamate `Translate` e `Scale` per spostare il percorso al centro dello schermo e per renderlo quasi la stessa altezza schermo:


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

Tuttavia, il percorso non viene eseguito il rendering. Al contrario, dopo le trasformazioni, il percorso viene usato per impostare un'area di visualizzazione con l'istruzione seguente:

```csharp
canvas.ClipPath(keyholePath);
```

Il `PaintSurface` gestore viene quindi reimpostato le trasformazioni con una chiamata a `ResetMatrix` e consente di disegnare la bitmap per estendere all'altezza dello schermo. Questo codice presuppone che la bitmap è quadrata, ovvero questa bitmap particolare. La bitmap viene eseguito il rendering solo all'interno dell'area definita dal tracciato di ritaglio:

[![](clipping-images/monkeythroughkeyhole-small.png "Schermata triplo del Monkey tramite pagina microchirurgia")](clipping-images/monkeythroughkeyhole-large.png#lightbox "tripla schermata del Monkey tramite pagina microchirurgia")

Tracciato di ritaglio è soggetto alle trasformazioni attiva quando il `ClipPath` viene chiamato il metodo e non per le trasformazioni in vigore quando un oggetto grafico (ad esempio, una bitmap) viene visualizzato. Tracciato di ritaglio fa parte dello stato, area di disegno che viene salvato con il `Save` metodo e ripristinato con il `Restore` (metodo).

## <a name="combining-clipping-paths"></a>Combinando i tracciati di ritaglio

In teoria, l'area di ritaglio non "impostazione" la `ClipPath` (metodo). Al contrario, viene combinato con il tracciato di ritaglio esistenti, che inizia come un rettangolo di dimensioni pari all'area di disegno. È possibile ottenere i limiti rettangolari dell'area di ritaglio utilizzando il [ `ClipBounds` ](xref:SkiaSharp.SKCanvas.ClipBounds) proprietà o il [ `ClipDeviceBounds` ](xref:SkiaSharp.SKCanvas.ClipDeviceBounds) proprietà. Il `ClipBounds` proprietà restituisce un `SKRect` valore che riflette eventuali trasformazioni che potrebbe essere in vigore. Il `ClipDeviceBounds` proprietà restituisce un `RectI` valore. Questo è un rettangolo con dimensioni di integer e descrive l'area di ritaglio in dimensioni effettive in pixel.

Tutte le chiamate a `ClipPath` consente di ridurre l'area di ritaglio combinando l'area di ritaglio con una nuova area. La sintassi completa del [ `ClipPath` ](xref:SkiaSharp.SKCanvas.ClipPath(SkiaSharp.SKPath,SkiaSharp.SKClipOperation,System.Boolean)) metodo è:

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

È inoltre disponibile un' [ `ClipRect` ](xref:SkiaSharp.SKCanvas.ClipRect(SkiaSharp.SKRect,SkiaSharp.SKClipOperation,System.Boolean)) metodo che combina l'area di ritaglio con un rettangolo:

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Per impostazione predefinita, l'area di ritaglio risultante sia un'intersezione tra l'area di ritaglio esistente e il `SKPath` oppure `SKRect` specificato nel `ClipPath` o `ClipRect` (metodo). Ciò è dimostrato nel **quattro cerchi si intersecano Clip** pagina. Il `PaintSurface` gestore nel [ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) classe riusa lo stesso `SKPath` oggetto da creare quattro cerchi sovrapposti, ognuno dei quali consente di ridurre l'area di ritaglio le chiamate successive alla `ClipPath`:

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

Ciò che rimane è l'intersezione di questi quattro cerchi:

[![](clipping-images//fourcircleintersectclip-small.png "Tripla screenshot della pagina di quattro cerchio si intersecano Clip")](clipping-images/fourcircleintersectclip-large.png#lightbox "tripla screenshot della pagina di quattro cerchio si intersecano Clip")

Il [ `SKClipOperation` ](xref:SkiaSharp.SKClipOperation) enumerazione ha solo due membri:

- `Difference` Rimuove il percorso specificato o il rettangolo dall'area di ritaglio esistente

- `Intersect` interseca il percorso specificato o il rettangolo con l'area di ritaglio esistente

Se si sostituisce le quattro `SKClipOperation.Intersect` argomenti in di `FourCircleIntersectClipPage` classe `SKClipOperation.Difference`, si noterà quanto segue:

[![](clipping-images//fourcircledifferenceclip-small.png "Tripla screenshot della pagina di quattro cerchio si intersecano Clip con l'operazione di differenza")](clipping-images/fourcircledifferenceclip-large.png#lightbox "tripla screenshot della pagina di quattro cerchio si intersecano Clip con rilevamento delle differenze")

Quattro cerchi sovrapposti sono stati rimossi dall'area di ritaglio.

Il **operazioni di ritaglio** pagina viene illustrata la differenza tra queste due operazioni con solo una coppia di cerchi. Il primo controllo circle a sinistra viene aggiunto all'area di ritaglio con l'operazione di ritaglio predefinita di `Intersect`, mentre il secondo cerchio sul lato destro viene aggiunto all'area di ritaglio con l'operazione di ritaglio indicata dall'etichetta di testo:

[![](clipping-images//clipoperations-small.png "Tripla screenshot della pagina delle operazioni di ritaglio")](clipping-images/clipoperations-large.png#lightbox "tripla screenshot della pagina delle operazioni di ritaglio")

Il [ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) classe vengono definiti due `SKPaint` oggetti come campi e quindi divide la schermata di in due aree rettangolari. Queste aree sono diverse a seconda del fatto che il telefono si trova in modalità verticale o orizzontale. Il `DisplayClipOp` classe visualizza il testo e chiamate `ClipPath` con i percorsi di due cerchio per illustrare ogni operazione di ritaglio:

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

La chiamata `DrawPaint` normalmente fa sì che l'intera area di disegno deve essere compilato con cui `SKPaint` oggetto, ma in questo caso, il metodo appena disegna all'interno dell'area di ritaglio.

## <a name="exploring-regions"></a>Esplorazione di aree

È anche possibile definire un'area di ritaglio in termini di un' [ `SKRegion` ](xref:SkiaSharp.SKRegion) oggetto.

Un oggetto appena creato `SKRegion` oggetto descrive un'area vuota. La prima chiamata per l'oggetto è in genere [ `SetRect` ](xref:SkiaSharp.SKRegion.SetRect(SkiaSharp.SKRectI)) in modo che l'area descrive un'area rettangolare. Il parametro `SetRect` è un `SKRectI` valore &mdash; un rettangolo con integer coordina perché specifica il rettangolo in termini di pixel. È quindi possibile chiamare [ `SetPath` ](xref:SkiaSharp.SKRegion.SetPath(SkiaSharp.SKPath,SkiaSharp.SKRegion)) con un `SKPath` oggetto. In questo modo viene creata un'area in cui è quello utilizzato per l'interno del percorso, ma ritagliata in base all'area rettangolare iniziale.

L'area può essere modificata anche chiamando uno dei [ `Op` ](xref:SkiaSharp.SKRegion.Op*) overload del metodo come questo:

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

Il [ `SKRegionOperation` ](xref:SkiaSharp.SKRegionOperation) enumerazione è simile a `SKClipOperation` ma ha altri membri:

- `Difference`

- `Intersect`

- `Union`

- `XOR`

- `ReverseDifference`

- `Replace`

L'area in cui si sta apportando le `Op` chiamata nella viene combinato con l'area specificata come un parametro basato sul `SKRegionOperation` membro. Quando si infine Ottiene un'area appropriata per il ritaglio, è possibile impostare che, all'area di ritaglio dell'area di disegno usando il [ `ClipRegion` ](xref:SkiaSharp.SKCanvas.ClipRegion(SkiaSharp.SKRegion,SkiaSharp.SKClipOperation)) metodo `SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

Lo screenshot seguente mostra le aree di ritaglio basate sulle operazioni sei area. Il cerchio a sinistra è l'area che il `Op` metodo viene chiamato su, e il controllo circle a destra è l'area passato al `Op` metodo:

[![](clipping-images//regionoperations-small.png "Tripla screenshot della pagina operazioni sulle aree")](clipping-images/regionoperations-large.png#lightbox "tripla screenshot della pagina operazioni sulle aree")

Sono tali tutte le possibilità di combinazione di queste due cerchi? Prendere in considerazione l'immagine risultante come una combinazione di tre componenti, che da sole è visibili nel `Difference`, `Intersect`, e `ReverseDifference` operazioni. Il numero totale di combinazioni è 2 alla potenza di 3, oppure a otto. Le due che non sono presenti sono nell'area originale (risultante dalla chiamata non `Op` affatto) e un'area vuota interamente.

È molto difficile per usare aree per il ritaglio perché è necessario creare innanzitutto un percorso, quindi selezionare un'area da quel percorso e quindi combinare più aree. La struttura generale del **operazioni sulle aree** è molto simile alla pagina **Clip operazioni** ma la [ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) classe divide la schermata di in sei aree e Mostra il lavoro aggiuntivo necessario per usare aree per questo processo:

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

Ecco una grande differenza tra il `ClipPath` metodo e il `ClipRegion` metodo:

> [!IMPORTANT]
> A differenza di `ClipPath` metodo, il `ClipRegion` (metodo) non è influenzato trasformazioni.

Per comprendere la logica alla base di questa differenza, è utile comprendere quali un'area. Se è stato pensato come le operazioni di ritaglio o area può essere implementata internamente, probabilmente sembra molto complicata. Combinate diversi percorsi potenzialmente molto complessi e la struttura del percorso risultante è probabilmente un incubo algoritmico.

Questo processo viene semplificato notevolmente se ogni percorso è ridotto a una serie di linee orizzontali analisi, ad esempio quelli nel vecchio vuoto tube TV. Ogni linea di digitalizzazione è semplicemente una riga orizzontale con un punto di partenza e un punto finale. Ad esempio, è possibile scomporre un cerchio con raggio pari a 10 pixel in 20 scansione orizzontale le righe, ognuna delle quali inizia la parte sinistra del cerchio e termina in corrispondenza la parte destra. La combinazione di due cerchi con operazioni area diventa estremamente semplice perché è sufficiente esaminare le coordinate di inizio e fine di ogni coppia corrispondente di linee di digitalizzazione.

Questo è ciò che è un'area: una serie di linee di scansione orizzontali che definiscono un'area.

Tuttavia, quando un'area viene ridotta a una serie di analisi le righe, queste analisi le righe sono basate su una dimensione particolare pixel. In teoria, l'area non è un oggetto di grafica vettoriale. È più da vicino natura in una bitmap monocromatica compressa rispetto a un percorso. Di conseguenza, le aree non possono essere scalate o ruotate senza perdere in fedeltà e per questo motivo che non vengono trasformate quando viene utilizzato per le aree di ritaglio.

Tuttavia, è possibile applicare trasformazioni a aree ai fini di disegno. Il **area di disegno** programma illustra vivido la natura interna delle aree. Il [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) classe crea un `SKRegion` oggetto basato su un `SKPath` di un cerchio di 10 unità radius. Una trasformazione espande quindi tale cerchio per riempire la pagina:

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

Il `DrawRegion` chiamata riempie l'area in arancione, mentre il `DrawPath` chiamata i tratti del percorso originale in blu per il confronto:

[![](clipping-images//regionpaint-small.png "Tripla screenshot della pagina area Paint")](clipping-images/regionpaint-large.png#lightbox "tripla screenshot della pagina di disegno di area")

L'area è chiaramente una serie di coordinate discrete.

Se non è necessario usare le trasformazioni in relazione alle proprie aree di visualizzazione, è possibile usare aree per il ritaglio, come le **sono ora disponibili – quattro** dimostra di pagina. Il [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) classe costruisce un'area composta da quattro aree circolari, imposta tale area composito come area di visualizzazione e quindi disegna una serie di linee rette 360 che derivano dal centro della pagina:

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

Davvero non sembra un – quattro sono ora disponibili, ma è un'immagine che altrimenti sarebbero difficile da eseguire il rendering senza ritaglio:

[![](clipping-images//fourleafclover-small.png "Tripla screenshot della pagina sono ora disponibili – quattro")](clipping-images/fourleafclover-large.png#lightbox "tripla screenshot della pagina sono ora disponibili – quattro")


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
