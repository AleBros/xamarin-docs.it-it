---
title: Effetti di percorso
description: Individuare i vari effetti di percorso che consentono i percorsi da utilizzare per la traccia e la compilazione
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: charlespetzold
ms.author: chape
ms.date: 07/29/2017
ms.openlocfilehash: 4097aea4079555b26b586db5ec63fa261d5e7946
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="path-effects"></a>Effetti di percorso

_Individuare i vari effetti di percorso che consentono i percorsi da utilizzare per la traccia e la compilazione_

A *effetto del percorso* è un'istanza di [ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/) classe che viene creato con uno degli otto statico `Create` metodi. Il `SKPathEffect` oggetto viene impostato il [ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/) proprietà di un `SKPaint` oggetto per un'ampia gamma di effetti interessanti, ad esempio, che traccia di una riga con un piccolo percorso replicato:

![](effects-images/patheffectsample.png "L'esempio catena collegato")

Gli effetti di percorso consentono di:

- Tracciare una linea con punti e trattini
- Tracciare una linea con qualsiasi percorso pieno
- Un'area riempita con linee tratteggiate
- Un'area riempita con un percorso affiancato
- Rendere acuto angoli arrotondati
- Aggiungere casuale "variazione" a linee e curve

Inoltre, è possibile combinare due o più effetti di percorso.

In questo articolo viene inoltre illustrato come utilizzare il `GetFillPath` metodo `SKPaint` per convertire un percorso in un altro percorso applicando le proprietà di `SKPaint`, tra cui `StrokeWidth` e `PathEffect`. Di conseguenza alcune tecniche interessanti, ad esempio il recupero di un percorso che è una struttura di un altro percorso. `GetFillPath` è inoltre utile per gli effetti di percorso.

## <a name="dots-and-dashes"></a>Punti e trattini

L'utilizzo del [ `PathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/) metodo descritto nell'articolo [ **punti e trattini**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md). Il primo argomento del metodo è una matrice contenente un numero pari di due o più valori, alternando tra lunghezze dei trattini e le lunghezze dei gap tra i trattini:

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

Questi valori sono *non* rispetto alla larghezza del tratto. Ad esempio, lo spessore del tratto è 10, se si desidera una linea composta da quadrato trattini e spazi quadrati, impostare il `intervals` matrice {10, 10}. Il `phase` argomento indica all'interno del motivo a tratteggio in cui la riga inizia. In questo esempio, se si desidera che la riga inizia con il quadrato gap, impostare `phase` a 10.

La fine dei trattini sono interessati i `StrokeCap` proprietà di `SKPaint`. Per le larghezze tratto ampia, è molto comune per impostare questa proprietà su `SKStrokeCap.Round` arrotondare le entità finali di trattini. In questo caso, i valori di `intervals` matrice effettuare *non* includono la maggiore lunghezza risultanti dall'arrotondamento, il che significa che un punto circolare è necessario specificare una larghezza pari a zero. Per un valore pari a 10, per creare una riga con punti circolare e gap tra i punti del diametro del stesso, usare un `intervals` matrice di {0, 20}.

Il **animata testo puntato** pagina è simile al **descritte testo** pagina descritto nell'articolo [ **l'integrazione di testo e grafica** ](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md) in che visualizza descritte caratteri di testo impostando il `Style` proprietà del `SKPaint` oggetto `SKPaintStyle.Stroke`. Inoltre, **animata testo puntato** Usa `SKPathEffect.CreateDash` per concedere questo descrive un aspetto punteggiato, e anche il programma aggiunge un'animazione il `phase` argomento del `SKPathEffect.CreateDash` metodo sembrano prediligere intorno al testo per i punti caratteri. Di seguito è la pagina in modalità orizzontale:

[![](effects-images/animateddottedtext-small.png "Schermata triplo della pagina animato testo puntato")](effects-images/animateddottedtext-large.png#lightbox "tripla schermata della pagina animato testo puntato")

Il [ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe inizia con la definizione di alcune costanti ed esegue l'override di `OnAppearing` e `OnDisappearing` metodi per l'animazione:

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

Il `PaintSurface` gestore inizia creando un `SKPaint` oggetto per visualizzare il testo. Il `TextSize` proprietà viene regolata in base alla larghezza dello schermo:

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
            SKRect textBounds;
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

Verso la fine del metodo, il `SKPathEffect.CreateDash` metodo viene chiamato utilizzando il `dashArray` definito come un campo e aggiunta un'animazione `phase` valore. Il `SKPathEffect` istanza è impostata la `PathEffect` proprietà del `SKPaint` oggetto per visualizzare il testo.

In alternativa, è possibile impostare il `SKPathEffect` dell'oggetto per il `SKPaint` oggetto prima di misurare il testo, allineamento al centro nella pagina. In tal caso, tuttavia, animate punti e trattini provocano una variazione delle dimensioni del testo viene eseguito il rendering e il testo tende a vibrare leggermente. (Provalo!)

Si noterà che come i caratteri di testo a cerchio punti animati, c'è un certo punto ogni curva chiusa in cui i punti sembrano pop da e verso l'esistenza. Si tratta in cui il percorso che definisce la struttura del carattere inizia e finisce. Se la lunghezza del percorso non è un multiplo intero della lunghezza del motivo di tratteggio (in questo caso 20 pixel) alla fine del percorso può contenere solo una parte di tale modello.

È possibile regolare la durata del motivo di tratteggio per adattarsi alla lunghezza del percorso, ma che è necessario determinare la lunghezza del percorso, una tecnica che essere analizzate in un altro articolo.

Il **punto / tratto Morph** programma aggiunge un'animazione motivo di tratteggio a se stessa in modo che sembrano trattini per suddividere in punti, combinano nuovamente in trattini modulo:

[![](effects-images/dotdashmorph-small.png "Schermata triplo della pagina Morph Dash punto")](effects-images/dotdashmorph-large.png#lightbox "tripla schermata della pagina Morph Dash punto")

Il [ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs) classe esegue l'override di `OnAppearing` e `OnDisappearing` nello stesso modo in cui ha il programma precedente, ma la classe definisce i metodi di `SKPaint` oggetto come un campo:

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

Il `PaintSurface` gestore crea un tracciato ellittico in base alla dimensione della pagina e viene eseguita una sezione di codice che imposta lungo il `dashArray` e `phase` variabili. Come variabile di animazione `t` compreso tra 0 e 1, il `if` blocchi suddividere che ora in quattro trimestri e in ognuno di tali trimestri, `tsub` anche compreso tra 0 e 1. Alla fine, viene creato il `SKPathEffect` e lo imposta sul `SKPaint` oggetto per il disegno.

## <a name="from-path-to-path"></a>Dal percorso

Il [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/) metodo `SKPaint` consente di trasformare un percorso in un altro in base alle impostazioni di `SKPaint` oggetto. Per visualizzare il funzionamento, sostituire il `canvas.DrawPath` chiamare nel programma precedente con il codice seguente:

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

In questo codice, il `GetFillPath` chiamare converte il `ellipsePath` (ovvero solo un'ellissi) in `newPath`, che viene visualizzato con `newPaint`. Il `newPaint` oggetto viene creato con tutte le impostazioni predefinite delle proprietà ad eccezione del fatto che il `Style` proprietà viene impostata in base al valore booleano restituito da `GetFillPath`.

Gli oggetti visivi sono identici tranne che per il colore, che è impostato in `ellipsePaint` ma non `newPaint`. Anziché l'ellisse semplice definito in `ellipsePath`, `newPath` contiene numerose distribuzioni del percorso che definiscono la serie di punti e trattini. Questo è il risultato dell'applicazione di varie proprietà di `ellipsePaint` : `StrokeWidth`, `StrokeCap`, e `PathEffect` : per `ellipsePath` e inserire il percorso risultante in `newPath`. Il `GetFillPath` metodo restituisce un valore booleano che indica se è il percorso di destinazione in cui inserire; in questo esempio, il valore restituito è `true` per riempire il percorso.

Provare a modificare il `Style` impostazione `newPaint` per `SKPaintStyle.Stroke` si noterà che le distribuzioni di singoli percorso delimitate da una linea di uno-a larghezza in pixel.

## <a name="stroking-with-a-path"></a>Traccia con un percorso

Il [ `SKPathEffect.Create1DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create1DPath/p/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPath1DPathEffectStyle/) è concettualmente simile al metodo `SKPathEffect.CreateDash` ad eccezione del fatto che si specifica un percorso anziché un motivo di trattini e spazi. Questo percorso viene replicato più volte per la riga di traccia o curva.

La sintassi è:

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

> [!IMPORTANT]
> Attenzione: è un overload di `Create1DPath` definito con un argomento di enumerazione di tipo `SkPath1DPathEffect` con una lettera minuscola 'k'. Questo nome è un errore e di conseguenza enumerazione e metodo sono deprecate, ma è molto semplice per il metodo obsoleto diventano parte del codice che è difficile vedere esattamente cosa non è corretto.

In generale, il percorso che viene passato a `Create1DPath` sarà piccole e centrato intorno al punto (0, 0). Il `advance` parametro indica la distanza tra i centri di percorso come il percorso verrà replicato nella riga. In genere si imposta questo argomento per la larghezza approssimativa del percorso. Il `phase` svolto argomento avviene per il ruolo stesso come il `CreateDash` metodo.

Il [ `SKPath1DPathEffectStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath1DPathEffectStyle/) dispone di tre membri:

- [`Translate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Translate/)
- [`Rotate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Rotate/)
- [`Morph`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Morph/)

Il `Translate` membro, il percorso viene replicato lungo una riga o una curva rimanga lo stesso orientamento. Per `Rotate`, il percorso viene ruotato in base a una tangente per la curva. Il percorso è relativo orientamento normale per le linee orizzontali. `Morph` è simile a `Rotate` ad eccezione del fatto che il percorso stesso anche curvo in modo che corrisponda di curvatura della linea viene tracciata.

Il **1 D percorso effetto** pagina illustra queste tre opzioni. Il [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml) file definisce una selezione contenente tre elementi corrispondenti ai tre membri dell'enumerazione:

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

Il [ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs) file code-behind definisce tre `SKPathEffect` oggetti come campi. Questi vengono creati utilizzando `SKPathEffect.Create1DPath` con `SKPath` gli oggetti creati utilizzando `SKPath.ParseSvgPathData`. La prima è una semplice casella, la seconda è una forma di rombo e il terzo è un rettangolo. Questi vengono utilizzati per illustrare gli stili di tre effetto:

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

Il `PaintSurface` gestore crea una curva di Bézier che esegue un ciclo stesso e accede alla selezione per determinare quale `PathEffect` deve essere utilizzato per applicare la traccia. Le tre opzioni: `Translate`, `Rotate`, e `Morph` , vengono visualizzati da sinistra a destra:

[![](effects-images/1dpatheffect-small.png "Schermata triplo della pagina percorso effetto 1D")](effects-images/1dpatheffect-large.png#lightbox "tripla schermata della pagina 1D effetto del percorso")

Il percorso specificato nella `SKPathEffect.Create1DPath` metodo viene sempre inserito. Il percorso specificato nel `DrawPath` metodo sempre viene tracciato se il `SKPaint` oggetto ha relativo `PathEffect` proprietà è impostata su un effetto di percorso 1 D. Si noti che il `pathPaint` oggetto non ha alcun `Style` impostazione, impostazione predefinita è solitamente `Fill`, ma tracciato indipendentemente dal fatto che il percorso.

La casella di utilizzato nel `Translate` esempio è 20 pixel quadrati e `advance` argomento è impostato su 24. Questa differenza provoca un gap tra le caselle quando la riga è approssimativamente orizzontale o verticale, ma le finestre si sovrappongono leggermente quando la riga è diagonale perché diagonale della finestra è 28.3 pixel. 

La forma di rombo di `Rotate` esempio anche è di 20 pixel. Il `advance` è impostato su 20, in modo da continuano i punti di tocco come rombo viene ruotato insieme curvatura della linea.

La forma rettangolo il `Morph` esempio è 50 pixel in larghezza con un `advance` impostazione 55 per garantire un breve intervallo tra i rettangoli sono piegati attorno alla curva di Bézier.

Se il `advance` argomento è inferiore alle dimensioni del percorso, quindi i percorsi replicati possono sovrapporsi. Ciò può comportare alcuni effetti interessanti. Il **catena collegato** pagina Visualizza una serie di cerchi che sembrano simile a quella di una catena di collegati, si blocca nella forma distintivo di una catenaria sovrapposti:

[![](effects-images/linkedchain-small.png "Schermata triplo della pagina collegata catena")](effects-images/linkedchain-large.png#lightbox "tripla schermata della pagina collegata catena")

Aspetto molto simile e si noterà che quelli non sono effettivamente cerchi. Ogni collegamento nella catena è due archi, ridimensionare e posizionare in modo sembrano connettersi con collegamenti adiacenti.

Una catena o il cavo di distribuzione del peso uniform si blocca sotto forma di un catenaria. Un'architettura compilato sotto forma di un catenaria invertito avvantaggia di una distribuzione uniforme di pressione dal peso di un'architettura. Catenaria presenta una descrizione di matematica apparentemente semplice:

y = un · cosh(x / a)

Il *cosh* è la funzione coseno iperbolico. Per *x* uguale a 0, *cosh* è uguale a zero e *y* è uguale a *un*. Che rappresenta il centro della catenaria. Ad esempio il *coseno* funzione *cosh* è detto *anche*, vale a dire che *cosh(–x)* è uguale a *cosh(x)*, e aumentano i valori per l'aumento argomenti positivi o negativi. Questi valori vengono descritti le curve che formano i lati della catenaria.

Trovare il valore corretto di *un* per adattarla alle dimensioni di pagina del telefono catenaria non è un calcolo diretto. Se *w* e *h* sono la larghezza e altezza di un rettangolo, il valore ottimale di *un* soddisfa l'equazione seguente:

cosh(w / 2 / a) = 1 + h / a

Il metodo seguente nel [ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) classe incorpora tale uguaglianza facendo riferimento a due espressioni a sinistra e a destra del segno di uguale come `left` e `right`. Per valori bassi di *un*, `left` è maggiore di `right`; valori elevati di *un*, `left` è minore di `right`. Il `while` ciclo viene convertito un valore ottimale di *un*:

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

Il `SKPath` dell'oggetto per i collegamenti che viene creato nel costruttore della classe e i risultanti `SKPathEffect` oggetto viene impostato il `PathEffect` proprietà del `SKPaint` oggetto archiviato come campo:

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

Il processo della `PaintSurface` gestore consiste nel creare un percorso per catenaria stesso. Dopo aver determinato ottimale *un* e archiviarlo nel `optA` variabile, ma è necessario anche per calcolare un offset dalla parte superiore della finestra. Quindi, può accumulare una raccolta di `SKPoint` valori per catenaria, attiva in un percorso e tracciare il percorso con creato in precedenza `SKPaint` oggetto:

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

Questo programma definisce il percorso utilizzato nella `Create1DPath` avere relativo (0, 0) punto al centro. Questo sembra ragionevole poiché il (0, 0) punto del percorso sia allineato con la riga o di una curva che sta decorando. Tuttavia, è possibile utilizzare non centrata (0, 0) di punti per alcuni effetti speciali.

Il **nastro trasportatore** pagina consente di creare un percorso simile a un nastro trasportatore oblunghi con una curva superiore e inferiore viene ridimensionato per le dimensioni della finestra. Tale percorso viene tracciato con un semplice `SKPaint` oggetto 20 pixel di larghezza e di colore grigio e tracciati nuovamente con un'altra `SKPaint` dell'oggetto con un `SKPathEffect` oggetto riferimento a un percorso simile a un minimo di bucket:

[![](effects-images/conveyorbelt-small.png "Schermata di triplo della pagina nastro trasportatore")](effects-images/conveyorbelt-large.png#lightbox "tripla schermata della pagina nastro trasportatore")

Il (0, 0) punto del percorso di bucket è l'handle, quando il `phase` argomento Mostra un'animazione, i bucket sembrano riguardano nastro trasportatore forse Rolly di acqua nella parte inferiore e i dump nella parte superiore.

Il [ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs) animazione con sostituzioni di implementa il `OnAppearing` e `OnDisappearing` metodi. Il percorso per il bucket è definito nel costruttore della pagina:

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

Il codice di creazione di bucket viene completato con due trasformazioni che rendono il bucket leggermente più grande e attivarla lateralmente. L'applicazione di tali trasformazioni è semplice modificare tutte le coordinate nel codice precedente. 

Il `PaintSurface` gestore inizia con la definizione di un percorso per il nastro trasportatore stesso. Si tratta semplicemente di una coppia di righe e una coppia di semi-cerchi disegnati con una linea di larghezza di 20 pixel grigio scuro:

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

La logica per il disegno nastro trasportatore non funziona in modalità orizzontale.

I bucket devono essere spaziati su 200 pixel di distanza su nastro trasportatore. Tuttavia, il nastro trasportatore probabilmente non è un multiplo di 200 pixel lungo, ovvero come il `phase` argomento di `SKPathEffect.Create1DPath` viene aggiunta un'animazione, bucket verranno visualizzata in e da esistenza. 

Per questo motivo, la prima viene calcolato un valore denominato `length` che rappresenta la lunghezza del nastro trasportatore. Poiché il nastro trasportatore è costituito da punto e virgola cerchi e linee rette, si tratta di un calcolo semplice. Successivamente, il numero di bucket viene calcolato dividendo `length` da 200. Questo valore viene arrotondato all'intero più vicino e che è quindi suddivisa in `length`. Il risultato è una spaziatura per un numero integrale di bucket. Il `phase` argomento è semplicemente una frazione di tale.

## <a name="from-path-to-path-again"></a>Dal percorso al nuovo percorso

Nella parte inferiore del `DrawSurface` gestore in **nastro trasportatore**, impostare come commento il `canvas.DrawPath` chiamare e sostituirlo con il codice seguente:

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

Come con l'esempio precedente dei `GetFillPath`, si noterà che i risultati sono la stessa ad eccezione di colore. Dopo l'esecuzione di `GetFillPath`, `newPath` oggetto contiene più copie del percorso di bucket, ognuno posizionato nello stesso campione che l'animazione loro al momento della chiamata.

## <a name="hatching-an-area"></a>Un'Area di tratteggio

Il [ `SKPathEffect.Create2DLines` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DLine/p/System.Single/SkiaSharp.SKMatrix/) metodo compila un'area con linee parallele, spesso denominata *tratteggio righe*. Il metodo presenta la sintassi seguente:

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

Il `width` argomento specifica lo spessore delle linee tratteggiate. Il `matrix` parametro è una combinazione di rotazione di ridimensionamento e facoltativa. Il fattore di scala indica l'incremento di pixel che utilizza Skia per spaziatura tra le linee tratteggiate. La separazione tra le linee è il fattore di scala meno il `width` argomento. Se il fattore di scala è minore o uguale al `width` valore, non si verificherà un inserire spazi tra le linee tratteggiate e viene visualizzata l'area da riempire. Specificare lo stesso valore per la scalabilità orizzontale e verticale. 

Per impostazione predefinita, sono disposti orizzontalmente linee tratteggiate. Se il `matrix` parametro contiene la rotazione, le linee tratteggiate vengono ruotate in senso orario.

Il **tratteggio riempimento** è illustrato l'effetto del percorso. Il [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) classe definisce tre effetti percorso come campi, la prima per linee tratteggiate orizzontali con una larghezza di 3 pixel con un fattore scala che indica che sono la spaziatura tra 6 pixel di distanza. Pertanto, la separazione tra le righe è di 3 pixel. Il secondo effetto di percorso è per le linee tratteggiate verticali con una larghezza di 6 pixel con spaziatura 24 pixel di distanza (così la separazione è 18 pixel), e il terzo è per le righe di tratteggio a diagonali 12 pixel ampia spaziatura 36 pixel di distanza. 

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
        SKMatrix target;
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

Si noti la matrice `Multiply` metodo. Poiché i fattori di scala orizzontali e verticali sono uguali, l'ordine in cui vengono moltiplicate le matrici di scalabilità e la rotazione non è rilevante.

Il `PaintSurface` gestore utilizza questi effetti tre percorso con tre colori diversi in combinazione con `fillPaint` per riempire un rettangolo arrotondato ridimensionato per adattarlo alla pagina. Il `Style` proprietà è impostata su `fillPaint` viene ignorato; quando il `SKPaint` oggetto include un effetto di percorso creato da `SKPathEffect.Create2DLine`, l'area viene riempita indipendentemente dal fatto che:

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

Se si osserva attentamente i risultati, si noterà che le linee tratteggiate rosso e blu non sono limitate con precisione al rettangolo arrotondato. (Questo è apparentemente una caratteristica del codice Skia sottostante). Se non è soddisfacente, un approccio alternativo viene visualizzato per le linee di tratteggio a diagonali in verde: il rettangolo arrotondato viene utilizzato come un tracciato di ritaglio e vengono disegnate le linee tratteggiate nella pagina intera.

Il `PaintSurface` gestore termina con una chiamata per tracciare semplicemente il rettangolo arrotondato, in modo da visualizzare la differenza con le linee tratteggiate rosso e blu:

[![](effects-images/hatchfill-small.png "Schermata di triplo della pagina tratteggio riempimento")](effects-images/hatchfill-large.png#lightbox "tripla schermata della pagina tratteggio riempimento")

Schermata di Android effettivamente non sembra simile: la scala della schermata ha causato il thin righe rosse e thin spazi di consolidare in linee rosse apparentemente più ampio e più ampia.

## <a name="filling-with-a-path"></a>Riempimento con un percorso

Il [ `SKPathEffect.Create2DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DPath/p/SkiaSharp.SKMatrix/SkiaSharp.SKPath/) consente di riempire un'area con un percorso che viene replicato orizzontalmente e verticalmente, in effetti affiancamento area:

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

Il `SKMatrix` fattori di scala indicano la spaziatura orizzontale e verticale del percorso di replica. Ma non è possibile ruotare il percorso usando questa `matrix` argomento; se si desidera che il percorso ruotato, ruotare il percorso stesso utilizzando il `Transform` definito dal metodo `SKPath`. 

Il percorso di replicato è in genere allineato con i bordi superiore e sinistro della schermata piuttosto che l'area riempita. È possibile eseguire l'override di questo comportamento, fornendo i fattori di conversione compreso tra 0 e i fattori di scala per specificare l'offset orizzontale e verticale i lati sinistro e superiore.

Il **percorso riquadro riempimento** è illustrato l'effetto del percorso. Il percorso utilizzato per affiancare l'area è definito come un campo di [ `PathFileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) classe. Coordinate orizzontali e verticali compreso da -40 e 40, il che significa che questo percorso è di 80 pixel quadrati: 

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

Nel `PaintSurface` gestore, la `SKPathEffect.Create2DPath` chiamate imposta la spaziatura orizzontale e verticale a 64 affinché i riquadri quadrati 80 pixel sovrapposizione. Per fortuna, il percorso è simile a una parte del puzzle, mesh perfettamente con riquadri adiacenti:

[![](effects-images/pathtilefill-small.png "Schermata triplo della pagina percorso riquadro riempimento")](effects-images/pathtilefill-large.png#lightbox "tripla schermata della pagina percorso riquadro riempimento")

Il ridimensionamento dalla schermata originale causa distorsioni, in particolare sullo schermo Android.

Si noti che questi riquadri sempre visualizzato interi e non vengono mai troncati. Ad eccezione del fatto sullo schermo, Windows 10 Mobile non è ancora evidente che l'area riempita è un rettangolo arrotondato. Se si desidera troncare questi riquadri per una determinata area, usare un tracciato di ritaglio.

Provare a impostare il `Style` proprietà del `SKPaint` oggetto `Stroke`, si noterà che i singoli riquadri descritti anziché compilato.

## <a name="rounding-sharp-corners"></a>Arrotondamento degli angoli acuti

Il **arrotondato Heptagon** programma presentati nel [ **tre modi per disegnare un arco** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md) articolo usato un arco tangente per i punti di una figura sette lati curva. Il **Heptagon arrotondato un altro** pagina viene illustrato un approccio molto più semplice che utilizza un effetto di percorso creato dal [ `SKPathEffect.CreateCorner` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCorner/p/System.Single/) metodo:

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

Anche se l'argomento singolo è denominato `radius` è necessario impostarlo per metà il raggio dell'angolo desiderato. (Si tratta di una caratteristica del codice Skia sottostante).

Ecco il `PaintSurface` gestore di [ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) classe:

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

È possibile utilizzare questo effetto con riempimento in base a o che traccia il `Style` proprietà del `SKPaint` oggetto. Di seguito in tutti e tre le piattaforme è:

[![](effects-images/anotherroundedheptagon-small.png "Schermata triplo della pagina di un altro Heptagon arrotondato")](effects-images/anotherroundedheptagon-large.png#lightbox "tripla schermata della pagina di un altro Heptagon arrotondato")

Si noterà che questo heptagon arrotondato sia identico al programma precedente. Se è necessario più convincere che il raggio dell'angolo è realmente 100 anziché il 50 specificato nella `SKPathEffect.CreateCorner` chiamata, è possibile rimuovere il commento l'istruzione finale nel programma e vedere un cerchio 100 radius sovrapposto all'angolo.

## <a name="random-jitter"></a>Instabilità casuale

Talvolta le linee rette perfetta di grafica su computer non sono abbastanza si desidera e si desidera la casualità un po'. In tal caso, è opportuno tentare di [ `SKPathEffect.CreateDiscrete` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDiscrete/p/System.Single/System.Single/System.UInt32/) metodo:

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

È possibile utilizzare questo effetto di percorso per la traccia o la compilazione. Le righe sono suddivise in segmenti connessi, la durata approssimativa di cui è specificata dalla `segLength` : ed estendere in diverse direzioni. L'entità della deviazione dalla riga originale viene specificato da `deviation`. 

L'argomento finale è un valore di inizializzazione utilizzato per generare la sequenza pseudocasuale utilizzata per l'effetto. L'effetto della variazione avrà un aspetto leggermente diverso per valori di inizializzazione diversi. L'argomento ha un valore predefinito pari a zero, il che significa che l'effetto è lo stesso ogni volta che si esegue il programma. Se si desidera instabilità diverso ogni volta che lo schermo è aggiornato, è possibile impostare il valore di inizializzazione il `Millisecond` proprietà di un `DataTime.Now` valore (ad esempio).


Il **variazione sperimentare** pagina consente di provare diversi valori in un rettangolo di traccia:

[![](effects-images/jitterexperiment-small.png "Schermata della pagina variazione esperimento tripla")](effects-images/jitterexperiment-large.png#lightbox "Triple screenshot of the JitterExperiment page")

Il programma è straightfoward. Il [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml) file crea due `Slider` elementi e un `SKCanvasView`:

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

Il `PaintSurface` gestore nel [ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs) file code-behind viene chiamato ogni volta che un `Slider` cambia valore. Chiama `SKPathEffect.CreateDiscrete` le due `Slider` valori che verrà usata per tracciare un rettangolo:

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

È possibile utilizzare questo effetto per il riempimento nonché, nel qual caso la struttura dell'area riempita è soggetto a queste deviazioni casuale. Il **testo variazione** pagina viene illustrato come utilizzare questo effetto di percorso per visualizzare il testo. La maggior parte del codice di `PaintSurface` gestore del [ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) classe dedicata al ridimensionamento e centrare il testo:

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
        SKRect textBounds;
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

Qui è in esecuzione in modalità orizzontale in tutte e tre le piattaforme:

[![](effects-images/jittertext-small.png "Schermata della pagina di testo variazione tripla")](effects-images/jittertext-large.png#lightbox "Triple screenshot of the JitterText page")

## <a name="path-outlining"></a>Struttura del percorso

Abbiamo già visto due esempi minimo del [ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/) metodo `SKPaint`, che è presente anche in un [overload](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/):

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale)
```

Solo i primi due argomenti sono necessari. Il metodo accede al percorso a cui fa riferimento il `src` argomento, modifica i dati del percorso basati sulle proprietà tratto nel `SKPaint` oggetto (inclusi i `PathEffect` proprietà) e quindi scrive i risultati nel `dst` percorso. Il `resScale` parametro consente di ridurre la precisione per creare un percorso di destinazione più piccolo e `cullRect` argomento consente di eliminare i contorni all'esterno di un rettangolo.

Un utilizzo di base di questo metodo non comporta effetti percorso affatto. Se il `SKPaint` oggetto ha relativo `Style` proprietà impostata su `SKPaintStyle.Stroke`e *non* hanno relativo `PathEffect` impostato, allora `GetFillPath` consente di creare un percorso che rappresenta un *struttura*del percorso di origine come se fosse stato tracciato dalle proprietà del disegno.

Ad esempio, se il `src` percorso è un cerchio semplice del raggio 500 e `SKPaint` oggetto specifica un valore pari a 100, il `dst` percorso diventa due cerchi concentrici, uno con un raggio di 450 e l'altro con un raggio di 550. Il metodo viene chiamato `GetFillPath` perché la compilazione di questo `dst` percorso corrisponde alla traccia di `src` percorso. Ma è anche possibile tracciare il `dst` tracciato per visualizzare i profili di percorso.

Il **toccare alla struttura di percorso** illustra questa operazione. Il `SKCanvasView` e `TapGestureRecognizer` viene creata un'istanza di [ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml) file. Il [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs) file code-behind definisce tre `SKPaint` oggetti come campi, due per applicare una traccia con larghezza pari a 100 e 20 e il terzo per il riempimento di traccia:

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

Se la schermata non è stata toccata, il `PaintSurface` gestore utilizza il `blueFill` e `redThickStroke` disegnare oggetti per eseguire il rendering di un percorso circolare:

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

Il cerchio viene compilato e tracciato come previsto:

[![](effects-images/taptooutlinethepathnormal-small.png "Schermata triplo della pagina scegliere per il percorso della struttura normale")](effects-images/taptooutlinethepathnormal-large.png#lightbox "tripla schermata della pagina scegliere per il percorso della struttura normale")

Quando si tocca lo schermo `outlineThePath` è impostato su `true`e `PaintSurface` gestore crea una nuova `SKPath` dell'oggetto che verrà usata come il percorso di destinazione in una chiamata a `GetFillPath` sul `redThickStroke` oggetto paint. Il percorso di destinazione viene quindi compilato e tracciato con `redThinStroke`, con conseguente le operazioni seguenti:

[![](effects-images/taptooutlinethepathoutlined-small.png "Schermata triplo della pagina scegliere per il percorso della struttura contorno")](effects-images/taptooutlinethepathoutlined-large.png#lightbox "tripla schermata della pagina contorno toccare per il percorso della struttura")

Due cerchi rossi indicano chiaramente che il percorso circolare originale è stato convertito in due contorni circolari.

Questo metodo può essere molto utile per lo sviluppo di percorsi da usare per il `SKPathEffect.Create1DPath` metodo. I percorsi specificati in questi metodi vengono sempre compilati quando i percorsi vengono replicati. Se non si desidera specificare il percorso completo, è necessario definire con attenzione i profili.

Ad esempio, nel **catena collegato** esempio, i collegamenti sono stati definiti con una serie di quattro archi, ogni coppia di cui era stata basata su due raggi descrive l'area del tracciato da riempire. È possibile sostituire il codice di `LinkedChainPage` classe per eseguire questa operazione in modo leggermente diverso.

In primo luogo, è opportuno ridefinire la `linkRadius` costante:

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

Il `linkPath` è ora solo due archi in base a tale singola radius, con l'oggetto desiderato avviare gli angoli e degli angoli di apertura:

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

Il `outlinePath` oggetto viene quindi il destinatario del contorno di `linkPath` quando tracciata con le proprietà specificate `strokePaint`. 

Un altro esempio, questa tecnica è esercitazione seguente per il percorso utilizzato un `SKPathEffect.Create2DPath` metodi. 

## <a name="combining-path-effects"></a>Combinando gli effetti di percorso

I due metodi di creazione statica finale di `SKPathEffect` sono [ `SKPathEffect.CreateSum` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateSum/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/) e [ `SKPathEffect.CreateCompose` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCompose/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

Entrambi i metodi di combinano due effetti di percorso per creare un effetto di percorso composita. Il `CreateSum` metodo crea un effetto simile agli effetti di due percorsi applicati separatamente, mentre `CreateCompose` si applica un effetto di percorso (il `inner`) e quindi applica il `outer` a quello.

Abbiamo già visto come `GetFillPath` metodo di `SKPaint` può convertire un percorso a un altro percorso in base `SKPaint` proprietà (inclusi `PathEffect`) in modo che non deve essere *troppo* insolite come un `SKPaint`oggetto tale operazione può essere eseguita due volte con gli effetti di due percorsi specificati nel `CreateSum` o `CreateCompose` metodi.

Uno degli utilizzi ovvio `CreateSum` consiste nel definire un `SKPaint` oggetto che viene compilato un percorso con effetto di un percorso e i tratti il percorso con effetto di un altro percorso. Questa funzionalità viene illustrata la **Cats nel Frame** un esempio che visualizza una matrice di cats all'interno di un frame con bordi ondulati:

[![](effects-images/catsinframe-small.png "Schermata di triplo della pagina Cats nel Frame")](effects-images/catsinframe-large.png#lightbox "tripla schermata della pagina Cats nel Frame")

Il [ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs) classe inizia con la definizione di più campi. È possibile riconoscere il primo campo da di [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe il [ **SVG percorso dati** ](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) articolo. Secondo percorso si basa su una riga e un arco per il modello smerlo del frame:

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

Il `catPath` possono essere utilizzati nel `SKPathEffect.Create2DPath` metodo se il `SKPaint` oggetto `Style` è impostata su `Stroke`. Tuttavia, se il `catPath` viene utilizzato direttamente in questo programma, quindi l'intero elemento head del cat verrà compilato e baffi anche non saranno visibili. (Provalo!) È necessario ottenere la struttura del percorso e utilizzare tale struttura nel `SKPathEffect.Create2DPath` metodo.

Il costruttore non questo processo. Si applica innanzitutto due trasformazioni `catPath` per spostare il (0, 0) posizionato al centro e ridurre le dimensioni. `GetFillPath` Ottiene tutti i profili dei contorni in `outlinedCatPath`, e tale oggetto viene utilizzato il `SKPathEffect.Create2DPath` chiamare. Il ridimensionamento fattori che hanno il `SKMatrix` valore sono leggermente superiore rispetto a orizzontale e dimensione verticale del cat per fornire un buffer minimo tra i riquadri, mentre i fattori di conversione sono state derivate in qualche modo ed empiricamente in modo che un cat completo è visibile nel Nell'angolo superiore sinistro del riquadro:

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

Quindi chiama il costruttore `SKPathEffect.Create1DPath` per il frame ondulato. Si noti che la larghezza del percorso è 100 pixel, ma l'avanzamento è di 75 pixel in modo che il percorso di replicato si sovrapposta attorno alla cornice. L'istruzione finale delle chiamate di costruttore `SKPathEffect.CreateSum` per combinare gli effetti di due percorsi e impostare il risultato di `SKPaint` oggetto.

Tutto questo lavoro consente il `PaintSurface` gestore è molto semplice. È solo necessario definire un rettangolo e disegnarlo utilizzando `framePaint`:

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

Gli algoritmi dietro gli effetti di percorso comportano sempre l'intero percorso utilizzato per il riempimento o di traccia da visualizzare, che può causare alcuni oggetti visivi all'esterno del rettangolo. Il `ClipRect` chiamare prima il `DrawRect` chiamata consente gli oggetti visivi essere notevolmente più chiara. (Prova senza ritaglio!)

È comune per utilizzare `SKPathEffect.CreateCompose` per aggiungere alcune instabilità per effetto di un altro percorso. È possibile provare certamente per conto proprio, ma di seguito è riportato un esempio leggermente diverso:

Il **tratteggio tratteggiate** riempie un'ellisse con linee tratteggiate che sono tratteggiate. La maggior parte delle operazioni nel [ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) viene eseguita la classe destra nelle definizioni di campo. Questi campi definiscono un effetto dash e un tratteggio. Sono definiti come `static` perché vi viene quindi fatto riferimento un `SKPathEffect.CreateCompose` chiamare il `SKPaint` definizione:

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
        SKMatrix target;
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

Il `PaintSurface` contengono solo l'overhead standard oltre a una chiamata al gestore necessario `DrawOval`:

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

Come è stato già individuato, le linee tratteggiate non con precisione limitate all'interno dell'area e in questo esempio, iniziano sempre a sinistra con un trattino intero:

[![](effects-images/dashedhatchlines-small.png "Schermata di triplo della pagina di linee tratteggiate rosse tratteggiate")](effects-images/dashedhatchlines-large.png#lightbox "tripla schermata della pagina di linee tratteggiate rosse tratteggiate")

Dopo aver esaminato gli effetti di percorso che da semplici punti e trattini per combinazioni strano, le fotografie e vedere ciò che è possibile creare.



## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
