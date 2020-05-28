---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f5738f376e2a814ef1af455178c2e68135184402
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140166"
---
# <a name="touch-manipulations"></a>Manipolazioni tramite tocco

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usare le trasformazioni matrice per implementare il trascinamento, il pizzicamento e la rotazione del tocco_

Negli ambienti multitocco come quelli dei dispositivi mobili, gli utenti spesso usano le dita per modificare gli oggetti sullo schermo. I movimenti comuni, ad esempio il trascinamento di un dito e un pizzico di due dita, possono spostare e ridimensionare oggetti o persino ruotarli. Questi movimenti vengono in genere implementati usando matrici di trasformazione e in questo articolo viene illustrato come eseguire questa operazione.

![](touch-images/touchmanipulationsexample.png "A bitmap subjected to translation, scaling, and rotation")

Tutti gli esempi riportati di seguito utilizzano l' Xamarin.Forms effetto di rilevamento del tocco presentato nell'articolo [**richiamo di eventi dagli effetti**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

## <a name="dragging-and-translation"></a>Trascinamento e conversione

Una delle applicazioni più importanti delle trasformazioni di matrici è l'elaborazione di touch. Un singolo [`SKMatrix`](xref:SkiaSharp.SKMatrix) valore può consolidare una serie di operazioni di tocco. 

Per il trascinamento di un singolo dito, il `SKMatrix` valore esegue la conversione. Questa operazione viene illustrata nella pagina **trascinamento bitmap** . Il file XAML crea un'istanza `SKCanvasView` di un oggetto in un oggetto Xamarin.Forms `Grid` . Un `TouchEffect` oggetto è stato aggiunto alla `Effects` raccolta di che `Grid` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.BitmapDraggingPage"
             Title="Bitmap Dragging">
    
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

In teoria, l' `TouchEffect` oggetto può essere aggiunto direttamente alla `Effects` raccolta di `SKCanvasView` , ma questo non funziona in tutte le piattaforme. Poiché `SKCanvasView` è la stessa dimensione di `Grid` in questa configurazione, la connessione al funziona anche in questo modo `Grid` .

Il file code-behind carica in una risorsa bitmap nel relativo costruttore e lo Visualizza nel `PaintSurface` gestore:

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    // Bitmap and matrix for display
    SKBitmap bitmap;
    SKMatrix matrix = SKMatrix.MakeIdentity();
    ···

    public BitmapDraggingPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, new SKPoint());
    }
}
```

Senza ulteriore codice, il `SKMatrix` valore è sempre la matrice di identificazione e non avrà alcun effetto sulla visualizzazione della bitmap. L'obiettivo del `OnTouchEffectAction` gestore impostato nel file XAML è quello di modificare il valore della matrice in modo da riflettere le manipolazioni dei tocchi.

Il `OnTouchEffectAction` gestore inizia con la conversione del Xamarin.Forms `Point` valore in un `SKPoint` valore SkiaSharp. Si tratta di una semplice operazione di ridimensionamento in base alle `Width` `Height` proprietà e di `SKCanvasView` (che sono unità indipendenti dal dispositivo) e alla `CanvasSize` proprietà, che si trova in unità di pixel:

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    ···
    // Touch information
    long touchId = -1;
    SKPoint previousPoint;
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
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point))
                {
                    touchId = args.Id;
                    previousPoint = point;
                }
                break;

            case TouchActionType.Moved:
                if (touchId == args.Id)
                {
                    // Adjust the matrix for the new position
                    matrix.TransX += point.X - previousPoint.X;
                    matrix.TransY += point.Y - previousPoint.Y;
                    previousPoint = point;
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = -1;
                break;
        }
    }
    ···
}
```

Quando un dito tocca per la prima volta lo schermo, viene generato un evento di tipo `TouchActionType.Pressed` . La prima attività consiste nel determinare se il dito sta toccando la bitmap. Questa attività viene spesso chiamata _hit testing_. In questo caso, l'hit testing può essere eseguito creando un `SKRect` valore corrispondente alla bitmap, applicando la trasformazione della matrice con `MapRect` e quindi determinando se il punto di tocco si trova all'interno del rettangolo trasformato.

In tal caso, il `touchId` campo viene impostato sull'ID tocco e la posizione del dito viene salvata.

Per l' `TouchActionType.Moved` evento, i fattori di conversione del `SKMatrix` valore vengono modificati in base alla posizione corrente del dito e alla nuova posizione del dito. La nuova posizione viene salvata per la volta successiva e l'oggetto `SKCanvasView` viene invalidato.

Quando si sperimenta questo programma, tenere presente che è possibile trascinare la bitmap solo quando il dito tocca un'area in cui viene visualizzata la bitmap. Sebbene tale restrizione non sia molto importante per questo programma, diventa fondamentale quando si modificano più bitmap.

## <a name="pinching-and-scaling"></a>Pizzicamento e ridimensionamento

Cosa si vuole fare quando due dita toccano la bitmap? Se le due dita si spostano in parallelo, è probabile che si voglia spostare la bitmap insieme alle dita. Se le due dita eseguono un'operazione di pizzico o estensione, è possibile che la bitmap venga ruotata (per essere descritta nella sezione successiva) o ridimensionata. Quando si ridimensiona una bitmap, è opportuno che le due dita rimangano nelle stesse posizioni rispetto alla bitmap e che la bitmap venga ridimensionata di conseguenza.

Gestire due dita in una sola volta sembra complicato, ma tenere presente che il `TouchAction` gestore riceve solo informazioni su un dito alla volta. Se due dita manipolano la bitmap, per ogni evento, una barretta ha modificato la posizione, ma l'altra non è stata modificata. Nel codice della pagina con **scalabilità bitmap** riportata di seguito, il dito che non ha modificato la posizione viene chiamato punto di _perno_ perché la trasformazione è relativa a tale punto.

Una differenza tra il programma e il programma precedente consiste nel fatto che è necessario salvare più ID tocco. A questo scopo, viene usato un dizionario, dove l'ID tocco è la chiave del dizionario e il valore del dizionario è la posizione corrente del dito:

```csharp
public partial class BitmapScalingPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
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
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point) && !touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Add(args.Id, point);
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger scale and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index of non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points involved in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Scaling factors are ratios of those
                        float scaleX = newVector.X / oldVector.X;
                        float scaleY = newVector.Y / oldVector.Y;

                        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
                            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
                        {
                            // If something bad hasn't happened, calculate a scale and translation matrix
                            SKMatrix scaleMatrix = 
                                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);

                            SKMatrix.PostConcat(ref matrix, scaleMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }
    ···
}
```

La gestione dell' `Pressed` azione è quasi identica a quella del programma precedente, ad eccezione del fatto che l'ID e il punto di tocco vengono aggiunti al dizionario. Le `Released` `Cancelled` azioni e rimuovono la voce del dizionario.

Tuttavia, la gestione dell' `Moved` azione è più complessa. Se è presente un solo dito, l'elaborazione è molto simile a quella del programma precedente. Per due o più dita, il programma deve anche ottenere informazioni dal dizionario che coinvolgono il dito che non si sta muovendo. Questa operazione viene eseguita copiando le chiavi del dizionario in una matrice e confrontando quindi la prima chiave con l'ID del dito spostato. Che consente al programma di ottenere il punto di perno corrispondente al dito che non si sposta.

Successivamente, il programma calcola due vettori della nuova posizione del dito rispetto al punto di perno e la posizione precedente del dito rispetto al punto di perno. I rapporti di questi vettori sono fattori di scala. Poiché la divisione per zero è una possibilità, è necessario verificare la presenza di valori infiniti o valori NaN (non un numero). Se tutte le sono buone, una trasformazione di ridimensionamento viene concatenata con il `SKMatrix` valore salvato come campo.

Quando si sperimenta questa pagina, si noterà che è possibile trascinare la bitmap con una o due dita oppure ridimensionarla con due dita. Il ridimensionamento è _anisotropico_, il che significa che la scalabilità può essere diversa nelle direzioni orizzontale e verticale. In questo modo le proporzioni vengono distorte, ma è possibile capovolgere la bitmap per creare un'immagine speculare. Si potrebbe anche scoprire che è possibile compattare la bitmap in una dimensione zero e scomparire. Nel codice di produzione è opportuno proteggersi da questa.

## <a name="two-finger-rotation"></a>Rotazione di due dita

La pagina della rotazione della **bitmap** consente di usare due dita per la rotazione o il ridimensionamento del formato di ridimensionamento. La bitmap mantiene sempre le proporzioni corrette. L'uso di due dita per la rotazione e il ridimensionamento anisotropico non funziona molto bene perché lo spostamento delle dita è molto simile per entrambe le attività.

La prima grande differenza in questo programma è la logica di hit testing. Nei programmi precedenti è stato utilizzato il `Contains` metodo di `SKRect` per determinare se il punto di tocco si trova all'interno del rettangolo trasformato che corrisponde alla bitmap. Tuttavia, quando l'utente modifica la bitmap, la bitmap potrebbe essere ruotata e `SKRect` non può rappresentare correttamente un rettangolo ruotato. In tal caso, si potrebbe temere che la logica di hit testing debba implementare una geometria analitica piuttosto complessa.

Tuttavia, è disponibile un collegamento: determinare se un punto si trova all'interno dei limiti di un rettangolo trasformato equivale a determinare se un punto trasformato inverso si trova all'interno dei limiti del rettangolo non trasformato. Si tratta di un calcolo molto più semplice e la logica può continuare a usare il `Contains` metodo pratico:

```csharp
public partial class BitmapRotationPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
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
                if (!touchDictionary.ContainsKey(args.Id))
                {
                    // Invert the matrix
                    if (matrix.TryInvert(out SKMatrix inverseMatrix))
                    {
                        // Transform the point using the inverted matrix
                        SKPoint transformedPoint = inverseMatrix.MapPoint(point);

                        // Check if it's in the untransformed bitmap rectangle
                        SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);

                        if (rect.Contains(transformedPoint))
                        {
                            touchDictionary.Add(args.Id, point);
                        }
                    }
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger rotate, scale, and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Find angles from pivot point to touch points
                        float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                        float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                        // Calculate rotation matrix
                        float angle = newAngle - oldAngle;
                        SKMatrix touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                        // Effectively rotate the old vector
                        float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                        oldVector.X = magnitudeRatio * newVector.X;
                        oldVector.Y = magnitudeRatio * newVector.Y;

                        // Isotropic scaling!
                        float scale = Magnitude(newVector) / Magnitude(oldVector);

                        if (!float.IsNaN(scale) && !float.IsInfinity(scale))
                        {
                            SKMatrix.PostConcat(ref touchMatrix,
                                SKMatrix.MakeScale(scale, scale, pivotPoint.X, pivotPoint.Y));

                            SKMatrix.PostConcat(ref matrix, touchMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }

    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
    ···
}
```

La logica per l' `Moved` evento inizia come il programma precedente. Due vettori denominati `oldVector` e `newVector` vengono calcolati in base al punto precedente e al punto corrente del dito e del punto pivot del dito che si sta spostando. Ma gli angoli di questi vettori vengono determinati e la differenza è l'angolo di rotazione.

Potrebbe essere necessario anche il ridimensionamento, quindi il vettore precedente viene ruotato in base all'angolo di rotazione. La grandezza relativa dei due vettori è ora il fattore di scala. Si noti che lo stesso `scale` valore viene utilizzato per la scalabilità orizzontale e verticale, in modo che il ridimensionamento sia di livello di filtro Il `matrix` campo viene regolato sia dalla matrice di rotazione che da una matrice di scala.

Se l'applicazione deve implementare l'elaborazione tocco per una singola bitmap o un altro oggetto, è possibile adattare il codice a questi tre esempi per la propria applicazione. Tuttavia, se è necessario implementare l'elaborazione tocco per più bitmap, è probabile che si desideri incapsulare queste operazioni di tocco in altre classi.

## <a name="encapsulating-the-touch-operations"></a>Incapsulamento delle operazioni di tocco

Nella pagina **manipolazione tocco** viene illustrata la manipolazione del tocco di una singola bitmap, ma vengono utilizzati diversi altri file che incapsulano la maggior parte della logica illustrata in precedenza. Il primo di questi file è l' [`TouchManipulationMode`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) enumerazione, che indica i diversi tipi di manipolazione del tocco implementati dal codice visualizzato:

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

`PanOnly`il trascinamento di un dito viene implementato con la traduzione. Tutte le opzioni successive includono anche la panoramica, ma coinvolgono due dita: `IsotropicScale` è un'operazione di pizzico che determina la scalabilità dell'oggetto in modo uniforme nelle direzioni orizzontale e verticale. `AnisotropicScale`consente un ridimensionamento diverso.

L' `ScaleRotate` opzione è per la scala e la rotazione di due dita. Il ridimensionamento è il filtro di stato. Come indicato in precedenza, l'implementazione della rotazione di due dita con la scalabilità anisotropico è problematica perché i movimenti del dito sono essenzialmente uguali.

L' `ScaleDualRotate` opzione consente di aggiungere una rotazione con un dito. Quando un singolo dito trascina l'oggetto, l'oggetto trascinato viene innanzitutto ruotato attorno al centro, in modo che il centro dell'oggetto venga allineato con il vettore di trascinamento.

Il file [**TouchManipulationPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml) include un oggetto `Picker` con i membri dell' `TouchManipulationMode` enumerazione:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos.Transforms"
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
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:TouchManipulationMode}">
                    <x:Static Member="local:TouchManipulationMode.None" />
                    <x:Static Member="local:TouchManipulationMode.PanOnly" />
                    <x:Static Member="local:TouchManipulationMode.IsotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.AnisotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.ScaleRotate" />
                    <x:Static Member="local:TouchManipulationMode.ScaleDualRotate" />
                </x:Array>
            </Picker.ItemsSource>
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

Verso il basso è un oggetto `SKCanvasView` e un oggetto `TouchEffect` collegato alla singola cella `Grid` che lo racchiude.

Il file code-behind [**TouchManipulationPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs) ha un `bitmap` campo, ma non è di tipo `SKBitmap` . Il tipo è `TouchManipulationBitmap` (una classe che verrà visualizzata a breve):

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

Il costruttore crea un'istanza di un `TouchManipulationBitmap` oggetto, passando al costruttore un oggetto `SKBitmap` ottenuto da una risorsa incorporata. Il costruttore termina impostando la `Mode` proprietà della `TouchManager` proprietà dell' `TouchManipulationBitmap` oggetto su un membro dell' `TouchManipulationMode` enumerazione.

Il `SelectedIndexChanged` gestore per `Picker` imposta anche questa `Mode` proprietà:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            bitmap.TouchManager.Mode = (TouchManipulationMode)picker.SelectedItem;
        }
    }
    ...
}
```

Il `TouchAction` gestore di di cui è stata `TouchEffect` creata un'istanza nel file XAML chiama due metodi in `TouchManipulationBitmap` denominati `HitTest` e `ProcessTouchEvent` :

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

Se il `HitTest` metodo restituisce il `true` &mdash; significato che un dito ha toccato lo schermo all'interno dell'area occupata dalla bitmap, &mdash; l'ID tocco viene aggiunto alla `TouchIds` raccolta. Questo ID rappresenta la sequenza di eventi di tocco per quel dito fino a quando il dito si solleva dallo schermo. Se più dita toccano la bitmap, la `touchIds` raccolta conterrà un ID tocco per ogni dito.

Il `TouchAction` gestore chiama anche la `ProcessTouchEvent` classe in `TouchManipulationBitmap` . Questo è il punto in cui si verificano alcuni (ma non tutti) dell'elaborazione dei tocchi reali.

La [`TouchManipulationBitmap`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs) classe è una classe wrapper per `SKBitmap` che contiene il codice per il rendering degli eventi di bitmap e di tocco del processo. Funziona insieme a codice più generalizzato in una `TouchManipulationManager` classe (che verrà visualizzata a breve).

Il `TouchManipulationBitmap` Costruttore Salva `SKBitmap` e crea un'istanza di due proprietà, la `TouchManager` proprietà di tipo `TouchManipulationManager` e la `Matrix` proprietà di tipo `SKMatrix` :

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

Questa `Matrix` proprietà è la trasformazione accumulata risultante da tutte le attività di tocco. Come si vedrà, ogni evento Touch viene risolto in una matrice, che viene quindi concatenato con il `SKMatrix` valore archiviato dalla `Matrix` Proprietà.

L' `TouchManipulationBitmap` oggetto viene disegnato nel relativo `Paint` metodo. L'argomento è un `SKCanvas` oggetto. `SKCanvas`A questo potrebbe essere già applicata una trasformazione, quindi il `Paint` metodo concatena la `Matrix` proprietà associata alla bitmap alla trasformazione esistente e ripristina l'area di disegno al termine:

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

Il `HitTest` metodo restituisce `true` se l'utente tocca lo schermo in un punto all'interno dei limiti della bitmap. Questa operazione usa la logica illustrata in precedenza nella pagina **rotazione bitmap** :

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

Il secondo metodo pubblico in `TouchManipulationBitmap` è `ProcessTouchEvent` . Quando viene chiamato questo metodo, è già stato stabilito che l'evento Touch appartiene a questa particolare bitmap. Il metodo mantiene un dizionario di [`TouchManipulationInfo`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) oggetti, che è semplicemente il punto precedente e il nuovo punto di ogni dito:

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

Ecco il dizionario e il `ProcessTouchEvent` Metodo:

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

Negli `Moved` eventi e `Released` il metodo chiama `Manipulate` . In questi casi, `touchDictionary` contiene uno o più `TouchManipulationInfo` oggetti. Se `touchDictionary` contiene un elemento, è probabile che i `PreviousPoint` `NewPoint` valori e siano diversi e rappresentino lo spostamento di un dito. Se più dita toccano la bitmap, il dizionario contiene più di un elemento, ma solo uno di questi elementi ha `PreviousPoint` valori e diversi `NewPoint` . Tutti gli altri hanno `PreviousPoint` valori uguali e `NewPoint` .

Questo è importante: il `Manipulate` metodo può presupporre che sta elaborando lo spostamento di un solo dito. Al momento della chiamata, nessuna delle altre dita viene spostata e, se è effettivamente in movimento (come probabilmente), questi movimenti verranno elaborati nelle chiamate future a `Manipulate` .

Il `Manipulate` metodo copia prima di tutto il dizionario in una matrice per praticità. Ignora qualsiasi elemento diverso dalle prime due voci. Se più di due dita tentano di modificare la bitmap, le altre vengono ignorate. `Manipulate`è il membro finale di `TouchManipulationBitmap` :

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

Se un dito sta manipolando la bitmap, `Manipulate` chiama il `OneFingerManipulate` metodo dell' `TouchManipulationManager` oggetto. Per due dita, chiama `TwoFingerManipulate` . Gli argomenti di questi metodi sono gli stessi: gli `prevPoint` `newPoint` argomenti e rappresentano il dito che sta per essere spostato. Tuttavia `pivotPoint` , l'argomento è diverso per le due chiamate:

Per la manipolazione di un dito, `pivotPoint` è il centro della bitmap. Per consentire la rotazione di un dito. Per la manipolazione a due dita, l'evento indica lo spostamento di un solo dito, in modo che `pivotPoint` sia il dito che non si sposta.

In entrambi i casi, `TouchManipulationManager` restituisce un `SKMatrix` valore, che il metodo concatena con la `Matrix` proprietà corrente `TouchManipulationPage` utilizzata da per eseguire il rendering della bitmap.

`TouchManipulationManager`è generalizzato e non usa altri file ad eccezione di `TouchManipulationMode` . Potrebbe essere possibile utilizzare questa classe senza modifiche nelle applicazioni. Definisce una singola proprietà di tipo `TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```

Tuttavia, è probabile che si desideri evitare l' `AnisotropicScale` opzione. Con questa opzione è molto semplice modificare la bitmap in modo che uno dei fattori di scala diventi zero. Che rende la bitmap scomparire dalla visione, mai restituire. Se è effettivamente necessaria la scalabilità anisotropico, è opportuno migliorare la logica per evitare risultati indesiderati.

`TouchManipulationManager`USA i vettori, ma poiché non esiste alcuna `SKVector` struttura in SkiaSharp, `SKPoint` viene invece usato. `SKPoint`supporta l'operatore di sottrazione e il risultato può essere considerato come un vettore. L'unica logica specifica del vettore che è necessario aggiungere è un `Magnitude` calcolo:

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

Ogni volta che la rotazione è stata selezionata, entrambi i metodi di manipolazione a una barretta e a due dita gestiscono prima la rotazione. Se viene rilevata una rotazione, il componente di rotazione viene rimosso in modo efficace. Ciò che rimane viene interpretato come panning e scalabilità.

Ecco il `OneFingerManipulate` metodo. Se la rotazione di un dito non è stata abilitata, la logica è semplice, &mdash; ma usa semplicemente il punto precedente e il nuovo punto per costruire un vettore denominato `delta` che corrisponde esattamente alla traduzione. Con la rotazione di una barretta abilitata, il metodo usa gli angoli dal punto di perno (il centro della bitmap) al punto precedente e un nuovo punto per costruire una matrice di rotazione:

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

Nel `TwoFingerManipulate` metodo, il punto di perno è la posizione del dito che non si sposta in questo particolare evento Touch. La rotazione è molto simile alla rotazione di un dito, quindi il vettore denominato `oldVector` (in base al punto precedente) viene regolato per la rotazione. Lo spostamento rimanente viene interpretato come scalabilità:

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

Si noterà che non è presente alcuna traduzione esplicita in questo metodo. Tuttavia, entrambi i `MakeRotation` `MakeScale` metodi e sono basati sul punto pivot e includono la conversione implicita. Se si usano due dita sulla bitmap e le si trascina nella stessa direzione, `TouchManipulation` riceverà una serie di eventi di tocco che si alternano tra le due dita. Poiché ogni dito si sposta rispetto all'altro, i risultati di ridimensionamento o rotazione sono negati dal movimento dell'altro dito e il risultato è Translation.

L'unica parte rimanente della pagina **manipolazione tocco** è il `PaintSurface` gestore nel `TouchManipulationPage` file code-behind. Viene chiamato il `Paint` metodo di `TouchManipulationBitmap` , che applica la matrice che rappresenta l'attività di tocco accumulata:

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

Il `PaintSurface` gestore termina visualizzando un oggetto che `MatrixDisplay` Mostra la matrice di tocco accumulata:

[![](touch-images/touchmanipulation-small.png "Triple screenshot of the Touch Manipulation page")](touch-images/touchmanipulation-large.png#lightbox "Triple screenshot of the Touch Manipulation page")

## <a name="manipulating-multiple-bitmaps"></a>Manipolazione di più bitmap

Uno dei vantaggi dell'isolamento del codice di elaborazione del tocco in classi come `TouchManipulationBitmap` e `TouchManipulationManager` è la possibilità di riutilizzare queste classi in un programma che consente all'utente di modificare più bitmap.

Nella pagina della **visualizzazione a dispersione bitmap** viene illustrato come eseguire questa operazione. Anziché definire un campo di tipo `TouchManipulationBitmap` , la [`BitmapScatterPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) classe definisce un oggetto `List` di oggetti bitmap:

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

Il costruttore carica tutte le bitmap disponibili come risorse incorporate e le aggiunge a `bitmapCollection` . Si noti che la `Matrix` proprietà viene inizializzata su ogni `TouchManipulationBitmap` oggetto, in modo che gli angoli superiore sinistro di ogni bitmap siano offset di 100 pixel.

La `BitmapScatterView` pagina deve anche gestire gli eventi di tocco per più bitmap. Anziché definire una `List` di ID tocco degli oggetti attualmente modificati `TouchManipulationBitmap` , questo programma richiede un dizionario:

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

Si noti il modo in cui la `Pressed` logica scorre `bitmapCollection` in senso inverso. Spesso le bitmap si sovrappongono. Le bitmap in un secondo momento nella raccolta si trovano sopra le bitmap precedentemente presenti nella raccolta. Se sono presenti più bitmap al di sotto del dito che preme sullo schermo, il primo deve essere quello modificato da tale dito.

Si noti inoltre che la `Pressed` logica sposta la bitmap alla fine della raccolta in modo che venga spostata visivamente nella parte superiore della pila di altre bitmap.

Negli `Moved` eventi e `Released` il `TouchAction` gestore chiama il `ProcessingTouchEvent` metodo in `TouchManipulationBitmap` proprio come il programma precedente.

Infine, il `PaintSurface` gestore chiama il `Paint` metodo di ogni `TouchManipulationBitmap` oggetto:

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

Il codice scorre la raccolta e visualizza il mucchio di bitmap dall'inizio della raccolta fino alla fine:

[![](touch-images/bitmapscatterview-small.png "Triple screenshot of the Bitmap Scatter View page")](touch-images/bitmapscatterview-large.png#lightbox "Triple screenshot of the Bitmap Scatter View page")

## <a name="single-finger-scaling"></a>Ridimensionamento a dito singolo

Un'operazione di ridimensionamento richiede in genere un gesto di pizzico usando due dita. È tuttavia possibile implementare la scalabilità con un solo dito facendo muovere il dito sugli angoli di una bitmap.

Questa operazione viene illustrata nella pagina della scala dell'angolo a un **singolo dito** . Poiché in questo esempio viene utilizzato un tipo di ridimensionamento leggermente diverso rispetto a quello implementato nella `TouchManipulationManager` classe, non viene utilizzata la classe o la `TouchManipulationBitmap` classe. Al contrario, tutta la logica di tocco si trova nel file code-behind. Si tratta di una logica piuttosto più semplice del solito perché tiene traccia solo di un dito alla volta e ignora semplicemente le dita secondarie che potrebbero toccare lo schermo.

La pagina [**SingleFingerCornerScale. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml) crea un'istanza della `SKCanvasView` classe e crea un `TouchEffect` oggetto per tenere traccia degli eventi di tocco:

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

Il file [**SingleFingerCornerScalePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs) carica una risorsa bitmap dalla directory **media** e la Visualizza usando un `SKMatrix` oggetto definito come campo:

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

Questo `SKMatrix` oggetto viene modificato dalla logica di tocco riportata di seguito.

Il resto del file code-behind è il `TouchEffect` gestore eventi. Inizia convertendo la posizione corrente del dito in un `SKPoint` valore. Per il `Pressed` tipo di azione, il gestore verifica che nessun altro dito tocchi lo schermo e che il dito si trovi all'interno dei limiti della bitmap.

La parte cruciale del codice è un' `if` istruzione che implica due chiamate al `Math.Pow` metodo. Questo Math controlla se la posizione del dito si trova al di fuori di un'ellisse che riempie la bitmap. In caso affermativo, si tratta di un'operazione di ridimensionamento. Il dito si trova vicino a uno degli angoli della bitmap e viene determinato un punto di perno che rappresenta l'angolo opposto. Se il dito si trova all'interno di questo ellisse, si tratta di un'operazione di panning normale:

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

Il `Moved` tipo di azione calcola una matrice corrispondente all'attività di tocco dal momento in cui il dito preme lo schermo fino a questo momento. Concatena la matrice con la matrice attiva al momento in cui il dito ha premuto prima la bitmap. L'operazione di ridimensionamento è sempre relativa all'angolo opposto a quello che ha toccato il dito.

Per le bitmap piccole o oblunghe, un'ellisse interna potrebbe occupare la maggior parte della bitmap e lasciare le piccole aree negli angoli per ridimensionare la bitmap. È possibile che si preferisca un approccio leggermente diverso, nel qual caso è possibile sostituire l'intero `if` blocco che imposta `isScaling` su `true` con il codice seguente:

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

Questo codice divide efficacemente l'area della bitmap in una forma a rombo interno e quattro triangoli negli angoli. In questo modo è possibile spostare e ridimensionare la bitmap in aree molto più grandi negli angoli.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Richiamo di eventi da effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
