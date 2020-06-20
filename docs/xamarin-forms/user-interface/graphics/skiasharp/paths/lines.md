---
title: Linee ed estremità dei tratti
description: In questo articolo viene illustrato come utilizzare SkiaSharp per tracciare linee con diversi limiti di tratto nelle Xamarin.Forms applicazioni e come illustrato nel codice di esempio.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 87b97ad913e08c42d16bbf055f168c07b9bd60e8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137206"
---
# <a name="lines-and-stroke-caps"></a>Linee ed estremità dei tratti

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Informazioni su come usare SkiaSharp per tracciare linee con diverse estremità del tratto_

In SkiaSharp, il rendering di una singola riga è molto diverso dal rendering di una serie di linee rette collegate. Anche quando si disegnano singole righe, tuttavia, spesso è necessario assegnare alle righe una particolare larghezza del tratto. Man mano che queste righe diventano più larghe, anche l'aspetto delle estremità delle linee diventa importante. L'aspetto della fine della linea è denominato *limite del tratto*:

![](lines-images/strokecapsexample.png "The three stroke caps options")

Per il disegno di singole righe, `SKCanvas` definisce un metodo semplice i [`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) cui argomenti indicano le coordinate di inizio e fine della riga con un `SKPaint` oggetto:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

Per impostazione predefinita, la [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) proprietà di un oggetto di cui è stata creata un'istanza `SKPaint` è uguale a 0, che ha lo stesso effetto di un valore pari a 1 per il rendering di una riga di un pixel nello spessore. Questo aspetto è molto sottile nei dispositivi ad alta risoluzione, ad esempio i telefoni, quindi è probabile che si desideri impostare `StrokeWidth` su un valore più grande. Tuttavia, una volta che si inizia a disegnare righe di uno spessore considerevole, viene generato un altro problema: come devono essere visualizzati i punti di inizio e di fine di queste linee spesse?

L'aspetto delle parti iniziale e finale delle linee viene definito *estremità della linea* o, in Skia, un *limite del tratto*. La parola "Cap" in questo contesto si riferisce a un tipo di Hat &mdash; che si trova alla fine della riga. Impostare la [`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap) proprietà dell' `SKPaint` oggetto su uno dei seguenti membri dell' [`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap) enumerazione:

- `Butt`(impostazione predefinita)
- `Square`
- `Round`

Queste procedure sono illustrate in modo ottimale con un programma di esempio. La sezione **SkiaSharp Lines and Paths** del programma [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) inizia con una pagina denominata **caps del tratto** basata sulla [`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs) classe. Questa pagina definisce un `PaintSurface` gestore eventi che esegue il ciclo dei tre membri dell' `SKStrokeCap` enumerazione, visualizzando sia il nome del membro di enumerazione che il disegno di una linea usando il limite del tratto seguente:

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

Per ogni membro dell' `SKStrokeCap` enumerazione, il gestore disegna due righe, una con uno spessore del tratto di 50 pixel e un'altra riga posizionata sopra con uno spessore del tratto di due pixel. Questa seconda riga ha lo scopo di illustrare l'inizio e la fine geometrica della linea indipendentemente dallo spessore della linea e da un limite del tratto:

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

Come si può notare, i `Square` `Round` caps del tratto e estendono efficacemente la lunghezza della riga di metà della larghezza del tratto all'inizio della riga e di nuovo alla fine. Questa estensione diventa importante quando è necessario determinare le dimensioni di un oggetto grafico sottoposto a rendering.

La `SKCanvas` classe include anche un altro metodo per disegnare più righe che sono alquanto particolari:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

Il `points` parametro è una matrice di `SKPoint` valori ed `mode` è un membro dell' [`SKPointMode`](xref:SkiaSharp.SKPointMode) enumerazione, che dispone di tre membri:

- `Points`per eseguire il rendering dei singoli punti
- `Lines`per connettere ogni coppia di punti
- `Polygon`per connettere tutti i punti consecutivi

Questo metodo viene illustrato nella pagina **più righe** . Il file [**MultipleLinesPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml) crea un'istanza di due `Picker` visualizzazioni che consentono di selezionare un membro dell' `SKPointMode` enumerazione e un membro dell' `SKStrokeCap` enumerazione:

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

Si noti che le dichiarazioni dello spazio dei nomi SkiaSharp sono leggermente diverse perché lo `SkiaSharp` spazio dei nomi è necessario per fare riferimento ai membri delle `SKPointMode` `SKStrokeCap` enumerazioni e. Il `SelectedIndexChanged` gestore per entrambe `Picker` le visualizzazioni invalida semplicemente l' `SKCanvasView` oggetto:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Questo gestore deve verificare l'esistenza dell' `SKCanvasView` oggetto perché il gestore eventi viene chiamato per la prima volta quando la `SelectedIndex` proprietà di `Picker` è impostata su 0 nel file XAML e che si verifica prima che `SKCanvasView` sia stata creata un'istanza di.

Il `PaintSurface` gestore ottiene i due valori di enumerazione dalle `Picker` viste:

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

Gli screenshot mostrano un'ampia gamma di `Picker` selezioni:

[![](lines-images/multiplelines-small.png "Triple screenshot of the Multiple Lines page")](lines-images/multiplelines-large.png#lightbox "Triple screenshot of the Multiple Lines page")

L'iPhone a sinistra Mostra come il `SKPointMode.Points` membro di enumerazione fa in modo `DrawPoints` che esegua il rendering di ognuno dei punti della `SKPoint` matrice come quadrato se il limite di riga è `Butt` o `Square` . Il rendering dei cerchi viene eseguito se il limite di riga è `Round` .

Lo screenshot Android Mostra il risultato di `SKPointMode.Lines` . Il `DrawPoints` metodo disegna una linea tra ogni coppia di `SKPoint` valori, usando il limite di riga specificato, in questo caso `Round` .

Quando invece si usa `SKPointMode.Polygon` , viene disegnata una linea tra i punti successivi della matrice, ma se si osserva molto attentamente, si noterà che queste righe non sono connesse. Ognuna di queste righe separate inizia e termina con il limite di riga specificato. Se si selezionano i `Round` Caps, le linee potrebbero sembrare connesse, ma non sono effettivamente connesse.

Il fatto che le linee siano connesse o non connesse sia un aspetto cruciale dell'uso dei percorsi grafici.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
