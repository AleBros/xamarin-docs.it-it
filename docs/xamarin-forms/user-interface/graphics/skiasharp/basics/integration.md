---
title: Integrazione conXamarin.Forms
description: Questo articolo illustra come creare grafica SkiaSharp che rispondono al tocco e agli Xamarin.Forms elementi e ne illustra il codice di esempio.
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9e763184f38719cda4526eb0a2dfdf39b2191a03
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137696"
---
# <a name="integrating-with-xamarinforms"></a>Integrazione conXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Crea grafica SkiaSharp che rispondono al tocco e agli Xamarin.Forms elementi_

La grafica SkiaSharp può essere integrata con il resto di Xamarin.Forms in diversi modi. È possibile combinare un'area di disegno SkiaSharp e Xamarin.Forms gli elementi nella stessa pagina e persino Xamarin.Forms gli elementi posizionati sopra un'area di disegno SkiaSharp:

![](integration-images/integrationexample.png "Selecting a color with sliders")

Un altro approccio per la creazione di immagini SkiaSharp interattive in Xamarin.Forms è tramite il tocco.
La seconda pagina del programma [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) è intitolata **toccare interruttore riempimento**. Disegna un cerchio semplice in due modi &mdash; senza riempimento e con un riempimento &mdash; attivato da un tocco. La [`TapToggleFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) classe Mostra come è possibile modificare la grafica di SkiaSharp in risposta all'input dell'utente.

Per questa pagina, `SKCanvasView` viene creata un'istanza della classe nel file [TapToggleFill. XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) , che imposta anche un oggetto nella Xamarin.Forms [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) vista:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.TapToggleFillPage"
             Title="Tap Toggle Fill">

    <skia:SKCanvasView PaintSurface="OnCanvasViewPaintSurface">
        <skia:SKCanvasView.GestureRecognizers>
            <TapGestureRecognizer Tapped="OnCanvasViewTapped" />
        </skia:SKCanvasView.GestureRecognizers>
    </skia:SKCanvasView>
</ContentPage>
```

Si noti la `skia` dichiarazione dello spazio dei nomi XML.

Il `Tapped` gestore per l' `TapGestureRecognizer` oggetto commuta semplicemente il valore di un campo booleano e chiama il [`InvalidateSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface) metodo di `SKCanvasView` :

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

La chiamata a `InvalidateSurface` genera efficacemente una chiamata al `PaintSurface` gestore, che usa il `showFill` campo per riempire o non riempire il cerchio:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 50
    };
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);

    if (showFill)
    {
        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.Blue;
        canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    }
}
```

La `StrokeWidth` proprietà è stata impostata su 50 per accentuare la differenza. È anche possibile visualizzare l'intera lunghezza riga disegnando prima l'interno e quindi il contorno. Per impostazione predefinita, le figure grafiche disegnate in un secondo momento nel `PaintSurface` gestore dell'evento oscurano quelle disegnate in precedenza nel gestore.

Nella pagina **Esplora colori** viene illustrato come è possibile integrare anche la grafica SkiaSharp con altri Xamarin.Forms elementi e viene inoltre illustrata la differenza tra due metodi alternativi per la definizione dei colori in SkiaSharp. Il [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte)) metodo statico crea un `SKColor` valore in base al modello tonalità-saturazione-luminosità:

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

Il [`SKColor.FromHsv`](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte)) metodo statico crea un `SKColor` valore in base al modello di tonalità-saturazione-valore simile:

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

In entrambi i casi, l' `h` argomento è compreso tra 0 e 360. Gli `s` `l` argomenti, e `v` variano da 0 a 100. L' `a` argomento (Alpha o Opacity) è compreso tra 0 e 255.

Il file [**ColorExplorePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) crea due `SKCanvasView` oggetti in un `StackLayout` affiancato con `Slider` visualizzazioni e `Label` che consentono all'utente di selezionare i valori HSL e HSV:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Basics.ColorExplorePage"
             Title="Color Explore">
    <StackLayout>
        <!-- Hue slider -->
        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference hueSlider},
                              Path=Value,
                              StringFormat='Hue = {0:F0}'}" />

        <!-- Saturation slider -->
        <Slider x:Name="saturationSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference saturationSlider},
                              Path=Value,
                              StringFormat='Saturation = {0:F0}'}" />

        <!-- Lightness slider -->
        <Slider x:Name="lightnessSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference lightnessSlider},
                              Path=Value,
                              StringFormat='Lightness = {0:F0}'}" />

        <!-- HSL canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hslCanvasView"
                               PaintSurface="OnHslCanvasViewPaintSurface" />

            <Label x:Name="hslLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>

        <!-- Value slider -->
        <Slider x:Name="valueSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference valueSlider},
                              Path=Value,
                              StringFormat='Value = {0:F0}'}" />

        <!-- HSV canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hsvCanvasView"
                               PaintSurface="OnHsvCanvasViewPaintSurface" />

            <Label x:Name="hsvLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>
    </StackLayout>
</ContentPage>
```

I due `SKCanvasView` elementi si trovano in una singola cella `Grid` con un oggetto in `Label` primo piano per visualizzare il valore di colore RGB risultante.

Il file code-behind [**ColorExplorePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) è relativamente semplice. Il `ValueChanged` gestore condiviso per i tre `Slider` elementi invalida semplicemente entrambi `SKCanvasView` gli elementi. I `PaintSurface` gestori cancellano l'area di disegno con il colore indicato dagli `Slider` elementi e impostano anche la parte `Label` superiore degli `SKCanvasView` elementi:

```csharp
public partial class ColorExplorePage : ContentPage
{
    public ColorExplorePage()
    {
        InitializeComponent();

        hueSlider.Value = 0;
        saturationSlider.Value = 100;
        lightnessSlider.Value = 50;
        valueSlider.Value = 100;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        hslCanvasView.InvalidateSurface();
        hsvCanvasView.InvalidateSurface();
    }

    void OnHslCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsl((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)lightnessSlider.Value);
        args.Surface.Canvas.Clear(color);

        hslLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }

    void OnHsvCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsv((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)valueSlider.Value);
        args.Surface.Canvas.Clear(color);

        hsvLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }
}
```

Nei modelli di colore HSL e HSV, il valore Hue è compreso tra 0 e 360 e indica la tonalità dominante del colore. Si tratta dei colori tradizionali dell'arcobaleno: rosso, arancione, giallo, verde, blu, indaco, viola e indietro in un cerchio a rosso.

Nel modello HSL, un valore 0 per la luminosità è sempre nero e un valore 100 è sempre bianco. Quando il valore di saturazione è 0, i valori di luminosità compresi tra 0 e 100 sono gradazioni di grigio. L'aumento della saturazione aumenta il colore. Colori puri (ovvero valori RGB con un componente uguale a 255, un altro uguale a 0 e il terzo compreso tra 0 e 255) si verificano quando la saturazione è 100 e la luminosità è 50.

Nel modello HSV, i colori puri risultano quando la saturazione e il valore sono 100. Quando value è 0, indipendentemente da altre impostazioni, il colore è nero. Le sfumature di grigio si verificano quando la saturazione è 0 e il valore è compreso tra 0 e 100.

Tuttavia, il modo migliore per acquisire familiarità con i due modelli è provare a eseguire queste operazioni:

[![](integration-images/colorexplore-large.png "Triple screenshot of the Color Explore page")](integration-images/colorexplore-small.png#lightbox "Triple screenshot of the Color Explore page")

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
