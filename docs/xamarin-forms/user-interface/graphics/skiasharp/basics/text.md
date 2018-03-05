---
title: L'integrazione di testo e grafica
description: Come determinare le dimensioni della stringa di testo viene eseguito il rendering per l'integrazione di testo con grafica SkiaSharp
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9a8cfac8911b1a8a13a4b6f149d02cd2f2c78453
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="integrating-text-and-graphics"></a>L'integrazione di testo e grafica

_Come determinare le dimensioni della stringa di testo viene eseguito il rendering per l'integrazione di testo con grafica SkiaSharp_

In questo articolo viene illustrato come misurare il testo, probabilmente scalare il testo per una determinata dimensione e integrare il testo con gli altri elementi grafici:

![](text-images/textandgraphicsexample.png "Testo racchiuso tra i rettangoli")

Il SkiaSharp `Canvas` classe include anche metodi per disegnare un rettangolo ([`DrawRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRect/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/)) e un rettangolo con angoli arrotondati ([`DrawRoundRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRoundRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPaint/)). Questi metodi richiedono il rettangolo deve essere definito come un `SKRect` valore.

Il **racchiuse testo** pagina Centra una breve stringa di testo nella pagina e scelte con un intervallo composto da una coppia di rettangoli con angoli arrotondati. Il [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) classe illustra come farlo.

In SkiaSharp è utilizzare la `SKPaint` classe di set di attributi di testo e i tipi di carattere, ma è possibile utilizzarlo anche per ottenere la dimensione di rendering del testo. L'inizio delle operazioni seguenti `PaintSurface` gestore eventi chiama due diversi `MeasureText` metodi. Il primo [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/) chiamata presenta una semplice `string` argomento e restituisce la larghezza in pixel del testo in base agli attributi di tipo di carattere corrente. Quindi viene calcolato un nuovo `TextSize` proprietà del `SKPaint` oggetto basato su tale larghezza di rendering corrente `TextSize` , proprietà e alla larghezza dell'area di visualizzazione. Questo consente di impostare `TextSize` in modo che la stringa di testo per eseguire il rendering al 90% della larghezza dello schermo:

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
    SKRect textBounds;
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

Il secondo [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/SkiaSharp.SKRect@/) chiamata ha un `SKRect` argomento, in modo Ottiene larghezza e altezza del testo visualizzabile. Il `Height` proprietà di questo `SKRect` valore dipende dalla presenza di lettere maiuscole, i tratti ascendenti e discendenti nella stringa di testo. Diversi `Height` valori sono segnalati per mom"testo stringhe", "cat" e "dog", ad esempio.

Il `Left` e `Top` le proprietà del `SKRect` struttura indicano le coordinate dell'angolo superiore sinistro del testo visualizzabile se viene visualizzato il testo da un `DrawText` chiamare con le posizioni X e Y pari a 0. Ad esempio, quando il programma è in esecuzione in un simulatore di iPhone 7, `TextSize` viene assegnato il valore 90.6254 come risultato il calcolo successivo alla prima chiamata a `MeasureText`. Il `SKRect` valore ottenuto dalla seconda chiamata a `MeasureText` con i valori di proprietà seguenti:

- `Left` = 6
- `Top` = &#x2013;68
- `Width` = 664.8214
- `Height` = 88;

Tenere presente che le coordinate X e Y per passare il `DrawText` metodo specificare il lato sinistro del testo nella linea di base. Il `Top` valore indica che il testo si estende 68 pixel sopra la linea di base e (sottraendo 68 it da 88) 20 pixel sotto la linea di base. Il `Left` valore 6 indica che il testo inizia 6 pixel a destra del valore di X il `DrawText` chiamare. In questo modo per la spaziatura tra i caratteri normale. Se si desidera visualizzare il testo offrono nell'angolo superiore sinistro dello schermo, passare i segni di sottrazione di questi `Left` e `Top` valori come le coordinate X e Y di `DrawText`, in questo esempio & #x 2013; 6 e 68.

Il `SKRect` struttura definisce molte proprietà utili e i metodi, alcuni dei quali vengono utilizzati nel resto del `PaintSurface` gestore. Il `MidX` e `MidY` valori indicano le coordinate del centro del rettangolo. (Nell'esempio iPhone 7, tali valori sono 338.4107 e & #x 2013; 24.) Il codice seguente utilizza questi valori per il calcolo più semplice di coordinate per allineare al centro del testo sullo schermo:

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

Il `PaintSurface` dell'operazione del gestore con due chiamate a `DrawRoundRect`, che richiedono argomenti di `SKRect`. Questo `SKRect` è certo che sia simile al valore di `SKRect` valore ottenuto dal `MeasureText` (metodo), ma non può essere lo stesso. In primo luogo, deve essere un po' più grandi in modo da non disegna il rettangolo arrotondato su bordi del testo, in secondo luogo, deve essere spostato nello spazio in modo che il `Left` e `Top` valori corrispondono nell'angolo superiore sinistro, in cui il rettangolo si desidera posizionato. Questi due processi vengono eseguiti da `Offset` e `Inflate` metodi definiti dal `SKRect`:

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

Successivamente, il resto del metodo è semplice. Crea un altro `SKPaint` oggetto per i bordi e chiamate `DrawRoundRect` due volte. La seconda chiamata viene utilizzato un rettangolo ingrandito in base a un altro 10 pixel. La prima chiamata specifica un raggio dell'angolo di 20 pixel. il secondo è un raggio dell'angolo di 30 pixel, in modo sembrano essere parallela:

 [![](text-images/framedtext-small.png "Schermata triplo della pagina di testo racchiuse")](text-images/framedtext-large.png "tripla schermata della pagina di testo racchiusi")

È possibile attivare il telefono o il simulatore lateralmente per visualizzare il testo e l'aumento delle dimensioni di frame.

Se è necessario solo per allineare al centro del testo sullo schermo, è possibile farlo senza circa la misurazione del testo impostando il `TextAlign` proprietà di `SKPaint` a `SKTextAlign.Center`. La coordinata X in cui si specifica il `DrawText` metodo indicherà in cui è posizionato il centro del testo orizzontale. Se si passa il punto centrale della schermata per la `DrawText` (metodo), il testo verrà centrato orizzontalmente e *quasi* centrati verticalmente perché verrà centrato verticalmente la linea di base.

Testo stesso può essere considerato molto simile a un'opzione grafica. Semplice è possibile visualizzare la struttura di caratteri di testo, anziché la normale visualizzazione piena:

[![](text-images/outlinedtext-small.png "Tripla cattura di schermata della pagina di testo descritte")](text-images/outlinedtext-large.png "tripla cattura di schermata della pagina descritte testo")

Questa operazione viene eseguita semplicemente modificando la normale `Style` proprietà del `SKPaint` l'impostazione predefinita dell'oggetto `SKPaintStyle.Fill` a `SKPaintStyle.Stroke` e specificando un valore. Il `PaintSurface` gestore del **descritte testo** pagina vengono visualizzati come farlo:

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
    SKRect textBounds;
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

 Negli ultimi articoli diversi, disporre ora illustrato come utilizzare SkiaSharp per disegnare il testo, cerchi, ellissi e rettangoli con angoli arrotondati. Il passaggio successivo consiste [SkiaSharp righe e i percorsi](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md) in cui si apprenderà come disegnare linee collegate in un percorso grafico.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
