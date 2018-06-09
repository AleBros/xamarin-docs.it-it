---
title: I tipi di riempimento del percorso
description: In questo articolo esamina gli effetti diversi possibili con tipi di riempimento percorso SkiaSharp e viene illustrato quanto descritto con codice di esempio.
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: d54ebd157fcc76b0fcc15bf89c72edbcd88b42f2
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243707"
---
# <a name="the-path-fill-types"></a>I tipi di riempimento del percorso

_Individuare gli effetti di diversi possibili con tipi di riempimento SkiaSharp percorso_

Possono sovrapporsi due contorni in un percorso e le righe che costituiscono una singola distribuzione possono sovrapporsi. Può quindi essere riempito potenzialmente qualsiasi area inclusa, ma potrebbe non vuoi compilare tutte le aree tra parentesi. Di seguito è riportato un esempio:

![](fill-types-images/filltypeexample.png "Punta a cinque stelle parzialmente filles")

È un po' preciso. L'algoritmo di riempimento è disciplinato dal [ `SKFillType` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.FillType/) proprietà di `SKPath`, che è impostato su un membro del [ `SKPathFillType` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathFillType/) enumerazione:

- [`Winding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.Winding/), il valore predefinito
- [`EvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.EvenOdd/)
- [`InverseWinding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseWinding/)
- [`InverseEvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseEvenOdd/)

Entrambi gli algoritmi culling e coppie determinano se qualsiasi area racchiusa viene compilato o non è compilato in base a una linea ipotetica area all'infinito. La riga supera una o più righe di limiti che costituiscono il percorso. Con la modalità continua, il numero di righe limite disegnato il saldo di una sola direzione il numero delle linee disegnate in altra direzione, quindi l'area non è compilato. In caso contrario, viene riempita l'area. L'algoritmo coppie riempie un'area, se il numero di linee è dispari.

Con tutti i percorsi di routine, l'algoritmo culling riempie spesso tutte le aree di un percorso tra parentesi. L'algoritmo coppie produce in genere risultati più significativi.

Il classico esempio è una stella di cinque checkpoint, come dimostrato nel **Five-Pointed Star** pagina. Il [FivePointedStarPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FivePointedStarPage.xaml) file crea due `Picker` viste per selezionare il percorso di riempimento di tipo e se il percorso è tracciato o compilato o entrambe e in quale ordine:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.FivePointedStarPage"
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
            <Picker.Items>
                <x:String>Winding</x:String>
                <x:String>EvenOdd</x:String>
                <x:String>InverseWinding</x:String>
                <x:String>InverseEvenOdd</x:String>
            </Picker.Items>
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
            <Picker.Items>
                <x:String>Fill only</x:String>
                <x:String>Stroke only</x:String>
                <x:String>Stroke then Fill</x:String>
                <x:String>Fill then Stroke</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Il file code-behind utilizza sia `Picker` valori per disegnare una stella di cinque checkpoint:

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
        FillType = (SKPathFillType)Enum.Parse(typeof(SKPathFillType),
                        fillTypePicker.Items[fillTypePicker.SelectedIndex])
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

    switch (drawingModePicker.SelectedIndex)
    {
        case 0:
            canvas.DrawPath(path, fillPaint);
            break;

        case 1:
            canvas.DrawPath(path, strokePaint);
            break;

        case 2:
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case 3:
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

Il tipo di riempimento del percorso deve riguardano in genere, solo operazioni di inserimento e non i tratti, ma i due `Inverse` influiscono sulla modalità sia riempimenti e tracce. Per operazioni di inserimento, i due `Inverse` tipi riempire le aree oppositely in modo che l'area di fuori di stella viene riempita. Per i tratti, i due `Inverse` tipi colore tutto ad eccezione del tratto. Uso di questi tipi di riempimento inverso può produrre alcuni effetti dispari, come illustrato nella schermata di iOS:

[![](fill-types-images/fivepointedstar-small.png "Schermata triplo della pagina a stella Five-Pointed")](fill-types-images/fivepointedstar-large.png#lightbox "tripla schermata della pagina Five-Pointed stella")

Le schermate di Android e UWP mostrano gli effetti di coppie e culling tipico, ma l'ordine del tratto e riempimento influisce inoltre sui risultati.

L'algoritmo è dipendente dalla direzione che vengono visualizzate linee. In genere quando si crea un percorso, è possibile controllare la direzione come specificare che le linee vengono disegnate da un punto a altro. Tuttavia, il `SKPath` classe definisce inoltre metodi, ad esempio `AddRect` e `AddCircle` che disegna l'intera distribuzione. Per controllare la modalità in cui vengono disegnati questi oggetti, i metodi includono un parametro di tipo [ `SKPathDirection` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathDirection/), che contiene due membri:

- [`Clockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.Clockwise/)
- [`CounterClockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.CounterClockwise/)

I metodi in `SKPath` che includono un `SKPathDirection` parametro assegnargli un valore predefinito di `Clockwise`.

Il **cerchi sovrapposti** pagina Crea un percorso con quattro cerchi sovrapposti con un tipo di riempimento di coppie di percorso:

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

È un'immagine interessano creata con un minimo di codice:

[![](fill-types-images/overlappingcircles-small.png "Schermata di triplo della pagina cerchi sovrapposti")](fill-types-images/overlappingcircles-large.png#lightbox "tripla schermata della pagine dei cerchi sovrapposti")


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
