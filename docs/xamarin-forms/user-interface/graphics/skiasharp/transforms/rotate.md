---
title: Trasformazione di rotazione
description: Questo articolo illustra gli effetti e animazioni possibili con la trasformazione di rotazione SkiaSharp e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 399f19ba4ec1ed8494e8269fc4cd0682b466a31a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187743"
---
# <a name="the-rotate-transform"></a>Trasformazione di rotazione

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Esplorare gli effetti e animazioni possibili con la trasformazione di rotazione in SkiaSharp_

Con la trasformazione di rotazione, gli oggetti di grafica SkiaSharp liberati del vincolo di allineamento con gli assi orizzontali e verticali:

![](rotate-images/rotateexample.png "Testo ruotato intorno a un centro")

Per ruotare un oggetto grafico intorno al punto (0, 0), SkiaSharp supporta sia un [ `RotateDegrees` ](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single)) metodo e un [ `RotateRadians` ](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single)) metodo:

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

Un cerchio di 360 gradi equivale twoπ radianti, in modo che risulti semplice eseguire la conversione tra le due unità. Usare a seconda del valore è comodo. Tutte le funzioni trigonometriche in .NET [ `Math` ](xref:System.Math) classe usare unità di radianti.

È in senso orario per incrementare gli angoli di rotazione. (Anche se in senso antiorario per convenzione, rotazione sul sistema di coordinate cartesiano rotazione in senso orario è coerenza con le coordinate Y aumentando inagibili come SkiaSharp). Il valore negativo degli angoli e angoli superiori a 360 gradi sono consentiti.

Le formule di trasformazione di rotazione sono più complesse rispetto a quelli per la traslazione e la scala. Per un angolo di α, le formule di trasformazione sono:

x' = x•cos(α) – y•sin(α)   

y` = x•sin(α) + y•cos(α)

Il **base ruotare** illustra come il `RotateDegrees` (metodo). Il [ **BasicRotate.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs) file Visualizza il testo con linea di base centrata nella pagina e ruota che si basa su un `Slider` con una gamma di – 360 a 360. Ecco la parte in questione di `PaintSurface` gestore:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

Poiché la rotazione è incentrata sull'angolo superiore sinistro dell'area di disegno, per la maggior parte degli angoli impostato in questo programma, il testo viene ruotato fuori dallo schermo:

[![](rotate-images/basicrotate-small.png "Tripla screenshot della pagina base ruotare")](rotate-images/basicrotate-large.png#lightbox "tripla screenshot della pagina base ruotare")

Molto spesso è opportuno eseguire la rotazione qualcosa incentrato su un punto di perno specificate usando queste versioni del [ `RotateDegrees` ](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single,System.Single,System.Single)) e [ `RotateRadians` ](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single,System.Single,System.Single)) metodi:

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

Il **centrato ruotare** pagina è analoga al **base ruota** ad eccezione del fatto che versione espansa del `RotateDegrees` consente di impostare il centro della rotazione allo stesso punto di usati per posizionare il testo:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

A questo punto il testo ruota intorno al punto di usati per posizionare il testo, ovvero al centro orizzontale della linea di base del testo:

[![](rotate-images/centeredrotate-small.png "Tripla screenshot della pagina centrato ruotare")](rotate-images/centeredrotate-large.png#lightbox "tripla screenshot della pagina al centro di rotazione")

Come con la versione centrata del `Scale` metodo, la versione centrata del `RotateDegrees` chiamata è una scelta rapida. Ecco il metodo:

```csharp
RotateDegrees (degrees, px, py);
```

Questa chiamata è equivalente alla seguente:

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

Si scoprirà che in alcuni casi è possibile combinare `Translate` chiamate con `Rotate` chiamate. Ad esempio, ecco la `RotateDegrees` e `DrawText` chiamate nel **centrato ruotare** pagina;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

Il `RotateDegrees` chiamata è equivalente a due `Translate` chiamate e non centrato `RotateDegrees`:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

Il `DrawText` chiamata per visualizzare il testo in una determinata posizione è equivalente a un `Translate` chiamare per quel percorso seguito da `DrawText` in corrispondenza del punto (0, 0):

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

I due consecutivi `Translate` annullano reciprocamente chiamate:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

Concettualmente, due trasformazioni vengono applicate nell'ordine opposto a come appaiono nel codice. Il `DrawText` chiamata visualizza il testo nell'angolo superiore sinistro dell'area di disegno. Il `RotateDegrees` chiamata Ruota testo rispetto all'angolo superiore sinistro. Il `Translate` chiamata consente di spostare il testo al centro dell'area di disegno.

In genere esistono diversi modi per combinare rotazione e traslazione. Il **testo ruotato** pagina Crea la visualizzazione seguente:

[![](rotate-images/rotatedtext-small.png "Tripla screenshot della pagina di testo ruotato")](rotate-images/rotatedtext-large.png#lightbox "tripla screenshot della pagina di testo ruotato")

Di seguito è riportato il `PaintSurface` gestore del [ `RotatedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) classe:

```csharp
static readonly string text = "    ROTATE";
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 72
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float yText = yCenter - textBounds.Height / 2 - textBounds.Top;

        for (int degrees = 0; degrees < 360; degrees += 30)
        {
            canvas.Save();
            canvas.RotateDegrees(degrees, xCenter, yCenter);
            canvas.DrawText(text, xCenter, yText, textPaint);
            canvas.Restore();
        }
    }
}

```

Il `xCenter` e `yCenter` valori indicano il centro dell'area di disegno. Il `yText` valore è un po' offset rispetto a quello. Questo valore è la coordinata Y necessaria per posizionare il testo in modo che sia effettivamente verticalmente centrato nella pagina. Il `for` ciclo imposta quindi una rotazione di base il centro dell'area di disegno. La rotazione è in incrementi di 30 gradi. Il testo viene visualizzato utilizzando il `yText` valore. Il numero di spazi vuoti prima della parola "Ruota" nel `text` è stato determinato valore in modo empirico per stabilire la connessione tra le stringhe di 12 testo sembra essere un dodecagon.

Un modo per semplificare questo codice è per incrementare l'angolo di rotazione di 30 gradi ogni volta che il ciclo dopo il `DrawText` chiamare. Questo elimina la necessità per le chiamate a `Save` e `Restore`. Si noti che il `degrees` variabile non viene più usata all'interno del corpo del `for` blocco:

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

È anche possibile usare il modulo di semplice `RotateDegrees` anteponendo il ciclo con una chiamata a `Translate` per spostare tutti gli elementi al centro dell'area di disegno:

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

Modificato `yText` calcolo non riprende più `yCenter`. A questo punto il `DrawText` chiamata Centra il testo verticalmente nella parte superiore dell'area di disegno.

Poiché le trasformazioni vengono applicate a livello concettuale vengono mantenuti come appaiono nel codice, è spesso possibile trasformazioni per iniziare più globale, seguite da altre trasformazioni locale. Ciò è spesso il modo più semplice per combinare rotazione e traslazione.

Si supponga, ad esempio disegnare un oggetto grafico che ruota intorno al proprio centro molto simile a un mondo di rotazione sul proprio asse. Ma si vuole anche in questo oggetto ruotano intorno al centro dello schermo molto simile a un mondo vengono disposti intorno il sole.

Questo scopo, è possibile posizionare l'oggetto nell'angolo superiore sinistro dell'area di disegno e quindi usare un'animazione per l'angolo corrispondente intorno all'asse. Successivamente, convertire l'oggetto in senso orizzontale come un raggio orbital. A questo punto si applicano una seconda rotazione animata, anche intorno all'origine. In questo modo l'oggetto ruotano intorno all'angolo. Convertire a questo punto al centro dell'area di disegno.

Di seguito è riportato il `PaintSurface` gestore che contiene queste trasforma le chiamate in ordine inverso:

```csharp
float revolveDegrees, rotateDegrees;
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Red
    })
    {
        // Translate to center of canvas
        canvas.Translate(info.Width / 2, info.Height / 2);

        // Rotate around center of canvas
        canvas.RotateDegrees(revolveDegrees);

        // Translate horizontally
        float radius = Math.Min(info.Width, info.Height) / 3;
        canvas.Translate(radius, 0);

        // Rotate around center of object
        canvas.RotateDegrees(rotateDegrees);

        // Draw a square
        canvas.DrawRect(new SKRect(-50, -50, 50, 50), fillPaint);
    }
}
```

Il `revolveDegrees` e `rotateDegrees` campi vengono animati. Questo programma Usa una tecnica di animazione differenti basata su xamarin. Forms [ `Animation` ](xref:Xamarin.Forms.Animation) classe. (Questa classe viene descritta [capitolo 22 *creazione di App per dispositivi mobili con xamarin. Forms*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)) i `OnAppearing` override vengono creati due `Animation` gli oggetti con i metodi di callback e lo chiama `Commit` su di essi per una durata animazione:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    new Animation((value) => revolveDegrees = 360 * (float)value).
        Commit(this, "revolveAnimation", length: 10000, repeat: () => true);

    new Animation((value) =>
    {
        rotateDegrees = 360 * (float)value;
        canvasView.InvalidateSurface();
    }).Commit(this, "rotateAnimation", length: 1000, repeat: () => true);
}
```

Il primo `Animation` aggiunge un'animazione oggetto `revolveDegrees` da 0 a 360 gradi oltre 10 secondi. Il secondo aggiunge un'animazione `rotateDegrees` da 0 a 360 gradi ogni 1 secondo e anche invalida l'area per generare un'altra chiamata al `PaintSurface` gestore. Il `OnDisappearing` override Annulla queste due animazioni:

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

Il **confusa orologio analogico** programma (le cosiddette perché un orologio analogico più interessante verrà descritte in un articolo successivo) utilizza la rotazione per disegnare i segni di minuti e ora dell'orologio e ruotare il movimento delle lancette. Il programma consente di disegnare l'orologio utilizzando un sistema di coordinate arbitrario basato su un cerchio è centrato al punto (0, 0) con un raggio pari a 100. Usa traslazione e ridimensionamento per espandere e allineare al centro il cerchio nella pagina.

Il `Translate` e `Scale` chiamate applicano a livello globale in base all'orologio, in modo che questi sono quelli da chiamare dopo l'inizializzazione del primo il `SKPaint` oggetti:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    using (SKPaint fillPaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.Color = SKColors.Black;
        strokePaint.StrokeCap = SKStrokeCap.Round;

        fillPaint.Style = SKPaintStyle.Fill;
        fillPaint.Color = SKColors.Gray;

        // Transform for 100-radius circle centered at origin
        canvas.Translate(info.Width / 2f, info.Height / 2f);
        canvas.Scale(Math.Min(info.Width / 200f, info.Height / 200f));
        ...
    }
}
```

Sono presenti 60 segni di due dimensioni diverse che devono essere disegnate in un cerchio di notte. Il `DrawCircle` chiamata consente di disegnare il cerchio in corrispondenza del punto (0, -90), che, rispetto al centro dell'orologio, corrisponde a 12:00. Il `RotateDegrees` chiamata incrementa l'angolo di rotazione di gradi 6 dopo ogni segno di graduazione. Il `angle` variabile viene utilizzata esclusivamente per determinare se viene disegnato un piccolo cerchio o un cerchio grande:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        // Hour and minute marks
        for (int angle = 0; angle < 360; angle += 6)
        {
            canvas.DrawCircle(0, -90, angle % 30 == 0 ? 4 : 2, fillPaint);
            canvas.RotateDegrees(6);
        }
    ...
    }
}
```

Infine, il `PaintSurface` gestore ottiene l'ora corrente e calcola i gradi di rotazione per l'ora, minuto e secondo mani. Ogni indicatore viene disegnato nella posizione GMT+12.00 in modo che l'angolo di rotazione è relativo alla che:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        DateTime dateTime = DateTime.Now;

        // Hour hand
        strokePaint.StrokeWidth = 20;
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawLine(0, 0, 0, -50, strokePaint);
        canvas.Restore();

        // Minute hand
        strokePaint.StrokeWidth = 10;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawLine(0, 0, 0, -70, strokePaint);
        canvas.Restore();

        // Second hand
        strokePaint.StrokeWidth = 2;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Second);
        canvas.DrawLine(0, 10, 0, -80, strokePaint);
        canvas.Restore();
    }
}
```

L'orologio è certamente funzionale, anche se il movimento delle lancette è piuttosto rudimentale:

[![](rotate-images/uglyanalogclock-small.png "Screenshot della pagina di testo di orologio analogico confusa è pari al triplo")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")

Per un orologio più interessante, vedere l'articolo [ **dati del tracciato SVG in SkiaSharp**](../curves/path-data.md).

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
