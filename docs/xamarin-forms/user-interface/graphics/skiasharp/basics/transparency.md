---
title: Trasparenza di SkiaSharp
description: Usare la trasparenza per combinare più oggetti in un'unica scena.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B62F9487-C30E-4C63-BAB1-4C091FF50378
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 74335de66e74f6adc7c9488a1b78c31d36d03f14
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759410"
---
# <a name="skiasharp-transparency"></a>Trasparenza di SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Come si è visto, la [ `SKPaint` ](xref:SkiaSharp.SKPaint) classe include una [ `Color` ](xref:SkiaSharp.SKPaint.Color) vlastnosti typu [ `SKColor` ](xref:SkiaSharp.SKColor). `SKColor` include un canale alfa, pertanto, qualsiasi elemento che di colore con un `SKColor` valore può essere parzialmente trasparenti. 

Sono stata illustrata alcuni trasparenza nel [ **animazione di base in SkiaSharp** ](animation.md) articolo. Questo articolo illustra un po' più approfondito la trasparenza per combinare più oggetti in un'unica scena, una tecnica nota anche come _fusione_. Più avanzate con sfumatura tecniche vengono illustrate negli articoli nel [ **shader SkiaSharp** ](../effects/shaders/index.md) sezione.

È possibile impostare il livello di trasparenza al momento della creazione un colore specificando il parametro di quattro [ `SKColor` ](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) costruttore:

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Un valore alfa pari a 0 è completamente trasparente e un valore alfa pari a 0xFF è completamente opaco. I valori compresi tra questi due estremi creano colori parzialmente trasparenti.

È inoltre `SKColor` definisce un comodo [ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*) metodo che crea un nuovo colore da un colore esistente ma con il livello di alfa specificato:

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

Viene illustrato l'utilizzo del testo parzialmente trasparente nel **codice più codice** nella pagina il [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) esempio. Questa pagina viene applicata la dissolvenza due stringhe di testo e disconnettersi, incorporando trasparenza nel `SKColor` valori:

```csharp
public class CodeMoreCodePage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    double transparency;

    public CodeMoreCodePage ()
    {
        Title = "Code More Code";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 5;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        transparency = 0.5 * (1 + Math.Sin(progress * 2 * Math.PI));
        canvasView.InvalidateSurface();

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        const string TEXT1 = "CODE";
        const string TEXT2 = "MORE";

        using (SKPaint paint = new SKPaint())
        {
            // Set text width to fit in width of canvas
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT1);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Center first text string
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT1, ref textBounds);

            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
            canvas.DrawText(TEXT1, xText, yText, paint);

            // Center second text string
            textBounds = new SKRect();
            paint.MeasureText(TEXT2, ref textBounds);

            xText = info.Width / 2 - textBounds.MidX;
            yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
            canvas.DrawText(TEXT2, xText, yText, paint);
        }
    }
}
```

Il `transparency` campo viene animato per variare da 0 a 1 e poi di nuovo in un ritmo sinusoidale. Prima stringa di testo viene visualizzata con un valore alfa calcolato sottraendo il `transparency` valore compreso tra 1:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

Il [ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*) metodo imposta il componente alfa su un colore esistente, che qui è `SKColors.Blue`. Seconda stringa di testo viene utilizzato un valore alfa calcolato dal `transparency` valore stesso:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

L'animazione si alterna tra le due parole, lettera "code" informazioni all'utente (o forse la richiesta "ulteriori code"):

[![Altro codice di codice](transparency-images/CodeMoreCode.png "maggiore quantità di codice del codice")](transparency-images/CodeMoreCode-Large.png#lightbox)

Nell'articolo precedente sulla [ **nozioni di base di Bitmap in SkiaSharp**](bitmaps.md), è stato illustrato come visualizzare le bitmap con uno dei [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap*) metodi di `SKCanvas`. Tutti i `DrawBitmap` metodi includono un `SKPaint` oggetto come ultimo parametro. Per impostazione predefinita, questo parametro è impostato su `null` ed è possibile ignorarlo. 

In alternativa, è possibile impostare il `Color` proprietà di questo oggetto `SKPaint` dell'oggetto per visualizzare una bitmap con un certo livello di trasparenza. Impostazione di un livello di trasparenza nel `Color` proprietà di `SKPaint` consente la dissolvenza in entrata e in uscita bitmap, o di sciogliere una bitmap in un altro. 

Viene illustrata la trasparenza di bitmap il **Bitmap sciogliere** pagina. Il file XAML crea un' `SKCanvasView` e un `Slider`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapDissolvePage"
             Title="Bitmap Dissolve">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

Il file code-behind carica due risorse bitmap. Tali bitmap non hanno le stesse dimensioni, ma sono le stesse proporzioni:

```csharp
public partial class BitmapDissolvePage : ContentPage
{
    SKBitmap bitmap1;
    SKBitmap bitmap2;

    public BitmapDissolvePage()
    {
        InitializeComponent();

        // Load two bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg"))
        {
            bitmap1 = SKBitmap.Decode(stream);
        }
        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.FacePalm.jpg"))
        {
            bitmap2 = SKBitmap.Decode(stream);
        }
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

        // Find rectangle to fit bitmap
        float scale = Math.Min((float)info.Width / bitmap1.Width,
                                (float)info.Height / bitmap1.Height);
        SKRect rect = SKRect.Create(scale * bitmap1.Width,
                                    scale * bitmap1.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Get progress value from Slider
        float progress = (float)progressSlider.Value;

        // Display two bitmaps with transparency
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = paint.Color.WithAlpha((byte)(0xFF * (1 - progress)));
            canvas.DrawBitmap(bitmap1, rect, paint);

            paint.Color = paint.Color.WithAlpha((byte)(0xFF * progress));
            canvas.DrawBitmap(bitmap2, rect, paint);
        }
    }
}
```

Il `Color` proprietà del `SKPaint` oggetto è impostato su due livelli alfa complementari per bitmap di due. Quando si usa `SKPaint` delle bitmap, non è importante che il resto del `Color` è valore. Ciò che conta è il canale alfa. Qui il codice chiama semplicemente il `WithAlpha` sul valore predefinito del metodo di `Color` proprietà.

Lo spostamento di `Slider` dissolvenze tra una singola bitmap e l'altro:

[![Bitmap dissolvenza](transparency-images/BitmapDissolve.png "Bitmap dissolvenza")](transparency-images/BitmapDissolve-Large.png#lightbox)

In diversi articoli precedenti, si è appreso come usare SkiaSharp per disegnare il testo, cerchi, ellissi, rettangoli con angoli arrotondati e bitmap. Il passaggio successivo consiste [SkiaSharp linee e tracciati](../paths/index.md) in cui si apprenderà come disegnare linee collegate in un percorso grafico.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
