---
title: Filtri immagini di SkiaSharp
description: Informazioni su come usare il filtro di immagine per creare le sfumature e ombreggiature.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 173E7B22-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: f93f0462d476daaaa551833391b1be1865795476
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770545"
---
# <a name="skiasharp-image-filters"></a>Filtri immagini di SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

I filtri di immagine sono gli effetti che operano su tutti i bit di colore del pixel che compongono un'immagine. Sono più versatile filtri mask, che funzionano solo sul canale alfa, come descritto nell'articolo [ **filtri maschera SkiaSharp**](mask-filters.md). Per usare un filtro di immagine, impostare il [ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter) proprietà della `SKPaint` a un oggetto di tipo [ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter) che è stato creato chiamando uno dei metodi statici della classe.

Il modo migliore per acquisire familiarità con i filtri di mask è eseguendo più esperimenti con questi metodi statici. È possibile usare un filtro di un'intera bitmap di sfocatura:

![Esempio Blur](image-filters-images/ImageFilterExample.png "Blur esempio")

Questo articolo illustra anche l'utilizzo di un filtro di immagini per creare un calo ombreggiatura e per rilievo e incisione effetti.

## <a name="blurring-vector-graphics-and-bitmaps"></a>Grafica vettoriale e bitmap di sfocatura

L'effetto di sfocatura creato [`SKImageFilter.CreateBlur`](xref:SkiaSharp.SKImageFilter.CreateBlur*) dal metodo statico presenta un vantaggio significativo rispetto ai metodi [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) Blur della classe: Il filtro immagine può offuscare un'intera bitmap. Il metodo ha la sintassi seguente:

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

Il metodo ha due valori sigma &mdash; la prima per l'estensione della sfocatura in direzione orizzontale e il secondo per la direzione verticale. Si possono propagare i filtri di immagine, specificando un altro filtro di immagine come terzo argomento facoltativo. È possibile specificare anche un rettangolo di ritaglio.

Il **immagine Blur sperimentare** nella pagina il [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) include due `Slider` viste che consentono di provare a impostare vari livelli di sfocatura:

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

Il file code-behind usa due `Slider` i valori per chiamare `SKImageFilter.CreateBlur` per il `SKPaint` oggetto utilizzato per visualizzare il testo e una mappa di bit:

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

Le tre schermate illustrano varie impostazioni per il `sigmaX` e `sigmaY` impostazioni:

[![Immagine di esperimento di sfocatura](image-filters-images/ImageBlurExperiment.png "immagine sfocatura esperimento")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

Per mantenere la sfocatura coerenti tra le diverse dimensioni e risoluzioni, impostare `sigmaX` e `sigmaY` su valori proporzionali alla dimensione dell'immagine di sfocatura applicato al pixel viene eseguito il rendering.

## <a name="drop-shadow"></a>Ombreggiatura

Il [ `SKImageFilter.CreateDropShadow` ](xref:SkiaSharp.SKImageFilter.CreateDropShadow*) metodo statico crea un' `SKImageFilter` oggetto per un'ombreggiatura:

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

Impostare questo oggetto il `ImageFilter` proprietà di un `SKPaint` oggetto e qualsiasi elemento disegnato con quell'oggetto avrà un'ombreggiatura dietro di essa.

Il `dx` e `dy` parametri indicano gli offset orizzontali e verticali dell'ombreggiatura in pixel dell'oggetto grafico. La convenzione di grafica 2D è presupporre che una sorgente di luce provenienti dall'alto a sinistra, che implica che entrambi questi argomenti devono essere positivi per posizionare l'ombreggiatura sotto e a destra dell'oggetto grafico.

Il `sigmaX` e `sigmaY` parametri sono più netti fattori per l'ombreggiatura.

Il `color` parametro corrisponde al colore dell'ombreggiatura. Ciò `SKColor` valore può includere la trasparenza. Una possibilità è il valore del colore `SKColors.Black.WithAlpha(0x80)` viene resa più scura qualsiasi colore di sfondo.

I due parametri finali sono facoltativi.

Il **Drop Shadow sperimentare** programma consente di sperimentare i valori di `dx`, `dy`, `sigmaX`, e `sigmaY` per visualizzare una stringa di testo con un'ombreggiatura. Il file XAML crea un'istanza di quattro `Slider` viste per impostare questi valori:

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

Il file code-behind Usa tali valori per creare un'ombreggiatura rosso in una stringa di testo in blu:

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

[![Eliminare l'esperimento Shadow](image-filters-images/DropShadowExperiment.png "Drop Shadow esperimento")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

I valori di offset negativi nella schermata (Universal Windows Platform) all'estrema destra causano l'ombreggiatura per vengano visualizzati sopra e a sinistra del testo. Ciò suggerisce una sorgente di luce in basso a destra, che non è la convenzione per infografica. Ma non sembra errato in alcun modo, probabilmente perché l'ombreggiatura viene anche eseguita molto sfocato e sembra più scopi ornamentale rispetto la maggior parte delle ombreggiature.

## <a name="lighting-effects"></a>Effetti di illuminazione

Il `SKImageFilter` classe definisce i sei metodi che hanno nomi e parametri, elencati in ordine crescente complessità simili:

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

Questi metodi creano i filtri di immagine che simulano l'effetto di tipi diversi di luce su superfici tridimensionale. Filtro dell'immagine risultante bidimensionali illumina gli oggetti presenti come se fosse presentato nello spazio 3D, che può causare questi oggetti vengano visualizzati con privilegi elevati o scollegato, o con l'illuminazione speculare.

Il `Distant` chiari metodi presuppongono che la luce proviene da una distanza decisamente. Ai fini di illuminanti oggetti, la luce presuppone in modo che punti in un'unica direzione coerente nello spazio 3D, in modo analogo il sole a una piccola superficie della terra. Il `Point` metodi chiari simulano una lampadina nello spazio 3D che emette luce in tutte le direzioni. Il `Spot` light dispone di una posizione e una direzione, molto simile a un torcia.

Percorsi e le direzioni nello spazio 3D sono entrambi specificate con valori del [ `SKPoint3` ](xref:SkiaSharp.SKPoint3) struttura, che è simile a `SKPoint` ma con tre proprietà denominate `X`, `Y`, e `Z`.

Il numero e la complessità dei parametri a questi metodi complicare la sperimentazione con essi. Per iniziare a usare, il **sperimentare luce lontano** pagina consente di sperimentare i parametri per il `CreateDistantLightDiffuse` metodo:

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

La pagina non usa gli ultimi due parametri facoltativi.

Tre `Slider` visualizzazioni in XAML i file consentono selezionare il `Z` coordinate del `SKPoint3` valore, il `surfaceScale` parametro e il `kd` parametro, che viene definito nella documentazione dell'API come costante di illuminazione con riflessione diffusa"":

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

Il file code-behind ottenuto questi tre valori e li utilizza per creare un filtro di immagini per visualizzare una stringa di testo:

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

Il primo argomento di `SKImageFilter.CreateDistantLitDiffuse` è la direzione della luce. Positivo X e Y coordinate indicano che la luce viene punta a destra e verso il basso. Punti di coordinate Z positivo nella schermata. Il file XAML consente di selezionare i valori Z negativi, ma è possibile vedere cosa accade: Dal punto di vista concettuale, le coordinate Z negative fanno sì che la luce punti all'esterno dello schermo. Per qualsiasi altro quindi piccoli valori negativi, l'effetto di illuminazione smette di funzionare.

Il `surfaceScale` argomenti sono compresi tra -1 e 1. (I valori superiori o inferiori non hanno alcun effetto). Questi sono valori relativi all'asse Z che indicano lo spostamento dell'oggetto grafico (in questo caso, la stringa di testo) dall'area di disegno. Usare i valori negativi per generare la stringa di testo sopra l'area dell'area di disegno e i valori positivi di deteriorare, nell'area di disegno.

Il `lightConstant` valore deve essere positivo. (Il programma consente i valori negativi in modo che è possibile vedere che provocano l'effetto smetterebbe di funzionare). I valori più alti causano più intenso è chiaro.

È possono bilanciare tali fattori per ottenere un effetto in rilievo effetto quando `surfaceScale` è negativo (come per iOS e Android schermate) e un incassati effetto quando `surfaceScale` è positivo, come con la schermata UWP a destra:

[![Esperimento di luce lontano](image-filters-images/DistantLightExperiment.png "distante esperimento chiaro")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

Lo screenshot di Android ha un valore Z di 0, a indicare che la luce solo che punta verso il basso e a destra. Lo sfondo non viene acceso e non viene acceso area della stringa di testo di entrambi. Alla luce hanno effetto solo il bordo del testo per un effetto molto complesso.

Un approccio alternativo al testo in rilievo e inciso è stato illustrato nell'articolo [la trasformazione translate](../transforms/translate.md): La stringa di testo viene visualizzata due volte con colori diversi che vengono spostati leggermente l'uno dall'altro.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
