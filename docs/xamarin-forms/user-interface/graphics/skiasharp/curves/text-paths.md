---
title: Tracciati e testo in SkiaSharp
description: Questo articolo illustra l'intersezione dei tracciati in SkiaSharp e testo e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2017
ms.openlocfilehash: d38391f3fd0f02dda8bfd92fce650c557bda0153
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645220"
---
# <a name="paths-and-text-in-skiasharp"></a>Tracciati e testo in SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Esplorare l'intersezione dei tracciati e testo_

Nei sistemi di grafica moderno, i tipi di carattere di testo sono raccolte di contorni dei caratteri, in genere definiti tramite le curve di Bézier quadratiche. Di conseguenza, molti sistemi di grafica moderni includono una funzionalità per convertire i caratteri di testo in un percorso grafico.

Abbiamo già visto che è possibile tracciare i contorni dei caratteri del testo, nonché inserire in questi elementi. In questo modo è possibile visualizzare i contorni dei caratteri con una larghezza specifica tratto e anche un effetto di percorso come descritto nel [ **effetti per il tracciato** ](effects.md) articolo. Ma è anche possibile convertire una stringa di caratteri in un `SKPath` oggetto. Ciò significa che i contorni del testo sono utilizzabile per il ritaglio con tecniche che sono stati descritti nel [ **ritaglio con tracciati e aree** ](clipping.md) articolo.

Oltre a utilizzare un effetto di percorso per tracciare una descrizione di carattere, è anche possibile creare effetti di percorso che si basano su un percorso che è derivato da una stringa di caratteri ed è anche possibile combinare le due effetti:

![](text-paths-images/pathsandtextsample.png "Effetto di testo percorso")

Nell'articolo precedente sulla [ **effetti per il tracciato**](effects.md), si è visto come il [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) metodo `SKPaint` può ottenere una struttura di un percorso tracciato. È anche possibile usare questo metodo con percorsi derivati da contorni dei caratteri.

Infine, in questo articolo viene illustrata un'altra intersezione di percorsi e testo: Il [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) metodo di `SKCanvas` consente di visualizzare una stringa di testo in modo che la linea di base del testo segua un tracciato curvo.

## <a name="text-to-path-conversion"></a>Testo per la conversione di percorso

Il [ `GetTextPath` ](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single)) metodo `SKPaint` converte una stringa di caratteri a un `SKPath` oggetto:

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

Il `x` e `y` argomenti indicano il punto iniziale della linea di base del lato sinistro del testo. Giochiamo stesso elemento role qui come il `DrawText` metodo di `SKCanvas`. All'interno del percorso, la linea di base del lato sinistro del testo avranno le coordinate (x, y).

Il `GetTextPath` metodo è un problema se si desidera semplicemente compilare o tracciare il percorso risultante. La normale `DrawText` metodo consente di eseguire questa operazione. Il `GetTextPath` metodo è più utile per le altre attività relative ai percorsi.

Una di queste attività è di ritaglio. Il **ritaglio testo** pagina consente di creare un tracciato di ritaglio in base i contorni dei caratteri della parola "CODE". Questo percorso viene adattato alle dimensioni della pagina per ritagliare un'immagine bitmap che contiene un'immagine del **ritaglio testo** codice sorgente:

[![](text-paths-images/clippingtext-small.png "Tripla screenshot della pagina di testo di ritaglio")](text-paths-images/clippingtext-large.png#lightbox "tripla screenshot della pagina di testo di ritaglio")

Il [ `ClippingTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) costruttore della classe carica la bitmap archiviata come una risorsa incorporata nel **supporti** cartella della soluzione:

```csharp
public class ClippingTextPage : ContentPage
{
    SKBitmap bitmap;

    public ClippingTextPage()
    {
        Title = "Clipping Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.PageOfCode.png";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

Il `PaintSurface` gestore inizia creando un `SKPaint` oggetto adatto per il testo. Il `Typeface` è impostata, nonché `TextSize`, anche se per questa determinata applicazione il `TextSize` proprietà è puramente arbitraria. Si noti inoltre è presente alcun `Style` impostazione.

Il `TextSize` e `Style` le impostazioni delle proprietà non sono necessari perché ciò `SKPaint` oggetto viene usato esclusivamente per il `GetTextPath` chiamare usando la stringa di testo "CODE". Il gestore misura quindi i risultanti `SKPath` dell'oggetto e applica tre trasformazioni per centro e ridimensionarlo in modo alle dimensioni della pagina. Quindi è possibile impostare il percorso come tracciato di ritaglio:

```csharp
public class ClippingTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Blue);

        using (SKPaint paint = new SKPaint())
        {
            paint.Typeface = SKTypeface.FromFamilyName(null, SKTypefaceStyle.Bold);
            paint.TextSize = 10;

            using (SKPath textPath = paint.GetTextPath("CODE", 0, 0))
            {
                // Set transform to center and enlarge clip path to window height
                SKRect bounds;
                textPath.GetTightBounds(out bounds);

                canvas.Translate(info.Width / 2, info.Height / 2);
                canvas.Scale(info.Width / bounds.Width, info.Height / bounds.Height);
                canvas.Translate(-bounds.MidX, -bounds.MidY);

                // Set the clip path
                canvas.ClipPath(textPath);
            }
        }

        // Reset transforms
        canvas.ResetMatrix();

        // Display bitmap to fill window but maintain aspect ratio
        SKRect rect = new SKRect(0, 0, info.Width, info.Height);
        canvas.DrawBitmap(bitmap,
            rect.AspectFill(new SKSize(bitmap.Width, bitmap.Height)));
    }
}
```

Dopo aver impostato il tracciato di ritaglio, la bitmap può essere visualizzata e verrà troncato ai contorni dei caratteri. Si noti l'uso del [ `AspectFill` ](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize)) metodo `SKRect` che calcola un rettangolo per il riempimento pagina mantenendo le proporzioni.

Il **effetto di testo percorso** pagina converte un carattere e commerciale singolo in un percorso per creare un effetto di percorso 1 giorno. Un oggetto paint con effetto in questo percorso viene quindi usato per disegnare il contorno di una versione più grande di tale carattere stesso:

[![](text-paths-images/textpatheffect-small.png "Tripla screenshot della pagina di testo percorso effetto")](text-paths-images/textpatheffect-large.png#lightbox "tripla screenshot della pagina di effetto di testo percorso")

Quantità di lavoro nel [ `TextPathEffectPath` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) classe viene generata nei campi e un costruttore. I due `SKPaint` oggetti definiti come campi vengono usati per due scopi diversi: Il primo (denominato `textPathPaint`) viene utilizzato per convertire la e commerciale con `TextSize` una di 50 in un percorso per l'effetto del percorso 1D. Il secondo (`textPaint`) consente di visualizzare la versione più grande di una e commerciale con effetto tale percorso. Per questo motivo, il `Style` di questo secondo vernice è impostata su `Stroke`, ma il `StrokeWidth` proprietà non è impostata perché tale proprietà non è necessaria quando si usa un effetto di percorso 1D:

```csharp
public class TextPathEffectPage : ContentPage
{
    const string character = "@";
    const float littleSize = 50;

    SKPathEffect pathEffect;

    SKPaint textPathPaint = new SKPaint
    {
        TextSize = littleSize
    };

    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black
    };

    public TextPathEffectPage()
    {
        Title = "Text Path Effect";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Get the bounds of textPathPaint
        SKRect textPathPaintBounds = new SKRect();
        textPathPaint.MeasureText(character, ref textPathPaintBounds);

        // Create textPath centered around (0, 0)
        SKPath textPath = textPathPaint.GetTextPath(character,
                                                    -textPathPaintBounds.MidX,
                                                    -textPathPaintBounds.MidY);
        // Create the path effect
        pathEffect = SKPathEffect.Create1DPath(textPath, littleSize, 0,
                                               SKPath1DPathEffectStyle.Translate);
    }
    ...
}
```

Il costruttore utilizza in primo luogo il `textPathPaint` oggetto per misurare la e commerciale con un `TextSize` pari a 50. Negativi delle coordinate di centro del rettangolo vengono quindi passati al `GetTextPath` metodo per convertire il testo in un percorso. Il percorso risultante è la (0, 0) punto al centro del carattere, ideale per un effetto di percorso 1 giorno.

Si potrebbe pensare che il `SKPathEffect` oggetto creato alla fine del costruttore può essere impostato per il `PathEffect` proprietà di `textPaint` anziché salvato come un campo. Ma questo non attivati out a funziona molto bene perché distorcere i risultati del `MeasureText` chiamare il `PaintSurface` gestore:

```csharp
public class TextPathEffectPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set textPaint TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Do not measure the text with PathEffect set!
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(character, ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Set the PathEffect property and display text
        textPaint.PathEffect = pathEffect;
        canvas.DrawText(character, xText, yText, textPaint);
    }
}
```

Che `MeasureText` chiamata viene utilizzata per allineare al centro il carattere nella pagina. Per evitare problemi, il `PathEffect` proprietà è impostata per l'oggetto paint dopo che il testo è stato misurato ma prima che venga visualizzato.

## <a name="outlines-of-character-outlines"></a>Contorni dei contorni dei caratteri

In genere il [ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) metodo `SKPaint` converte un percorso a un altro applicando proprietà paint, in particolare l'effetto di tratto larghezza e il percorso. Quando usata senza effetti per il tracciato, `GetFillPath` crea in modo efficace un percorso che descrive un altro percorso. Questa funzionalità è stata illustrata la **toccare alla struttura di percorso** nella pagina il [ **effetti per il tracciato** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) articolo.

È inoltre possibile chiamare `GetFillPath` nel percorso restituito da `GetTextPath` ma inizialmente potrebbe non essere completamente sicuri che aspetto sarebbe il seguente.

Il **contorni dei caratteri struttura** illustra come la tecnica. È tutto il codice pertinente nel `PaintSurface` gestore del [ `CharacterOutlineOutlinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) classe.

Il costruttore inizia creando un' `SKPaint` oggetto denominato `textPaint` con un `TextSize` proprietà in base alla dimensione della pagina. Questo viene convertito in un percorso usando il `GetTextPath` (metodo). Gli argomenti di coordinate da `GetTextPath` centrare in modo efficace il percorso nella schermata:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint())
    {
        // Set Style for the character outlines
        textPaint.Style = SKPaintStyle.Stroke;

        // Set TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Measure the text
        SKRect textBounds = new SKRect();
        textPaint.MeasureText("@", ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Get the path for the character outlines
        using (SKPath textPath = textPaint.GetTextPath("@", xText, yText))
        {
            // Create a new path for the outlines of the path
            using (SKPath outlinePath = new SKPath())
            {
                // Convert the path to the outlines of the stroked path
                textPaint.StrokeWidth = 25;
                textPaint.GetFillPath(textPath, outlinePath);

                // Stroke that new path
                using (SKPaint outlinePaint = new SKPaint())
                {
                    outlinePaint.Style = SKPaintStyle.Stroke;
                    outlinePaint.StrokeWidth = 5;
                    outlinePaint.Color = SKColors.Red;

                    canvas.DrawPath(outlinePath, outlinePaint);
                }
            }
        }
    }
}
```

Il `PaintSurface` gestore crea quindi un nuovo percorso denominato `outlinePath`. Questo diventa il percorso di destinazione nella chiamata a `GetFillPath`. Il `StrokeWidth` proprietà delle 25 cause `outlinePath` per descrivere la struttura di un percorso di larghezza di 25 pixel che traccia i caratteri di testo. Questo percorso viene quindi visualizzato in rosso con uno spessore pari a 5:

[![](text-paths-images/characteroutlineoutlines-small.png "Tripla screenshot della pagina dei contorni dei caratteri contorno")](text-paths-images/characteroutlineoutlines-large.png#lightbox "tripla screenshot della pagina struttura contorni dei caratteri")

Prestare particolare attenzione e vedrai sovrapposizioni in cui la struttura di percorso può risultare un angolo acuto. Questi valori sono normali elementi di questo processo.

## <a name="text-along-a-path"></a>Testo lungo un tracciato

Testo in genere viene visualizzato in una linea di base orizzontale. Testo può essere ruotato per l'esecuzione in verticale o diagonale, ma la linea di base è ancora una linea retta.

Esistono, tuttavia, si vogliano testo per eseguire una curva. Questo è lo scopo del [ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) metodo `SKCanvas`:

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

Il testo specificato nel primo argomento è tentato di eseguire lungo il percorso specificato come secondo argomento. È possibile iniziare il testo in un offset dall'inizio del percorso con il `hOffset` argomento. In genere il percorso costituisce la linea di base del testo: Il testo ascendenti si trovano su un lato del percorso e i discendenti di testo sono sull'altro. Ma è possibile compensare la linea di base di testo dal percorso con il `vOffset` argomento.

Questo metodo non dispone di funzionalità per fornire materiale sussidiario sulla configurazione di `TextSize` proprietà di `SKPaint` per rendere il testo ridimensionato perfettamente per l'esecuzione dall'inizio del percorso fino alla fine. In alcuni casi è possibile determinare tale dimensione del testo per conto proprio. In altri casi è necessario usare le funzioni di misurazione percorso a essere descritta nell'articolo successivo in [ **informazioni sul percorso e l'enumerazione**](information.md).

Il **circolare testo** programma esegue il wrapping di testo intorno a un cerchio. È facile determinare la circonferenza del cerchio, pertanto è facile ridimensionare il testo da inserire esattamente. Il `PaintSurface` gestore del [ `CircularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) classe calcola un raggio del cerchio in base alla dimensione della pagina. Il cerchio diventa `circularPath`:

```csharp
public class CircularTextPage : ContentPage
{
    const string text = "xt in a circle that shapes the te";
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circularPath = new SKPath())
        {
            float radius = 0.35f * Math.Min(info.Width, info.Height);
            circularPath.AddCircle(info.Width / 2, info.Height / 2, radius);

            using (SKPaint textPaint = new SKPaint())
            {
                textPaint.TextSize = 100;
                float textWidth = textPaint.MeasureText(text);
                textPaint.TextSize *= 2 * 3.14f * radius / textWidth;

                canvas.DrawTextOnPath(text, circularPath, 0, 0, textPaint);
            }
        }
    }
}
```

Il `TextSize` proprietà di `textPaint` viene quindi regolato in modo che la larghezza di testo corrisponde la circonferenza del cerchio:

[![](text-paths-images/circulartext-small.png "Tripla screenshot della pagina di testo circolare")](text-paths-images/circulartext-large.png#lightbox "tripla screenshot della pagina di testo circolare")

Il testo stesso è stato scelto anche come circolare: La parola "Circle" è sia l'oggetto della frase che l'oggetto di una frase preposizionale.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
