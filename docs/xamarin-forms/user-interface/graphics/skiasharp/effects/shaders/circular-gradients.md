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
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199009"
---
# <a name="the-skiasharp-circular-gradients"></a>Le sfumature circolare SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Il [ `SKShader` ](xref:SkiaSharp.SKShader) classe definisce i metodi statici per creare quattro tipi diversi di sfumature. Il [ **sfumatura lineare SkiaSharp** ](linear-gradient.md) articolo vengono illustrate le [ `CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*) (metodo). Questo articolo illustra i tre tipi di sfumature, ognuno dei quali si basano su cerchi.

Il [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient*) metodo crea una sfumatura che si diffonde dal centro del cerchio:

![Esempio di sfumatura radiale](circular-gradients-images/RadialGradientSample.png)

Il [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient*) metodo crea una sfumatura che esegue lo sweep intorno al centro del cerchio:

![Esempio di sfumatura durante lo sweep](circular-gradients-images/SweepGradientSample.png)

Il terzo tipo di sfumatura è piuttosto insolito. Viene chiamata la sfumatura piramidale due punti e dipende dal [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) (metodo). La sfumatura si estende da un cerchio a altro:

![Esempio di sfumatura piramidale](circular-gradients-images/ConicalGradientSample.png)

Se i due cerchi sono di dimensioni diverse, la sfumatura assume la forma di cono.

Questo articolo esamina queste sfumature in modo più dettagliato.

## <a name="the-radial-gradient"></a>Sfumatura radiale

Il [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) metodo ha la sintassi seguente:

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

Oggetto [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) overload include anche un parametro di matrice di trasformazione.

I primi due argomenti specificano il centro del cerchio e un raggio. La sfumatura inizia in corrispondenza di tale area e si estende verso l'esterno per `radius` pixel. Cosa accade di là `radius` dipende il [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) argomento. Il `colors` parametro è una matrice di due o più colori (analogamente lineari metodi sfumatura), e `colorPos` è una matrice di numeri interi nell'intervallo compreso tra 0 e 1. Questi numeri interi indicano le posizioni relative dei colori lungo che `radius` riga. È possibile impostare tale argomento `null` ugualmente spazi i colori.

Se si usa `CreateRadialGradient` per riempire un cerchio, è possibile impostare il centro della sfumatura per il centro del cerchio e il raggio della sfumatura e il raggio del cerchio. In tal caso, il `SKShaderTileMode` argomento non ha alcun effetto sul rendering della sfumatura. Se l'area riempita con sfumatura è maggiore rispetto a quello definito per la sfumatura, tuttavia il `SKShaderTileMode` argomento ha ripercussioni su cosa accade di fuori del cerchio.

L'effetto della `SKShaderMode` è illustrato nel **sfumatura radiale** nella pagina il [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) esempio. Il file XAML della pagina Crea un `Picker` che consente di selezionare uno dei tre membri del `SKShaderTileMode` enumerazione:

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

Questo codice crea una sfumatura con nero al centro, gradualmente a dissolvenza bianco 100 pixel dal centro. Cosa accade di là del raggio varia a seconda di `SKShaderTileMode` argomento:

[![Sfumatura radiale](circular-gradients-images/RadialGradient.png "sfumatura radiale")](circular-gradients-images/RadialGradient-Large.png#lightbox)

In tutti i tre casi, la sfumatura riempie l'area di disegno. Nella schermata iOS a sinistra, la sfumatura di oltre il raggio continua con l'ultimo colore, che è il bianco. Che rappresenta il risultato di `SKShaderTileMode.Clamp`. La schermata Android Mostra l'effetto di `SKShaderTileMode.Repeat`: A 100 pixel dal centro, la sfumatura inizia di nuovo con il primo colore, che è il nero. La sfumatura viene ripetuta ogni 100 pixel, del raggio. 

La schermata (Universal Windows Platform) a destra come `SKShaderTileMode.Mirror` fa sì che le istruzioni alternative per le sfumature. La sfumatura di primo è dal nero al centro su bianco in un raggio pari a 100 pixel. Successivo è il bianco dal raggio 100 pixel su nero a un raggio di 200 pixel e sfumatura successiva viene invertita nuovamente.

È possibile usare più di due colori in una sfumatura radiale. Il **arcobaleno arco sfumatura** esempio crea una matrice di otto colori corrispondente per i colori del arcobaleno e finendo con red, nonché una matrice di otto valori di posizione:

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

Si supponga che il valore minimo della larghezza e altezza dell'area di disegno è 1000, vale a dire che il `rainbowWidth` valore è 250. Il `outerRadius` e `innerRadius` valori vengono impostati su 1000 e 750, rispettivamente. Questi valori vengono usati per calcolare il `positions` matrice; l'intervallo di otto valori da 0.75f su 1. Il `radius` valore viene utilizzato per che traccia il cerchio. Il valore di 875 indica che si estende lo spessore del tratto 250 pixel tra il raggio di 750 pixel e il raggio pari a 1000 pixel:

[![Sfumatura arco arcobaleno](circular-gradients-images/RainbowArcGradient.png "arcobaleno arco sfumatura")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

Se l'intera area di disegno è stato compilato con questa sfumatura, si vedrebbe che è rossa all'interno del raggio interno. Infatti il `positions` matrice non inizia con 0. Il primo colore viene utilizzato per gli offset pari a 0 tramite il primo valore della matrice. La sfumatura è anche rossa oltre il raggio esterno. Che rappresenta il risultato del `Clamp` modalità del riquadro. Poiché la sfumatura viene usata per applicare traccia una linea spessa, queste aree rosse non sono visibili.

## <a name="radial-gradients-for-masking"></a>Sfumature radiali per il mascheramento

Ad esempio sfumature lineari, sfumature radiali possono incorporare i colori trasparenti o parzialmente trasparenti. Questa funzionalità è utile per un processo denominato _maschera_, che nasconde parte di un'immagine per sottolineare un'altra parte dell'immagine.

Il **maschera della sfumatura radiale** pagina viene illustrato un esempio. Il programma carica un'immagine bitmap di risorsa. Il `CENTER` e `RADIUS` campi erano quindi determinati da un esame della bitmap e fare riferimento a un'area in cui dovrebbe essere evidenziata. Il `PaintSurface` gestore inizia calcolando un rettangolo per visualizzare la bitmap e quindi lo visualizza in quel rettangolo:

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

Dopo la bitmap di disegno, si converte codice semplice `CENTER` e `RADIUS` al `center` e `radius`, che fanno riferimento alle area evidenziata nella mappa di bit che è stato ridimensionato e spostato per la visualizzazione. Questi valori vengono usati per creare una sfumatura radiale con il centro e radius. I due colori partono trasparente al centro e per il primo 60% del raggio. La sfumatura si dissolve verso il bianco:

[![Maschera della sfumatura radiale](circular-gradients-images/RadialGradientMask.png "maschera della sfumatura radiale")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

Questo approccio non è il modo migliore per mascherare una bitmap. Il problema è che la maschera è principalmente un colore bianco, che è stato scelto in modo che corrispondano lo sfondo dell'area di disegno. Se lo sfondo viene alcuni altri colori &mdash; o forse una sfumatura stesso &mdash; non corrisponderà. Nell'articolo viene illustrato un approccio migliore per la maschera [SkiaSharp Porter-Duff blend modalità](../blend-modes/porter-duff.md).

## <a name="radial-gradients-for-specular-highlights"></a>Sfumature radiali per evidenziazioni speculari

Quando una luce colpisce un'area di angoli arrotondata, riflette la luce in molte direzioni, ma alcune della luce bounces direttamente nell'osservatore. Ciò crea spesso l'aspetto di un'area bianca fuzzy sull'area del chiamato un _evidenziazione con riflessione speculare_.

Nella grafica tridimensionale evidenziazioni speculari spesso causano dagli algoritmi utilizzati per determinare i percorsi di luce e sfondo. Nella grafica bidimensionale evidenziazioni speculari vengono aggiunti a volte per suggerire l'aspetto di una superficie 3D. Un'evidenziazione con riflessione speculare può trasformare un cerchio rosso flat in una sfera rossa arrotondamento.

Il **radiale dell'evidenziazione con riflessione speculare** pagina utilizza una sfumatura radiale per ottenere esattamente questo risultato. Il `PaintSurface` esseri gestore calcolando il raggio del cerchio e due `SKPoint` valori &mdash; una `center` e un `offCenter` che si trova a metà tra il centro e il bordo superiore sinistro del cerchio:

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

Il `CreateRadialGradient` chiamata crea una sfumatura che inizia che `offCenter` punto con bianco e termina con red a una distanza di metà il raggio. e ha l'aspetto seguente:

[![Evidenziazione con riflessione speculare radiale](circular-gradients-images/RadialSpecularHighlight.png "radiale evidenziazione con riflessione speculare")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

Se si esamina con attenzione questa sfumatura, si potrebbe decidere che presenta dei difetti. La sfumatura è centrata intorno a un punto specifico, e può essere necessario fosse un po' meno simmetrico in modo da riflettere la superficie arrotondata. In tal caso, si potrebbe preferire l'evidenziazione con riflessione speculare illustrato di seguito nella sezione [ **piramidale sfumature per evidenziazioni speculari**](#conical-gradients-for-specular-highlights).

## <a name="the-sweep-gradient"></a>La sfumatura durante lo sweep

Il [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[])) metodo presenta la sintassi più semplice di tutti i metodi per la creazione di sfumatura:

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

È semplicemente un centro di una matrice di colori e le posizioni di colore. La sfumatura inizia a destra del punto centrale e sweep di 360 gradi in senso orario intorno al centro. Si noti che non vi è alcun `SKShaderTileMode` parametro.

Oggetto [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix)) overload con un parametro di trasformazione di matrice è anche disponibile. È possibile applicare una trasformazione di rotazione per la sfumatura per modificare il punto di partenza. È inoltre possibile applicare una trasformazione di ridimensionamento per modificare la direzione da in senso orario in senso antiorario.

Il **sfumatura durante lo Sweep** pagina utilizza una sfumatura durante lo sweep di un cerchio con uno spessore pari a 50 pixel a colori:

[![Durante lo sweep sfumatura](circular-gradients-images/SweepGradient.png "durante lo Sweep sfumatura")](circular-gradients-images/SweepGradient-Large.png#lightbox)

Il `SweepGradientPage` classe definisce una matrice di otto colori con valori di tonalità diversi. Si noti che la matrice inizia e finisce con red (un valore di tonalità pari a 0 o a 360), che viene visualizzato all'estrema destra nelle schermate:

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

Il programma implementa anche un `TapGestureRecognizer` che consente di codice alla fine del `PaintSurface` gestore. Questo codice Usa la stessa sfumatura per riempire l'area di disegno:

[![Durante lo sweep Full sfumatura](circular-gradients-images/SweepGradientFull.png "durante lo Sweep Full sfumatura")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

Queste schermate illustrano che i riempimenti sfumati qualsiasi area viene colorata da esso. Se la sfumatura non iniziare e terminare con lo stesso colore, esisterà una discontinuità a destra del punto centrale.

## <a name="the-two-point-conical-gradient"></a>Due punti piramidale sfumatura

Il [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) metodo ha la sintassi seguente:

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

I parametri iniziano con punti centrali e raggi per due cerchi, detti il _avviare_ cerchio e _end_ cerchio. I tre parametri rimanenti sono uguali a quelle per `CreateLinearGradient` e `CreateRadialGradient`. Oggetto [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) overload include una trasformazione di matrice.

La sfumatura inizia in corrispondenza del cerchio di inizio e termina in corrispondenza del cerchio di fine. Il `SKShaderTileMode` parametro determina ciò che accade oltre i due cerchi. La sfumatura piramidale due punti è unico sfumatura non interamente è riempire un'area. Se i due cerchi stesso raggio, la sfumatura è limitata a un rettangolo con una larghezza che equivale a diametro dei cerchi. Se i due cerchi hanno raggi differenti, la sfumatura della forma un cono.

È probabile che si voglia provare a usare la sfumatura piramidale due punti, in modo che il **piramidale sfumatura** pagina deriva da `InteractivePage` per consentire due punti di tocco venga spostata per i due cerchi raggi:

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

Il file code-behind definisce due `TouchPoint` oggetti con un raggio predefinito di 50 e 100:

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

Il `colors` array è rosso, verde e blu. Il codice nella parte inferiore del `PaintSurface` gestore consente di disegnare i punti di due tocco come nero cerchi in modo che essi non impediscono la sfumatura.

Si noti che `DrawRect` chiamata Usa la sfumatura per colorare l'intera area di disegno. In generale, tuttavia, gran parte dell'area di disegno rimane non colorata per la sfumatura. Ecco il programma che mostra tre opzioni di configurazione:

[![Sfumatura piramidale](circular-gradients-images/ConicalGradient.png "piramidale sfumatura")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

La schermata iOS a sinistra mostra l'effetto del `SKShaderTileMode` impostazione di `Clamp`. La sfumatura inizia con red all'interno del bordo del cerchio più piccolo opposta rispetto al lato più vicino al controllo circle secondo. Il `Clamp` da rosso a continuare fino al punto di cono anche causa del valore. La sfumatura termina con blu del bordo esterno del cerchio più grande più vicina al controllo circle prima, ma continua con blu all'interno di tale cerchio e altro ancora.

La schermata Android è simile, ma con un `SKShaderTileMode` di `Repeat`. A questo punto è chiaro che la sfumatura inizia all'interno del cerchio prima e termina all'esterno del cerchio secondo. Il `Repeat` impostazione fa in modo che la sfumatura ripetere di nuovo con red all'interno del cerchio più grande.

La schermata UWP illustra cosa accade quando viene spostato il più piccolo cerchio interamente all'interno del cerchio più grande. La sfumatura non è più un cono e invece riempie l'intera area. L'effetto è simile a sfumatura radiale, ma è asimmetrico se il cerchio più piccolo non esattamente viene centrato all'interno del cerchio più grande.

Si potrebbe dubitare di utilità pratica della sfumatura quando un cerchio è annidato in un altro, ma è ideale per un'evidenziazione con riflessione speculare.

## <a name="conical-gradients-for-specular-highlights"></a>Sfumature coniche per evidenziazioni speculari

In precedenza in questo articolo è stato illustrato come usare una sfumatura radiale per creare un'evidenziazione con riflessione speculare. È anche possibile usare la sfumatura piramidale due punti a questo scopo e si potrebbe preferire l'aspetto:

[![Evidenziazione con riflessione speculare piramidale](circular-gradients-images/ConicalSpecularHighlight.png "piramidale evidenziazione con riflessione speculare")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

L'aspetto asimmetrico suggerisce meglio la superficie arrotondata dell'oggetto. 

Il codice di disegno nel **piramidale dell'evidenziazione con riflessione speculare** pagina è quello utilizzato per il **radiale dell'evidenziazione con riflessione speculare** pagina, ad eccezione dello shader:

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

Centri di avere due cerchi `offCenter` e `center`. Il cerchio centrati nella `center` è associato un raggio che comprende la palla intera, ma il cerchio centrato nella `offCenter` ha un raggio di un solo pixel. La sfumatura in modo efficace a questo punto inizia e termina in corrispondenza del bordo della palla.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
