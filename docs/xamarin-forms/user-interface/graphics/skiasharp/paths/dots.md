---
title: Punti e trattini in SkiaSharp
description: Questo articolo illustra come gestire le complessità del disegno di linee tratteggiate e tratteggiate in SkiaSharp e ne illustra il codice di esempio.
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8698bacd7257df431b12166c749f5826e8d6fd3c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138450"
---
# <a name="dots-and-dashes-in-skiasharp"></a>Punti e trattini in SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Padroneggiare le complessità del disegno di linee tratteggiate e tratteggiate in SkiaSharp_

SkiaSharp consente di creare linee che non sono solide ma sono invece costituite da punti e trattini:

![](dots-images/dottedlinesample.png "Dotted line")

Questa operazione viene eseguita con un *effetto percorso*, ovvero un'istanza della [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) classe impostata sulla [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) proprietà di `SKPaint` . È possibile creare un effetto tracciato (o combinare effetti percorso) utilizzando uno dei metodi di creazione statici definiti da `SKPathEffect` . ( `SKPathEffect` è uno dei sei effetti supportati da SkiaSharp, mentre gli altri sono descritti nella sezione [**effetto SkiaSharp**](../effects/index.md)).

Per creare linee tratteggiate o tratteggiate, è possibile usare il [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) metodo statico. Esistono due argomenti: questa prima è una matrice di `float` valori che indicano le lunghezze dei punti e dei trattini e la lunghezza degli spazi tra di essi. Questa matrice deve avere un numero pari di elementi e deve contenere almeno due elementi. (Possono essere presenti zero elementi nella matrice, ma ciò comporta una linea continua). Se sono presenti due elementi, il primo è la lunghezza di un punto o un trattino e il secondo è la lunghezza del gap prima del punto o del trattino successivo. Se sono presenti più di due elementi, sono in questo ordine: lunghezza trattino, lunghezza Gap, lunghezza trattino, lunghezza Gap e così via.

In genere, è opportuno fare in modo che le lunghezze del trattino e del gap siano multiple della larghezza del tratto. Se la larghezza del tratto è pari a 10 pixel, ad esempio, la matrice {10, 10} traccia una linea tratteggiata in cui i punti e i gap hanno la stessa lunghezza dello spessore del tratto.

Tuttavia, l' `StrokeCap` impostazione dell' `SKPaint` oggetto influiscono anche su questi punti e trattini. Come si vedrà a breve, ciò influisca sugli elementi di questa matrice.

Le linee tratteggiate e tratteggiate sono illustrate nella pagina **punti e trattini** . Il file [**DotsAndDashesPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml) crea un'istanza `Picker` di due visualizzazioni, una per consentire di selezionare una copertura del tratto e la seconda per selezionare una matrice di trattini:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.DotsAndDashesPage"
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
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>10, 10</x:String>
                    <x:String>30, 10</x:String>
                    <x:String>10, 10, 30, 10</x:String>
                    <x:String>0, 20</x:String>
                    <x:String>20, 20</x:String>
                    <x:String>0, 20, 20, 20</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

 I primi tre elementi in `dashArrayPicker` presuppongono che la larghezza del tratto sia 10 pixel. La matrice {10, 10} è per una linea tratteggiata, {30, 10} è per una linea tratteggiata e {10, 10, 30, 10} è per una linea punto e Dash. Gli altri tre verranno discussi a breve.

Il [`DotsAndDashesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml.cs) file code-behind contiene il `PaintSurface` gestore eventi e alcune routine di supporto per l'accesso alle `Picker` Visualizzazioni:

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
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem,
        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint);
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string str = (string)picker.SelectedItem;
    string[] strs = str.Split(new char[] { ' ', ',' }, StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

Negli screenshot seguenti la schermata iOS all'estrema sinistra mostra una linea tratteggiata:

[![](dots-images/dotsanddashes-small.png "Triple screenshot of the Dots and Dashes page")](dots-images/dotsanddashes-large.png#lightbox "Triple screenshot of the Dots and Dashes page")

Tuttavia, anche la schermata Android dovrebbe mostrare una linea tratteggiata usando la matrice {10, 10}, ma la riga è a tinta unita. Che cosa è successo? Il problema è che anche la schermata di Android ha un'impostazione dei limiti del tratto di `Square` . In questo modo si estendono tutti i trattini della metà della larghezza del tratto, causando la colmatura dei gap.

Per ovviare a questo problema quando si usa un limite del tratto di `Square` o `Round` , è necessario ridurre le lunghezze dei trattini nella matrice per la lunghezza del tratto (a volte con un trattino di lunghezza pari a 0) e aumentare le lunghezze della distanza in base alla lunghezza del tratto. Questo è il modo in cui sono state calcolate le ultime tre matrici Dash nell'oggetto `Picker` nel file XAML:

- {10, 10} diventa {0, 20} per una linea tratteggiata
- {30, 10} diventa {20, 20} per una linea tratteggiata
- {10, 10, 30, 10} diventa {0, 20, 20, 20} per una linea tratteggiata e tratteggiata

La schermata UWP Mostra la linea tratteggiata e tratteggiata per un limite del tratto di `Round` . Il `Round` limite del tratto offre spesso l'aspetto migliore di punti e trattini in linee spesse.

Fino a questo punto non è stato fatto menzione del secondo parametro al `SKPathEffect.CreateDash` metodo. Questo parametro è denominato `phase` e fa riferimento a un offset all'interno del modello di punto e trattino per l'inizio della riga. Se, ad esempio, la matrice di trattini è {10, 10} e `phase` è 10, la riga inizia con un gap anziché con un punto.

Un'applicazione interessante del `phase` parametro è in un'animazione. La pagina a **spirale animata** è simile alla pagina di **spirale Archimede** , ad eccezione del fatto che la [`AnimatedSpiralPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) classe aggiunge un'animazione al `phase` parametro usando il Xamarin.Forms `Device.Timer` Metodo:

```csharp
public class AnimatedSpiralPage : ContentPage
{
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
    ···  
}
```

Naturalmente, sarà necessario eseguire effettivamente il programma per visualizzare l'animazione:

[![](dots-images/animatedspiral-small.png "Triple screenshot of the Animated Spiral page")](dots-images/animatedspiral-large.png#lightbox "Triple screenshot of the Animated Spiral page")

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
