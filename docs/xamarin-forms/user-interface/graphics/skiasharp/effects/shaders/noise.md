---
title: SkiaSharp rumore e composizione
description: Genera shader di Perlin e combina con altri shader.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: c1e500936b89f2ec8dc17279a7ed878dc7f5cbb3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029431"
---
# <a name="skiasharp-noise-and-composing"></a>SkiaSharp rumore e composizione

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La grafica vettoriale semplice tende a sembrare non naturale. Le linee rette, le curve smussate e i colori a tinta unita non assomigliano agli inestetismi degli oggetti reali. Quando si lavora con la grafica generata dal computer per il film _Tron_1982, il computer scienziato Ken Perlin ha iniziato a sviluppare algoritmi che usavano processi casuali per fornire a queste immagini trame più realistiche. In 1997, Ken Perlin ha vinto un premio Academy per il successo tecnico. Il suo lavoro è stato conosciuto come il rumore Perlin ed è supportato in SkiaSharp. Di seguito è riportato un esempio:

![Esempio di rumore Perlin](noise-images/NoiseSample.png "Esempio di rumore Perlin")

Come si può notare, ogni pixel non è un valore di colore casuale. La continuità da pixel a pixel produce forme casuali.

Il supporto del rumore Perlin in Skia si basa su una specifica W3C per CSS e SVG. La sezione 8,20 del [**livello 1 del modulo effetti di filtro**](https://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) include gli algoritmi di disturbo di Perlin sottostanti nel codice C.

## <a name="exploring-perlin-noise"></a>Esplorazione del rumore Perlin

La classe [`SKShader`](xref:SkiaSharp.SKShader) definisce due metodi statici diversi per la generazione del rumore Perlin: [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) e [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*). I parametri sono identici:

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

Entrambi i metodi sono presenti anche nelle versioni di overload con un parametro di `SKPointI` aggiuntivo. La sezione relativa al [**disturbo Perlin di affiancamento**](#tiling-perlin-noise) illustra questi overload.

I due argomenti `baseFrequency` sono valori positivi definiti nella documentazione di SkiaSharp, che vanno da 0 a 1, ma possono essere impostati anche su valori più elevati. Maggiore è il valore, maggiore è la modifica nell'immagine casuale nelle direzioni orizzontale e verticale.

Il valore `numOctaves` è un numero intero pari a 1 o superiore. Si riferisce a un fattore di iterazione negli algoritmi. Ogni ottava aggiuntiva contribuisce a un effetto che corrisponde alla metà dell'ottava precedente, quindi l'effetto diminuisce con valori di ottava più alti.

Il parametro `seed` è il punto di partenza per il generatore di numeri casuali. Sebbene venga specificato come valore a virgola mobile, la frazione viene troncata prima di essere utilizzata e 0 corrisponde a 1.

La pagina del **rumore Perlin** nell'esempio [ **SkiaSharpFormsDemos**)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) consente di provare diversi valori degli argomenti `baseFrequency` e `numOctaves`. Ecco il file XAML:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PerlinNoisePage"
             Title="Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="baseFrequencyXSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyXText"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="baseFrequencyYSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyYText"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference octavesStepper},
                                  Path=Value,
                                  StringFormat='Number of Octaves: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="octavesStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

USA due viste `Slider` per i due argomenti di `baseFrequency`. Per espandere l'intervallo dei valori inferiori, i dispositivi di scorrimento sono logaritmici. Il file code-behind calcola gli argomenti per i metodi di `SKShader`dalle potenze dei valori `Slider`. Le visualizzazioni `Label` visualizzano i valori calcolati:

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

Il valore 1 di `Slider` corrisponde a 0,001, un valore `Slider` OS 2 corrisponde a 0,01, un valore di `Slider` pari a 3 corrisponde a 0,1, mentre un valore `Slider` pari a 4 corrisponde a 1.

Ecco il file code-behind che include il codice seguente:

```csharp
public partial class PerlinNoisePage : ContentPage
{
    public PerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader =
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0);

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader =
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0);

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Ecco il programma in esecuzione su dispositivi iOS, Android e piattaforma UWP (Universal Windows Platform) (UWP). Il rumore frattale viene visualizzato nella metà superiore dell'area di disegno. Il rumore di turbolenza è nella metà inferiore:

[![Rumore Perlin](noise-images/PerlinNoise.png "Rumore Perlin")](noise-images/PerlinNoise-Large.png#lightbox)

Gli stessi argomenti producono sempre lo stesso modello che inizia dall'angolo superiore sinistro. Questa coerenza è ovvia quando si regolano la larghezza e l'altezza della finestra UWP. Poiché Windows 10 ridisegna lo schermo, il modello nella metà superiore dell'area di disegno rimane lo stesso.

Il modello di disturbo include diversi gradi di trasparenza. La trasparenza diventa ovvia se si imposta un colore nella chiamata `canvas.Clear()`. Tale colore diventa importante nel modello. Questo effetto verrà visualizzato anche nella sezione combinazione di [**più shader**](#combining-multiple-shaders).

Questi modelli di rumore Perlin vengono usati raramente da soli. Spesso sono soggette alle modalità di Blend e ai filtri colori descritti negli articoli successivi.

## <a name="tiling-perlin-noise"></a>Affiancamento del rumore Perlin

I due metodi di `SKShader` statici per la creazione di un rumore Perlin sono disponibili anche nelle versioni di overload. Gli overload [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) e [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) hanno un parametro `SKPointI` aggiuntivo:

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

La struttura [`SKPointI`](xref:SkiaSharp.SKPointI) è la versione Integer della struttura familiare [`SKPoint`](xref:SkiaSharp.SKPoint) . `SKPointI` definisce `X` e `Y` proprietà di tipo `int` anziché `float`.

Questi metodi creano un modello ripetuto della dimensione specificata. In ogni riquadro il bordo destro corrisponde al bordo sinistro e il bordo superiore è lo stesso del bordo inferiore. Questa caratteristica è illustrata nella pagina del **rumore di Perlin affiancata** . Il file XAML è simile all'esempio precedente, ma dispone solo di una vista `Stepper` per la modifica dell'argomento `seed`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.TiledPerlinNoisePage"
             Title="Tiled Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference seedStepper},
                                  Path=Value,
                                  StringFormat='Seed: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="seedStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

Il file code-behind definisce una costante per le dimensioni del riquadro. Il gestore di `PaintSurface` crea una bitmap di tale dimensione e un `SKCanvas` per il disegno in tale bitmap. Il metodo `SKShader.CreatePerlinNoiseTurbulence` crea uno shader con le dimensioni del riquadro. Questo shader viene disegnato sulla bitmap:

```csharp
public partial class TiledPerlinNoisePage : ContentPage
{
    const int TILE_SIZE = 200;

    public TiledPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get seed value from stepper
        float seed = (float)seedStepper.Value;

        SKRect tileRect = new SKRect(0, 0, TILE_SIZE, TILE_SIZE);

        using (SKBitmap bitmap = new SKBitmap(TILE_SIZE, TILE_SIZE))
        {
            using (SKCanvas bitmapCanvas = new SKCanvas(bitmap))
            {
                bitmapCanvas.Clear();

                // Draw tiled turbulence noise on bitmap
                using (SKPaint paint = new SKPaint())
                {
                    paint.Shader = SKShader.CreatePerlinNoiseTurbulence(
                                        0.02f, 0.02f, 1, seed,
                                        new SKPointI(TILE_SIZE, TILE_SIZE));

                    bitmapCanvas.DrawRect(tileRect, paint);
                }
            }

            // Draw tiled bitmap shader on canvas
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
                canvas.DrawRect(info.Rect, paint);
            }

            // Draw rectangle showing tile
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 2;

                canvas.DrawRect(tileRect, paint);
            }
        }
    }
}
```

Dopo la creazione della bitmap, viene usato un altro oggetto `SKPaint` per creare un modello di bitmap affiancata chiamando `SKShader.CreateBitmap`. Si notino i due argomenti di `SKShaderTileMode.Repeat`:

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

Questo shader viene usato per coprire l'area di disegno. Infine, viene usato un altro oggetto `SKPaint` per tracciare un rettangolo che mostra le dimensioni della bitmap originale.

Dall'interfaccia utente è selezionabile solo il parametro `seed`. Se si usa lo stesso modello di `seed` in ogni piattaforma, viene visualizzato lo stesso modello. Valori `seed` diversi generano modelli diversi:

[![Disturbo Perlin affiancato](noise-images/TiledPerlinNoise.png "Disturbo Perlin affiancato")](noise-images/TiledPerlinNoise-Large.png#lightbox)

Il modello quadrato di 200 pixel nell'angolo superiore sinistro passa facilmente agli altri riquadri.

## <a name="combining-multiple-shaders"></a>Combinazione di più shader

La classe `SKShader` include un metodo [`CreateColor`](xref:SkiaSharp.SKShader.CreateColor*) che consente di creare uno shader con un colore a tinta unita specificato. Questo shader non è molto utile perché è possibile semplicemente impostare tale colore sulla proprietà `Color` dell'oggetto `SKPaint` e impostare la proprietà `Shader` su null.

Questo `CreateColor` metodo risulta utile in un altro metodo definito da `SKShader`. Questo metodo è [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)), che combina due shader. Ecco la sintassi:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

Il `srcShader` (source shader) viene effettivamente disegnato sopra il `dstShader` (shader di destinazione). Se lo shader di origine è un colore a tinta unita o una sfumatura senza trasparenza, lo shader di destinazione verrà completamente oscurato.

Uno shader Perlin contiene trasparenza. Se lo shader è l'origine, lo shader di destinazione verrà visualizzato attraverso le aree trasparenti.

La pagina del **rumore Perl in composto** include un file XAML praticamente identico alla prima pagina del **rumore Perlin** . Anche il file code-behind è simile. Tuttavia, la pagina originale del **rumore Perlin** imposta la proprietà `Shader` di `SKPaint` sullo shader restituito dai metodi statici di `CreatePerlinNoiseFractalNoise` e `CreatePerlinNoiseTurbulence`. Questa combinazione di chiamate a pagine non **significative perlin** `CreateCompose` per uno shader combinato. La destinazione è uno shader a tinta unita blu creato con `CreateColor`. Il codice sorgente è uno shader Perlin:

```csharp
public partial class ComposedPerlinNoisePage : ContentPage
{
    public ComposedPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0));

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0));

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Lo shader del rumore frattale si trova nella parte superiore; lo shader di turbolenza è in fondo:

[![Disturbo Perlin composto](noise-images/ComposedPerlinNoise.png "Disturbo Perlin composto")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

Si noti che questi shader sono molto blu rispetto a quelli visualizzati dalla pagina del **rumore Perlin** . La differenza illustra la quantità di trasparenza negli shader.

Esiste anche un overload del metodo [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) :

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

Il parametro finale è un membro dell'enumerazione `SKBlendMode`, un'enumerazione con 29 membri che viene discussa nella serie successiva di articoli sulle [**modalità di composizione e fusione SkiaSharp**](../blend-modes/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
