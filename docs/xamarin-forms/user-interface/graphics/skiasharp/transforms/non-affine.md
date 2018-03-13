---
title: Trasformazioni non affini.
description: Creare prospettive ed effetti conici con la terza colonna della matrice di trasformazione
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: charlespetzold
ms.author: chape
ms.date: 04/14/2017
ms.openlocfilehash: 2e2e83404bc93bd07885008b868c51eba2ff7140
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="non-affine-transforms"></a>Trasformazioni non affini.

_Creare prospettive ed effetti conici con la terza colonna della matrice di trasformazione_

Conversione, il ridimensionamento, rotazione e inclinazione sono classificate come *affine* Trasforma. Le trasformazioni affini mantengono linee parallele. Se due linee parallele prima della trasformazione, rimangono parallele dopo la trasformazione. I rettangoli sono sempre trasformati parallelogrammi.

Tuttavia, è inoltre in grado di trasformazioni non affini, che dispongono della funzionalità per trasformare un rettangolo in qualsiasi quadrilatero convesso SkiaSharp:

![](non-affine-images/nonaffinetransformexample.png "Una bitmap trasformata in un quadrilatero convesso")

Un quadrilatero convesso è una figura quattro lati con angoli sempre un valore minore di 180 gradi e a che non superano i loro interno.

Non affini Trasforma i risultati quando la terza riga della matrice di trasformazione è impostata su valori diversi da 0, 0 e 1. La versione completa `SKMatrix` moltiplicazione:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

Le formule di trasformazione risultante sono:

x' = ScaleX·x + SkewX·y + TransX

y' = SkewY·x + ScaleY·y + TransY

z` = Persp0·x + Persp1·y + Persp2

La regola fondamentale dell'utilizzo di una matrice 3 per 3 per le trasformazioni bidimensionale è che tutto ciò che rimane nel piano in cui Z è uguale a 1. A meno che non `Persp0` e `Persp1` sono 0, e `Persp2` è uguale a 1, la trasformazione è stato spostato le coordinate Z tale piano.

Per ripristinare una trasformazione bidimensionale, è necessario spostare le coordinate a questo piano. Un altro passaggio è obbligatorio. La x', y', e z 'i valori devono essere diviso per z':

x" = x' / z'

y" = y' / z'

z" = z' / z' = 1

Questi sono conosciuti come *coordinate omogenee* e sono stati sviluppati da si ipotizza agosto Ferdinand Möbius, molto più noti per il suo oddity topologiche, elenco di Möbius.

Se z' è 0, i risultati di divisione in coordinate infinite. In realtà, una delle motivazioni del Möbius per lo sviluppo di coordinate omogenee era la possibilità di rappresentare valori infiniti con numeri finiti.

Quando la visualizzazione della grafica, tuttavia, si desidera evitare il rendering di un elemento con le coordinate di trasformare i valori infinito. Non è possibile eseguire il rendering di tali coordinate. Tutti gli elementi in tali coordinate sarà molto grandi e probabilmente non visivamente coerente.

L'equazione, non si desidera il valore di z' diventa zero:

z` = Persp0·x + Persp1·y + Persp2

Il `Persp2` cella può essere zero o non zero. Se `Persp2` è zero, z' è uguale a zero per il punto (0, 0) e che non è in genere preferibile perché tale punto è molto comune nei grafici bidimensionali. Se `Persp2` non è uguale a zero, non vi è alcuna perdita di generalità se `Persp2` è fissato a 1. Ad esempio, se si determina che `Persp2` deve essere di 5, quindi è possibile dividere semplicemente tutte le celle della matrice di 5, che rende `Persp2` uguale a 1 e il risultato sarà lo stesso.

Per questi motivi, `Persp2` spesso è fissato a 1, che è lo stesso valore nella matrice di identità.

In genere, `Persp0` e `Persp1` sono numeri di piccole dimensioni. Si supponga ad esempio iniziare con una matrice di identità ma set `Persp0` a 0,01:

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

Le formule di trasformazione sono:

x' = x / (0.01·x + 1)

y' = y / (0.01·x + 1)

Utilizzare questa trasformazione per eseguire il rendering di un quadrato di 100 pixel posizionato in corrispondenza dell'origine. Ecco come vengono trasformati gli angoli di quattro:

(0, 0) → (0, 0)

(0, 100) → (0, 100)

(100, 0) → (50, 0)

(100, 100) → (50, 50)

Se x è 100, quindi la z' denominatore è 2, pertanto le coordinate x e y sono effettivamente dimezzato. Il lato destro della finestra diventa più breve rispetto al lato sinistro:

![](non-affine-images/nonaffinetransform.png "Una casella soggetto a una trasformazione non affini")

Il `Persp` parte di questi nomi di cella fa riferimento a "Prospettiva" perché la prospettiva suggerisce che la casella ora inclinata al lato destro del visualizzatore.

Il **prospettiva Test** pagina consente di sperimentare con valori di `Persp0` e `Pers1` per ottenere un'idea di come funzionano. Valori accettabili di queste celle della matrice sono talmente che il `Slider` nella piattaforma Windows universale non può gestire correttamente tali. Per supportare il problema UWP, i due `Slider` gli elementi di [ **TestPerspective.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml) devono essere inizializzati all'intervallo da -1 a 1:

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

I gestori eventi per i dispositivi di scorrimento nel [ `TestPerspectivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) file code-behind dividere i valori da 100 in modo che sono compresi tra –0.01 e 0,01. Inoltre, il costruttore consente di caricare una bitmap.

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
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

Il `PaintSurface` gestore calcola un `SKMatrix` valore denominato `perspectiveMatrix` in base ai valori di queste due cursori divisi per 100. Questo è combinato con due tradurre le trasformazioni che inserire il centro della trasformazione al centro della bitmap:

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

[![](non-affine-images/testperspective-small.png "Schermata di triplo della pagina prospettiva Test")](non-affine-images/testperspective-large.png#lightbox "tripla schermata della pagina prospettiva Test")

Mentre si sperimentano i dispositivi di scorrimento, sono disponibili che valori oltre 0.0066 o di sotto di –0.0066 causare l'immagine risulti improvvisamente fractured e incoerenti. La mappa di bit vengono trasformato è 300 pixel. Trasformato rispetto al centro, pertanto le coordinate della mappa di bit compreso tra –150 e 150. Tenere presente che il valore di z' è:

z` = Persp0·x + Persp1·y + 1

Se `Persp0` o `Persp1` è maggiore di 0.0066 o di sotto di –0.0066, quindi è sempre alcune coordinate della bitmap risultante in z' valore pari a zero. Che causa la divisione per zero e il rendering diventa un messaggio. Quando si utilizzano le trasformazioni non affini, si desidera evitare il rendering di qualsiasi operazione con le coordinate che causano la divisione per zero.

È in genere, non impostare `Persp0` e `Persp1` in isolamento. È inoltre spesso necessario impostare le altre celle della matrice per ottenere determinati tipi di trasformazioni non affini.

Una tale trasformazione non affini un *trasformazione conica*. Questo tipo di trasformazione non affini conserva le dimensioni complessive di un rettangolo, ma si assottiglia attacco lato "uno":

![](non-affine-images/tapertransform.png "Una casella soggetto a una trasformazione conica")

Il [ `TaperTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs) classe esegue un calcolo generalizzato di una trasformazione non affini basato su questi parametri:

- le dimensioni dell'immagine vengono trasformato, rettangolare
- enumerazione che indica il lato del rettangolo che si assottiglia attacco,
- un'altra enumerazione che indica come si assottiglia attacco, e
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

Questa classe viene utilizzata nel **trasformare conica** pagina. Il file XAML crea un'istanza di due `Picker` elementi per selezionare i valori di enumerazione e un `Slider` per la scelta la frazione conica. Il [ `PaintSurface` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55) gestore combina la trasformazione conica con due tradurre le trasformazioni per rendere la trasformazione rispetto all'angolo superiore sinistro della bitmap:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedIndex;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedIndex;
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

[![](non-affine-images/tapertransform-small.png "Schermata triplo della pagina trasformare conica")](non-affine-images/tapertransform-large.png#lightbox "tripla schermata della pagina trasformare conica")

Un altro tipo di trasformazioni non affini generalizzate è rotazione 3D, come illustrata nell'articolo successivo, [rotazioni 3D](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md).

La trasformazione non affini può trasformare un rettangolo in qualsiasi quadrilatero convessa. Ciò viene dimostrata la **Mostra matrice Non affini** pagina. È molto simile al **Mostra matrice Affine** pagina dal [Trasforma una matrice](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md) articolo ad eccezione del fatto che disponga di un quarto `TouchPoint` oggetto per modificare l'angolo dell'immagine bitmap della quarta:

[![](non-affine-images/shownonaffinematrix-small.png "Schermata triplo della pagina Mostra matrice Non affini")](non-affine-images/shownonaffinematrix-large.png#lightbox "tripla schermata della pagina Mostra matrice Non affini")

Purché si non tenta di creare un angolo interno di uno degli angoli della bitmap maggiore di 180 gradi oppure creare due lati di incrociarsi, viene calcolato correttamente la trasformazione usando il metodo di [ `ShowNonAffineMatrixPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) classe:

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

Per facilitare il calcolo, questo metodo ottiene la trasformazione totale come prodotto tra tre trasformazioni separate, che sono symbolized qui con frecce che indicano come tali trasformazioni modificano i quattro angoli dell'immagine bitmap:

(0, 0) → → (0, 0) → (0, 0) (x 0, y0) (in alto a sinistra)

(0, H) → → (0, 1) → (0, 1) (x1, y1) (in basso a sinistra)

(W, 0) → → (1, 0) → (1, 0) (x2, y2) (angolo superiore destro)

(W, H) → → (a, b) → (1, 1) (x3, y3) (in basso a destra)

Le coordinate finale a destra sono i quattro punti associati i punti di quattro tocco. Queste sono le coordinate degli angoli della bitmap finale.

L e rappresentano la larghezza e altezza della bitmap. La prima trasformazione (`S`) semplicemente ridimensiona la bitmap da un quadrato di 1 pixel. La seconda trasformazione è la trasformazione non affini `N`, e il terzo è la trasformazione affine `A`. La trasformazione affine è basata sui tre punti, pertanto è analogo la precedenza affine [ `ComputeMatrix` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) (metodo) e non prevede la quarta riga con il (punto a, b).

Il `a` e `b` i valori vengono calcolati in modo che il terzo file di trasformazione affine. Il codice ottiene l'inverso di trasformazione affine e che quindi viene utilizzato per eseguire il mapping nell'angolo inferiore destro. Che è il punto (a, b).

Un altro utilizzo delle trasformazioni non affini è simulare grafica tridimensionale. Vedere l'articolo successivo [rotazioni 3D](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md) viene visualizzato come ruotare un grafico bidimensionale nello spazio 3D.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
