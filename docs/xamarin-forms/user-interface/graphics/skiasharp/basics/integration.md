---
title: L'integrazione con xamarin. Forms
description: Questo articolo illustra come creare elementi grafici di SkiaSharp che rispondono al tocco e gli elementi di xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: 35aede1a541d0ff62f6a4a5b57256c389e5a8640
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997530"
---
# <a name="integrating-with-xamarinforms"></a>L'integrazione con xamarin. Forms

_Creare grafici di SkiaSharp che rispondono al tocco e gli elementi di xamarin. Forms_

Grafica di SkiaSharp può integrarsi con il resto di xamarin. Forms in diversi modi. È possibile combinare un'area di disegno di SkiaSharp e gli elementi di xamarin. Forms nella stessa pagina ed elementi di xamarin. Forms posizione anche all'inizio di un'area di disegno di SkiaSharp:

![](integration-images/integrationexample.png "Selezionare un colore con dispositivi di scorrimento")

Un altro approccio alla creazione grafica interattiva di SkiaSharp in xamarin. Forms è tramite tocco.
La seconda pagina il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programma è autorizzata a utilizzare **toccare riempire attiva/disattiva**. Disegna un cerchio semplice due modi &mdash; senza riempimento e con riempimento &mdash; attivata/disattivata tramite tocco. Il [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) classe illustra come è possibile modificare i grafici di SkiaSharp in risposta all'input dell'utente.

Per questa pagina, il `SKCanvasView` viene creata un'istanza di classe nel [TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) file, che imposta anche un xamarin. Forms [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) sulla vista:

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

Si noti che il `skia` dichiarazione dello spazio dei nomi XML.

Il `Tapped` gestore per il `TapGestureRecognizer` oggetto semplicemente attiva o disattiva il valore di un campo booleano e chiama le [ `InvalidateSurface` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface()/) metodo di `SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

La chiamata a `InvalidateSurface` genera in modo efficace una chiamata ai `PaintSurface` gestore, che usa il `showFill` campo da riempire o non di riempimento del cerchio:

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

Il `StrokeWidth` proprietà è stata impostata su 50 per sottolineare la differenza. È anche possibile visualizzare la larghezza intera riga disegnando prima di tutto l'interno e quindi la struttura. Per impostazione predefinita, figure disegnata in un secondo momento nel grafico il `PaintSurface` gestore dell'evento oscurare quelli creato in precedenza nel gestore.

Il **colore esplorare** pagina viene illustrato come è possibile integrare SkiaSharp grafica con altri elementi di xamarin. Forms e illustra anche la differenza tra due metodi alternativi per definire i colori in SkiaSharp. Il metodo statico [ `SKColor.FromHsl` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsl/p/System.Single/System.Single/System.Single/System.Byte/) metodo crea un `SKColor` valore basato sul modello di Hue-Saturation-Lightness:

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

Il metodo statico [ `SKColor.FromHsv` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsv/p/System.Single/System.Single/System.Single/System.Byte/) metodo crea un `SKColor` valore basato sul modello di Tonalità-saturazione-valore simile:

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

In entrambi i casi il `h` argomento compreso tra 0 e 360. Il `s`, `l`, e `v` argomenti compresa tra 0 e 100. Il `a` (alfa o all'opacità) argomento è compreso tra 0 e 255.

Il [ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) file vengono creati due `SKCanvasView` gli oggetti in un `StackLayout` side-by-side con `Slider` e `Label` visualizzazioni che consentono all'utente di selezionare HSL e Valori di colore HSV:

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

I due `SKCanvasView` elementi si trovano in una cella singola `Grid` con un `Label` che si trova nella parte superiore per visualizzare il valore di colore RGB risultante.

Il [ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) file code-behind è relativamente semplice. Condiviso `ValueChanged` gestore per i tre `Slider` elementi invalida semplicemente entrambi `SKCanvasView` elementi. Il `PaintSurface` gestori Cancella tutto con il colore indicato dal `Slider` gli elementi, impostare inoltre il `Label` che si trova nella parte superiore del `SKCanvasView` elementi:

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

Nei modelli di colori HSL sia HSV, il valore di tonalità compreso tra 0 e 360 e indica alla tonalità del colore dominante. Questi sono i colori tradizionali dell'arcobaleno: rosso, arancione, giallo, verde, blu, indigo, viola e back in un cerchio rosso.

Nel modello di HSL, un valore 0 per la luminosità è sempre nero e un valore 100 è bianco always. Quando il valore di saturazione è 0, luminosità valori compresi tra 0 e 100 sono gradazioni di grigio. Aumentando la saturazione aggiunge altri colori. Colori puri, che sono valori RGB con un solo componente uguale a 255, un altro uguale a 0 e il terzo compreso tra 0 e 255, si verificano quando la saturazione è 100 e la luminosità è 50.

Nel modello di HSV colori puri risultato quando il saturazione e il valore è 100. Quando il valore è 0, indipendentemente dalle eventuali altre impostazioni, il colore è nero. Le sfumature di grigio si verificano quando la saturazione è 0 e intervalli di valori compreso tra 0 e 100.

Ma il modo migliore per acquisire familiarità con i due modelli di sperimentarle manualmente:

[![](integration-images/colorexplore-large.png "Tripla screenshot della pagina di colore esplorare")](integration-images/colorexplore-small.png#lightbox "tripla screenshot della pagina di esplorazione di colore")


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
