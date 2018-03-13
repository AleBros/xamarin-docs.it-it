---
title: Le righe e maiuscole tratto
description: "Informazioni su come usare SkiaSharp per disegnare linee con estremità diversi"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: abcda680f6cfbde802f7b666cf2aade2c6e11093
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="lines-and-stroke-caps"></a>Le righe e maiuscole tratto

_Informazioni su come usare SkiaSharp per disegnare linee con estremità diversi_

In SkiaSharp, il rendering di una singola riga è molto diverso da eseguire il rendering di una serie di linee rette collegate. Anche durante il disegno di linee, tuttavia, è spesso necessario assegnare le righe di un valore specifico e maggiori sono la riga, più importante diventa l'aspetto della fine delle righe, chiamare il *estremità del tratto*:

![](lines-images/strokecapsexample.png "Le opzioni di tre traccia maiuscole")

Per il disegno di linee, `SKCanvas` definisce un semplice [ `DrawLine` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawLine/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/) metodo i cui argomenti indicano l'inizio e fine delle coordinate di riga con un `SKPaint` oggetto:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

Per impostazione predefinita, il `StrokeWidth` proprietà di un appena creata un'istanza `SKPaint` oggetto è 0, che ha lo stesso effetto di un valore di 1 per il rendering di una riga di un pixel spessore. Questo valore viene visualizzato molto sottile dispositivi ad alta risoluzione, ad esempio telefoni, pertanto è opportuno impostare il `StrokeWidth` su un valore maggiore. Ma una volta avviato il disegno di linee di spessore ridimensionabile, che genera un altro problema: come deve nel inizia e termina di queste righe spessore visualizzato?

L'aspetto del inizio e fine delle righe viene chiamato un *estremità di linea* o Skia, un *estremità del tratto*. La parola "cap" in questo contesto si riferisce a un tipo di hat & #x 2014; un elemento che si trova all'estremità della linea. Impostare il [ `StrokeCap` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeCap/) proprietà del `SKPaint` oggetto in uno dei seguenti membri del [ `SKStrokeCap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeCap/) enumerazione:

- [`Butt`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Butt/) (predefinito)
- [`Square`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)

Questi sono illustrate più chiaramente con un programma di esempio. La seconda sezione della home page del [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programma inizia con una pagina denominata **maiuscole tratto** in base il [ `StrokeCapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs) classe. Questa pagina definisce un `PaintSurface` gestore eventi che esegue il ciclo di tre membri del `SKStrokeCap` enumerazione, la visualizzazione sia il nome del membro di enumerazione e creazione di una linea che estremità della traccia utilizzando:

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

Per ogni membro del `SKStrokeCap` enumerazione, il gestore crea due righe, una con uno spessore del tratto di 50 pixel e un'altra riga posizionata nella parte superiore con uno spessore del tratto di 2 pixel. Questa seconda riga è destinata a illustrare geometrica inizio e alla fine della riga indipendente di spessore della linea e un'estremità della traccia:

[![](lines-images/strokecaps-small.png "Schermata triplo della pagina maiuscole tratto")](lines-images/strokecaps-large.png#lightbox "tripla schermata della pagina maiuscole tratto")

Come si può notare, la `Square` e `Round` maiuscole tratto estendono in modo efficace la lunghezza della riga per metà larghezza del tratto all'inizio della riga e di nuovo alla fine. Questa estensione diventa importante quando è necessario determinare le dimensioni di un oggetto graphics sottoposto a rendering.

La `SKCanvas` classe include anche un altro metodo per la creazione di più righe in qualche modo particolare:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

Il `points` parametro è una matrice di `SKPoint` valori e `mode` è membro il [ `SKPointMode` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPointMode/) enumerazione, che dispone di tre membri:

- [`Points`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Points/) Per eseguire il rendering dei singoli punti
- [`Lines`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Lines/) Per connettersi a ogni coppia di punti
- [`Polygon`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Polygon/) Per connettere tutti i punti consecutivi

Il **più righe** è illustrato questo metodo. Il [ `MultipleLinesPage` file XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml) un'istanza di due `Picker` visualizzazioni che consentono di selezionare un membro del `SKPointMode` enumerazione e un membro del `SKStrokeCap` enumerazione:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.MultipleLinesPage"
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
            <Picker.Items>
                <x:String>Points</x:String>
                <x:String>Lines</x:String>
                <x:String>Polygon</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
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

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

Il `SelectedIndexChanged` gestore sia per `Picker` viste invalida il `SKCanvasView` oggetto:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Deve verificare l'esistenza di questo gestore di `SKCanvasView` oggetto perché il gestore eventi viene chiamato quando il `SelectedIndex` proprietà del `Picker` è impostato su 0 nel file XAML, e che si verifica prima il `SKCanvasView` istanza è stata creata.

Il `PaintSurface` gestore accede a un metodo generico per i due elementi selezionati da ottenere il `Picker` viste e convertirli in valori di enumerazione:

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
        StrokeCap = GetPickerItem<SKStrokeCap>(strokeCapPicker)
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = GetPickerItem<SKPointMode>(pointModePicker);
    canvas.DrawPoints(pointMode, points, paint);
}

T GetPickerItem<T>(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return default(T);
    }
    return (T)Enum.Parse(typeof(T), picker.Items[picker.SelectedIndex]);
}
```

Nella schermata mostra una vasta gamma di `Picker` selezioni nelle tre piattaforme:

[![](lines-images/multiplelines-small.png "Schermata di triplo della pagina più righe")](lines-images/multiplelines-large.png#lightbox "tripla schermata della pagina più righe")

L'iPhone a sinistra illustra il modo in `SKPointMode.Points` fa sì che il membro di enumerazione `DrawPoints` per eseguire il rendering di ognuno dei punti di `SKPoint` matrice come un quadrato, se la terminazione di riga è `Butt` o `Square`. Cerchi rendering vengono eseguiti se la terminazione di riga è `Round`.

Quando si utilizza invece `SKPointMode.Lines`, come illustrato nella schermata di Android nel centro di `DrawPoints` metodo disegna una linea tra ogni coppia di `SKPoint` valori, utilizzando il delimitatore di riga specificata, in questo caso `Round`.

Il dispositivo mobile Windows Mostra il risultato del `SKPointMode.Polygon` valore. Viene tracciata una linea tra i successivi punti nella matrice, ma se si osserva attentamente, si noterà che le righe seguenti non sono connessi. Ognuna di queste righe separate inizia e termina con il delimitatore di riga specificato. Se si seleziona il `Round` BLOC MAIUSC, le righe sembrano essere connessi, ma sono effettivamente non connessi.

Se le righe vengono connessi o non è connesso è un aspetto fondamentale di utilizzo dei percorsi di grafica.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
