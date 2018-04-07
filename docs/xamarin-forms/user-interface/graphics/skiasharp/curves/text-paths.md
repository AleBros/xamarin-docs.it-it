---
title: I percorsi e testo
description: Esplorare l'intersezione di percorsi e testo
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 08/01/2017
ms.openlocfilehash: 9b3f906a23ed0d51237a244f3944104acc76e259
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="paths-and-text"></a>I percorsi e testo

_Esplorare l'intersezione di percorsi e testo_

Nei sistemi grafico moderno, caratteri di testo sono insiemi di strutture di un carattere, in genere definite da curve di Bézier quadratiche. Di conseguenza, numerosi sistemi grafico moderno includono una funzionalità per convertire i caratteri di testo in un percorso grafico. 

Abbiamo già visto che è possibile tracciare i contorni dei caratteri del testo nonché compilare i campi. In questo modo è possibile visualizzare queste strutture di un carattere con un valore specifico e un effetto di percorso, come descritto nel [ **effetti percorso** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) articolo. Ma è anche possibile convertire una stringa di caratteri in un `SKPath` oggetto. Ciò significa profili di testo possono essere utilizzati per il ritaglio con tecniche che sono stati descritti nel [ **ritaglio e percorsi** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md) articolo. 

Oltre a utilizzare un effetto di percorso per tracciare una struttura di tipo carattere, è inoltre possibile creare stringhe di caratteri derivano gli effetti che si basano sui percorsi di un percorso ed è anche possibile combinare questi due effetti:

![](text-paths-images/pathsandtextsample.png "Effetto di testo percorso")

Nel [articolo precedente](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) si è visto come [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/) metodo `SKPaint` può ottenere una struttura di un percorso tracciato. È inoltre possibile utilizzare questo metodo con percorsi derivati da strutture di un carattere.

Infine, in questo articolo viene illustrato un altro punto di intersezione tra i percorsi e di testo: il [ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/) metodo `SKCanvas` consente di visualizzare una stringa di testo in modo che la linea di base del testo segue un percorso curvo.

## <a name="text-to-path-conversion"></a>Testo per la conversione di percorso

Il [ `GetTextPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetTextPath/p/System.String/System.Single/System.Single/) metodo `SKPaint` converte una stringa di caratteri in un `SKPath` oggetto:

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

Il `x` e `y` argomenti indicano il punto iniziale della linea di base del lato sinistro del testo. Vengono riprodotti il ruolo stesso come nel `DrawText` metodo `SKCanvas`. All'interno del percorso, la linea di base del lato sinistro del testo avrà le coordinate (x, y). 

Il `GetTextPath` metodo è inutile se si desidera semplicemente compilare o tracciare il percorso risultante. La normale `DrawText` metodo consente di eseguire tale operazione. Il `GetTextPath` metodo è più utile per le altre attività relative ai percorsi.

Una di queste attività è ritaglio. Il **il ritaglio di testo** pagina Crea un tracciato di ritaglio sulla base delle strutture di carattere della parola "CODE". Questo percorso viene estesa per le dimensioni della pagina per una bitmap che contiene un'immagine di ritagliare il **ritaglio testo** codice sorgente:

[![](text-paths-images/clippingtext-small.png "Schermata triplo della pagina di testo di ritaglio")](text-paths-images/clippingtext-large.png#lightbox "tripla schermata della pagina di testo di ritaglio")

Il [ `ClippingTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) costruttore della classe carica la bitmap che verrà archiviata come una risorsa incorporata nel **Media** cartella della soluzione:

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }
    ...
}
```

Il `PaintSurface` gestore inizia creando un `SKPaint` oggetto adatto per il testo. Il `Typeface` proprietà è impostata, nonché `TextSize`, anche se per questa determinata applicazione il `TextSize` proprietà è puramente arbirtrary. Si noti inoltre è presente alcun `Style` impostazione.

Il `TextSize` e `Style` le impostazioni delle proprietà non sono necessarie perché questo `SKPaint` oggetto viene utilizzato esclusivamente per il `GetTextPath` chiamate usando la stringa di testo "CODE". Il gestore misura quindi i risultanti `SKPath` dell'oggetto e applica le tre trasformazioni per centro e applicarvi la scalabilità per le dimensioni della pagina. Quindi è possibile impostare il percorso come tracciato di ritaglio:

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

Dopo aver impostato il tracciato di ritaglio, la mappa di bit può essere visualizzato e verrà ritagliato per i profili di carattere. Si noti l'uso del [ `AspectFill` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRect.AspectFill/p/SkiaSharp.SKSize/) metodo `SKRect` che calcola un rettangolo per riempire la pagina mantenendo le proporzioni.

Il **effetto di testo percorso** pagina converte un carattere e commerciale singola in un percorso per creare un effetto di percorso 1 D. Un oggetto di disegno con effetto in questo percorso viene quindi utilizzato per tracciare la struttura di una versione più grande di tale carattere stesso:

[![](text-paths-images/textpatheffect-small.png "Schermata triplo della pagina di testo percorso effetto")](text-paths-images/textpatheffect-large.png#lightbox "tripla schermata della pagina di testo percorso effetto")

Quantità di lavoro nel [ `TextPathEffectPath` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) classe viene generata nei campi e il costruttore. I due `SKPaint` oggetti definiti come campi sono utilizzati per scopi diversi due: il primo (denominato `textPathPaint`) viene utilizzato per convertire la e commerciale con un `TextSize` di 50 a un percorso per l'effetto di percorso 1 D. Il secondo (`textPaint`) viene utilizzato per visualizzare la versione più grande della e commerciale con effetto tale percorso. Per questo motivo, il `Style` di questo disegno secondo oggetto è impostato su `Stroke`, ma la `StrokeWidth` non è impostata in quanto tale proprietà non è necessaria quando si usa un effetto di percorso 1D:

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

Il costruttore utilizza in primo luogo il `textPathPaint` oggetto per misurare la e commerciale con un `TextSize` di 50. I segni di sottrazione delle coordinate del centro del rettangolo vengono quindi passati al `GetTextPath` metodo per convertire il testo in un percorso. Il percorso risultante è il (0, 0) punto al centro del carattere, ideale per un effetto di percorso 1D.

Si potrebbe pensare che il `SKPathEffect` oggetto creato alla fine del costruttore può essere impostata sul `PathEffect` proprietà di `textPaint` anziché essere salvato come un campo. Ma questa non attivato out per il corretto funzionamento molto perché distorta i risultati del `MeasureText` chiamare il `PaintSurface` gestore:

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

Che `MeasureText` chiamata viene utilizzata per allineare al centro il carattere nella pagina. Per evitare problemi, il `PathEffect` proprietà è impostata per l'oggetto paint dopo il testo è stato misurato ma prima che venga visualizzato.

## <a name="outlines-of-character-outlines"></a>Profili dei contorni dei caratteri

In genere il [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/) metodo `SKPaint` converte un percorso a un altro applicando proprietà paint, in particolare l'effetto di tratto larghezza e il percorso. Quando viene utilizzata senza effetti di percorso, `GetFillPath` crea in modo efficace un percorso che descrive un altro percorso. Questa funzionalità è stata illustrata la **toccare alla struttura, il percorso** nella pagina di [ **effetti percorso** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) articolo.

È inoltre possibile chiamare `GetFillPath` nel percorso restituito da `GetTextPath` ma inizialmente potrebbe non essere completamente verificare quali che sarebbe.

Il **carattere struttura contorni** illustra come la tecnica. Tutto il codice pertinente è la `PaintSurface` gestore del [ `CharacterOutlineOutlinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) classe.

Il costruttore inizia creando un `SKPaint` oggetto denominato `textPaint` con un `TextSize` proprietà in base alla dimensione della pagina. Questo viene convertito in un percorso utilizzando il `GetTextPath` metodo. Gli argomenti da coordinate `GetTextPath` center in modo efficace il percorso nella schermata:

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

Il `PaintSurface` gestore crea quindi un nuovo percorso denominato `outlinePath`. Questo valore diventa il percorso di destinazione nella chiamata a `GetFillPath`. Il `StrokeWidth` proprietà 25 cause `outlinePath` per descrivere la struttura di un percorso di larghezza di 25 pixel che traccia i caratteri di testo. Questo percorso viene quindi visualizzato in rosso con un valore pari a 5:

[![](text-paths-images/characteroutlineoutlines-small.png "Tripla schermata della pagine dei contorni struttura caratteri")](text-paths-images/characteroutlineoutlines-large.png#lightbox "tripla schermata della pagine dei contorni struttura caratteri")

Prestare particolare attenzione, si noterà sovrapposizioni in cui la struttura del percorso rende un angolo acuto. Questi valori sono elementi normale di questo processo.

## <a name="text-along-a-path"></a>Testo lungo un percorso

In genere visualizzato su una linea di base orizzontale. Testo può essere ruotato per l'esecuzione in verticale o diagonale, ma la linea di base è ancora una linea retta.

Esistono, tuttavia, si vogliano testo per l'esecuzione di una curva. È questo lo scopo del [ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/) metodo `SKCanvas`:

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

Il testo specificato nel primo argomento viene effettuato per eseguire lungo il percorso specificato come secondo argomento. È possibile iniziare il testo in un offset dall'inizio del percorso con il `hOffset` argomento. In genere il percorso del form la linea di base del testo: i tratti ascendenti testo sono su un lato del percorso e i discendenti di testo sono su altro. Ma è possibile compensare la linea di base di testo dal percorso con il `vOffset` argomento.

Questo metodo non dispone di funzionalità per fornire informazioni aggiuntive sull'impostazione di `TextSize` proprietà `SKPaint` per rendere il testo ridimensionato perfettamente eseguito dall'inizio del percorso fino alla fine. In alcuni casi è possibile determinare la dimensione del testo per conto proprio. In altri casi è necessario utilizzare le funzioni di misurazione di percorso per descrivere in un altro articolo.

Il **testo circolare** programma wrapping testo di un cerchio. È facile determinare la circonferenza del cerchio, pertanto è facile ridimensionare il testo per esigenze di adattamento. Il `PaintSurface` gestore del [ `CircularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) classe calcola un raggio di un cerchio in base alla dimensione della pagina. Tale cerchio diventa `circularPath`:

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

Il `TextSize` proprietà `textPaint` quindi viene regolato in modo che la larghezza corrisponda la circonferenza del cerchio:

[![](text-paths-images/circulartext-small.png "Schermata triplo della pagina di testo circolare")](text-paths-images/circulartext-large.png#lightbox "tripla schermata della pagina di testo circolare")

Il testo è stata scelta per essere in qualche modo circolare: la parola "circle" è l'oggetto della frase sia l'oggetto di una frase preposizionali. 

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
