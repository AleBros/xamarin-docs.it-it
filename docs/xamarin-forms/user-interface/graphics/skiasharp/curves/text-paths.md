---
title: "paths and text in SkiaSharp" Description: "in questo articolo viene illustrata l'intersezione tra i percorsi e il testo di SkiaSharp e viene illustrato questo problema con il codice di esempio".
ms. prod: Novell MS. AssetID: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B ms. Technology: Novell-skiasharp autore: davidbritch ms. Author: dabritch ms. Date: 08/01/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="paths-and-text-in-skiasharp"></a>Percorsi e testo in SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Esplorare l'intersezione dei percorsi e del testo_

Nei sistemi grafici moderni, i tipi di carattere del testo sono raccolte di strutture di caratteri, generalmente definite dalle curve di Bézier quadratiche. Di conseguenza, molti sistemi grafici moderni includono una funzionalità per convertire i caratteri di testo in un percorso grafico.

Si è già visto che è possibile tracciare le strutture dei caratteri di testo e riempirle. In questo modo è possibile visualizzare le strutture dei caratteri con una particolare larghezza del tratto e anche un effetto tracciato, come descritto nell'articolo [**effetti percorso**](effects.md) . Tuttavia è anche possibile convertire una stringa di caratteri in un `SKPath` oggetto. Ciò significa che è possibile usare le strutture di testo per il ritaglio con le tecniche descritte nell'articolo [**ritaglio con percorsi e aree**](clipping.md) .

Oltre a usare un effetto tracciato per tracciare un contorno di caratteri, è anche possibile creare effetti di tracciato basati su un tracciato derivato da una stringa di caratteri ed è anche possibile combinare i due effetti:

![](text-paths-images/pathsandtextsample.png "Text Path Effect")

Nell'articolo precedente sugli [**effetti del percorso**](effects.md)si è visto come il [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) metodo di `SKPaint` possa ottenere un contorno di un tracciato tratteggiato. È anche possibile usare questo metodo con i percorsi derivati da strutture di caratteri.

Infine, in questo articolo viene illustrata un'altra intersezione dei percorsi e del testo: il [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) metodo di `SKCanvas` consente di visualizzare una stringa di testo in modo che la linea di base del testo segua un tracciato curvo.

## <a name="text-to-path-conversion"></a>Conversione da testo a percorso

Il [`GetTextPath`](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single)) metodo di `SKPaint` converte una stringa di caratteri in un `SKPath` oggetto:

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

Gli `x` `y` argomenti e indicano il punto iniziale della linea di base del lato sinistro del testo. Svolgono lo stesso ruolo come nel `DrawText` metodo di `SKCanvas` . All'interno del percorso, la linea di base del lato sinistro del testo avrà le coordinate (x, y).

Il `GetTextPath` metodo è eccessivo se si desidera semplicemente riempire o tracciare il percorso risultante. Il `DrawText` metodo normale consente di eseguire questa operazione. Il `GetTextPath` metodo è più utile per altre attività che coinvolgono percorsi.

Una di queste attività è il ritaglio. Nella pagina **testo di ritaglio** viene creato un tracciato di ritaglio basato sui caratteri delineati della parola "codice". Questo percorso è allungato alla dimensione della pagina per ritagliare una bitmap contenente un'immagine del codice sorgente del **testo di ritaglio** :

[![](text-paths-images/clippingtext-small.png "Triple screenshot of the Clipping Text page")](text-paths-images/clippingtext-large.png#lightbox "Triple screenshot of the Clipping Text page")

Il [`ClippingTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) costruttore della classe carica la bitmap archiviata come risorsa incorporata nella cartella **media** della soluzione:

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

Il `PaintSurface` gestore inizia creando un `SKPaint` oggetto appropriato per il testo. La `Typeface` proprietà è impostata e `TextSize` , anche se per questa particolare applicazione la `TextSize` proprietà è puramente arbitraria. Si noti inoltre che non è disponibile alcuna `Style` impostazione.

Le `TextSize` `Style` impostazioni delle proprietà e non sono necessarie perché questo `SKPaint` oggetto viene usato esclusivamente per la `GetTextPath` chiamata usando la stringa di testo "code". Il gestore misura quindi l'oggetto risultante `SKPath` e applica tre trasformazioni per centrarla e ridimensionarla alla dimensione della pagina. Il percorso può quindi essere impostato come tracciato di ritaglio:

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

Una volta impostato il tracciato di ritaglio, è possibile visualizzare la bitmap che verrà ritagliata in caratteri delineati. Si noti l'utilizzo del [`AspectFill`](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize)) metodo di `SKRect` che calcola un rettangolo per riempire la pagina mantenendo le proporzioni.

La pagina **effetto percorso testo** converte un singolo carattere commerciale in un percorso per creare un effetto del percorso 1D. Un oggetto Paint con questo effetto tracciato viene quindi utilizzato per tracciare il contorno di una versione più grande dello stesso carattere:

[![](text-paths-images/textpatheffect-small.png "Triple screenshot of the Text Path Effect page")](text-paths-images/textpatheffect-large.png#lightbox "Triple screenshot of the Text Path Effect page")

Gran parte del lavoro della [`TextPathEffectPath`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) classe si verifica nei campi e nel costruttore. I due `SKPaint` oggetti definiti come campi vengono utilizzati per due scopi diversi: il primo (denominato `textPathPaint` ) viene utilizzato per convertire la e commerciale con `TextSize` 50 in un percorso per l'effetto del percorso 1D. Il secondo ( `textPaint` ) viene usato per visualizzare la versione più grande della e commerciale con tale effetto del percorso. Per questo motivo, il `Style` di questo secondo oggetto Paint è impostato su `Stroke` , ma la `StrokeWidth` proprietà non è impostata perché tale proprietà non è necessaria quando si utilizza un effetto del percorso 1D:

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

Il costruttore usa prima di tutto l' `textPathPaint` oggetto per misurare la e commerciale con un valore `TextSize` di 50. I negativi delle coordinate centrali di tale rettangolo vengono quindi passati al `GetTextPath` metodo per convertire il testo in un percorso. Il percorso risultante ha il punto (0,0) al centro del carattere, ideale per un effetto del percorso 1D.

Si potrebbe pensare che l' `SKPathEffect` oggetto creato alla fine del costruttore possa essere impostato sulla `PathEffect` proprietà di `textPaint` anziché salvare come campo. Ma questa operazione non è stata eseguita correttamente, perché i risultati della `MeasureText` chiamata nel gestore sono distorti `PaintSurface` :

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

Tale `MeasureText` chiamata viene utilizzata per centrare il carattere nella pagina. Per evitare problemi, la `PathEffect` proprietà viene impostata sull'oggetto Paint dopo che il testo è stato misurato ma prima che venga visualizzato.

## <a name="outlines-of-character-outlines"></a>Delineamenti di delineamenti di caratteri

Normalmente il [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) metodo di `SKPaint` converte un percorso in un altro applicando le proprietà di disegno, in particolare la larghezza del tratto e l'effetto del percorso. Se usato senza effetti del percorso, `GetFillPath` Crea in modo efficace un percorso che delinea un altro percorso. Questa operazione è stata illustrata nella pagina **toccare per delineare il percorso** nell'articolo [**effetti percorso**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) .

È anche possibile chiamare `GetFillPath` sul percorso restituito da `GetTextPath` , ma in primo luogo potrebbe non essere certo che si tratta di un aspetto simile.

La pagina **struttura del contorno dei caratteri** illustra la tecnica. Tutto il codice pertinente si trova nel `PaintSurface` gestore della [`CharacterOutlineOutlinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) classe.

Il costruttore inizia creando un `SKPaint` oggetto denominato `textPaint` con una `TextSize` Proprietà in base alla dimensione della pagina. Viene convertito in un percorso utilizzando il `GetTextPath` metodo. Gli argomenti della coordinata per `GetTextPath` centrare efficacemente il percorso sullo schermo:

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

Il `PaintSurface` gestore crea quindi un nuovo percorso denominato `outlinePath` . Questo diventa il percorso di destinazione nella chiamata a `GetFillPath` . La `StrokeWidth` proprietà di 25 fa `outlinePath` in modo che descriva il contorno di un percorso di 25 pixel che indica i caratteri di testo. Questo percorso viene quindi visualizzato in rosso con una larghezza del tratto di 5:

[![](text-paths-images/characteroutlineoutlines-small.png "Triple screenshot of the Character Outline Outlines page")](text-paths-images/characteroutlineoutlines-large.png#lightbox "Triple screenshot of the Character Outline Outlines page")

Esaminare attentamente e si noterà che la struttura del percorso crea un angolo acuto. Si tratta di elementi normali di questo processo.

## <a name="text-along-a-path"></a>Testo lungo un tracciato

Il testo viene in genere visualizzato su una linea di base orizzontale. Il testo può essere ruotato per essere eseguito verticalmente o diagonalmente, ma la linea di base è ancora una linea retta.

In alcuni casi, tuttavia, si desidera che il testo venga eseguito lungo una curva. Questo è lo scopo del [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) metodo di `SKCanvas` :

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

Il testo specificato nel primo argomento viene eseguito lungo il percorso specificato come secondo argomento. È possibile iniziare il testo in corrispondenza di un offset dall'inizio del percorso con l' `hOffset` argomento. In genere il percorso costituisce la linea di base del testo: gli ascendenti di testo si trovano su un lato del percorso e i discendenti di testo sono sull'altro. È tuttavia possibile compensare la linea di base del testo dal percorso con l' `vOffset` argomento.

Questo metodo non ha alcuna funzione per fornire indicazioni sull'impostazione della `TextSize` proprietà di `SKPaint` per rendere il testo perfettamente dimensionato per l'esecuzione dall'inizio del percorso fino alla fine. A volte è possibile determinare le dimensioni del testo in modo autonomo. In altre occasioni è necessario usare funzioni di misurazione del percorso per essere descritte nell'articolo successivo sulle [**informazioni sul percorso e sull'enumerazione**](information.md).

Il programma di **testo circolare** esegue il wrapping del testo intorno a un cerchio. È facile determinare la circonferenza di un cerchio, quindi è facile ridimensionare il testo per adattarlo esattamente. Il `PaintSurface` gestore della [`CircularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) classe calcola un raggio di un cerchio in base alle dimensioni della pagina. Il cerchio diventa `circularPath` :

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

La `TextSize` proprietà di `textPaint` viene quindi regolata in modo che la larghezza del testo corrisponda alla circonferenza del cerchio:

[![](text-paths-images/circulartext-small.png "Triple screenshot of the Circular Text page")](text-paths-images/circulartext-large.png#lightbox "Triple screenshot of the Circular Text page")

Il testo stesso è stato scelto anche come circolare: la parola "Circle" è sia l'oggetto della frase che l'oggetto di una frase preposizionale.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
