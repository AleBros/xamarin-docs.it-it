---
title: Trasformazioni non affini
description: Questo articolo illustra come creare prospettive e gli effetti di rastremazione con la terza colonna della matrice di trasformazione e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: da820b0c48eaec52da76504b1aed8e9793c1e74d
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057272"
---
# <a name="non-affine-transforms"></a>Trasformazioni non affini

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Creare prospettive e gli effetti di rastremazione con la terza colonna della matrice di trasformazione_

Traslazione, ridimensionamento, rotazione e inclinazione sono classificati come *affini* Trasforma. Le trasformazioni affini mantengono linee parallele. Se le due righe sono parallele prima della trasformazione, rimangono parallele dopo la trasformazione. I rettangoli vengono sempre trasformati in parallelogrammi.

Tuttavia, è inoltre in grado di trasformazioni non affini, che hanno la possibilità di trasformare un rettangolo in qualsiasi quadrilatero convesso SkiaSharp:

![](non-affine-images/nonaffinetransformexample.png "Una bitmap trasformata in un quadrilatero convesso")

Un quadrilatero convesso è una figura i quattro lati con gli angoli interni sempre un valore minore di 180 gradi e le parti che non superano tra loro.

Non affini Trasforma risultato quando la terza riga della matrice di trasformazione è impostata su valori diversi da 0, 0 e 1. La versione completa `SKMatrix` moltiplicazione è:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

Le formule di trasformazione risultanti sono:

x' = ScaleX·x SkewX·y + TransX

y' = SkewY·x ScaleY·y + TransY

z' = Persp0·x Persp1·y + Persp2

La regola fondamentale dell'uso di una matrice 3 per 3 per le trasformazioni bidimensionali è che tutto ciò che rimane nel piano in cui Z è uguale a 1. A meno che `Persp0` e `Persp1` sono pari a 0, e `Persp2` è uguale a 1, la trasformazione ha spostato le coordinate Z tale piano.

Per ripristinare questo una trasformazione bidimensionale, le coordinate devono essere spostate nuovamente in tale piano. Un altro passaggio è obbligatorio. La x', y', e z 'valori devono essere diviso per z':

x" = x' / z'

'y = y' / z'

z" = z' / z' = 1

Si parla *coordinate omogenee* e sono state sviluppate da Ferdinand Möbius agosto, molto più noti per il suo stranezza topologico, si ipotizza la striscia Möbius.

Se z' è 0, i risultati di divisione in base alle coordinate infiniti. In effetti, una delle motivazioni del Möbius per lo sviluppo di coordinate omogenee era la possibilità di rappresentare i valori infiniti con numeri finiti.

La visualizzazione grafica, tuttavia, si desidera evitare il rendering di un elemento con coordinate che consentono di trasformare i valori infinito. Queste coordinate non verranno eseguito il rendering. Tutti gli elementi in prossimità di tali coordinate saranno molto grandi e probabilmente non visivamente coerente.

L'equazione, non si desidera il valore di z' che diventa zero:

z' = Persp0·x Persp1·y + Persp2

Di conseguenza, questi valori hanno alcune limitazioni pratiche: 

Il `Persp2` cella può essere zero o non zero. Se `Persp2` è zero, z' è pari a zero per il punto (0, 0) e che in genere non è consigliabile perché tale punto è molto comune nei grafici bidimensionali. Se `Persp2` non è uguale a zero, non vi è alcuna perdita di generalità se `Persp2` è fissato a 1. Ad esempio, se si determina che `Persp2` deve essere 5, quindi si possono dividere semplicemente tutte le celle della matrice da 5, che rende `Persp2` uguale a 1 e il risultato sarà lo stesso.

Per questi motivi, `Persp2` spesso è fissato a 1, ovvero lo stesso valore della matrice di identità.

In genere `Persp0` e `Persp1` sono un numero limitato. Ad esempio, si supponga di iniziare con una matrice di identità, ma con set `Persp0` pari a 0,01:

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

Le formule di trasformazione sono:

x' = x / (0.01·x + 1)

y' = y / (0.01·x + 1)

Ora è possibile usare questa trasformazione per eseguire il rendering di una casella quadrata 100 pixel posizionata in corrispondenza dell'origine. Ecco come vengono trasformati gli angoli di quattro:

(0, 0) → (0, 0)

(0, 100) → (0, 100)

(100, 0) → (50, 0)

(100, 100) → (50, 50)

Quando x è 100, quindi il valore z' denominatore è 2, in modo che le coordinate x e y sono effettivamente dimezzato. Il lato destro della casella diventa più breve rispetto a sinistra:

![](non-affine-images/nonaffinetransform.png "Una casella soggetto a una trasformazione non affine")

Il `Persp` parte di questi nomi cella fa riferimento a "Prospettiva" perché la prospettiva suggerisce che la casella è ora inclinata al lato destro del visualizzatore.

Il **prospettiva Test** pagina consente di provare a usare i valori di `Persp0` e `Pers1` per avere un'idea di come funzionano. Valori accettabili di queste celle della matrice sono così ridotte che di `Slider` nella piattaforma Windows universale non può gestire correttamente tali. Per supportare il problema della piattaforma UWP, i due `Slider` elementi nel [ **TestPerspective.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml) devono essere inizializzati all'intervallo da -1 a 1:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.TestPerspectivePage"
             Title="Test Perpsective">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Minimum" Value="-1" />
                    <Setter Property="Maximum" Value="1" />
                    <Setter Property="Margin" Value="20, 0" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="persp0Slider"
                Grid.Row="0"
                ValueChanged="OnPersp0SliderValueChanged" />

        <Label x:Name="persp0Label"
               Text="Persp0 = 0.0000"
               Grid.Row="1" />

        <Slider x:Name="persp1Slider"
                Grid.Row="2"
                ValueChanged="OnPersp1SliderValueChanged" />

        <Label x:Name="persp1Label"
               Text="Persp1 = 0.0000"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

I gestori eventi per i dispositivi di scorrimento nel [ `TestPerspectivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) file code-behind divida i valori da 100 in modo che è compreso tra 0,01 e –0.01. Inoltre, il costruttore carica in una mappa di bit:

```csharp
public partial class TestPerspectivePage : ContentPage
{
    SKBitmap bitmap;

    public TestPerspectivePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnPersp0SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp0Label.Text = String.Format("Persp0 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }

    void OnPersp1SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp1Label.Text = String.Format("Persp1 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }
    ...
}
```

Il `PaintSurface` gestore calcola un' `SKMatrix` valore denominato `perspectiveMatrix` in base ai valori di questi due cursori divisi per 100. Questo parametro viene combinato con due trasformazioni che inserire il centro di questa trasformazione al centro della bitmap di conversione:

```csharp
public partial class TestPerspectivePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Calculate perspective matrix
        SKMatrix perspectiveMatrix = SKMatrix.MakeIdentity();
        perspectiveMatrix.Persp0 = (float)persp0Slider.Value / 100;
        perspectiveMatrix.Persp1 = (float)persp1Slider.Value / 100;

        // Center of screen
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);
        SKMatrix.PostConcat(ref matrix, perspectiveMatrix);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(xCenter, yCenter));

        // Coordinates to center bitmap on canvas
        float x = xCenter - bitmap.Width / 2;
        float y = yCenter - bitmap.Height / 2;

        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

Ecco alcune immagini di esempio:

[![](non-affine-images/testperspective-small.png "Tripla screenshot della pagina di Test prospettiva")](non-affine-images/testperspective-large.png#lightbox "tripla screenshot della pagina della prospettiva Test")

Mentre si sperimentano i dispositivi di scorrimento, troverai che i valori di là 0.0066 o di sotto –0.0066 causano l'immagine da divengono improvvisamente fractured e incoerenti. La mappa di bit in corso di trasformazione è 300 pixel quadrati. Questo viene trasformato rispetto al relativo centro, in modo che le coordinate della mappa di bit compresi tra –150 a 150. Si tenga presente che il valore di z' è:

z' = Persp0·x Persp1·y + 1

Se `Persp0` o `Persp1` è maggiore di quella 0.0066 o seguito –0.0066, quindi c'è sempre alcune coordinate della mappa di bit che comporta una z' valore pari a zero. Che fa sì che la divisione per zero e il rendering diventa urbano. Quando si utilizzano trasformazioni non affini, si desidera evitare il rendering di un'operazione con coordinate che causano la divisione per zero.

In generale, che non verrà impostato `Persp0` e `Persp1` in isolamento. È inoltre spesso necessario impostare le altre celle della matrice per ottenere determinati tipi di trasformazioni non affini.

Una tale trasformazione non affine è un *trasformazione di rastremazione*. Questo tipo di trasformazione non affine mantiene le dimensioni complessive di un rettangolo, ma si assottiglia attacco lato "uno":

![](non-affine-images/tapertransform.png "Una casella sottoposta a trasformazione di rastremazione")

Il [ `TaperTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs) classe esegue un calcolo generalizzato di una trasformazione non affine basato su questi parametri:

- le dimensioni dell'immagine in corso di trasformazione, rettangolare
- enumerazione che indica il lato del rettangolo che si assottiglia attacco,
- un'altra enumerazione che indica come lo si assottiglia attacco, e
- estensione del rapporto di conicità.

Ecco il codice:

```csharp
enum TaperSide { Left, Top, Right, Bottom }

enum TaperCorner { LeftOrTop, RightOrBottom, Both }

static class TaperTransform
{
    public static SKMatrix Make(SKSize size, TaperSide taperSide, TaperCorner taperCorner, float taperFraction)
    {
        SKMatrix matrix = SKMatrix.MakeIdentity();

        switch (taperSide)
        {
            case TaperSide.Left:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp0 = (taperFraction - 1) / size.Width;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Top:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp1 = (taperFraction - 1) / size.Height;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Right:
                matrix.ScaleX = 1 / taperFraction;
                matrix.Persp0 = (1 - taperFraction) / (size.Width * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        break;
                }
                break;

            case TaperSide.Bottom:
                matrix.ScaleY = 1 / taperFraction;
                matrix.Persp1 = (1 - taperFraction) / (size.Height * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        break;
                }
                break;
        }
        return matrix;
    }
}
```

Questa classe viene utilizzata nel **trasformazione di rastremazione** pagina. Il file XAML crea un'istanza di due `Picker` elementi da selezionare i valori di enumerazione e un `Slider` per la scelta di rastremazione frazione. Il [ `PaintSurface` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55) gestore combina la trasformazione di rastremazione con due trasformazioni per rendere la trasformazione rispetto all'angolo superiore sinistro dell'immagine bitmap della conversione:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedItem;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedItem;
    float taperFraction = (float)taperFractionSlider.Value;

    SKMatrix taperMatrix =
        TaperTransform.Make(new SKSize(bitmap.Width, bitmap.Height),
                            taperSide, taperCorner, taperFraction);

    // Display the matrix in the lower-right corner
    SKSize matrixSize = matrixDisplay.Measure(taperMatrix);

    matrixDisplay.Paint(canvas, taperMatrix,
        new SKPoint(info.Width - matrixSize.Width,
                    info.Height - matrixSize.Height));

    // Center bitmap on canvas
    float x = (info.Width - bitmap.Width) / 2;
    float y = (info.Height - bitmap.Height) / 2;

    SKMatrix matrix = SKMatrix.MakeTranslation(-x, -y);
    SKMatrix.PostConcat(ref matrix, taperMatrix);
    SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(x, y));

    canvas.SetMatrix(matrix);
    canvas.DrawBitmap(bitmap, x, y);
}
```

Ecco alcuni esempi:

[![](non-affine-images/tapertransform-small.png "Tripla screenshot della pagina di trasformazione di rastremazione")](non-affine-images/tapertransform-large.png#lightbox "tripla screenshot della pagina di trasformazione di rastremazione")

Un altro tipo di trasformazioni non affini generalizzate è rotazione 3D, come illustrata nell'articolo successivo, [ **rotazioni 3D**](3d-rotation.md).

La trasformazione non affine è possibile trasformare un rettangolo in qualsiasi quadrilatero convesso. Ciò viene dimostrata il **Mostra Non affini matrice** pagina. È molto simile al **mostrare matrice Affine** pagina dal [ **matrice trasforma** ](matrix.md) articolo ad eccezione del fatto che abbia una quarta `TouchPoint` oggetto per manipolare il quarto angolo della bitmap:

[![](non-affine-images/shownonaffinematrix-small.png "Tripla screenshot della pagina Mostra matrice Non affini")](non-affine-images/shownonaffinematrix-large.png#lightbox "tripla screenshot della pagina Mostra matrice Non affini")

Purché si non tenta di creare maggiore di 180 gradi angolo interno di uno degli angoli della bitmap oppure creare due lati tra loro, viene calcolato correttamente la trasformazione usando questo metodo dal [ `ShowNonAffineMatrixPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) classe:

```csharp
static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL, SKPoint ptLR)
{
    // Scale transform
    SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

    // Affine transform
    SKMatrix A = new SKMatrix
    {
        ScaleX = ptUR.X - ptUL.X,
        SkewY = ptUR.Y - ptUL.Y,
        SkewX = ptLL.X - ptUL.X,
        ScaleY = ptLL.Y - ptUL.Y,
        TransX = ptUL.X,
        TransY = ptUL.Y,
        Persp2 = 1
    };

    // Non-Affine transform
    SKMatrix inverseA;
    A.TryInvert(out inverseA);
    SKPoint abPoint = inverseA.MapPoint(ptLR);
    float a = abPoint.X;
    float b = abPoint.Y;

    float scaleX = a / (a + b - 1);
    float scaleY = b / (a + b - 1);

    SKMatrix N = new SKMatrix
    {
        ScaleX = scaleX,
        ScaleY = scaleY,
        Persp0 = scaleX - 1,
        Persp1 = scaleY - 1,
        Persp2 = 1
    };

    // Multiply S * N * A
    SKMatrix result = SKMatrix.MakeIdentity();
    SKMatrix.PostConcat(ref result, S);
    SKMatrix.PostConcat(ref result, N);
    SKMatrix.PostConcat(ref result, A);

    return result;
}
```

Per la facilità di calcolo, questo metodo ottiene la trasformazione totale come prodotto di tre trasformazioni separate, che sono rappresentate in questo caso con frecce che indicano come queste trasformazioni modificano i quattro angoli dell'immagine bitmap:

(0, 0) → (0, 0) → (0, 0) → (x 0, y0) (in alto a sinistra)

(0, H) → (0, 1) → (0, 1) → (x1, y1) (in basso a sinistra)

(P, 0) → → (1, 0) → (1, 0) (x2, y2) (angolo superiore destro)

(L, H) → → (a, b) → (1, 1) (x3, y3) (in basso a destra)

Le coordinate finali a destra sono i quattro punti associati i punti di quattro tocco. Queste sono le coordinate degli angoli della bitmap finale.

L e rappresentano la larghezza e altezza della bitmap. La prima trasformazione `S` semplicemente ridimensiona la bitmap da un quadrato di 1 pixel. La seconda trasformazione è la trasformazione non affine `N`, e la terza etichetta è la trasformazione affine `A`. Tale trasformazione affine si basa sui tre punti, in modo che lo ha esattamente come le versioni precedenti affini [ `ComputeMatrix` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) metodo e non prevede la quarta riga con il (punto a, b).

Il `a` e `b` valori vengono calcolati in modo che il terzo file di trasformazione è affine o meno. Il codice ottiene l'inverso di trasformazione affine e che quindi viene utilizzato per eseguire il mapping nell'angolo in basso a destra. Che rappresenta il punto (a, b).

Un altro utilizzo delle trasformazioni non affini consiste nel simulare grafica tridimensionale. Nell'articolo successivo [ **rotazioni 3D** ](3d-rotation.md) vedrai come ruotare un grafico bidimensionale nello spazio 3D.


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
