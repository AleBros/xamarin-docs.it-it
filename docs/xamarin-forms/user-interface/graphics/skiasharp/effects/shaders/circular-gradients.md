---
title: Sfumature circolari SkiaSharp
description: Informazioni sui diversi tipi di sfumature basate sui cerchi.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 418d29010a8cce81d2bb8c365608c54b61739622
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135642"
---
# <a name="the-skiasharp-circular-gradients"></a>Sfumature circolari SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La [`SKShader`](xref:SkiaSharp.SKShader) classe definisce i metodi statici per creare quattro tipi diversi di sfumature. L'articolo relativo alla [**sfumatura lineare SkiaSharp**](linear-gradient.md) illustra il [`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) metodo. Questo articolo illustra gli altri tre tipi di gradienti, tutti basati sui cerchi.

Il [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*) metodo crea una sfumatura che emana dal centro di un cerchio:

![Esempio di sfumatura radiale](circular-gradients-images/RadialGradientSample.png)

Il [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*) metodo crea una sfumatura che si estende attorno al centro di un cerchio:

![Esempio di sfumatura Sweep](circular-gradients-images/SweepGradientSample.png)

Il terzo tipo di sfumatura è piuttosto insolito. Viene chiamato sfumatura conica a due punti ed è definito dal [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) metodo. La sfumatura si estende da un cerchio a un altro:

![Esempio di sfumatura conica](circular-gradients-images/ConicalGradientSample.png)

Se i due cerchi hanno dimensioni diverse, la sfumatura assume la forma di un cono.

In questo articolo vengono esaminate in modo più dettagliato queste sfumature.

## <a name="the-radial-gradient"></a>Sfumatura radiale

Il [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) metodo presenta la sintassi seguente:

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Un [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) Overload include anche un parametro della matrice di trasformazione.

I primi due argomenti specificano il centro di un cerchio e un raggio. La sfumatura inizia in corrispondenza di tale centro ed estende verso l'esterno per i `radius` pixel. Ciò che accade oltre `radius` dipende dall' [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) argomento. Il `colors` parametro è una matrice di due o più colori (esattamente come nei metodi di sfumatura lineare) ed `colorPos` è una matrice di numeri interi nell'intervallo compreso tra 0 e 1. Questi numeri interi indicano le posizioni relative dei colori lungo la `radius` riga. È possibile impostare l'argomento su `null` per spaziare equamente i colori.

Se si utilizza `CreateRadialGradient` per riempire un cerchio, è possibile impostare il centro della sfumatura sul centro del cerchio e il raggio della sfumatura sul raggio del cerchio. In tal caso, l' `SKShaderTileMode` argomento non ha alcun effetto sul rendering della sfumatura. Tuttavia, se l'area riempita dalla sfumatura è più grande del cerchio definito dalla sfumatura, l' `SKShaderTileMode` argomento ha un effetto profondo su ciò che accade all'esterno del cerchio.

L'effetto di `SKShaderMode` è illustrato nella pagina di **sfumatura radiale** nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Il file XAML per questa pagina Crea un'istanza di un oggetto `Picker` che consente di selezionare uno dei tre membri dell' `SKShaderTileMode` enumerazione:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.RadialGradientPage"
             Title="Radial Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="0"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

Il file code-behind colora l'intera area di disegno con una sfumatura radiale. Il centro della sfumatura è impostato sul centro dell'area di disegno e il raggio è impostato su 100 pixel. La sfumatura è costituita solo da due colori, nero e bianco:

```csharp
public partial class RadialGradientPage : ContentPage
{
    public RadialGradientPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                new SKPoint(info.Rect.MidX, info.Rect.MidY),
                                100,
                                new SKColor[] { SKColors.Black, SKColors.White },
                                null,
                                tileMode);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Questo codice crea una sfumatura con nero al centro, che si dissolve gradualmente in bianco di 100 pixel dal centro. Ciò che accade oltre tale raggio dipende dall' `SKShaderTileMode` argomento:

[![Sfumatura radiale](circular-gradients-images/RadialGradient.png "Sfumatura radiale")](circular-gradients-images/RadialGradient-Large.png#lightbox)

In tutti e tre i casi, la sfumatura riempie l'area di disegno. Nella schermata iOS a sinistra, la sfumatura oltre il raggio continua con l'ultimo colore, che è bianco. Questo è il risultato di `SKShaderTileMode.Clamp` . La schermata Android Mostra l'effetto di `SKShaderTileMode.Repeat` : a 100 pixel dal centro, la sfumatura inizia di nuovo con il primo colore, che è il nero. La sfumatura viene ripetuta ogni 100 pixel di raggio. 

La schermata piattaforma UWP (Universal Windows Platform) a destra Mostra come `SKShaderTileMode.Mirror` fa in modo che le sfumature alternano le direzioni. La prima sfumatura è da nero al centro verso il bianco a un raggio di 100 pixel. Il successivo è bianco dal raggio di 100 pixel al nero a un raggio di 200 pixel e la sfumatura successiva viene nuovamente invertita.

È possibile utilizzare più di due colori in una sfumatura radiale. L'esempio di **gradiente Rainbow Arc** crea una matrice di otto colori corrispondenti ai colori dell'arcobaleno e termina con il rosso, oltre a una matrice di otto valori di posizione:

```csharp
public class RainbowArcGradientPage : ContentPage
{
    public RainbowArcGradientPage ()
    {
        Title = "Rainbow Arc Gradient";

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
            float rainbowWidth = Math.Min(info.Width, info.Height) / 4f;

            // Center of arc and gradient is lower-right corner
            SKPoint center = new SKPoint(info.Width, info.Height);

            // Find outer, inner, and middle radius
            float outerRadius = Math.Min(info.Width, info.Height);
            float innerRadius = outerRadius - rainbowWidth;
            float radius = outerRadius - rainbowWidth / 2;

            // Calculate the colors and positions
            SKColor[] colors = new SKColor[8];
            float[] positions = new float[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
                positions[i] = (i + (7f - i) * innerRadius / outerRadius) / 7f;
            }

            // Create sweep gradient based on center and outer radius
            paint.Shader = SKShader.CreateRadialGradient(center, 
                                                         outerRadius, 
                                                         colors, 
                                                         positions, 
                                                         SKShaderTileMode.Clamp);
            // Draw a circle with a wide line
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = rainbowWidth;

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

Si supponga che il valore minimo della larghezza e dell'altezza dell'area di disegno sia 1000, il che significa che il `rainbowWidth` valore è 250. I `outerRadius` `innerRadius` valori e sono rispettivamente impostati su 1000 e 750. Questi valori vengono utilizzati per il calcolo della `positions` matrice; gli otto valori sono compresi tra 0,75 f e 1. Il `radius` valore viene utilizzato per tracciare il cerchio. Il valore 875 indica che la larghezza del tratto 250-pixel si estende tra il raggio di 750 pixel e il raggio di 1000 pixel:

[![Sfumatura arco arcobaleno](circular-gradients-images/RainbowArcGradient.png "Sfumatura arco arcobaleno")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Se si riempie l'intera area di disegno con questa sfumatura, si noterà che è rossa all'interno del raggio interno. Questo è dovuto al fatto che la `positions` matrice non inizia con 0. Il primo colore viene usato per gli offset di 0 attraverso il primo valore di matrice. La sfumatura è inoltre rossa oltre il raggio esterno. Questo è il risultato della `Clamp` modalità affiancata. Poiché la sfumatura viene utilizzata per il segno di una linea spessa, queste aree rosse non sono visibili.

## <a name="radial-gradients-for-masking"></a>Sfumature radiali per la maschera

Come le sfumature lineari, le sfumature radiali possono incorporare colori trasparenti o parzialmente trasparenti. Questa funzionalità è utile per un processo denominato _mascheramento_, che nasconde parte di un'immagine per accentuare un'altra parte dell'immagine.

La pagina **maschera di sfumatura radiale** Mostra un esempio. Il programma carica una delle bitmap di risorsa. I `CENTER` `RADIUS` campi e sono stati determinati da un esame della bitmap e fanno riferimento a un'area da evidenziare. Il `PaintSurface` gestore inizia con il calcolo di un rettangolo per visualizzare la bitmap e quindi la Visualizza in tale rettangolo:

```csharp
public class RadialGradientMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(RadialGradientMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public RadialGradientMaskPage ()
    {
        Title = "Radial Gradient Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                                (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                     scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Transparent,
                                                SKColors.White },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            // Display rectangle using that gradient
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Dopo aver disegnato la bitmap, il codice semplice `CENTER` viene convertito e `RADIUS` in `center` e `radius` , che fanno riferimento all'area evidenziata nella bitmap che è stata ridimensionata e spostata per la visualizzazione. Questi valori vengono usati per creare una sfumatura radiale con il centro e il raggio. I due colori iniziano con trasparente al centro e per il primo 60% del raggio. La sfumatura viene quindi dissolta in bianco:

[![Maschera sfumatura radiale](circular-gradients-images/RadialGradientMask.png "Maschera sfumatura radiale")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Questo approccio non è il modo migliore per mascherare una bitmap. Il problema consiste nel fatto che la maschera ha principalmente il colore bianco, che è stato scelto per corrispondere allo sfondo dell'area di disegno. Se lo sfondo è un altro colore &mdash; o forse una sfumatura &mdash; , non corrisponderà. Un approccio migliore alla maschera è illustrato nell'articolo modalità di [Blend di SkiaSharp Porter-Duff](../blend-modes/porter-duff.md).

## <a name="radial-gradients-for-specular-highlights"></a>Sfumature radiali per le evidenziazioni speculari

Quando una luce colpisce una superficie arrotondata, riflette la luce in molte direzioni, ma parte della luce rimbalza direttamente nell'occhio del visualizzatore. Questa operazione spesso crea l'aspetto di un'area bianca fuzzy sulla superficie denominata _evidenziazione speculare_.

Nei grafici tridimensionali, le evidenziazioni speculari spesso derivano dagli algoritmi usati per determinare i percorsi leggeri e le ombreggiature. Nella grafica bidimensionale le evidenziazioni speculari vengono talvolta aggiunte per suggerire l'aspetto di una superficie 3D. Un'evidenziazione speculare può trasformare un cerchio rosso piatto in una palla rossa rotonda.

La pagina dell' **evidenziazione speculare radiale** usa una sfumatura radiale per eseguire esattamente questa operazione. Gli `PaintSurface` esseri del gestore calcolano un raggio per il cerchio e due `SKPoint` valori &mdash; a `center` e un oggetto a `offCenter` metà tra il centro e il bordo superiore sinistro del cerchio:

```csharp
public class RadialSpecularHighlightPage : ContentPage
{
    public RadialSpecularHighlightPage()
    {
        Title = "Radial Specular Highlight";

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

        float radius = 0.4f * Math.Min(info.Width, info.Height);
        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        SKPoint offCenter = center - new SKPoint(radius / 2, radius / 2);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                offCenter,
                                radius / 2,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

La `CreateRadialGradient` chiamata crea una sfumatura che inizia in corrispondenza di quel `offCenter` punto con il bianco e termina con il rosso a una distanza dalla metà del raggio. e ha l'aspetto seguente:

[![Evidenziazione speculare radiale](circular-gradients-images/RadialSpecularHighlight.png "Evidenziazione speculare radiale")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Se si osserva attentamente questa sfumatura, si potrebbe decidere che è difettosa. La sfumatura è centrata intorno a un particolare punto e potrebbe essere necessario che si tratti di un po' meno simmetrico per riflettere la superficie arrotondata. In tal caso, è possibile preferire l'evidenziazione speculare riportata di seguito nella sezione [**gradienti conici per le evidenziazioni speculari**](#conical-gradients-for-specular-highlights).

## <a name="the-sweep-gradient"></a>Sfumatura Sweep

Il [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) metodo ha la sintassi più semplice di tutti i metodi di creazione di sfumatura:

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

Si tratta solo di un centro, una matrice di colori e le posizioni dei colori. La sfumatura inizia a destra del punto centrale e si estende 360 gradi in senso orario intorno al centro. Si noti che non esiste alcun `SKShaderTileMode` parametro.

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix))È disponibile anche un overload con un parametro di trasformazione matrice. È possibile applicare una trasformazione di rotazione alla sfumatura per modificare il punto di partenza. È anche possibile applicare una trasformazione scala per modificare la direzione da senso orario a antiorario.

La pagina **sfumatura Sweep** usa una sfumatura di sweep per colorare un cerchio con una larghezza del tratto di 50 pixel:

[![Sfumatura Sweep](circular-gradients-images/SweepGradient.png "Sfumatura Sweep")](circular-gradients-images/SweepGradient-Large.png#lightbox)

La `SweepGradientPage` classe definisce una matrice di otto colori con valori di tonalità diversi. Si noti che la matrice inizia e termina con il rosso (un valore di tonalità pari a 0 o 360), visualizzato all'estrema destra negli screenshot:

```csharp
public class SweepGradientPage : ContentPage
{
    bool drawBackground;

    public SweepGradientPage ()
    {
        Title = "Sweep Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Define an array of rainbow colors
            SKColor[] colors = new SKColor[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
            }

            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);

            // Create sweep gradient based on center of canvas
            paint.Shader = SKShader.CreateSweepGradient(center, colors, null);

            // Draw a circle with a wide line
            const int strokeWidth = 50;
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = strokeWidth;

            float radius = (Math.Min(info.Width, info.Height) - strokeWidth) / 2;
            canvas.DrawCircle(center, radius, paint);

            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                paint.Style = SKPaintStyle.Fill;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

Il programma implementa anche un `TapGestureRecognizer` che Abilita il codice alla fine del `PaintSurface` gestore. Questo codice usa la stessa sfumatura per riempire l'area di disegno:

[![Sfumatura completa Sweep](circular-gradients-images/SweepGradientFull.png "Sfumatura completa Sweep")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Queste schermate dimostrano che la sfumatura riempie l'area in cui è colorato. Se la sfumatura non inizia e termina con lo stesso colore, sarà presente una discontinuità a destra del punto centrale.

## <a name="the-two-point-conical-gradient"></a>Sfumatura conica a due punti

Il [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) metodo presenta la sintassi seguente:

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

I parametri iniziano con i punti centrali e i raggi per due cerchi, detti cerchio _iniziale_ e cerchio _finale_ . I tre parametri rimanenti sono uguali a quelli di `CreateLinearGradient` e `CreateRadialGradient` . Un [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) Overload include una trasformazione matrice.

La sfumatura inizia dal cerchio iniziale e termina in corrispondenza del cerchio finale. Il `SKShaderTileMode` parametro determina ciò che accade oltre i due cerchi. La sfumatura conica a due punti è l'unica sfumatura che non riempie completamente un'area. Se i due cerchi hanno lo stesso raggio, la sfumatura è limitata a un rettangolo con una larghezza uguale al diametro dei cerchi. Se i due cerchi hanno raggi diversi, la sfumatura forma un cono.

È probabile che si voglia sperimentare la sfumatura conica a due punti, quindi la pagina di **sfumatura conica** deriva da `InteractivePage` per consentire lo spostamento di due punti di tocco per i due raggi del cerchio:

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.ConicalGradientPage"
                       Title="Conical Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        
        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

Il file code-behind definisce i due `TouchPoint` oggetti con un raggio fisso di 50 e 100:

```csharp
public partial class ConicalGradientPage : InteractivePage
{
    const int RADIUS1 = 50;
    const int RADIUS2 = 100;

    public ConicalGradientPage ()
    {
        touchPoints = new TouchPoint[2];

        touchPoints[0] = new TouchPoint
        {
            Center = new SKPoint(100, 100),
            Radius = RADIUS1
        };

        touchPoints[1] = new TouchPoint
        {
            Center = new SKPoint(300, 300),
            Radius = RADIUS2
        };

        InitializeComponent();
        baseCanvasView = canvasView;
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode = 
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ? 
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(touchPoints[0].Center,
                                                                  RADIUS1,
                                                                  touchPoints[1].Center,
                                                                  RADIUS2,
                                                                  colors,
                                                                  null,
                                                                  tileMode);
            canvas.DrawRect(info.Rect, paint);
        }

        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }
        }
    }
}
```

La `colors` matrice è rossa, verde e blu. Il codice verso la parte inferiore del `PaintSurface` gestore disegna i due punti di tocco come cerchi neri, in modo da non ostacolare la sfumatura.

Si noti che `DrawRect` Call usa la sfumatura per colorare l'intera area di disegno. Nel caso generale, tuttavia, gran parte dell'area di disegno rimane non colorata dalla sfumatura. Ecco il programma che mostra tre configurazioni possibili:

[![Sfumatura conica](circular-gradients-images/ConicalGradient.png "Sfumatura conica")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

La schermata iOS a sinistra mostra l'effetto dell' `SKShaderTileMode` impostazione di `Clamp` . La sfumatura inizia con il rosso all'interno del bordo del cerchio più piccolo che si trova opposto al lato più vicino al secondo cerchio. Il `Clamp` valore fa anche in modo che il rosso continui fino al punto del cono. La sfumatura termina con blu al bordo esterno del cerchio più grande più vicino al primo cerchio, ma continua con blu all'interno del cerchio e oltre.

La schermata Android è simile, ma con `SKShaderTileMode` `Repeat` . A questo punto è più chiaro che la sfumatura inizia all'interno del primo cerchio e termina all'esterno del secondo cerchio. L' `Repeat` impostazione causa la ripetizione della sfumatura con il rosso all'interno del cerchio più grande.

La schermata UWP Mostra cosa accade quando il cerchio più piccolo viene spostato interamente all'interno del cerchio più grande. La sfumatura si interrompe come un cono e riempie invece l'intera area. L'effetto è simile alla sfumatura radiale, ma è asimmetrico se il cerchio più piccolo non è esattamente centrato all'interno del cerchio più grande.

Si potrebbe dubitare dell'utilità pratica della sfumatura quando un cerchio è annidato in un altro, ma è ideale per un'evidenziazione speculare.

## <a name="conical-gradients-for-specular-highlights"></a>Gradienti conici per le evidenziazioni speculari

In precedenza in questo articolo è stato illustrato come usare una sfumatura radiale per creare un'evidenziazione speculare. Per questo scopo, è anche possibile usare la sfumatura conica a due punti ed è possibile che l'aspetto sia simile al seguente:

[![Evidenziazione speculare conica](circular-gradients-images/ConicalSpecularHighlight.png "Evidenziazione speculare conica")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

L'aspetto asimmetrico suggerisce meglio la superficie arrotondata dell'oggetto. 

Il codice di disegno nella pagina **evidenziazione speculare conica** è lo stesso della pagina di **evidenziazione speculare radiale** ad eccezione dello shader:

```csharp
public class ConicalSpecularHighlightPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(
                                offCenter,
                                1,
                                center,
                                radius,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

I due cerchi hanno centri di `offCenter` e `center` . Il cerchio centrato su `center` è associato a un raggio che comprende l'intera palla, ma il cerchio centrato `offCenter` ha un raggio di un solo pixel. Il gradiente inizia in modo efficace da quel punto e termina in corrispondenza del bordo della palla.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
