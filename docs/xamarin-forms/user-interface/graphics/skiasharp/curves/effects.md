---
title: "effetti percorso in SkiaSharp" Descrizione: "in questo articolo vengono illustrati i vari effetti del percorso SkiaSharp che consentono di utilizzare i percorsi per la tracciatura e il riempimento e ne viene illustrato il codice di esempio".
ms. prod: Novell MS. Technology: Novell-skiasharp ms. AssetID: 95167D1F-A718-405A-AFCC-90E596D422F3 autore: davidbritch ms. Author: dabritch ms. Date: 07/29/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="path-effects-in-skiasharp"></a>Effetti percorso in SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Individuare i vari effetti del percorso che consentono di usare i percorsi per la tracciatura e il riempimento_

Un *effetto del percorso* è un'istanza della [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) classe creata con uno degli otto metodi di creazione statici definiti dalla classe. L' `SKPathEffect` oggetto viene quindi impostato sulla [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) proprietà di un [`SKPaint`](xref:SkiaSharp.SKPaint) oggetto per diversi effetti interessanti, ad esempio, per tracciare una linea con un percorso replicato di piccole dimensioni:

![Esempio di catena collegata](effects-images/patheffectsample.png)

Gli effetti del percorso consentono di:

- Tracciare una linea con punti e trattini
- Traccia una riga con qualsiasi percorso compilato
- Riempire un'area con linee tratteggiate
- Riempire un'area con un tracciato affiancato
- Arrotonda angoli acuti
- Aggiungere "jitter" casuale a linee e curve

Inoltre, è possibile combinare due o più effetti del percorso.

Questo articolo illustra anche come usare il [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) metodo di `SKPaint` per convertire un percorso in un altro percorso applicando le proprietà di `SKPaint` , tra cui `StrokeWidth` e `PathEffect` . Ciò comporta alcune tecniche interessanti, ad esempio il recupero di un percorso che rappresenta un contorno di un altro percorso. `GetFillPath`è utile anche in relazione agli effetti del percorso.

## <a name="dots-and-dashes"></a>Punti e trattini

L'uso del [`PathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) metodo è stato descritto nell'articolo [**punti e trattini**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md). Il primo argomento del metodo è una matrice contenente un numero pari di due o più valori, alternando tra le lunghezze dei trattini e le lunghezze dei gap tra i trattini:

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Questi valori *non* sono relativi alla larghezza del tratto. Se, ad esempio, la larghezza del tratto è 10 e si desidera una linea composta da trattini quadrati e Gap quadrati, impostare la `intervals` matrice su {10, 10}. L' `phase` argomento indica il punto in cui inizia la riga all'interno del modello di tratteggio. In questo esempio, se si desidera che la riga inizi con il gap quadrato, impostare `phase` su 10.

Le entità finali dei trattini sono interessate dalla `StrokeCap` proprietà di `SKPaint` . Per larghezze di tratto Wide, è molto comune impostare questa proprietà su `SKStrokeCap.Round` per arrotondare le estremità dei trattini. In questo caso, i valori nella `intervals` matrice *non* includono la lunghezza aggiuntiva risultante dall'arrotondamento. Questo fatto significa che un punto circolare richiede la specifica di una larghezza pari a zero. Per la larghezza del tratto di 10, per creare una linea con punti e spazi vuoti tra i punti dello stesso diametro, utilizzare una `intervals` matrice di {0, 20}.

La pagina di **testo punteggiato animato** è simile alla pagina di **testo** descritta nell'articolo integrazione di [**testo e grafica**](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) perché Visualizza caratteri di testo delineati impostando la `Style` proprietà dell' `SKPaint` oggetto su `SKPaintStyle.Stroke` . Inoltre, il **testo punteggiato animato** USA `SKPathEffect.CreateDash` per assegnare a questo contorno un aspetto tratteggiato, mentre il programma aggiunge un'animazione all' `phase` argomento del `SKPathEffect.CreateDash` metodo per far sembrare che i punti siano in viaggio attorno ai caratteri di testo. Ecco la pagina in modalità orizzontale:

[![Schermata tripla della pagina di testo tratteggiata animata](effects-images/animateddottedtext-small.png)](effects-images/animateddottedtext-large.png#lightbox)

La [`AnimatedDottedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe inizia definendo alcune costanti e sostituisce anche i `OnAppearing` `OnDisappearing` metodi e per l'animazione:

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

Il `PaintSurface` gestore inizia creando un `SKPaint` oggetto per visualizzare il testo. La `TextSize` proprietà viene modificata in base alla larghezza dello schermo:

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

Verso la fine del metodo, il `SKPathEffect.CreateDash` metodo viene chiamato usando l'oggetto `dashArray` definito come campo e il valore animato `phase` . L' `SKPathEffect` istanza viene impostata sulla `PathEffect` proprietà dell' `SKPaint` oggetto per visualizzare il testo.

In alternativa, è possibile impostare l' `SKPathEffect` oggetto sull' `SKPaint` oggetto prima di misurare il testo e centrarlo sulla pagina. In tal caso, tuttavia, i punti e i trattini animati causano una variazione della dimensione del testo sottoposto a rendering e il testo tende a vibrare leggermente. (Prova!)

Si noterà anche che, come il cerchio dei punti animati intorno ai caratteri di testo, c'è un certo punto in ogni curva chiusa in cui i punti sembrano entrare ed uscire dall'esistenza. Questo è il punto in cui il tracciato che definisce la struttura dei caratteri inizia e termina. Se la lunghezza del percorso non è un multiplo integrale della lunghezza del motivo del tratteggio (in questo caso 20 pixel), solo una parte di tale modello può adattarsi alla fine del percorso.

È possibile regolare la lunghezza del motivo del tratteggio per adattarla alla lunghezza del tracciato, ma che richiede la determinazione della lunghezza del percorso, una tecnica analizzata nelle [**informazioni sul percorso e nell'enumerazione**](information.md)degli articoli.

Il programma di **morphing Dot/Dash** aggiunge un'animazione allo schema di tratteggio, in modo che i trattini dividino i punti, che si combinano di nuovo per formare i trattini:

[![Schermata tripla della pagina morph del punto Dash](effects-images/dotdashmorph-small.png)](effects-images/dotdashmorph-large.png#lightbox)

La [`DotDashMorphPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe esegue l'override dei `OnAppearing` `OnDisappearing` metodi e come nel programma precedente, ma la classe definisce l' `SKPaint` oggetto come un campo:

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

Il `PaintSurface` gestore crea un percorso ellittico basato sulle dimensioni della pagina ed esegue una sezione di codice lungo che imposta le `dashArray` `phase` variabili e. Poiché la variabile animata `t` è compresa tra 0 e 1, i blocchi vengono suddivisi `if` in quattro trimestri e, in ognuno di questi trimestri, viene `tsub` anche compreso tra 0 e 1. Al termine, il programma crea l' `SKPathEffect` oggetto e lo imposta sull' `SKPaint` oggetto per il disegno.

## <a name="from-path-to-path"></a>Da percorso a percorso

Il [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single)) metodo di `SKPaint` converte un percorso in un altro in base alle impostazioni dell' `SKPaint` oggetto. Per verificarne il funzionamento, sostituire la `canvas.DrawPath` chiamata nel programma precedente con il codice seguente:

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

In questo nuovo codice, la `GetFillPath` chiamata converte `ellipsePath` (che è semplicemente un ovale) in `newPath` , che viene quindi visualizzato con `newPaint` . L' `newPaint` oggetto viene creato con tutte le impostazioni predefinite delle proprietà, ad eccezione del fatto che la `Style` proprietà è impostata in base al valore booleano restituito da `GetFillPath` .

Gli oggetti visivi sono identici, ad eccezione del colore, che è impostato `ellipsePaint` ma non `newPaint` . Anziché l'ellisse semplice definita in `ellipsePath` , `newPath` contiene numerosi contorni di percorso che definiscono la serie di punti e trattini. Questo è il risultato dell'applicazione di varie proprietà di `ellipsePaint` (in particolare,, `StrokeWidth` `StrokeCap` e `PathEffect` ) a `ellipsePath` e a inserire il percorso risultante in `newPath` . Il `GetFillPath` metodo restituisce un valore booleano che indica se il percorso di destinazione deve essere compilato. in questo esempio, il valore restituito è `true` per riempire il percorso.

Provare a modificare l' `Style` impostazione in in `newPaint` modo da `SKPaintStyle.Stroke` visualizzare i singoli contorni di percorso delineati con una riga con una lunghezza di un pixel.

## <a name="stroking-with-a-path"></a>Tracciatura con un tracciato

Il [`SKPathEffect.Create1DPath`](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle)) metodo è concettualmente simile a `SKPathEffect.CreateDash` , ad eccezione del fatto che si specifica un percorso piuttosto che uno schema di trattini e spazi vuoti. Questo percorso viene replicato più volte per tracciare la linea o la curva.

La sintassi è:

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

In generale, il percorso passato a `Create1DPath` sarà ridotto e centrato intorno al punto (0, 0). Il `advance` parametro indica la distanza tra i centri del percorso in cui viene replicato il percorso sulla riga. Questo argomento viene in genere impostato sulla larghezza approssimativa del percorso. L' `phase` argomento svolge lo stesso ruolo nel `CreateDash` metodo.

Il [`SKPath1DPathEffectStyle`](xref:SkiaSharp.SKPath1DPathEffectStyle) dispone di tre membri:

- `Translate`
- `Rotate`
- `Morph`

Il `Translate` membro fa sì che il percorso rimanga nello stesso orientamento in cui viene replicato lungo una linea o una curva. Per `Rotate` , il percorso viene ruotato in base a una tangente alla curva. Il percorso ha il normale orientamento per le linee orizzontali. `Morph`è simile a, ad `Rotate` eccezione del fatto che anche il percorso è curvo in modo da corrispondere alla curvatura della linea tracciata.

Nella pagina dell' **effetto del percorso 1D** sono illustrate queste tre opzioni. Il file [**OneDimensionalPathEffectPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) definisce una selezione contenente tre elementi corrispondenti ai tre membri dell'enumerazione:

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
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Translate</x:String>
                    <x:String>Rotate</x:String>
                    <x:String>Morph</x:String>
                </x:Array>
            </Picker.ItemsSource>
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

Il file code-behind [**OneDimensionalPathEffectPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) definisce tre `SKPathEffect` oggetti come campi. Tutti questi vengono creati usando `SKPathEffect.Create1DPath` con `SKPath` gli oggetti creati usando `SKPath.ParseSvgPathData` . Il primo è una semplice casella, il secondo è una forma a rombo e il terzo è un rettangolo. Questi vengono usati per illustrare i tre stili degli effetti:

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

            switch ((string)effectStylePicker.SelectedItem))
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

Il `PaintSurface` gestore crea una curva di Bézier che esegue il ciclo e accede alla selezione per determinare quale `PathEffect` deve essere usato per tracciare l'oggetto. Le tre opzioni `Translate` ,, `Rotate` e, `Morph` sono visualizzate da sinistra a destra:

[![Schermata tripla della pagina effetto percorso 1D](effects-images/1dpatheffect-small.png)](effects-images/1dpatheffect-large.png#lightbox)

Il percorso specificato nel `SKPathEffect.Create1DPath` metodo viene sempre riempito. Il percorso specificato nel `DrawPath` metodo viene sempre tracciato se la proprietà dell' `SKPaint` oggetto è `PathEffect` impostata su un effetto del percorso 1D. Si noti che l' `pathPaint` oggetto non dispone `Style` di alcuna impostazione, il cui valore predefinito `Fill` è, ma il percorso viene tracciato indipendentemente.

La casella utilizzata nell' `Translate` esempio è il quadrato di 20 pixel e l' `advance` argomento è impostato su 24. Questa differenza causa un divario tra le caselle quando la linea è approssimativamente orizzontale o verticale, ma le caselle si sovrappongono leggermente quando la linea è diagonale perché la diagonale della casella è 28,3 pixel.

Anche la forma a rombo nell' `Rotate` esempio è di 20 pixel di larghezza. `advance`È impostato su 20, in modo che i punti continuino a essere toccati quando il rombo viene ruotato insieme alla curvatura della linea.

La forma rettangolare nell' `Morph` esempio è di 50 pixel in larghezza con un' `advance` impostazione di 55 per creare un piccolo divario tra i rettangoli Man mano che si piegano attorno alla curva di Bézier.

Se l' `advance` argomento è minore della dimensione del percorso, i percorsi replicati possono sovrapporsi. Questo può causare effetti interessanti. La pagina **catena collegata** Visualizza una serie di cerchi sovrapposti che sembrano simili a una catena collegata, che si blocca nella forma distintiva di una catenaria:

[![Schermata tripla della pagina a catena collegata](effects-images/linkedchain-small.png)](effects-images/linkedchain-large.png#lightbox)

Sembra molto vicino. noterete che questi non sono in realtà cerchi. Ogni collegamento nella catena è due archi, ridimensionati e posizionati in modo che sembrino connettersi con collegamenti adiacenti.

Una catena o un cavo della distribuzione uniforme del peso si blocca sotto forma di catenaria. Un Arch costruito sotto forma di una catenaria invertita trae vantaggio dalla distribuzione equa della pressione rispetto al peso di un archetto. La catenaria ha una descrizione matematica apparentemente semplice:

`y = a · cosh(x / a)`

*Cosh* è la funzione coseno iperbolica. Per *x* uguale a 0, *cosh* è zero e *y* è uguale *a*. Questo è il centro della catenaria. Analogamente alla funzione *coseno* , *cosh* viene definito pari *, il*che significa che *cosh (– x)* è uguale a *cosh (x)* e i valori aumentano per gli argomenti positivi o negativi che aumentano. Questi valori descrivono le curve che formano i lati della catenaria.

Individuare il valore appropriato di *un oggetto* per adattarlo alle dimensioni della pagina del telefono non è un calcolo diretto. Se *w* e *h* sono la larghezza e l'altezza di un rettangolo, il valore ottimale di *un oggetto* soddisfa l'equazione seguente:

`cosh(w / 2 / a) = 1 + h / a`

Il metodo seguente nella [`LinkedChainPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) classe incorpora tale uguaglianza facendo riferimento alle due espressioni a sinistra e a destra del segno di uguale come `left` e `right` . Per valori di dimensioni ridotte di *un*, `left` è maggiore di `right` ; per *a*i valori di grandi dimensioni, `left` è minore di `right` . Il `while` ciclo si restringe in su un valore ottimale di *a*:

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

L' `SKPath` oggetto per i collegamenti viene creato nel costruttore della classe e l' `SKPathEffect` oggetto risultante viene quindi impostato sulla `PathEffect` proprietà dell' `SKPaint` oggetto archiviato come campo:

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

Il compito principale del `PaintSurface` gestore è creare un percorso per la catenaria. Dopo aver determinato l' *oggetto* ottimale e averlo archiviato nella `optA` variabile, è necessario calcolare anche un offset dalla parte superiore della finestra. Quindi, può accumulare una raccolta di `SKPoint` valori per la catenaria, trasformarla in un tracciato e tracciare il percorso con l'oggetto creato in precedenza `SKPaint` :

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

Questo programma definisce il percorso usato in `Create1DPath` per avere il punto (0,0) al centro. Sembra ragionevole perché il punto (0,0) del percorso è allineato alla linea o alla curva che sta decorando. Tuttavia, è possibile usare un punto non centrato (0, 0) per alcuni effetti speciali.

La pagina **nastro trasportatore** crea un percorso simile a un nastro trasportatore oblungo con una parte superiore e inferiore curva, ridimensionata in base alle dimensioni della finestra. Tale percorso viene tracciato con un oggetto semplice di `SKPaint` 20 pixel di larghezza e colorato grigio, quindi tratteggiato nuovamente con un altro `SKPaint` oggetto con un oggetto che fa `SKPathEffect` riferimento a un percorso simile a un piccolo bucket:

[![Schermata tripla della pagina del nastro trasportatore](effects-images/conveyorbelt-small.png)](effects-images/conveyorbelt-large.png#lightbox)

Il punto (0,0) del percorso del bucket è l'handle. Pertanto, quando l' `phase` argomento viene animato, i bucket sembrano ruotare intorno al nastro trasportatore, forse raccogliendo acqua nella parte inferiore e facendone il dump nella parte superiore.

La [`ConveyorBeltPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) classe implementa l'animazione con le sostituzioni dei `OnAppearing` `OnDisappearing` metodi e. Il percorso per il bucket è definito nel costruttore della pagina:

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

Il codice di creazione del bucket viene completato con due trasformazioni che rendono il bucket leggermente più grande e lo rivolgono lateralmente. L'applicazione di queste trasformazioni è stata più semplice rispetto alla regolazione di tutte le coordinate del codice precedente.

Il `PaintSurface` gestore inizia definendo un percorso per il nastro trasportatore stesso. Si tratta semplicemente di una coppia di linee e di una coppia di semicerchi disegnata con una linea grigia scura di 20 pixel:

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

La logica per disegnare il nastro trasportatore non funziona in modalità orizzontale.

I bucket devono essere distanziati di circa 200 pixel di distanza sul nastro del trasportatore. Tuttavia, il nastro del trasportatore probabilmente non è un multiplo di 200 pixel, il che significa che, quando l' `phase` argomento di `SKPathEffect.Create1DPath` viene animato, i bucket si estendono e non esistono.

Per questo motivo, il programma calcola innanzitutto un valore denominato `length` che corrisponde alla lunghezza del nastro del trasportatore. Poiché il nastro trasportatore è costituito da linee rette e semi-cerchi, si tratta di un calcolo semplice. Il numero di bucket viene quindi calcolato dividendo `length` per 200. Viene arrotondato all'intero più vicino e tale numero viene quindi diviso in `length` . Il risultato è una spaziatura per un numero integrale di bucket. L' `phase` argomento è semplicemente una frazione di tale oggetto.

## <a name="from-path-to-path-again"></a>Da percorso a percorso di nuovo

Nella parte inferiore del `DrawSurface` gestore, impostare **Conveyor Belt**come commento la `canvas.DrawPath` chiamata e sostituirla con il codice seguente:

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Come per l'esempio precedente di `GetFillPath` , si noterà che i risultati sono gli stessi, ad eccezione del colore. Dopo l'esecuzione `GetFillPath` , l' `newPath` oggetto contiene più copie del percorso del bucket, ognuna posizionata nello stesso punto in cui l'animazione è stata posizionata al momento della chiamata.

## <a name="hatching-an-area"></a>Tratteggio di un'area

Il [`SKPathEffect.Create2DLines`](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix)) metodo riempie un'area con linee parallele, spesso denominate *linee tratteggiate*. Il metodo presenta la sintassi seguente:

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

L' `width` argomento specifica la larghezza del tratto delle linee tratteggiate. Il `matrix` parametro è una combinazione di ridimensionamento e rotazione facoltativa. Il fattore di scala indica l'incremento di pixel utilizzato da Skia per spaziare le linee tratteggiate. La separazione tra le linee è il fattore di scala meno l' `width` argomento. Se il fattore di scala è minore o uguale al `width` valore, non sarà presente alcuno spazio tra le linee tratteggiate e l'area verrà compilata. Specificare lo stesso valore per la scalabilità orizzontale e verticale.

Per impostazione predefinita, le linee tratteggiate sono orizzontali. Se il `matrix` parametro contiene la rotazione, le linee tratteggiate vengono ruotate in senso orario.

La pagina di **riempimento del tratteggio** illustra questo effetto del percorso. La [`HatchFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) classe definisce tre effetti del percorso come campi, il primo per le linee tratteggiate orizzontali con una larghezza di 3 pixel con un fattore di scala che indica che sono spaziati a distanza di 6 pixel. La separazione tra le righe è pertanto di tre pixel. Il secondo effetto del percorso è per le linee di tratteggio verticali con una larghezza di sei pixel con spazi a distanza di 24 pixel, quindi la separazione è di 18 pixel e la terza è per le linee di tratteggio diagonali a 12 pixel di larghezza larga 36 pixel.

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

Si noti il `Multiply` Metodo Matrix. Poiché i fattori di scala orizzontale e verticale sono uguali, l'ordine in cui vengono moltiplicate le matrici di ridimensionamento e rotazione non è rilevante.

Il `PaintSurface` gestore utilizza questi tre effetti percorso con tre colori diversi in combinazione con `fillPaint` per riempire un rettangolo arrotondato per adattarlo alla pagina. La `Style` proprietà impostata su `fillPaint` viene ignorata; quando l' `SKPaint` oggetto include un effetto tracciato creato da `SKPathEffect.Create2DLine` , l'area viene riempita indipendentemente da:

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

Se si osservano con attenzione i risultati, si noterà che le linee tratteggiate rosse e blu non sono confinate esattamente nel rettangolo arrotondato. (Si tratta apparentemente di una caratteristica del codice Skia sottostante). Se questo non è soddisfacente, viene illustrato un approccio alternativo per le linee tratteggiate diagonali in verde: il rettangolo arrotondato viene utilizzato come tracciato di ritaglio e le linee tratteggiate vengono disegnate sull'intera pagina.

Il `PaintSurface` gestore termina con una chiamata per tracciare semplicemente il rettangolo arrotondato, in modo che sia possibile visualizzare la discrepanza con le linee tratteggiate rosse e blu:

[![Schermata tripla della pagina di riempimento del tratteggio](effects-images/hatchfill-small.png)](effects-images/hatchfill-large.png#lightbox)

La schermata Android non ha un aspetto simile al seguente: il ridimensionamento dello screenshot ha causato il consolidamento delle linee rosse sottili e degli spazi sottili in linee rosse apparentemente più ampie e spazi più ampi.

## <a name="filling-with-a-path"></a>Riempimento con un percorso

[`SKPathEffect.Create2DPath`](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath))Consente di riempire un'area con un percorso replicato orizzontalmente e verticalmente, in effetti affiancando l'area:

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

I `SKMatrix` fattori di scala indicano la spaziatura orizzontale e verticale del percorso replicato. Tuttavia, non è possibile ruotare il percorso utilizzando questo `matrix` argomento; se si desidera ruotare il tracciato, ruotare il percorso stesso utilizzando il `Transform` metodo definito da `SKPath` .

Il percorso replicato è in genere allineato con i bordi sinistro e superiore dello schermo anziché l'area da riempire. È possibile eseguire l'override di questo comportamento fornendo fattori di traduzione compresi tra 0 e i fattori di scalabilità per specificare offset orizzontali e verticali dai lati sinistro e superiore.

La pagina di **riempimento del riquadro del percorso** illustra questo effetto del percorso. Il percorso utilizzato per affiancare l'area è definito come campo nella [`PathTileFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) classe. Le coordinate orizzontali e verticali variano da – 40 a 40, il che significa che questo percorso è 80 pixel quadrato:

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

Nel `PaintSurface` gestore, le chiamate impostano `SKPathEffect.Create2DPath` la spaziatura orizzontale e verticale su 64 per compromettere la sovrapposizione dei riquadri di 80 pixel. Fortunatamente, il percorso è simile a un pezzo di rompicapo, con la stessa trama con riquadri adiacenti:

[![Schermata tripla della pagina di riempimento del riquadro del percorso](effects-images/pathtilefill-small.png)](effects-images/pathtilefill-large.png#lightbox)

Il ridimensionamento dallo screenshot originale causa alcune distorsioni, in particolare sullo schermo Android.

Si noti che questi riquadri appaiono sempre interi e non vengono mai troncati. Nelle prime due schermate, non è evidente che l'area riempita è un rettangolo arrotondato. Se si desidera troncare questi riquadri in un'area specifica, utilizzare un tracciato di ritaglio.

Provare a impostare la `Style` proprietà dell' `SKPaint` oggetto su per `Stroke` visualizzare i singoli riquadri delineati anziché riempiti.

È anche possibile riempire un'area con una bitmap affiancata, come illustrato nell'articolo [**SkiaSharp bitmap affiancata**](../effects/shaders/bitmap-tiling.md).

## <a name="rounding-sharp-corners"></a>Arrotondamento degli angoli acuti

Il programma **ettagono arrotondato** presentato nei [**tre modi per creare un**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) articolo di arco usava un arco tangente per curvare i punti di una figura a sette lati. L' **altra pagina ettagono arrotondata** Mostra un approccio molto più semplice che usa un effetto tracciato creato dal [`SKPathEffect.CreateCorner`](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single)) Metodo:

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Anche se l'argomento singolo è denominato `radius` , è necessario impostarlo sulla metà del raggio dell'angolo desiderato. (Si tratta di una caratteristica del codice Skia sottostante).

Ecco il `PaintSurface` gestore nella [`AnotherRoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) classe:

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

È possibile utilizzare questo effetto con la delineatura o la compilazione in base alla `Style` proprietà dell' `SKPaint` oggetto. In esecuzione:

[![Schermata tripla della pagina di un'altra ettagono arrotondata](effects-images/anotherroundedheptagon-small.png)](effects-images/anotherroundedheptagon-large.png#lightbox)

Si noterà che questo ettagono arrotondato è identico al programma precedente. Se è necessario più convincente che il raggio dell'angolo sia effettivamente 100 anziché 50 specificato nella `SKPathEffect.CreateCorner` chiamata, è possibile rimuovere il commento dall'istruzione finale nel programma e osservare un cerchio 100-RADIUS sovrapposto nell'angolo.

## <a name="random-jitter"></a>Jitter casuale

A volte le linee dritte senza problemi della grafica dei computer non sono quelle desiderate e si desidera un po' di casualità. In tal caso, è opportuno provare il [`SKPathEffect.CreateDiscrete`](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32)) Metodo:

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

È possibile utilizzare questo effetto del percorso per l'operazione di delineatura o riempimento. Le linee sono separate in segmenti collegati, la cui lunghezza approssimativa è specificata da `segLength` , e si estendono in direzioni diverse. L'extent della deviazione rispetto alla riga originale viene specificato da `deviation` .

L'argomento finale è un valore di inizializzazione utilizzato per generare la sequenza pseudo-casuale utilizzata per l'effetto. L'effetto di jitter avrà un aspetto leggermente diverso per i diversi semi. L'argomento ha un valore predefinito pari a zero, che indica che l'effetto è lo stesso ogni volta che si esegue il programma. Se si desidera un'instabilità diversa ogni volta che lo schermo viene ridisegnato, è possibile impostare il valore di inizializzazione sulla `Millisecond` proprietà di un `DataTime.Now` valore (ad esempio).

La pagina dell' **esperimento di jitter** consente di sperimentare valori diversi per la tracciatura di un rettangolo:

[![Schermata tripla della pagina JitterExperiment](effects-images/jitterexperiment-small.png)](effects-images/jitterexperiment-large.png#lightbox)

Il programma è semplice. Il file [**JitterExperimentPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) crea un'istanza di due `Slider` elementi e un oggetto `SKCanvasView` :

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

Il `PaintSurface` gestore nel file code-behind [**JitterExperimentPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) viene chiamato ogni volta che un `Slider` valore viene modificato. Chiama `SKPathEffect.CreateDiscrete` usando i due `Slider` valori e lo usa per tracciare un rettangolo:

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

È possibile utilizzare questo effetto anche per compilarlo, nel qual caso il contorno dell'area riempita è soggetto a queste deviazioni casuali. Nella pagina **testo jitter** viene illustrato l'utilizzo di questo effetto percorso per visualizzare il testo. La maggior parte del codice nel `PaintSurface` gestore della [`JitterTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) classe è dedicata al ridimensionamento e al centramento del testo:

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

Qui viene eseguito in modalità orizzontale:

[![Schermata tripla della pagina JitterText](effects-images/jittertext-small.png)](effects-images/jittertext-large.png#lightbox)

## <a name="path-outlining"></a>Struttura del percorso

Sono già stati visualizzati due piccoli esempi del [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) metodo di `SKPaint` , che esistono due versioni:

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

Sono necessari solo i primi due argomenti. Il metodo accede al percorso a cui fa riferimento l' `src` argomento, modifica i dati del percorso in base alle proprietà del tratto nell' `SKPaint` oggetto (inclusa la `PathEffect` proprietà), quindi scrive i risultati nel `dst` percorso. Il `resScale` parametro consente di ridurre la precisione per creare un percorso di destinazione più piccolo e l' `cullRect` argomento può eliminare i contorni all'esterno di un rettangolo.

Un utilizzo di base di questo metodo non implica alcun effetto sul percorso: se la `SKPaint` proprietà dell'oggetto è `Style` impostata su `SKPaintStyle.Stroke` e non è *not* `PathEffect` impostato, in `GetFillPath` viene creato un percorso che rappresenta una *struttura* del percorso di origine come se fosse stato tracciato dalle proprietà del disegno.

Se, ad esempio, il `src` percorso è un semplice cerchio di radius 500 e l' `SKPaint` oggetto specifica una larghezza del tratto di 100, il `dst` percorso diventa due cerchi concentrici, uno con raggio 450 e l'altro con raggio 550. Il metodo viene chiamato `GetFillPath` perché la compilazione `dst` di questo percorso è uguale a quella del `src` tracciato. Tuttavia, è anche possibile tracciare il `dst` percorso per visualizzare i profili dei percorsi.

Il **tocco per delineare il percorso** illustra questo. `SKCanvasView` `TapGestureRecognizer` Viene creata un'istanza di e nel file [**TapToOutlineThePathPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) . Il file code-behind [**TapToOutlineThePathPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) definisce tre `SKPaint` oggetti come campi, due per le carezze con larghezze di tratto di 100 e 20 e la terza per il riempimento:

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

Se la schermata non è stata toccata, il `PaintSurface` gestore utilizza gli `blueFill` `redThickStroke` oggetti e Paint per eseguire il rendering di un percorso circolare:

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

Il cerchio viene riempito e tratteggiato come previsto:

[![Schermata tripla del rubinetto normale per delineare la pagina del percorso](effects-images/taptooutlinethepathnormal-small.png)](effects-images/taptooutlinethepathnormal-large.png#lightbox)

Quando si tocca la schermata, `outlineThePath` viene impostato su `true` e il `PaintSurface` gestore crea un nuovo `SKPath` oggetto e lo utilizza come percorso di destinazione in una chiamata a `GetFillPath` sull' `redThickStroke` oggetto Paint. Il percorso di destinazione viene quindi compilato e sottoposto a traccia `redThinStroke` , ottenendo i seguenti elementi:

[![Schermata tripla del tocco indicato per delineare la pagina del percorso](effects-images/taptooutlinethepathoutlined-small.png)](effects-images/taptooutlinethepathoutlined-large.png#lightbox)

I due cerchi rossi indicano chiaramente che il percorso circolare originale è stato convertito in due contorni circolari.

Questo metodo può essere molto utile per lo sviluppo di percorsi da usare per il `SKPathEffect.Create1DPath` metodo. I percorsi specificati in questi metodi vengono sempre riempiti quando vengono replicati i percorsi. Se non si desidera che venga compilato l'intero percorso, è necessario definire con attenzione le strutture.

Nell'esempio **Chain collegato** , ad esempio, i collegamenti sono stati definiti con una serie di quattro archi, ciascuna delle quali è basata su due raggi per delineare l'area del tracciato da riempire. È possibile sostituire il codice nella classe per eseguirlo in `LinkedChainPage` modo leggermente diverso.

Per prima cosa, è necessario ridefinire la `linkRadius` costante:

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath`Ora è solo due archi basati sul raggio singolo, con gli angoli iniziali e gli angoli di apertura desiderati:

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

L' `outlinePath` oggetto è quindi il destinatario della struttura di `linkPath` quando viene tracciato con le proprietà specificate in `strokePaint` .

Un altro esempio che usa questa tecnica è la prossima volta per il percorso usato in un metodo.

## <a name="combining-path-effects"></a>Combinazione degli effetti tracciato

I due metodi di creazione statici finali di `SKPathEffect` sono [`SKPathEffect.CreateSum`](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) e [`SKPathEffect.CreateCompose`](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) :

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Entrambi questi metodi combinano due effetti percorso per creare un effetto tracciato composito. Il `CreateSum` metodo crea un effetto del percorso simile ai due effetti del percorso applicati separatamente, mentre `CreateCompose` applica un effetto del percorso ( `inner` ) e quindi applica `outer` a tale oggetto.

Si è già visto come il `GetFillPath` metodo di `SKPaint` può convertire un percorso in un altro percorso in base alle `SKPaint` Proprietà (incluso `PathEffect` ), quindi non dovrebbe essere *troppo* misterioso come un `SKPaint` oggetto può eseguire tale operazione due volte con i due effetti percorso specificati nei `CreateSum` `CreateCompose` metodi o.

Un uso ovvio di `CreateSum` è la definizione di un `SKPaint` oggetto che riempie un percorso con un effetto del percorso e ne traccia il percorso con un altro effetto tracciato. Questa operazione è illustrata nell'esempio **Cats in frame** , che visualizza una matrice di gatti all'interno di un frame con bordi ondulati:

[![Schermata tripla dei gatti nella pagina frame](effects-images/catsinframe-small.png)](effects-images/catsinframe-large.png#lightbox)

La [`CatsInFramePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) classe inizia definendo diversi campi. È possibile riconoscere il primo campo della [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe dall'articolo [**dati del percorso SVG**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) . Il secondo percorso si basa su una linea e un arco per il modello a pettine del frame:

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

Il `catPath` metodo può essere utilizzato nel `SKPathEffect.Create2DPath` metodo se la `SKPaint` proprietà dell'oggetto `Style` è impostata su `Stroke` . Tuttavia, se `catPath` viene usato direttamente in questo programma, l'intera intestazione del gatto verrà riempita e i baffi non saranno visibili. (Prova!) È necessario ottenere la struttura di tale percorso e utilizzare tale struttura nel `SKPathEffect.Create2DPath` metodo.

Il costruttore esegue questo processo. Prima di tutto applica due trasformazioni a `catPath` per spostare il punto (0,0) al centro e ridurne le dimensioni. `GetFillPath`Ottiene tutti i contorni dei contorni in `outlinedCatPath` e tale oggetto viene utilizzato nella `SKPathEffect.Create2DPath` chiamata. I fattori di scala nel `SKMatrix` valore sono leggermente più grandi delle dimensioni orizzontali e verticali del gatto per fornire un piccolo buffer tra i riquadri, mentre i fattori di traduzione sono stati derivati in modo empirico, in modo che un gatto completo sia visibile nell'angolo superiore sinistro del frame:

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

Il costruttore chiama quindi `SKPathEffect.Create1DPath` per il frame ondulato. Si noti che la larghezza del percorso è 100 pixel, ma l'avanzamento è 75 pixel, in modo che il percorso replicato sia sovrapposto al frame. L'istruzione finale del costruttore chiama `SKPathEffect.CreateSum` per combinare i due effetti del percorso e impostare il risultato sull' `SKPaint` oggetto.

Tutto questo lavoro consente al `PaintSurface` gestore di essere abbastanza semplice. È necessario solo definire un rettangolo e utilizzarlo per `framePaint` :

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

Gli algoritmi dietro gli effetti del percorso provocano sempre l'intero percorso usato per la visualizzazione di carezze o riempimento, che può causare la visualizzazione di alcuni oggetti visivi all'esterno del rettangolo. La `ClipRect` chiamata prima della `DrawRect` chiamata consente agli oggetti visivi di essere notevolmente puliti. (Prova senza ritaglio)

È comune utilizzare `SKPathEffect.CreateCompose` per aggiungere alcune jitter a un altro effetto del percorso. È certamente possibile sperimentare autonomamente, ma ecco un esempio leggermente diverso:

Le **linee tratteggiate tratteggiate** riempiono un'ellisse con linee tratteggiate. La maggior parte delle operazioni nella [`DashedHatchLinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) classe viene eseguita direttamente nelle definizioni dei campi. Questi campi definiscono un effetto Dash e un effetto tratteggio. Sono definite come in quanto viene `static` fatto riferimento in una `SKPathEffect.CreateCompose` chiamata nella `SKPaint` definizione:

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

Il `PaintSurface` gestore deve contenere solo l'overhead standard più una chiamata a `DrawOval` :

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

Come è già stato fatto, le linee tratteggiate non sono esattamente limitate all'interno dell'area e in questo esempio iniziano sempre a sinistra con un trattino intero:

[![Schermata tripla della pagina delle linee tratteggiate tratteggiate](effects-images/dashedhatchlines-small.png)](effects-images/dashedhatchlines-large.png#lightbox)

Ora che sono stati visualizzati gli effetti del percorso che variano da punti semplici e trattini a combinazioni strane, usare la propria immaginazione e vedere cosa è possibile creare.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
