---
title: La trasformazione rotativa
description: Esplorare gli effetti e animazioni possibili con la trasformazione di rotazione SkiaSharp
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: 354844f69c0b8755a15a3325c39e35b97d44f94d
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
# <a name="the-rotate-transform"></a>La trasformazione rotativa

_Esplorare gli effetti e animazioni possibili con la trasformazione di rotazione SkiaSharp_

Con la trasformazione di rotazione, gli oggetti di grafica SkiaSharp interrompere gratuiti del vincolo di allineamento con gli assi orizzontali e verticali:

![](rotate-images/rotateexample.png "Testo ruotato lungo un centro")

Per la rotazione di un oggetto grafico intorno al punto (0, 0), SkiaSharp supporta sia un [ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/) (metodo) e un [ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/) metodo:

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

Un cerchio di 360 gradi equivale 2 π radianti, in modo facile eseguire la conversione tra le due unità. Utilizzare a seconda del valore è utile. Tutte le funzioni trigonometriche statica [ `Math` ](https://developer.xamarin.com/api/type/System.Math/) classe utilizzare unità di radianti.

La rotazione è in senso orario per aumentare gli angoli. (Sebbene rotazione sul sistema di coordinate cartesiano in senso antiorario per convenzione, rotazione in senso orario è coerenza con le coordinate Y aumentando corso verso il basso). Negativo angoli e degli angoli maggiori rispetto a quelli consentiti 360 gradi.

Le formule di trasformazione di rotazione sono più complesse rispetto a quelli per la traduzione e la scala. Per un angolo di α, le formule di trasformazione sono:

x' = x•cos(α) – y•sin(α)   

y` = x•sin(α) + y•cos(α)

Il **ruota base** è illustrato il `RotateDegrees` metodo. Il [ `BasicRotate.xaml.cs` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs) file Visualizza il testo con linea di base centrata nella pagina e in base alle ruota un `Slider` con un intervallo di – 360 a 360. Di seguito è la parte in questione di `PaintSurface` gestore:

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

Poiché la rotazione è incentrata sull'angolo superiore sinistro dell'area di disegno per la maggior parte degli angoli impostato in questo programma, il testo viene ruotato sullo schermo:

[![](rotate-images/basicrotate-small.png "Schermata triplo della pagina base ruotare")](rotate-images/basicrotate-large.png#lightbox "tripla schermata della pagina base ruotare")

Molto spesso si desideri ruotare un elemento basato su un punto pivot specificata tramite queste versioni del [ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/System.Single/System.Single/) e [ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/System.Single/System.Single/) metodi:

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

Il **centrata ruotare** pagina è analoga il **ruota base** ad eccezione del fatto che versione espansa del `RotateDegrees` utilizzato per impostare il centro di rotazione allo stesso punto utilizzato per posizionare il testo:

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

Ora il testo ruota intorno al punto utilizzato per posizionare il testo, ovvero il centro della linea di base del testo orizzontale:

[![](rotate-images/centeredrotate-small.png "Schermata triplo della pagina al centro di rotazione")](rotate-images/centeredrotate-large.png#lightbox "tripla schermata della pagina al centro di rotazione")

Come con la versione di centrato il `Scale` (metodo), la versione al centro del `RotateDegrees` chiamata è una scelta rapida:

```csharp
RotateDegrees (degrees, px, py);
```

L'espressione equivale alla seguente:

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

È possibile osservare che in alcuni casi è possibile combinare `Translate` chiamate con `Rotate` chiamate. Ad esempio, ecco la `RotateDegrees` e `DrawText` chiama **centrata ruotare** pagina;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

Il `RotateDegrees` chiamata è equivalente a due `Translate` chiamate e non centrata `RotateDegrees`:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

Il `DrawText` è equivalente alla chiamata per visualizzare il testo in una determinata posizione un `Translate` chiamare per quel percorso seguito da `DrawText` in corrispondenza del punto (0, 0):

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

Concettualmente, le due trasformazioni vengono applicate nell'ordine opposto come vengono visualizzati nel codice. Il `DrawText` chiamata viene visualizzato il testo nell'angolo superiore sinistro dell'area di disegno. Il `RotateDegrees` chiamata consente di ruotare il testo rispetto all'angolo superiore sinistro. Il `Translate` chiamata consente di spostare il testo al centro dell'area di disegno.

Esistono diversi modi per combinare rotazione e conversione. Il **il testo ruotato** pagina Crea la visualizzazione seguente:

[![](rotate-images/rotatedtext-small.png "Schermata di triplo della pagina di testo ruotato")](rotate-images/rotatedtext-large.png#lightbox "tripla schermata della pagina di testo ruotato")

Ecco il `PaintSurface` gestore del [ `RotatedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) classe:

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

Il `xCenter` e `yCenter` i valori indicano il centro dell'area di disegno. Il `yText` valore è un po' offset rispetto a quello. Indica la coordinata Y necessaria per posizionare il testo in modo che sia effettivamente verticalmente centrato nella pagina. Il `for` ciclo imposta quindi una rotazione centrata nell'area dell'area di disegno. La rotazione è in incrementi di 30 gradi. Il testo viene disegnato utilizzando il `yText` valore. Il numero di spazi vuoti prima della parola "Ruota" il `text` è stato determinato valore in modo empirico per stabilire la connessione tra le stringhe di 12 testo sembra essere un dodecagon.

Per semplificare il codice è possibile incrementare l'angolo di rotazione di 30 gradi ogni volta che il ciclo dopo il `DrawText` chiamare. Non è necessario per le chiamate a `Save` e `Restore`. Si noti che il `degrees` variabile non viene più utilizzata all'interno del corpo del `for` blocco:

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

È inoltre possibile utilizzare il modulo di semplice `RotateDegrees` anteponendo il ciclo con una chiamata a `Translate` per spostare tutti gli elementi al centro dell'area di disegno:

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

Modificato `yText` calcolo non è più incorpora `yCenter`. A questo punto il `DrawText` chiamata Centra il testo verticale nella parte superiore dell'area di disegno.

Poiché le trasformazioni vengono applicate a livello concettuale invece di come vengono visualizzati nel codice, è spesso possibile trasformazioni è più globale, seguite da altre trasformazioni locale. Questo è spesso il modo più semplice per combinare rotazione e conversione.

Si supponga, ad esempio disegnare un oggetto grafico che ruota intorno al proprio centro Analogamente a un mondo rotazione sul suo asse. Ma anche che l'oggetto da ruotare attorno al centro dello schermo analogamente a un mondo in cui vengono disposti intorno al sole.

È possibile farlo posizionando l'oggetto nell'angolo superiore sinistro dell'area di disegno, e quindi utilizzando un'animazione di rotazione, l'angolo corrispondente. Successivamente, convertire l'oggetto in senso orizzontale come un raggio orbital. Applicare una seconda rotazione animata anche intorno all'origine. In questo modo l'oggetto riguardano l'angolo. Tradurre ora al centro dell'area di disegno.

Ecco il `PaintSurface` gestore che include queste trasforma le chiamate in ordine inverso:

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

Il `revolveDegrees` e `rotateDegrees` campi sono animati. Questo programma utilizza una tecnica di animazione diversi in base a di xamarin. Forms `Animation` classe. (Questa classe viene descritta nella [capitolo 22 *la creazione di App per dispositivi mobili con xamarin. Forms*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)) il `OnAppearing` override vengono create due `Animation` gli oggetti con metodi di callback e quindi chiama `Commit` su di essi per una durata animazione:

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

Il primo `Animation` oggetto aggiunge un'animazione `revolveDegrees` da 0 a 360 gradi più di 10 secondi. Aggiunge un'animazione secondo `rotateDegrees` da 0 a 360 gradi ogni 1 secondo e anche invalida l'area per generare un'altra chiamata al `PaintSurface` gestore. Il `OnDisappearing` override Annulla queste due animazioni:

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

Il **propriamente orologio analogico** programma (così definito perché un orologio analogico più interessante verrà descritta in un articolo più avanti) utilizza rotazione per disegnare i segni di minuto e all'ora dell'orologio e ruotare le lancette. Il programma Disegna l'orologio utilizzando un sistema di coordinate arbitrario basato su un cerchio centrato in corrispondenza del punto (0, 0) con un raggio pari a 100. Usa traduzione e la scalabilità per espandere e allineare al centro il cerchio nella pagina.

Il `Translate` e `Scale` chiamate si applicano globalmente all'orologio, di quelli sono quelli prima di essere chiamata dopo l'inizializzazione del `SKPaint` oggetti:

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

```csharp
There are 60 marks of two different sizes that must be drawn in a circle around the clock. The `DrawCircle` call draws that circle at the point (0, –90), which relative to the center of the clock corresponds to 12:00. The `RotateDegrees` call increments the rotation angle by 6 degrees after every tick mark. The `angle` variable is used solely to determine if a large circle or a small circle is drawn:

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

Infine, il `PaintSurface` gestore ottiene l'ora corrente e calcola i gradi di rotazione per l'ora, minuto e secondo mani. Ogni indicatore viene disegnato nella posizione 12:00, in modo che l'angolo di rotazione è relativo che:

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

L'orologio è certamente funzionale nonostante gli indicatori sono piuttosto essenziale è costituita dal:

[![](rotate-images/uglyanalogclock-small.png "Schermata della pagina di testo di orologio analogico propriamente tripla")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
