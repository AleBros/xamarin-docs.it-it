---
title: Tipi di riempimento dei tracciati
description: Questo articolo esamina i diversi effetti possibili con i tipi di riempimento di SkiaSharp percorso e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 98081ed1a9aef1260150671d4fd026dd64c20b62
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292996"
---
# <a name="the-path-fill-types"></a>Tipi di riempimento dei tracciati

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Individuare i diversi effetti possibili con i tipi di riempimento del percorso SkiaSharp_

Può essere sovrapposto due distribuzioni in un percorso e le righe che compongono una distribuzione del singolo possono sovrapporsi. Può quindi essere riempito potenzialmente qualsiasi area inclusa, ma è possibile evitare di riempire tutte le aree racchiusi. Ad esempio:

![](fill-types-images/filltypeexample.png "Five-pointed star partially filles")

È necessario un po' controllare. L'algoritmo di riempimento è regolato dalla proprietà [`SKFillType`](xref:SkiaSharp.SKPath.FillType) di `SKPath`, impostata su un membro dell'enumerazione [`SKPathFillType`](xref:SkiaSharp.SKPathFillType) :

- `Winding`, il valore predefinito
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

Entrambi gli algoritmi dei vertici e coppie determinano se qualsiasi area racchiusa viene compilata o non compilato in base a un'ipotetica linea tracciata da quell'area verso l'infinito. Tale riga attraversa uno o più righe di limiti che costituiscono il percorso. Con la modalità dei vertici, se non è il numero di linee dei bordi disegnata in equilibrio di una sola direzione il numero delle linee disegnate in altra direzione, l'area riempita. In caso contrario, l'area viene riempita. L'algoritmo coppie riempie un'area, se il numero di righe di limiti è dispari.

Con tutti i percorsi di routine, l'algoritmo dei vertici riempie spesso tutte le aree racchiusa di un percorso. L'algoritmo coppie produce in genere risultati più interessanti.

L'esempio classico è una stella a cinque punte, come illustrato nella pagina a **cinque punte** . Il file [**FivePointedStarPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FivePointedStarPage.xaml) crea un'istanza di due viste `Picker` per selezionare il tipo di riempimento del percorso e indica se il percorso è tracciato o riempito o se il percorso è in ordine:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.FivePointedStarPage"
             Title="Five-Pointed Star">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="fillTypePicker"
                Title="Path Fill Type"
                Grid.Row="0"
                Grid.Column="0"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPathFillType}">
                    <x:Static Member="skia:SKPathFillType.Winding" />
                    <x:Static Member="skia:SKPathFillType.EvenOdd" />
                    <x:Static Member="skia:SKPathFillType.InverseWinding" />
                    <x:Static Member="skia:SKPathFillType.InverseEvenOdd" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="drawingModePicker"
                Title="Drawing Mode"
                Grid.Row="0"
                Grid.Column="1"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Fill only</x:String>
                    <x:String>Stroke only</x:String>
                    <x:String>Stroke then Fill</x:String>
                    <x:String>Fill then Stroke</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2"
                                PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Il file code-behind usa entrambi i valori `Picker` per creare una stella a cinque punte:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPath path = new SKPath
    {
        FillType = (SKPathFillType)fillTypePicker.SelectedItem
    };
    path.MoveTo(info.Width / 2, info.Height / 2 - radius);

    for (int i = 1; i < 5; i++)
    {
        // angle from vertical
        double angle = i * 4 * Math.PI / 5;
        path.LineTo(center + new SKPoint(radius * (float)Math.Sin(angle),
                                        -radius * (float)Math.Cos(angle)));
    }
    path.Close();

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 50,
        StrokeJoin = SKStrokeJoin.Round
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    switch ((string)drawingModePicker.SelectedItem)
    {
        case "Fill only":
            canvas.DrawPath(path, fillPaint);
            break;

        case "Stroke only":
            canvas.DrawPath(path, strokePaint);
            break;

        case "Stroke then Fill":
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case "Fill then Stroke":
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

In genere, il tipo di riempimento del percorso deve influire solo sui riempimenti e non sui tratti, ma le due modalità `Inverse` influiscono sia sui riempimenti che sui tratti. Per Fill, i due `Inverse` tipi riempiono le aree opposte in modo che l'area esterna alla stella venga riempita. Per i tratti, i due tipi di `Inverse` colorano tutti gli elementi, ad eccezione del tratto. Utilizzo di questi tipi di riempimento inverso può produrre alcuni effetti dispari, come illustrato nella schermata di iOS:

[![](fill-types-images/fivepointedstar-small.png "Triple screenshot of the Five-Pointed Star page")](fill-types-images/fivepointedstar-large.png#lightbox "Triple screenshot of the Five-Pointed Star page")

Lo screenshot Android Mostra gli effetti tipici anche dispari e di avvolgimento, ma anche l'ordine del tratto e del riempimento influisce sui risultati.

La direzione che vengono visualizzate linee dipende l'algoritmo dei vertici. In genere quando si crea un percorso, è possibile controllare tale direzione come specificato da righe sono rappresentate da un punto a altro. Tuttavia, la classe `SKPath` definisce anche metodi come `AddRect` e `AddCircle` che delineano interi contorni. Per controllare il modo in cui vengono disegnati questi oggetti, i metodi includono un parametro di tipo [`SKPathDirection`](xref:SkiaSharp.SKPathDirection), che dispone di due membri:

- `Clockwise`
- `CounterClockwise`

I metodi in `SKPath` che includono un parametro di `SKPathDirection` forniscono un valore predefinito di `Clockwise`.

Nella pagina **cerchi sovrapposti** viene creato un tracciato con quattro cerchi sovrapposti con un tipo di riempimento percorso pari:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(info.Width, info.Height) / 4;

    SKPath path = new SKPath
    {
        FillType = SKPathFillType.EvenOdd
    };

    path.AddCircle(center.X - radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X - radius / 2, center.Y + radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y + radius / 2, radius);

    SKPaint paint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    canvas.DrawPath(path, paint);

    paint.Style = SKPaintStyle.Stroke;
    paint.StrokeWidth = 10;
    paint.Color = SKColors.Magenta;

    canvas.DrawPath(path, paint);
}
```

È un'immagine interessa creata con poche righe di codice:

[![](fill-types-images/overlappingcircles-small.png "Triple screenshot of the Overlapping Circles page")](fill-types-images/overlappingcircles-large.png#lightbox "Triple screenshot of the Overlapping Circles page")

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
