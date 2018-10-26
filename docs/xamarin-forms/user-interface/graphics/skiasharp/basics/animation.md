---
title: Animazione di base in SkiaSharp
description: Questo articolo illustra come animare la grafica di SkiaSharp in applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 1357f0be1ba4c15c4046e92f8556c957b9c4bd46
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122244"
---
# <a name="basic-animation-in-skiasharp"></a>Animazione di base in SkiaSharp

_Informazioni su come aggiungere un'animazione di grafica in SkiaSharp_

È possibile animare grafica di SkiaSharp in xamarin. Forms causando il `PaintSurface` metodo da chiamare periodicamente, ogni volta che gli elementi grafici di disegno in modo leggermente diverso. Ecco un'animazione illustrata più avanti in questo articolo con cerchi concentrici che apparentemente espandere dal centro:

![](animation-images/animationexample.png "Alcuni dei cerchi concentrici apparentemente espansione dal centro")

Il **Pulsating ellisse** pagina il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programma anima due assi di un'ellisse in modo che il file sembra essere pulsating ed è anche possibile controllare il frequenza di questo pulsazioni. Il [ **PulsatingEllipsePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml) file crea un'istanza di un xamarin. Forms `Slider` e un `Label` per visualizzare il valore corrente del dispositivo di scorrimento. Questo è un modo comune per integrare un `SKCanvasView` con altre viste di xamarin. Forms:

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

Crea un'istanza di file code-behind un `Stopwatch` oggetto come un orologio ad alta precisione. Il `OnAppearing` eseguire l'override di set di `pageIsActive` campo `true` e chiama un metodo denominato `AnimationLoop`. Il `OnDisappearing` set di sostituzione `pageIsActive` campo `false`:

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

Il `AnimationLoop` avvio del metodo di `Stopwatch` e quindi i cicli durante `pageIsActive` è `true`. Si tratta essenzialmente un "ciclo infinito", mentre la pagina è attiva, ma senza comportare il blocco in quanto il ciclo si conclude con una chiamata a del programma `Task.Delay` con il `await` operatore, che consente altre parti della funzione di programma. L'argomento `Task.Delay` fa sì che il completamento dopo 30/1 secondo. Definisce la frequenza dei fotogrammi di animazione.

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

Il `while` ciclo inizia ottenendo una durata del ciclo dal `Slider`. Si tratta di un tempo in secondi, ad esempio, 5. La seconda istruzione calcola un valore pari `t` per *ora*. Per un `cycleTime` pari a 5, `t` aumenta da 0 a 1 ogni 5 secondi. L'argomento di `Math.Sin` funzione nel seconda istruzione è compreso tra 0 a 2π ogni 5 secondi. Il `Math.Sin` funzione restituisce un valore compreso tra 0 e 1 back su 0 e quindi a &ndash;1 e 0 ogni 5 secondi, ma con i valori che cambiano più lentamente quando il valore è vicino a 1 o -1. Il valore 1 viene aggiunto in modo che i valori sono sempre positivi, e quindi viene diviso per 2, in modo che i valori compresi tra ½ su 1 per ½ su 0 per ½, ma più lento quando il valore è di circa 1 e 0. È archiviato nel `scale` campo e il `SKCanvasView` viene invalidata.

Il `PaintSurface` metodo utilizza questo `scale` valore per calcolare le due assi dell'ellisse:

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

Il metodo calcola un raggio massimo in base alla dimensione dell'area di visualizzazione e un raggio minimo in base al raggio massimo. Il `scale` valore è animata compreso tra 0 e 1 e nuovamente su 0, in modo che il metodo che usa per calcolare un' `xRadius` e `yRadius` compreso nell'intervallo tra `minRadius` e `maxRadius`. Questi valori vengono utilizzati per disegnare e compilare un'ellisse:

[![](animation-images/pulsatingellipse-small.png "Tripla screenshot della pagina di ellisse raffigurante")](animation-images/pulsatingellipse-large.png#lightbox "tripla screenshot della pagina raffigurante ellisse")

Si noti che il `SKPaint` oggetto viene creato in un `using` blocco. Come molte classi di SkiaSharp `SKPaint` deriva da `SKObject`, che deriva da `SKNativeObject`, che implementa il [ `IDisposable` ](xref:System.IDisposable) interfaccia. `SKPaint` esegue l'override di `Dispose` metodo per rilasciare le risorse non gestite.

 L'inserimento `SKPaint` in un `using` blocco assicura che `Dispose` viene chiamato alla fine del blocco per liberare le risorse non gestite. Ciò si verifica comunque quando la memoria utilizzata dal `SKPaint` oggetto viene liberato dal garbage collector di .NET, ma nel codice di animazione, è consigliabile essere proattivi nel liberare la memoria in modo più ordinato.

 Una soluzione migliore in questo caso specifico, è possibile creare due `SKPaint` gli oggetti di una sola volta e salvarli come campi.

Che cos'è il **espansione cerchi** animazione viene. Il [ `ExpandingCirclesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs) classe inizia con la definizione dei campi diversi, tra cui un `SKPaint` oggetto:

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

Questo programma Usa un approccio diverso per animazione basata su xamarin. Forms `Device.StartTimer` (metodo). Il `t` campo viene animato da 0 a 1 ogni `cycleTime` millisecondi:

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

Il `PaintSurface` gestore consente di disegnare cerchi concentrici cinque con raggi animati. Se il `baseRadius` variabile è pari a 100, quindi come `t` viene animata da 0 a 1, i raggi dell'aumento di cinque cerchi da 0 a 100, 100 a 200, 200 e 300, 300 a 400 e 400 e 500. Per la maggior parte dei cerchi le `strokeWidth` circolare 50, ma per la prima, la `strokeWidth` aggiunge un'animazione da 0 a 50. Per la maggior parte dei cerchi, il colore è blu, ma per il cerchio ultimo, il colore viene animato da blu a trasparente. Si noti che il quarto argomento per il `SKColor` costruttore che specifichi l'opacità:

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

Il risultato è che l'immagine di ricerca stesso when `t` è uguale a 0 come quando `t` è uguale a 1 e sembrano continuare a espandere per sempre i cerchi:

[![](animation-images/expandingcircles-small.png "Tripla screenshot della pagina di espansione cerchi")](animation-images/expandingcircles-large.png#lightbox "tripla screenshot della pagina di espansione cerchi")


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
