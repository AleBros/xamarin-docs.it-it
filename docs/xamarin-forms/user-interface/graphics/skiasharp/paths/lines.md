---
title: Linee ed estremità dei tratti
description: Questo articolo illustra come usare SkiaSharp per disegnare linee con estremità dei tratti diversi nelle applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 9aaecb8c63ff28111097dce81954f523b4c7731b
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292911"
---
# <a name="lines-and-stroke-caps"></a>Linee ed estremità dei tratti

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Informazioni su come usare SkiaSharp per tracciare linee con diverse estremità del tratto_

In SkiaSharp, il rendering di una singola riga è molto diverso dal rendering di una serie di linee rette collegate. Anche quando si disegnano le singole righe, tuttavia, è spesso necessario assegnare le righe uno spessore particolare. Man mano che queste righe diventano più ampie, diventa importante anche l'aspetto delle entità finali delle righe. L'aspetto della fine della linea è denominato *limite del tratto*:

![](lines-images/strokecapsexample.png "The three stroke caps options")

Per il disegno di singole righe, `SKCanvas` definisce un metodo di [`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) semplice i cui argomenti indicano le coordinate di inizio e fine della riga con un oggetto `SKPaint`:

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

Per impostazione predefinita, la proprietà [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) di un oggetto `SKPaint` di cui è stata creata un'istanza è uguale a 0, che ha lo stesso effetto di un valore pari a 1 per il rendering di una riga di un pixel di spessore. Questo aspetto è molto sottile nei dispositivi ad alta risoluzione, ad esempio i telefoni, quindi è probabile che si voglia impostare il `StrokeWidth` su un valore più grande. Ma quando si inizia a creare linee di spessore ridimensionabile, che genera un altro problema: modalità di avvio e finali di queste righe spesse di rendering?

L'aspetto delle parti iniziale e finale delle linee viene definito *estremità della linea* o, in Skia, un *limite del tratto*. Il termine "Cap" in questo contesto si riferisce a un tipo di Hat &mdash; qualcosa che si trova alla fine della riga. Impostare la proprietà [`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap) dell'oggetto `SKPaint` su uno dei seguenti membri dell'enumerazione [`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap) :

- `Butt` (impostazione predefinita)
- `Square`
- `Round`

Questi vengono illustrate più chiaramente con un programma di esempio. La sezione **SkiaSharp Lines and Paths** del programma [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) inizia con una pagina denominata **caps del tratto** basata sulla classe [`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs) . Questa pagina definisce un gestore dell'evento `PaintSurface` che esegue il ciclo dei tre membri dell'enumerazione `SKStrokeCap`, visualizzando sia il nome del membro di enumerazione che il disegno di una linea usando tale limite del tratto:

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

Per ogni membro dell'enumerazione `SKStrokeCap`, il gestore disegna due righe, una con uno spessore del tratto di 50 pixel e un'altra riga posizionata sopra con uno spessore del tratto di due pixel. Questa seconda riga è lo scopo di illustrare geometrica inizio e alla fine della riga di indipendenti di spessore della linea e un limite di traccia:

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

Come si può notare, i tappi `Square` e `Round` Stroke estendono in modo efficace la lunghezza della linea per metà della larghezza del tratto all'inizio della riga e di nuovo alla fine. Questa estensione diventa importante quando è necessario determinare le dimensioni di un oggetto di rendering della grafica.

La classe `SKCanvas` include anche un altro metodo per disegnare più righe che sono alquanto particolari:

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

Il `points` parametro è una matrice di valori `SKPoint` e `mode` è un membro dell'enumerazione [`SKPointMode`](xref:SkiaSharp.SKPointMode) , che dispone di tre membri:

- `Points` per eseguire il rendering dei singoli punti
- `Lines` per connettere ogni coppia di punti
- `Polygon` per connettere tutti i punti consecutivi

Questo metodo viene illustrato nella pagina **più righe** . Il file [**MultipleLinesPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml) crea un'istanza di due viste `Picker` che consentono di selezionare un membro dell'enumerazione `SKPointMode` e un membro dell'enumerazione `SKStrokeCap`:

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

Si noti che le dichiarazioni dello spazio dei nomi SkiaSharp sono leggermente diverse perché lo spazio dei nomi `SkiaSharp` è necessario per fare riferimento ai membri delle enumerazioni `SKPointMode` e `SKStrokeCap`. Il gestore `SelectedIndexChanged` per entrambe le visualizzazioni `Picker` invalida semplicemente l'oggetto `SKCanvasView`:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

Questo gestore deve verificare l'esistenza dell'oggetto `SKCanvasView` perché il gestore eventi viene chiamato per la prima volta quando la proprietà `SelectedIndex` della `Picker` è impostata su 0 nel file XAML e che si verifica prima che venga creata un'istanza del `SKCanvasView`.

Il gestore `PaintSurface` ottiene i due valori di enumerazione dalle visualizzazioni `Picker`:

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

Gli screenshot mostrano un'ampia gamma di selezioni `Picker`:

[![](lines-images/multiplelines-small.png "Triple screenshot of the Multiple Lines page")](lines-images/multiplelines-large.png#lightbox "Triple screenshot of the Multiple Lines page")

L'iPhone a sinistra Mostra come il membro di enumerazione `SKPointMode.Points` causa `DrawPoints` di eseguire il rendering di ognuno dei punti della matrice `SKPoint` come quadrato se il limite di riga è `Butt` o `Square`. Viene eseguito il rendering dei cerchi se il limite di riga è `Round`.

Lo screenshot Android Mostra il risultato della `SKPointMode.Lines`. Il metodo `DrawPoints` disegna una linea tra ogni coppia di valori di `SKPoint`, usando il limite di riga specificato, in questo caso `Round`.

Quando si usa invece `SKPointMode.Polygon`, viene disegnata una linea tra i punti successivi della matrice, ma se si osserva molto attentamente, si noterà che queste righe non sono connesse. Ognuna di queste righe separate inizia e termina con la terminazione di riga specificato. Se si selezionano i `Round` Caps, le linee potrebbero sembrare connesse, ma non sono effettivamente connesse.

Se le righe sono connesso o non connesso è un aspetto fondamentale di utilizzo dei percorsi di elementi grafici.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
