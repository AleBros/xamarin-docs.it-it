---
title: Punti e trattini
description: Gli aspetti complessi di disegno di linee punteggiate e tratteggiate in SkiaSharp master
ms.topic: article
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 30b7e322d618492164fac5e439c5187616d61717
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="dots-and-dashes"></a>Punti e trattini

_Gli aspetti complessi di disegno di linee punteggiate e tratteggiate in SkiaSharp master_

SkiaSharp consente di disegnare linee che non sono a tinta unita, ma sono composti da punti e trattini:

![](dots-images/dottedlinesample.png "Linea punteggiata")

Eseguire questa operazione con un *effetto del percorso*, che è un'istanza del [ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/) classe che viene impostato il [ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/) proprietà di `SKPaint`. È possibile creare un percorso effetto o combinare percorso effetti mediante statica `Create` metodi definiti dal `SKPathEffect`.

Per disegnare linee punteggiate o tratteggiate, si utilizza il [ `SKPathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/) metodo statico. Esistono due argomenti: questo primo è una matrice di `float` valori che indicano le lunghezze dei punti e trattini e la lunghezza degli spazi tra di essi. Questa matrice deve avere un numero pari di elementi, e devono essere presenti almeno due elementi. (Possono essere presenti zero elementi nella matrice, ma che generano una linea continua.) Se sono presenti due elementi, il primo è la lunghezza di un punto o un trattino e il secondo è la lunghezza dello spazio vuoto prima il punto o un trattino successivo. Se sono presenti più di due elementi, quindi si trovano in questo ordine: trattino-lunghezza, lunghezza dello spazio, dash lunghezza, lunghezza dello spazio e così via.

In genere, sarà necessario apportare le lunghezze trattino e uno spazio di un multiplo dello spessore del tratto. Se lo spessore del tratto è 10 pixel, ad esempio, quindi la matrice {10, 10} verrà disegnare una linea punteggiata in cui i punti e spazi vuoti sono la stessa lunghezza lo spessore del tratto.

Tuttavia, il `StrokeCap` impostazione il `SKPaint` oggetto influisce inoltre su questi punti e trattini. Come si vedrà a breve, che ha un impatto sugli elementi di questa matrice.

Con punti e vengono illustrate le linee tratteggiate nella **punti e trattini** pagina. Il [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml) file crea due `Picker` viste, uno per cui è possibile selezionare un'estremità della traccia e il secondo per selezionare una matrice di trattini:

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

I primi tre elementi di `dashArrayPicker` si presuppone che lo spessore del tratto sia 10 pixel. Il {10, 10} matrice è per una linea punteggiata, {30, 10} è per una linea tratteggiata e {10, 10, 30, 10} per una riga di punti e trattini. (Gli altri tre verranno descritte al più presto.)

Il [ `DotsAndDashesPage` file code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs) contiene il `PaintSurface` gestore dell'evento e un paio di routine di supporto per l'accesso di `Picker` viste:

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

Nelle schermate seguenti, la schermata di iOS all'estrema sinistra mostra una linea punteggiata:

[![](dots-images/dotsanddashes-small.png "La schermata della pagine dei punti e trattini")](dots-images/dotsanddashes-large.png "tripla schermata della pagine dei punti e trattini")

Tuttavia, la schermata Android dovrebbe anche per visualizzare una linea punteggiata usando la matrice {10, 10} ma invece la riga è a tinta unita. Cosa è successo? Il problema è che la schermata di Android include anche un'impostazione maiuscole tratto di `Square`. Estende a tutti i trattini per metà larghezza del tratto, provocando riempire i gap.

Per aggirare questo problema quando si usa un limite di traccia di `Square` o `Round`, è necessario ridurre la lunghezza di trattino nella matrice dalla lunghezza tratto (talvolta risultante in lunghezza dash pari a 0) e aumentare le lunghezze di distanza per la lunghezza del tratto. Si tratta come tre finale tratteggio le matrici di `Picker` sono stati calcolati nel file XAML:

- {10, 10} è {0, 20} per una linea punteggiata
- {30, 10} diventa {20, 20} per una linea tratteggiata
- {10, 10, 30, 10} è {0, 20, 20, 20} per una riga con tratteggiata o

Chiudere il Mostra schermata di Windows che da punti e tratteggiata per un tratto di `Round`. Il `Round` nelle righe thick estremità della traccia offre spesso migliorare l'aspetto dei punti e trattini.

Finora non è diventati alcuna indicazione del secondo parametro per il `SKPathEffect.CreateDash` metodo. Questo parametro è denominato `phase` e si riferisce a un offset all'interno del criterio trattino e punto per l'inizio della riga. Ad esempio, se la matrice dash è {10, 10} e `phase` è 10, quindi la riga inizia con uno spazio vuoto anziché un punto.

Un'interessante applicazione del `phase` parametro è un'animazione. Il **spirale animato** pagina è simile al **Archimedean spirale** pagina con la differenza che il [ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/AnimatedSpiralPage.cs) classe aggiunge un'animazione di `phase` parametro. La pagina viene inoltre illustrato un altro approccio per l'animazione. Il precedente esempio del [ `PulsatingEllipsePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml.cs) utilizzato il `Task.Delay` metodo per controllare l'animazione. Questo esempio viene utilizzato invece di xamarin. Forms `Device.Timer` metodo:


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

Naturalmente, è necessario eseguire effettivamente il programma per visualizzare l'animazione:

[![](dots-images/animatedspiral-small.png "Schermata triplo della pagina spirale animato")](dots-images/animatedspiral-large.png "tripla schermata della pagina spirale animato")

Abbiamo visto come per disegnare linee e curve utilizzando equazioni parametriche. Una sezione per la pubblicazione in un secondo momento illustra i vari tipi di curve che `SKPath` supporta.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
