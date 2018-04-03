---
title: Rotazioni 3D
description: Utilizzare le trasformazioni non affini per ruotare gli oggetti 2D nello spazio 3D.
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: charlespetzold
ms.author: chape
ms.date: 04/14/2017
ms.openlocfilehash: a7d76bfad6a34bcd43b304a6ea4a8f9210e3550c
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="3d-rotations"></a>Rotazioni 3D

_Utilizzare le trasformazioni non affini per ruotare gli oggetti 2D nello spazio 3D._

Un'applicazione più comune di trasformazioni non affini simula la rotazione di un oggetto di nello spazio 3D 2D:

![](3d-rotation-images/3drotationsexample.png "Una stringa di testo ruotato nello spazio 3D")

Questa attività comporta l'utilizzo di rotazioni tridimensionali e quindi la derivazione di una non affini `SKMatrix` trasformazione che esegue queste rotazioni 3D.

È difficile sviluppare `SKMatrix` trasformazione utilizza esclusivamente all'interno di due dimensioni. Il processo diventa molto più semplice quando la matrice 3 per 3 è derivato da una matrice 4 per 4 usata nella grafica 3D. SkiaSharp include il [ `SKMatrix44` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.PreConcat/p/SkiaSharp.SKMatrix44/) classe per questo scopo, ma alcuni sfondo nella grafica 3D è necessaria per la comprensione di rotazione 3D e la matrice di trasformazione da 4-4.

Un sistema di coordinate tridimensionale aggiunge un terzo asse Z. concettualmente chiamato, l'asse Z ad angolo retto sullo schermo. Punti di coordinate nello spazio 3D sono indicati con tre numeri: (x, y, z). In 3D sistema di coordinate utilizzato in questo articolo, aumentare i valori di X a destra e a incremento dei valori di Y visitare verso il basso, analogamente a due dimensioni. Aumentare i valori Z positivo provengono dallo schermo. L'origine è l'angolo superiore sinistro, come nei grafici 2D. La schermata è possibile considerare come un piano XY con l'asse Z ad angolo retto a questo piano.

Si tratta di un sistema di coordinate a sinistra. Se si posiziona il indice per il lato sinistro nella direzione di positivo X coordinate (a destra) e coordina il dito intermedio nella direzione crescente Y (in basso), il cursore fa riferimento nella direzione di aumentare le coordinate Z, che si estende fuori dal la schermata.

In grafica 3D, le trasformazioni sono basate su una matrice 4 per 4. Di seguito è riportata la matrice di identità da 4-4:

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

Utilizzo di una matrice 4 per 4, è utile identificare le celle con i relativi numeri di riga e colonna:

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

Tuttavia, il SkiaSharp `Matrix44` classe è leggermente diversa. L'unico modo per impostare o ottenere singoli valori di cella `SKMatrix44` utilizzando il [ `Item` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix44.Item/p/System.Int32/System.Int32/) dell'indicizzatore. Gli indici di riga e colonna sono in base zero anziché in base uno, e vengono scambiate le righe e colonne. La cella M14 nel diagramma sopra riportato è possibile accedere utilizzando l'indicizzatore `[3, 0]` in un `SKMatrix44` oggetto.

In un sistema grafico 3D, un punto 3D (x, y, z) viene convertito in una matrice da 1 a 4 per la moltiplicazione per la matrice di trasformazione da 4-4:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Analogo a 2D le trasformazioni che si verificano in tre dimensioni, le trasformazioni 3D si presuppone che si verificano in quattro dimensioni. La dimensione del quarto è detto W, e si presuppone che lo spazio 3D esiste nello spazio 4D in coordinate W sono uguali a 1. Di seguito sono elencate le formule di trasformazione:

x' = M11·x + M21·y + M31·z + M41

y' = M12·x + M22·y + M32·z + M42

z' = M13·x + M23·y + M33·z + M43

w' = M14·x + M24·y + M34·z + M44

È evidente dalle formule di trasformazione che le celle `M11`, `M22`, `M33` sono fattori di scala nelle direzioni X, Y e Z, e `M41`, `M42`, e `M43` sono fattori di traslazione X, Y e Z direzioni.

Per convertire queste coordinate nuovamente lo spazio 3D in cui è uguale a 1, x W', y', e z 'coordinate sono tutti diviso per w':

x" = x' / w'

'y = y' /w '

z" = z' / w'

w" = w' / w' = 1

La divisione per w' offre prospettiva nello spazio 3D. Se w' uguale a 1, quindi si verifica alcuna prospettiva.

Rotazioni nello spazio 3D possono essere molto complesse, ma le rotazioni più semplice quelli intorno all'asse X, Y e Z. Una rotazione dell'angolo α intorno all'asse X è la matrice:

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

I valori di X rimangono invariati quando è sottoposto a questa trasformazione. La rotazione intorno all'asse Y lascia i valori di Y invariata:

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

La rotazione intorno all'asse Z è uguale a quello di grafica 2D:

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

Il lato operativo del sistema di coordinate è implicita la direzione di rotazione. Si tratta di un sistema a mano, pertanto se si posiziona il cursore di mano sinistra per aumentare i valori per un asse particolare, a destra per la rotazione intorno all'asse X, verso il basso per la rotazione intorno all'asse Y e verso il basso per la rotazione intorno all'asse Z, quindi la curva di, le altre dita indica la direzione di rotazione per angoli positivi.

`SKMatrix44` è generalizzato statico [ `CreateRotation` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.CreateRotation/p/System.Single/System.Single/System.Single/System.Single/) e [ `CreateRotationDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.CreateRotationDegrees/p/System.Single/System.Single/System.Single/System.Single/) metodi che consentono di specificare l'asse intorno al quale si verifica la rotazione:

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

Impostare i primi tre argomenti per la rotazione intorno all'asse X, 1, 0, 0. Per eseguire la rotazione intorno all'asse Y, impostati su 0, 1, 0 e per la rotazione intorno all'asse Z, impostarli su 0, 0, 1.

La quarta colonna 4 per 4 è per la prospettiva. Il `SKMatrix44` non ha metodi per la creazione di trasformazioni prospettiva, ma è possibile creare uno manualmente usando il codice seguente:

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

Il motivo per il nome dell'argomento `depth` sarà evidente a breve. Tale codice crea una matrice:

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

Le formule di trasformazione comportare il seguente calcolo w':

w' = – z / profondità + 1

Questo consente di ridurre le coordinate X e Y quando i valori di Z sono minori di zero (concettualmente dietro il piano XY) e per aumentare le coordinate X e Y per i valori positivi di Z. Quando la coordinata Z è uguale a `depth`, quindi w' è zero, nonché le coordinate infinite. Sistemi grafici tridimensionali basati su una metafora, fotocamera e `depth` valore rappresenta la distanza della camera dall'origine del sistema di coordinate. Se un oggetto grafico ha Z coordinata ovvero `depth` unità dall'origine, tocca a livello concettuale l'obiettivo della fotocamera e diventa grande all'infinito.

Tenere presente che sarà probabilmente possibile usare questo `perspectiveMatrix` valore in combinazione con matrici di rotazione. Se un oggetto graphics da ruotare è maggiore di coordinate X o Y `depth`, quindi la rotazione di questo oggetto di nello spazio 3D è probabile che coinvolgono le coordinate Z maggiore `depth`. Questo deve essere evitato. Quando si creano `perspectiveMatrix` si desidera impostare `depth` su un valore sufficientemente grande per tutte le coordinate nell'oggetto grafico indipendentemente dalla modalità di rotazione. Ciò garantisce che sia mai qualsiasi divisione per zero.

La combinazione di prospettiva e rotazioni 3D richiede moltiplicazione di matrici da 4-4. A tale scopo, `SKMatrix44` definisce i metodi di concatenazione. Se `A` e `B` sono `SKMatrix44` oggetti, quindi il codice seguente imposta un uguale a un × b:

```csharp
A.PostConcat(B);
```

Quando una matrice di trasformazione da 4-4 viene utilizzata in un sistema grafico 2D, questo viene applicato a oggetti 2D. Questi oggetti sono flat e si presuppone che le coordinate Z pari a zero. La moltiplicazione di trasformazione è un po' più semplice che la trasformazione illustrata in precedenza:

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Il valore 0 per ottenere risultati z nelle formule di trasformazione che non riguardano le celle nella terza riga della matrice:

x' = M11·x + M21·y + M41

y' = M12·x + M22·y + M42

z' = M13·x + M23·y + M43

w' = M14·x + M24·y + M44

Inoltre, la z' coordinata è irrilevante qui anche. Quando un oggetto 3D viene visualizzato in un sistema grafico 2D, compresso in un oggetto bidimensionale ignorando i valori delle coordinate Z. Le formule di trasformazione sono in realtà solo queste due:

x" = x' / w'

'y = y' /w '

Ciò significa che la terza riga *e* terza colonna della matrice 4 per 4 può essere ignorata.

Ma se questa operazione, perché è in primo luogo la matrice 4 per 4 persino necessari?

Anche se la terza riga e nella terza colonna di 4 per 4 sono irrilevanti per trasformazioni bidimensionale, la terza riga e colonna *si* svolgono un ruolo prima che quando varie `SKMatrix44` valori vengono moltiplicati insieme. Si supponga, ad esempio, si moltiplica di rotazione intorno all'asse Y con la trasformazione del punto di vista:

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

Nel prodotto, la cella `M14` ora contiene un valore di prospettiva. Se si desidera applicare tale matrice di oggetti 2D, la terza riga e colonna vengono eliminati per convertirla in una matrice 3 per 3:

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

Ora può essere utilizzato per trasformare un punto 2D:

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

Le formule di trasformazione sono:

x' = cos(α)·x

y' = y

z' = (sin(α)/depth)·x + 1

Ora tutto diviso z':

"x = cos ·x (α) / ((sin (α) / profondità) ·x + 1)

"y = y / ((sin (α) / profondità) ·x + 1)

Quando gli oggetti 2D vengono ruotati con un angolo positivo intorno all'asse Y, quindi positivo valori X si abbassano in background mentre negativo X valori provengono in primo piano. I valori X sembrano per spostare più vicino all'asse Y (ovvero in base al valore coseno) come coordinate più l'asse Y diventa minore o maggiore durante lo spostamento del visualizzatore o più vicino al visualizzatore.

Quando si utilizza `SKMatrix44`, eseguire tutte le operazioni di prospettiva e rotazione 3D moltiplicando vari `SKMatrix44` valori. Quindi è possibile estrarre una matrice bidimensionale di 3 per 3 da 4 per 4 utilizzando una matrice di [ `Matrix` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix44.Matrix/) proprietà del `SKMatrix44` classe. Questa proprietà restituisce già `SKMatrix` valore.

Il **rotazione 3D** pagina consente sperimentare rotazione 3D. Il [ **Rotation3DPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml) quattro dispositivi di scorrimento per impostare la rotazione intorno all'asse X, Y e Z e per impostare un valore di profondità crea un'istanza di file:

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

Si noti che il `depthSlider` viene inizializzata con un `Minimum` valore pari a 250. Ciò implica che l'oggetto 2D da ruotare qui ha coordinate X e Y limitate a un cerchio definito da un raggio di 250 pixel intorno all'origine. Coordinate valori inferiori a 250 si otterrà sempre alcuna rotazione di questo oggetto di nello spazio 3D.

Il [ **Rotation3DPage.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs) file code-behind viene caricata in una bitmap di 300 pixel quadrati:

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
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

Se la trasformazione 3D è incentrata su questa bitmap, quindi coordinate X e Y sono compresi tra –150 e 150, mentre gli angoli sono 212 pixel di distanza dal centro, pertanto tutti gli elementi all'interno del raggio di 250 pixel.

Il `PaintSurface` gestore crea `SKMatrix44` oggetti in base ai dispositivi di scorrimento e moltiplica tra loro tramite `PostConcat`. Il `SKMatrix` valore estratto dalla finale `SKMatrix44` oggetto è circondato da tradurre le trasformazioni per allineare al centro di rotazione al centro dello schermo:

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
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

Quando si utilizza il dispositivo di scorrimento quarto, si noterà che le impostazioni di diversi profondità non spostare ulteriormente l'oggetto dal visualizzatore, ma invece modificano l'estensione della prospettiva effetto:

[![](3d-rotation-images/rotation3d-small.png "Schermata triplo della rotazione 3D pagina")](3d-rotation-images/rotation3d-large.png#lightbox "tripla schermata della pagina rotazione 3D")

Il **aggiungendo un'animazione di rotazione 3D** utilizza inoltre `SKMatrix44` per aggiungere un'animazione a una stringa di testo nello spazio 3D. Il `textPaint` oggetto impostato come un campo viene usato nel costruttore per determinare i limiti del testo:

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

Il `OnAppearing` override definisce tre xamarin. Forms `Animation` oggetti da cui iniziare l'animazione di `xRotationDegrees`, `yRotationDegrees`, e `zRotationDegrees` campi a velocità diverse. Si noti che i periodi di tali animazioni siano impostati su numeri primi, ovvero 5 secondi, 7 secondi e secondi 11, pertanto la combinazione globale ripete solo ogni 385 secondi o più di 10 minuti:

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

Come il programma precedente, il `PaintCanvas` gestore crea `SKMatrix44` i valori per la rotazione e prospettiva e moltiplica insieme:

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

Questa rotazione 3D è racchiuso tra più trasformazioni 2D per spostare il centro di rotazione al centro dello schermo e ridimensionare le dimensioni della stringa di testo in modo che sia la stessa larghezza schermo:

[![](3d-rotation-images/animatedrotation3d-small.png "Schermata di triplo della pagina aggiungendo un'animazione di rotazione 3D")](3d-rotation-images/animatedrotation3d-large.png#lightbox "tripla schermata della pagina aggiungendo un'animazione di rotazione 3D")


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
