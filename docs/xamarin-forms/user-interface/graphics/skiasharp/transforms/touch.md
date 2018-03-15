---
title: Manipolazioni tocco
description: Trasforma una matrice di utilizzo per implementare il trascinamento di tocco, compressione e la rotazione
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: charlespetzold
ms.author: chape
ms.date: 04/12/2017
ms.openlocfilehash: 90be80d42c20ca7509037b5f59b34cc1bddde6de
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2018
---
# <a name="touch-manipulations"></a>Manipolazioni tocco

_Trasforma una matrice di utilizzo per implementare il trascinamento di tocco, compressione e la rotazione_

In ambienti multi-touch, ad esempio quelli dei dispositivi mobili, gli utenti utilizzano spesso le dita per manipolare gli oggetti sullo schermo. I movimenti comuni, ad esempio il trascinamento di un dito uno e due dita alternativa possano spostare e ridimensionare gli oggetti o anche ruotando. Questi movimenti in genere vengono implementati utilizzando matrici di trasformazione e in questo articolo viene illustrato come eseguire questa operazione.

![](touch-images/touchmanipulationsexample.png "Una bitmap sottoposta a conversione, il ridimensionamento e rotazione")

## <a name="manipulating-one-bitmap"></a>La modifica di una Bitmap

Il **modifica tocco** illustra come manipolazioni tocco su una singola bitmap.
In questo esempio si avvalgono dell'effetto tocco rilevamento presentata nell'articolo [richiamare eventi dagli effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

Numerosi altri file forniscono il supporto per il **modifica tocco** pagina. Il primo è il [ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) enumerazione che indica i diversi tipi di modifica tocco implementata dal codice verranno momento vengano visualizzati:

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

`PanOnly` è il trascinamento di un dito che viene implementato con la conversione. Tutte le opzioni successive anche includere Panoramica ma comportano due dita: `IsotropicScale` è un'operazione di zoom indietro che restituisce l'oggetto scala in modo uniforme nelle direzioni orizzontale e verticale. `AnisotropicScale` Consente di scalare non uguali.

Il `ScaleRotate` opzione è per un dito due scalabilità e la rotazione. La scalabilità è isotropico. Implementazione di rotazione di due dita con scalabilità anisotropico è problematica, perché i movimenti dito sono essenzialmente gli stessi.

Il `ScaleDualRotate` opzione consente di aggiungere un dito una rotazione. Quando un singolo dito trascina l'oggetto, l'oggetto trascinato viene ruotata innanzitutto attorno al centro in modo che il centro dell'oggetto sia allineato con il vettore di trascinamento.

Il [ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) file include un `Picker` con i membri del `TouchManipulationMode` enumerazione:

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

Nella parte inferiore è un `SKCanvasView` e `TouchEffect` collegata a cella singola `Grid` che racchiude.

Il [ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) file code-behind è un `bitmap` campo ma non è di tipo `SKBitmap`. Il tipo è `TouchManipulationBitmap` (una classe che verrà visualizzato a breve):

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            SKBitmap bitmap = SKBitmap.Decode(skStream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

Il costruttore crea un `TouchManipulationBitmap` oggetto, passare al costruttore un `SKBitmap` ottenuto da una risorsa incorporata. Il costruttore si conclude con l'impostazione di `Mode` proprietà del `TouchManager` proprietà del `TouchManipulationBitmap` oggetto a un membro del `TouchManipulationMode` enumerazione.

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

Il `TouchAction` gestore del `TouchEffect` creata nel file XAML chiama due metodi in `TouchManipulationBitmap` denominato `HitTest` e `ProcessTouchEvent`:

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

Se il `HitTest` restituisce `true` &mdash; vale a dire che la schermata all'interno dell'area occupata dal bitmap è interessate da un dito &mdash; viene quindi aggiunto l'ID tocco per il `TouchIds` insieme. L'ID rappresenta la sequenza di eventi di tocco per tale dito fino a quando non solleva il dito dallo schermo. Se più dita toccano bitmap, il `touchIds` raccolta contiene un ID tocco per ogni dito.

Il `TouchAction` gestore chiama anche il `ProcessTouchEvent` classe `TouchManipulationBitmap`. Questa opzione è quando alcuni, ma non tutte, del tocco reale si verifica l'elaborazione.

Il [ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) classe è una classe wrapper per `SKBitmap` che contiene il codice per eseguire il rendering di bitmap ed elaborare gli eventi tocco. Funziona in combinazione con più generalizzato di codice in un `TouchManipulationManager` classe (che verrà visualizzato a breve).

Il `TouchManipulationBitmap` costruttore salva il `SKBitmap` e crea un'istanza di due proprietà, il `TouchManager` proprietà di tipo `TouchManipulationManager` e `Matrix` proprietà di tipo `SKMatrix`:

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

Questo `Matrix` proprietà è la trasformazione accumulata risultanti da tutte le attività di tocco. Come si vedrà, ogni evento tocco viene risolta in una matrice, che quindi viene concatenata con il `SKMatrix` valore archiviato di `Matrix` proprietà.

Il `TouchManipulationBitmap` oggetto disegna se stesso nel relativo `Paint` metodo. L'argomento è un `SKCanvas` oggetto. Questo `SKCanvas` dispone già di una trasformazione applicata, pertanto la `Paint` metodo concatena il `Matrix` proprietà associata con la bitmap per la trasformazione esistente e di ripristinare l'area di disegno quando ha terminato:

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

Il `HitTest` restituisce `true` se l'utente tocca lo schermo in un punto all'interno dei limiti della bitmap. Mentre l'utente modifica la mappa di bit, la bitmap può essere ruotata, o anche (tramite una combinazione di filtraggio anisotropo della scalabilità e la rotazione) essere nella forma del parallelogramma. Si potrebbe ritenere che il `HitTest` (metodo) deve implementare in tal caso geometry analitico piuttosto complessa.

Tuttavia, è disponibile un collegamento:

Per determinare se un punto si trova entro i limiti di un rettangolo trasformato è identico per determinare se un punto trasformato inverso si trova entro i limiti del rettangolo non trasformato modificato. Un altro calcolo più semplice e può usare l'intuitiva `Contains` definito dal metodo `SKRect`:

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

Il secondo metodo pubblico in `TouchManipulationBitmap` è `ProcessTouchEvent`. Quando viene chiamato questo metodo, si è già stato stabilito che l'evento tocco appartiene a questa bitmap particolare. Il metodo gestisce un dizionario di [ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) oggetti, è semplicemente il precedente punto e il nuovo punto di ogni dito:

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

Ecco il dizionario e `ProcessTouchEvent` metodo stesso:

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

Nel `Moved` e `Released` eventi, le chiamate al metodo `Manipulate`. In questi casi, il `touchDictionary` contiene uno o più `TouchManipulationInfo` oggetti. Se il `touchDictionary` contiene un elemento, è probabile che il `PreviousPoint` e `NewPoint` i valori sono uguali e rappresentano lo spostamento di un dito. Se più dita stanno toccando la mappa di bit, il dizionario contiene più di un elemento, ma solo uno di questi elementi è diverso `PreviousPoint` e `NewPoint` valori. Tutti gli altri sono uguali `PreviousPoint` e `NewPoint` valori.

Questo aspetto è importante: il `Manipulate` metodo può presupporre che sia in corso lo spostamento di un solo dito. Al momento della chiamata nessuna delle altre dita è mobile, e se sono effettivamente sposta (come probabilmente), verranno elaborati i movimenti nelle future chiamate al `Manipulate`.

Il `Manipulate` metodo copia prima di tutto il dizionario in una matrice per praticità. Ignora un valore diverso dalle prime due voci. Se più di due dita siano tentando di modificare la mappa di bit, gli altri vengono ignorati. `Manipulate` è il membro finale della `TouchManipulationBitmap`:

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

Se la mappa di bit, è la modifica di un solo dito sul `Manipulate` chiamate di `OneFingerManipulate` metodo il `TouchManipulationManager` oggetto. Per due dita, chiama `TwoFingerManipulate`. Gli argomenti di questi metodi sono gli stessi: il `prevPoint` e `newPoint` gli argomenti rappresentano il dito spostati. Ma la `pivotPoint` argomento è diverso per le due chiamate:

Per la modifica di un dito il `pivotPoint` è il centro della bitmap. Si tratta per consentire la rotazione di un dito. Per la modifica di due dita, l'evento indica lo spostamento di un solo dito, in modo che il `pivotPoint` è il dito che non è in movimento.

In entrambi i casi, `TouchManipulationManager` restituisce un `SKMatrix` valore, il metodo concatena con corrente `Matrix` proprietà che `TouchManipulationPage` viene utilizzato per eseguire il rendering della bitmap.

`TouchManipulationManager` è stato generalizzato e viene utilizzato alcun altro file, ad eccezione `TouchManipulationMode`. È possibile utilizzare questa classe senza modifiche nelle proprie applicazioni. Definisce una singola proprietà di tipo `TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```


Tuttavia, è opportuno evitare di `AnisotropicScale` opzione. È molto semplice con questa opzione per modificare la mappa di bit in modo che uno dei fattori di scala diventa zero. Che effettua la bitmap scompaiano dalla vista mai da restituire. Se è effettivamente necessario filtraggio anisotropo della scala, sarà possibile migliorare la logica per evitare risultati indesiderati.

`TouchManipulationManager` si avvalgono di vettori, ma poiché non esiste alcun `SKVector` struttura in SkiaSharp, `SKPoint` viene invece utilizzato. `SKPoint` supporta che l'operatore di sottrazione e il risultato può essere considerato come un vettore. La logica solo vettore specifici necessari da aggiungere è un `Magnitude` calcolo:

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

Ogni volta che è stata selezionata la rotazione, entrambi i metodi di modifica di un dito uno e due dita gestiscono in primo luogo la rotazione. Se viene rilevata alcuna rotazione, viene rimosso in modo efficace il componente di rotazione. Il valore restante viene interpretato come panoramica e scalabilità.

Ecco il `OneFingerManipulate` metodo. Se un dito una rotazione non è stata abilitata, quindi la logica è semplice &mdash; utilizza semplicemente il punto precedente e nuovo per creare un vettore denominato `delta` che corrisponde esattamente alla conversione. Con un dito rotazione abilitata, il metodo Usa gli angoli dal punto pivot (ovvero il centro della bitmap) per il punto precedente e nuovo per costruire una matrice di rotazione:

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

Nel `TwoFingerManipulate` (metodo), punto pivot è la posizione di dito che non è in movimento in questo caso particolare tocco. La rotazione è molto simile a un dito una rotazione e il vettore denominato `oldVector` (in base al punto precedente) viene regolato per la rotazione. Lo spostamento rimanente viene interpretato come scalabilità:

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

Si noterà che non vi è alcuna conversione esplicita in questo metodo. Tuttavia, sia il `MakeRotation` e `MakeScale` metodi si basano sulle punto pivot, e che include la conversione implicita. Se si sta usando due dita in bitmap e trascinarli nella stessa direzione, `TouchManipulation` verrà visualizzata una serie di eventi tocco alternando tra due dita. Consente di spostare ogni dito relativo altri, ridimensionamento o la rotazione risultati, ma negata per lo spostamento del dito e il risultato è una conversione.

L'unica parte rimanente del **modifica tocco** pagina è il `PaintSurface` gestore il `TouchManipulationPage` file code-behind. Chiama il `Paint` metodo il `TouchManipulationBitmap`, che si applica la matrice che rappresenta l'attività touch accumulato:

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

Il `PaintSurface` gestore si conclude con la visualizzazione di un `MatrixDisplay` oggetto mostrando la matrice di tocco accumulato:

[![](touch-images/touchmanipulation-small.png "Schermata triplo della pagina Modifica tocco")](touch-images/touchmanipulation-large.png#lightbox "tripla schermata della pagina Modifica tocco")

## <a name="manipulating-multiple-bitmaps"></a>La modifica più bitmap

Uno dei vantaggi dell'isolamento del codice di tocco per l'elaborazione nelle classi, ad esempio `TouchManipulationBitmap` e `TouchManipulationManager` è la possibilità di riutilizzare in un programma che consente all'utente di modificare più immagini bitmap.

Il **visualizzazione a dispersione Bitmap** pagina viene illustrato come questa operazione viene eseguita. Invece di definire un campo di tipo `TouchManipulationBitmap`, [ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) classe definisce un `List` di oggetti bitmap:

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
                using (SKManagedStream skStream = new SKManagedStream(stream))
                {
                    SKBitmap bitmap = SKBitmap.Decode(skStream);
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

Carica in tutte le bitmap disponibili come risorse incorporate, il costruttore e li aggiunge al `bitmapCollection`. Si noti che il `Matrix` proprietà viene inizializzata su ogni `TouchManipulationBitmap` dell'oggetto, pertanto gli angoli superiore sinistro di ogni bitmap hanno offset uguale a 100 pixel.

Il `BitmapScatterView` pagina deve anche gestire gli eventi di tocco per più immagini bitmap. Invece di definire un `List` di touch ID dei modificato attualmente `TouchManipulationBitmap` oggetti, il programma richiede un dizionario:

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

Si noti come `Pressed` logica consente di scorrere il `bitmapCollection` in ordine inverso. Le bitmap spesso si sovrappongono. Le bitmap in un secondo momento nella raccolta visivamente si trovano all'inizio le bitmap in precedenza nella raccolta. Se sono presenti più bitmap con il dito che preme sullo schermo, quello in primo piano deve essere quello che viene modificato tramite il dito.

Si noti inoltre che il `Pressed` sposti tale bitmap alla fine della raccolta in modo da spostarla visivamente in cima l'accumulo di altri bitmap.

Nel `Moved` e `Released` eventi, il `TouchAction` chiamate del gestore di `ProcessingTouchEvent` metodo in `TouchManipulationBitmap` esattamente come il programma precedente.

Infine, il `PaintSurface` chiamate del gestore di `Paint` metodo ciascun `TouchManipulationBitmap` oggetto:

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

Il codice scorre la raccolta e visualizza l'accumulo di bitmap dall'inizio della raccolta alla fine:

[![](touch-images/bitmapscatterview-small.png "Schermata di triplo della pagina di visualizzazione a dispersione Bitmap")](touch-images/bitmapscatterview-large.png#lightbox "tripla schermata della pagina di visualizzazione a dispersione di Bitmap")


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
- [Richiamo di eventi da effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
