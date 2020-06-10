---
title: "integrazione di testo e grafica" Descrizione: "in questo articolo viene illustrato come determinare le dimensioni della stringa di testo di cui è stato eseguito il rendering per integrare il testo con la grafica SkiaSharp in Xamarin.Forms applicazioni e come illustrato nel codice di esempio".
ms. prod: Novell MS. Technology: Novell-skiasharp ms. AssetID: A0B5AC82-7736-4AD8-AA16-FE43E18D203C autore: davidbritch ms. Author: dabritch ms. Date: 03/10/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="integrating-text-and-graphics"></a>Integrazione di testo e grafica

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Vedere come determinare le dimensioni della stringa di testo di cui è stato eseguito il rendering per integrare il testo con SkiaSharp graphics_

Questo articolo illustra come misurare il testo, ridimensionare il testo a una dimensione specifica e integrare il testo con altri elementi grafici:

![](text-images/textandgraphicsexample.png "Text surrounded by rectangles")

Tale immagine include anche un rettangolo arrotondato. La `Canvas` classe SkiaSharp include i [`DrawRect`](xref:SkiaSharp.SKCanvas.DrawRect*) metodi per creare un rettangolo e i [`DrawRoundRect`](xref:SkiaSharp.SKCanvas.DrawRoundRect*) metodi per creare un rettangolo con angoli arrotondati. Questi metodi consentono di definire il rettangolo come `SKRect` valore o in altri modi.

La pagina di **testo incorniciato** centra una stringa di testo breve nella pagina e la racchiude con un frame costituito da una coppia di rettangoli arrotondati. La [`FramedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) classe Mostra come viene eseguita.

In SkiaSharp si usa la `SKPaint` classe per impostare gli attributi del testo e del tipo di carattere, ma è anche possibile usarla per ottenere la dimensione del testo di cui è stato eseguito il rendering. L'inizio del `PaintSurface` gestore eventi seguente chiama due metodi diversi `MeasureText` . La prima [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) chiamata ha un `string` argomento semplice e restituisce la larghezza in pixel del testo in base agli attributi del tipo di carattere corrente. Il programma calcola quindi una nuova `TextSize` proprietà dell' `SKPaint` oggetto in base alla larghezza di cui è stato eseguito il rendering, alla `TextSize` proprietà corrente e alla larghezza dell'area di visualizzazione. Questo calcolo deve essere impostato in `TextSize` modo che venga eseguito il rendering della stringa di testo al 90% della larghezza dello schermo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

La seconda [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@)) chiamata presenta un `SKRect` argomento, quindi ottiene sia la larghezza sia l'altezza del testo sottoposto a rendering. La `Height` proprietà di questo `SKRect` valore dipende dalla presenza di lettere maiuscole, ascendenti e discendenti nella stringa di testo. `Height`Sono riportati valori diversi per le stringhe di testo "mom", "Cat" e "Dog", ad esempio.

Le `Left` `Top` proprietà e della `SKRect` struttura indicano le coordinate dell'angolo superiore sinistro del testo sottoposto a rendering se il testo viene visualizzato da una `DrawText` chiamata con posizioni X e Y pari a 0. Ad esempio, quando il programma è in esecuzione in un simulatore iPhone 7, a `TextSize` viene assegnato il valore 90,6254 come risultato del calcolo che segue la prima chiamata a `MeasureText` . Il `SKRect` valore ottenuto dalla seconda chiamata a `MeasureText` presenta i valori di proprietà seguenti:

- `Left`= 6
- `Top` = &ndash;68
- `Width`= 664,8214
- `Height`= 88;

Tenere presente che le coordinate X e Y passate al `DrawText` Metodo specificano il lato sinistro del testo in corrispondenza della linea di base. Il `Top` valore indica che il testo estende 68 pixel al di sopra della linea di base e (sottraendo 68 da 88) 20 pixel al di sotto della linea di base. Il `Left` valore 6 indica che il testo inizia con sei pixel a destra del valore X della `DrawText` chiamata. Questo consente la spaziatura tra i caratteri normale. Se si desidera visualizzare il testo comodamente nell'angolo superiore sinistro dello schermo, passare i valori negativi di questi `Left` `Top` valori e come coordinate X e Y di `DrawText` , in questo esempio &ndash; 6 e 68.

La `SKRect` struttura definisce diversi metodi e proprietà utili, alcuni dei quali vengono utilizzati nella parte restante del `PaintSurface` gestore. I `MidX` `MidY` valori e indicano le coordinate del centro del rettangolo. Nell'esempio di iPhone 7, questi valori sono 338,4107 e &ndash; 24. Il codice seguente usa questi valori per il calcolo più semplice delle coordinate per centrare il testo sullo schermo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

La `SKImageInfo` struttura info definisce anche una [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) proprietà di tipo `SKRect` , quindi è possibile calcolare `xText` e `yText` come segue:

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

Il `PaintSurface` gestore termina con due chiamate a `DrawRoundRect` , entrambe che richiedono argomenti di `SKRect` . Questo `SKRect` valore è basato sul `SKRect` valore ottenuto dal `MeasureText` metodo, ma non può essere lo stesso. Per prima cosa, deve essere un po' più grande, in modo che il rettangolo arrotondato non disegni sui bordi del testo. In secondo luogo, è necessario spostare lo spazio in modo che i `Left` valori e `Top` corrispondano all'angolo superiore sinistro in cui deve essere posizionato il rettangolo. Questi due processi vengono eseguiti dai [`Offset`](xref:SkiaSharp.SKRect.Offset*) metodi e [`Inflate`](xref:SkiaSharp.SKRect.Inflate*) definiti da `SKRect` :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

In seguito, il resto del metodo è semplice. Viene creato un altro `SKPaint` oggetto per i bordi e viene chiamato `DrawRoundRect` due volte. La seconda chiamata utilizza un rettangolo inflatto da altri 10 pixel. La prima chiamata specifica un raggio dell'angolo di 20 pixel. Il secondo ha un raggio d'angolo di 30 pixel, che sembra essere parallelo:

 [![](text-images/framedtext-small.png "Triple screenshot of the Framed Text page")](text-images/framedtext-large.png#lightbox "Triple screenshot of the Framed Text page")

È possibile trasformare il telefono o il simulatore lateralmente per visualizzare le dimensioni del testo e dell'aumento dei frame.

Se è sufficiente centrare il testo sullo schermo, è possibile eseguire l'operazione approssimativamente senza misurare il testo. Al contrario, impostare la [`TextAlign`](xref:SkiaSharp.SKPaint.TextAlign) proprietà di sul `SKPaint` membro dell'enumerazione [`SKTextAlign.Center`](xref:SkiaSharp.SKTextAlign) . La coordinata X specificata nel `DrawText` metodo indica il punto in cui è posizionato il centro orizzontale del testo. Se si passa il punto medio dello schermo al `DrawText` metodo, il testo verrà centrato orizzontalmente e *quasi* verticalmente al centro, perché la linea di base verrà centrata verticalmente.

Il testo può essere trattato in modo analogo a qualsiasi altro oggetto grafico. Una semplice opzione consiste nel visualizzare il contorno dei caratteri di testo:

[![](text-images/outlinedtext-small.png "Triple screen shot of the Outlined Text page")](text-images/outlinedtext-large.png#lightbox "Triple screenshot of the Outlined Text page")

Questa operazione viene eseguita semplicemente modificando la `Style` proprietà Normal dell' `SKPaint` oggetto dalla relativa impostazione predefinita `SKPaintStyle.Fill` a `SKPaintStyle.Stroke` e specificando la larghezza del tratto. Il `PaintSurface` gestore della pagina di **testo delineata** Mostra il modo in cui viene eseguito:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

Un altro oggetto grafico comune è la bitmap. Questo è un argomento di grandi dimensioni illustrato in dettaglio nella sezione [**bitmap di SkiaSharp**](../bitmaps/index.md), ma il prossimo articolo, [**nozioni di base sulle bitmap in SkiaSharp**](bitmaps.md), fornisce un'introduzione più breve.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
