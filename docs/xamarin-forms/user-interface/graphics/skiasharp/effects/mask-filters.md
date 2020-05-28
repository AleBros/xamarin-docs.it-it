---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 10192e93d2e20f9aa58ca95dd81c07f560193905
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136409"
---
# <a name="skiasharp-mask-filters"></a>Filtri maschera SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

I filtri maschera sono effetti che consentono di modificare la geometria e il canale alfa degli oggetti grafici. Per usare un filtro mask, impostare la [`MaskFilter`](xref:SkiaSharp.SKPaint.MaskFilter) proprietà di `SKPaint` su un oggetto di tipo [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) creato chiamando uno dei `SKMaskFilter` metodi statici.

Il modo migliore per acquisire familiarità con i filtri maschera consiste nel provare questi metodi statici. Il filtro mask più utile crea una sfocatura:

![Esempio di sfocatura](mask-filters-images/MaskFilterExample.png "Esempio di sfocatura")

Questa è l'unica funzionalità di filtro maschera descritta in questo articolo. Il prossimo articolo sui [**filtri immagini di SkiaSharp**](image-filters.md) descrive anche un effetto sfocato che potrebbe essere preferibile a questo. 

Il [`SKMaskFilter.CreateBlur`](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single)) metodo statico presenta la sintassi seguente:

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

Gli overload consentono di specificare i flag per l'algoritmo utilizzato per creare la sfocatura e un rettangolo per evitare la sfocatura nelle aree che verranno analizzate con altri oggetti grafici.

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle)è un'enumerazione con i membri seguenti:

- `Normal`
- `Solid`
- `Outer`
- `Inner`

Gli effetti di questi stili sono illustrati negli esempi seguenti. Il `sigma` parametro specifica l'extent della sfocatura. Nelle versioni precedenti di Skia, l'entità della sfocatura è stata indicata con un valore RADIUS. Se è preferibile un valore RADIUS per l'applicazione, è disponibile un [`SKMaskFilter.ConvertRadiusToSigma`](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*) metodo statico che può essere convertito da uno all'altro. Il metodo moltiplica il raggio per 0,57735 e aggiunge 0,5.

La pagina dell' **esperimento di offuscamento della maschera** nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) consente di sperimentare gli stili di sfocatura e i valori Sigma. Il file XAML crea un'istanza di `Picker` con i quattro `SKBlurStyle` membri di enumerazione e un oggetto `Slider` per specificare il valore Sigma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.MaskBlurExperimentPage"
             Title="Mask Blur Experiment">
    
    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blurStylePicker" 
                Title="Filter Blur Style" 
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlurStyle}">
                    <x:Static Member="skia:SKBlurStyle.Normal" />
                    <x:Static Member="skia:SKBlurStyle.Solid" />
                    <x:Static Member="skia:SKBlurStyle.Outer" />
                    <x:Static Member="skia:SKBlurStyle.Inner" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="sigmaSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaSlider},
                              Path=Value,
                              StringFormat='Sigma = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

Il file code-behind utilizza tali valori per creare un `SKMaskFilter` oggetto e impostarlo sulla `MaskFilter` proprietà di un `SKPaint` oggetto. Questo `SKPaint` oggetto viene usato per creare una stringa di testo e una bitmap:

```csharp
public partial class MaskBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage), 
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public MaskBlurExperimentPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Pink);

        // Get values from XAML controls
        SKBlurStyle blurStyle =
            (SKBlurStyle)(blurStylePicker.SelectedIndex == -1 ?
                                        0 : blurStylePicker.SelectedItem);

        float sigma = (float)sigmaSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);

            // Get text bounds and calculate display rectangle
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);
            SKRect textRect = new SKRect(0, 0, info.Width, textBounds.Height + 50);

            // Center the text in the display rectangle
            float xText = textRect.Width / 2 - textBounds.MidX;
            float yText = textRect.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);

            // Calculate rectangle for bitmap
            SKRect bitmapRect = new SKRect(0, textRect.Bottom, info.Width, info.Height);
            bitmapRect.Inflate(-50, -50);

            canvas.DrawBitmap(bitmap, bitmapRect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Ecco il programma in esecuzione in iOS, Android e il piattaforma UWP (Universal Windows Platform) (UWP) con lo `Normal` stile di sfocatura e i livelli crescenti `sigma` :

[![Esperimento di sfocatura maschera-normale](mask-filters-images/MaskBlurExperiment-Normal.png "Esperimento di sfocatura maschera-normale")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

Si noterà che solo i bordi della bitmap sono interessati dalla sfocatura. La `SKMaskFilter` classe non è l'effetto corretto da usare se si vuole sfocare un'intera immagine bitmap. A tale proposito, è necessario usare la [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) classe come descritto nel prossimo articolo sui [**filtri immagine di SkiaSharp**](image-filters.md).

Il testo è più sfocato con l'aumento dei valori dell' `sigma` argomento. Nella sperimentazione di questo programma si noterà che per un determinato `sigma` valore, la sfocatura è più estrema sul desktop di Windows 10. Questa differenza si verifica perché la densità in pixel è inferiore su un monitor desktop rispetto ai dispositivi mobili e, di conseguenza, l'altezza del testo in pixel è inferiore. Il `sigma` valore è proporzionale a un extent di sfocatura in pixel, quindi, per un determinato `sigma` valore, l'effetto è più estremo sulle visualizzazioni di risoluzione inferiore. In un'applicazione di produzione, è probabile che si desideri calcolare un `sigma` valore proporzionale alle dimensioni dell'elemento grafico. 

Provare diversi valori prima di stabilirsi a un livello di sfocatura che risulti più adatto per l'applicazione. Ad esempio, nella pagina dell' **esperimento maschera di sfocatura** , provare a impostare `sigma` come segue:

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

Ora `Slider` non ha alcun effetto, ma il grado di sfocatura è coerente tra le piattaforme:

[![Maschera dell'esperimento di sfocatura-coerente](mask-filters-images/MaskBlurExperiment-Consistent.png "Maschera dell'esperimento di sfocatura-coerente")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

Tutte le schermate fino ad ora hanno mostrato la sfocatura creata con il `SKBlurStyle.Normal` membro di enumerazione. Gli screenshot seguenti mostrano gli effetti degli stili di `Solid` `Outer` `Inner` sfocatura, e:

[![Esperimento di sfocatura maschera](mask-filters-images/MaskBlurExperiment.png "Esperimento di sfocatura maschera")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

Lo screenshot iOS Mostra lo `Solid` stile: i caratteri di testo sono ancora presenti come tratti neri solidi e la sfocatura viene aggiunta all'esterno di questi caratteri di testo. 

Lo screenshot Android nel mezzo Mostra lo `Outer` stile: i tratti di carattere stessi vengono eliminati (così come la bitmap) e la sfocatura racchiude lo spazio vuoto in cui i caratteri di testo sono apparsi. 

Lo screenshot UWP a destra mostra lo `Inner` stile. La sfocatura è limitata all'area normalmente occupata dai caratteri di testo.

L'articolo relativo alla sfumatura [**lineare SkiaSharp**](shaders/linear-gradient.md#transparency-and-gradients) descrive un programma di **sfumatura della reflection** che ha usato una sfumatura lineare e una trasformazione per simulare una reflection di una stringa di testo:

[![Sfumatura Reflection](shaders/linear-gradient-images/ReflectionGradient.png "Sfumatura Reflection")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

La pagina **Reflection sfocata** aggiunge un'unica istruzione a tale codice:

```csharp
public class BlurryReflectionPage : ContentPage
{
    const string TEXT = "Reflection";

    public BlurryReflectionPage()
    {
        Title = "Blurry Reflection";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Create a blur mask filter
            paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

La nuova istruzione aggiunge un filtro di sfocatura per il testo riflesso basato sulle dimensioni del testo:

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

Questo filtro di sfocatura fa sì che la reflection sembri molto più realistica:

[![Reflection sfocata](mask-filters-images/BlurryReflection.png "Reflection sfocata")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
