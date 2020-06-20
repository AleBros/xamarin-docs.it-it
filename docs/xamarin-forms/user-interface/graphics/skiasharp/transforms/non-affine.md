---
title: Trasformazioni non affini
description: In questo articolo viene illustrato come creare effetti della prospettiva e della rastremazione con la terza colonna della matrice di trasformazione e come illustrato nel codice di esempio.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 91a639b2d3c2f6a8437a09a70808dc6d793ba76b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84131755"
---
# <a name="non-affine-transforms"></a>Trasformazioni non affini

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Crea effetti della prospettiva e della rastremazione con la terza colonna della matrice di trasformazione_

La conversione, la scalabilità, la rotazione e la distorsione sono tutte classificate come trasformazioni *affini* . Le trasformazioni affini mantengono le linee parallele. Se due righe sono parallele prima della trasformazione, rimangono parallele dopo la trasformazione. I rettangoli vengono sempre trasformati in parallelogrammi.

Tuttavia, SkiaSharp è anche in grado di eseguire trasformazioni non affini, che hanno la possibilità di trasformare un rettangolo in qualsiasi quadrilatero convesso:

![](non-affine-images/nonaffinetransformexample.png "A bitmap transformed into a convex quadrilateral")

Un quadrilatero convesso è una figura su quattro lati con angoli interni sempre minori di 180 gradi e lati che non si intersecano tra loro.

Le trasformazioni non affini vengono generate quando la terza riga della matrice di trasformazione è impostata su valori diversi da 0, 0 e 1. La `SKMatrix` moltiplicazione completa è:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

Le formule di trasformazione risultanti sono:

x ' = ScaleX · x + SkewX · y + TransX

y ' = asimmetria · x + scalabilità · y + transy

z ' = Persp0 · x + Persp1 · y + Persp2

La regola fondamentale dell'uso di una matrice 3 per 3 per le trasformazioni bidimensionali è che tutto rimane sul piano dove Z è uguale a 1. A meno `Persp0` `Persp1` che e siano 0 e `Persp2` uguale a 1, la trasformazione ha spostato le coordinate Z rispetto a tale piano.

Per ripristinare questo oggetto in una trasformazione bidimensionale, è necessario spostare le coordinate di nuovo su tale piano. È necessario un altro passaggio. I valori x ', y ' e z ' devono essere divisi per z ':

x "= x '/z '

y "= y"/z "

z "= z '/z ' = 1

Queste sono note come *coordinate omogenee* e sono state sviluppate dal matematico August Ferdinand Möbius, molto più noto per la sua stranezza topologica, il Möbius strip.

Se z ' è 0, la divisione restituisce le coordinate infinite. Infatti, una delle motivazioni Möbius's per lo sviluppo di coordinate omogenee è la possibilità di rappresentare valori infiniti con numeri finiti.

Quando si visualizzano elementi grafici, tuttavia, si desidera evitare di eseguire il rendering di elementi con coordinate che trasformano in valori infiniti. Non verrà eseguito il rendering di queste coordinate. Tutti gli elementi che si trovano in prossimità di queste coordinate saranno molto grandi e probabilmente non coerenti visivamente.

In questa equazione non si desidera che il valore di z ' diventi zero:

z ' = Persp0 · x + Persp1 · y + Persp2

Di conseguenza, questi valori presentano alcune restrizioni pratiche: 

La `Persp2` cella può essere zero o non zero. Se `Persp2` è zero, z ' è zero per il punto (0, 0) e non è in genere consigliabile perché tale punto è molto comune nelle immagini bidimensionali. Se `Persp2` non è uguale a zero, non vi è alcuna perdita di generalizzazione se `Persp2` è fissata su 1. Se, ad esempio, si determina che `Persp2` dovrebbe essere 5, è possibile dividere semplicemente tutte le celle nella matrice per 5, il che rende `Persp2` uguale a 1 e il risultato sarà lo stesso.

Per questi motivi, `Persp2` viene spesso corretto a 1, ovvero lo stesso valore nella matrice di identità.

In genere, `Persp0` e `Persp1` sono numeri ridotti. Si supponga, ad esempio, di iniziare con una matrice di identità, ma di impostarla `Persp0` su 0,01:

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

Le formule di trasformazione sono:

x ' = x/(0,01 · x + 1)

y ' = y/(0,01 · x + 1)

Usare ora questa trasformazione per eseguire il rendering di una casella quadrata di 100 pixel posizionata all'origine. Ecco il modo in cui vengono trasformati i quattro angoli:

(0,0) → (0, 0)

(0, 100) → (0, 100)

(100,0) → (50, 0)

(100, 100) → (50, 50)

Quando x è 100, il denominatore z è 2, quindi le coordinate x e y vengono effettivamente dimezzate. Il lato destro della casella diventa più breve del lato sinistro:

![](non-affine-images/nonaffinetransform.png "A box subjected to a non-affine transform")

La `Persp` parte di questi nomi di celle si riferisce alla "prospettiva" perché lo scorcio suggerisce che la casella è ora inclinata con il lato destro oltre il visualizzatore.

La pagina della **prospettiva test** consente di sperimentare i valori di `Persp0` e `Pers1` per ottenere un'idea del funzionamento. I valori ragionevoli di queste celle della matrice sono talmente piccoli che `Slider` nel piattaforma UWP (Universal Windows Platform) non possono gestirli correttamente. Per risolvere il problema UWP, `Slider` è necessario inizializzare i due elementi in [**TestPerspective. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml) per un intervallo compreso tra-1 e 1:

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

I gestori eventi per i dispositivi di scorrimento nel [`TestPerspectivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) file code-behind dividono i valori di 100 in modo che siano compresi tra-0,01 e 0,01. Inoltre, il costruttore viene caricato in una bitmap:

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

Il `PaintSurface` gestore calcola un `SKMatrix` valore denominato `perspectiveMatrix` in base ai valori di questi due dispositivi di scorrimento divisi per 100. Questo è combinato con due trasformazioni di conversione che collocano il centro della trasformazione al centro della bitmap:

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

Di seguito sono riportate alcune immagini di esempio:

[![](non-affine-images/testperspective-small.png "Triple screenshot of the Test Perspective page")](non-affine-images/testperspective-large.png#lightbox "Triple screenshot of the Test Perspective page")

Quando si sperimentano i dispositivi di scorrimento, si noterà che i valori superiori a 0,0066 o inferiori a 0,0066 fanno sì che l'immagine diventi improvvisamente fratturata e incoerente. La bitmap da trasformare è 300-pixel. Viene trasformato in relazione al relativo centro, quindi le coordinate della bitmap variano da – 150 a 150. Ricordare che il valore di z ' è:

z ' = Persp0 · x + Persp1 · y + 1

Se `Persp0` o `Persp1` è maggiore di 0,0066 o inferiore a-0,0066, esiste sempre una coordinata della bitmap che restituisce un valore z ' pari a zero. Che causa la divisione per zero e il rendering diventa un pasticcio. Quando si utilizzano trasformazioni non affini, si desidera evitare il rendering di qualsiasi elemento con coordinate che causino la divisione per zero.

In genere, non è possibile impostare `Persp0` e `Persp1` in isolamento. Spesso è anche necessario impostare altre celle nella matrice per ottenere determinati tipi di trasformazioni non affini.

Una trasformazione di questo tipo non affine è una *trasformazione conica*. Questo tipo di trasformazione non affine mantiene le dimensioni complessive di un rettangolo, ma i nastri sono un lato:

![](non-affine-images/tapertransform.png "A box subjected to a taper transform")

La [`TaperTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs) classe esegue un calcolo generalizzato di una trasformazione non affine in base a questi parametri:

- dimensioni rettangolari dell'immagine da trasformare.
- Enumerazione che indica il lato del rettangolo che i nastri,
- un'altra enumerazione che indica il modo in cui i nastri e
- extent della rastremazione.

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

Questa classe viene utilizzata nella pagina **trasformazione conica** . Il file XAML crea un'istanza `Picker` di due elementi per selezionare i valori di enumerazione e un oggetto `Slider` per scegliere la frazione conica. Il [`PaintSurface`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55) gestore combina la trasformazione di rastremazione con due trasformazioni di conversione per trasformare la trasformazione rispetto all'angolo superiore sinistro della bitmap:

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

Di seguito sono riportati alcuni esempi:

[![](non-affine-images/tapertransform-small.png "Triple screenshot of the Taper Transform page")](non-affine-images/tapertransform-large.png#lightbox "Triple screenshot of the Taper Transform page")

Un altro tipo di trasformazioni non affini generalizzate è la rotazione 3D, illustrata nel prossimo articolo, le [**rotazioni 3D**](3d-rotation.md).

La trasformazione non affine può trasformare un rettangolo in qualsiasi quadrilatero convesso. Questa operazione viene illustrata nella pagina **Mostra matrice non affine** . È molto simile alla pagina **Mostra matrice affine** dall'articolo [**trasformazioni matrice**](matrix.md) , ad eccezione del fatto che dispone di un quarto `TouchPoint` oggetto per modificare il quarto angolo della bitmap:

[![](non-affine-images/shownonaffinematrix-small.png "Triple screenshot of the Show Non-Affine Matrix page")](non-affine-images/shownonaffinematrix-large.png#lightbox "Triple screenshot of the Show Non-Affine Matrix page")

Fino a quando non si tenta di creare un angolo interno di uno degli angoli della bitmap maggiore di 180 gradi, o di far attraversare due lati, il programma calcola correttamente la trasformazione utilizzando questo metodo dalla [`ShowNonAffineMatrixPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) classe:

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

Per semplificare il calcolo, questo metodo ottiene la trasformazione totale come prodotto di tre trasformazioni separate, che sono simboleggiate qui con frecce che mostrano il modo in cui queste trasformazioni modificano i quattro angoli della bitmap:

(0,0) → (0,0) → (0, 0) → (x0, y0) (in alto a sinistra)

(0, H) → (0,1) → (0,1) → (x1, Y1) (in basso a sinistra)

(W,0) → (1, 0) → (1, 0) → (X2, Y2) (in alto a destra)

(W, H) → (1,1) → (a, b) → (X3, Y3) (in basso a destra)

Le coordinate finali a destra sono i quattro punti associati ai quattro punti di tocco. Queste sono le coordinate finali degli angoli della bitmap.

W e H rappresentano la larghezza e l'altezza della bitmap. La prima trasformazione `S` Ridimensiona semplicemente la bitmap in un quadrato da 1 pixel. La seconda trasformazione è la trasformazione non affine `N` e la terza è la trasformazione affine `A` . Questa trasformazione affine si basa su tre punti, quindi è come il metodo affine precedente [`ComputeMatrix`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) e non implica la quarta riga con il punto (a, b).

I `a` `b` valori e vengono calcolati in modo che la terza trasformazione sia affine. Il codice ottiene l'inverso della trasformazione affine e quindi lo utilizza per eseguire il mapping dell'angolo inferiore destro. Questo è il punto (a, b).

Un altro uso delle trasformazioni non affini consiste nel simulare immagini tridimensionali. Nell'articolo successivo, le [**rotazioni 3D**](3d-rotation.md) consentono di ruotare un grafico bidimensionale nello spazio 3D.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
