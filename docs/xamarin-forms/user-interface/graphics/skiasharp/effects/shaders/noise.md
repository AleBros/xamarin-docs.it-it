---
title: SkiaSharp rumore e composizione
description: Generare gli shader di Perlin noise e combinare con altri shader.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 4801aa12acf8eca2384cc5b41d677f7cb0bdd90d
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53052051"
---
# <a name="skiasharp-noise-and-composing"></a>SkiaSharp rumore e composizione

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Grafica vettoriale semplice tende a prospettiche. Le linee rette, curve smooth e colori a tinta unita non sono simili alle imperfezioni degli oggetti reali. Durante l'utilizzo di grafica generato dal computer per il film 1982 _Tron_, esperto di informatica Ken Perlin ha iniziato a sviluppare algoritmi utilizzati processi casuali per offrire queste immagini trame più realistiche. Nel 1997, Ken Perlin vinto un premio Academy per il conseguimento tecnici. Il suo lavoro ha iniziato a essere nota come Perlin noise ed è supportato in SkiaSharp. Di seguito è riportato un esempio:

![Esempio Perlin Noise](noise-images/NoiseSample.png "Perlin Noise esempio")

Come si può notare, ogni pixel contenuto non è un valore di colore casuale. La continuità dal pixel per pixel comporta forme casuale.

Il supporto di Perlin noise in Skia è basato su una specifica del W3C per CSS e SVG. Sezione 8.20 dei [ **a livello di modulo di filtro effetti 1** ](http://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) include gli algoritmi di sottostante Perlin noise nel codice C.

## <a name="exploring-perlin-noise"></a>Esplorazione Perlin noise

Il [ `SKShader` ](xref:SkiaSharp.SKShader) classe definisce due diversi metodi statici per la generazione di Perlin noise: [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) e [ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*). I parametri sono identici:

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

Entrambi i metodi sono inclusi anche in versioni di overload con un'ulteriore `SKPointI` parametro. La sezione [ **affiancamento Perlin noise** ](#tiling-perlin-noise) descrive questi overload.

I due `baseFrequency` gli argomenti sono definiti nella documentazione di SkiaSharp compreso tra 0 e 1 i valori positivi, ma possono essere impostati per i valori più alti. Maggiore è questo valore, maggiore è la modifica dell'immagine casuale nelle direzioni orizzontale e verticale.

Il `numOctaves` valore è un numero intero pari a 1 o versione successiva. In relazione a un fattore di iterazione per gli algoritmi. Ciascuna ottava aggiuntiva favorisce un effetto pari alla metà dell'ottava precedente, in modo che l'effetto diminuisca con i valori più alti octave.

Il `seed` parametro è il punto di partenza per il generatore di numeri casuali. Anche se è specificato come valore a virgola mobile, la frazione viene troncata prima di utilizzarlo e 0 equivale a 1.

Il **Perlin Noise** pagina il [ **SkiaSharpFormsDemos**)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio consente sperimentare con diversi valori del `baseFrequency` e `numOctaves` argomenti. Ecco il file XAML:

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

Usa due `Slider` viste per le due `baseFrequency` argomenti. Per espandere l'intervallo dei valori più bassi, i dispositivi di scorrimento sono logaritmiche. Il file code-behind calcola gli elementi per il `SKShader`metodi dalle potenze del `Slider` valori. Il `Label` consente di visualizzare i valori calcolati:

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

Oggetto `Slider` il valore 1 corrisponde a 0,001 e una `Slider` valore del sistema operativo 2 corrisponde a 0,01, un `Slider` valori pari a 3 corrisponde a 0,1 e un `Slider` valore pari a 4 corrisponde a 1.

Ecco il file code-behind che include tale codice:

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

Ecco il programma in esecuzione in iOS, Android e Universal Windows Platform (UWP) i dispositivi. Il rumore frattale viene visualizzato nella parte superiore dell'area di disegno. È il rumore turbolenza nella parte inferiore della metà:

[![Perlin Noise](noise-images/PerlinNoise.png "Perlin Noise")](noise-images/PerlinNoise-Large.png#lightbox)

Gli stessi argomenti producano sempre lo stesso modello in cui inizia l'angolo superiore sinistro. Questa coerenza è evidente quando si modifica la larghezza e altezza della finestra della piattaforma UWP. Come Windows 10 consente di ricreare la schermata, il modello nella parte superiore dell'area di disegno rimane invariato.

Il motivo di disturbo incorpora vari livelli di trasparenza. La trasparenza diventa evidente se si imposta un colore nel `canvas.Clear()` chiamare. Colore diventa evidente nel modello. Scoprirai anche questo effetto nella sezione [ **combinando più shader**](#combining-multiple-shaders).

Questi modelli di rumore Perlin vengono utilizzati raramente da sole. Essi sono spesso sottoposti per unire le modalità e i filtri di colore illustrati negli articoli relativi alle versioni successive.

## <a name="tiling-perlin-noise"></a>Affiancamento Perlin noise

Il metodo statico due `SKShader` metodi per la creazione di Perlin noise sono presenti anche nelle versioni di overload. Il [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) e [ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) overload presentano un'ulteriore `SKPointI` parametro:

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

Il [ `SKPointI` ](xref:SkiaSharp.SKPointI) struttura è la versione di numero intero di nella versione familiare [ `SKPoint` ](xref:SkiaSharp.SKPoint) struttura. `SKPointI` definisce `X` e `Y` delle proprietà di tipo `int` anziché `float`.

Questi metodi creano un motivo ripetuto della dimensione specificata. In ogni riquadro, del bordo destro è identico al bordo sinistro e il bordo superiore è lo stesso come il bordo inferiore. Questa caratteristica viene illustrata la **affiancato Perlin Noise** pagina. Il file XAML è simile all'esempio precedente, ma è sufficiente una `Stepper` visualizzazione per la modifica di `seed` argomento:

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

Il file code-behind definisce una costante per le dimensioni del riquadro. Il `PaintSurface` gestore crea una bitmap di tale dimensione e un `SKCanvas` per il disegno in quella bitmap. Il `SKShader.CreatePerlinNoiseTurbulence` metodo consente di creare uno shader con tale dimensione. Questo shader viene disegnato sulla bitmap:

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

Dopo che la bitmap è stato creato, un altro `SKPaint` oggetto viene usato per creare un modello affiancato bitmap chiamando `SKShader.CreateBitmap`. Si noti che i due argomenti di `SKShaderTileMode.Repeat`:

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

Questo shader viene utilizzato per coprire l'area di disegno. Infine, un'altra `SKPaint` oggetto viene usato per disegnare un rettangolo che mostra le dimensioni della bitmap originale.

Solo il `seed` parametro è selezionabile dall'interfaccia utente. Se lo stesso `seed` modello viene usato in ogni piattaforma, Visualizza lo stesso modello. Diversi `seed` valori determinano in modi diversi:

[![Affiancato Perlin Noise](noise-images/TiledPerlinNoise.png "affiancato Perlin Noise")](noise-images/TiledPerlinNoise-Large.png#lightbox)

Il modello di 200 pixel quadrato nell'angolo superiore sinistro vengono trasmessi senza problemi gli altri riquadri.

## <a name="combining-multiple-shaders"></a>La combinazione di più gli shader

Il `SKShader` classe include una [ `CreateColor` ](xref:SkiaSharp.SKShader.CreateColor*) metodo che consente di creare uno shader con un colore a tinta unita specificato. Questo shader non è molto utile di per sé perché è sufficiente impostare il colore il `Color` proprietà del `SKPaint` dell'oggetto e impostare il `Shader` proprietà su null.

Ciò `CreateColor` metodo risulta utile in un altro metodo che `SKShader` definisce. Questo metodo è [ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)), che combina due degli shader. Di seguito è riportata la sintassi:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

Il `srcShader` (shader di origine) in modo efficace viene disegnato sopra il `dstShader` (shader di destinazione). Se lo shader di origine è un colore a tinta unita o sfumatura senza trasparenza, lo shader di destinazione sarà completamente nascosti.

Uno shader di Perlin noise contiene la trasparenza. Se tale shader è l'origine, lo shader destinazione mostrerà attraverso le aree trasparenti.

Il **composto da Perlin Noise** pagina dispone di un file XAML che sia virtualmente identico al primo **Perlin Noise** pagina. Il file code-behind è anche simile. Ma originale **Perlin Noise** pagina set il `Shader` proprietà della `SKPaint` allo shader restituito da statico `CreatePerlinNoiseFractalNoise` e `CreatePerlinNoiseTurbulence` metodi. Ciò **composto da Perlin Noise** pagina chiamate `CreateCompose` per uno shader di combinazione. La destinazione è uno shader con colore blu a tinta unita con creati utilizzando `CreateColor`. L'origine è uno shader di Perlin noise:

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

Lo shader di rumore frattale è nella parte superiore; lo shader turbolenza è nella parte inferiore:

[![Composto da Perlin Noise](noise-images/ComposedPerlinNoise.png "composto Perlin Noise")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

Si noti che quanto più gli shader sono rispetto a quelli visualizzati per i **Perlin Noise** pagina. La differenza viene illustrata la quantità di trasparenza negli shader rumore.

È inoltre disponibile un overload del [ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) metodo:

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

L'ultimo parametro è un membro del `SKBlendMode` enumerazione, un'enumerazione con 29 membri è descritto nella prossima serie di articoli sui [ **modalità di composizione e blend SkiaSharp**](../blend-modes/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
