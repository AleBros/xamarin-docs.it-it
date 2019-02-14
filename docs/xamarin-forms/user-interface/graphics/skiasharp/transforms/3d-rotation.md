---
title: Rotazioni 3D in SkiaSharp
description: Questo articolo illustra come usare le trasformazioni non affini per ruotare gli oggetti 2D nello spazio 3D e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: 7ac9ec458f16357ef50e23c459a9b0e1f79bdd97
ms.sourcegitcommit: c6ff24b524d025d7e87b7b9c25f04c740dd93497
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56240370"
---
# <a name="3d-rotations-in-skiasharp"></a>Rotazioni 3D in SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_Usare le trasformazioni non affini ruotare gli oggetti 2D nello spazio 3D._

Un'applicazione comune delle trasformazioni non affini simula la rotazione di un oggetto 2D nello spazio 3D:

![](3d-rotation-images/3drotationsexample.png "Una stringa di testo ruotato nello spazio 3D")

Questa attività comporta l'utilizzo con le rotazioni tridimensionali e ricavando un non affini `SKMatrix` trasformazione che esegue rotazioni 3D.

È difficile sviluppare questo `SKMatrix` trasformazione lavorano esclusivamente all'interno di due dimensioni. Il processo diventa molto più semplice quando questa matrice 3 per 3 è derivato da una matrice 4 x 4 usata nella grafica 3D. SkiaSharp include il [ `SKMatrix44` ](xref:SkiaSharp.SKMatrix44) classe per questo scopo, ma alcune conoscenze nelle grafica 3D è necessaria per la comprensione delle rotazioni 3D e la matrice di trasformazione 4 per 4.

Un sistema di coordinate tridimensionale aggiunge un terzo asse z concettualmente chiamato, l'asse Z è ad angolo retto sullo schermo. I punti di coordinate nello spazio 3D sono indicati con tre numeri: (x, y, z). In 3D sono sistema di coordinate utilizzato in questo articolo, aumentare i valori di X a destra e valori crescenti di Y diminuiscono, analogamente a due dimensioni. Aumentare i valori Z positivi dallo schermo. L'origine è l'angolo superiore sinistro, come nei grafici 2D. La schermata può pensare come un piano XY con l'asse Z retto rispetto a questo piano.

Si tratta di un sistema di coordinate a sinistra. Se si posiziona il puntatore di indice per la parte sinistra nella direzione di coordinate (a destra) X positivo e coordina il dito intermedio nella direzione crescente Y (in basso), quindi la casella di scorrimento punta nella direzione crescente le coordinate Z: estensione da la schermata.

Nella grafica 3D, le trasformazioni si basano su una matrice 4 per 4. Di seguito è riportata la matrice identità 4 per 4:

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

Nell'uso di una matrice 4 per 4, è utile identificare le celle con i relativi numeri di riga e colonna:

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

Tuttavia, di SkiaSharp `Matrix44` classe è leggermente diversa. L'unico modo per impostare o ottenere singoli valori di cella `SKMatrix44` consiste nell'usare il [ `Item` ](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32)) dell'indicizzatore. Gli indici di riga e colonna sono in base zero anziché in base uno, e vengono scambiate le righe e colonne. La cella M14 nel diagramma precedente è accessibile tramite l'indicizzatore `[3, 0]` in un `SKMatrix44` oggetto.

In un sistema di grafica 3D, un punto 3D (x, y, z) viene convertito in una matrice da 1 a 4 per la moltiplicazione per la matrice di trasformazione 4 per 4:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Analogo a 2D Trasforma che si verificano in tre dimensioni, trasformazioni 3D si presuppone che si verificano in quattro dimensioni. La quarta dimensione è detta W, e si presuppone che lo spazio 3D esiste nello spazio di 4D in coordinate W sono uguali a 1. Le formule di trasformazione sono i seguenti:

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

È ovvio dalle formule di trasformazione che le celle `M11`, `M22`, `M33` sono fattori di scala nelle direzioni X, Y e Z, e `M41`, `M42`, e `M43` sono fattori di traslazione X, Y e Z direzioni.

Per convertire tali coordinate allo spazio 3D dove W uguale a 1, x', y', e z 'le coordinate sono tutti diviso w':

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

La divisione per w' fornisce un punto di vista nello spazio 3D. Se l ' è uguale a 1, quindi si verifica alcuna prospettiva.

Le rotazioni nello spazio 3D possono essere molto complesse, ma le rotazioni più semplici sono quelli intorno all'asse X, Y e Z. Una rotazione dell'angolo α intorno all'asse X è la matrice:

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

I valori di X rimangono invariati quando è sottoposto a questa trasformazione. Rotazione intorno all'asse Y lascia i valori di Y non modificato:

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

Rotazione intorno all'asse Z è uguale a quello della grafica 2D:

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

Il lato operativo del sistema di coordinate è implicita la direzione di rotazione. Si tratta di un sistema battitori, pertanto se si posiziona il cursore del a sinistra per aumentare i valori per un determinato asse, ovvero a destra per la rotazione intorno all'asse X, verso il basso per la rotazione intorno all'asse Y e verso il basso per la rotazione intorno all'asse Z, quindi la curva di yo le altre dita indica la direzione di rotazione per gli angoli positivi.

`SKMatrix44` è generalizzato statica [ `CreateRotation` ](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single)) e [ `CreateRotationDegrees` ](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) metodi che consentono di specificare l'asse intorno al quale si verifica la rotazione:

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

Per la rotazione intorno all'asse X, impostare i primi tre argomenti su 1, 0, 0. Per la rotazione intorno all'asse Y, impostarli su 0, 1, 0 e per la rotazione intorno all'asse Z, impostarli su 0, 0, 1.

La quarta colonna del 4 per 4 è per la prospettiva. Il `SKMatrix44` dispone di alcun metodo per la creazione di trasformazioni di punto di vista, ma è possibile crearne una manualmente tramite il codice seguente:

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

Il motivo per il nome dell'argomento `depth` sarà evidente a breve. Questo codice crea la matrice:

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

Le formule di trasformazione comportare il calcolo seguente di w':

`w' = –z / depth + 1`

Ciò serve a ridurre le coordinate X e Y, quando i valori di Z sono minore di zero (concettualmente dietro al piano XY) e per aumentare le coordinate X e Y per i valori positivi di Z. Quando la coordinata Z è uguale a `depth`, quindi l ' è pari a zero e le coordinate diventano infinite. I sistemi di grafica tridimensionale sono basati su una metafora della fotocamera e il `depth` valore in questo campo rappresenta la distanza della fotocamera rispetto all'origine del sistema di coordinate. Se un oggetto grafico è incluso un Z coordinare vale a dire `depth` unità dall'origine, a livello concettuale tocca l'obiettivo della fotocamera e diventa elevato all'infinito.

Tenere presente che sarà probabilmente possibile usare questo `perspectiveMatrix` valore in combinazione con le matrici di rotazione. Se un oggetto graphics da ruotare ha le coordinate X o Y maggiore `depth`, quindi la rotazione dell'oggetto nello spazio 3D è probabile che coinvolgono le coordinate Z maggiore `depth`. Questo deve essere evitato. Durante la creazione `perspectiveMatrix` si desidera impostare `depth` su un valore sufficientemente elevato per tutte le coordinate dell'oggetto di grafica, indipendentemente dal modo in cui la rotazione. Ciò garantisce che non sia mai qualsiasi divisione per zero.

La combinazione di punto di vista di rotazione 3D richiede la moltiplicazione delle matrici di 4 per 4 tra loro. A tale scopo, `SKMatrix44` definisce i metodi di concatenazione. Se `A` e `B` sono `SKMatrix44` oggetti, quindi il codice seguente imposta un'uguale a un × b:

```csharp
A.PostConcat(B);
```

Quando viene usata una matrice di trasformazione 4 per 4 in un sistema di grafica 2D, viene applicato a oggetti 2D. Questi oggetti sono fissi e si presume abbiano le coordinate Z pari a zero. La moltiplicazione di trasformazione è un po' più semplice che la trasformazione illustrata in precedenza:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Tale valore 0 per ottenere risultati z nelle formule di trasformazione, che non comprendono tutte le celle nella terza riga della matrice:

x' = M11·x + M21·y + M41

y' = M12·x M22·y + M42

z' = M13·x M23·y + M43

w' = M14·x M24·y + M44

Inoltre, il valore z' coordinata è irrilevante qui anche. Quando un oggetto 3D viene visualizzato in un sistema di grafica 2D, ignorando i valori delle coordinate Z è compresso in un oggetto bidimensionale. Le formule di trasformazione sono semplicemente questi due:

`x" = x' / w'`

`y" = y' / w'`

Ciò significa che la terza riga *e* terza colonna della matrice 4 per 4 può essere ignorata.

Ma se si tratta di questa operazione, il motivo per cui è in primo luogo la matrice 4 per 4 persino necessari?

Anche se la terza riga e della terza colonna del 4 per 4 sono irrilevanti per le trasformazioni bidimensionali, la terza riga e colonna *scopo* svolgono un ruolo prima che quando varie `SKMatrix44` valori vengono moltiplicati insieme. Si supponga, ad esempio, che si moltiplica la rotazione intorno all'asse Y con la trasformazione di punto di vista:

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

Nel prodotto, la cella `M14` ora contiene un valore di punto di vista. Se si desidera applicare tale matrice per gli oggetti 2D, la terza riga e colonna vengono eliminati per convertirla in una matrice 3 per 3:

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

A questo punto può essere utilizzato per trasformare un punto 2D:

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

Le formule di trasformazione sono:

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

A questo punto tutto dividere z':

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

Quando gli oggetti 2D vengono ruotati con un angolo positivo intorno all'asse Y, quindi positivo valori X spariscono sullo sfondo mentre negativo X valori provengono in primo piano. I valori X sembrano di avvicinarsi all'asse Y (ovvero in base al valore del coseno) come coordinate più lontano dall'asse Y diventa più piccoli o ingrandita man mano che si spostano più lontano dal visualizzatore o più vicino al visualizzatore.

Quando si usa `SKMatrix44`, eseguire tutte le operazioni di prospettiva e rotazione 3D moltiplicando vari `SKMatrix44` valori. Quindi è possibile estrarre una matrice bidimensionale di 3 per 3 da 4 per 4 matrice usando il [ `Matrix` ](xref:SkiaSharp.SKMatrix44.Matrix) proprietà del `SKMatrix44` classe. Questa proprietà restituisce una familiare `SKMatrix` valore.

Il **rotazione 3D** pagina consente di sperimentare rotazione 3D. Il [ **Rotation3DPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml) file crea un'istanza di quattro dispositivi di scorrimento per impostare la rotazione intorno all'asse X, Y e Z e per impostare un valore di profondità:

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

Si noti che il `depthSlider` viene inizializzata con un `Minimum` pari a 250. Ciò implica che l'oggetto 2D da ruotare qui ha le coordinate X e Y limitate a un cerchio definito da un raggio di 250 pixel intorno all'origine. Qualsiasi rotazione dell'oggetto nello spazio 3D causerà sempre coordinati valori inferiori a 250.

Il [ **Rotation3DPage.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs) file code-behind carica in una bitmap di 300 pixel quadrati:

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

Se la trasformazione 3D coincide con questa bitmap, quindi coordinate X e Y sono compresi tra –150 e 150, mentre gli angoli sono 212 pixel nell'area, in modo che tutto ciò che è all'interno del raggio di 250 pixel.

Il `PaintSurface` Crea gestore `SKMatrix44` gli oggetti in base i dispositivi di scorrimento e li moltiplica tra loro tramite `PostConcat`. Il `SKMatrix` valore estratto dal finale `SKMatrix44` oggetto è circondato da tradurre le trasformazioni per allineare al centro di rotazione al centro dello schermo:

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

Quando si utilizza il dispositivo di scorrimento quarto, si noterà che le impostazioni di profondità diversi non spostare ulteriormente l'oggetto lontano dall'osservatore, ma invece modificano l'estensione dell'effetto di punto di vista:

[![](3d-rotation-images/rotation3d-small.png "Tripla screenshot della pagina rotazione 3D")](3d-rotation-images/rotation3d-large.png#lightbox "tripla screenshot della pagina rotazione 3D")

Il **aggiungendo un'animazione di rotazione 3D** Usa anche `SKMatrix44` per animare una stringa di testo nello spazio 3D. Il `textPaint` oggetto impostato come un campo viene usato nel costruttore per determinare i limiti del testo:

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

Il `OnAppearing` sostituzione definisce tre xamarin. Forms `Animation` oggetti da animare il `xRotationDegrees`, `yRotationDegrees`, e `zRotationDegrees` campi con ritmi diversi. Si noti che i periodi di queste animazioni vengono impostati su un numero primo numeri (5 secondi, 7 secondi e 11 secondi) in modo che solo la combinazione globale si ripete ogni 385 secondi o più di 10 minuti:

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

Come nel programma precedente, il `PaintCanvas` gestore crea `SKMatrix44` i valori di rotazione e prospettiva e li moltiplica insieme:

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

Questa rotazione 3D è racchiusa tra più trasformazioni 2D per passare al centro della rotazione al centro dello schermo e per aumentare le dimensioni della stringa di testo in modo che sia la stessa larghezza schermo:

[![](3d-rotation-images/animatedrotation3d-small.png "Tripla screenshot della pagina aggiungendo un'animazione di rotazione 3D")](3d-rotation-images/animatedrotation3d-large.png#lightbox "tripla screenshot della pagina aggiungendo un'animazione di rotazione 3D")


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
