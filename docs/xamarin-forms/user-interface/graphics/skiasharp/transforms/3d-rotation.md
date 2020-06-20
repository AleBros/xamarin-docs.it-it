---
title: Rotazioni 3D in SkiaSharp
description: Questo articolo illustra come usare le trasformazioni non affini per ruotare gli oggetti 2D nello spazio 3D e lo dimostra con il codice di esempio.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3706139a2c15d01af67203c2bd09b281de80ed52
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140205"
---
# <a name="3d-rotations-in-skiasharp"></a>Rotazioni 3D in SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utilizzare trasformazioni non affini per ruotare gli oggetti 2D nello spazio 3D._

Un'applicazione comune di trasformazioni non affini consiste nel simulare la rotazione di un oggetto 2D nello spazio 3D:

![](3d-rotation-images/3drotationsexample.png "A text string rotated in 3D space")

Questo processo prevede l'uso di rotazioni tridimensionali e quindi la derivazione di una trasformazione non affine `SKMatrix` che esegue queste rotazioni 3D.

È difficile sviluppare questa `SKMatrix` trasformazione in modo che funzioni esclusivamente all'interno di due dimensioni. Il processo risulta molto più semplice quando la matrice 3 per 3 è derivata da una matrice 4 per 4 utilizzata nella grafica 3D. SkiaSharp include la [`SKMatrix44`](xref:SkiaSharp.SKMatrix44) classe per questo scopo, ma alcuni retroscena nella grafica 3D sono necessari per comprendere le rotazioni 3D e la matrice di trasformazione 4 per 4.

Un sistema di coordinate tridimensionali aggiunge un terzo asse chiamato Z. concettualmente, l'asse Z si trova negli angoli destro dello schermo. I punti di coordinate nello spazio 3D sono indicati con tre numeri: (x, y, z). Nel sistema di coordinate 3D usato in questo articolo, l'aumento dei valori di X è a destra e aumentano i valori di Y, così come in due dimensioni. L'aumento dei valori Z positivi esce dallo schermo. L'origine è l'angolo superiore sinistro, così come nella grafica 2D. È possibile considerare lo schermo come un piano XY con l'asse Z sugli angoli destro del piano.

Si tratta di un sistema di coordinate di sinistra. Se si punta l'indice per la mano sinistra nella direzione delle coordinate X positive (a destra) e il dito intermedio nella direzione di aumentare le coordinate Y (verso il basso), il cursore punta alla direzione di aumentare le coordinate Z, estendendosi dallo schermo.

Nella grafica 3D le trasformazioni sono basate su una matrice 4 per 4. Di seguito è illustrata la matrice di identità 4 per 4:

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

Quando si utilizza una matrice 4 per 4, è consigliabile identificare le celle con i relativi numeri di riga e di colonna:

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

Tuttavia, la `Matrix44` classe SkiaSharp è leggermente diversa. L'unico modo per impostare o ottenere singoli valori di cella in `SKMatrix44` è tramite l' [`Item`](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32)) indicizzatore. Gli indici di riga e di colonna sono in base zero anziché in base uno e le righe e le colonne vengono scambiate. È possibile accedere alla cella M14 nel diagramma sopra usando l'indicizzatore `[3, 0]` in un `SKMatrix44` oggetto.

In un sistema di grafica 3D, un punto 3D (x, y, z) viene convertito in una matrice 1 per 4 per la moltiplicazione per la matrice di trasformazione 4 per 4:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Analogamente alle trasformazioni 2D che avvengono in tre dimensioni, si presuppone che le trasformazioni 3D avvengano in quattro dimensioni. La quarta dimensione è detta W e lo spazio 3D si presuppone che esista nello spazio 4D dove le coordinate W sono pari a 1. Le formule di trasformazione sono le seguenti:

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

È ovvio dalle formule di trasformazione che le celle `M11` , `M22` , `M33` sono fattori di scala nelle direzioni x, Y e z e `M41` , `M42` , e `M43` sono fattori di traduzione nelle direzioni x, y e z.

Per convertire di nuovo le coordinate nello spazio 3D in cui W è uguale a 1, le coordinate x ', y ' e z ' sono tutte divise per W ':

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

Questa divisione per w "fornisce la prospettiva nello spazio 3D. Se w è uguale a 1, non viene eseguita alcuna prospettiva.

Le rotazioni nello spazio 3D possono essere abbastanza complesse, ma le rotazioni più semplici sono quelle intorno agli assi X, Y e Z. Una rotazione dell'angolo α intorno all'asse X è questa matrice:

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

I valori di X rimangono invariati se sottoposti a questa trasformazione. La rotazione intorno all'asse Y lascia invariati i valori di Y:

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

La rotazione intorno all'asse Z è identica a quella della grafica 2D:

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

La direzione di rotazione è implicita dalla manualità del sistema di coordinate. Si tratta di un sistema a sinistra, quindi se si posiziona il cursore del cursore verso l'aumentare dei valori per un determinato asse, a destra per la rotazione intorno all'asse X, verso il basso per la rotazione intorno all'asse Y e verso la rotazione intorno all'asse Z, la curva delle altre dita indica la direzione della rotazione per gli angoli positivi.

`SKMatrix44`dispone [`CreateRotation`](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single)) di metodi e statici generalizzati [`CreateRotationDegrees`](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) che consentono di specificare l'asse intorno al quale si verifica la rotazione:

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

Per la rotazione intorno all'asse X, impostare i primi tre argomenti su 1, 0, 0. Per la rotazione intorno all'asse Y, impostarli su 0, 1, 0 e per la rotazione intorno all'asse Z, impostarli su 0, 0, 1.

La quarta colonna di 4 per 4 è per la prospettiva. `SKMatrix44`Non dispone di metodi per la creazione di trasformazioni della prospettiva, ma è possibile crearne uno usando il codice seguente:

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

Il motivo del nome dell'argomento `depth` sarà evidente a breve. Il codice crea la matrice:

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

Le formule Transform generano il seguente calcolo di w:

`w' = –z / depth + 1`

Questo consente di ridurre le coordinate X e Y quando i valori della Z sono minori di zero (concettualmente dietro il piano XY) e di aumentare le coordinate X e Y per i valori positivi della Z. Quando la coordinata Z `depth` è uguale a, w ' è zero e le coordinate diventano infinite. I sistemi grafici tridimensionali si basano su una metafora della fotocamera e il `depth` valore qui rappresenta la distanza della fotocamera dall'origine del sistema di coordinate. Se un oggetto grafico ha una coordinata Z che corrisponde a `depth` unità dall'origine, viene concettualmente toccato l'obiettivo della fotocamera e diventa infinitamente grande.

Tenere presente che è probabile che questo valore venga usato `perspectiveMatrix` in combinazione con le matrici di rotazione. Se un oggetto Graphics da ruotare ha coordinate X o Y maggiori di `depth` , è probabile che la rotazione di questo oggetto nello spazio 3D includa coordinate Z maggiori di `depth` . Questa operazione deve essere evitata. Quando si crea, `perspectiveMatrix` si desidera impostare `depth` su un valore sufficientemente grande per tutte le coordinate nell'oggetto Graphics indipendentemente dal modo in cui viene ruotato. In questo modo si garantisce che non vi sia alcuna divisione per zero.

Per combinare le rotazioni e la prospettiva 3D è necessario moltiplicare 4 per 4 matrici. A questo scopo, `SKMatrix44` definisce i metodi di concatenazione. Se `A` e `B` sono `SKMatrix44` oggetti, il codice seguente imposta un valore uguale a a × B:

```csharp
A.PostConcat(B);
```

Quando in un sistema grafico 2D viene utilizzata una matrice di trasformazione 4 per 4, questa viene applicata agli oggetti 2D. Questi oggetti sono Flat e si presuppone che le coordinate Z siano pari a zero. La moltiplicazione di trasformazione è leggermente più semplice rispetto alla trasformazione illustrata in precedenza:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Il valore 0 per z genera formule di trasformazione che non coinvolgono alcuna cella nella terza riga della matrice:

x ' = M11 · x + M21 · y + M41

y ' = M12 · x + M22 · y + M42

z ' = M13 · x + M23 · y + M43

w ' = M14 · x + M24 · y + M44

Inoltre, la coordinata z è irrilevante anche qui. Quando un oggetto 3D viene visualizzato in un sistema grafico 2D, viene compresso in un oggetto bidimensionale ignorando i valori delle coordinate Z. Le formule di trasformazione sono solo queste due:

`x" = x' / w'`

`y" = y' / w'`

Ciò significa che la terza riga *e* la terza colonna della matrice 4 per 4 possono essere ignorate.

Ma in tal caso, perché la matrice 4 per 4 è ancora necessaria?

Sebbene la terza riga e la terza colonna di 4 per 4 siano irrilevanti per le trasformazioni bidimensionali, la terza riga e la *colonna svolgono* un ruolo precedente a quello in cui i vari `SKMatrix44` valori vengono moltiplicati insieme. Si supponga, ad esempio, di moltiplicare la rotazione intorno all'asse Y con la trasformazione prospettiva:

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

Nel prodotto la cella `M14` contiene ora un valore Perspective. Se si desidera applicare la matrice a oggetti 2D, la terza riga e la colonna vengono eliminati per convertirla in una matrice 3 per 3:

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

Ora può essere usato per trasformare un punto 2D:

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

Le formule di trasformazione sono:

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

Ora dividere tutti gli elementi per z:

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

Quando gli oggetti 2D vengono ruotati con un angolo positivo intorno all'asse Y, i valori X positivi vengono ritirati sullo sfondo, mentre i valori X negativi vengono in primo piano. I valori X sembrano avvicinarsi all'asse Y, che è regolato dal valore del coseno, perché le coordinate più lontane dall'asse Y diventano più piccole o più grandi quando si spostano ulteriormente dal visualizzatore o più vicine al visualizzatore.

Quando si usa `SKMatrix44` , eseguire tutte le operazioni di rotazione e prospettiva 3D moltiplicando `SKMatrix44` i diversi valori. È quindi possibile estrarre una matrice tridimensionale tridimensionale dalla matrice 4 per 4 usando la [`Matrix`](xref:SkiaSharp.SKMatrix44.Matrix) proprietà della `SKMatrix44` classe. Questa proprietà restituisce un `SKMatrix` valore familiare.

La pagina **rotazione 3D** consente di sperimentare la rotazione 3D. Il file [**Rotation3DPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml) crea un'istanza di quattro dispositivi di scorrimento per impostare la rotazione intorno agli assi X, Y e Z e per impostare un valore di profondità:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.Rotation3DPage"
             Title="Rotation 3D">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
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
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Maximum" Value="360" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="xRotateSlider"
                Grid.Row="0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference xRotateSlider},
                              Path=Value,
                              StringFormat='X-Axis Rotation = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="yRotateSlider"
                Grid.Row="2"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference yRotateSlider},
                              Path=Value,
                              StringFormat='Y-Axis Rotation = {0:F0}'}"
               Grid.Row="3" />

        <Slider x:Name="zRotateSlider"
                Grid.Row="4"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zRotateSlider},
                              Path=Value,
                              StringFormat='Z-Axis Rotation = {0:F0}'}"
               Grid.Row="5" />

        <Slider x:Name="depthSlider"
                Grid.Row="6"
                Maximum="2500"
                Minimum="250"
                ValueChanged="OnSliderValueChanged" />

        <Label Grid.Row="7"
               Text="{Binding Source={x:Reference depthSlider},
                              Path=Value,
                              StringFormat='Depth = {0:F0}'}" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="8"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

Si noti che `depthSlider` viene inizializzato con un `Minimum` valore di 250. Ciò implica che l'oggetto 2D da ruotare in questo punto presenta le coordinate X e Y limitate a un cerchio definito da un raggio di 250 pixel intorno all'origine. Qualsiasi rotazione di questo oggetto nello spazio 3D determinerà sempre valori di coordinate minori di 250.

Il file code-behind [**Rotation3DPage.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs) viene caricato in una bitmap di 300 pixel al quadrato:

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Se la trasformazione 3D è centrata su questa bitmap, le coordinate X e Y sono comprese tra-150 e 150, mentre gli angoli sono 212 pixel dal centro, quindi tutti gli elementi si trovano all'interno del raggio 250-pixel.

Il `PaintSurface` gestore crea `SKMatrix44` oggetti basati sui dispositivi di scorrimento e li moltiplica insieme usando `PostConcat` . Il `SKMatrix` valore estratto dall'oggetto finale `SKMatrix44` è racchiuso tra trasformazioni di traslazione per centrare la rotazione al centro dello schermo:

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Use 3D matrix for 3D rotations and perspective
        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, (float)xRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, (float)yRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, (float)zRotateSlider.Value));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / (float)depthSlider.Value;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the bitmap
        canvas.SetMatrix(matrix);
        float xBitmap = xCenter - bitmap.Width / 2;
        float yBitmap = yCenter - bitmap.Height / 2;
        canvas.DrawBitmap(bitmap, xBitmap, yBitmap);
    }
}
```

Quando si sperimenta il quarto dispositivo di scorrimento, si noterà che le diverse impostazioni di profondità non spostano l'oggetto lontano dal visualizzatore, ma modificano invece la portata dell'effetto prospettico:

[![](3d-rotation-images/rotation3d-small.png "Triple screenshot of the Rotation 3D page")](3d-rotation-images/rotation3d-large.png#lightbox "Triple screenshot of the Rotation 3D page")

La **rotazione animata 3D** usa anche `SKMatrix44` per animare una stringa di testo nello spazio 3D. Il `textPaint` set di oggetti come campo viene usato nel costruttore per determinare i limiti del testo:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    SKCanvasView canvasView;
    float xRotationDegrees, yRotationDegrees, zRotationDegrees;
    string text = "SkiaSharp";
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        TextSize = 100,
        StrokeWidth = 3,
    };
    SKRect textBounds;

    public AnimatedRotation3DPage()
    {
        Title = "Animated Rotation 3D";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Measure the text
        textPaint.MeasureText(text, ref textBounds);
    }
    ...
}
```

L' `OnAppearing` override definisce tre Xamarin.Forms `Animation` oggetti per animare i `xRotationDegrees` `yRotationDegrees` campi, e `zRotationDegrees` a frequenze diverse. Si noti che i periodi di queste animazioni sono impostati su numeri primi (5 secondi, 7 secondi e 11 secondi), quindi la combinazione complessiva si ripete solo ogni 385 secondi o più di 10 minuti:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();

        new Animation((value) => xRotationDegrees = 360 * (float)value).
            Commit(this, "xRotationAnimation", length: 5000, repeat: () => true);

        new Animation((value) => yRotationDegrees = 360 * (float)value).
            Commit(this, "yRotationAnimation", length: 7000, repeat: () => true);

        new Animation((value) =>
        {
            zRotationDegrees = 360 * (float)value;
            canvasView.InvalidateSurface();
        }).Commit(this, "zRotationAnimation", length: 11000, repeat: () => true);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        this.AbortAnimation("xRotationAnimation");
        this.AbortAnimation("yRotationAnimation");
        this.AbortAnimation("zRotationAnimation");
    }
    ...
}
```

Come nel programma precedente, il `PaintCanvas` gestore crea `SKMatrix44` valori per la rotazione e la prospettiva e li moltiplica tra loro:

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Scale so text fits
        float scale = Math.Min(info.Width / textBounds.Width,
                               info.Height / textBounds.Height);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(scale, scale));

        // Calculate composite 3D transforms
        float depth = 0.75f * scale * textBounds.Width;

        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, xRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, yRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, zRotationDegrees));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / depth;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the text
        canvas.SetMatrix(matrix);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;
        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

Questa rotazione 3D è racchiusa tra diverse trasformazioni 2D per spostare il centro della rotazione al centro dello schermo e ridimensionare le dimensioni della stringa di testo in modo che corrisponda alla larghezza dello schermo:

[![](3d-rotation-images/animatedrotation3d-small.png "Triple screenshot of the Animated Rotation 3D page")](3d-rotation-images/animatedrotation3d-large.png#lightbox "Triple screenshot of the Animated Rotation 3D page")

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
