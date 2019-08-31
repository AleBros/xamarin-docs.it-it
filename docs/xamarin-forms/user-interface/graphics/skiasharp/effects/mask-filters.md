---
title: Filtri di maschera di SkiaSharp
description: Informazioni su come usare il filtro per creare le sfumature e altri effetti alfa.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 940422A1-8BC0-4039-8AD7-26C61320F858
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: 36a8b5c32261d4f508c82feea1e6127574db6a20
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198253"
---
# <a name="skiasharp-mask-filters"></a>Filtri di maschera di SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Maschera i filtri sono gli effetti che consentono di modificare la geometria e il canale alfa di oggetti grafici. Per usare un filtro, impostare il [ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter) proprietà della `SKPaint` a un oggetto di tipo [ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter) che è stato creato chiamando uno del `SKMaskFilter` metodi statici.

Il modo migliore per acquisire familiarità con i filtri di mask è eseguendo più esperimenti con questi metodi statici. Il filtro più utile di maschera crea un' sfocatura:

![Esempio Blur](mask-filters-images/MaskFilterExample.png "Blur esempio")

Che è l'unica funzionalità di filtro maschera descritte in questo articolo. Nel prossimo articolo sul [ **filtri immagini di SkiaSharp** ](image-filters.md) descrive anche un effetto di sfocatura che è preferibile alla seguente. 

Il metodo statico [ `SKMaskFilter.CreateBlur` ](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single)) metodo ha la sintassi seguente:

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

Gli overload consentono che specifica i flag per l'algoritmo utilizzato per creare la sfocatura e un rettangolo per evitare di sfocatura nelle aree che saranno coperte da altri oggetti grafici.

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle) è un'enumerazione con i membri seguenti:

- `Normal`
- `Solid`
- `Outer`
- `Inner`

Negli esempi seguenti vengono visualizzati gli effetti di questi stili. Il `sigma` parametro specifica l'extent della sfocatura. Nelle versioni precedenti di Skia, l'extent della sfocatura indicato con il valore del raggio. Se il valore del raggio è preferibile per l'applicazione, è un valore statico [ `SKMaskFilter.ConvertRadiusToSigma` ](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*) metodo che può convertire da uno a altro. Il metodo Moltiplica il raggio 0.57735 e aggiunge 0,5.

Il **maschera Blur sperimentare** nella pagina il [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) esempio consente di sperimentare con i valori di sigma e gli stili di sfocatura. Il file XAML crea un'istanza di un `Picker` con i quattro `SKBlurStyle` membri dell'enumerazione e un `Slider` per specificare il valore di sigma:

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

Il file code-behind Usa tali valori per creare un `SKMaskFilter` dell'oggetto e impostarlo sul `MaskFilter` proprietà di un `SKPaint` oggetto. Ciò `SKPaint` oggetto viene usato per disegnare sia una stringa di testo e una mappa di bit:

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

Ecco il programma in esecuzione in iOS, Android e Universal Windows Platform (UWP) con i `Normal` blur stile e aumentando `sigma` livelli:

[![Mascherare sfocatura esperimento: Normal](mask-filters-images/MaskBlurExperiment-Normal.png "maschera Blur esperimento - normale")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

Si noterà che sono interessati solo i bordi della bitmap dalla sfocatura. Il `SKMaskFilter` classe non è l'effetto corretto da usare se si desidera un'immagine intera bitmap di sfocatura. Per cui è opportuno usare la [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) classe come descritto nell'articolo successivo nella [ **filtri immagini di SkiaSharp**](image-filters.md).

Il testo è sfocato più con l'aumentare i valori del `sigma` argomento. Sperimentazione con questo programma, si noterà che per un particolare `sigma` valore, l'effetto è più estremo sul desktop di Windows 10. Questa differenza si verifica perché la densità di pixel è inferiore su un monitor da tavolo rispetto a sui dispositivi mobili e di conseguenza l'altezza del testo in pixel è inferiore. Il `sigma` valore è proporzionale un' sfocatura nella misura in pixel, in tal caso per un determinato `sigma` valore, l'effetto è più estremo su schermi a risoluzione inferiore. In un'applicazione di produzione, è opportuno calcolare un `sigma` valore proporzionale alle dimensioni dell'immagine. 

Provare diversi valori prima di stabilire un livello di sfocatura Cerca il meglio per l'applicazione. Ad esempio, nelle **maschera Blur esperimento** pagina, provare a impostare `sigma` simile al seguente:

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

A questo punto il `Slider` non ha alcun effetto, ma il grado di sfocatura sia coerenza tra le piattaforme:

[![Mascherare esperimento sfocatura - coerente](mask-filters-images/MaskBlurExperiment-Consistent.png "mascherare sfocatura esperimento: coerente")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

Tutte le schermate hanno illustrato finora sfocatura creato con il `SKBlurStyle.Normal` membro di enumerazione. Le schermate seguenti illustrano gli effetti del `Solid`, `Outer`, e `Inner` blur stili:

[![Mascherare esperimento sfocatura](mask-filters-images/MaskBlurExperiment.png "mascherare sfocatura esperimento")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

Lo screenshot iOS Mostra lo `Solid` stile: I caratteri di testo sono ancora presenti come tratti neri solidi e la sfocatura viene aggiunta all'esterno di questi caratteri di testo. 

Lo screenshot Android nel mezzo Mostra lo `Outer` stile: I tratti di carattere stessi vengono eliminati (così come la bitmap) e la sfocatura racchiude lo spazio vuoto dove i caratteri di testo vengono visualizzati una volta. 

Nella schermata UWP destra il `Inner` stile. La sfocatura è limitata all'area normalmente occupata dai caratteri di testo.

Il [ **sfumatura lineare SkiaSharp** ](shaders/linear-gradient.md#transparency-and-gradients) articolo descritto un **Reflection sfumatura** programma che utilizzata una sfumatura lineare e una trasformazione per simulare un riflesso di una stringa di testo:

[![Reflection sfumatura](shaders/linear-gradient-images/ReflectionGradient.png "Reflection sfumatura")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

Il **Reflection sfocati** pagina aggiunge una singola istruzione per tale codice:

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

La nuova istruzione aggiunge un filtro sfocatura del testo riflesso si basa sulle dimensioni testo:

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

Questo filtro sfocatura fa in modo che la reflection per sembrano molto più realistici:

[![Reflection sfocate](mask-filters-images/BlurryReflection.png "sfocate Reflection")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
