---
title: Le sfumature circolare SkiaSharp
description: Informazioni sui diversi tipi di sfumature basate cerchi.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: d56cc499112a937cd1a22664adeedd54c4397341
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304177"
---
# <a name="the-skiasharp-circular-gradients"></a>Le sfumature circolare SkiaSharp

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La classe [`SKShader`](xref:SkiaSharp.SKShader) definisce i metodi statici per creare quattro tipi diversi di sfumature. L'articolo relativo alla [**sfumatura lineare SkiaSharp**](linear-gradient.md) illustra il metodo [`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) . Questo articolo illustra i tre tipi di sfumature, ognuno dei quali si basano su cerchi.

Il metodo [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*) crea una sfumatura che emana dal centro di un cerchio:

![Esempio di sfumatura radiale](circular-gradients-images/RadialGradientSample.png)

Il metodo [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*) crea una sfumatura che si estende attorno al centro di un cerchio:

![Esempio di sfumatura durante lo sweep](circular-gradients-images/SweepGradientSample.png)

Il terzo tipo di sfumatura è piuttosto insolito. Viene chiamato sfumatura conica a due punti e viene definito dal metodo [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) . La sfumatura si estende da un cerchio a altro:

![Esempio di sfumatura piramidale](circular-gradients-images/ConicalGradientSample.png)

Se i due cerchi sono di dimensioni diverse, la sfumatura assume la forma di cono.

Questo articolo esamina queste sfumature in modo più dettagliato.

## <a name="the-radial-gradient"></a>Sfumatura radiale

Il metodo [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) presenta la sintassi seguente:

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Un overload di [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) include anche un parametro della matrice di trasformazione.

I primi due argomenti specificano il centro del cerchio e un raggio. La sfumatura inizia in corrispondenza di tale centro ed estende verso l'esterno per `radius` pixel. Ciò che accade oltre `radius` dipende dall'argomento [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) . Il `colors` parametro è una matrice di due o più colori (analogamente ai metodi di sfumatura lineare) e `colorPos` è una matrice di numeri interi nell'intervallo compreso tra 0 e 1. Questi numeri interi indicano le posizioni relative dei colori lungo il `radius` riga. È possibile impostare questo argomento su `null` per spaziare equamente i colori.

Se si usa `CreateRadialGradient` per riempire un cerchio, è possibile impostare il centro della sfumatura sul centro del cerchio e il raggio della sfumatura sul raggio del cerchio. In tal caso, l'argomento `SKShaderTileMode` non ha alcun effetto sul rendering della sfumatura. Tuttavia, se l'area riempita dalla sfumatura è più grande del cerchio definito dalla sfumatura, l'argomento `SKShaderTileMode` ha un effetto profondo su ciò che accade all'esterno del cerchio.

L'effetto di `SKShaderMode` viene illustrato nella pagina di **sfumatura radiale** nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . Il file XAML per questa pagina Crea un'istanza di un `Picker` che consente di selezionare uno dei tre membri dell'enumerazione `SKShaderTileMode`:

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

Il file code-behind colori l'intera area di disegno con una sfumatura radiale. Il centro della sfumatura è impostato al centro dell'area di disegno, e il raggio è impostato su 100 pixel. La sfumatura è costituita da due colori, bianchi e nero:

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

Questo codice crea una sfumatura con nero al centro, gradualmente a dissolvenza bianco 100 pixel dal centro. Ciò che accade oltre tale raggio dipende dall'argomento `SKShaderTileMode`:

[![Sfumatura radiale](circular-gradients-images/RadialGradient.png "Sfumatura radiale")](circular-gradients-images/RadialGradient-Large.png#lightbox)

In tutti i tre casi, la sfumatura riempie l'area di disegno. Nella schermata iOS a sinistra, la sfumatura di oltre il raggio continua con l'ultimo colore, che è il bianco. Questo è il risultato della `SKShaderTileMode.Clamp`. La schermata Android Mostra l'effetto di `SKShaderTileMode.Repeat`: a 100 pixel dal centro, la sfumatura inizia di nuovo con il primo colore, che è nero. La sfumatura viene ripetuta ogni 100 pixel, del raggio. 

La schermata piattaforma UWP (Universal Windows Platform) a destra Mostra come `SKShaderTileMode.Mirror` le sfumature alternano le direzioni. La sfumatura di primo è dal nero al centro su bianco in un raggio pari a 100 pixel. Successivo è il bianco dal raggio 100 pixel su nero a un raggio di 200 pixel e sfumatura successiva viene invertita nuovamente.

È possibile usare più di due colori in una sfumatura radiale. L'esempio di **gradiente Rainbow Arc** crea una matrice di otto colori corrispondenti ai colori dell'arcobaleno e termina con il rosso, oltre a una matrice di otto valori di posizione:

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

Si supponga che il valore minimo della larghezza e dell'altezza dell'area di disegno sia 1000, il che significa che il valore `rainbowWidth` è 250. I valori `outerRadius` e `innerRadius` sono rispettivamente impostati su 1000 e 750. Questi valori vengono usati per calcolare la matrice di `positions`; gli otto valori sono compresi tra 0,75 f e 1. Il valore `radius` viene usato per il segno di cerchio. Il valore di 875 indica che si estende lo spessore del tratto 250 pixel tra il raggio di 750 pixel e il raggio pari a 1000 pixel:

[![Sfumatura arco arcobaleno](circular-gradients-images/RainbowArcGradient.png "Sfumatura arco arcobaleno")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Se l'intera area di disegno è stato compilato con questa sfumatura, si vedrebbe che è rossa all'interno del raggio interno. Questo è dovuto al fatto che la matrice `positions` non inizia con 0. Il primo colore viene utilizzato per gli offset pari a 0 tramite il primo valore della matrice. La sfumatura è anche rossa oltre il raggio esterno. Questo è il risultato della modalità riquadro `Clamp`. Poiché la sfumatura viene usata per applicare traccia una linea spessa, queste aree rosse non sono visibili.

## <a name="radial-gradients-for-masking"></a>Sfumature radiali per il mascheramento

Ad esempio sfumature lineari, sfumature radiali possono incorporare i colori trasparenti o parzialmente trasparenti. Questa funzionalità è utile per un processo denominato _mascheramento_, che nasconde parte di un'immagine per accentuare un'altra parte dell'immagine.

La pagina **maschera di sfumatura radiale** Mostra un esempio. Il programma carica un'immagine bitmap di risorsa. I campi `CENTER` e `RADIUS` sono stati determinati da un esame della bitmap e fanno riferimento a un'area da evidenziare. Il gestore `PaintSurface` inizia con il calcolo di un rettangolo per visualizzare la bitmap e quindi la Visualizza in tale rettangolo:

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

Dopo aver disegnato la bitmap, il codice semplice converte `CENTER` e `RADIUS` `center` e `radius`, che fanno riferimento all'area evidenziata nella bitmap che è stata ridimensionata e spostata per la visualizzazione. Questi valori vengono usati per creare una sfumatura radiale con il centro e radius. I due colori partono trasparente al centro e per il primo 60% del raggio. La sfumatura si dissolve verso il bianco:

[![Maschera sfumatura radiale](circular-gradients-images/RadialGradientMask.png "Maschera sfumatura radiale")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Questo approccio non è il modo migliore per mascherare una bitmap. Il problema è che la maschera è principalmente un colore bianco, che è stato scelto in modo che corrispondano lo sfondo dell'area di disegno. Se lo sfondo è un altro colore &mdash; o forse una sfumatura &mdash; non corrisponderà. Un approccio migliore alla maschera è illustrato nell'articolo modalità di [Blend di SkiaSharp Porter-Duff](../blend-modes/porter-duff.md).

## <a name="radial-gradients-for-specular-highlights"></a>Sfumature radiali per evidenziazioni speculari

Quando una luce colpisce un'area di angoli arrotondata, riflette la luce in molte direzioni, ma alcune della luce bounces direttamente nell'osservatore. Questa operazione spesso crea l'aspetto di un'area bianca fuzzy sulla superficie denominata _evidenziazione speculare_.

Nella grafica tridimensionale evidenziazioni speculari spesso causano dagli algoritmi utilizzati per determinare i percorsi di luce e sfondo. Nella grafica bidimensionale evidenziazioni speculari vengono aggiunti a volte per suggerire l'aspetto di una superficie 3D. Un'evidenziazione con riflessione speculare può trasformare un cerchio rosso flat in una sfera rossa arrotondamento.

La pagina dell' **evidenziazione speculare radiale** usa una sfumatura radiale per eseguire esattamente questa operazione. Il `PaintSurface` gestore di esse calcolando un raggio per il cerchio e due valori `SKPoint` &mdash; un `center` e un `offCenter` a metà tra il centro e il bordo superiore sinistro del cerchio:

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

La chiamata di `CreateRadialGradient` crea una sfumatura che inizia in corrispondenza di tale punto di `offCenter` con il bianco e termina con il rosso a una distanza dalla metà del raggio. Di seguito è riportata un'immagine di tale finestra:

[![Evidenziazione speculare radiale](circular-gradients-images/RadialSpecularHighlight.png "Evidenziazione speculare radiale")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Se si esamina con attenzione questa sfumatura, si potrebbe decidere che presenta dei difetti. La sfumatura è centrata intorno a un punto specifico, e può essere necessario fosse un po' meno simmetrico in modo da riflettere la superficie arrotondata. In tal caso, è possibile preferire l'evidenziazione speculare riportata di seguito nella sezione [**gradienti conici per le evidenziazioni speculari**](#conical-gradients-for-specular-highlights).

## <a name="the-sweep-gradient"></a>La sfumatura durante lo sweep

Il metodo [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) presenta la sintassi più semplice di tutti i metodi di creazione di sfumatura:

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

È semplicemente un centro di una matrice di colori e le posizioni di colore. La sfumatura inizia a destra del punto centrale e sweep di 360 gradi in senso orario intorno al centro. Si noti che non esiste alcun parametro `SKShaderTileMode`.

È disponibile anche un overload di [`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix)) con un parametro di trasformazione matrice. È possibile applicare una trasformazione di rotazione per la sfumatura per modificare il punto di partenza. È inoltre possibile applicare una trasformazione di ridimensionamento per modificare la direzione da in senso orario in senso antiorario.

La pagina **sfumatura Sweep** usa una sfumatura di sweep per colorare un cerchio con una larghezza del tratto di 50 pixel:

[![Sfumatura Sweep](circular-gradients-images/SweepGradient.png "Sfumatura Sweep")](circular-gradients-images/SweepGradient-Large.png#lightbox)

La classe `SweepGradientPage` definisce una matrice di otto colori con valori di tonalità diversi. Si noti che la matrice inizia e finisce con red (un valore di tonalità pari a 0 o a 360), che viene visualizzato all'estrema destra nelle schermate:

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

Il programma implementa anche un `TapGestureRecognizer` che Abilita il codice alla fine del gestore di `PaintSurface`. Questo codice Usa la stessa sfumatura per riempire l'area di disegno:

[![Sfumatura completa Sweep](circular-gradients-images/SweepGradientFull.png "Sfumatura completa Sweep")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Queste schermate illustrano che i riempimenti sfumati qualsiasi area viene colorata da esso. Se la sfumatura non iniziare e terminare con lo stesso colore, esisterà una discontinuità a destra del punto centrale.

## <a name="the-two-point-conical-gradient"></a>Due punti piramidale sfumatura

Il metodo [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) presenta la sintassi seguente:

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

I parametri iniziano con i punti centrali e i raggi per due cerchi, detti cerchio _iniziale_ e cerchio _finale_ . I tre parametri rimanenti sono uguali a quelli per `CreateLinearGradient` e `CreateRadialGradient`. Un overload [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) include una trasformazione matrice.

La sfumatura inizia in corrispondenza del cerchio di inizio e termina in corrispondenza del cerchio di fine. Il parametro `SKShaderTileMode` determina ciò che accade oltre i due cerchi. La sfumatura piramidale due punti è unico sfumatura non interamente è riempire un'area. Se i due cerchi stesso raggio, la sfumatura è limitata a un rettangolo con una larghezza che equivale a diametro dei cerchi. Se i due cerchi hanno raggi differenti, la sfumatura della forma un cono.

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

Il file code-behind definisce i due oggetti `TouchPoint` con un raggio fisso di 50 e 100:

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

Il `colors` matrice è rosso, verde e blu. Il codice verso la parte inferiore del gestore `PaintSurface` disegna i due punti di tocco come cerchi neri, in modo che non ostacolino la sfumatura.

Si noti che `DrawRect` chiamata utilizza la sfumatura per colorare l'intera area di disegno. In generale, tuttavia, gran parte dell'area di disegno rimane non colorata per la sfumatura. Ecco il programma che mostra tre opzioni di configurazione:

[![Sfumatura conica](circular-gradients-images/ConicalGradient.png "Sfumatura conica")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

La schermata iOS a sinistra mostra l'effetto dell'impostazione `SKShaderTileMode` di `Clamp`. La sfumatura inizia con red all'interno del bordo del cerchio più piccolo opposta rispetto al lato più vicino al controllo circle secondo. Il valore di `Clamp` fa anche in modo che il rosso continui fino al punto del cono. La sfumatura termina con blu del bordo esterno del cerchio più grande più vicina al controllo circle prima, ma continua con blu all'interno di tale cerchio e altro ancora.

La schermata Android è simile, ma con un `SKShaderTileMode` di `Repeat`. A questo punto è chiaro che la sfumatura inizia all'interno del cerchio prima e termina all'esterno del cerchio secondo. L'impostazione `Repeat` causa la ripetizione della sfumatura con il colore rosso all'interno del cerchio più grande.

La schermata UWP illustra cosa accade quando viene spostato il più piccolo cerchio interamente all'interno del cerchio più grande. La sfumatura non è più un cono e invece riempie l'intera area. L'effetto è simile a sfumatura radiale, ma è asimmetrico se il cerchio più piccolo non esattamente viene centrato all'interno del cerchio più grande.

Si potrebbe dubitare di utilità pratica della sfumatura quando un cerchio è annidato in un altro, ma è ideale per un'evidenziazione con riflessione speculare.

## <a name="conical-gradients-for-specular-highlights"></a>Sfumature coniche per evidenziazioni speculari

In precedenza in questo articolo è stato illustrato come usare una sfumatura radiale per creare un'evidenziazione con riflessione speculare. È anche possibile usare la sfumatura piramidale due punti a questo scopo e si potrebbe preferire l'aspetto:

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

I due cerchi hanno centri di `offCenter` e `center`. Il cerchio centrato alla `center` è associato a un raggio che comprende l'intera palla, ma il cerchio centrato alla `offCenter` ha un raggio di un solo pixel. La sfumatura in modo efficace a questo punto inizia e termina in corrispondenza del bordo della palla.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
