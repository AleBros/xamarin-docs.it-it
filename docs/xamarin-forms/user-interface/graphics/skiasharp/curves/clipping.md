---
title: Ritaglio e percorsi
description: Utilizzare i percorsi di grafica di ritaglio per aree specifiche e per creare aree
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: charlespetzold
ms.author: chape
ms.date: 06/16/2017
ms.openlocfilehash: bb99984f93f494cfb5ad3d37ccb25f0b91d0b489
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="clipping-with-paths-and-regions"></a>Ritaglio e percorsi

_Utilizzare i percorsi di grafica di ritaglio per aree specifiche e per creare aree_

In alcuni casi è necessario limitare il rendering di immagini a una determinata area. Questo è noto come *ritaglio*. Il ritaglio è possibile utilizzare per effetti speciali, ad esempio l'immagine di un monkey visualizzata tramite un microchirurgia:

![](clipping-images/clippingsample.png "Monkey tramite un microchirurgia")

Il *area di ritaglio* è l'area dello schermo in cui viene eseguito il rendering di grafica. Tutto ciò che viene visualizzato all'esterno dell'area di ritaglio non viene eseguito il rendering. L'area di visualizzazione viene in genere definita da un [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) oggetto ma è in alternativa possibile definire un'area di ritaglio utilizzando un [ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/) oggetto. Questi due tipi di oggetti sembrano innanzitutto correlati poiché è possibile creare un'area da un percorso. Tuttavia, non è possibile creare un percorso da un'area e internamente sono molto diversi: un percorso è costituito da una serie di linee e curve, mentre una regione è definita da una serie di linee di scansione orizzontale.

L'immagine precedente è stato creato dal **Monkey tramite microchirurgia** pagina. Il [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs) classe definisce un percorso utilizzando dati SVG e utilizza il costruttore per caricare una bitmap da risorse programma:

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }
    ...
}
```

Sebbene il `keyholePath` descrive la struttura di un microchirurgia, le coordinate sono completamente arbitrari e riflettono animale utile quando i dati del percorso è stati sviluppati. Per questo motivo, il `PaintSurface` gestore ottiene i limiti di questo percorso e le chiamate `Translate` e `Scale` per spostare il percorso al centro dello schermo e renderla quasi la stessa altezza schermo:


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

Tuttavia, il percorso non viene eseguito il rendering. Al contrario, dopo le trasformazioni, il percorso utilizzato per impostare un'area di visualizzazione con l'istruzione seguente:

```csharp
canvas.ClipPath(keyholePath);
```

Il `PaintSurface` gestore reimposta quindi le trasformazioni con una chiamata a `ResetMatrix` e disegna l'immagine bitmap per estendere all'altezza dello schermo. Questo codice si presuppone che la mappa di bit quadrato, ovvero questa bitmap particolare. La bitmap viene eseguito il rendering solo all'interno dell'area definita dal tracciato di ritaglio:

[![](clipping-images/monkeythroughkeyhole-small.png "Schermata triplo del Monkey tramite pagina microchirurgia")](clipping-images/monkeythroughkeyhole-large.png#lightbox "tripla schermata della pagina microchirurgia il Monkey")

Tracciato di ritaglio è soggetto alle trasformazioni attivo quando il `ClipPath` metodo viene chiamato e non per le trasformazioni attiva quando un oggetto grafico (ad esempio, una bitmap) viene visualizzato. Tracciato di ritaglio è parte dello stato dell'area di disegno che viene salvato con il `Save` metodo e ripristinati con il `Restore` metodo.

## <a name="combining-clipping-paths"></a>Combinare tracciati di ritaglio

In teoria, l'area di visualizzazione "impostazione" la `ClipPath` metodo. Al contrario, viene combinato con il tracciato di ritaglio esistente, inizia come un rettangolo di dimensioni pari allo schermo. È possibile ottenere i limiti dell'area di ritaglio utilizzando rettangolari di [ `ClipBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipBounds/) proprietà o [ `ClipDeviceBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipDeviceBounds/) proprietà. Il `ClipBounds` proprietà restituisce un `SKRect` valore che corrisponde a qualsiasi trasformazioni che potrebbe essere attiva. Il `ClipDeviceBounds` proprietà restituisce un `RectI` valore. Questo è un rettangolo con dimensioni di tipo integer e descrive l'area di ritaglio in dimensioni effettive in pixel.

Qualsiasi chiamata a `ClipPath` consente di ridurre l'area di visualizzazione combinando l'area di visualizzazione con una nuova area. La sintassi completa del [ `ClipPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipPath/p/SkiaSharp.SKPath/SkiaSharp.SKClipOperation/System.Boolean/) metodo:

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

È inoltre disponibile un [ `ClipRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRect/p/SkiaSharp.SKRect/SkiaSharp.SKClipOperation/System.Boolean/) (metodo) che combina l'area di visualizzazione con un rettangolo:

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

Per impostazione predefinita, l'area di visualizzazione risultante è un punto di intersezione tra l'area di visualizzazione esistente e `SKPath` o `SKRect` specificata nel `ClipPath` o `ClipRect` metodo. Questa funzionalità viene illustrata la **quattro cerchi si intersecano Clip** pagina. Il `PaintSurface` gestore il [ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) classe riutilizza la stessa `SKPath` oggetto da creare quattro cerchi sovrapposti, ognuno dei quali consente di ridurre l'area di visualizzazione tramite le chiamate successive a `ClipPath`:

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

Ciò che resta è l'intersezione di questi quattro cerchi:

[![](clipping-images//fourcircleintersectclip-small.png "Schermata triplo della pagina quattro cerchio si intersecano Clip")](clipping-images/fourcircleintersectclip-large.png#lightbox "tripla schermata della pagine dei quattro cerchio si intersecano Clip")

Il [ `SKClipOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKClipOperation/) enumerazione presenta solo due membri:

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Difference/) Rimuove il percorso specificato o un rettangolo dall'area di ritaglio esistente

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Intersect/) interseca il percorso specificato o un rettangolo con l'area di visualizzazione esistente

Se si sostituisce le quattro `SKClipOperation.Intersect` gli argomenti di `FourCircleIntersectClipPage` classe con `SKClipOperation.Difference`, si noterà quanto segue:

[![](clipping-images//fourcircledifferenceclip-small.png "Schermata triplo della pagina quattro cerchio si intersecano Clip con l'operazione di differenza")](clipping-images/fourcircledifferenceclip-large.png#lightbox "tripla schermata della pagine dei quattro cerchio si intersecano Clip con l'operazione di differenza")

Quattro cerchi sovrapposti sono state rimosse dall'area di ritaglio.

Il **operazioni di ritaglio** pagina viene illustrata la differenza tra queste due operazioni con solo una coppia di cerchi. Il primo cerchio a sinistra viene aggiunto all'area di ritaglio con l'operazione di ritaglio predefinita di `Intersect`, mentre il secondo cerchio sulla destra viene aggiunto all'area di ritaglio con l'operazione di ritaglio indicata dall'etichetta di testo:

[![](clipping-images//clipoperations-small.png "Schermata di triplo della pagina operazioni di ritaglio")](clipping-images/clipoperations-large.png#lightbox "tripla schermata della pagina operazioni di ritaglio")

Il [ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) classe definisce due `SKPaint` oggetti come campi e quindi suddivide la schermata in due aree rettangolari. Queste aree sono diverse a seconda se il telefono è in modalità verticale o orizzontale. Il `DisplayClipOp` classe visualizza il testo e le chiamate `ClipPath` con i percorsi di due cerchio per illustrare ogni operazione di ritaglio:

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

La chiamata `DrawPaint` normalmente fa sì che l'intera area di disegno da riempire con quella `SKPaint` oggetto, ma in questo caso, il metodo appena disegna all'interno dell'area di ritaglio.

## <a name="exploring-regions"></a>Esplorazione delle aree

Se la documentazione dell'API per avere esaminato `SKCanvas`, è possibile notare gli overload del `ClipPath` e `ClipRect` metodi che sono simili ai metodi descritti in precedenza, ma viene invece hanno un parametro denominato [ `SKRegionOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegionOperation/) anziché `SKClipOperation`. `SKRegionOperation` include sei membri, fornisce maggiore flessibilità per la combinazione di percorsi di aree di form il ritaglio:

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Difference/)

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Intersect/)

- [`Union`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Union/)

- [`XOR`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.XOR/)

- [`ReverseDifference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.ReverseDifference/)

- [`Replace`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Replace/)

Tuttavia, gli overload di `ClipPath` e `ClipRect` con `SKRegionOperation` i parametri sono obsoleti e non possono essere utilizzati.

È comunque possibile usare il `SKRegionOperation` enumerazione ma che è necessario definire un'area di visualizzazione in termini di un [ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/) oggetto.

Oggetto appena creato `SKRegion` descrive un'area vuota. In genere la prima chiamata per l'oggetto è [ `SetRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetRect/p/SkiaSharp.SKRectI/) in modo che l'area descrivere un'area rettangolare. Il parametro `SetRect` è un un `SKRectI` valore & #x 2014; il valore del rettangolo con le proprietà di tipo integer. È quindi possibile chiamare [ `SetPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetPath/p/SkiaSharp.SKPath/SkiaSharp.SKRegion/) con un `SKPath` oggetto. Crea un'area che corrisponde all'interno del percorso, ma l'area rettangolare iniziale troncato.

Il `SKRegionOperation` enumerazione solo entra in gioco quando si chiama uno del [ `Op` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.Op/p/SkiaSharp.SKRegion/SkiaSharp.SKRegionOperation/) , ad esempio l'overload del metodo:

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

L'area che si desidera apportare la `Op` chiamata su viene combinato con l'area specificata come un parametro basato sul `SKRegionOperation` membro. Quando si infine Ottiene un'area appropriata per il ritaglio, è possibile che come area di ritaglio dell'area di lavoro utilizzando il [ `ClipRegion` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRegion/p/SkiaSharp.SKRegion/SkiaSharp.SKClipOperation/) metodo `SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

La schermata seguente mostra in base alle operazioni sei area aree di ritaglio. Il cerchio a sinistra è l'area che il `Op` metodo viene chiamato su, e il cerchio destro è l'area passato per il `Op` metodo:

[![](clipping-images//regionoperations-small.png "Schermata triplo della pagina operazioni sulle aree")](clipping-images/regionoperations-large.png#lightbox "tripla schermata della pagina operazioni sulle aree")

Sono tali tutte le possibilità di combinare questi due cerchi? Prendere in considerazione l'immagine risultante come una combinazione di tre componenti, che da soli è visibili nel `Difference`, `Intersect`, e `ReverseDifference` operazioni. Il numero totale di combinazioni è 2 alla potenza di 3 o otto. I due mancanti sono l'area originale (risultante dalla chiamata non `Op` affatto) e un'area vuota completamente.

È più difficile da usare aree per il ritaglio perché è necessario creare innanzitutto un percorso, quindi selezionare un'area da tale percorso e quindi combinare più aree. La struttura complessiva del **area operazioni** è molto simile alla pagina **operazioni di ritaglio** ma la [ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) classe suddivide la schermata in sei aree e Mostra il lavoro aggiuntivo necessario per usare aree per questo processo:

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

Ecco una notevole differenza tra il `ClipPath` (metodo) e `ClipRegion` metodo:

> [!IMPORTANT]
> A differenza di `ClipPath` (metodo), il `ClipRegion` (metodo) non è interessata dalle trasformazioni.

Per comprendere la logica alla base di questa differenza, è utile comprendere quali un'area è. Se è pensato come le operazioni di ritaglio o area potrebbe essere implementato internamente, probabilmente sembra molto complessa. Più percorsi potenzialmente molto complessi vengono combinati e la struttura del percorso risulta è probabilmente un incubo algoritmico.

Ma questo processo viene semplificato notevolmente se ogni percorso viene ridotta a una serie di linee di scansione orizzontale, ad esempio quelli in datato vuoto tube TV. Ogni riga di analisi è semplicemente una riga orizzontale da un punto di inizio e di un punto finale. Ad esempio, un cerchio con un raggio di 10 può essere scomposta in 20 righe di scansione orizzontale, ognuno dei quali inizia la parte sinistra del cerchio e termina in corrispondenza la parte destra. La combinazione di due cerchi con qualsiasi operazione area diventa estremamente semplice perché si tratta semplicemente di esaminare le coordinate di inizio e fine di ogni coppia di righe corrispondenti di analisi.

Questo è ciò che è un'area: una serie di linee di scansione orizzontale che definisce un'area.

Tuttavia, quando un'area viene ridotto a una serie di analisi le righe, tali righe sono basate su una dimensione di un pixel specifico di analisi. In teoria, l'area non è un oggetto di grafica vettoriale. È più vicino di natura una bitmap monocromatica compressa rispetto a un percorso. Di conseguenza, non sono ridimensionate o ruotate senza perdita di fedeltà e per questo motivo che non vengono trasformate quando viene utilizzato per le aree di ritaglio aree.

Tuttavia, è possibile applicare trasformazioni alle aree per scopi di disegno. Il **area di disegno** programma illustra intensamente la natura interna delle aree. Il [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs) classe crea un `SKRegion` oggetto basato su un `SKPath` di un cerchio di 10 unità radius. Una trasformazione viene quindi espanso il cerchio per riempire la pagina:

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

Il `DrawRegion` chiamata riempie l'area di colore arancione, mentre il `DrawPath` chiamata tracce il percorso originale in blu per il confronto:

[![](clipping-images//regionpaint-small.png "Schermata triplo della pagina di disegno area")](clipping-images/regionpaint-large.png#lightbox "tripla schermata della pagina di disegno di area")

L'area è chiaramente una serie di coordinate discrete.

Se non è necessario utilizzare le trasformazioni in relazione le aree di visualizzazione, è possibile utilizzare le aree per il ritaglio, come il **quattro – foglia trifoglio** illustra come. Il [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs) classe costruisce un'area composta da quattro aree circolari, imposta tale area composito come area di ritaglio e quindi disegna una serie di 360 linee rette provenienti dall'area della pagina:

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

Davvero non sembra un trifoglio foglia – di quattro, ma è un'immagine che altrimenti sarebbero difficile da eseguire il rendering senza ritaglio:

[![](clipping-images//fourleafclover-small.png "La schermata della pagine dei quattro – foglia trifoglio")](clipping-images/fourleafclover-large.png#lightbox "tripla schermata della pagine dei quattro – foglia trifoglio")


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
