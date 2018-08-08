---
title: Punti e trattini in SkiaSharp
description: Questo articolo illustra come master le complicazioni del disegno di linee con tratteggiate o in SkiaSharp e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 7c336e6b5224f61ff84eb39652788b23f52b806e
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615418"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Punti e trattini in SkiaSharp

_Master le complicazioni del disegno di linee con tratteggiate o in SkiaSharp_

SkiaSharp consente di disegnare le righe che non sono affidabili ma che invece sono costituiti da punti e trattini:

![](dots-images/dottedlinesample.png "Linea punteggiata")

Usando un *effetto del percorso*, che è un'istanza del [ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/) classe che è impostato sulla [ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/) proprietà di `SKPaint`. È possibile creare un percorso di effetto (o combinare percorso effetti) usando il metodo statico `Create` metodi definiti da `SKPathEffect`.

Per disegnare linee tratteggiate o punteggiate, si utilizza il [ `SKPathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/) metodo statico. Esistono due argomenti: si tratta prima di tutto una matrice di `float` valori che indicano la lunghezza dei punti e trattini e la lunghezza degli spazi tra di essi. Questa matrice deve avere un numero pari di elementi e devono essere presenti almeno due elementi. (Può essere zero elementi nella matrice, ma che i risultati in una linea continua.) Se sono presenti due elementi, la prima è la lunghezza di un punto o trattino e il secondo è la lunghezza del gap prima il successivo punto o trattino. Se sono presenti più di due elementi, quindi sono nell'ordine indicato: trattino di lunghezza, durata dell'intervallo, lunghezza del trattino, durata dell'intervallo e così via.

In generale, è opportuno apportare le lunghezze dash e gap un multiplo dello spessore del tratto. Se lo spessore del tratto è di 10 pixel, ad esempio, quindi la matrice {10, 10} verrà disegnare una linea punteggiata in cui i punti e spazi vuoti sono della stessa lunghezza come lo spessore del tratto.

Tuttavia, il `StrokeCap` impostazione del `SKPaint` oggetto interessa anche questi punti e trattini. Come si vedrà a breve, che ha un impatto sugli elementi di questa matrice.

Con punti e vengono illustrate le linee tratteggiate nella **da punti e trattini** pagina. Il [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml) un'istanza di file due `Picker` Visualizza, uno per cui è possibile selezionare un'estremità della traccia e il secondo per selezionare un array di dash:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.DotsAndDashesPage"
             Title="Dots and Dashes">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Butt</x:String>
                <x:String>Round</x:String>
                <x:String>Square</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>10, 10</x:String>
                <x:String>30, 10</x:String>
                <x:String>10, 10, 30, 10</x:String>
                <x:String>0, 20</x:String>
                <x:String>20, 20</x:String>
                <x:String>0, 20, 20, 20</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

I primi tre elementi di `dashArrayPicker` si presuppone che lo spessore del tratto sia 10 pixel. La {10, 10} array si trovi per una linea punteggiata, {30, 10} è per una linea tratteggiata e {10, 10, 30, 10} per una riga di punto e dash. (Gli altri tre verranno discussa tra breve.)

Il [ `DotsAndDashesPage` file code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs) contiene il `PaintSurface` gestore dell'evento e un paio di routine di supporto per l'accesso al `Picker` viste:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = (SKStrokeCap)Enum.Parse(typeof(SKStrokeCap),
                        strokeCapPicker.Items[strokeCapPicker.SelectedIndex]),

        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint);
}

T GetPickerItem<T>(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return default(T);
    }

    return (T)Enum.Parse(typeof(T), picker.Items[picker.SelectedIndex]);
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string[] strs = picker.Items[picker.SelectedIndex].Split(new char[] { ' ', ',' },
                                                             StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

Nelle schermate seguenti, la schermata iOS all'estrema sinistra mostra una linea punteggiata:

[![](dots-images/dotsanddashes-small.png "Tripla screenshot della pagina di punti e trattini")](dots-images/dotsanddashes-large.png#lightbox "tripla screenshot della pagina di punti e trattini")

Tuttavia, lo schermo Android anche dovrebbe per visualizzare una linea punteggiata utilizzando la matrice {10, 10} ma è invece a tinta unita con la riga. Cosa è successo? Il problema è che lo schermo Android include anche un'impostazione di criteri di autorizzazione connessioni tratto di `Square`. Estende tutti i trattini per metà della larghezza di tratto, comportando riempire i gap.

Per evitare questo problema quando si usa un'estremità del tratto del `Square` o `Round`, è necessario ridurre le lunghezze di trattino nella matrice per la lunghezza di tratto (talvolta risultante in una lunghezza di tratteggio pari a 0) e aumentare le lunghezze di gap per la lunghezza di tratto. Si tratta di come i tre finali dash matrici nel `Picker` nel file XAML sono state calcolate:

- {10, 10} diventa {0, 20} per una linea punteggiata
- {30, 10} diventa {20, 20} per una linea tratteggiata
- {10, 10, 30, 10} diventa {0, 20, 20, 20} per una riga con tratteggiata o

Limitare il Mostra schermata UWP che da punti e linee tratteggiate line per un tratto di `Round`. Il `Round` nelle righe spesse estremità della traccia offre spesso l'aspetto migliore di punti e trattini.

Finora non è diventato alcun accenno del secondo parametro per il `SKPathEffect.CreateDash` (metodo). Questo parametro è denominato `phase` e fa riferimento a un offset all'interno del criterio trattino e punto per l'inizio della riga. Ad esempio, se la matrice dash è {10, 10} che `phase` è 10, quindi la riga inizia con uno spazio vuoto anziché a un punto.

Un'interessante applicazione dei `phase` parametro è un'animazione. Il **animato spirale** pagina è simile al **Archimedean spirale** pagina con la differenza che il [ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/AnimatedSpiralPage.cs) classe aggiunge un'animazione il `phase` parametro. La pagina viene anche illustrato un altro approccio per l'animazione. Nell'esempio precedente del [ `PulsatingEllipsePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml.cs) utilizzato la `Task.Delay` metodo per controllare l'animazione. Questo esempio viene usato invece xamarin. Forms `Device.Timer` metodo:


```csharp
const double cycleTime = 250;       // in milliseconds

SKCanvasView canvasView;
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float dashPhase;

public AnimatedSpiralPage()
{
    Title = "Animated Spiral";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}

protected override void OnAppearing()
{
    base.OnAppearing();
    pageIsActive = true;
    stopwatch.Start();

    Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
    {
        double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
        dashPhase = (float)(10 * t);
        canvasView.InvalidateSurface();

        if (!pageIsActive)
        {
            stopwatch.Stop();
        }

        return pageIsActive;
    });
}
```

Naturalmente, è possibile eseguire effettivamente il programma per verificare l'animazione:

[![](dots-images/animatedspiral-small.png "Tripla screenshot della pagina aggiungendo un'animazione spirale")](dots-images/animatedspiral-large.png#lightbox "tripla screenshot della pagina spirale animata")

A questo punto si è appreso come per disegnare linee e curve utilizzando equazioni parametriche definire. Una sezione da pubblicare in un secondo momento illustra i vari tipi di curve che `SKPath` supporta.


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
