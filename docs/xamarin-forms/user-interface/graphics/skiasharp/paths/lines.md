---
title: Linee ed estremità dei tratti
description: Questo articolo illustra come usare SkiaSharp per disegnare linee con estremità dei tratti diversi nelle applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 6dc7737290bf7eacb3ba0e0bca0ddcfcd4aacba3
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "39615262"
---
# <a name="lines-and-stroke-caps"></a>Linee ed estremità dei tratti

_Informazioni su come usare SkiaSharp per disegnare linee con estremità dei tratti diversi_

In SkiaSharp, il rendering di una singola riga è molto diverso dal rendering di una serie di linee rette collegate. Anche quando si disegnano le singole righe, tuttavia, è spesso necessario assegnare le righe uno spessore particolare. Man mano che queste righe diventano più ampie, diventa importante anche l'aspetto delle entità finali delle righe. L'aspetto della fine della riga viene chiamato il *estremità della traccia*:

![](lines-images/strokecapsexample.png "Le opzioni di limiti tre tratto")

Per disegnare singole righe, `SKCanvas` definisce una semplice [ `DrawLine` ](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) metodo cui argomenti indicano l'inizio e fine coordinate della riga con un `SKPaint` oggetto:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

Per impostazione predefinita, il [ `StrokeWidth` ](xref:SkiaSharp.SKPaint.StrokeWidth) proprietà di una nuova istanza `SKPaint` oggetto è 0, che ha lo stesso effetto di un valore di 1 il rendering di una riga di un pixel in spessore. Questo valore viene visualizzato fette molto sottile dispositivi ad alta risoluzione, ad esempio telefoni, pertanto è opportuno impostare il `StrokeWidth` su un valore maggiore. Ma quando si inizia a creare linee di spessore ridimensionabile, che genera un altro problema: modalità di avvio e finali di queste righe spesse di rendering?

L'aspetto dell'avvio e fine delle righe viene chiamato un *estremità della linea* o, in Skia, un *estremità della traccia*. La parola "limite" in questo contesto si riferisce a un tipo di hat &mdash; qualcosa che si trova all'estremità della linea. È impostato il [ `StrokeCap` ](xref:SkiaSharp.SKPaint.StrokeCap) proprietà della `SKPaint` oggetto in uno dei seguenti membri del [ `SKStrokeCap` ](xref:SkiaSharp.SKStrokeCap) enumerazione:

- `Butt` (predefinito)
- `Square`
- `Round`

Questi vengono illustrate più chiaramente con un programma di esempio. Il **SkiaSharp linee e i percorsi** sezione del [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programma inizia con una pagina intitolata **estremità dei tratti** base il [ `StrokeCapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs) classe. Questa pagina definisce una `PaintSurface` gestore eventi che consente di scorrere i tre membri del `SKStrokeCap` enumerazione, la visualizzazione sia il nome del membro dell'enumerazione e tracciare una linea con tale limite di traccia:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Center
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width / 2;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = textPaint.FontSpacing;

    foreach (SKStrokeCap strokeCap in Enum.GetValues(typeof(SKStrokeCap)))
    {
        // Display text
        canvas.DrawText(strokeCap.ToString(), xText, y, textPaint);
        y += textPaint.FontSpacing;

        // Display thick line
        thickLinePaint.StrokeCap = strokeCap;
        canvas.DrawLine(xLine1, y, xLine2, y, thickLinePaint);

        // Display thin line
        canvas.DrawLine(xLine1, y, xLine2, y, thinLinePaint);
        y += 2 * textPaint.FontSpacing;
    }
}
```

Per ogni membro del `SKStrokeCap` enumerazione, il gestore disegnate due linee, uno con uno spessore del tratto pari a 50 pixel e un'altra riga posizionata nella parte superiore e con uno spessore del tratto di due pixel. Questa seconda riga è lo scopo di illustrare geometrica inizio e alla fine della riga di indipendenti di spessore della linea e un limite di traccia:

[![](lines-images/strokecaps-small.png "Tripla screenshot della pagina di estremità dei tratti")](lines-images/strokecaps-large.png#lightbox "tripla screenshot della pagina di estremità dei tratti")

Come può notare, il `Square` e `Round` estremità dei tratti estendere in modo efficace la lunghezza della riga per metà della larghezza di tratto all'inizio della riga e di nuovo alla fine. Questa estensione diventa importante quando è necessario determinare le dimensioni di un oggetto di rendering della grafica.

Il `SKCanvas` classe include anche un altro metodo per la creazione di più righe che è un po' particolare:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

Il `points` parametro è una matrice di `SKPoint` i valori e `mode` è un membro del [ `SKPointMode` ](xref:SkiaSharp.SKPointMode) enumerazione, che dispone di tre membri:

- `Points` Per eseguire il rendering dei singoli punti
- `Lines` Per connettersi ogni coppia di punti
- `Polygon` Per connettere tutti i punti consecutivi

Il **più righe** pagina viene illustrato questo metodo. Il [ **MultipleLinesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml) file crea un'istanza di due `Picker` viste che consentono di selezionare un membro del `SKPointMode` enumerazione e un membro del `SKStrokeCap` enumerazione:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.MultipleLinesPage"
             Title="Multiple Lines">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="pointModePicker"
                Title="Point Mode"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPointMode}">
                    <x:Static Member="skia:SKPointMode.Points" />
                    <x:Static Member="skia:SKPointMode.Lines" />
                    <x:Static Member="skia:SKPointMode.Polygon" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
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

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

Si noti che le dichiarazioni dello spazio dei nomi di SkiaSharp sono un po' diverso perché le `SkiaSharp` dello spazio dei nomi è necessaria per fare riferimento ai membri del `SKPointMode` e `SKStrokeCap` enumerazioni. Il `SelectedIndexChanged` gestore per entrambi `Picker` viste semplicemente invalida il `SKCanvasView` oggetto:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Deve verificare l'esistenza di questo gestore il `SKCanvasView` oggetto perché è il primo gestore dell'evento viene chiamato quando il `SelectedIndex` proprietà delle `Picker` è impostato su 0 nel file XAML, e che si verifica prima il `SKCanvasView` è stata creata un'istanza.

Il `PaintSurface` gestore ottiene i due valori di enumerazione dal `Picker` viste:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create an array of points scattered through the page
    SKPoint[] points = new SKPoint[10];

    for (int i = 0; i < 2; i++)
    {
        float x = (0.1f + 0.8f * i) * info.Width;

        for (int j = 0; j < 5; j++)
        {
            float y = (0.1f + 0.2f * j) * info.Height;
            points[2 * j + i] = new SKPoint(x, y);
        }
    }

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.DarkOrchid,
        StrokeWidth = 50,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = (SKPointMode)pointModePicker.SelectedItem;
    canvas.DrawPoints(pointMode, points, paint);
}
```

Lo screenshot Mostra numerosi `Picker` selezioni sulle tre piattaforme:

[![](lines-images/multiplelines-small.png "Tripla screenshot della pagina di più righe")](lines-images/multiplelines-large.png#lightbox "tripla screenshot della pagina di più righe")

L'iPhone a sinistra illustra come la `SKPointMode.Points` fa sì che il membro di enumerazione `DrawPoints` per eseguire il rendering di ognuno dei punti nel `SKPoint` matrice come un quadrato se è il delimitatore di linea `Butt` o `Square`. Cerchi vengono sottoposti a rendering se è il delimitatore di linea `Round`.

Quando si usa invece `SKPointMode.Lines`, come illustrato nella schermata Android nel centro, il `DrawPoints` metodo disegna una linea tra ogni coppia di `SKPoint` i valori, utilizzando la terminazione di riga specificata, in questo caso `Round`.

La schermata UWP Mostra il risultato del `SKPointMode.Polygon` valore. Viene tracciata una linea tra i punti consecutivi nella matrice, ma se si osserva attentamente, si noterà che le righe seguenti non sono connessi. Ognuna di queste righe separate inizia e termina con la terminazione di riga specificato. Se si seleziona il `Round` BLOC MAIUSC, le righe potrebbero sembrare siano connessi, ma davvero non sono connesse.

Se le righe sono connesso o non connesso è un aspetto fondamentale di utilizzo dei percorsi di elementi grafici.


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
