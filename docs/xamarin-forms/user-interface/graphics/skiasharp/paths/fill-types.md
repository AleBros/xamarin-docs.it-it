---
title: Tipi di riempimento dei tracciati
description: Questo articolo esamina i diversi effetti possibili con i tipi di riempimento del percorso SkiaSharp e ne illustra il codice di esempio.
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c8c54f3d3815e418d2f71960dc7733711cb40ae2
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139048"
---
# <a name="the-path-fill-types"></a>Tipi di riempimento dei tracciati

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Individuare i diversi effetti possibili con i tipi di riempimento del percorso SkiaSharp_

Due contorni in un percorso possono sovrapporsi e le linee che compongono un singolo contorno possono sovrapporsi. Qualsiasi area racchiusa può essere compilata, ma potrebbe non essere necessario riempire tutte le aree racchiuse. Ad esempio:

![](fill-types-images/filltypeexample.png "Five-pointed star partially filles")

Si ha un piccolo controllo su questo. L'algoritmo di riempimento è regolato dalla [`SKFillType`](xref:SkiaSharp.SKPath.FillType) proprietà di `SKPath` , impostata su un membro dell' [`SKPathFillType`](xref:SkiaSharp.SKPathFillType) enumerazione:

- `Winding`, il valore predefinito
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

Sia gli algoritmi di avvolgimento che quelli dispari determinano se un'area racchiusa viene riempita o non riempita in base a una linea ipotetica disegnata da tale area a infinito. Tale riga incrocia una o più linee di limite che costituiscono il percorso. Con la modalità di avvolgimento, se il numero di linee limite disegnate in una direzione bilancia il numero di linee disegnate nell'altra direzione, l'area non viene riempita. In caso contrario, viene riempita l'area. L'algoritmo Even-Odd riempie un'area se il numero di linee limite è dispari.

Con molti percorsi di routine, l'algoritmo di Winding riempie spesso tutte le aree racchiuse di un percorso. L'algoritmo Even-Odd produce in genere risultati più interessanti.

L'esempio classico è una stella a cinque punte, come illustrato nella pagina a **cinque punte** . Il file [**FivePointedStarPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FivePointedStarPage.xaml) crea un'istanza `Picker` di due visualizzazioni per selezionare il tipo di riempimento del percorso e indica se il percorso è tracciato o riempito o se il percorso è in ordine:

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

Il file code-behind usa entrambi `Picker` i valori per creare una stella a cinque punte:

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

In genere, il tipo di riempimento del percorso deve influire solo sui riempimenti e non sui tratti, ma le due `Inverse` modalità influiscono su riempimenti e tratti. Per Fill, i due `Inverse` tipi riempiono le aree opposte in modo che l'area esterna alla stella venga riempita. Per i tratti, i due `Inverse` tipi colorano tutti gli elementi, ad eccezione del tratto. L'uso di questi tipi di riempimento inversi può produrre alcuni effetti anomali, come illustrato nella schermata iOS:

[![](fill-types-images/fivepointedstar-small.png "Triple screenshot of the Five-Pointed Star page")](fill-types-images/fivepointedstar-large.png#lightbox "Triple screenshot of the Five-Pointed Star page")

Lo screenshot Android Mostra gli effetti tipici anche dispari e di avvolgimento, ma anche l'ordine del tratto e del riempimento influisce sui risultati.

L'algoritmo di Winding dipende dalla direzione con cui vengono disegnate le linee. In genere, quando si crea un percorso, è possibile controllare tale direzione quando si specifica che le linee vengono disegnate da un punto a un altro. Tuttavia, la `SKPath` classe definisce anche metodi come `AddRect` e `AddCircle` che attingono interi Contour. Per controllare il modo in cui vengono disegnati questi oggetti, i metodi includono un parametro di tipo [`SKPathDirection`](xref:SkiaSharp.SKPathDirection) , che dispone di due membri:

- `Clockwise`
- `CounterClockwise`

I metodi in `SKPath` che includono un `SKPathDirection` parametro forniscono un valore predefinito di `Clockwise` .

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

Si tratta di un'immagine interessante creata con un minimo di codice:

[![](fill-types-images/overlappingcircles-small.png "Triple screenshot of the Overlapping Circles page")](fill-types-images/overlappingcircles-large.png#lightbox "Triple screenshot of the Overlapping Circles page")

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
