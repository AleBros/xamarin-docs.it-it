---
title: ''
description: In questo articolo viene illustrato come aggiungere un'animazione alla grafica di SkiaSharp nelle Xamarin.Forms applicazioni e come illustrato nel codice di esempio.
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9a59f65655772768860ce29128f14a48641abc26
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84134277"
---
# <a name="basic-animation-in-skiasharp"></a>Animazione di base in SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Scopri come animare la grafica di SkiaSharp_

È possibile animare la grafica SkiaSharp in, facendo in Xamarin.Forms modo che il `PaintSurface` metodo venga chiamato periodicamente, ogni volta che si disegnano i grafici in modo leggermente diverso. Ecco un'animazione illustrata più avanti in questo articolo con circoli concentrici che apparentemente si espandono dal centro:

![](animation-images/animationexample.png "Several concentric circles seemingly expanding from the center")

La pagina di **ellisse pulsante** nel programma [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) anima i due assi di un'ellisse in modo che sembri palpitante ed è anche possibile controllare la frequenza di questa pulsazione. Il file [**PulsatingEllipsePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) crea un'istanza di Xamarin.Forms `Slider` e un oggetto `Label` per visualizzare il valore corrente del dispositivo di scorrimento. Si tratta di un modo comune per integrare un `SKCanvasView` con altre Xamarin.Forms Visualizzazioni:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.PulsatingEllipsePage"
             Title="Pulsating Ellipse">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Slider x:Name="slider"
                Grid.Row="0"
                Maximum="10"
                Minimum="0.1"
                Value="5"
                Margin="20, 0" />

        <Label Grid.Row="1"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Cycle time = {0:F1} seconds'}"
               HorizontalTextAlignment="Center" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Il file code-behind crea un'istanza `Stopwatch` di un oggetto che funge da clock a precisione elevata. L' `OnAppearing` override imposta il `pageIsActive` campo su `true` e chiama un metodo denominato `AnimationLoop` . L' `OnDisappearing` override imposta il `pageIsActive` campo su `false` :

```csharp
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float scale;            // ranges from 0 to 1 to 0

public PulsatingEllipsePage()
{
    InitializeComponent();
}

protected override void OnAppearing()
{
    base.OnAppearing();
    pageIsActive = true;
    AnimationLoop();
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    pageIsActive = false;
}
```

Il `AnimationLoop` metodo avvia l'oggetto `Stopwatch` e quindi esegue il ciclo mentre `pageIsActive` è `true` . Si tratta essenzialmente di un "ciclo infinito" mentre la pagina è attiva, ma non causa il blocco del programma perché il ciclo termina con una chiamata a `Task.Delay` con l' `await` operatore, che consente ad altre parti della funzione Program. L'argomento per `Task.Delay` ne determina il completamento dopo 1/30 secondi. Definisce la frequenza dei fotogrammi dell'animazione.

```csharp
async Task AnimationLoop()
{
    stopwatch.Start();

    while (pageIsActive)
    {
        double cycleTime = slider.Value;
        double t = stopwatch.Elapsed.TotalSeconds % cycleTime / cycleTime;
        scale = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;
        canvasView.InvalidateSurface();
        await Task.Delay(TimeSpan.FromSeconds(1.0 / 30));
    }

    stopwatch.Stop();
}

```

Il `while` ciclo inizia ottenendo un'ora di ciclo da `Slider` . Si tratta di un intervallo di tempo in secondi, ad esempio 5. La seconda istruzione calcola un valore di `t` per *Time*. Per un valore `cycleTime` di 5, `t` aumenta da 0 a 1 ogni 5 secondi. L'argomento della `Math.Sin` funzione nella seconda istruzione è compreso tra 0 e 2 π ogni 5 secondi. La `Math.Sin` funzione restituisce un valore compreso tra 0 e 1 fino a 0 e quindi a &ndash; 1 e 0 ogni 5 secondi, ma con valori che cambiano più lentamente quando il valore è prossimo a 1 o-1. Il valore 1 viene aggiunto in modo che i valori siano sempre positivi, quindi è diviso per 2, quindi i valori sono compresi tra 1/2 e 1 e 1/2 da 0 a 1/2, ma più lenti quando il valore è di circa 1 e 0. Viene archiviato nel `scale` campo e l'oggetto `SKCanvasView` viene invalidato.

Il `PaintSurface` metodo usa questo `scale` valore per calcolare i due assi dell'ellisse:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float maxRadius = 0.75f * Math.Min(info.Width, info.Height) / 2;
    float minRadius = 0.25f * maxRadius;

    float xRadius = minRadius * scale + maxRadius * (1 - scale);
    float yRadius = maxRadius * scale + minRadius * (1 - scale);

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.Color = SKColors.Blue;
        paint.StrokeWidth = 50;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);

        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.SkyBlue;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
    }
}
```

Il metodo calcola un raggio massimo in base alle dimensioni dell'area di visualizzazione e un raggio minimo in base al raggio massimo. Il `scale` valore viene animato tra 0 e 1 e torna a 0, quindi il metodo lo usa per calcolare un oggetto `xRadius` e che è compreso `yRadius` tra `minRadius` e `maxRadius` . Questi valori vengono usati per creare e riempire un'ellisse:

[![](animation-images/pulsatingellipse-small.png "Triple screenshot of the Pulsating Ellipse page")](animation-images/pulsatingellipse-large.png#lightbox "Triple screenshot of the Pulsating Ellipse page")

Si noti che l' `SKPaint` oggetto viene creato in un `using` blocco. Analogamente a molte classi SkiaSharp `SKPaint` derivano da `SKObject` , che deriva da `SKNativeObject` , che implementa l' [`IDisposable`](xref:System.IDisposable) interfaccia. `SKPaint`esegue l'override del `Dispose` metodo per rilasciare le risorse non gestite.

 `SKPaint`L'inserimento di un `using` blocco garantisce che `Dispose` venga chiamato alla fine del blocco per liberare le risorse non gestite. Questo problema si verifica quando la memoria usata dall' `SKPaint` oggetto viene liberata dal Garbage Collector .NET, ma nel codice di animazione è preferibile essere proattiva per liberare memoria in modo più ordinato.

 Una soluzione migliore in questo caso particolare consiste nel creare due `SKPaint` oggetti una volta e salvarli come campi.

Questo è il risultato dell'animazione dei **cerchi in espansione** . La [`ExpandingCirclesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) classe inizia definendo diversi campi, incluso un `SKPaint` oggetto:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    const double cycleTime = 1000;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float t;
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke
    };

    public ExpandingCirclesPage()
    {
        Title = "Expanding Circles";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ...
}
```

Questo programma usa un approccio diverso per l'animazione in base al Xamarin.Forms `Device.StartTimer` metodo. Il `t` campo viene animato da 0 a 1 ogni `cycleTime` millisecondi:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            t = (float)(stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }
            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

Il `PaintSurface` gestore disegna cinque cerchi concentrici con raggi animati. Se la `baseRadius` variabile viene calcolata come 100, quando `t` viene animata da 0 a 1, i raggi dei cinque cerchi aumentano da 0 a 100, da 100 a 200, da 200 a 300, da 300 a 400 e da 400 a 500. Per la maggior parte dei cerchi `strokeWidth` è 50, ma per il primo cerchio, il `strokeWidth` aggiunge un'animazione da 0 a 50. Per la maggior parte dei cerchi, il colore è blu, ma per l'ultimo cerchio il colore viene animato da blu a trasparente. Si noti il quarto argomento del `SKColor` costruttore che specifica l'opacità:

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
        float baseRadius = Math.Min(info.Width, info.Height) / 12;

        for (int circle = 0; circle < 5; circle++)
        {
            float radius = baseRadius * (circle + t);

            paint.StrokeWidth = baseRadius / 2 * (circle == 0 ? t : 1);
            paint.Color = new SKColor(0, 0, 255,
                (byte)(255 * (circle == 4 ? (1 - t) : 1)));

            canvas.DrawCircle(center.X, center.Y, radius, paint);
        }
    }
}
```

Il risultato è che l'immagine ha lo stesso aspetto quando `t` è uguale a 0 come quando `t` è uguale a 1 e i cerchi sembrano continuare ad espandersi per sempre:

[![](animation-images/expandingcircles-small.png "Triple screenshot of the Expanding Circles page")](animation-images/expandingcircles-large.png#lightbox "Triple screenshot of the Expanding Circles page")

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
