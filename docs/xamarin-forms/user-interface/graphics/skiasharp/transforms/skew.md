---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 207b16f062a5c2137ac5fc3c21775d2486fda57d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135863"
---
# <a name="the-skew-transform"></a>Trasformazione di inclinazione

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Scopri in che modo la trasformazione asimmetria può creare oggetti grafici inclinati in SkiaSharp_

In SkiaSharp la trasformazione inclinazione inclina gli oggetti grafici, ad esempio l'ombreggiatura in questa immagine:

![](skew-images/skewexample.png "An example of skewing from the Skew Shadow Text program")

L'inclinazione converte un rettangolo in un parallelogramma, ma un'ellisse inclinata è ancora un'ellisse.

Sebbene Xamarin.Forms definisca le proprietà per la conversione, il ridimensionamento e le rotazioni, non esiste alcuna proprietà corrispondente in Xamarin.Forms per asimmetria.

Il [`Skew`](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single)) metodo `SKCanvas` accetta due argomenti per inclinazione orizzontale e asimmetria verticale:

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Un secondo [`Skew`](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint)) metodo combina gli argomenti in un singolo `SKPoint` valore:

```csharp
public void Skew (SKPoint skew)
```

Tuttavia, è improbabile che venga usato uno di questi due metodi in isolamento.

La pagina dell' **esperimento di inclinazione** consente di provare i valori di inclinazione compresi tra-10 e 10. Una stringa di testo viene posizionata nell'angolo superiore sinistro della pagina, con valori di inclinazione ottenuti da due `Slider` elementi. Ecco il `PaintSurface` gestore nella [`SkewExperimentPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) classe:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        canvas.Skew((float)xSkewSlider.Value, (float)ySkewSlider.Value);
        canvas.DrawText(text, 0, -textBounds.Top, textPaint);
    }
}
```

I valori dell' `xSkew` argomento spostano la parte inferiore del testo a destra per i valori positivi o a sinistra per i valori negativi. I valori di `ySkew` spostano il lato destro del testo verso il basso per i valori positivi o verso l'alto per i valori negativi:

[![](skew-images/skewexperiment-small.png "Triple screenshot of the Skew Experiment page")](skew-images/skewexperiment-large.png#lightbox "Triple screenshot of the Skew Experiment page")

Se il `xSkew` valore è il valore negativo del `ySkew` valore, il risultato è rotazione, ma anche ridimensionato in qualche modo.

Le formule di trasformazione sono le seguenti:

x ' = x + xSkew · y

y ' = ySkew · x + y

Per un valore positivo, ad esempio `xSkew` , il `x'` valore trasformato aumenta come `y` aumenta. Questa è la causa dell'inclinazione.

Se un triangolo 200 pixel di larghezza e 100 pixel di altezza è posizionato con l'angolo superiore sinistro al punto (0,0) e viene eseguito il rendering con un `xSkew` valore di 1,5, viene restituito il parallelogrammo seguente:

![](skew-images/skeweffect.png "The effect of the skew transform on a rectangle")

Le coordinate del bordo inferiore hanno `y` valori pari a 100, quindi vengono spostati 150 pixel a destra.

Per i valori diversi da zero di `xSkew` o `ySkew` , solo il punto (0, 0) rimane invariato. Tale punto può essere considerato il centro dell'asimmetria. Se è necessario che il centro della distorsione sia un altro elemento, in genere il caso, non esiste alcun `Skew` metodo che lo fornisca. È necessario combinare in modo esplicito `Translate` le chiamate con la `Skew` chiamata. Per centrare la distorsione in corrispondenza di `px` e `py` , effettuare le chiamate seguenti:

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

Le formule di trasformazione composite sono:

x ' = x + xSkew · (y-py)

y ' = ySkew · (x – px) + y

Se `ySkew` è zero, il `px` valore non viene utilizzato. Il valore è irrilevante e, analogamente, per `ySkew` e `py` .

È possibile che si risulti più agevole specificando l'inclinazione come angolo di inclinazione, ad esempio l'angolo α in questo diagramma:

![](skew-images/skewangleeffect.png "The effect of the skew transform on a rectangle with a skewing angle indicated")

Il rapporto tra lo spostamento di 150 pixel e il 100 pixel verticale è la tangente di tale angolo, in questo esempio 56,3 gradi.

Il file XAML della pagina dell' **esperimento dell'angolo di inclinazione** è simile alla pagina dell' **angolo di inclinazione** , ad eccezione del fatto che gli `Slider` elementi variano da-90 gradi a 90 gradi. Il [`SkewAngleExperiment`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) file code-behind centra il testo nella pagina e USA `Translate` per impostare una parte centrale della distorsione al centro della pagina. Un `SkewDegrees` Metodo breve nella parte inferiore del codice converte gli angoli in valori di inclinazione:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;

        canvas.Translate(xCenter, yCenter);
        SkewDegrees(canvas, xSkewSlider.Value, ySkewSlider.Value);
        canvas.Translate(-xCenter, -yCenter);
        canvas.DrawText(text, xText, yText, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

Poiché un angolo si avvicina a 90 gradi positivi o negativi, la tangente si avvicina a infinito, ma gli angoli fino a circa 80 gradi sono utilizzabili:

[![](skew-images/skewangleexperiment-small.png "Triple screenshot of the Skew Angle Experiment page")](skew-images/skewangleexperiment-large.png#lightbox "Triple screenshot of the Skew Angle Experiment page")

Una piccola inclinazione orizzontale negativa può simulare testo obliquo o corsivo, come illustrato nella pagina di **testo obliquo** . La [`ObliqueTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) classe Mostra come viene eseguita:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Maroon,
        TextAlign = SKTextAlign.Center,
        TextSize = info.Width / 8       // empirically determined
    })
    {
        canvas.Translate(info.Width / 2, info.Height / 2);
        SkewDegrees(canvas, -20, 0);
        canvas.DrawText(Title, 0, 0, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

La `TextAlign` proprietà di `SKPaint` viene impostata su `Center` . Senza alcuna trasformazione, la `DrawText` chiamata con coordinate (0, 0) posiziona il testo con il centro orizzontale della linea di base nell'angolo superiore sinistro. Il `SkewDegrees` testo inclina il testo orizzontalmente di 20 gradi rispetto alla linea di base. La `Translate` chiamata sposta il centro orizzontale della linea di base del testo al centro dell'area di disegno:

[![](skew-images/obliquetext-small.png "Triple screenshot of the Oblique Text page")](skew-images/obliquetext-large.png#lightbox "Triple screenshot of the Oblique Text page")

La pagina del **testo dell'ombreggiatura inclinata** Mostra come usare una combinazione di inclinazione di 45 gradi e scala verticale per rendere un'ombreggiatura del testo che si inclina dal testo. Ecco la parte pertinente del `PaintSurface` gestore:

```csharp
using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = info.Width / 6;   // empirically determined

    // Common to shadow and text
    string text = "Shadow";
    float xText = 20;
    float yText = info.Height / 2;

    // Shadow
    textPaint.Color = SKColors.LightGray;
    canvas.Save();
    canvas.Translate(xText, yText);
    canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
    canvas.Scale(1, 3);
    canvas.Translate(-xText, -yText);
    canvas.DrawText(text, xText, yText, textPaint);
    canvas.Restore();

    // Text
    textPaint.Color = SKColors.Blue;
    canvas.DrawText(text, xText, yText, textPaint);
}
```

L'ombreggiatura viene visualizzata per prima e quindi il testo:

[![](skew-images/skewshadowtext1-small.png "Triple screenshot of the Skew Shadow Text page")](skew-images/skewshadowtext1-large.png#lightbox "Triple screenshot of the Skew Shadow Text page")

La coordinata verticale passata al `DrawText` metodo indica la posizione del testo rispetto alla linea di base. Si tratta della stessa coordinata verticale utilizzata per il centro di asimmetria. Questa tecnica non funzionerà se la stringa di testo contiene i discendenti. Ad esempio, sostituire la parola "stravagante" per "Shadow" ed ecco il risultato:

[![](skew-images/skewshadowtext2-small.png "Triple screenshot of the Skew Shadow Text page with an alternative word with descenders")](skew-images/skewshadowtext2-large.png#lightbox "Triple screenshot of the Skew Shadow Text page with an alternative word with descenders")

L'ombreggiatura e il testo sono ancora allineati alla linea di base, ma l'effetto sembra errato. Per risolvere il problema, è necessario ottenere i limiti di testo:

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

Le `Translate` chiamate devono essere regolate in base all'altezza dei discendenti:

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

Ora l'ombreggiatura si estende dalla parte inferiore dei discendenti:

[![](skew-images/skewshadowtext3-small.png "Triple screenshot of the Skew Shadow Text page with adjustments for descenders")](skew-images/skewshadowtext3-large.png#lightbox "Triple screenshot of the Skew Shadow Text page with adjustments for descenders")

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
