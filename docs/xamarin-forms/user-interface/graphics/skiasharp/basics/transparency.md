---
title: Descrizione "SkiaSharp transparency": "usare la trasparenza per combinare più oggetti in un'unica scena".
ms. prod: Novell MS. Technology: Novell-skiasharp ms. AssetID: B62F9487-C30E-4C63-BAB1-4C091FF50378 autore: davidbritch ms. Author: dabritch ms. Date: 08/23/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="skiasharp-transparency"></a>Trasparenza SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Come si è visto, la [`SKPaint`](xref:SkiaSharp.SKPaint) classe include una [`Color`](xref:SkiaSharp.SKPaint.Color) proprietà di tipo [`SKColor`](xref:SkiaSharp.SKColor) . `SKColor`include un canale alfa, quindi tutto ciò che si colora con un `SKColor` valore può essere parzialmente trasparente. 

Una certa trasparenza è stata illustrata nell'articolo [**animazione di base in SkiaSharp**](animation.md) . Questo articolo illustra in modo più approfondito la trasparenza per combinare più oggetti in un'unica scena, una tecnica talvolta nota come _fusione_. Le tecniche di fusione più avanzate sono illustrate negli articoli della sezione [**shader SkiaSharp**](../effects/shaders/index.md) .

È possibile impostare il livello di trasparenza quando si crea per la prima volta un colore usando il costruttore a quattro parametri [`SKColor`](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) :

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Un valore alfa pari a 0 è completamente trasparente e un valore alfa di 0xFF è completamente opaco. I valori tra questi due estremi creano colori parzialmente trasparenti.

`SKColor`Definisce inoltre un metodo pratico per la [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) creazione di un nuovo colore da un colore esistente ma con il livello alfa specificato:

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

L'uso del testo parzialmente trasparente viene illustrato nella tabella **Codici altro** nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Questa pagina consente di dissolvere due stringhe di testo in entrata e in uscita incorporando la trasparenza nei `SKColor` valori:

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

Il `transparency` campo viene animato per variare da 0 a 1 e viceversa in un ritmo sinusoidale. La prima stringa di testo viene visualizzata con un valore alfa calcolato sottraendo il `transparency` valore da 1:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

Il [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) metodo imposta il componente alfa su un colore esistente, che in questo caso è `SKColors.Blue` . La seconda stringa di testo usa un valore alfa calcolato dal `transparency` valore stesso:

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

L'animazione alterna le due parole, esortando l'utente a "Code more" (o forse richiedendo "altro codice"):

[![Codice altro codice](transparency-images/CodeMoreCode.png "Codice altro codice")](transparency-images/CodeMoreCode-Large.png#lightbox)

Nell'articolo precedente sui [**concetti di base di bitmap in SkiaSharp**](bitmaps.md)è stato illustrato come visualizzare le bitmap usando uno dei [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap*) metodi di `SKCanvas` . Tutti i `DrawBitmap` metodi includono un `SKPaint` oggetto come ultimo parametro. Per impostazione predefinita, questo parametro è impostato su `null` ed è possibile ignorarlo. 

In alternativa, è possibile impostare la `Color` proprietà di questo `SKPaint` oggetto per visualizzare una bitmap con un certo livello di trasparenza. Impostando un livello di trasparenza nella `Color` proprietà di `SKPaint` è possibile dissolvere bitmap in entrata e in uscita o dissolverne una bitmap in un'altra. 

La trasparenza bitmap è illustrata nella pagina **dissolvenza bitmap** . Il file XAML crea un'istanza di `SKCanvasView` e `Slider` :

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

Il file code-behind carica due risorse bitmap. Queste bitmap non hanno le stesse dimensioni, ma hanno le stesse proporzioni:

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

La `Color` proprietà dell' `SKPaint` oggetto è impostata su due livelli Alpha complementari per le due bitmap. Quando `SKPaint` si usa con bitmap, non importa quale sia il resto del `Color` valore. Tutto ciò che conta è il canale alfa. Il codice qui chiama semplicemente il `WithAlpha` metodo sul valore predefinito della `Color` Proprietà.

Lo stato di `Slider` dissolvenza tra una bitmap e l'altra:

[![Dissolvenza bitmap](transparency-images/BitmapDissolve.png "Dissolvenza bitmap")](transparency-images/BitmapDissolve-Large.png#lightbox)

Negli ultimi articoli, è stato illustrato come usare SkiaSharp per creare testo, cerchi, ellissi, rettangoli arrotondati e bitmap. Il passaggio successivo è [SkiaSharp linee e percorsi](../paths/index.md) in cui si apprenderà come creare linee connesse in un percorso grafico.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
