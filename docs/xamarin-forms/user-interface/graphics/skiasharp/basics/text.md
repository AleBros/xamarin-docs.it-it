---
title: Integrazione di testo e grafica
description: Questo articolo illustra come determinare le dimensioni della stringa di testo sottoposto a rendering per integrare il testo con la grafica di SkiaSharp nelle applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: d23b4dbb97f4f98ff0361bb056e394bb7cbd941a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645640"
---
# <a name="integrating-text-and-graphics"></a>Integrazione di testo e grafica

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Informazioni su come determinare le dimensioni della stringa di testo sottoposto a rendering per l'integrazione di testo con la grafica di SkiaSharp_

Questo articolo viene illustrato come misurare il testo, ridimensionare il testo da una determinata dimensione e l'integrazione di testo con gli altri elementi grafici:

![](text-images/textandgraphicsexample.png "Testo racchiuso tra i rettangoli")

Tale immagine include inoltre un rettangolo arrotondato. Di SkiaSharp `Canvas` classe comprende [ `DrawRect` ](xref:SkiaSharp.SKCanvas.DrawRect*) metodi per disegnare un rettangolo e [ `DrawRoundRect` ](xref:SkiaSharp.SKCanvas.DrawRoundRect*) metodi per disegnare un rettangolo con angoli arrotondati. Questi metodi che consentono il rettangolo deve essere definita come un `SKRect` valore o in altri modi.

Il **incorniciata testo** pagina Centra una breve stringa di testo nella pagina e racchiude con un intervallo composto da una coppia di rettangoli con angoli arrotondati. Il [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) classe illustra come farlo.

In SkiaSharp, si usa il `SKPaint` classe per impostare gli attributi di testo e del tipo di carattere, ma si può anche usarlo per ottenere la dimensione viene eseguito il rendering del testo. L'inizio delle operazioni seguenti `PaintSurface` gestore dell'evento chiama due diversi `MeasureText` metodi. Il primo [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String)) chiamata ha un semplice `string` argomento e restituisce la larghezza in pixel del testo basata sugli attributi del tipo di carattere corrente. Il programma, quindi calcola una nuova `TextSize` proprietà del `SKPaint` oggetto basato su tale larghezza di rendering, corrente `TextSize` proprietà e la larghezza dell'area di visualizzazione. Questo calcolo consente di impostare `TextSize` in modo che la stringa di testo per eseguire il rendering al 90% della larghezza della schermata:

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

La seconda [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@)) chiamata ha un `SKRect` argomento, in modo che ottiene larghezza e altezza del testo sottoposto a rendering. Il `Height` proprietà di questo oggetto `SKRect` valore dipende dalla presenza di lettere maiuscole, i tratti ascendenti e tratti discendenti nella stringa di testo. Diversi `Height` valori sono restituiti per il mom"testo stringhe", "cat" e "cane", ad esempio.

Il `Left` e `Top` proprietà delle `SKRect` struttura indica le coordinate dell'angolo superiore sinistro del testo sottoposto a rendering se viene visualizzato il testo da un `DrawText` chiamare con le posizioni X e Y pari a 0. Ad esempio, quando questo programma è in esecuzione in un simulatore iPhone 7 `TextSize` viene assegnato il valore 90.6254 come risultato il calcolo successivo alla prima chiamata a `MeasureText`. Il `SKRect` valore ottenuto dalla seconda chiamata a `MeasureText` ha i valori delle proprietà seguenti:

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

Tenere presente che le coordinate X e Y è passare il `DrawText` metodo specifica il lato sinistro del testo presente la linea di base. Il `Top` valore indica che il testo estende 68 pixel di sopra di questi dati di riferimento e di (sottraendo 68 it da 88) 20 pixel sotto la linea di base. Il `Left` il valore 6 indica che il testo inizia sei pixel a destra del valore di X il `DrawText` chiamare. In questo modo per la spaziatura tra i caratteri normali. Se si desidera visualizzare il testo saldamente nell'angolo superiore sinistro dello schermo, passare i negativi di questi `Left` e `Top` i valori come le coordinate X e Y del `DrawText`, in questo esempio &ndash;6 e 68.

Il `SKRect` struttura definisce diverse proprietà utili e metodi, alcuni dei quali vengono usati nel resto del `PaintSurface` gestore. Il `MidX` e `MidY` valori indicano le coordinate del punto centrale del rettangolo. (Nell'esempio iPhone 7, tali valori sono 338.4107 e &ndash;24.) Il codice seguente usa questi valori per il calcolo più semplice delle coordinate per allineare al centro il testo sullo schermo:

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

Il `SKImageInfo` struttura di informazioni definisce anche una [ `Rect` ](xref:SkiaSharp.SKImageInfo.Rect) vlastnosti typu `SKRect`, pertanto è anche possibile calcolare `xText` e `yText` simile al seguente:

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

Il `PaintSurface` gestore si conclude con due chiamate a `DrawRoundRect`, che richiedono argomenti di `SKRect`. Ciò `SKRect` valore si basa sul `SKRect` valore ottenuto dal `MeasureText` (metodo), ma non può essere lo stesso. In primo luogo, deve essere un po' più grande in modo che non disegna il rettangolo arrotondato sopra contorni del testo. In secondo luogo, deve essere spostato nello spazio in modo che il `Left` e `Top` valori corrispondono in cui il rettangolo deve essere posizionato nell'angolo superiore sinistro. Questi due processi vengono effettuati mediante il [ `Offset` ](xref:SkiaSharp.SKRect.Offset*) e [ `Inflate` ](xref:SkiaSharp.SKRect.Inflate*) metodi definiti da `SKRect`:

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

In seguito, il resto del metodo è molto semplice. Crea un altro `SKPaint` oggetto per i bordi e le chiamate `DrawRoundRect` due volte. La seconda chiamata utilizza un rettangolo a inflating per un altro 10 pixel. La prima chiamata consente di specificare un raggio dell'angolo pari a 20 pixel. Il secondo contiene un raggio dell'angolo di 30 pixel, in modo che sembrano essere parallele:

 [![](text-images/framedtext-small.png "Tripla screenshot della pagina di testo incorniciata")](text-images/framedtext-large.png#lightbox "tripla screenshot della pagina di testo inseriti in un frame")

È possibile trasformare il telefono o un simulatore lateralmente per visualizzare il testo e il frame dell'aumento delle dimensioni.

Se è necessario solo per allineare al centro del testo sullo schermo, è possibile farlo senza circa la misurazione del testo. Al contrario, impostare il [ `TextAlign` ](xref:SkiaSharp.SKPaint.TextAlign) proprietà della `SKPaint` al membro di enumerazione [ `SKTextAlign.Center` ](xref:SkiaSharp.SKTextAlign). La coordinata X in cui si specifica il `DrawText` metodo indica quindi dove è posizionato al centro orizzontale del testo. Se si passa il punto centrale della schermata per la `DrawText` metodo, il testo verrà centrato in senso orizzontale e *quasi* centrati verticalmente poiché la linea di base verrà centrato verticalmente.

Testo può essere considerato molto come qualsiasi altro oggetto grafico. Semplice è possibile visualizzare la struttura dei caratteri di testo:

[![](text-images/outlinedtext-small.png "È pari al triplo cattura di schermata della pagina di testo con contorni")](text-images/outlinedtext-large.png#lightbox "Triple screenshot of the Outlined Text page")

Questa operazione viene eseguita semplicemente modificando la normale `Style` proprietà del `SKPaint` rispetto all'impostazione predefinita dell'oggetto `SKPaintStyle.Fill` a `SKPaintStyle.Stroke`e specificando una larghezza di tratto. Il `PaintSurface` gestore del **testo con contorni** pagina mostra come farlo:

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

Un altro oggetto con interfaccia grafico comune è la bitmap. Questo è un argomento molto ampio trattato in dettaglio nella sezione [ **SkiaSharp Bitmaps**](../bitmaps/index.md), il prossimo articolo, ma [ **nozioni di base di Bitmap in SkiaSharp**](bitmaps.md), viene fornita una breve introduzione.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
