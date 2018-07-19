---
title: Manipolazioni tramite tocco
description: Questo articolo illustra come usare le trasformazioni di matrice per implementare il trascinamento di tocco, avvicinare e la rotazione e viene illustrata questa con codice di esempio.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: charlespetzold
ms.author: chape
ms.date: 04/03/2018
ms.openlocfilehash: 2de5b9a3a6bf0d36330212a52ba5c7278b970efc
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130907"
---
# <a name="touch-manipulations"></a>Manipolazioni tramite tocco

_Trasformazioni di usare matrici per implementare il trascinamento di tocco, avvicinare e rotazione_

Negli ambienti multi-touch, ad esempio quelli nei dispositivi mobili, gli utenti usano spesso le dita per manipolare gli oggetti sullo schermo. I movimenti comuni, ad esempio un trascinamento di un dito e una con avvicinamento delle dita con due dita possono spostare e ridimensionare gli oggetti o persino ruotarle. Questi movimenti sono generalmente implementati usando matrici di trasformazione e questo articolo illustra come eseguire questa operazione.

![](touch-images/touchmanipulationsexample.png "Una bitmap sottoposta alla traslazione, ridimensionamento e rotazione")

## <a name="manipulating-one-bitmap"></a>La modifica di una singola Bitmap

Il **Touch manipolazione** illustra come manipolazioni di tocco su una singola bitmap.
In questo esempio Usa l'effetto di tocco di rilevamento presentato nell'articolo [richiamo eventi dagli effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

Numerosi altri file forniscono supporto per la **Touch manipolazione** pagina. Il primo è il [ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) enumerazione che indica i diversi tipi di implementata dal codice si sarà possibile visualizzare la modifica del tocco:

```csharp
enum TouchManipulationMode
{
    None,
    PanOnly,
    IsotropicScale,     // includes panning
    AnisotropicScale,   // includes panning
    ScaleRotate,        // implies isotropic scaling
    ScaleDualRotate     // adds one-finger rotation
}
```

`PanOnly` è un trascinamento di un dito quello che viene implementato tramite la conversione. Tutte le opzioni successive anche includere la panoramica ma implicano due dita: `IsotropicScale` è un'operazione con avvicinamento delle dita che restituisce l'oggetto scalabilità in modo uniforme nelle direzioni orizzontale e verticale. `AnisotropicScale` Consente di scalare non uguali.

Il `ScaleRotate` opzione è per la scalabilità con due dita e rotazione. Il ridimensionamento è isotropo. Implementazione di due dita rotazione con adattamento filtraggio anisotropo della è problematico perché i movimenti con un dito sono sostanzialmente uguali.

Il `ScaleDualRotate` opzione consente di aggiungere un dito una rotazione. Quando un singolo dito trascina l'oggetto, l'oggetto trascinato prima di tutto viene ruotato intorno al relativo centro in modo che il centro dell'oggetto con il vettore di trascinamento.

Il [ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) file include un `Picker` con i membri del `TouchManipulationMode` enumerazione:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.TouchManipulationPage"
             Title="Touch Manipulation">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker Title="Touch Mode"
                Grid.Row="0"
                SelectedIndexChanged="OnTouchModePickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>None</x:String>
                <x:String>PanOnly</x:String>
                <x:String>IsotropicScale</x:String>
                <x:String>AnisotropicScale</x:String>
                <x:String>ScaleRotate</x:String>
                <x:String>ScaleDualRotate</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                4
            </Picker.SelectedIndex>
        </Picker>

        <Grid BackgroundColor="White"
              Grid.Row="1">

            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>
    </Grid>
</ContentPage>
```

Nella parte inferiore è un' `SKCanvasView` e una `TouchEffect` collegata a cella singola `Grid` che lo racchiude.

Il [ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) file code-behind è un `bitmap` campo ma non è di tipo `SKBitmap`. Il tipo è `TouchManipulationBitmap` (una classe si vedrà a breve):

```csharp
public partial class TouchManipulationPage : ContentPage
{
    TouchManipulationBitmap bitmap;
    ...

    public TouchManipulationPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.MountainClimbers.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            SKBitmap bitmap = SKBitmap.Decode(stream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

Costruttore crea un `TouchManipulationBitmap` oggetto, passando al costruttore un `SKBitmap` ottenuto da una risorsa incorporata. Il costruttore termina impostando il `Mode` proprietà del `TouchManager` proprietà delle `TouchManipulationBitmap` oggetto a un membro del `TouchManipulationMode` enumerazione.

Il `SelectedIndexChanged` gestore per il `Picker` imposta anche questo `Mode` proprietà:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            TouchManipulationMode mode;
            Enum.TryParse(picker.Items[picker.SelectedIndex], out mode);
            bitmap.TouchManager.Mode = mode;
        }
    }
    ...
}
```

Il `TouchAction` gestore del `TouchEffect` creata un'istanza di chiamate di file XAML in due metodi in `TouchManipulationBitmap` denominato `HitTest` e `ProcessTouchEvent`:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    List<long> touchIds = new List<long>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (bitmap.HitTest(point))
                {
                    touchIds.Add(args.Id);
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    break;
                }
                break;

            case TouchActionType.Moved:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    touchIds.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Se il `HitTest` restituzione del metodo `true` &mdash; vale a dire che un dito ha interessate schermo all'interno dell'area occupata dal bitmap &mdash; ID tocco viene aggiunto al `TouchIds` raccolta. Questo ID rappresenta la sequenza di eventi di tocco per tale dito fino a quando non solleva il dito dallo schermo. Se la bitmap di tocco più dita il `touchIds` raccolta contiene un ID tocco per ogni dito.

Il `TouchAction` gestore chiama anche il `ProcessTouchEvent` classe `TouchManipulationBitmap`. Si tratta in cui alcuni, ma non tutte, del tocco reale viene eseguita l'elaborazione.

Il [ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) classe è una classe wrapper per `SKBitmap` che contiene il codice per eseguire il rendering di bitmap ed elaborare gli eventi di tocco. Funziona in combinazione con più generalizzati codice in un `TouchManipulationManager` classe (che si vedrà a breve).

Il `TouchManipulationBitmap` costruttore salva la `SKBitmap` e crea un'istanza di due proprietà, il `TouchManager` vlastnosti typu `TouchManipulationManager` e il `Matrix` vlastnosti typu `SKMatrix`:

```csharp
class TouchManipulationBitmap
{
    SKBitmap bitmap;
    ...

    public TouchManipulationBitmap(SKBitmap bitmap)
    {
        this.bitmap = bitmap;
        Matrix = SKMatrix.MakeIdentity();

        TouchManager = new TouchManipulationManager
        {
            Mode = TouchManipulationMode.ScaleRotate
        };
    }

    public TouchManipulationManager TouchManager { set; get; }

    public SKMatrix Matrix { set; get; }
    ...
}
```

Ciò `Matrix` proprietà è la trasformazione accumulata risultanti da tutte le attività di tocco. Come si vedrà, ogni evento di tocco viene risolta in una matrice, che quindi viene concatenata con il `SKMatrix` valore archiviato per il `Matrix` proprietà.

Il `TouchManipulationBitmap` oggetto disegna se stesso relativo `Paint` (metodo). L'argomento è un `SKCanvas` oggetto. Ciò `SKCanvas` potrebbe essere già presente una trasformazione applicata in modo che il `Paint` metodo consente di concatenare il `Matrix` proprietà associati con la mappa di bit per la trasformazione esistente e consente di ripristinare l'area di disegno dopo aver terminato:

```csharp
class TouchManipulationBitmap
{
    ...
    public void Paint(SKCanvas canvas)
    {
        canvas.Save();
        SKMatrix matrix = Matrix;
        canvas.Concat(ref matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();
    }
    ...
}
```

Il `HitTest` restituzione del metodo `true` se l'utente tocca lo schermo in un momento entro i limiti della bitmap. Mentre l'utente modifica la bitmap, bitmap può essere ruotata, o anche (tramite una combinazione di filtraggio anisotropo della scalabilità e rotazione) essere nella forma del parallelogramma. Si potrebbe ritenere che il `HitTest` metodo deve implementare in tal caso geometria analitica piuttosto complessa.

Tuttavia, è disponibile un collegamento:

Determinare se un punto si trova entro i limiti di un rettangolo trasformato è quello utilizzato per determinare se un punto trasformato inverso si trova entro i limiti del rettangolo di stato non trasformato. Questo è un calcolo molto più semplice e può usare il pratico `Contains` definito dal metodo `SKRect`:

```csharp
class TouchManipulationBitmap
{
    ...
    public bool HitTest(SKPoint location)
    {
        // Invert the matrix
        SKMatrix inverseMatrix;

        if (Matrix.TryInvert(out inverseMatrix))
        {
            // Transform the point using the inverted matrix
            SKPoint transformedPoint = inverseMatrix.MapPoint(location);

            // Check if it's in the untransformed bitmap rectangle
            SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
            return rect.Contains(transformedPoint);
        }
        return false;
    }
    ...
}
```

Il secondo metodo pubblico nel `TouchManipulationBitmap` è `ProcessTouchEvent`. Quando viene chiamato questo metodo, si è già stato stabilito che l'evento di tocco appartiene a questa bitmap particolare. Il metodo gestisce un dizionario della [ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) gli oggetti che è semplicemente al punto precedente e il nuovo punto di ogni dito:

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

Ecco il dizionario e `ProcessTouchEvent` metodo di stesso:

```csharp
class TouchManipulationBitmap
{
    ...
    Dictionary<long, TouchManipulationInfo> touchDictionary =
        new Dictionary<long, TouchManipulationInfo>();
    ...
    public void ProcessTouchEvent(long id, TouchActionType type, SKPoint location)
    {
        switch (type)
        {
            case TouchActionType.Pressed:
                touchDictionary.Add(id, new TouchManipulationInfo
                {
                    PreviousPoint = location,
                    NewPoint = location
                });
                break;

            case TouchActionType.Moved:
                TouchManipulationInfo info = touchDictionary[id];
                info.NewPoint = location;
                Manipulate();
                info.PreviousPoint = info.NewPoint;
                break;

            case TouchActionType.Released:
                touchDictionary[id].NewPoint = location;
                Manipulate();
                touchDictionary.Remove(id);
                break;

            case TouchActionType.Cancelled:
                touchDictionary.Remove(id);
                break;
        }
    }
    ...
}
```

Nel `Moved` e `Released` eventi, le chiamate al metodo `Manipulate`. In questi casi, il `touchDictionary` contiene uno o più `TouchManipulationInfo` oggetti. Se il `touchDictionary` contiene un elemento, è probabile che il `PreviousPoint` e `NewPoint` valori non sono uguali e rappresentano lo spostamento di un dito. Se più dita stanno toccando la mappa di bit, il dizionario contiene più di un elemento, ma solo uno di questi elementi presenta diversi `PreviousPoint` e `NewPoint` valori. Tutti gli altri hanno uguale `PreviousPoint` e `NewPoint` valori.

Questo aspetto è importante: il `Manipulate` metodo può presupporre che sia in corso lo spostamento di un solo dito. Al momento della chiamata nessuna delle altre dita stanno spostando e se è realmente spostano (come probabilmente), tali spostamenti verranno elaborati in chiamate successive a `Manipulate`.

Il `Manipulate` metodo copia prima di tutto il dizionario in una matrice per motivi di praticità. Ignora un valore diverso dalle prime due voci. Se più di due dita siano tentando di modificare la mappa di bit, gli altri vengono ignorati. `Manipulate` è il membro finale della `TouchManipulationBitmap`:

```csharp
class TouchManipulationBitmap
{
    ...
    void Manipulate()
    {
        TouchManipulationInfo[] infos = new TouchManipulationInfo[touchDictionary.Count];
        touchDictionary.Values.CopyTo(infos, 0);
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();

        if (infos.Length == 1)
        {
            SKPoint prevPoint = infos[0].PreviousPoint;
            SKPoint newPoint = infos[0].NewPoint;
            SKPoint pivotPoint = Matrix.MapPoint(bitmap.Width / 2, bitmap.Height / 2);

            touchMatrix = TouchManager.OneFingerManipulate(prevPoint, newPoint, pivotPoint);
        }
        else if (infos.Length >= 2)
        {
            int pivotIndex = infos[0].NewPoint == infos[0].PreviousPoint ? 0 : 1;
            SKPoint pivotPoint = infos[pivotIndex].NewPoint;
            SKPoint newPoint = infos[1 - pivotIndex].NewPoint;
            SKPoint prevPoint = infos[1 - pivotIndex].PreviousPoint;

            touchMatrix = TouchManager.TwoFingerManipulate(prevPoint, newPoint, pivotPoint);
        }

        SKMatrix matrix = Matrix;
        SKMatrix.PostConcat(ref matrix, touchMatrix);
        Matrix = matrix;
    }
}
```

Se un dito manipola la mappa di bit, `Manipulate` chiama il `OneFingerManipulate` metodo il `TouchManipulationManager` oggetto. Per due dita, chiama `TwoFingerManipulate`. Gli argomenti a questi metodi sono gli stessi: il `prevPoint` e `newPoint` gli argomenti rappresentano il dito che è in movimento. Ma la `pivotPoint` argomento è diverso per le due chiamate:

Per la manipolazione di un dito, il `pivotPoint` corrisponde al punto centrale della bitmap. Questa è la possibilità di rotazione di un dito. Per la manipolazione di due dita, l'evento indica lo spostamento di un solo dito, in modo che il `pivotPoint` è il dito che non è in movimento.

In entrambi i casi `TouchManipulationManager` restituisce un `SKMatrix` valore, che consente di concatenare il metodo con l'attuale `Matrix` proprietà che `TouchManipulationPage` Usa per eseguire il rendering della bitmap.

`TouchManipulationManager` è stato generalizzato e viene utilizzato alcun altro file, ad eccezione `TouchManipulationMode`. È possibile utilizzare questa classe senza alcuna modifica nelle proprie applicazioni. Definisce una singola proprietà di tipo `TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```


Tuttavia, è opportuno evitare di `AnisotropicScale` opzione. È molto semplice con questa opzione per modificare la mappa di bit in modo che uno dei fattori di ridimensionamento diventa zero. Ciò rende scompaiano dalla vista, mai per restituire la bitmap. Se è realmente necessario filtraggio anisotropo della scalabilità, si migliora la logica per evitare risultati indesiderati.

`TouchManipulationManager` Usa dei vettori, ma poiché è presente alcun `SKVector` struttura di SkiaSharp, `SKPoint` viene invece usato. `SKPoint` supporta che l'operatore di sottrazione e il risultato può essere considerato come un vettore. La logica solo vettore specifici necessari da aggiungere è un `Magnitude` calcolo:

```csharp
class TouchManipulationManager
{
    ...
    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
}
```

Ogni volta che è stata selezionata la rotazione, entrambi i metodi di modifica di un dito e due dita gestiscono prima di tutto la rotazione. Se viene rilevata alcuna rotazione, viene rimosso in modo efficace il componente di rotazione. Il valore restante viene interpretata come la panoramica e la scalabilità.

Di seguito è riportato il `OneFingerManipulate` (metodo). Se un dito una rotazione non è stata abilitata, quindi la logica è semplice &mdash; utilizza semplicemente il punto precedente e nuovo per costruire un vettore denominato `delta` che corrisponde esattamente a translation. Con un dito una rotazione abilitata, il metodo Usa gli angoli dal punto pivot (ovvero il centro della bitmap) per il punto precedente e nuovo per costruire una matrice di rotazione:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }

    public SKMatrix OneFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        if (Mode == TouchManipulationMode.None)
        {
            return SKMatrix.MakeIdentity();
        }

        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint delta = newPoint - prevPoint;

        if (Mode == TouchManipulationMode.ScaleDualRotate)  // One-finger rotation
        {
            SKPoint oldVector = prevPoint - pivotPoint;
            SKPoint newVector = newPoint - pivotPoint;

            // Avoid rotation if fingers are too close to center
            if (Magnitude(newVector) > 25 && Magnitude(oldVector) > 25)
            {
                float prevAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                // Calculate rotation matrix
                float angle = newAngle - prevAngle;
                touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                // Effectively rotate the old vector
                float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                oldVector.X = magnitudeRatio * newVector.X;
                oldVector.Y = magnitudeRatio * newVector.Y;

                // Recalculate delta
                delta = newVector - oldVector;
            }
        }

        // Multiply the rotation matrix by a translation matrix
        SKMatrix.PostConcat(ref touchMatrix, SKMatrix.MakeTranslation(delta.X, delta.Y));

        return touchMatrix;
    }
    ...
}
```

Nel `TwoFingerManipulate` metodo, il punto di perno è la posizione del dito che non è in movimento in questo evento di tocco specifico. La rotazione è molto simile alla rotazione di un dito e quindi il vettore denominato `oldVector` (in base al punto precedente) viene regolata la rotazione. Lo spostamento rimanente viene interpretato come la scalabilità:

```csharp
class TouchManipulationManager
{
    ...
    public SKMatrix TwoFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint oldVector = prevPoint - pivotPoint;
        SKPoint newVector = newPoint - pivotPoint;

        if (Mode == TouchManipulationMode.ScaleRotate ||
            Mode == TouchManipulationMode.ScaleDualRotate)
        {
            // Find angles from pivot point to touch points
            float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
            float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

            // Calculate rotation matrix
            float angle = newAngle - oldAngle;
            touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

            // Effectively rotate the old vector
            float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
            oldVector.X = magnitudeRatio * newVector.X;
            oldVector.Y = magnitudeRatio * newVector.Y;
        }

        float scaleX = 1;
        float scaleY = 1;

        if (Mode == TouchManipulationMode.AnisotropicScale)
        {
            scaleX = newVector.X / oldVector.X;
            scaleY = newVector.Y / oldVector.Y;

        }
        else if (Mode == TouchManipulationMode.IsotropicScale ||
                 Mode == TouchManipulationMode.ScaleRotate ||
                 Mode == TouchManipulationMode.ScaleDualRotate)
        {
            scaleX = scaleY = Magnitude(newVector) / Magnitude(oldVector);
        }

        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
        {
            SKMatrix.PostConcat(ref touchMatrix,
                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y));
        }

        return touchMatrix;
    }
    ...
}
```

Si noterà che non vi è alcuna conversione esplicita in questo metodo. Tuttavia, entrambe le `MakeRotation` e `MakeScale` metodi si basano sul punto di perno, e che include la conversione implicita. Se si usa due dita su bitmap e trascinarli nella stessa direzione, `TouchManipulation` otterrà una serie di eventi di tocco alternando tra due dita. Come ogni dito viene modificata rispetto a altro, ridimensionamento o rotazione risultati, ma negata da movimento del dito e il risultato è la traduzione.

L'unica parte rimanente del **Touch Manipulation** pagina è la `PaintSurface` gestore nel `TouchManipulationPage` file code-behind. Chiama il `Paint` metodo del `TouchManipulationBitmap`, cui viene applicata la matrice che rappresenta l'attività touch accumulato:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    MatrixDisplay matrixDisplay = new MatrixDisplay();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        bitmap.Paint(canvas);

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(bitmap.Matrix);

        matrixDisplay.Paint(canvas, bitmap.Matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));
    }
}
```

Il `PaintSurface` gestore termina visualizzando un `MatrixDisplay` oggetto mostrando la matrice accumulato tocco:

[![](touch-images/touchmanipulation-small.png "Tripla screenshot della pagina di modifica tocco")](touch-images/touchmanipulation-large.png#lightbox "tripla screenshot della pagina di manipolazione Touch")

## <a name="manipulating-multiple-bitmaps"></a>La modifica più bitmap

Uno dei vantaggi dell'isolamento del codice di tocco per l'elaborazione nelle classi, ad esempio `TouchManipulationBitmap` e `TouchManipulationManager` consiste nella possibilità di riutilizzare queste classi in un programma che consente all'utente di modificare più immagini bitmap.

Il **vista a dispersione delle Bitmap** pagina viene illustrato come questa operazione viene eseguita. Invece di definire un campo di tipo `TouchManipulationBitmap`, il [ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) classe definisce un `List` degli oggetti bitmap:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    List<TouchManipulationBitmap> bitmapCollection =
        new List<TouchManipulationBitmap>();
    ...
    public BitmapScatterViewPage()
    {
        InitializeComponent();

        // Load in all the available bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;
        string[] resourceIDs = assembly.GetManifestResourceNames();
        SKPoint position = new SKPoint();

        foreach (string resourceID in resourceIDs)
        {
            if (resourceID.EndsWith(".png") ||
                resourceID.EndsWith(".jpg"))
            {
                using (Stream stream = assembly.GetManifestResourceStream(resourceID))
                {
                    SKBitmap bitmap = SKBitmap.Decode(stream);
                    bitmapCollection.Add(new TouchManipulationBitmap(bitmap)
                    {
                        Matrix = SKMatrix.MakeTranslation(position.X, position.Y),
                    });
                    position.X += 100;
                    position.Y += 100;
                }
            }
        }
    }
    ...
}
```

Il costruttore viene caricato in tutte le bitmap disponibili come risorse incorporate e li aggiunge al `bitmapCollection`. Si noti che il `Matrix` proprietà viene inizializzata su ognuna `TouchManipulationBitmap` dell'oggetto, in modo che gli angoli superiore sinistro di ogni mappa di bit vengono ritardati di 100 pixel.

Il `BitmapScatterView` pagina deve anche gestire gli eventi di tocco per bitmap di più. Invece di definire un `List` di touch ID dei manipolati attualmente `TouchManipulationBitmap` oggetti, il programma richiede un dizionario:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    Dictionary<long, TouchManipulationBitmap> bitmapDictionary =
       new Dictionary<long, TouchManipulationBitmap>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                for (int i = bitmapCollection.Count - 1; i >= 0; i--)
                {
                    TouchManipulationBitmap bitmap = bitmapCollection[i];

                    if (bitmap.HitTest(point))
                    {
                        // Move bitmap to end of collection
                        bitmapCollection.Remove(bitmap);
                        bitmapCollection.Add(bitmap);

                        // Do the touch processing
                        bitmapDictionary.Add(args.Id, bitmap);
                        bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                        canvasView.InvalidateSurface();
                        break;
                    }
                }
                break;

            case TouchActionType.Moved:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    bitmapDictionary.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

Si noti che il `Pressed` per la logica consente di scorrere il `bitmapCollection` in ordine inverso. Le bitmap spesso si sovrappongono. Le bitmap in un secondo momento nella raccolta in modo visivo si trovano sopra le bitmap in precedenza nella raccolta. Se sono presenti più immagini bitmap sotto il dito che preme sullo schermo, quello in primo piano deve essere quello che viene modificato tramite il dito.

Si noti inoltre che il `Pressed` sposti quella bitmap alla fine della raccolta in modo da spostarla visivamente in cima l'accumulo delle altre bitmap.

Nel `Moved` e `Released` eventi, il `TouchAction` chiamate del gestore il `ProcessingTouchEvent` metodo nella `TouchManipulationBitmap` esattamente come il programma precedente.

Infine, il `PaintSurface` gestore chiama il `Paint` metodo della ognuno `TouchManipulationBitmap` oggetto:

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (TouchManipulationBitmap bitmap in bitmapCollection)
        {
            bitmap.Paint(canvas);
        }
    }
}
```

Il codice scorre la raccolta e visualizza l'accumulo delle bitmap dall'inizio della raccolta alla fine:

[![](touch-images/bitmapscatterview-small.png "Tripla screenshot della pagina della visualizzazione a dispersione Bitmap")](touch-images/bitmapscatterview-large.png#lightbox "tripla screenshot della pagina della visualizzazione a dispersione di Bitmap")

## <a name="single-finger-scaling"></a>Ridimensionamento di un singolo dito

Un'operazione di ridimensionamento richiede in genere un movimento di zoom indietro usando due dita. Tuttavia, è possibile implementare la scalabilità con un singolo dito facendo in modo che il dito spostare gli angoli di una bitmap.

Questa funzionalità viene illustrata la **singolo dito angolo scalabilità** pagina. Poiché in questo esempio utilizza un tipo leggermente diverso di ridimensionamento che che implementati nel `TouchManipulationManager` (classe), non usa tale classe o il `TouchManipulationBitmap` classe. È invece tutta la logica di tocco nel file code-behind. Questo è un po' più semplice per la logica superiore al normale perché tiene traccia di un solo dito alla volta e viene semplicemente ignorato qualsiasi dita secondarie che potrebbero essere toccare lo schermo.

Il [ **SingleFingerCornerScale.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml) pagina Crea un'istanza il `SKCanvasView` classe e viene creato un `TouchEffect` oggetto per la gestione degli eventi tocco:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.SingleFingerCornerScalePage"
             Title="Single Finger Corner Scale">

    <Grid BackgroundColor="White"
          Grid.Row="1">

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction"   />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

Il [ **SingleFingerCornerScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs) file carica una risorsa della bitmap dal **supporti** directory e li visualizza mediante un `SKMatrix` oggetto, definito un campo:

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();
    ···

    public SingleFingerCornerScalePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.SetMatrix(currentMatrix);
        canvas.DrawBitmap(bitmap, 0, 0);
    }
    ···
}
```

Ciò `SKMatrix` oggetto viene modificato dalla logica di tocco illustrata di seguito.

Il resto del file code-behind è la `TouchEffect` gestore dell'evento. Inizia eseguendo la conversione della posizione corrente del dito per un `SKPoint` valore. Per il `Pressed` tipo di azione, il gestore verifica che nessun altro con un dito tocca la schermata, e che si trova entro i limiti della bitmap.

La parte fondamentale del codice è un' `if` istruzione che interessa due chiamate al `Math.Pow` (metodo). Questo math controlla se il percorso di un dito è di fuori di un'ellisse che riempie la bitmap. In questo caso, che è un'operazione di ridimensionamento. Il dito è vicino a uno degli angoli della bitmap e un punto di perno è determinato che è l'angolo opposto. Se il dito all'interno di questa ellissi, è una normale operazione di panoramica:

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();

    // Information for translating and scaling
    long? touchId = null;
    SKPoint pressedLocation;
    SKMatrix pressedMatrix;

    // Information for scaling
    bool isScaling;
    SKPoint pivotPoint;
    ···

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Track only one finger
                if (touchId.HasValue)
                    return;

                // Check if the finger is within the boundaries of the bitmap
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = currentMatrix.MapRect(rect);
                if (!rect.Contains(point))
                    return;

                // First assume there will be no scaling
                isScaling = false;

                // If touch is outside interior ellipse, make this a scaling operation
                if (Math.Pow((point.X - rect.MidX) / (rect.Width / 2), 2) +
                    Math.Pow((point.Y - rect.MidY) / (rect.Height / 2), 2) > 1)
                {
                    isScaling = true;
                    float xPivot = point.X < rect.MidX ? rect.Right : rect.Left;
                    float yPivot = point.Y < rect.MidY ? rect.Bottom : rect.Top;
                    pivotPoint = new SKPoint(xPivot, yPivot);
                }

                // Common for either pan or scale
                touchId = args.Id;
                pressedLocation = point;
                pressedMatrix = currentMatrix;
                break;

            case TouchActionType.Moved:
                if (!touchId.HasValue || args.Id != touchId.Value)
                    return;

                SKMatrix matrix = SKMatrix.MakeIdentity();

                // Translating
                if (!isScaling)
                {
                    SKPoint delta = point - pressedLocation;
                    matrix = SKMatrix.MakeTranslation(delta.X, delta.Y);
                }
                // Scaling
                else
                {
                    float scaleX = (point.X - pivotPoint.X) / (pressedLocation.X - pivotPoint.X);
                    float scaleY = (point.Y - pivotPoint.Y) / (pressedLocation.Y - pivotPoint.Y);
                    matrix = SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);
                }

                // Concatenate the matrices
                SKMatrix.PreConcat(ref matrix, pressedMatrix);
                currentMatrix = matrix;
                canvasView.InvalidateSurface();
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = null;
                break;
        }
    }
}
```

Il `Moved` tipo di azione calcola una matrice corrispondente all'attività touch dal momento in cui il dito premuto la schermata fino a questo momento. Tale matrice con la matrice concatena in vigore al momento che il dito preme la bitmap. L'operazione di ridimensionamento è sempre relativo all'angolo opposto a quello che il dito interessato.

Per le bitmap piccole o oblunghi, un'ellisse interna potrebbe occupare la maggior parte della bitmap e lasciare aree molto piccola negli angoli per ridimensionare la mappa di bit. Si potrebbe preferire un approccio leggermente diverso, nel qual caso è possibile sostituire tale intera `if` blocco che imposta `isScaling` a `true` con questo codice:

```csharp
float halfHeight = rect.Height / 2;
float halfWidth = rect.Width / 2;

// Top half of bitmap
if (point.Y < rect.MidY)
{
    float yRelative = (point.Y - rect.Top) / halfHeight;

    // Upper-left corner
    if (point.X < rect.MidX - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Bottom);
    }
    // Upper-right corner
    else if (point.X > rect.MidX + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Bottom);
    }
}
// Bottom half of bitmap
else
{
    float yRelative = (point.Y - rect.MidY) / halfHeight;

    // Lower-left corner
    if (point.X < rect.Left + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Top);
    }
    // Lower-right corner
    else if (point.X > rect.Right - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Top);
    }
}
```

Questo codice in modo efficace divide l'area della bitmap in una forma di rombo interni e quattro triangoli negli angoli. In questo modo molte aree di dimensioni maggiori con gli angoli per recuperare e ridimensionare la mappa di bit.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Richiamo di eventi dagli effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
