---
title: Effetti per il tracciato in SkiaSharp
description: Questo articolo illustra i vari effetti percorso SkiaSharp che consentono i percorsi da utilizzare per la traccia e la compilazione e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: charlespetzold
ms.author: chape
ms.date: 07/29/2017
ms.openlocfilehash: 28f628fb4e8ab77e9c36e6e1972d7269ad0dad4d
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615678"
---
# <a name="path-effects-in-skiasharp"></a>Effetti per il tracciato in SkiaSharp

_Individuare i vari effetti di percorso che consentono i percorsi da utilizzare per la traccia e la compilazione_

Oggetto *effetto del percorso* è un'istanza del [ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/) classe che viene creato con uno degli otto statico `Create` metodi. Il `SKPathEffect` oggetto viene quindi impostato sul [ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/) proprietà di un `SKPaint` oggetti per un'ampia gamma di effetti interessanti, ad esempio, che traccia una linea con un piccolo percorso replicata:

![](effects-images/patheffectsample.png "L'esempio di catena collegato")

Effetti per il tracciato consentono di:

- Disegnare una linea con punti e trattini
- Disegnare una linea con qualsiasi percorso piena
- Un'area riempita con linee tratteggiate
- Un'area riempita con un percorso affiancato
- Verificare gli angoli arrotondati vivi
- Aggiungere casuale "instabilità" a linee e curve

Inoltre, è possibile combinare due o più effetti per il tracciato.

Questo articolo illustra anche come usare il `GetFillPath` metodo di `SKPaint` per convertire un percorso in un altro percorso applicando le proprietà degli `SKPaint`, tra cui `StrokeWidth` e `PathEffect`. Di conseguenza alcune interessanti tecniche, ad esempio il recupero di un percorso che è una struttura di un altro percorso. `GetFillPath` è anche utile in relazione gli effetti di percorso.

## <a name="dots-and-dashes"></a>Punti e trattini

L'utilizzo dei [ `PathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/) metodo è stato descritto nell'articolo [ **da punti e trattini**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md). Il primo argomento del metodo è una matrice contenente un numero pari di due o più valori, alternando tra le lunghezze dei trattini e le lunghezze dei gap tra i trattini:

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Questi valori sono *non* relativo lo spessore del tratto. Ad esempio, se lo spessore del tratto è 10, e si desidera una linea composto quadrati trattini e spazi square, impostare il `intervals` matrice {10, 10}. Il `phase` argomento indica all'interno del motivo a tratteggio in cui la riga inizia. In questo esempio, se si desidera che la riga inizia con il gap di square, impostare `phase` a 10.

Le entità finali di trattini sono interessate dal `StrokeCap` proprietà di `SKPaint`. Per le larghezze di tratto ampia, è molto comune per impostare questa proprietà su `SKStrokeCap.Round` arrotondare le entità finali di trattini. In questo caso, i valori di `intervals` matrice *non* includono la lunghezza aggiuntiva risultanti dall'arrotondamento, il che significa che un punto circolare è necessario specificare una larghezza pari a zero. Per uno spessore pari a 10, per creare una linea con punti circolare e gap tra i punti del diametro della stesso, usare un `intervals` matrice di {0, 20}.

Il **animato testo puntato** pagina è simile al **testo con contorni** pagina descritta nell'articolo [ **l'integrazione di testo e grafica** ](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) in che viene visualizzato descritti caratteri del testo impostando il `Style` proprietà del `SKPaint` dell'oggetto a `SKPaintStyle.Stroke`. Inoltre **animato testo puntato** Usa `SKPathEffect.CreateDash` per concedere questo descrive un aspetto separata da punti e il programma aggiunge anche un'animazione la `phase` argomento del `SKPathEffect.CreateDash` metodo sembra che viaggiano attraverso il testo per i punti caratteri. Ecco la pagina in modalità orizzontale:

[![](effects-images/animateddottedtext-small.png "Tripla screenshot della pagina aggiungendo un'animazione testo puntato")](effects-images/animateddottedtext-large.png#lightbox "tripla screenshot della pagina aggiungendo un'animazione testo punteggiato")

Il [ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe inizia con la definizione di alcune costanti e sovrascrive anche il `OnAppearing` e `OnDisappearing` metodi per l'animazione:

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    const string text = "DOTTED";
    const float strokeWidth = 10;
    static readonly float[] dashArray = { 0, 2 * strokeWidth };

    SKCanvasView canvasView;
    bool pageIsActive;

    public AnimatedDottedTextPage()
    {
        Title = "Animated Dotted Text";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;

        Device.StartTimer(TimeSpan.FromSeconds(1f / 60), () =>
        {
            canvasView.InvalidateSurface();
            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

Il `PaintSurface` gestore inizia creando un `SKPaint` oggetto visualizzazione del testo. Il `TextSize` proprietà viene regolata in base alla larghezza dello schermo:

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create an SKPaint object to display the text
        using (SKPaint textPaint = new SKPaint
            {
                Style = SKPaintStyle.Stroke,
                StrokeWidth = strokeWidth,
                StrokeCap = SKStrokeCap.Round,
                Color = SKColors.Blue,
            })
        {
            // Adjust TextSize property so text is 95% of screen width
            float textWidth = textPaint.MeasureText(text);
            textPaint.TextSize *= 0.95f * info.Width / textWidth;

            // Find the text bounds
            SKRect textBounds = new SKRect();
            textPaint.MeasureText(text, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Animate the phase; t is 0 to 1 every second
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 1 / 1);
            float phase = -t * 2 * strokeWidth;

            // Create dotted line effect based on dash array and phase
            using (SKPathEffect dashEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                // Set it to the paint object
                textPaint.PathEffect = dashEffect;

                // And draw the text
                canvas.DrawText(text, xText, yText, textPaint);
            }
        }
    }
}
```

Verso la fine del metodo, il `SKPathEffect.CreateDash` metodo viene chiamato usando la `dashArray` che viene definito come un campo e l'animazione `phase` valore. Il `SKPathEffect` istanza è impostata sul `PathEffect` proprietà del `SKPaint` oggetto visualizzazione del testo.

In alternativa, è possibile impostare il `SKPathEffect` dell'oggetto per il `SKPaint` oggetto prima della misurazione del testo e allineamento al centro nella pagina. In tal caso, tuttavia, animate punti e trattini causano alcune variazioni nelle dimensioni del testo sottoposto a rendering e il testo tende a vibrazione un po'. (Provalo!)

Si noterà anche che come cerchio punti animati intorno caratteri del testo, c'è un determinato punto ogni curva chiusa in cui i punti sembrano Apri nuova finestra da e verso l'esistenza. Si tratta in cui il percorso che definisce la struttura del carattere inizia e finisce. Se la lunghezza del percorso non è un multiplo intero della lunghezza di motivo di tratteggio (in questo caso 20 pixel) alla fine del percorso può contenere solo una parte di tale modello.

È possibile modificare la lunghezza del motivo di tratteggio per adattarsi alla lunghezza del percorso, ma che è necessario determinare la lunghezza del percorso, una tecnica che è descritti in un articolo futuro.

Il **Dot / Dash Morph** programma anima il motivo di tratteggio a se stessa in modo che sembrano trattini per dividere in punti, che combinano in trattini form nuovamente:

[![](effects-images/dotdashmorph-small.png "Tripla screenshot della pagina Dot Dash Morph")](effects-images/dotdashmorph-large.png#lightbox "tripla screenshot della pagina Morph trattino punto")

Il [ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe sostituzioni il `OnAppearing` e `OnDisappearing` esattamente come il precedente programma ha, ma la classe definisce i metodi di `SKPaint` oggetto come un campo:

```csharp
public class DotDashMorphPage : ContentPage
{
    const float strokeWidth = 30;
    static readonly float[] dashArray = new float[4];

    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint ellipsePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = strokeWidth,
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create elliptical path
        using (SKPath ellipsePath = new SKPath())
        {
            ellipsePath.AddOval(new SKRect(50, 50, info.Width - 50, info.Height - 50));

            // Create animated path effect
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 3 / 3);
            float phase = 0;

            if (t < 0.25f)  // 1, 0, 1, 2 --> 0, 2, 0, 2
            {
                float tsub = 4 * t;
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2 * tsub;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2;
            }
            else if (t < 0.5f)  // 0, 2, 0, 2 --> 1, 2, 1, 0
            {
                float tsub = 4 * (t - 0.25f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2 * (1 - tsub);
                phase = strokeWidth * tsub;
            }
            else if (t < 0.75f) // 1, 2, 1, 0 --> 0, 2, 0, 2
            {
                float tsub = 4 * (t - 0.5f);
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2 * tsub;
                phase = strokeWidth * (1 - tsub);
            }
            else               // 0, 2, 0, 2 --> 1, 0, 1, 2
            {
                float tsub = 4 * (t - 0.75f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2 * (1 - tsub);
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2;
            }

            using (SKPathEffect pathEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                ellipsePaint.PathEffect = pathEffect;
                canvas.DrawPath(ellipsePath, ellipsePaint);
            }
        }
    }
}
```

Il `PaintSurface` gestore viene creato un percorso ellittico basato sulle dimensioni della pagina e viene eseguita una sezione di codice che imposta lungo il `dashArray` e `phase` variabili. Come la variabile animata `t` compreso tra 0 e 1, il `if` blocchi suddividere da quel momento in quattro trimestri e in ognuna di tali trimestri, `tsub` inoltre compreso tra 0 e 1. Alla fine, il programma crea il `SKPathEffect` e lo imposta sul `SKPaint` oggetto per il disegno.

## <a name="from-path-to-path"></a>Dal percorso

Il [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/) metodo del `SKPaint` Trasforma un percorso in un altro in base alle impostazioni di `SKPaint` oggetto. Per visualizzarne il funzionamento, sostituire il `canvas.DrawPath` chiamare nel programma precedente con il codice seguente:

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

In questo nuovo codice, il `GetFillPath` chiamare converte il `ellipsePath` (che è semplicemente un ovale) in `newPath`, che viene quindi mostrato con `newPaint`. Il `newPaint` oggetto viene creato con tutte le impostazioni predefinite delle proprietà ad eccezione del fatto che il `Style` viene impostata in base al valore booleano restituito da `GetFillPath`.

Gli oggetti visivi sono identici tranne che per il colore, che verrà impostato nelle `ellipsePaint` ma non `newPaint`. Anziché l'ellisse semplice definito in `ellipsePath`, `newPath` contiene numerose distribuzioni del percorso che definiscono la serie di punti e trattini. Questo è il risultato dell'applicazione di diverse proprietà della `ellipsePaint` , ovvero `StrokeWidth`, `StrokeCap`, e `PathEffect` , alla `ellipsePath` e inserendo il percorso risultante nella `newPath`. Il `GetFillPath` metodo restituisce un valore booleano che indica se il percorso di destinazione deve essere compilato; in questo esempio, il valore restituito è `true` per riempire il percorso.

Provare a modificare il `Style` impostazione `newPaint` a `SKPaintStyle.Stroke` e vedrai i contorni del singolo percorso delimitati da una linea uno-a larghezza in pixel.

## <a name="stroking-with-a-path"></a>Traccia con un percorso

Il [ `SKPathEffect.Create1DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create1DPath/p/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPath1DPathEffectStyle/) metodo è concettualmente simile a `SKPathEffect.CreateDash` ad eccezione del fatto che si specifica un percorso invece un motivo di trattini e spazi. Questo percorso viene replicato più volte a tratto la riga o una curva.

La sintassi è:

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

> [!IMPORTANT]
> Prestare attenzione: un overload del `Create1DPath` che viene definita con un argomento di enumerazione di tipo `SkPath1DPathEffect` con una minuscola 'k'. Questo nome è un errore e, di conseguenza, enumerazione e il metodo sono deprecate, ma è molto semplice per il metodo deprecato diventare parte del codice che è difficile vedere esattamente ciò che è errato.

In generale, il percorso in cui si passa a `Create1DPath` sarà piccole e centrato intorno al punto (0, 0). Il `advance` parametro indica la distanza tra le aree di percorso come il percorso viene replicato nella riga. In genere si imposta in questo argomento per la larghezza approssimativa del percorso. Il `phase` svolto dal argomento stesso ruolo di seguito viene svolto nel `CreateDash` (metodo).

Il [ `SKPath1DPathEffectStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath1DPathEffectStyle/) ha tre membri:

- [`Translate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Translate/)
- [`Rotate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Rotate/)
- [`Morph`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Morph/)

Il `Translate` membro fa sì che il percorso deve rimanere nell'orientamento stesso come viene replicato lungo una riga o una curva. Per `Rotate`, il percorso viene ruotato in una tangente della curva di base. È il percorso relativo orientamento del normale per le linee orizzontali. `Morph` è simile a `Rotate` ad eccezione del fatto che il percorso stesso anche curvo in modo da corrispondere la curvatura della linea viene tracciata.

Il **effettive di percorso 1 D** illustra come queste tre opzioni. Il [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) file definisce un controllo di selezione contenente tre elementi corrispondenti ai tre membri dell'enumerazione:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.OneDimensionalPathEffectPage"
             Title="1D Path Effect">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="effectStylePicker"
                Title="Effect Style"
                Grid.Row="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Translate</x:String>
                <x:String>Rotate</x:String>
                <x:String>Morph</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1" />
    </Grid>
</ContentPage>
```

Il [ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) file code-behind definisce tre `SKPathEffect` oggetti come campi. Questi vengono creati tutti usando `SKPathEffect.Create1DPath` con `SKPath` gli oggetti creati usando `SKPath.ParseSvgPathData`. Il primo è una semplice casella, la seconda è una forma di rombo e il terzo è un rettangolo. Questi vengono usati per illustrare gli stili di tre effetto:

```csharp
public partial class OneDimensionalPathEffectPage : ContentPage
{
    SKPathEffect translatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 -10 L 10 -10, 10 10, -10 10 Z"),
                                  24, 0, SKPath1DPathEffectStyle.Translate);

    SKPathEffect rotatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 0 L 0 -10, 10 0, 0 10 Z"),
                                  20, 0, SKPath1DPathEffectStyle.Rotate);

    SKPathEffect morphPathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -25 -10 L 25 -10, 25 10, -25 10 Z"),
                                  55, 0, SKPath1DPathEffectStyle.Morph);

    SKPaint pathPaint = new SKPaint
    {
        Color = SKColors.Blue
    };

    public OneDimensionalPathEffectPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            path.MoveTo(new SKPoint(0, 0));
            path.CubicTo(new SKPoint(2 * info.Width, info.Height),
                         new SKPoint(-info.Width, info.Height),
                         new SKPoint(info.Width, 0));

            switch (effectStylePicker.Items[effectStylePicker.SelectedIndex])
            {
                case "Translate":
                    pathPaint.PathEffect = translatePathEffect;
                    break;

                case "Rotate":
                    pathPaint.PathEffect = rotatePathEffect;
                    break;

                case "Morph":
                    pathPaint.PathEffect = morphPathEffect;
                    break;
            }

            canvas.DrawPath(path, pathPaint);
        }
    }
}
```

Il `PaintSurface` gestore crea una curva di Bézier che un ciclo per se stesso e accede il selettore per determinare quali `PathEffect` deve essere usato per applicare la traccia. Le tre opzioni, ovvero `Translate`, `Rotate`, e `Morph` , ovvero vengono visualizzati da sinistra a destra:

[![](effects-images/1dpatheffect-small.png "Tripla screenshot della pagina del percorso effetto 1D")](effects-images/1dpatheffect-large.png#lightbox "tripla screenshot della pagina del percorso effetto 1D")

Il percorso specificato nella `SKPathEffect.Create1DPath` metodo è sempre pieni. Il percorso specificato nel `DrawPath` metodo sempre viene tracciato se il `SKPaint` oggetto ha relativo `PathEffect` proprietà è impostata su un effetto di percorso 1 giorno. Si noti che il `pathPaint` oggetto non ha alcuno `Style` impostazione, impostazione predefinita è solitamente `Fill`, ma viene tracciato indipendentemente dal fatto che il percorso.

La finestra utilizzata nel `Translate` esempio è 20 pixel quadrati e il `advance` argomento è impostato su 24. Questa differenza provoca un gap tra le caselle quando la riga è all'incirca orizzontale o verticale, ma le finestre si sovrappongono leggermente quando la riga è diagonale perché la diagonale della casella viene 28.3 pixel.

La forma di rombo nella `Rotate` esempio è anche 20 pixel di larghezza. Il `advance` è impostato su 20 in modo da continuano i punti di tocco come la forma di rombo viene ruotata insieme la curvatura della riga.

La forma rettangolo nel `Morph` esempio è 50 pixel in larghezza con un `advance` l'impostazione di 55 per rendere un breve intervallo tra i rettangoli come sono piegati attorno alla curva di Bézier.

Se il `advance` argomento è inferiore alla dimensione del percorso, quindi i percorsi replicati possono sovrapporsi. Ciò può comportare alcuni effetti interessanti. Il **catena collegato** pagina Visualizza una serie di cerchi che sembrano essere simile a una catena di collegato, si blocca nella forma distintivo di un catenaria sovrapposti:

[![](effects-images/linkedchain-small.png "Tripla screenshot della pagina collegata catena")](effects-images/linkedchain-large.png#lightbox "tripla screenshot della pagina della catena collegato")

Aspetto molto simili e si noterà che quelli non sono effettivamente cerchi. Ogni collegamento nella catena è due archi, ridimensionati e posizionati in modo che sembrano per la connessione con collegamenti adiacenti.

Sotto forma di un catenaria si blocca una catena o cable della distribuzione del peso uniforme. Un arch creato sotto forma di un catenaria invertito trae vantaggio da una distribuzione uniforme di pressione dal peso di un arch. Catenaria presenta una descrizione di matematica apparentemente semplice:

y = un · cosh(x / a)

Il *cosh* è la funzione coseno iperbolico. Per la *x* uguale a 0, *cosh* è uguale a zero e *y* equals *un*. Che è al centro della catenaria. Ad esempio il *coseno* (funzione), *cosh* è detto *anche*, vale a dire che *cosh(–x)* è uguale a *cosh(x)*, e aumentano i valori per l'aumento argomenti positivi o negativi. Questi valori descrivono le curve che costituiscono i lati del catenaria.

Trovare il valore corretto della *un* per adattarsi catenaria in base alle dimensioni della pagina del telefono non è un calcolo diretto. Se *w* e *h* è la larghezza e altezza di un rettangolo, il valore ottimale della *un* soddisfa l'equazione seguente:

cosh (w/2/a) = 1 + h / a

Il metodo seguente il [ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) classe incorpora tale uguaglianza facendo riferimento a due espressioni a sinistra e a destra del segno di uguale come `left` e `right`. Per valori bassi di *una*, `left` è maggiore di quella `right`; valori elevati di *un*, `left` è minore di `right`. Il `while` ciclo viene convertito un valore ottimale della *un*:

```csharp
float FindOptimumA(float width, float height)
{
    Func<float, float> left = (float a) => (float)Math.Cosh(width / 2 / a);
    Func<float, float> right = (float a) => 1 + height / a;

    float gtA = 1;         // starting value for left > right
    float ltA = 10000;     // starting value for left < right

    while (Math.Abs(gtA - ltA) > 0.1f)
    {
        float avgA = (gtA + ltA) / 2;

        if (left(avgA) < right(avgA))
        {
            ltA = avgA;
        }
        else
        {
            gtA = avgA;
        }
    }

    return (gtA + ltA) / 2;
}
```

Il `SKPath` dell'oggetto per i collegamenti viene creato nel costruttore della classe e le risultanti `SKPathEffect` oggetto viene quindi impostato sulle `PathEffect` proprietà del `SKPaint` oggetto archiviato come campo:

```csharp
public class LinkedChainPage : ContentPage
{
    const float linkRadius = 30;
    const float linkThickness = 5;

    Func<float, float, float> catenary = (float a, float x) => (float)(a * Math.Cosh(x / a));

    SKPaint linksPaint = new SKPaint
    {
        Color = SKColors.Silver
    };

    public LinkedChainPage()
    {
        Title = "Linked Chain";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the individual links
        SKRect outer = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
        SKRect inner = outer;
        inner.Inflate(-linkThickness, -linkThickness);

        using (SKPath linkPath = new SKPath())
        {
            linkPath.AddArc(outer, 55, 160);
            linkPath.ArcTo(inner, 215, -160, false);
            linkPath.Close();

            linkPath.AddArc(outer, 235, 160);
            linkPath.ArcTo(inner, 395, -160, false);
            linkPath.Close();

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(linkPath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);
        }
    }
    ...
}
```

Il compito principale del `PaintSurface` gestore consiste nel creare un percorso per catenaria stesso. Dopo aver determinato ottimo *una* e archiviarlo nel `optA` variabile, è necessario eseguire anche calcolare un offset dalla parte superiore della finestra. Quindi, può accumulare una raccolta di `SKPoint` i valori per catenaria, che trasforma in un percorso e disegnare il percorso con creato in precedenza `SKPaint` oggetto:

```csharp
public class LinkedChainPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Width and height of catenary
        int width = info.Width;
        float height = info.Height - linkRadius;

        // Find the optimum 'a' for this width and height
        float optA = FindOptimumA(width, height);

        // Calculate the vertical offset for that value of 'a'
        float yOffset = catenary(optA, -width / 2);

        // Create a path for the catenary
        SKPoint[] points = new SKPoint[width];

        for (int x = 0; x < width; x++)
        {
            points[x] = new SKPoint(x, yOffset - catenary(optA, x - width / 2));
        }

        using (SKPath path = new SKPath())
        {
            path.AddPoly(points, false);

            // And render that path with the linksPaint object
            canvas.DrawPath(path, linksPaint);
        }
    }
    ...
}
```

Il programma definisce il percorso utilizzato nella `Create1DPath` avere relativo (0, 0) punto al centro. Ciò sembra ragionevole poiché il (0, 0) punto del percorso sia allineato con la riga o una curva che è decorazione. Tuttavia, è possibile usare un non-centrato (0, 0) il punto per alcuni effetti speciali.

Il **nastro trasportatore** pagina consente di creare un percorso simile a quello di un nastro trasportatore oblunghi con una curva superiore e inferiore viene ridimensionato in base alle dimensioni della finestra. Tale percorso viene tracciato con un semplice `SKPaint` dell'oggetto di colore grigio e 20 pixel di larghezza e quindi tracciato nuovamente con un'altra `SKPaint` dell'oggetto con un `SKPathEffect` oggetti che fanno riferimento a un percorso che assomiglia a un bucket minimo:

[![](effects-images/conveyorbelt-small.png "Tripla screenshot della pagina del nastro trasportatore")](effects-images/conveyorbelt-large.png#lightbox "tripla screenshot della pagina del nastro trasportatore")

I (0, 0) punto del percorso di bucket è l'handle, pertanto quando il `phase` argomento è animato, i bucket sembrano vertono sul nastro trasportatore, forse Rolly di acqua nella parte inferiore e il dump orizzontale nella parte superiore.

Il [ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) classe implementa animazione con sostituzioni del `OnAppearing` e `OnDisappearing` metodi. Il percorso per il bucket è definito nel costruttore della pagina:

```csharp
public class ConveyorBeltPage : ContentPage
{
    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint conveyerPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 20,
        Color = SKColors.DarkGray
    };

    SKPath bucketPath = new SKPath();

    SKPaint bucketsPaint = new SKPaint
    {
        Color = SKColors.BurlyWood,
    };

    public ConveyorBeltPage()
    {
        Title = "Conveyor Belt";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the bucket starting with the handle
        bucketPath.AddRect(new SKRect(-5, -3, 25, 3));

        // Sides
        bucketPath.AddRoundedRect(new SKRect(25, -19, 27, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);
        bucketPath.AddRoundedRect(new SKRect(63, -19, 65, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);

        // Five slats
        for (int i = 0; i < 5; i++)
        {
            bucketPath.MoveTo(25, -19 + 8 * i);
            bucketPath.LineTo(25, -13 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.CounterClockwise, 65, -13 + 8 * i);
            bucketPath.LineTo(65, -19 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.Clockwise, 25, -19 + 8 * i);
            bucketPath.Close();
        }

        // Arc to suggest the hidden side
        bucketPath.MoveTo(25, -17);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.Clockwise, 65, -17);
        bucketPath.LineTo(65, -19);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.CounterClockwise, 25, -19);
        bucketPath.Close();

        // Make it a little bigger and correct the orientation
        bucketPath.Transform(SKMatrix.MakeScale(-2, 2));
        bucketPath.Transform(SKMatrix.MakeRotationDegrees(90));
    }
    ...
```

Il codice di creazione di bucket viene completata con le due trasformazioni che rendono il bucket un po' più grande e trasformarlo lateralmente. Applicando queste trasformazioni è stata più semplice che modifica tutte le coordinate nel codice precedente.

Il `PaintSurface` gestore inizia definendo un percorso per il nastro trasportatore stesso. Si tratta semplicemente di una coppia di righe e una coppia di cerchi e virgola che vengono disegnati con una riga grigio scuro a livello di pixel 20:

```csharp
public class ConveyorBeltPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float width = info.Width / 3;
        float verticalMargin = width / 2 + 150;

        using (SKPath conveyerPath = new SKPath())
        {
            // Straight verticals capped by semicircles on top and bottom
            conveyerPath.MoveTo(width, verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, 2 * width, verticalMargin);
            conveyerPath.LineTo(2 * width, info.Height - verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, width, info.Height - verticalMargin);
            conveyerPath.Close();

            // Draw the conveyor belt itself
            canvas.DrawPath(conveyerPath, conveyerPaint);

            // Calculate spacing based on length of conveyer path
            float length = 2 * (info.Height - 2 * verticalMargin) +
                           2 * ((float)Math.PI * width / 2);

            // Value will be somewhere around 200
            float spacing = length / (float)Math.Round(length / 200);

            // Now animate the phase; t is 0 to 1 every 2 seconds
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 2 / 2);
            float phase = -t * spacing;

            // Create the buckets PathEffect
            using (SKPathEffect bucketsPathEffect =
                        SKPathEffect.Create1DPath(bucketPath, spacing, phase,
                                                  SKPath1DPathEffectStyle.Rotate))
            {
                // Set it to the Paint object and draw the path again
                bucketsPaint.PathEffect = bucketsPathEffect;
                canvas.DrawPath(conveyerPath, bucketsPaint);
            }
        }
    }
}
```

La logica di disegno sul nastro trasportatore non funziona in modalità orizzontale.

I bucket devono essere separate da uno spazio su 200 pixel di distanza su nastro trasportatore. Tuttavia, il nastro trasportatore probabilmente non è un multiplo di 200 pixel lungo, ovvero come il `phase` argomento di `SKPathEffect.Create1DPath` è animato, bucket preleverà dentro e fuori esistenza.

Per questo motivo, la prima viene calcolato un valore denominato `length` che rappresenta la lunghezza del nastro trasportatore. Poiché il nastro trasportatore è costituito da linee rette e cerchi e virgola, questo è un semplice calcolo. Successivamente, il numero di bucket viene calcolato dividendo `length` da 200. Questo valore viene arrotondato all'intero più vicino e che sia quindi suddivisa in `length`. Il risultato è una spaziatura per un numero integrale di bucket. Il `phase` argomento è semplicemente una frazione di tale.

## <a name="from-path-to-path-again"></a>Dal nuovo percorso

Nella parte inferiore del `DrawSurface` gestore **nastro trasportatore**, impostare come commento il `canvas.DrawPath` chiamare e sostituirlo con il codice seguente:

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Come con l'esempio precedente dei `GetFillPath`, si noterà che i risultati siano uguali ad eccezione di colore. Dopo l'esecuzione `GetFillPath`, il `newPath` oggetto contiene più copie del percorso di bucket, ognuna posizionata nello stesso punto che l'animazione posizionato loro al momento della chiamata.

## <a name="hatching-an-area"></a>Un'Area di tratteggio

Il [ `SKPathEffect.Create2DLines` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DLine/p/System.Single/SkiaSharp.SKMatrix/) metodo riempie un'area con linee parallele, spesso denominata *linee di tratteggio*. Il metodo ha la sintassi seguente:

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

Il `width` argomento specifica lo spessore del tratto delle linee tratteggiate. Il `matrix` parametro è una combinazione di rotazione di ridimensionamento e facoltativa. Il fattore di scala indica l'incremento di pixel che usa Skia lasciare spazi le linee tratteggiate. La separazione tra le linee è il fattore di scala meno il `width` argomento. Se il fattore di scala è minore o uguale al `width` valore, non vi sarà alcun spazio tra le linee tratteggiate e verrà visualizzata l'area da riempire. Specificare lo stesso valore per la scalabilità orizzontale e verticale.

Per impostazione predefinita, sono disposti orizzontalmente linee tratteggiate. Se il `matrix` parametro contiene la rotazione, le linee tratteggiate vengono ruotate in senso orario.

Il **tratteggio riempimento** pagina viene illustrato l'effetto del percorso. Il [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) classe definisce tre effetti per il tracciato come campi, il primo per le linee orizzontali tratteggiate con una larghezza pari a 3 pixel con un fattore ridimensionamento che indica che essi sono separate da uno spazio 6 pixel di distanza. La separazione tra la riga è pertanto di 3 pixel. L'effetto del percorso secondario è per le linee tratteggiate verticali con una larghezza pari a 6 pixel di spaziatura tra 24 pixel di distanza (quindi, la separazione è 18 pixel), e la terza etichetta è per 12 pixel con spaziatura wide 36 pixel di distanza di linee diagonali tratteggiate.

```csharp
public class HatchFillPage : ContentPage
{
    SKPaint fillPaint = new SKPaint();

    SKPathEffect horzLinesPath = SKPathEffect.Create2DLine(3, SKMatrix.MakeScale(6, 6));

    SKPathEffect vertLinesPath = SKPathEffect.Create2DLine(6,
        Multiply(SKMatrix.MakeRotationDegrees(90), SKMatrix.MakeScale(24, 24)));

    SKPathEffect diagLinesPath = SKPathEffect.Create2DLine(12,
        Multiply(SKMatrix.MakeScale(36, 36), SKMatrix.MakeRotationDegrees(45)));

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

Si noti che la matrice `Multiply` (metodo). Poiché i fattori di scala orizzontali e verticali sono uguali, non è importante l'ordine in cui vengono moltiplicate le matrici di ridimensionamento e rotazione.

Il `PaintSurface` gestore Usa questi effetti tre percorso con tre colori diversi in combinazione con `fillPaint` per riempire un rettangolo arrotondato ridimensionato per adattarlo alla pagina. Il `Style` proprietà è impostata sul `fillPaint` viene ignorata; quando il `SKPaint` oggetto include un effetto di percorso creato da `SKPathEffect.Create2DLine`, l'area viene riempita indipendentemente dal fatto che:

```csharp
public class HatchFillPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath roundRectPath = new SKPath())
        {
            // Create a path
            roundRectPath.AddRoundedRect(
                new SKRect(50, 50, info.Width - 50, info.Height - 50), 100, 100);

            // Horizontal hatch marks
            fillPaint.PathEffect = horzLinesPath;
            fillPaint.Color = SKColors.Red;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Vertical hatch marks
            fillPaint.PathEffect = vertLinesPath;
            fillPaint.Color = SKColors.Blue;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Diagonal hatch marks -- use clipping
            fillPaint.PathEffect = diagLinesPath;
            fillPaint.Color = SKColors.Green;

            canvas.Save();
            canvas.ClipPath(roundRectPath);
            canvas.DrawRect(new SKRect(0, 0, info.Width, info.Height), fillPaint);
            canvas.Restore();

            // Outline the path
            canvas.DrawPath(roundRectPath, strokePaint);
        }
    }
    ...
}
```

Se si osserva con attenzione i risultati, si noterà che non sono limitate le linee tratteggiate rosse e blu con precisione al rettangolo arrotondato. (Si tratta apparentemente una caratteristica del codice sottostante Skia.) Se questo non è soddisfacente, è illustrato un approccio alternativo per le linee diagonali tratteggiate in verde: il rettangolo arrotondato viene utilizzato come un tracciato di ritaglio e vengono disegnate le linee tratteggiate nella pagina intera.

Il `PaintSurface` gestore si conclude con una chiamata a semplicemente disegnare il rettangolo arrotondato, in modo da visualizzare la discrepanza con le linee tratteggiate rosse e blu:

[![](effects-images/hatchfill-small.png "Tripla screenshot della pagina di tratteggio riempimento")](effects-images/hatchfill-large.png#lightbox "tripla screenshot della pagina di tratteggio riempimento")

La schermata Android non davvero un aspetto simile: il ridimensionamento dello screenshot ha causato il thin righe rosse e spazi thin di consolidare in linee rosse apparentemente più ampio e più ampio.

## <a name="filling-with-a-path"></a>Riempimento con un percorso

Il [ `SKPathEffect.Create2DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DPath/p/SkiaSharp.SKMatrix/SkiaSharp.SKPath/) consente di riempire un'area con un percorso che viene replicato in senso orizzontale e verticale, in effetti affiancamento l'area:

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

Il `SKMatrix` fattori di scala indicano la spaziatura orizzontale e verticale del percorso di replica. Ma non è possibile ruotare il percorso usando questo `matrix` argomento; se si desidera che il percorso ruotato, ruotare il percorso stesso usando il `Transform` metodo definito dal `SKPath`.

Il percorso di replicato è in genere allineato con i bordi superiore e sinistro della schermata piuttosto che dell'area riempita. È possibile eseguire l'override di questo comportamento, fornendo i fattori di traduzione compreso tra 0 e i fattori di scala per specificare gli offset orizzontali e verticali i lati sinistro e superiore.

Il **riempimento riquadro percorso** pagina viene illustrato l'effetto del percorso. Il percorso usato per affiancare l'area è definito come un campo la [ `PathFileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) classe. Le coordinate orizzontali e verticali compreso tra da -40 e 40, il che significa che questo percorso è 80 pixel quadrati:

```csharp
public class PathTileFillPage : ContentPage
{
    SKPath tilePath = SKPath.ParseSvgPathData(
        "M -20 -20 L 2 -20, 2 -40, 18 -40, 18 -20, 40 -20, " +
        "40 -12, 20 -12, 20 12, 40 12, 40 40, 22 40, 22 20, " +
        "-2 20, -2 40, -20 40, -20 8, -40 8, -40 -8, -20 -8 Z");
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Red;

            using (SKPathEffect pathEffect =
                   SKPathEffect.Create2DPath(SKMatrix.MakeScale(64, 64), tilePath))
            {
                paint.PathEffect = pathEffect;

                canvas.DrawRoundRect(
                    new SKRect(50, 50, info.Width - 50, info.Height - 50),
                    100, 100, paint);
            }
        }
    }
}
```

Nel `PaintSurface` gestore, la `SKPathEffect.Create2DPath` chiamate imposta la spaziatura orizzontale e verticale a 64 per fare in modo i riquadri quadrati 80 pixel si sovrappongano. Per fortuna, il percorso è simile a una parte del puzzle, la generazione di mesh perfettamente con i riquadri adiacenti:

[![](effects-images/pathtilefill-small.png "Tripla screenshot della pagina del percorso tessera occupa")](effects-images/pathtilefill-large.png#lightbox "tripla screenshot della pagina del percorso riquadro riempimento")

Il ridimensionamento nella schermata originale fa sì che alcuni distorsione, in particolare nella schermata di Android.

Si noti che questi riquadri sempre visualizzato interi e non vengano mai troncati. Nelle prime due schermate, non è ancora evidente che l'area riempita è un rettangolo arrotondato. Se si desidera troncare questi riquadri per una determinata area, usare un tracciato di ritaglio.

Provare a impostare il `Style` proprietà del `SKPaint` oggetto `Stroke`, e vedrai i singoli riquadri descritti invece compilato.

## <a name="rounding-sharp-corners"></a>Arrotondamento degli angoli ben strutturati

Il **arrotondato Heptagon** programma presentati nel [ **tre modi per disegnare un arco** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) articolo usato da un arco tangente per i punti di una figura sette lati della curva. Il **Heptagon arrotondato un altro** pagina viene illustrato un approccio molto più semplice che usa un effetto di percorso creato dalle [ `SKPathEffect.CreateCorner` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCorner/p/System.Single/) metodo:

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Anche se l'argomento singolo è denominato `radius` è necessario impostarlo a metà il raggio dell'angolo desiderato. (Si tratta di una caratteristica del codice Skia sottostante).

Di seguito è riportato il `PaintSurface` gestore nel [ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) classe:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);
    SKPoint[] vertices = new SKPoint[numVertices];
    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    float cornerRadius = 100;

    // Create the path
    using (SKPath path = new SKPath())
    {
        path.AddPoly(vertices, true);

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            // Set argument to half the desired corner radius!
            paint.PathEffect = SKPathEffect.CreateCorner(cornerRadius / 2);

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);

            // Uncomment DrawCircle call to verify corner radius
            float offset = cornerRadius / (float)Math.Sin(Math.PI * (numVertices - 2) / numVertices / 2);
            paint.Color = SKColors.Green;
            // canvas.DrawCircle(vertices[0].X, vertices[0].Y + offset, cornerRadius, paint);
        }
    }
}
```

È possibile usare questo effetto con riempimento in base o che traccia il `Style` proprietà del `SKPaint` oggetto. Ecco in tutte e tre le piattaforme:

[![](effects-images/anotherroundedheptagon-small.png "Tripla screenshot della pagina di un altro Heptagon arrotondato")](effects-images/anotherroundedheptagon-large.png#lightbox "tripla screenshot della pagina di un altro Heptagon arrotondato")

Si noterà che questo heptagon arrotondato sia identico al programma precedente. Se è necessario più convincente che il raggio dell'angolo è realmente 100 piuttosto che di 50 specificato nella `SKPathEffect.CreateCorner` chiamata, è possibile rimuovere il commento l'istruzione finale nel programma e vedere un cerchio di 100-radius sovrapposto all'angolo.

## <a name="random-jitter"></a>Variazione casuale

In alcuni casi le linee rette perfetta di infografica non sono piuttosto che si desidera, e si desidera la casualità un po'. In tal caso, è opportuno provare il [ `SKPathEffect.CreateDiscrete` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDiscrete/p/System.Single/System.Single/System.UInt32/) metodo:

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

È possibile usare questo effetto di percorso che traccia o la compilazione. Le righe sono separate in segmenti connesso, ovvero la durata approssimativa di cui viene specificata dalla `segLength` ed estendere in direzioni diverse. L'extent della deviazione dalla riga originale specificata dal `deviation`.

L'argomento finale è un valore di inizializzazione utilizzato per generare la sequenza pseudocasuale utilizzata per l'effetto. L'effetto di instabilità avrà un aspetto leggermente diverso per diversi valori di inizializzazione. L'argomento ha un valore predefinito pari a zero, il che significa che l'effetto è lo stesso ogni volta che si esegue il programma. Se si desidera instabilità diverso ogni volta che lo schermo viene aggiornato, è possibile impostare il valore di inizializzazione il `Millisecond` proprietà di un `DataTime.Now` valore (ad esempio).


Il **instabilità sperimentare** pagina consente di sperimentare più valori in un rettangolo di traccia:

[![](effects-images/jitterexperiment-small.png "Screenshot della pagina dell'esperimento instabilità è pari al triplo")](effects-images/jitterexperiment-large.png#lightbox "Triple screenshot of the JitterExperiment page")

Il programma è straightfoward. Il [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) un'istanza di file due `Slider` gli elementi e un `SKCanvasView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.JitterExperimentPage"
             Title="Jitter Experiment">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Minimum" Value="0" />
                    <Setter Property="Maximum" Value="100" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="segLengthSlider"
                Grid.Row="0"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference segLengthSlider},
                              Path=Value,
                              StringFormat='Segment Length = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="deviationSlider"
                Grid.Row="2"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference deviationSlider},
                              Path=Value,
                              StringFormat='Deviation = {0:F0}'}"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Il `PaintSurface` gestore nel [ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) file code-behind viene chiamato ogni volta che un `Slider` modificato il valore. Viene chiamato `SKPathEffect.CreateDiscrete` usando i due `Slider` valori e lo usa per disegnare un rettangolo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float segLength = (float)segLengthSlider.Value;
    float deviation = (float)deviationSlider.Value;

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.StrokeWidth = 5;
        paint.Color = SKColors.Blue;

        using (SKPathEffect pathEffect = SKPathEffect.CreateDiscrete(segLength, deviation))
        {
            paint.PathEffect = pathEffect;

            SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

È possibile usare questo effetto per il riempimento, nel qual caso la struttura dell'area riempita è soggetto a queste deviazioni casuale. Il **instabilità testo** pagina viene illustrato come utilizzare questo effetto percorso per visualizzare il testo. La maggior parte del codice nel `PaintSurface` gestore del [ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) classe dedicata al ridimensionamento e centrare il testo:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "FUZZY";

    using (SKPaint textPaint = new SKPaint())
    {
        textPaint.Color = SKColors.Purple;
        textPaint.PathEffect = SKPathEffect.CreateDiscrete(3f, 10f);

        // Adjust TextSize property so text is 95% of screen width
        float textWidth = textPaint.MeasureText(text);
        textPaint.TextSize *= 0.95f * info.Width / textWidth;

        // Find the text bounds
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

Qui è in esecuzione in modalità orizzontale in tutti e tre le piattaforme:

[![](effects-images/jittertext-small.png "Screenshot della pagina di instabilità testo è pari al triplo")](effects-images/jittertext-large.png#lightbox "Triple screenshot of the JitterText page")

## <a name="path-outlining"></a>La struttura di percorso

Abbiamo già visto due esempi su little il [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/) metodo `SKPaint`, che esiste anche in un [overload](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/):

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale)
```

Sono necessari solo i primi due argomenti. Il metodo accede il percorso fa riferimento il `src` argomento, modifica i dati del percorso basati le proprietà relative al tratto nel `SKPaint` oggetto (inclusi i `PathEffect` proprietà) e quindi scrive i risultati nel `dst` percorso. Il `resScale` parametro consente di ridurre la precisione per creare un percorso di destinazione più piccolo e il `cullRect` argomento consente di eliminare i contorni all'esterno di un rettangolo.

Un uso di base di questo metodo non comporta effetti per il tracciato affatto. Se il `SKPaint` oggetto ha relativo `Style` impostata su `SKPaintStyle.Stroke`ed esegue *non* hanno relativo `PathEffect` impostare, quindi `GetFillPath` viene creato un percorso che rappresenta un *struttura*del percorso di origine come se fosse stato tracciato dalle proprietà di disegno.

Ad esempio, se il `src` il percorso è un cerchio semplice del raggio 500 e il `SKPaint` oggetto specifica uno spessore pari a 100, il `dst` percorso diventa due cerchi concentrici, uno con un raggio di 450 e l'altro con un raggio di 550. Il metodo viene chiamato `GetFillPath` poiché il riempimento si `dst` percorso è lo stesso come che traccia il `src` percorso. Ma è anche possibile tracciare il `dst` percorso per verificare i contorni di percorso.

Il **toccare alla struttura di percorso** viene illustrata questa evenienza. Il `SKCanvasView` e `TapGestureRecognizer` viene creata un'istanza di [ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) file. Il [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) file code-behind definisce tre `SKPaint` oggetti come campi, due per applicare una traccia con tracciare larghezze pari a 100 e 20 e il terzo per il riempimento:

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    bool outlineThePath = false;

    SKPaint redThickStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 100
    };

    SKPaint redThinStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 20
    };

    SKPaint blueFill = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    public TapToOutlineThePathPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewTapped(object sender, EventArgs args)
    {
        outlineThePath ^= true;
        (sender as SKCanvasView).InvalidateSurface();
    }
    ...
}
```

Se la schermata non è stata toccata, il `PaintSurface` gestore utilizza la `blueFill` e `redThickStroke` disegnare gli oggetti per il rendering di un percorso circolare:

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circlePath = new SKPath())
        {
            circlePath.AddCircle(info.Width / 2, info.Height / 2,
                                 Math.Min(info.Width / 2, info.Height / 2) -
                                 redThickStroke.StrokeWidth);

            if (!outlineThePath)
            {
                canvas.DrawPath(circlePath, blueFill);
                canvas.DrawPath(circlePath, redThickStroke);
            }
            else
            {
                using (SKPath outlinePath = new SKPath())
                {
                    redThickStroke.GetFillPath(circlePath, outlinePath);

                    canvas.DrawPath(outlinePath, blueFill);
                    canvas.DrawPath(outlinePath, redThinStroke);
                }
            }
        }
    }
}
```

Il cerchio viene riempito e tracciato come previsto:

[![](effects-images/taptooutlinethepathnormal-small.png "Tripla screenshot della pagina toccare per il percorso della struttura normali")](effects-images/taptooutlinethepathnormal-large.png#lightbox "tripla screenshot della pagina toccare per il percorso della struttura normale")

Quando si tocca la schermata `outlineThePath` è impostata su `true`e il `PaintSurface` gestore consente di creare una nuova `SKPath` dell'oggetto e lo usa come percorso di destinazione in una chiamata a `GetFillPath` sul `redThickStroke` oggetto paint. Tale percorso di destinazione viene quindi compilato e tracciato con `redThinStroke`, risultante in quanto segue:

[![](effects-images/taptooutlinethepathoutlined-small.png "Tripla screenshot della pagina toccare per il percorso della struttura contorno")](effects-images/taptooutlinethepathoutlined-large.png#lightbox "tripla screenshot della pagina toccare per il percorso della struttura contorno")

Due cerchi rossi indicano chiaramente che il percorso circolare originale è stato convertito in due distribuzioni del circolare.

Questo metodo può essere molto utile per lo sviluppo di percorsi da usare per il `SKPathEffect.Create1DPath` (metodo). I percorsi specificati in questi metodi vengono sempre compilati quando i percorsi vengono replicati. Se non si desidera l'intero percorso deve essere compilato, è necessario definire con attenzione i contorni.

Ad esempio, nelle **catena collegato** esempio, i collegamenti sono stati definiti con una serie di quattro archi, ogni coppia di cui sono basati su due raggi descrive l'area del tracciato da riempire. È possibile sostituire il codice nel `LinkedChainPage` classe farlo in modo leggermente diverso.

In primo luogo, è opportuno ridefinire la `linkRadius` costante:

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

Il `linkPath` è ora solo due archi basati su tale singola radius, con il valore desiderato iniziare angoli e durante lo sweep degli angoli:

```csharp
using (SKPath linkPath = new SKPath())
{
    SKRect rect = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
    linkPath.AddArc(rect, 55, 160);
    linkPath.AddArc(rect, 235, 160);

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = linkThickness;

        using (SKPath outlinePath = new SKPath())
        {
            strokePaint.GetFillPath(linkPath, outlinePath);

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(outlinePath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);

        }

    }
}
```

Il `outlinePath` oggetto viene quindi il destinatario del contorno del `linkPath` quando tracciata con le proprietà specificate nel `strokePaint`.

Un altro esempio di utilizzo di questa tecnica in arrivo per il percorso usato in un `SKPathEffect.Create2DPath` metodi.

## <a name="combining-path-effects"></a>Combinando gli effetti di percorso

I due metodi di creazione statica finale di `SKPathEffect` vengono [ `SKPathEffect.CreateSum` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateSum/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/) e [ `SKPathEffect.CreateCompose` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCompose/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Entrambi i metodi di combinano due effetti per il tracciato per creare un effetto del percorso composto. Il `CreateSum` metodo crea un effetto di percorso simile agli effetti di due percorsi applicati separatamente, mentre `CreateCompose` si applica un effetto di percorso (il `inner`) e quindi applica il `outer` a quello.

Abbiamo già visto come il `GetFillPath` metodo di `SKPaint` può convertire un percorso a un altro percorso in base `SKPaint` delle proprietà (inclusi `PathEffect`) in modo che non deve essere *troppo* misteriosi come un `SKPaint`oggetto è possibile eseguire questa operazione due volte con gli effetti di due percorsi specificati nel `CreateSum` o `CreateCompose` metodi.

Un uso ovvio del `CreateSum` consiste nel definire un `SKPaint` che riempie un percorso con effetto di un percorso e i tratti il percorso con effetto di un altro percorso. Questa funzionalità viene illustrata la **gatti nel Frame** un esempio che visualizza una matrice di gatti all'interno di un frame con bordi ondulati:

[![](effects-images/catsinframe-small.png "Tripla screenshot della pagina gatti In Frame")](effects-images/catsinframe-large.png#lightbox "tripla screenshot della pagina gatti In Frame")

Il [ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) classe inizia con la definizione dei campi diversi. È possibile che si riconoscano il primo campo dal [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe la [ **dati del tracciato SVG** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) articolo. Il secondo percorso si basa su una riga e un arco per il modello cresta del frame:

```csharp
public class CatsInFramePage : ContentPage
{
    // From PathDataCatPage.cs
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint catStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5
    };

    SKPath scallopPath =
        SKPath.ParseSvgPathData("M 0 0 L 50 0 A 60 60 0 0 1 -50 0 Z");

    SKPaint framePaint = new SKPaint
    {
        Color = SKColors.Black
    };
    ...
}
```

Il `catPath` potrebbero essere utilizzate nel `SKPathEffect.Create2DPath` metodo se il `SKPaint` oggetto `Style` viene impostata su `Stroke`. Tuttavia, se il `catPath` viene utilizzato direttamente in questo programma, quindi l'intera testa il team cat verrà compilato e baffi non saranno ancora visibili. (Provalo!) È necessario ottenere la struttura del percorso e usare tale profilo nel `SKPathEffect.Create2DPath` (metodo).

Il costruttore esegue questo processo. Viene applicata innanzitutto due trasformazioni `catPath` per spostare il (0, 0) posizionato al centro e ridurre le dimensioni. `GetFillPath` Ottiene tutti i contorni dei contorni in `outlinedCatPath`, e tale oggetto viene utilizzato il `SKPathEffect.Create2DPath` chiamare. Il ridimensionamento interagisce con il `SKMatrix` valore sono leggermente maggiori orizzontale e dimensione verticale del team cat per fornire un buffer minimo tra i riquadri, mentre i fattori di traduzione erano derivato in qualche modo in modo empirico in modo che un gatto completo è visibile di Nell'angolo superiore sinistro del frame:

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    public CatsInFramePage()
    {
        Title = "Cats in Frame";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Move (0, 0) point to center of cat path
        catPath.Transform(SKMatrix.MakeTranslation(-240, -175));

        // Now catPath is 400 by 250
        // Scale it down to 160 by 100
        catPath.Transform(SKMatrix.MakeScale(0.40f, 0.40f));

        // Get the outlines of the contours of the cat path
        SKPath outlinedCatPath = new SKPath();
        catStroke.GetFillPath(catPath, outlinedCatPath);

        // Create a 2D path effect from those outlines
        SKPathEffect fillEffect = SKPathEffect.Create2DPath(
            new SKMatrix { ScaleX = 170, ScaleY = 110,
                           TransX = 75, TransY = 80,
                           Persp2 = 1 },
            outlinedCatPath);

        // Create a 1D path effect from the scallop path
        SKPathEffect strokeEffect =
            SKPathEffect.Create1DPath(scallopPath, 75, 0, SKPath1DPathEffectStyle.Rotate);

        // Set the sum the effects to frame paint
        framePaint.PathEffect = SKPathEffect.CreateSum(fillEffect, strokeEffect);
    }
    ...
}
```

Il costruttore chiama quindi `SKPathEffect.Create1DPath` per il frame ondulato. Si noti che la larghezza del percorso è uguale a 100 pixel, ma l'avanzamento è 75 pixel in modo che il percorso di replicato si sovrapposta intorno al frame. L'istruzione finale delle chiamate di costruttore `SKPathEffect.CreateSum` combinare gli effetti di due percorsi e impostare il risultato di `SKPaint` oggetto.

Tutto questo lavoro consente il `PaintSurface` gestore di è molto semplice. È necessario solo definire un rettangolo e disegnare usando `framePaint`:

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect rect = new SKRect(50, 50, info.Width - 50, info.Height - 50);
        canvas.ClipRect(rect);
        canvas.DrawRect(rect, framePaint);
    }
}
```

Gli algoritmi dietro gli effetti di percorso causano sempre l'intero percorso utilizzato per il riempimento o che traccia da visualizzare, che può causare alcuni oggetti visivi da visualizzare all'esterno del rettangolo. Il `ClipRect` chiamare prima il `DrawRect` chiamata consente gli oggetti visivi per essere notevolmente più chiara. (Disponibile senza ritaglio!)

Accade spesso di usare `SKPathEffect.CreateCompose` per aggiungere una certa instabilità a un altro effetto di percorso. È certamente possibile sperimentare autonomamente, ma di seguito è riportato un esempio leggermente diverso:

Il **tratteggiate tratteggio** riempie un'ellisse con linee tratteggiate che sono linee tratteggiate. La maggior parte delle operazioni nel [ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) classe viene eseguita direttamente nelle definizioni di campo. Questi campi definiscono un effetto di dash e un effetto di tratteggio. Sono definiti come `static` perché vi viene quindi fatto riferimento un `SKPathEffect.CreateCompose` chiamare nel `SKPaint` definizione:

```csharp
public class DashedHatchLinesPage : ContentPage
{
    static SKPathEffect dashEffect =
        SKPathEffect.CreateDash(new float[] { 30, 30 }, 0);

    static SKPathEffect hatchEffect = SKPathEffect.Create2DLine(20,
        Multiply(SKMatrix.MakeScale(60, 60),
                 SKMatrix.MakeRotationDegrees(45)));

    SKPaint paint = new SKPaint()
    {
        PathEffect = SKPathEffect.CreateCompose(dashEffect, hatchEffect),
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

Il `PaintSurface` dover contengono solo l'overhead standard oltre a una sola chiamata al gestore `DrawOval`:

```csharp
public class DashedHatchLinesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawOval(info.Width / 2, info.Height / 2,
                        0.45f * info.Width, 0.45f * info.Height,
                        paint);
    }
    ...
}
```

Come già stato individuato, le linee tratteggiate non sono con precisione limitate all'interno dell'area e in questo esempio, iniziano sempre a sinistra con un trattino intero:

[![](effects-images/dashedhatchlines-small.png "Tripla screenshot della pagina delle linee tratteggiate linee tratteggiate")](effects-images/dashedhatchlines-large.png#lightbox "tripla screenshot della pagina delle linee tratteggiate linee tratteggiate")

Ora che si sono visto gli effetti di percorso che spaziano da semplici punti e trattini per combinazioni di strano, usare la tua immaginazione e vedere cosa è possibile creare.



## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
