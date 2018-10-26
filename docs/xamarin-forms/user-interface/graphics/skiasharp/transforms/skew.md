---
title: Trasformazione di inclinazione
description: Questo articolo illustra come la trasformazione di inclinazione creare gli oggetti grafici inclinati in SkiaSharp e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: ecb07c69b7720f77401bf9bf454ee4b0248ad238
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113820"
---
# <a name="the-skew-transform"></a>Trasformazione di inclinazione

_Vedere come la trasformazione di inclinazione creare gli oggetti grafici inclinati in SkiaSharp_

In SkiaSharp, la trasformazione di inclinazione inclina oggetti grafici, ad esempio l'ombreggiatura in questa immagine:

![](skew-images/skewexample.png "Un esempio di inclinazione dal programma inclinare il testo con ombreggiatura")

L'inclinazione Trasforma un rettangolo in un parallelogramma, ma un'ellisse asimmetriche è ancora un'ellisse.

Sebbene xamarin. Forms definisce le proprietà di traslazione, ridimensionamento e rotazioni, non è presente alcuna proprietà corrispondente in xamarin. Forms per l'inclinazione.

Il [ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single)) metodo `SKCanvas` accetta due argomenti per l'inclinazione orizzontale e verticale di una differenza:

```csharp
public void Skew (Single xSkew, Single ySkew)
```

Una seconda [ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint)) metodo combina tali argomenti in una singola `SKPoint` valore:

```csharp
public void Skew (SKPoint skew)
```

Tuttavia, è improbabile che si userà uno di questi due metodi in isolamento.

Il **inclinare sperimentare** pagina consente di sperimentare inclina i valori compresi tra -10 e 10. Una stringa di testo è posizionata nell'angolo superiore sinistro della pagina con dello sfasamento del valori ottenuti dai due `Slider` elementi. Di seguito è riportato il `PaintSurface` gestore nel [ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) classe:

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

I valori del `xSkew` argomento spostare parte inferiore del testo a destra per i valori positivi o a sinistra per i valori negativi. I valori di `ySkew` shift destra del testo verso il basso per i valori positivi o per i valori negativi:

[![](skew-images/skewexperiment-small.png "Tripla screenshot della pagina dell'esperimento inclinare")](skew-images/skewexperiment-large.png#lightbox "tripla screenshot della pagina dell'esperimento inclinare")

Se il `xSkew` valore è il corrispondente negativo del `ySkew` valore, il risultato è la rotazione, ma anche ridimensionata un po' come indica la visualizzazione della piattaforma UWP.

Le formule di trasformazione sono i seguenti:

x' = x + xSkew · y

y' = · ySkew x + y

Ad esempio, per un numero positivo `xSkew` valore trasformato `x'` valore aumenta man mano che `y` aumenta. Che è quello che determina l'inclinazione.

Se un pixel di triangolo 200 larghezza e 100 pixel in altezza viene posizionato con relativo angolo superiore sinistro in corrispondenza del punto (0, 0) e viene eseguito il rendering con un `xSkew` valore pari a 1,5, i risultati parallelogramma seguenti:

![](skew-images/skeweffect.png "L'effetto della trasformazione di inclinazione su un rettangolo")

Le coordinate del bordo inferiore hanno `y` valori pari a 100, pertanto è spostati 150 pixel a destra.

Per i valori di diverso da zero `xSkew` o `ySkew`, solo il punto (0, 0) rimane invariato. Tale punto può essere considerato il centro di inclinazione. Se è necessario, ovvero in genere il caso, il centro di inclinazione da qualche altro, è presente alcun `Skew` metodo che consente di corrispondenti. È necessario combinare in modo esplicito `Translate` viene chiamato con il `Skew` chiamare. Per allineare al centro fuorvianti alla `px` e `py`, effettuare le chiamate seguenti:

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

Le formule di trasformazione compositi sono:

x' = x + xSkew · (y: py)

y' = · ySkew (x – px) + y

Se `ySkew` è uguale a zero, il `px` valore non viene utilizzato. Il valore è irrilevante e analogamente `ySkew` e `py`.

Ritieni più a proprio agio specificando inclinazione come un angolo di inclinazione, ad esempio l'angolo α nella figura seguente:

![](skew-images/skewangleeffect.png "L'effetto della trasformazione di inclinazione su un rettangolo con un angolo di inclinazione indicato")

Il rapporto tra il turno 150 pixel a 100 pixel verticale corrisponde alla tangente tale angolo, in questo esempio 56.3 gradi.

Il file XAML del **esperimento angolo di inclinazione** pagina è simile al **angolo di inclinazione** pagina con la differenza che la `Slider` elementi compresi tra-90 gradi e 90 gradi. Il [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs) file code-behind Centra il testo della pagina e Usa `Translate` per impostare un centro di inclinazione al centro della pagina. Un valore breve `SkewDegrees` metodo nella parte inferiore del codice esegue la conversione degli angoli per inclinare valori:

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

Come si avvicina a un angolo positivi o negativi di 90 gradi, la tangente sta per raggiungere un numero infinito, ma gli angoli fino a circa 80 gradi o operazione possono essere utilizzati:

[![](skew-images/skewangleexperiment-small.png "Tripla screenshot della pagina dell'esperimento angolo di inclinazione")](skew-images/skewangleexperiment-large.png#lightbox "tripla screenshot della pagina dell'esperimento angolo di inclinazione")

Un'inclinazione orizzontale negativa piccola può simulare testo in corsivo o obliquo, come le **obliquo testo** illustra come. Il [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs) classe illustra come farlo:

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

Il `TextAlign` proprietà di `SKPaint` è impostata su `Center`. Senza le eventuali trasformazioni, il `DrawText` chiamare con le coordinate di (0, 0) sarebbe di collocare il testo con al centro orizzontale della linea di base nell'angolo superiore sinistro. Il `SkewDegrees` inclina il testo orizzontalmente 20 gradi rispetto alla linea di base. Il `Translate` chiamata passa al centro orizzontale della linea di base del testo al centro dell'area di disegno:

[![](skew-images/obliquetext-small.png "Tripla screenshot della pagina di testo obliquo")](skew-images/obliquetext-large.png#lightbox "tripla screenshot della pagina di testo obliquo")

Il **inclinare il testo con ombreggiatura** pagina illustra come usare una combinazione di una scala di inclinazione e verticale di 45 gradi per rendere un'ombreggiatura del testo che inclina lontano da testo. Ecco la parte pertinente del `PaintSurface` gestore:

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

[![](skew-images/skewshadowtext1-small.png "Tripla screenshot della pagina inclinare il testo con ombreggiatura")](skew-images/skewshadowtext1-large.png#lightbox "tripla screenshot della pagina inclinare il testo con ombreggiatura")

La coordinata verticale passato al `DrawText` metodo indica la posizione del testo rispetto alla linea di base. Che rappresenta la coordinata verticale stesso utilizzata per il centro di inclinazione. Questa tecnica non funzionerà se la stringa di testo contiene tratti discendenti. Ad esempio, sostituire la parola "complessa" per "Ombra" ed ecco il risultato:

[![](skew-images/skewshadowtext2-small.png "Tripla screenshot della pagina inclinare il testo con ombreggiatura con una parola alternativa con tratti discendenti")](skew-images/skewshadowtext2-large.png#lightbox "tripla screenshot della pagina inclinare il testo con ombreggiatura con una parola alternativa con tratti discendenti")

Il testo e l'ombreggiatura ancora sono allineati in linea di base, ma l'effetto aspetto è errato. Per risolvere il problema, è necessario ottenere i limiti di testo:

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

Il `Translate` chiamate devono essere modificate dall'altezza dei tratti discendenti:

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

A questo punto l'ombreggiatura estende nella parte inferiore di questi tratti discendenti:

[![](skew-images/skewshadowtext3-small.png "Tripla screenshot della pagina inclinare il testo con ombreggiatura con regolazioni destinato ai tratti discendenti")](skew-images/skewshadowtext3-large.png#lightbox "tripla screenshot della pagina inclinare il testo con ombreggiatura con regolazioni destinato ai tratti discendenti")


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
