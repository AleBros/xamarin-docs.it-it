---
title: La trasformazione di inclinazione
description: In questo articolo viene illustrato come la trasformazione di inclinazione creare inclinato gli oggetti grafici in SkiaSharp e viene illustrato quanto descritto con codice di esempio.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: charlespetzold
ms.author: chape
ms.date: 03/20/2017
ms.openlocfilehash: 8a0d17f5cdc790aed2bd8fd671563378d5d523b1
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244081"
---
# <a name="the-skew-transform"></a>La trasformazione di inclinazione

_Vedere come la trasformazione di inclinazione creare gli oggetti grafici inclinati in SkiaSharp_

In SkiaSharp, la trasformazione di inclinazione inclina gli oggetti grafici, ad esempio l'ombreggiatura in questa immagine:

![](skew-images/skewexample.png "Un esempio di inclinazione dal programma di inclinare il testo con ombreggiatura")

L'inclinazione Trasforma rettangoli in parallelogrammi, ma un'ellisse asimmetrici è ancora un'ellisse.

Sebbene xamarin. Forms definisce le proprietà per la traduzione, scalabilità e rotazioni, non è presente alcuna proprietà corrispondente in xamarin. Forms per lo sfasamento dei segnali.

Il [ `Skew` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Skew/p/System.Single/System.Single/) metodo `SKCanvas` accetta due argomenti per inclinazione orizzontale e verticale inclinare:

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Un secondo [ `Skew` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Skew/p/SkiaSharp.SKPoint/) metodo combina tali argomenti in una singola `SKPoint` valore:

```csharp
public void Skew (SKPoint skew)
```

Tuttavia, è improbabile che sarà possibile usare uno di questi due metodi in isolamento.

Il **inclinare sperimentare** pagina consente sperimentare inclinazione i valori compresi tra -10 e 10. Una stringa di testo viene posizionata nell'angolo superiore sinistro della pagina, con valori di inclinazione ottenuti da due `Slider` elementi. Ecco il `PaintSurface` gestore di [ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) classe:

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

I valori del `xSkew` argomento shift a fondo il testo a destra per i valori positivi o a sinistra per i valori negativi. I valori di `ySkew` spostare destra del testo verso il basso per i valori positivi o a valori negativi:

[![](skew-images/skewexperiment-small.png "Schermata triplo della pagina di sfasamento dell'esperimento")](skew-images/skewexperiment-large.png#lightbox "tripla schermata della pagina di sfasamento dell'esperimento")

Se `xSkew` è il corrispondente negativo del `ySkew`, il risultato è la rotazione, ma anche scalato in qualche modo come indica la visualizzazione di piattaforma UWP.

Di seguito sono elencate le formule di trasformazione:

x' = x + xSkew · y

y' = ySkew · x + y

Ad esempio, per un numero positivo `xSkew` valore trasformato `x'` valore aumenta man mano che `y` aumenta. Che è quello che determina l'inclinazione.

Se un pixel triangolo 200 e 100 pixel di altezza viene posizionato con il relativo angolo superiore sinistro nel punto (0, 0) e viene eseguito il rendering con un `xSkew` valore pari a 1,5, i risultati del parallelogramma seguenti:

![](skew-images/skeweffect.png "L'effetto della trasformazione di inclinazione su un rettangolo")

Le coordinate del bordo inferiore hanno `y` valori pari a 100, pertanto è 150 pixel di spostare a destra.

Per i valori diversi da zero di `xSkew` o `ySkew`, solo il punto (0, 0) rimane invariato. Tale punto può essere considerato il centro di inclinazione. Se è necessario il centro di inclinazione da un altro (che è in genere il caso), vi è alcun `Skew` metodo che fornisce che. È necessario combinare in modo esplicito `Translate` chiama con il `Skew` chiamare. Per centrare l'inclinazione in `px` e `py`, eseguire le chiamate seguenti:

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

Le formule di trasformazione composita sono:

x' = x + xSkew · (y-PIA)

y' = ySkew · (x – px) + y

Se `ySkew` è zero e si specifica solo un valore diverso da zero di `xSkew`, quindi `px` valore non viene utilizzato. Il valore è irrilevante e in modo analogo per `ySkew` e `py`.

Si potrebbe preferisce specificare inclinazione come un angolo di inclinazione, ad esempio l'angolo α in questo diagramma:

![](skew-images/skewangleeffect.png "L'effetto della trasformazione di inclinazione su un rettangolo con un angolo di inclinazione indicato")

Il rapporto del turno 150 pixel di 100 pixel verticale è la tangente di tale angolo, in questo esempio 56.3 gradi.

Il file XAML del **esperimento angolo di inclinazione** è simile alla pagina di **angolo di inclinazione** pagina con la differenza che il `Slider` elementi compresi tra -90 e 90 gradi. Il [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) file code-behind Centra il testo della pagina e utilizza `Translate` per impostare un centro di inclinazione al centro della pagina. Un breve `SkewDegrees` metodo nella parte inferiore del codice converte l'angolo di inclinazione valori:

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

Con un angolo avvicinarsi positivi o negativi di 90 gradi, la tangente si avvicina a infinito, ma gli angoli fino a circa 80 gradi o scopo possono essere usati:

[![](skew-images/skewangleexperiment-small.png "Schermata di triplo della pagina esperimento angolo di inclinazione")](skew-images/skewangleexperiment-large.png#lightbox "tripla schermata della pagina esperimento angolo di inclinazione")

Una piccola inclinazione orizzontale negativo può simulare testo in corsivo o obliquo, come il **testo obliquo** illustra come. Il [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) classe illustra come farlo:

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

Il `TextAlign` proprietà di `SKPaint` è impostato su `Center`. Senza le eventuali trasformazioni, il `DrawText` chiamare con coordinate (0, 0) posiziona il testo con il centro orizzontale della linea di base nell'angolo superiore sinistro. Il `SkewDegrees` inclina il testo orizzontalmente 20 gradi rispetto alla linea di base. Il `Translate` chiamata viene spostata al centro orizzontale della linea di base del testo al centro dell'area di disegno:

[![](skew-images/obliquetext-small.png "Schermata triplo della pagina di testo obliquo")](skew-images/obliquetext-large.png#lightbox "tripla schermata della pagina di testo obliquo")

Il **inclinare testo Shadow** pagina viene illustrato come utilizzare una combinazione di una scala verticale e inclinazione di 45 gradi per creare un'ombreggiatura del testo che inclina dal testo. Di seguito è la parte pertinente la `PaintSurface` gestore:

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

L'ombreggiatura viene innanzitutto visualizzato e quindi il testo:

[![](skew-images/skewshadowtext1-small.png "Schermata triplo della pagina inclinare testo Shadow")](skew-images/skewshadowtext1-large.png#lightbox "tripla schermata della pagina inclinare il testo con ombreggiatura")

Coordinata verticale passato per il `DrawText` metodo indica la posizione del testo rispetto alla linea di base. Che rappresenta la coordinata verticale stesso utilizzata per il centro di inclinazione. Questa tecnica non funzionerà se la stringa di testo contiene tratti discendenti. Ad esempio, sostituire la parola "complessa" per "Shadow" e 's il risultato:

[![](skew-images/skewshadowtext2-small.png "Schermata triplo della pagina inclinare Shadow testo con una parola alternativa con tratti discendenti")](skew-images/skewshadowtext2-large.png#lightbox "tripla schermata della pagina inclinare Shadow testo con una parola alternativa con tratti discendenti")

L'ombreggiatura e il testo sono sempre allineati alla linea di base, ma solo l'effetto è errato. Per risolvere il problema è necessario ottenere i limiti di testo:

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

Il `Translate` chiamate hanno bisogno di essere regolato dall'altezza del tratti discendenti:

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

Ora l'ombreggiatura si estende dal livello inferiore i tratti discendenti:

[![](skew-images/skewshadowtext3-small.png "Schermata triplo della pagina inclinare testo Shadow rettifiche destinato ai tratti discendenti")](skew-images/skewshadowtext3-large.png#lightbox "tripla schermata della pagina inclinare testo Shadow rettifiche destinato ai tratti discendenti")


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
