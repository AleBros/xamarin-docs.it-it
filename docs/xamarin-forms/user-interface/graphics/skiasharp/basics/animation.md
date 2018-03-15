---
title: Animazione di base
description: Per scoprire come animare la grafica SkiaSharp
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 347ecf72356915714200835263e38519c1ad8a13
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2018
---
# <a name="basic-animation"></a>Animazione di base

_Per scoprire come animare la grafica SkiaSharp_

È possibile animare grafica SkiaSharp in xamarin. Forms, di conseguenza il `PaintSurface` metodo da chiamare con una frequenza elevata, ogni volta di grafica in modo leggermente diverso. Ecco un'animazione illustrata più avanti in questo articolo con cerchi concentrici che apparentemente espandere dall'area:

![](animation-images/animationexample.png "Alcuni dei cerchi concentrici apparentemente espansione dal centro")

Il **Pulsating ellisse** nella pagina di [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programma aggiunge un'animazione due assi di un'ellisse in modo che sembra essere pulsating ed è anche possibile controllare il frequenza di questo pulsazioni:


Il [ **PulsatingEllipsePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) file crea un xamarin. Forms `Slider` e `Label` per visualizzare il valore corrente del dispositivo di scorrimento. Questo è un modo comune per integrare un `SKCanvasView` con altre viste di xamarin. Forms:

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

Crea il file code-behind un `Stopwatch` oggetto come un orologio ad alta precisione. Il `OnAppearing` eseguire l'override di set di `pageIsActive` campo `true` e chiama un metodo denominato `AnimationLoop`. Il `OnDisappearing` set di sostituzione `pageIsActive` campo `false`:

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

Il `AnimationLoop` avvio del metodo di `Stopwatch` e quindi cicli durante `pageIsActive` è `true`. Si tratta essenzialmente un "ciclo infinito", mentre la pagina è attiva, ma non causa il blocco in quanto il ciclo termina con una chiamata a del programma `Task.Delay` con il `await` operatore, che consente di altre parti della funzione di programma. L'argomento `Task.Delay` fa sì che venga completata dopo il 1/30 secondi. Definisce la frequenza dei fotogrammi di animazione.

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

Il `while` ciclo inizia con una durata del ciclo di recupero di `Slider`. Si tratta di un tempo in secondi, ad esempio 5. La seconda istruzione calcola un valore di `t` per *tempo*. Per un `cycleTime` pari a 5, `t` aumenta da 0 a 1 ogni 5 secondi. L'argomento di `Math.Sin` funzione il seconda istruzione è compreso tra 0 a 2 π ogni 5 secondi. Il `Math.Sin` funzione restituisce un valore compreso tra 0 e 1 posteriore a 0 e quindi a &ndash;1 e 0 ogni 5 secondi, ma con i valori che cambiano più lentamente quando il valore è vicino a 1 o -1. Il valore 1 viene aggiunto in modo che i valori sono sempre positivo e quindi viene diviso per 2, pertanto i valori è compreso tra ½ su 1 per ½ su 0 per ½, ma più lenta quando il valore è di circa 1 e 0. Questo viene archiviato nel `scale` campo e `SKCanvasView` viene invalidata.

Il `PaintSurface` metodo utilizza questo `scale` valore per calcolare i due assi dell'ellisse:

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

Il metodo calcola un raggio massimo in base alla dimensione dell'area di visualizzazione e un raggio minimo in base al raggio massimo. Il `scale` valore animato compreso tra 0 e 1 e reimpostarlo su 0, pertanto il metodo che usa per calcolare un `xRadius` e `yRadius` che è compreso tra `minRadius` e `maxRadius`. Questi valori vengono utilizzati per tracciare e riempire un'ellisse:

[![](animation-images/pulsatingellipse-small.png "Schermata triplo della pagina ellisse raffigurante")](animation-images/pulsatingellipse-large.png#lightbox "tripla schermata della pagina raffigurante ellisse")

Si noti che il `SKPaint` oggetto viene creato un `using` blocco. Come molte classi SkiaSharp `SKPaint` deriva da `SKObject`, che deriva da `SKNativeObject`, che implementa il [ `IDisposable` ](https://developer.xamarin.com/api/type/System.IDisposable/) interfaccia. `SKPaint` esegue l'override di `Dispose` metodo per rilasciare le risorse non gestite.

 Inserimento di `SKPaint` in un `using` blocco garantisce che `Dispose` viene chiamato alla fine del blocco da liberare le risorse non gestite. Ciò si verifica comunque quando la memoria utilizzata dal `SKPaint` oggetto viene liberato dal garbage collector di .NET, ma nel codice di animazione, è consigliabile essere in qualche modo proattivo nella liberando memoria in modo corretto.

 Una soluzione migliore in questo caso specifico, è possibile creare due `SKPaint` oggetti una sola volta e salvarle come campi.

Che cos'è il **espansione cerchi** animazione. Il [ `ExpandingCirclesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) classe inizia con la definizione di diversi campi, inclusi un `SKPaint` oggetto:

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

Questo programma utilizza un approccio diverso per l'animazione in base a di xamarin. Forms `Device.StartTimer`. Il `t` campo viene animato da 0 a 1 ogni `cycleTime` millisecondi:

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

Il `PaintSurface` gestore disegna 5 cerchi concentrici con raggi animati. Se il `baseRadius` variabile viene calcolata come 100, quindi come `t` viene animata da 0 a 1, il raggio dell'aumento di cinque cerchi da 0 a 100, 100 a 200, 200 e 300, 300-400 e 400 e 500. Per la maggior parte dei cerchi di `strokeWidth` è 50, ma per il primo cerchio, il `strokeWidth` anima da 0 a 50. Per la maggior parte dei cerchi, il colore è blu, ma per il cerchio ultimo, il colore viene animato da blu a trasparente:

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

Il risultato è che l'immagine esamina gli stessi quando `t` è uguale a 0, come quando `t` è uguale a 1 e i cerchi sembrano continuare a espandere forever:

[![](animation-images/expandingcircles-small.png "Schermata triplo della pagina espandendo cerchi")](animation-images/expandingcircles-large.png#lightbox "tripla schermata della pagine dei cerchi di espansione")


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
