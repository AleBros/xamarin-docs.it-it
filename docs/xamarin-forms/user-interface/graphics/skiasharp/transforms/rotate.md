---
title: Trasformazione di rotazione
description: In questo articolo vengono esaminati gli effetti e le animazioni possibili con la trasformazione SkiaSharp rotate e viene illustrato il codice di esempio.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 520c4c3b61049bf17c2c964523714db196da6839
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84132184"
---
# <a name="the-rotate-transform"></a>Trasformazione di rotazione

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Esplorare gli effetti e le animazioni possibili con la trasformazione SkiaSharp rotate_

Con la trasformazione rotazione, gli oggetti grafici SkiaSharp non sono in alcun modo privi del vincolo di allineamento con gli assi orizzontali e verticali:

![](rotate-images/rotateexample.png "Text rotated around a center")

Per ruotare un oggetto grafico intorno al punto (0, 0), SkiaSharp supporta sia un [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single)) metodo che un [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single)) Metodo:

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

Un cerchio di 360 gradi corrisponde a twoπ radianti, quindi è facile eseguire la conversione tra le due unità. Usare qualunque sia la pratica. Tutte le funzioni trigonometriche nella classe .NET [`Math`](xref:System.Math) usano unità di radianti.

La rotazione è in senso orario per aumentare gli angoli. (Anche se la rotazione sul sistema di coordinate cartesiane è in senso antiorario per convenzione, la rotazione in senso orario è coerente con le coordinate Y che aumentano verso il basso come in SkiaSharp). Sono consentiti angoli negativi e angoli superiori a 360 gradi.

Le formule di trasformazione per la rotazione sono più complesse rispetto a quelle per la conversione e la scalabilità. Per un angolo di α, le formule di trasformazione sono:

x ' = x • cos (α) – y • sin (α)   

y ' = x • sin (α) + y • cos (α)

La pagina di **rotazione di base** illustra il `RotateDegrees` metodo. Il file [**BasicRotate.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs) Visualizza un testo con la relativa linea di base centrata sulla pagina e lo ruota in base a un `Slider` con un intervallo compreso tra-360 e 360. Di seguito è illustrata la parte pertinente del `PaintSurface` gestore:

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

Poiché la rotazione è centrata intorno all'angolo superiore sinistro dell'area di disegno, per la maggior parte degli angoli impostati in questo programma, il testo viene ruotato sullo schermo:

[![](rotate-images/basicrotate-small.png "Triple screenshot of the Basic Rotate page")](rotate-images/basicrotate-large.png#lightbox "Triple screenshot of the Basic Rotate page")

Molto spesso è necessario ruotare un elemento centrato attorno a un punto pivot specificato utilizzando queste versioni dei [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single,System.Single,System.Single)) metodi e [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single,System.Single,System.Single)) :

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

La pagina **ruota centrata** è esattamente come la **rotazione di base** , ad eccezione del fatto che la versione espansa di `RotateDegrees` viene usata per impostare il centro della rotazione sullo stesso punto usato per posizionare il testo:

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

Ora il testo ruota intorno al punto usato per posizionare il testo, che è il centro orizzontale della linea di base del testo:

[![](rotate-images/centeredrotate-small.png "Triple screenshot of the Centered Rotate page")](rotate-images/centeredrotate-large.png#lightbox "Triple screenshot of the Centered Rotate page")

Come per la versione centrata del `Scale` metodo, la versione centrata della `RotateDegrees` chiamata è un collegamento. Ecco il metodo:

```csharp
RotateDegrees (degrees, px, py);
```

La chiamata è equivalente a quanto segue:

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

Si noterà che talvolta è possibile combinare `Translate` chiamate con `Rotate` chiamate. Ad esempio, di seguito sono riportate le `RotateDegrees` `DrawText` chiamate e nella pagina **ruota centrata** ;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

La `RotateDegrees` chiamata è equivalente a due `Translate` chiamate e a un oggetto non centrato `RotateDegrees` :

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

La `DrawText` chiamata per visualizzare il testo in una determinata posizione è equivalente a una `Translate` chiamata per tale posizione seguita da `DrawText` al punto (0,0):

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

Le due chiamate consecutive si `Translate` annullano a vicenda:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

Concettualmente, le due trasformazioni vengono applicate nell'ordine opposto a come vengono visualizzate nel codice. La `DrawText` chiamata Visualizza il testo nell'angolo superiore sinistro dell'area di disegno. La `RotateDegrees` chiamata ruota tale testo rispetto all'angolo superiore sinistro. Quindi la `Translate` chiamata sposta il testo al centro dell'area di disegno.

Esistono in genere diversi modi per combinare la rotazione e la traduzione. La pagina di **testo ruotata** crea la seguente visualizzazione:

[![](rotate-images/rotatedtext-small.png "Triple screenshot of the Rotated Text page")](rotate-images/rotatedtext-large.png#lightbox "Triple screenshot of the Rotated Text page")

Ecco il `PaintSurface` gestore della [`RotatedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) classe:

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

I `xCenter` `yCenter` valori e indicano il centro dell'area di disegno. Il `yText` valore è un lieve offset. Questo valore è la coordinata Y necessaria per posizionare il testo in modo che sia effettivamente centrato verticalmente nella pagina. Il `for` ciclo imposta quindi una rotazione in base al centro dell'area di disegno. La rotazione è con incrementi di 30 gradi. Il testo viene disegnato usando il `yText` valore. Il numero di spazi vuoti prima della parola "ruota" nel `text` valore è stato determinato in modo empirico per fare in modo che la connessione tra queste 12 stringhe di testo appaia come dodecagono.

Un modo per semplificare questo codice consiste nell'incrementare l'angolo di rotazione di 30 gradi ogni volta nel ciclo dopo la `DrawText` chiamata. In questo modo si elimina la necessità di chiamare `Save` e `Restore` . Si noti che la `degrees` variabile non viene più usata all'interno del corpo del `for` blocco:

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

È anche possibile usare il formato semplice di `RotateDegrees` anteponendo il ciclo con una chiamata a `Translate` per spostare tutti gli elementi al centro dell'area di disegno:

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

Il `yText` calcolo modificato non è più incorporato `yCenter` . A questo punto `DrawText` , la chiamata Centra verticalmente il testo nella parte superiore dell'area di disegno.

Poiché le trasformazioni vengono applicate concettualmente a fronte di come appaiono nel codice, è spesso possibile iniziare con più trasformazioni globali, seguite da più trasformazioni locali. Questo è spesso il modo più semplice per combinare la rotazione e la traduzione.

Si supponga, ad esempio, di voler creare un oggetto grafico che ruoti attorno al centro in modo analogo a un pianeta che ruota sull'asse. Si vuole però che questo oggetto ruoti al centro dello schermo in modo analogo a un pianeta che ruota intorno al sole.

Questa operazione può essere eseguita posizionando l'oggetto nell'angolo superiore sinistro dell'area di disegno e quindi usando un'animazione per ruotarlo intorno all'angolo. Tradurre quindi l'oggetto orizzontalmente come un raggio orbitale. Applicare ora una seconda rotazione animata, anche intorno all'origine. In questo modo l'oggetto ruota intorno all'angolo. Ora traslare al centro dell'area di disegno.

Ecco il `PaintSurface` gestore che contiene queste chiamate di trasformazione in ordine inverso:

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

I `revolveDegrees` `rotateDegrees` campi e sono animati. Questo programma usa una tecnica di animazione diversa basata sulla Xamarin.Forms [`Animation`](xref:Xamarin.Forms.Animation) classe. Questa classe è descritta nel [capitolo 22 della *creazione di app per dispositivi Xamarin.Forms mobili con * ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)) `OnAppearing` . l'override crea due `Animation` oggetti con i metodi di callback e quindi `Commit` li chiama per una durata dell'animazione:

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

Il primo `Animation` oggetto aggiunge un'animazione `revolveDegrees` da 0 gradi a 360 gradi in 10 secondi. Il secondo aggiunge un'animazione `rotateDegrees` da 0 gradi a 360 gradi ogni 1 secondo e invalida anche la superficie per generare un'altra chiamata al `PaintSurface` gestore. L' `OnDisappearing` override Annulla queste due animazioni:

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

Il **brutto programma di clock analogico** (chiamato così perché un orologio analogico più interessante verrà descritto in un articolo successivo) usa la rotazione per creare i segni di minuto e ora dell'orologio e per ruotare le lancette. Il programma disegna l'orologio usando un sistema di coordinate arbitrario basato su un cerchio centrato al punto (0, 0) con un raggio di 100. Usa la conversione e il ridimensionamento per espandere e centrare il cerchio nella pagina.

Le `Translate` `Scale` chiamate e si applicano a livello globale al clock, quindi questi sono i primi a essere chiamati dopo l'inizializzazione degli `SKPaint` oggetti:

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

Sono presenti 60 contrassegni di due dimensioni diverse che devono essere disegnate in un cerchio intorno al clock. La `DrawCircle` chiamata disegna il cerchio in corrispondenza del punto (0, – 90), che in relazione al centro del clock corrisponde a 12:00. La `RotateDegrees` chiamata incrementa l'angolo di rotazione di 6 gradi dopo ogni segno di graduazione. La `angle` variabile viene utilizzata esclusivamente per determinare se viene disegnato un cerchio o un cerchio di grandi dimensioni:

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

Infine, il `PaintSurface` gestore ottiene l'ora corrente e calcola i gradi di rotazione per l'ora, il minuto e il secondo. Ogni mano viene disegnata nella posizione 12:00 in modo che l'angolo di rotazione sia relativo a quello seguente:

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

Il clock è certamente funzionale anche se le mani sono piuttosto rudimentali:

[![](rotate-images/uglyanalogclock-small.png "Triple screenshot of the Ugly Analog Clock Text page")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")

Per un orologio più interessante, vedere l'articolo relativo ai [**dati del percorso SVG in SkiaSharp**](../curves/path-data.md).

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
