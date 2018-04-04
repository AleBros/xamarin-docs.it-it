---
title: L'integrazione con xamarin. Forms
description: Creare grafici SkiaSharp che rispondono a tocco e gli elementi di xamarin. Forms
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: 67c4330d8e446a407dec7792fe5f40cdd9d23c22
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="integrating-with-xamarinforms"></a>L'integrazione con xamarin. Forms

_Creare grafici SkiaSharp che rispondono a tocco e gli elementi di xamarin. Forms_

Grafica SkiaSharp possibile integrare con il resto di xamarin. Forms in diversi modi. È possibile combinare un'area di disegno SkiaSharp e gli elementi di xamarin. Forms nella stessa pagina e gli elementi di xamarin. Forms posizione anche all'inizio di un'area di disegno SkiaSharp:

![](integration-images/integrationexample.png "Selezionare un colore con dispositivi di scorrimento")

Un altro approccio per la creazione interattiva grafica SkiaSharp in xamarin. Forms è tramite tocco.
La seconda pagina di [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programma è autorizzata a utilizzare **toccare Attiva/Disattiva riempimento**. Disegna un cerchio semplice in due modi &mdash; senza riempimento e con un riempimento &mdash; attivata/disattivata tramite una scelta. Il [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs) classe viene illustrato come è possibile modificare la grafica SkiaSharp in risposta all'input dell'utente.

Per questa pagina, il `SKCanvasView` viene creata un'istanza di classe nel [TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) file, che imposta anche un xamarin. Forms [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) sulla vista:

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

Si noti il `skia` dichiarazione dello spazio dei nomi XML.

Il `Tapped` gestore per il `TapGestureRecognizer` oggetto semplicemente attiva o disattiva il valore di un campo booleano e chiama il [ `InvalidateSurface` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface()/) metodo `SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

La chiamata a `InvalidateSurface` genera in modo efficace una chiamata al `PaintSurface` gestore, che utilizza il `showFill` campo da riempire o non riempire il cerchio:

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

Il `StrokeWidth` impostata su 50 per evidenziare la differenza. È inoltre possibile visualizzare la larghezza intera riga innanzitutto l'area interna di disegno e la struttura. Per impostazione predefinita, le cifre disegnata più avanti in grafica di `PaintSurface` gestore nascondere quelle creato in precedenza nel gestore.

Il **colore esplorare** pagina viene illustrato come è possibile integrare SkiaSharp grafica con altri elementi di xamarin. Forms e anche la differenza tra i due metodi alternativi per definire i colori in SkiaSharp. Il metodo statico [ `SKColor.FromHsl` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsl/p/System.Single/System.Single/System.Single/System.Byte/) metodo crea un `SKColor` valore in base al modello di Tonalità-saturazione-luminosità:

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

Il metodo statico [ `SKColor.FromHsv` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsv/p/System.Single/System.Single/System.Single/System.Byte/) metodo crea un `SKColor` valore in base al modello di Tonalità-saturazione-Value simile:

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

In entrambi i casi, il `h` argomento compreso tra 0 e 360. Il `s`, `l`, e `v` argomenti compreso tra 0 e 100. Il `a` (alfa o opacità) argomento è compreso tra 0 e 255.

Il [ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml) file crea due `SKCanvasView` gli oggetti in un `StackLayout` side-by-side con `Slider` e `Label` viste che consentono all'utente di selezionare HSL e Valori di colore HSV:

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

I due `SKCanvasView` elementi si trovano in una cella singola `Grid` con un `Label` si trova nella parte superiore per la visualizzazione del valore di colore RGB risulta.

Il [ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs) file code-behind è relativamente semplice. Condiviso `ValueChanged` gestore per le tre `Slider` elementi invalida entrambi `SKCanvasView` elementi. Il `PaintSurface` gestori cancellare l'area di disegno con il colore indicato dal `Slider` elementi, impostare inoltre il `Label` si trova in cima il `SKCanvasView` elementi:

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

Nei modelli di colore HSL sia HSV, il valore di tonalità compreso tra 0 e 360 e indica la tonalità del colore dominante. I colori tradizionali dell'arcobaleno: rosso, arancione, giallo, verde, blu, indigo, viola e nuovamente in un cerchio su rosso.

Nel modello HSL, un valore 0 per luminosità è sempre nero e un valore 100 è sempre il bianco. Quando il valore di saturazione è 0, i valori di luminosità compreso tra 0 e 100 sono gradazioni di grigio. Aumentando la saturazione aggiunge altri colori. Colori puri, che sono valori RGB con un componente uguale a 255, un altro uguale a 0 e il terzo compreso tra 0 e 255, si verificano quando la saturazione è 100 e la luminosità è 50.

Nel modello HSV colori puri comportare quando la saturazione e il valore è 100. Quando il valore è 0, indipendentemente dalle altre impostazioni, il colore è nero. Quando la saturazione è 0 e compreso tra 0 e 100 le sfumature di grigio.

Ma il modo migliore per acquisire familiarità con i due modelli per sperimentare manualmente:

[![](integration-images/colorexplore-large.png "Schermata triplo della pagina esplorazione colore")](integration-images/colorexplore-small.png#lightbox "tripla schermata della pagina di esplorazione di colore")


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
