---
title: Sfumatura lineare SkiaSharp
description: Informazioni su come tracciare le linee o aree di riempimento con sfumature è costituito da un graduale di due colori.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 290e533e54b2ee150b94d9fb6b0f5119324f9cf0
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305409"
---
# <a name="the-skiasharp-linear-gradient"></a>Sfumatura lineare SkiaSharp

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

La classe [`SKPaint`](xref:SkiaSharp.SKPaint) definisce una proprietà [`Color`](xref:SkiaSharp.SKPaint.Color) utilizzata per tracciare le linee o riempire le aree con un colore a tinta unita. In alternativa, è possibile tracciare linee o aree di riempimento con _sfumature_, ovvero miscele graduali di colori:

![Esempio di sfumatura lineare](linear-gradient-images/LinearGradientSample.png "Esempio di sfumatura lineare")

Il tipo di sfumatura più elementare è una sfumatura _lineare_ . La combinazione di colori si verifica su una linea (denominata _linea sfumata_) da un punto a un altro. Le righe che sono perpendicolare alla linea della sfumatura presentano lo stesso colore. Si crea una sfumatura lineare usando uno dei due metodi di [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) statici. La differenza tra i due overload è che una include una trasformazione di matrice e l'altro no. 

Questi metodi restituiscono un oggetto di tipo [`SKShader`](xref:SkiaSharp.SKShader) impostato sulla proprietà [`Shader`](xref:SkiaSharp.SKPaint.Shader) di `SKPaint`. Se la proprietà `Shader` è diversa da null, esegue l'override della proprietà `Color`. Qualsiasi riga tracciata o qualsiasi area riempita utilizzando questo `SKPaint` oggetto è basata sulla sfumatura anziché sul colore a tinta unita.

> [!NOTE]
> La proprietà `Shader` viene ignorata quando si include un oggetto `SKPaint` in una chiamata di `DrawBitmap`. È possibile usare la proprietà `Color` di `SKPaint` per impostare un livello di trasparenza per la visualizzazione di una bitmap, come descritto nell'articolo [visualizzazione di bitmap SkiaSharp](../../bitmaps/displaying.md#displaying-in-pixel-dimensions), ma non è possibile usare la proprietà `Shader` per visualizzare una bitmap con una trasparenza sfumatura. Altre tecniche sono disponibili per la visualizzazione di bitmap con lucidi sfumatura: queste sono descritte negli articoli [SkiaSharp le sfumature circolari](circular-gradients.md#radial-gradients-for-masking) e le [modalità di composizione SkiaSharp e Blend](../blend-modes/porter-duff.md#gradient-transparency-and-transitions).

## <a name="corner-to-corner-gradients"></a>Sfumature angolo a altro

Spesso una sfumatura lineare compreso tra un angolo di un rettangolo a un altro. Se il punto iniziale è l'angolo superiore sinistro del rettangolo, è possibile estendere la sfumatura:

- verticale per l'angolo inferiore sinistro
- in senso orizzontale all'angolo superiore destro
- in senso diagonale all'angolo in basso a destra

La sfumatura lineare diagonale viene illustrata nella prima pagina della sezione **SkiaSharp shaders and other Effects** dell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . La pagina **di sfumatura da angolo a angolo** crea una `SKCanvasView` nel relativo costruttore. Il gestore di `PaintSurface` crea un oggetto `SKPaint` in un'istruzione `using` e quindi definisce un rettangolo quadrato di 300 pixel centrato nell'area di disegno:

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    ···
    public CornerToCornerGradientPage ()
    {
        Title = "Corner-to-Corner Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ···
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create 300-pixel square centered rectangle
            float x = (info.Width - 300) / 2;
            float y = (info.Height - 300) / 2;
            SKRect rect = new SKRect(x, y, x + 300, y + 300);

            // Create linear gradient from upper-left to lower-right
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { SKColors.Red, SKColors.Blue },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Repeat);

            // Draw the gradient on the rectangle
            canvas.DrawRect(rect, paint);
            ···
        }
    }
}
```

Alla proprietà `Shader` di `SKPaint` viene assegnato il `SKShader` valore restituito dal metodo `SKShader.CreateLinearGradient` statico. I cinque argomenti sono i seguenti:

- Il punto iniziale della sfumatura, impostati per l'angolo superiore sinistro del rettangolo
- Il punto finale della sfumatura, impostati per l'angolo inferiore destro del rettangolo
- Una matrice di due o più colori che contribuiscono a sfumatura
- Matrice di valori di `float` che indica la posizione relativa dei colori all'interno della linea di sfumatura.
- Membro dell'enumerazione [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) che indica il comportamento della sfumatura oltre le estremità della linea della sfumatura

Una volta creato l'oggetto Gradient, il metodo `DrawRect` disegna il rettangolo quadrato di 300 pixel utilizzando l'oggetto `SKPaint` che include lo shader. Qui è in esecuzione in iOS, Android e Universal Windows Platform (UWP):

[![Sfumatura da angolo a angolo](linear-gradient-images/CornerToCornerGradient.png "Sfumatura da angolo a angolo")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

La linea della sfumatura è definita dai due punti specificati come i primi due argomenti. Si noti che questi punti sono relativi all' _area di disegno_ e _non_ all'oggetto grafico visualizzato con la sfumatura. Lungo la linea della sfumatura, il colore passa gradualmente da rosso nell'angolo superiore sinistro impostandolo sul blu nell'angolo inferiore destro. Qualsiasi linea perpendicolare alla linea sfumata è una costante di colore.

La matrice di valori `float` specificati come quarto argomento hanno una corrispondenza uno-a-uno con la matrice di colori. I valori indicano la posizione relativa lungo la linea della sfumatura in cui si verificano questi colori. In questo caso, 0 indica che `Red` si verifica all'inizio della linea di sfumatura e 1 indica che `Blue` si verifica alla fine della riga. I numeri devono essere crescente e devono essere compreso nell'intervallo da 0 a 1. Se non sono in tale intervallo, essi vengono regolate per essere compreso nell'intervallo.

I due valori nella matrice possono essere impostati su un valore diverso da 0 e 1. Provare a eseguire quanto segue:

```csharp
new float[] { 0.25f, 0.75f }
```

Ora completamente primo trimestre della linea della sfumatura è rosso puro e l'ultimo trimestre è blu pure. La combinazione di rosso e blu è limitata alla parte centrale della linea della sfumatura.

In generale, è opportuno lasciare spazi questi valori di posizione ugualmente da 0 a 1. In tal caso, è possibile specificare semplicemente `null` come quarto argomento `CreateLinearGradient`.

Anche se la sfumatura viene definita tra due gli angoli del rettangolo 300 pixel quadrato, non è limitato a riempire il rettangolo. La pagina **di sfumatura da angolo a angolo** include un codice aggiuntivo che risponde ai tocchi o ai clic del mouse nella pagina. Il campo `drawBackground` viene attivato o disattivato tra `true` e `false` a ogni tocco. Se il valore è `true`, il gestore `PaintSurface` usa lo stesso oggetto `SKPaint` per riempire l'intera area di disegno, quindi disegna un rettangolo nero che indica il rettangolo più piccolo: 

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    bool drawBackground;

    public CornerToCornerGradientPage ()
    {
        ···
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
        ···
        using (SKPaint paint = new SKPaint())
        {
            ···
            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                canvas.DrawRect(info.Rect, paint);

                // Outline the smaller rectangle
                paint.Shader = null;
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                canvas.DrawRect(rect, paint);
            }
        }
    }
}
```

Ecco cosa verrà visualizzato dopo aver toccato la schermata:

[![Sfumatura da angolo a angolo piena](linear-gradient-images/CornerToCornerGradientFull.png "Sfumatura da angolo a angolo piena")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

Si noti che la sfumatura viene ripetuto nel modello stesso oltre i punti che definiscono la linea della sfumatura. Questa ripetizione si verifica perché è `SKShaderTileMode.Repeat`l'ultimo argomento `CreateLinearGradient`. (Si vedrà le altre opzioni tra breve.)

Si noti inoltre che i punti che si usano per specificare la linea della sfumatura non sono univoci. Le righe che sono perpendicolare alla linea della sfumatura hanno lo stesso colore, in modo che siano presenti un numero infinito di righe di sfumature che è possibile specificare per lo stesso effetto. Ad esempio, durante la compilazione di un rettangolo con una sfumatura orizzontale, è possibile specificare gli angoli superiore sinistro e superiore destro, o gli angoli in basso a sinistra e basso a destra o due punti che sono anche con e paralleli su tali righe.

## <a name="interactively-experiment"></a>Sperimentare in modo interattivo

È possibile sperimentare in modo interattivo le sfumature lineari con la pagina di **sfumatura lineare interattiva** . Questa pagina usa la classe `InteractivePage` introdotta nell'articolo [**tre modi per creare un arco**](../../curves/arcs.md). `InteractivePage` gestisce gli eventi [`TouchEffect`](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) per mantenere una raccolta di oggetti `TouchPoint` che è possibile spostare con le dita o con il mouse.

Il file XAML connette il `TouchEffect` a un elemento padre della `SKCanvasView` e include anche una `Picker` che consente di selezionare uno dei tre membri dell'enumerazione [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) :

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.InteractiveLinearGradientPage"
                       Title="Interactive Linear Gradient">
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

Il costruttore nel file code-behind crea due oggetti `TouchPoint` per i punti iniziale e finale della sfumatura lineare. Il gestore di `PaintSurface` definisce una matrice di tre colori (per una sfumatura da rosso a verde a blu) e ottiene la `SKShaderTileMode` corrente dalla `Picker`:

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    public InteractiveLinearGradientPage ()
    {
        InitializeComponent ();

        touchPoints = new TouchPoint[2];

        for (int i = 0; i < 2; i++)
        { 
            touchPoints[i] = new TouchPoint
            {
                Center = new SKPoint(100 + i * 200, 100 + i * 200)
            };
        }

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
            paint.Shader = SKShader.CreateLinearGradient(touchPoints[0].Center,
                                                         touchPoints[1].Center,
                                                         colors,
                                                         null,
                                                         tileMode);
            canvas.DrawRect(info.Rect, paint);
        }
        ···
    }
}
```

Il gestore di `PaintSurface` crea l'oggetto `SKShader` da tutte le informazioni e lo usa per colorare l'intera area di disegno. La matrice di valori `float` è impostata su `null`. In caso contrario, per spazio ugualmente tre colori, impostare tale parametro in una matrice con i valori 0, 0,5 e 1.

La maggior parte del gestore `PaintSurface` è dedicata alla visualizzazione di diversi oggetti: i punti di tocco come cerchi del contorno, la linea della sfumatura e le linee perpendicolari alle linee della sfumatura nei punti di tocco:

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
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

            // Draw gradient line connecting touchpoints
            canvas.DrawLine(touchPoints[0].Center, touchPoints[1].Center, paint);

            // Draw lines perpendicular to the gradient line
            SKPoint vector = touchPoints[1].Center - touchPoints[0].Center;
            float length = (float)Math.Sqrt(Math.Pow(vector.X, 2) +
                                            Math.Pow(vector.Y, 2));
            vector.X /= length;
            vector.Y /= length;
            SKPoint rotate90 = new SKPoint(-vector.Y, vector.X);
            rotate90.X *= 200;
            rotate90.Y *= 200;

            canvas.DrawLine(touchPoints[0].Center, 
                            touchPoints[0].Center + rotate90, 
                            paint);

            canvas.DrawLine(touchPoints[0].Center,
                            touchPoints[0].Center - rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center + rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center - rotate90,
                            paint);
        }
    }
}
```

È facile disegnare la linea della sfumatura ci si connette lo due spezzano, ma le righe perpendicolare richiedono altre operazioni. La linea della sfumatura viene convertita in un vettore normalizzata per avere una lunghezza di un'unità e quindi ruotata di 90 gradi. Tale vettore quindi ha una lunghezza pari a 200 pixel. Viene utilizzato per disegnare quattro righe che si estendono dai punti di tocco per essere perpendicolare alla linea della sfumatura.

Le righe perpendicolare coincidano con l'inizio e alla fine della sfumatura. Ciò che accade oltre tali righe dipende dall'impostazione dell'enumerazione `SKShaderTileMode`:

[![Sfumatura lineare interattiva](linear-gradient-images/InteractiveLinearGradient.png "Sfumatura lineare interattiva")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

Le tre schermate mostrano i risultati dei tre diversi valori di [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode). La schermata iOS Mostra `SKShaderTileMode.Clamp`, che estende solo i colori sul bordo della sfumatura. L'opzione `SKShaderTileMode.Repeat` nello screenshot di Android Mostra come viene ripetuto il modello di sfumatura. L'opzione `SKShaderTileMode.Mirror` nello screenshot UWP ripete anche il modello, ma il modello viene invertito ogni volta, con conseguente assenza di discontinuità dei colori.

## <a name="gradients-on-gradients"></a>Sfumature in sfumature

La classe `SKShader` non definisce proprietà o metodi pubblici ad eccezione di `Dispose`. Gli oggetti `SKShader` creati con i metodi statici sono pertanto non modificabili. Anche se si usa la stessa sfumatura per due oggetti diversi, è probabile che si voglia variano leggermente la sfumatura. A tale scopo, è necessario creare un nuovo oggetto `SKShader`.

Nella pagina **Testo sfumato** vengono visualizzati il testo e un in background colorati con sfumature simili:

[![Testo sfumato](linear-gradient-images/GradientText.png "Testo sfumato")](linear-gradient-images/GradientText-Large.png#lightbox)

Le uniche differenze nelle sfumature sono i punti iniziale e finale. Sfumatura utilizzata per la visualizzazione di testo si basa su due punti sugli angoli del rettangolo di delimitazione per il testo. Per lo sfondo, i due punti sono basati sull'intera area di lavoro. Ecco il codice:

```csharp
public class GradientTextPage : ContentPage
{
    const string TEXT = "GRADIENT";

    public GradientTextPage ()
    {
        Title = "Gradient Text";

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
            // Create gradient for background
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                new SKPoint(info.Width, info.Height),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw background
            canvas.DrawRect(info.Rect, paint);

            // Set TextSize to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Shift textBounds by that amount
            textBounds.Offset(xText, yText);

            // Create gradient for text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(textBounds.Left, textBounds.Top),
                                new SKPoint(textBounds.Right, textBounds.Bottom),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

Il `Shader` proprietà dell'oggetto `SKPaint` è impostato per primo per visualizzare una sfumatura per coprire lo sfondo. I punti di sfumatura sono impostati per gli angoli superiore sinistro e inferiore destro dell'area di disegno.

Il codice imposta la proprietà `TextSize` dell'oggetto `SKPaint` in modo che il testo venga visualizzato al 90% della larghezza dell'area di disegno. I limiti di testo vengono utilizzati per calcolare `xText` e `yText` valori da passare al metodo `DrawText` per centrare il testo.

Tuttavia, i punti di sfumatura per la seconda chiamata di `CreateLinearGradient` devono fare riferimento all'angolo superiore sinistro e inferiore destro del testo relativo all'area di disegno quando viene visualizzato. Questa operazione viene eseguita spostando il rettangolo di `textBounds` con gli stessi valori di `xText` e `yText`:

```csharp
textBounds.Offset(xText, yText);
```

A questo punto gli angoli superiore sinistro e inferiore destro del rettangolo consente di impostare i punti iniziale e finale della sfumatura.

## <a name="animating-a-gradient"></a>Animazione di una sfumatura

Esistono diversi modi per animare una sfumatura. Un approccio consiste nell'aggiungere un'animazione di punti iniziale e finale. La pagina **animazione sfumatura** sposta i due punti intorno a un cerchio centrato sull'area di disegno. Il raggio del cerchio è la metà larghezza o dell'altezza dell'area di disegno, qualunque sia il minore. I punti di inizio e di fine sono opposti tra loro in questo cerchio e la sfumatura passa da bianco a nero con una `Mirror` modalità affiancata:

[![Animazione sfumatura](linear-gradient-images/GradientAnimation.png "Animazione sfumatura")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

Il costruttore crea la `SKCanvasView`. I metodi `OnAppearing` e `OnDisappearing` gestiscono la logica di animazione:

```csharp
public class GradientAnimationPage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    double angle;
    Stopwatch stopwatch = new Stopwatch();

    public GradientAnimationPage()
    {
        Title = "Gradient Animation";

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
        const int duration = 3000;
        angle = 2 * Math.PI * (stopwatch.ElapsedMilliseconds % duration) / duration;
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

Il metodo `OnTimerTick` calcola un valore `angle` che viene animato da 0 a 2 π ogni 3 secondi. 

Ecco un modo per calcolare i due punti di sfumatura. Un valore `SKPoint` denominato `vector` viene calcolato per estendersi dal centro dell'area di disegno a un punto sul raggio del cerchio. La direzione del vettore corrente si basa sui valori seno e il coseno dell'angolo. Vengono quindi calcolati i due punti di sfumatura opposti: un punto viene calcolato sottraendo tale vettore dal punto center e altri punti viene calcolato aggiungendo il vettore punto centrale:

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
            int radius = Math.Min(info.Width, info.Height) / 2;
            SKPoint vector = new SKPoint((float)(radius * Math.Cos(angle)),
                                         (float)(radius * Math.Sin(angle)));

            paint.Shader = SKShader.CreateLinearGradient(
                                center - vector,
                                center + vector,
                                new SKColor[] { SKColors.White, SKColors.Black },
                                null,
                                SKShaderTileMode.Mirror);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Un approccio leggermente diverso richiede meno codice. Questo approccio usa il metodo di overload [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) con una trasformazione matrice come ultimo argomento. Questo approccio è la versione nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                info.Width < info.Height ? new SKPoint(info.Width, 0) : 
                                                           new SKPoint(0, info.Height),
                                new SKColor[] { SKColors.White, SKColors.Black },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Mirror,
                                SKMatrix.MakeRotation((float)angle, info.Rect.MidX, info.Rect.MidY));

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Se la larghezza dell'area di disegno è minore dell'altezza, i due punti sfumatura vengono impostati su (0,0) e (`info.Width`, 0). La trasformazione di rotazione passata come ultimo argomento a `CreateLinearGradient` ruota effettivamente i due punti intorno al centro dello schermo.

Si noti che se l'angolo è 0, non vi è alcuna rotazione e i due punti di sfumatura sono gli angoli superiore sinistro e superiore destro dell'area di disegno. Questi punti non sono gli stessi punti di sfumatura calcolati come illustrato nella chiamata `CreateLinearGradient` precedente. Ma questi punti sono _paralleli_ alla linea sfumata orizzontale che interseca il centro dell'area di disegno e generano una sfumatura identica.

**Sfumatura arcobaleno**

La pagina di **sfumatura arcobaleno** disegna un arcobaleno dall'angolo superiore sinistro dell'area di disegno all'angolo inferiore destro. Ma questa sfumatura con arcobaleno non è, ad esempio un arcobaleno reale. È direttamente anziché curvi, ma si basa su otto colori HSL (hue-saturazione-luminosità) che dipendono da un ciclo tra i valori di tonalità compreso tra 0 e 360:

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

Il codice fa parte del gestore `PaintSurface` mostrato di seguito. Il gestore inizia con la creazione di un percorso che definisce un poligono sei lati che si estende dall'angolo superiore sinistro dell'area di disegno nell'angolo in basso a destra:

```csharp
public class RainbowGradientPage : ContentPage
{
    public RainbowGradientPage ()
    {
        Title = "Rainbow Gradient";

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

        using (SKPath path = new SKPath())
        {
            float rainbowWidth = Math.Min(info.Width, info.Height) / 2f;

            // Create path from upper-left to lower-right corner
            path.MoveTo(0, 0);
            path.LineTo(rainbowWidth / 2, 0);
            path.LineTo(info.Width, info.Height - rainbowWidth / 2);
            path.LineTo(info.Width, info.Height);
            path.LineTo(info.Width - rainbowWidth / 2, info.Height);
            path.LineTo(0, rainbowWidth / 2);
            path.Close();

            using (SKPaint paint = new SKPaint())
            {
                SKColor[] colors = new SKColor[8];

                for (int i = 0; i < colors.Length; i++)
                {
                    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
                }

                paint.Shader = SKShader.CreateLinearGradient(
                                    new SKPoint(0, rainbowWidth / 2), 
                                    new SKPoint(rainbowWidth / 2, 0),
                                    colors,
                                    null,
                                    SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

I due punti sfumatura nel metodo `CreateLinearGradient` sono basati su due punti che definiscono questo percorso: entrambi i punti sono vicini all'angolo superiore sinistro. Il primo è sul bordo superiore dell'area di disegno e il secondo è il bordo sinistro dell'area di disegno. Il risultato è il seguente:

[![Sfumatura arcobaleno difettosa](linear-gradient-images/RainbowGradientFaulty.png "Sfumatura arcobaleno difettosa")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

Si tratta di un'immagine di interessante, ma non è abbastanza lo scopo. Il problema è che quando si crea una sfumatura lineare, le righe di costante di colore sono perpendicolare alla linea della sfumatura. La linea della sfumatura si basa sui punti in cui la figura interessa i bordi superiore e sinistro e tale riga non è in genere perpendicolare ai bordi che si estendono all'angolo inferiore destro della figura. Questo approccio funzionerebbe solo se l'area di disegno sono stato quadrato.

Per creare una sfumatura con arcobaleno appropriate, la linea della sfumatura deve essere perpendicolare al bordo dell'arcobaleno. Ovvero un calcolo più complesso. Deve essere definito un vettore parallela sul lato lungo della figura. Il vettore è ruotata di 90 gradi in modo che risulti perpendicolare a tale lato. Viene quindi allungato in modo che sia la larghezza della figura moltiplicando per `rainbowWidth`. I due punti di sfumatura in un punto di fianco nella figura vengono calcolati in base e che puntano oltre il vettore. Ecco il codice visualizzato nella pagina di **sfumatura arcobaleno** nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

```csharp
public class RainbowGradientPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPath path = new SKPath())
        {
            ···
            using (SKPaint paint = new SKPaint())
            {
                ···
                // Vector on lower-left edge, from top to bottom 
                SKPoint edgeVector = new SKPoint(info.Width - rainbowWidth / 2, info.Height) - 
                                     new SKPoint(0, rainbowWidth / 2);

                // Rotate 90 degrees counter-clockwise:
                SKPoint gradientVector = new SKPoint(edgeVector.Y, -edgeVector.X);

                // Normalize
                float length = (float)Math.Sqrt(Math.Pow(gradientVector.X, 2) +
                                                Math.Pow(gradientVector.Y, 2));
                gradientVector.X /= length;
                gradientVector.Y /= length;

                // Make it the width of the rainbow
                gradientVector.X *= rainbowWidth;
                gradientVector.Y *= rainbowWidth;

                // Calculate the two points
                SKPoint point1 = new SKPoint(0, rainbowWidth / 2);
                SKPoint point2 = point1 + gradientVector;

                paint.Shader = SKShader.CreateLinearGradient(point1,
                                                             point2,
                                                             colors,
                                                             null,
                                                             SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

Ora i colori con arcobaleno sono allineati con la cifra:

[![Sfumatura arcobaleno](linear-gradient-images/RainbowGradient.png "Sfumatura arcobaleno")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**Colori Infinity**

Una sfumatura arcobaleno viene usata anche nella pagina **colori Infinity** . Questa pagina disegna un segno di infinito usando un oggetto Path descritto nell'articolo [**tre tipi di curve di Bézier**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs). L'immagine viene quindi colorato con una sfumatura animato arcobaleno che esegue lo sweep continuamente tra l'immagine.

Il costruttore crea l'oggetto `SKPath` che descrive il segno di infinito. Dopo aver creato il percorso, il costruttore inoltre possibile ottenere i limiti rettangolari del percorso. Viene quindi calcolato un valore denominato `gradientCycleLength`. Se una sfumatura è basata sugli angoli superiore sinistro e inferiore destro del rettangolo di `pathBounds`, questo valore `gradientCycleLength` è la larghezza orizzontale totale del modello di sfumatura:

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    SKCanvasView canvasView;

    // Path information 
    SKPath infinityPath;
    SKRect pathBounds;
    float gradientCycleLength;

    // Gradient information
    SKColor[] colors = new SKColor[8];
    ···

    public InfinityColorsPage ()
    {
        Title = "Infinity Colors";

        // Create path for infinity sign
        infinityPath = new SKPath();
        infinityPath.MoveTo(0, 0);                                  // Center
        infinityPath.CubicTo(  50,  -50,   95, -100,  150, -100);   // To top of right loop
        infinityPath.CubicTo( 205, -100,  250,  -55,  250,    0);   // To far right of right loop
        infinityPath.CubicTo( 250,   55,  205,  100,  150,  100);   // To bottom of right loop
        infinityPath.CubicTo(  95,  100,   50,   50,    0,    0);   // Back to center  
        infinityPath.CubicTo( -50,  -50,  -95, -100, -150, -100);   // To top of left loop
        infinityPath.CubicTo(-205, -100, -250,  -55, -250,    0);   // To far left of left loop
        infinityPath.CubicTo(-250,   55, -205,  100, -150,  100);   // To bottom of left loop
        infinityPath.CubicTo( -95,  100, - 50,   50,    0,    0);   // Back to center
        infinityPath.Close();

        // Calculate path information 
        pathBounds = infinityPath.Bounds;
        gradientCycleLength = pathBounds.Width +
            pathBounds.Height * pathBounds.Height / pathBounds.Width;

        // Create SKColor array for gradient
        for (int i = 0; i < colors.Length; i++)
        {
            colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
        }

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

Il costruttore crea anche la matrice di `colors` per l'arcobaleno e l'oggetto `SKCanvasView`.

Gli override dei metodi `OnAppearing` e `OnDisappearing` eseguono l'overhead per l'animazione. Il metodo `OnTimerTick` aggiunge un'animazione al campo `offset` da 0 a `gradientCycleLength` ogni due secondi:

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    float offset;
    Stopwatch stopwatch = new Stopwatch();
    ···

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
        const int duration = 2;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        offset = (float)(gradientCycleLength * progress);
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

Infine, il gestore di `PaintSurface` esegue il rendering del segno di infinito. Poiché il percorso contiene coordinate negative e positive che racchiudono un punto centrale di (0, 0), viene usata una trasformazione `Translate` nell'area di disegno per spostarla al centro. La trasformazione translate è seguita da una trasformazione `Scale` che applica un fattore di scala che rende il segno di infinito il più grande possibile rimanendo comunque entro il 95% della larghezza e dell'altezza dell'area di disegno. 

Si noti che la costante `STROKE_WIDTH` viene aggiunta alla larghezza e all'altezza del rettangolo di delimitazione del percorso. Il percorso verrà tracciato con una riga di questa larghezza, in modo che le dimensioni delle dimensioni del numero infinito di rendering viene incrementata a metà larghezza specificata su tutti e quattro i lati:

```csharp
public class InfinityColorsPage : ContentPage
{
    const int STROKE_WIDTH = 50;
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transforms to shift path to center and scale to canvas size
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.95f * 
            Math.Min(info.Width / (pathBounds.Width + STROKE_WIDTH),
                     info.Height / (pathBounds.Height + STROKE_WIDTH)));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = STROKE_WIDTH;
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(pathBounds.Left, pathBounds.Top),
                                new SKPoint(pathBounds.Right, pathBounds.Bottom),
                                colors,
                                null,
                                SKShaderTileMode.Repeat,
                                SKMatrix.MakeTranslation(offset, 0));

            canvas.DrawPath(infinityPath, paint);
        }
    }
}
```

Esaminare i punti passati come i primi due argomenti di `SKShader.CreateLinearGradient`. Tali punti sono basati sul percorso originale rettangolo di delimitazione. Il primo punto è (&ndash;250, &ndash;100) e il secondo è (250, 100). Interni di SkiaSharp, tali punti sono soggetti alla trasformazione di area di disegno corrente in modo da allinearli correttamente con il simbolo di infinito visualizzato.

Senza l'ultimo argomento da `CreateLinearGradient`, viene visualizzata una sfumatura arcobaleno che si estende dall'angolo superiore sinistro del segno di infinito in basso a destra. (In realtà, la sfumatura estende dall'angolo superiore sinistro all'angolo inferiore destro del rettangolo di delimitazione. Il segno di infinito sottoposto a rendering è maggiore del rettangolo delimitatore della metà del valore `STROKE_WIDTH` su tutti i lati. Poiché la sfumatura è rossa sia all'inizio che alla fine e la sfumatura viene creata con `SKShaderTileMode.Repeat`, la differenza non è evidente.

Con l'ultimo argomento da `CreateLinearGradient`, il modello di sfumatura viene continuamente spazzato nell'immagine:

[![Colori Infinity](linear-gradient-images/InfinityColors.png "Colori Infinity")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>La trasparenza e sfumature

I colori che contribuiscono a una sfumatura possono incorporare la trasparenza. Invece di una sfumatura che si dissolve da un colore a altro, la sfumatura può dissolvenza in entrata da un colore su trasparente. 

È possibile usare questa tecnica per ottenere alcuni effetti interessanti. Uno degli esempi classici illustra un oggetto grafico con la reflection:

[![Sfumatura Reflection](linear-gradient-images/ReflectionGradient.png "Sfumatura Reflection")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

Il testo che viene capovolto è visualizzato con una sfumatura che è trasparente in alto per completamente trasparente nella parte inferiore del 50%. Questi livelli di trasparenza sono associati i valori alfa di 0x80 e 0.

Il gestore `PaintSurface` nella pagina **sfumatura Reflection** ridimensiona le dimensioni del testo al 90% della larghezza dell'area di disegno. Calcola quindi `xText` e `yText` valori per posizionare il testo in modo che sia centrato orizzontalmente, ma che si trovi su una linea di base corrispondente al centro verticale della pagina:

```csharp
public class ReflectionGradientPage : ContentPage
{
    const string TEXT = "Reflection";

    public ReflectionGradientPage ()
    {
        Title = "Reflection Gradient";

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

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

I valori `xText` e `yText` sono gli stessi valori utilizzati per visualizzare il testo riflesso nella chiamata `DrawText` nella parte inferiore del gestore `PaintSurface`. Appena prima di tale codice, tuttavia, verrà visualizzata una chiamata al metodo `Scale` di `SKCanvas`. Questo `Scale` metodo si ridimensiona orizzontalmente di 1 (che non esegue alcuna operazione) ma verticalmente di &ndash;1, che capovolge in modo efficace tutti gli elementi. Il centro della rotazione è impostato sul punto (0, `yText`), in cui `yText` è il centro verticale dell'area di disegno, calcolato originariamente come `info.Height` diviso per 2.

Tenere presente che Skia Usa la sfumatura per colorare gli oggetti grafici prima le trasformazioni di area di disegno. Dopo aver disegnato il testo non riflesso, il rettangolo `textBounds` viene spostato in modo che corrisponda al testo visualizzato:

```csharp
textBounds.Offset(xText, yText);
```

La chiamata `CreateLinearGradient` definisce una sfumatura dalla parte superiore del rettangolo fino alla fine. La sfumatura è da un blu completamente trasparente (`paint.Color.WithAlpha(0)`) a un 50% trasparente blu (`paint.Color.WithAlpha(0x80)`). La trasformazione di canvas inverte il testo capovolto, in modo che inizia in corrispondenza della linea di base, il blu trasparente il 50% e diventa trasparente nella parte superiore del testo.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
