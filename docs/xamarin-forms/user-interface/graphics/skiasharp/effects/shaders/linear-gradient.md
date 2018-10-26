---
title: Sfumatura lineare SkiaSharp
description: Informazioni su come tracciare le linee o aree di riempimento con sfumature è costituito da un graduale di due colori.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: de563c4780a57e439abab61378919501ce88b11b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131406"
---
# <a name="the-skiasharp-linear-gradient"></a>Sfumatura lineare SkiaSharp

Il [ `SKPaint` ](xref:SkiaSharp.SKPaint) classe definisce un [ `Color` ](xref:SkiaSharp.SKPaint.Color) proprietà utilizzato per disegnare linee o aree di riempimento con un colore a tinta unita. In alternativa è possibile disegnare linee o riempire le aree _sfumature_, quali sono le sfumature graduale dei colori:

![Esempio della sfumatura lineare](linear-gradient-images/LinearGradientSample.png "esempio sfumatura lineare")

Il tipo di base della sfumatura è un _lineare_ sfumatura. Si verifica la combinazione di colori in una riga (denominato il _linea sfumata_) da un punto a altro. Le righe che sono perpendicolare alla linea della sfumatura presentano lo stesso colore. Si crea una sfumatura lineare utilizzando uno dei due statica [ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*) metodi. La differenza tra i due overload è che una include una trasformazione di matrice e l'altro no. 

Questi metodi restituiscono un oggetto di tipo [ `SKShader` ](xref:SkiaSharp.SKShader) impostato per il [ `Shader` ](xref:SkiaSharp.SKPaint.Shader) proprietà `SKPaint`. Se il `Shader` proprietà è diverso da null, viene eseguito l'override di `Color` proprietà. Tutte le righe che viene tracciata o qualsiasi area in cui viene riempito con questo `SKPaint` oggetto si basa sulla sfumatura anziché il colore a tinta unita.

> [!NOTE]
> Il `Shader` proprietà viene ignorata quando si include un' `SKPaint` dell'oggetto un `DrawBitmap` chiamare. È possibile usare la `Color` proprietà di `SKPaint` per impostare un livello di trasparenza per la visualizzazione di una mappa di bit (come descritto nell'articolo [bitmap visualizzazione SkiaSharp](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)), ma non è possibile usare il `Shader` proprietà per la visualizzazione una bitmap con una trasparenza della sfumatura. Altre tecniche disponibili per la visualizzazione di bitmap con sfumatura lucidi: questi elementi sono descritti negli articoli [sfumature circolare SkiaSharp](circular-gradients.md#radial-gradients-for-masking) e [modalità di composizione e blend SkiaSharp](../blend-modes/porter-duff.md#gradient-transparency-and-transitions).

## <a name="corner-to-corner-gradients"></a>Sfumature angolo a altro

Spesso una sfumatura lineare compreso tra un angolo di un rettangolo a un altro. Se il punto iniziale è l'angolo superiore sinistro del rettangolo, è possibile estendere la sfumatura:

- verticale per l'angolo inferiore sinistro
- in senso orizzontale all'angolo superiore destro
- in senso diagonale all'angolo in basso a destra

Sfumatura lineare diagonale è illustrata nella prima pagina nel **SkiaSharp shader e altri effetti** sezione il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio. Il **angolo a altro sfumatura** pagina Crea un `SKCanvasView` nel relativo costruttore. Il `PaintSurface` gestore crea un' `SKPaint` dell'oggetto un `using` istruzione e quindi definisce un rettangolo di 300 pixel quadrato centrato nell'area di disegno:

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

Il `Shader` proprietà di `SKPaint` viene assegnato il `SKShader` restituito da statico `SKShader.CreateLinearGradient` (metodo). I cinque argomenti sono i seguenti:

- Il punto iniziale della sfumatura, impostati per l'angolo superiore sinistro del rettangolo
- Il punto finale della sfumatura, impostati per l'angolo inferiore destro del rettangolo
- Una matrice di due o più colori che contribuiscono a sfumatura
- Matrice di `float` i valori che indicano la posizione relativa dei colori nella linea della sfumatura
- Un membro del [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) enumerazione che indica il comportamento della sfumatura oltre l'estremità della linea di sfumature

Dopo aver creato l'oggetto della sfumatura, il `DrawRect` metodo consente di disegnare il rettangolo di 300 pixel quadrato utilizzando il `SKPaint` oggetto che include lo shader. Qui è in esecuzione in iOS, Android e Universal Windows Platform (UWP):

[![Angolo a altro sfumatura](linear-gradient-images/CornerToCornerGradient.png "sfumatura angolo a altro")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

La linea della sfumatura è definita dai due punti specificati come i primi due argomenti. Si noti che tali punti sono relativi al _canvas_ e _non_ all'oggetto grafico visualizzato con sfumatura. Lungo la linea della sfumatura, il colore passa gradualmente da rosso nell'angolo superiore sinistro impostandolo sul blu nell'angolo inferiore destro. Qualsiasi linea perpendicolare alla linea sfumata è una costante di colore.

Matrice di `float` i valori specificati come quarto argomento hanno una corrispondenza uno a uno con la matrice di colori. I valori indicano la posizione relativa lungo la linea della sfumatura in cui si verificano questi colori. In questo caso, il valore 0 significa che `Red` si verifica all'inizio della linea di sfumature, mentre 1 indica che `Blue` si verifica alla fine della riga. I numeri devono essere crescente e devono essere compreso nell'intervallo da 0 a 1. Se non sono in tale intervallo, essi vengono regolate per essere compreso nell'intervallo.

I due valori nella matrice possono essere impostati su un valore diverso da 0 e 1. Provare questo codice:

```csharp
new float[] { 0.25f, 0.75f }
```

Ora completamente primo trimestre della linea della sfumatura è rosso puro e l'ultimo trimestre è blu pure. La combinazione di rosso e blu è limitata alla parte centrale della linea della sfumatura.

In generale, è opportuno lasciare spazi questi valori di posizione ugualmente da 0 a 1. In tal caso, è possibile fornire semplicemente `null` come argomento per il quarto `CreateLinearGradient`.

Anche se la sfumatura viene definita tra due gli angoli del rettangolo 300 pixel quadrato, non è limitato a riempire il rettangolo. Il **sfumatura angolo a altro** pagina include un codice aggiuntivo che risponde alle scelte o un clic del mouse sulla pagina. Il `drawBackground` campo è attivato o disattivato tra `true` e `false` con ogni tocco. Se il valore è `true`, il `PaintSurface` gestore Usa lo stesso `SKPaint` dell'oggetto fino a riempire l'intera area di disegno e disegna un rettangolo di colore nero che indica il rettangolo più piccolo: 

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

[![Full sfumatura angolo-a-Corner](linear-gradient-images/CornerToCornerGradientFull.png "Full sfumatura angolo a altro")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

Si noti che la sfumatura viene ripetuto nel modello stesso oltre i punti che definiscono la linea della sfumatura. La ripetizione si verifica perché l'ultimo argomento a `CreateLinearGradient` è `SKShaderTileMode.Repeat`. (Si vedrà le altre opzioni tra breve.)

Si noti inoltre che i punti che si usano per specificare la linea della sfumatura non sono univoci. Le righe che sono perpendicolare alla linea della sfumatura hanno lo stesso colore, in modo che siano presenti un numero infinito di righe di sfumature che è possibile specificare per lo stesso effetto. Ad esempio, durante la compilazione di un rettangolo con una sfumatura orizzontale, è possibile specificare gli angoli superiore sinistro e superiore destro, o gli angoli in basso a sinistra e basso a destra o due punti che sono anche con e paralleli su tali righe.

## <a name="interactively-experiment"></a>Sperimentare in modo interattivo

È possibile provare in modo interattivo con sfumature lineari con il **interattiva sfumatura lineare** pagina. Questa pagina Usa la `InteractivePage` classe introdotto nell'articolo [ **tre modi per disegnare un arco**](../../curves/arcs.md). `InteractivePage` handle [ `TouchEffect` ](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) eventi per gestire l'insieme di `TouchPoint` gli oggetti che è possibile spostare il puntatore del mouse o le dita.

Allega il file XAML la `TouchEffect` a un elemento padre del `SKCanvasView` e include inoltre una `Picker` che consente di selezionare uno dei tre membri della [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) enumerazione:

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

Il costruttore nel file code-behind vengono creati due `TouchPoint` gli oggetti per i punti iniziale e finale della sfumatura lineare. Il `PaintSurface` gestore definisce una matrice di tre colori (per una sfumatura da rosso a verde a blu) e ottiene l'oggetto corrente `SKShaderTileMode` dal `Picker`:

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

Il `PaintSurface` gestore crea il `SKShader` oggetto da tutte le informazioni e lo usa per l'intera area di disegno dei colori. Matrice di `float` valori è impostata su `null`. In caso contrario, per spazio ugualmente tre colori, impostare tale parametro in una matrice con i valori 0, 0,5 e 1.

La maggior parte del `PaintSurface` gestore di è dedicato alla visualizzazione di oggetti diversi: i punti di tocco come cerchi contorno, la linea della sfumatura e le righe perpendicolare alle righe di sfumatura in corrispondenza dei punti di tocco:

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

Le righe perpendicolare coincidano con l'inizio e alla fine della sfumatura. Cosa accade oltre tali linee dipende dall'impostazione del `SKShaderTileMode` enumerazione:

[![Sfumatura lineare interattivo](linear-gradient-images/InteractiveLinearGradient.png "interattiva sfumatura lineare")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

Le tre screenshot mostrano i risultati di tre valori diversi di [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode). La schermata mostra iOS `SKShaderTileMode.Clamp`, che estende semplicemente i colori del bordo della sfumatura. Il `SKShaderTileMode.Repeat` opzione nella schermata Android viene illustrato come viene ripetuto il motivo della sfumatura. Il `SKShaderTileMode.Mirror` opzione nello screenshot UWP ripete anche il modello, ma il modello viene invertito ogni volta, causando discontinuità Nessun colore.

## <a name="gradients-on-gradients"></a>Sfumature in sfumature

Il `SKShader` classe non definisce proprietà pubbliche o i metodi, ad eccezione di `Dispose`. Il `SKShader` gli oggetti che ha creato dai relativi metodi statici non sono quindi modificabili. Anche se si usa la stessa sfumatura per due oggetti diversi, è probabile che si voglia variano leggermente la sfumatura. A tale scopo, è necessario creare un nuovo `SKShader` oggetto.

Il **testo della sfumatura della** pagina Visualizza testo e un'in background che sono colorate con sfumature simile:

[![Testo della sfumatura](linear-gradient-images/GradientText.png "testo della sfumatura")](linear-gradient-images/GradientText-Large.png#lightbox)

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

Il `Shader` proprietà del `SKPaint` oggetto viene impostato prima di tutto per visualizzare una sfumatura per lo sfondo. I punti di sfumatura sono impostati per gli angoli superiore sinistro e inferiore destro dell'area di disegno.

Il codice imposta la `TextSize` proprietà del `SKPaint` in modo che il testo viene visualizzato al 90% della larghezza dell'area di disegno dell'oggetto. I limiti di testo vengono usati per calcolare `xText` e `yText` i valori da passare per il `DrawText` metodo per allineare al centro il testo.

Tuttavia, i punti di sfumatura per il secondo `CreateLinearGradient` chiamata deve fare riferimento nell'angolo in alto a sinistra e basso a destra del testo rispetto all'area di disegno quando viene visualizzato. Questa operazione viene eseguita passando il `textBounds` rettangolo dallo stesso `xText` e `yText` valori:

```csharp
textBounds.Offset(xText, yText);
```

A questo punto gli angoli superiore sinistro e inferiore destro del rettangolo consente di impostare i punti iniziale e finale della sfumatura.

## <a name="animating-a-gradient"></a>Animazione di una sfumatura

Esistono diversi modi per animare una sfumatura. Un approccio consiste nell'aggiungere un'animazione di punti iniziale e finale. Il **animazione sfumatura** pagina non si sposta i due punti in un cerchio viene centrato nell'area di disegno. Il raggio del cerchio è la metà larghezza o dell'altezza dell'area di disegno, qualunque sia il minore. I punti iniziale e finale sono opposto tra loro in questa cerchio e sfumatura va da bianco a nero con un `Mirror` modalità riquadro:

[![Animazione della sfumatura](linear-gradient-images/GradientAnimation.png "animazione sfumatura")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

Il costruttore crea il `SKCanvasView`. Il `OnAppearing` e `OnDisappearing` metodi gestiscono la logica dell'animazione:

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

Il `OnTimerTick` metodo calcola un `angle` valore che viene animata da 0 a 2π ogni 3 secondi. 

Ecco un modo per calcolare i due punti di sfumatura. Un' `SKPoint` valore denominato `vector` viene calcolata per estendere dal centro dell'area di disegno a un punto sul raggio del cerchio. La direzione del vettore corrente si basa sui valori seno e il coseno dell'angolo. Vengono quindi calcolati i due punti di sfumatura opposti: un punto viene calcolato sottraendo tale vettore dal punto center e altri punti viene calcolato aggiungendo il vettore punto centrale:

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

Un approccio leggermente diverso richiede meno codice. Questo approccio Usa la [ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) overload di metodo con una trasformazione di matrice come ultimo argomento. Questo approccio è la versione di [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio:

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

Se la larghezza dell'area di disegno è inferiore all'altezza, quindi i due punti di sfumatura sono impostati su (0, 0) e (`info.Width`, 0). La trasformazione di rotazione passato come ultimo argomento a `CreateLinearGradient` Ruota in modo efficace i due punti intorno al centro dello schermo.

Si noti che se l'angolo è 0, non vi è alcuna rotazione e i due punti di sfumatura sono gli angoli superiore sinistro e superiore destro dell'area di disegno. Tali punti non sono gli stessi punti di gradienti calcolati, come illustrato nella precedente `CreateLinearGradient` chiamare. Ma tali punti rappresentano _parallele_ alla linea della sfumatura orizzontale che intersechi il centro dell'area di disegno e sono il risultato in una sfumatura identica.

**Rainbow sfumatura**

Il **arcobaleno sfumatura** pagina consente di disegnare un arcobaleno nell'angolo in alto a sinistra dell'area di disegno nell'angolo in basso a destra. Ma questa sfumatura con arcobaleno non è, ad esempio un arcobaleno reale. È direttamente anziché curvi, ma si basa su otto colori HSL (hue-saturazione-luminosità) che dipendono da un ciclo tra i valori di tonalità compreso tra 0 e 360:

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

Che codice fa parte di `PaintSurface` gestore mostrato di seguito. Il gestore inizia con la creazione di un percorso che definisce un poligono sei lati che si estende dall'angolo superiore sinistro dell'area di disegno nell'angolo in basso a destra:

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

I due punti di sfumatura nel `CreateLinearGradient` metodo si basano su due dei punti che definiscono questo percorso: I punti sono più vicino all'angolo superiore sinistro. Il primo è sul bordo superiore dell'area di disegno e il secondo è il bordo sinistro dell'area di disegno. Ecco il risultato:

[![Sfumatura con arcobaleno difettoso](linear-gradient-images/RainbowGradientFaulty.png "sfumatura arcobaleno difettoso")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

Si tratta di un'immagine di interessante, ma non è abbastanza lo scopo. Il problema è che quando si crea una sfumatura lineare, le righe di costante di colore sono perpendicolare alla linea della sfumatura. La linea della sfumatura si basa sui punti in cui la figura interessa i bordi superiore e sinistro e tale riga non è in genere perpendicolare ai bordi che si estendono all'angolo inferiore destro della figura. Questo approccio funzionerebbe solo se l'area di disegno sono stato quadrato.

Per creare una sfumatura con arcobaleno appropriate, la linea della sfumatura deve essere perpendicolare al bordo dell'arcobaleno. Ovvero un calcolo più complesso. Deve essere definito un vettore parallela sul lato lungo della figura. Il vettore è ruotata di 90 gradi in modo che risulti perpendicolare a tale lato. Quindi allungamento per essere la larghezza dell'elemento figure moltiplicando per `rainbowWidth`. I due punti di sfumatura in un punto di fianco nella figura vengono calcolati in base e che puntano oltre il vettore. Ecco il codice che viene visualizzato nei **arcobaleno sfumatura** nella pagina il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio:

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

[![Sfumatura con arcobaleno](linear-gradient-images/RainbowGradient.png "arcobaleno sfumatura")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**Colori di un numero infinito**

Una sfumatura con arcobaleno viene usata anche nel **infinito colori** pagina. Questa pagina consente di disegnare utilizzando un oggetto percorso descritto nell'articolo un segno di un numero infinito [ **tre tipi di curve di Bézier**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs). L'immagine viene quindi colorato con una sfumatura animato arcobaleno che esegue lo sweep continuamente tra l'immagine.

Il costruttore crea il `SKPath` oggetto che descrive il simbolo di infinito. Dopo aver creato il percorso, il costruttore inoltre possibile ottenere i limiti rettangolari del percorso. Successivamente calcola un valore denominato `gradientCycleLength`. Se una sfumatura è basata sugli angoli superiore sinistro e inferiore destro del `pathBounds` rettangolo, ciò `gradientCycleLength` valore rappresenta la larghezza orizzontale totale del modello della sfumatura:

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

Costruttore crea anche il `colors` matrice per l'arcobaleno e `SKCanvasView` oggetto.

Esegue l'override del `OnAppearing` e `OnDisappearing` metodi eseguono l'overhead per l'animazione. Il `OnTimerTick` metodo anima la `offset` campo compreso tra 0 e `gradientCycleLength` ogni due secondi:

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

Infine, il `PaintSurface` gestore esegue il rendering il simbolo di infinito. Poiché il percorso contiene coordinate negative e positive che circonda un punto centrale di (0, 0), un `Translate` trasformazione nell'area di disegno viene usata per lo spostamento al centro. La trasformazione di traslazione è seguita da un `Scale` trasformazione che applica un fattore di scala che rende il segno di un numero infinito di dimensioni corrispondenti rimanendo comunque entro 95% della larghezza e altezza dell'area di disegno. 

Si noti che il `STROKE_WIDTH` viene aggiunta anche la larghezza e altezza del rettangolo di delimitazione del percorso. Il percorso verrà tracciato con una riga di questa larghezza, in modo che le dimensioni delle dimensioni del numero infinito di rendering viene incrementata a metà larghezza specificata su tutti e quattro i lati:

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

Esaminare i punti passati come i primi due argomenti di `SKShader.CreateLinearGradient`. Tali punti sono basati sul percorso originale rettangolo di delimitazione. È il primo punto (&ndash;250, &ndash;100) e il secondo è (250, 100). Interni di SkiaSharp, tali punti sono soggetti alla trasformazione di area di disegno corrente in modo da allinearli correttamente con il simbolo di infinito visualizzato.

Senza l'ultimo argomento a `CreateLinearGradient`, è possibile visualizzare una sfumatura con arcobaleno che si estende dall'angolo superiore sinistro del segno di un numero infinito all'angolo inferiore destro. (In realtà, la sfumatura estende dall'angolo superiore sinistro all'angolo inferiore destro del rettangolo di delimitazione. Il simbolo di infinito sottoposto a rendering è maggiore del rettangolo di delimitazione della metà il `STROKE_WIDTH` valore su tutti i lati. Poiché la sfumatura di rossa all'inizio e fine, sia la sfumatura viene creata con `SKShaderTileMode.Repeat`, la differenza non è evidente.)

Con questo ultimo argomento a `CreateLinearGradient`, il motivo della sfumatura di sweep in modo continuo tra l'immagine:

[![I colori Infinity](linear-gradient-images/InfinityColors.png "infinito colori")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>La trasparenza e sfumature

I colori che contribuiscono a una sfumatura possono incorporare la trasparenza. Invece di una sfumatura che si dissolve da un colore a altro, la sfumatura può dissolvenza in entrata da un colore su trasparente. 

È possibile usare questa tecnica per ottenere alcuni effetti interessanti. Uno degli esempi classici illustra un oggetto grafico con la reflection:

[![Reflection sfumatura](linear-gradient-images/ReflectionGradient.png "Reflection sfumatura")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

Il testo che viene capovolto è visualizzato con una sfumatura che è trasparente in alto per completamente trasparente nella parte inferiore del 50%. Questi livelli di trasparenza sono associati i valori alfa di 0x80 e 0.

Il `PaintSurface` gestore nel **Reflection sfumatura** pagina ridimensiona le dimensioni del testo sul 90% della larghezza dell'area di disegno. Successivamente calcola `xText` e `yText` valori per posizionare il testo da allineare al centro in senso orizzontale, ma si trova su una linea di base corrispondente al centro verticale della pagina:

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

Quelli `xText` e `yText` i valori sono gli stessi valori utilizzati per visualizzare il testo riflesso nel `DrawText` chiamare nella parte inferiore del `PaintSurface` gestore. Appena prima che il codice, tuttavia, si noterà una chiamata ai `Scale` metodo `SKCanvas`. Ciò `Scale` metodo scala orizzontalmente da 1 (che non esegue alcuna operazione) ma verticalmente base &ndash;1, che in modo efficace consente di capovolgere tutto capovolto. Il centro della rotazione è impostato per il punto (0, `yText`), dove `yText` è al centro verticale dell'area di disegno, originariamente calcolato come `info.Height` diviso per 2.

Tenere presente che Skia Usa la sfumatura per colorare gli oggetti grafici prima le trasformazioni di area di disegno. Dopo che viene disegnato il testo unreflected, il `textBounds` rettangolo viene spostato in modo che corrisponde al testo visualizzato:

```csharp
textBounds.Offset(xText, yText);
```

Il `CreateLinearGradient` chiamata definisce una sfumatura dall'inizio di questo rettangolo nella parte inferiore. La sfumatura è completamente trasparente di colore blu (`paint.Color.WithAlpha(0)`) di colore blu trasparente il 50% (`paint.Color.WithAlpha(0x80)`). La trasformazione di canvas inverte il testo capovolto, in modo che inizia in corrispondenza della linea di base, il blu trasparente il 50% e diventa trasparente nella parte superiore del testo.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
