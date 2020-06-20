---
title: Filtri immagine SkiaSharp
description: Informazioni su come usare il filtro immagine per creare sfocature e ombreggiature.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 173E7B22-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: eedbca080fce9f3001a7b1e2358845fd63c6121b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136344"
---
# <a name="skiasharp-image-filters"></a>Filtri immagine SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

I filtri immagine sono effetti che operano su tutti i bit di colore dei pixel che costituiscono un'immagine. Sono più versatili dei filtri maschera, che operano solo sul canale alfa, come descritto nell'articolo [**filtri SkiaSharp mask**](mask-filters.md). Per usare un filtro immagine, impostare la [`ImageFilter`](xref:SkiaSharp.SKPaint.ImageFilter) proprietà di `SKPaint` su un oggetto di tipo [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) creato chiamando uno dei metodi statici della classe.

Il modo migliore per acquisire familiarità con i filtri maschera consiste nel provare questi metodi statici. È possibile usare un filtro maschera per sfocare un'intera bitmap:

![Esempio di sfocatura](image-filters-images/ImageFilterExample.png "Esempio di sfocatura")

Questo articolo illustra anche l'uso di un filtro immagine per creare un'ombreggiatura e per gli effetti di rilievo e incisione.

## <a name="blurring-vector-graphics-and-bitmaps"></a>Grafica vettoriale sfocata e bitmap

L'effetto di sfocatura creato dal [`SKImageFilter.CreateBlur`](xref:SkiaSharp.SKImageFilter.CreateBlur*) metodo statico presenta un vantaggio significativo rispetto ai metodi Blur della [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) classe: il filtro immagine può offuscare un'intera bitmap. Il metodo presenta la sintassi seguente:

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

Il metodo ha due valori Sigma &mdash; il primo per l'extent della sfocatura nella direzione orizzontale e il secondo per la direzione verticale. È possibile a CATENARE i filtri di immagine specificando un altro filtro immagine come terzo argomento facoltativo. È possibile specificare anche un rettangolo di ritaglio.

La pagina dell' **esperimento di sfocatura delle immagini** in [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) include due `Slider` visualizzazioni che consentono di provare a impostare diversi livelli di sfocatura:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.ImageBlurExperimentPage"
             Title="Image Blur Experiment">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

Il file code-behind usa i due `Slider` valori da chiamare `SKImageFilter.CreateBlur` per l' `SKPaint` oggetto usato per visualizzare sia il testo che una bitmap:

```csharp
public partial class ImageBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage),
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public ImageBlurExperimentPage ()
    {
        InitializeComponent ();
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

        // Get values from sliders
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.ImageFilter = SKImageFilter.CreateBlur(sigmaX, sigmaY);

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

Le tre schermate mostrano diverse impostazioni per le `sigmaX` `sigmaY` Impostazioni e:

[![Esperimento di sfocatura immagine](image-filters-images/ImageBlurExperiment.png "Esperimento di sfocatura immagine")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

Per assicurare la coerenza della sfocatura tra dimensioni e risoluzioni di visualizzazione diverse, impostare `sigmaX` e `sigmaY` su valori proporzionali alla dimensione in pixel di cui è stato eseguito il rendering dell'immagine a cui viene applicata la sfocatura.

## <a name="drop-shadow"></a>Ombreggiatura

Il [`SKImageFilter.CreateDropShadow`](xref:SkiaSharp.SKImageFilter.CreateDropShadow*) metodo statico crea un `SKImageFilter` oggetto per un'ombreggiatura:

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

Impostare questo oggetto sulla `ImageFilter` proprietà di un `SKPaint` oggetto e qualsiasi elemento disegnato con tale oggetto avrà un'ombreggiatura sottostante.

I `dx` `dy` parametri e indicano gli offset orizzontali e verticali dell'ombreggiatura in pixel dall'oggetto grafico. La convenzione nella grafica 2D consiste nel presupporre una fonte di luce proveniente dalla parte superiore sinistra, che implica che entrambi gli argomenti devono essere positivi per posizionare l'ombreggiatura sotto e a destra dell'oggetto grafico.

I `sigmaX` `sigmaY` parametri e sono fattori di sfocatura per l'ombreggiatura.

Il `color` parametro è il colore dell'ombreggiatura. Questo `SKColor` valore può includere la trasparenza. Una possibilità è il valore `SKColors.Black.WithAlpha(0x80)` di colore per scurire qualsiasi sfondo del colore.

I due parametri finali sono facoltativi.

Il programma **Drop Shadow Experiment** consente di sperimentare i valori di `dx` , `dy` , `sigmaX` e `sigmaY` per visualizzare una stringa di testo con un'ombreggiatura. Il file XAML crea un'istanza `Slider` di quattro visualizzazioni per impostare i valori seguenti:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.DropShadowExperimentPage"
             Title="Drop Shadow Experiment">
    <ContentPage.Resources>
        <Style TargetType="Slider">
            <Setter Property="Margin" Value="10, 0" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="dxSlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dxSlider},
                              Path=Value,
                              StringFormat='Horizontal offset = {0:F1}'}" />

        <Slider x:Name="dySlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dySlider},
                              Path=Value,
                              StringFormat='Vertical offset = {0:F1}'}" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}" />
    </StackLayout>
</ContentPage>
```

Il file code-behind utilizza tali valori per creare un'ombreggiatura rossa su una stringa di testo blu:

```csharp
public partial class DropShadowExperimentPage : ContentPage
{
    const string TEXT = "Drop Shadow";

    public DropShadowExperimentPage ()
    {
        InitializeComponent ();
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

        canvas.Clear();

        // Get values from sliders
        float dx = (float)dxSlider.Value;
        float dy = (float)dySlider.Value;
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = info.Width / 7;
            paint.Color = SKColors.Blue;
            paint.ImageFilter = SKImageFilter.CreateDropShadow(
                                    dx,
                                    dy,
                                    sigmaX,
                                    sigmaY,
                                    SKColors.Red,
                                    SKDropShadowImageFilterShadowMode.DrawShadowAndForeground);

            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Center the text in the display rectangle
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Ecco il programma in esecuzione:

[![Rilascia esperimento ombreggiatura](image-filters-images/DropShadowExperiment.png "Rilascia esperimento ombreggiatura")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

I valori di offset negativi nella schermata piattaforma UWP (Universal Windows Platform) all'estrema destra fanno sì che l'ombreggiatura appaia sopra e a sinistra del testo. Questo suggerisce una sorgente di luce in basso a destra, che non è la convenzione per la grafica del computer. Ma non sembra male in alcun modo, forse perché l'ombreggiatura è anche molto sfocata e sembra più ornamentale della maggior parte delle ombreggiature.

## <a name="lighting-effects"></a>Effetti sull'illuminazione

La `SKImageFilter` classe definisce sei metodi con nomi e parametri simili, elencati di seguito in ordine di complessità crescente:

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

Questi metodi creano filtri immagine che simulano l'effetto di diversi tipi di luce su superfici tridimensionali. Il filtro immagine risultante illumina gli oggetti bidimensionali come se fossero presenti nello spazio 3D, che può comportare la visualizzazione di questi oggetti con privilegi elevati o di incasso oppure con evidenziazione speculare.

I `Distant` metodi leggeri presuppongono che la luce provenga da un'estrema distanza. Ai fini dell'illuminazione degli oggetti, si presuppone che la luce punti in una direzione coerente nello spazio 3D, in modo analogo al sole in una piccola area della terra. I `Point` metodi leggeri simulano una lampadina posizionata nello spazio 3D che emette la luce in tutte le direzioni. La `Spot` luce ha una posizione e una direzione, molto simile a una torcia.

I percorsi e le direzioni nello spazio 3D sono entrambi specificati con i valori della [`SKPoint3`](xref:SkiaSharp.SKPoint3) struttura, che è simile a `SKPoint` ma con tre proprietà denominate `X` , `Y` e `Z` .

Il numero e la complessità dei parametri di questi metodi rendono difficoltosa la sperimentazione. Per iniziare, la pagina dell' **esperimento di luce distante** consente di sperimentare i parametri del `CreateDistantLightDiffuse` Metodo:

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

La pagina non usa gli ultimi due parametri facoltativi.

Tre `Slider` visualizzazioni nel file XAML consentono di selezionare la `Z` coordinata del `SKPoint3` valore, il `surfaceScale` parametro e il `kd` parametro, definito nella documentazione dell'API come "costante illuminazione diffusa":

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaLightExperiment.MainPage"
             Title="Distant Light Experiment">

    <StackLayout>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           VerticalOptions="FillAndExpand" />

        <Slider x:Name="zSlider"
                Minimum="-10"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zSlider},
                              Path=Value,
                              StringFormat='Z = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="surfaceScaleSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference surfaceScaleSlider},
                              Path=Value,
                              StringFormat='Surface Scale = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="lightConstantSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference lightConstantSlider},
                              Path=Value,
                              StringFormat='Light Constant = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

Il file code-behind ottiene questi tre valori e li usa per creare un filtro immagine per visualizzare una stringa di testo:

```csharp
public partial class DistantLightExperimentPage : ContentPage
{
    const string TEXT = "Lighting";

    public DistantLightExperimentPage()
    {
        InitializeComponent();
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

        canvas.Clear();

        float z = (float)zSlider.Value;
        float surfaceScale = (float)surfaceScaleSlider.Value;
        float lightConstant = (float)lightConstantSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.IsAntialias = true;

            // Size text to 90% of canvas width
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Find coordinates to center text
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            float xText = info.Rect.MidX - textBounds.MidX;
            float yText = info.Rect.MidY - textBounds.MidY;

            // Create distant light image filter
            paint.ImageFilter = SKImageFilter.CreateDistantLitDiffuse(
                                    new SKPoint3(2, 3, z),
                                    SKColors.White,
                                    surfaceScale,
                                    lightConstant);

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Il primo argomento di `SKImageFilter.CreateDistantLitDiffuse` è la direzione della luce. Le coordinate X e Y positive indicano che la luce è puntata a destra e in basso. Coordinate Z positive che puntano allo schermo. Il file XAML consente di selezionare i valori Z negativi, ma è possibile vedere cosa accade: concettualmente, le coordinate Z negative fanno sì che la luce punti all'esterno dello schermo. Per qualsiasi altro valore negativo, l'effetto di illuminazione smette di funzionare.

L' `surfaceScale` argomento può essere compreso tra-1 e 1. I valori di livello superiore o inferiore non hanno alcun effetto. Si tratta di valori relativi nell'asse Z che indicano lo spostamento dell'oggetto grafico, in questo caso la stringa di testo, dalla superficie dell'area di disegno. Utilizzare valori negativi per generare la stringa di testo sopra la superficie dell'area di disegno e i valori positivi da decomprimere nell'area di disegno.

Il `lightConstant` valore deve essere positivo. (Il programma consente valori negativi, quindi è possibile vedere che l'effetto smette di funzionare). I valori più elevati provocano una luce più intensa.

Questi fattori possono essere bilanciati per ottenere un effetto in rilievo quando `surfaceScale` è negativo (come per gli screenshot iOS e Android) e un effetto inciso quando `surfaceScale` è positivo, come con lo screenshot UWP a destra:

[![Esperimento distante Light](image-filters-images/DistantLightExperiment.png "Esperimento distante Light")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

Lo screenshot Android ha un valore Z pari a 0, che indica che la luce è rivolta verso il basso e verso destra. Lo sfondo non è illuminato e la superficie della stringa di testo non è accesa. La luce ha effetto solo sul bordo del testo per un effetto molto sottile.

Un approccio alternativo al testo in rilievo e inciso è stato illustrato nell'articolo [la trasformazione di conversione](../transforms/translate.md): la stringa di testo viene visualizzata due volte con colori diversi che vengono spostati leggermente tra loro.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
