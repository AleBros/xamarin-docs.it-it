---
title: Animazione di bitmap di SkiaSharp
description: Informazioni su come eseguire l'animazione di bitmap in modo sequenziale visualizzando una serie di bitmap, e il rendering di immagini GIF animate.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
ms.openlocfilehash: 33e17a01d8a13fcdaee27e5857c554a4a232c534
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304044"
---
# <a name="animating-skiasharp-bitmaps"></a>Animazione di bitmap di SkiaSharp

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Le applicazioni che animano la grafica SkiaSharp in genere chiamano `InvalidateSurface` sul `SKCanvasView` a una frequenza fissa, spesso ogni 16 millisecondi. L'invalidamento della superficie attiva una chiamata al gestore `PaintSurface` per ricreare la visualizzazione. Come gli oggetti visivi vengono ridisegnati 60 volte al secondo, sembrano essere animate senza problemi.

Tuttavia, se gli elementi grafici non troppo complessi da sottoporre a rendering in 16 millisecondi, l'animazione può diventare instabilità. Il programmatore potrebbe scegliere di ridurre la frequenza di aggiornamento a 30 volte o 15 volte al secondo, ma in alcuni casi anche che non è sufficiente. In alcuni casi grafica è talmente complessa che sono semplicemente non è possibile eseguire il rendering in tempo reale.

Un'unica soluzione consiste nel preparare in anticipo per l'animazione eseguendo il rendering dei singoli frame di animazione in una serie di bitmap. Per visualizzare l'animazione, è necessario solo visualizzare tali bitmap in modo sequenziale 60 volte al secondo.

Naturalmente, che è potenzialmente di molte delle bitmap, ma che è 3D come big budget film animati vengono apportate. La grafica 3D è troppo complessa da sottoporre a rendering in tempo reale. Per eseguire il rendering di ogni fotogramma, è necessario molto tempo di elaborazione. Ciò che viene visualizzato quando si guarda il film è essenzialmente una serie di bitmap.

È possibile eseguire un'operazione simile in SkiaSharp. Questo articolo illustra due tipi di animazione di bitmap. Il primo esempio è un'animazione del Set di Mandelbrot:

![Esempio di animazione](animating-images/AnimatingSample.png "Esempio di animazione")

Nel secondo esempio viene illustrato come utilizzare SkiaSharp per il rendering di un GIF animato.

## <a name="bitmap-animation"></a>Animazione di bitmap

Il Set di Mandelbrot è visivamente affascinanti ma computionally lunga durata. Per una descrizione del set di Mandelbrot e della matematica usati in questo articolo, vedere il [capitolo 20 della pagina relativa alla _creazione di app per dispositivi mobili con Novell. Forms_ ](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf) a partire dalla pagina 666. La descrizione seguente si presuppone che informazioni di background.)

Nell'esempio di [**animazione di Mandelbrot**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima) viene utilizzata l'animazione bitmap per simulare uno zoom continuo di un punto fisso nel set di Mandelbrot. Lo zoom avanti è seguito da uno zoom indietro, e quindi il ciclo viene ripetuto all'infinito o fino al termine del programma.

Il programma Prepara per questa animazione mediante la creazione di un massimo di 50 bitmap archiviati nell'archiviazione locale dell'applicazione. Ogni bitmap comprende metà della larghezza e altezza del piano complesso come mappa di bit precedente. Nel programma, le bitmap sono definite _livelli di zoom_integrali. Le bitmap vengono quindi visualizzate in sequenza. Il ridimensionamento di ogni bitmap viene animato per fornire una progressione smooth da una singola bitmap a altro.

Come il programma finale descritto nel capitolo 20 della _creazione di app per dispositivi mobili con Novell. Forms_, il calcolo del set di Mandelbrot nell' **animazione di Mandelbrot** è un metodo asincrono con otto parametri. I parametri includono un punto centrale complessi e una larghezza e altezza del piano complesso che circonda il punto centrale. I tre parametri successivi sono la larghezza in pixel e l'altezza della bitmap da creare e un numero massimo di iterazioni per il calcolo ricorsive. Il parametro `progress` viene utilizzato per visualizzare lo stato di avanzamento di questo calcolo. Il parametro `cancelToken` non viene utilizzato in questo programma:

```csharp
static class Mandelbrot
{
    public static Task<BitmapInfo> CalculateAsync(Complex center,
                                                  double width, double height,
                                                  int pixelWidth, int pixelHeight,
                                                  int iterations,
                                                  IProgress<double> progress,
                                                  CancellationToken cancelToken)
    {
        return Task.Run(() =>
        {
            int[] iterationCounts = new int[pixelWidth * pixelHeight];
            int index = 0;

            for (int row = 0; row < pixelHeight; row++)
            {
                progress.Report((double)row / pixelHeight);
                cancelToken.ThrowIfCancellationRequested();

                double y = center.Imaginary + height / 2 - row * height / pixelHeight;

                for (int col = 0; col < pixelWidth; col++)
                {
                    double x = center.Real - width / 2 + col * width / pixelWidth;
                    Complex c = new Complex(x, y);

                    if ((c - new Complex(-1, 0)).Magnitude < 1.0 / 4)
                    {
                        iterationCounts[index++] = -1;
                    }
                    // http://www.reenigne.org/blog/algorithm-for-mandelbrot-cardioid/
                    else if (c.Magnitude * c.Magnitude * (8 * c.Magnitude * c.Magnitude - 3) < 3.0 / 32 - c.Real)
                    {
                        iterationCounts[index++] = -1;
                    }
                    else
                    {
                        Complex z = 0;
                        int iteration = 0;

                        do
                        {
                            z = z * z + c;
                            iteration++;
                        }
                        while (iteration < iterations && z.Magnitude < 2);

                        if (iteration == iterations)
                        {
                            iterationCounts[index++] = -1;
                        }
                        else
                        {
                            iterationCounts[index++] = iteration;
                        }
                    }
                }
            }
            return new BitmapInfo(pixelWidth, pixelHeight, iterationCounts);
        }, cancelToken);
    }
}
```

Il metodo restituisce un oggetto di tipo `BitmapInfo` che fornisce informazioni per la creazione di una bitmap:

```csharp
class BitmapInfo
{
    public BitmapInfo(int pixelWidth, int pixelHeight, int[] iterationCounts)
    {
        PixelWidth = pixelWidth;
        PixelHeight = pixelHeight;
        IterationCounts = iterationCounts;
    }

    public int PixelWidth { private set; get; }

    public int PixelHeight { private set; get; }

    public int[] IterationCounts { private set; get; }
}
```

Il file XAML di **animazione di Mandelbrot** include due viste `Label`, una `ProgressBar`e una `Button`, nonché la `SKCanvasView`:

```csharp
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="MandelAnima.MainPage"
             Title="Mandelbrot Animation">

    <StackLayout>
        <Label x:Name="statusLabel"
               HorizontalTextAlignment="Center" />
        <ProgressBar x:Name="progressBar" />

        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     Padding="5">
            <Label x:Name="storageLabel"
                   VerticalOptions="Center" />

            <Button x:Name="deleteButton"
                    Text="Delete All"
                    HorizontalOptions="EndAndExpand"
                    Clicked="OnDeleteButtonClicked" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Il file code-behind inizia definendo tre costanti fondamentale e una matrice di bitmap:

```csharp
public partial class MainPage : ContentPage
{
    const int COUNT = 10;           // The number of bitmaps in the animation.
                                    // This can go up to 50!

    const int BITMAP_SIZE = 1000;   // Program uses square bitmaps exclusively

    // Uncomment just one of these, or define your own
    static readonly Complex center = new Complex(-1.17651152924355, 0.298520986549558);
    //   static readonly Complex center = new Complex(-0.774693089457127, 0.124226621261617);
    //   static readonly Complex center = new Complex(-0.556624880053304, 0.634696788141351);

    SKBitmap[] bitmaps = new SKBitmap[COUNT];   // array of bitmaps
    ···
}
```

A un certo punto, probabilmente si vuole modificare il valore di `COUNT` in 50 per visualizzare l'intera gamma dell'animazione. I valori superiori a 50 non sono utili. Intorno a un livello di zoom di 48 o questa operazione, la risoluzione di numeri a virgola mobile a precisione doppia diventa insufficiente per il calcolo del Set di Mandelbrot. Questo problema viene illustrato nella pagina 684 della _creazione di app per dispositivi mobili con Novell. Forms_.

Il valore `center` è molto importante. Questo è lo stato attivo dello zoom animazione. I tre valori nel file sono quelli usati nelle tre schermate finali nel capitolo 20 della creazione di _app per dispositivi mobili con Novell. Forms_ nella pagina 684, ma è possibile sperimentare il programma in tale capitolo per iniziare a usare uno dei propri valori.

L'esempio di **animazione di Mandelbrot** archivia tali bitmap `COUNT` nell'archiviazione dell'applicazione locale. Le cinquanta bitmap richiedono oltre 20 megabyte di spazio di archiviazione nel dispositivo, pertanto si potrebbe voler sapere quanto spazio di archiviazione occupata dai tali bitmap e a un certo punto è possibile eliminarli tutti. Questo è lo scopo di questi due metodi nella parte inferiore della classe `MainPage`:

```csharp
public partial class MainPage : ContentPage
{
    ···
    void TallyBitmapSizes()
    {
        long fileSize = 0;

        foreach (string filename in Directory.EnumerateFiles(FolderPath()))
        {
            fileSize += new FileInfo(filename).Length;
        }

        storageLabel.Text = $"Total storage: {fileSize:N0} bytes";
    }

    void OnDeleteButtonClicked(object sender, EventArgs args)
    {
        foreach (string filepath in Directory.EnumerateFiles(FolderPath()))
        {
            File.Delete(filepath);
        }

        TallyBitmapSizes();
    }
}
```

È possibile eliminare le mappe di bit nell'archivio locale mentre il programma viene aggiunta un'animazione tali bitmap stessa perché il programma li conserva in memoria. Ma alla successiva che esecuzione del programma, sarà necessario ricreare le bitmap.

Le bitmap archiviate nell'archiviazione dell'applicazione locale incorporano il valore `center` nei rispettivi nomi file, pertanto se si modifica l'impostazione di `center`, le bitmap esistenti non verranno sostituite nello spazio di archiviazione e continueranno a occupare spazio.

Di seguito sono riportati i metodi usati `MainPage` per costruire i nomi di file, nonché un metodo `MakePixel` per la definizione di un valore in pixel basato sui componenti di colore:

```csharp
public partial class MainPage : ContentPage
{
    ···
    // File path for storing each bitmap in local storage
    string FolderPath() =>
        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);

    string FilePath(int zoomLevel) =>
        Path.Combine(FolderPath(),
                     String.Format("R{0}I{1}Z{2:D2}.png", center.Real, center.Imaginary, zoomLevel));

    // Form bitmap pixel for Rgba8888 format
    uint MakePixel(byte alpha, byte red, byte green, byte blue) =>
        (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

Il parametro `zoomLevel` per `FilePath` intervallo compreso tra 0 e la costante `COUNT` meno 1.

Il costruttore `MainPage` chiama il metodo `LoadAndStartAnimation`:

```csharp
public partial class MainPage : ContentPage
{
    ···
    public MainPage()
    {
        InitializeComponent();

        LoadAndStartAnimation();
    }
    ···
}
```

Il metodo `LoadAndStartAnimation` è responsabile dell'accesso all'archiviazione locale dell'applicazione per caricare le bitmap che potrebbero essere state create quando il programma è stato eseguito in precedenza. Esegue il ciclo `zoomLevel` valori da 0 a `COUNT`. Se il file esiste, lo carica nella matrice `bitmaps`. In caso contrario, è necessario creare una bitmap per i valori `center` e `zoomLevel` specifici chiamando `Mandelbrot.CalculateAsync`. Questo metodo ottiene il numero di iterazione per ciascun pixel, che questo metodo converte in colori:

```csharp
public partial class MainPage : ContentPage
{
    ···
    async void LoadAndStartAnimation()
    {
        // Show total bitmap storage
        TallyBitmapSizes();

        // Create progressReporter for async operation
        Progress<double> progressReporter =
            new Progress<double>((double progress) => progressBar.Progress = progress);

        // Create (unused) CancellationTokenSource for async operation
        CancellationTokenSource cancelTokenSource = new CancellationTokenSource();

        // Loop through all the zoom levels
        for (int zoomLevel = 0; zoomLevel < COUNT; zoomLevel++)
        {
            // If the file exists, load it
            if (File.Exists(FilePath(zoomLevel)))
            {
                statusLabel.Text = $"Loading bitmap for zoom level {zoomLevel}";

                using (Stream stream = File.OpenRead(FilePath(zoomLevel)))
                {
                    bitmaps[zoomLevel] = SKBitmap.Decode(stream);
                }
            }
            // Otherwise, create a new bitmap
            else
            {
                statusLabel.Text = $"Creating bitmap for zoom level {zoomLevel}";

                CancellationToken cancelToken = cancelTokenSource.Token;

                // Do the (generally lengthy) Mandelbrot calculation
                BitmapInfo bitmapInfo =
                    await Mandelbrot.CalculateAsync(center,
                                                    4 / Math.Pow(2, zoomLevel),
                                                    4 / Math.Pow(2, zoomLevel),
                                                    BITMAP_SIZE, BITMAP_SIZE,
                                                    (int)Math.Pow(2, 10), progressReporter, cancelToken);

                // Create bitmap & get pointer to the pixel bits
                SKBitmap bitmap = new SKBitmap(BITMAP_SIZE, BITMAP_SIZE, SKColorType.Rgba8888, SKAlphaType.Opaque);
                IntPtr basePtr = bitmap.GetPixels();

                // Set pixel bits to color based on iteration count
                for (int row = 0; row < bitmap.Width; row++)
                    for (int col = 0; col < bitmap.Height; col++)
                    {
                        int iterationCount = bitmapInfo.IterationCounts[row * bitmap.Width + col];
                        uint pixel = 0xFF000000;            // black

                        if (iterationCount != -1)
                        {
                            double proportion = (iterationCount / 32.0) % 1;
                            byte red = 0, green = 0, blue = 0;

                            if (proportion < 0.5)
                            {
                                red = (byte)(255 * (1 - 2 * proportion));
                                blue = (byte)(255 * 2 * proportion);
                            }
                            else
                            {
                                proportion = 2 * (proportion - 0.5);
                                green = (byte)(255 * proportion);
                                blue = (byte)(255 * (1 - proportion));
                            }

                            pixel = MakePixel(0xFF, red, green, blue);
                        }

                        // Calculate pointer to pixel
                        IntPtr pixelPtr = basePtr + 4 * (row * bitmap.Width + col);

                        unsafe     // requires compiling with unsafe flag
                        {
                            *(uint*)pixelPtr.ToPointer() = pixel;
                        }
                    }

                // Save as PNG file
                SKData data = SKImage.FromBitmap(bitmap).Encode();

                try
                {
                    File.WriteAllBytes(FilePath(zoomLevel), data.ToArray());
                }
                catch
                {
                    // Probably out of space, but just ignore
                }

                // Store in array
                bitmaps[zoomLevel] = bitmap;

                // Show new bitmap sizes
                TallyBitmapSizes();
            }

            // Display the bitmap
            bitmapIndex = zoomLevel;
            canvasView.InvalidateSurface();
        }

        // Now start the animation
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }
    ···
}
```

Si noti che il programma archivia tali bitmap nello spazio di memorizzazione locale dell'applicazione invece che nella libreria di foto del dispositivo. La libreria .NET Standard 2,0 consente di utilizzare i metodi `File.OpenRead` e `File.WriteAllBytes` noti per questa attività.

Dopo che tutte le bitmap sono state create o caricate in memoria, il metodo avvia un oggetto `Stopwatch` e chiama `Device.StartTimer`. Il metodo `OnTimerTick` viene chiamato ogni 16 millisecondi.

`OnTimerTick` calcola un valore `time` in millisecondi compreso tra 0 e 6000 volte `COUNT`, che apportions sei secondi per la visualizzazione di ogni bitmap. Il valore `progress` usa il valore `Math.Sin` per creare un'animazione sinusoidale che risulteranno più lente all'inizio del ciclo e più lentamente alla fine mentre viene invertita la direzione.

Il valore `progress` è compreso tra 0 e `COUNT`. Ciò significa che la parte intera di `progress` è un indice nella matrice di `bitmaps`, mentre la parte frazionaria di `progress` indica un livello di zoom per quella particolare bitmap. Questi valori vengono archiviati nei campi `bitmapIndex` e `bitmapProgress` e vengono visualizzati dal `Label` e `Slider` nel file XAML. Il `SKCanvasView` viene invalidato per aggiornare la visualizzazione bitmap:

```csharp
public partial class MainPage : ContentPage
{
    ···
    Stopwatch stopwatch = new Stopwatch();      // for the animation
    int bitmapIndex;
    double bitmapProgress = 0;
    ···
    bool OnTimerTick()
    {
        int cycle = 6000 * COUNT;       // total cycle length in milliseconds

        // Time in milliseconds from 0 to cycle
        int time = (int)(stopwatch.ElapsedMilliseconds % cycle);

        // Make it sinusoidal, including bitmap index and gradation between bitmaps
        double progress = COUNT * 0.5 * (1 + Math.Sin(2 * Math.PI * time / cycle - Math.PI / 2));

        // These are the field values that the PaintSurface handler uses
        bitmapIndex = (int)progress;
        bitmapProgress = progress - bitmapIndex;

        // It doesn't often happen that we get up to COUNT, but an exception would be raised
        if (bitmapIndex < COUNT)
        {
            // Show progress in UI
            statusLabel.Text = $"Displaying bitmap for zoom level {bitmapIndex}";
            progressBar.Progress = bitmapProgress;

            // Update the canvas
            canvasView.InvalidateSurface();
        }

        return true;
    }
    ···
}
```

Infine, il gestore `PaintSurface` della `SKCanvasView` calcola un rettangolo di destinazione per visualizzare la bitmap il più grande possibile mantenendo le proporzioni. Un rettangolo di origine è basato sul valore `bitmapProgress`. Il valore `fraction` calcolato qui è compreso tra 0 e 0 quando `bitmapProgress` è 0 per visualizzare l'intera bitmap, 0,25 quando `bitmapProgress` è 1 per visualizzare la metà della larghezza e dell'altezza della bitmap, effettuando lo zoom avanti in modo efficace:

```csharp
public partial class MainPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        if (bitmaps[bitmapIndex] != null)
        {
            // Determine destination rect as square in canvas
            int dimension = Math.Min(info.Width, info.Height);
            float x = (info.Width - dimension) / 2;
            float y = (info.Height - dimension) / 2;
            SKRect destRect = new SKRect(x, y, x + dimension, y + dimension);

            // Calculate source rectangle based on fraction:
            //  bitmapProgress == 0: full bitmap
            //  bitmapProgress == 1: half of length and width of bitmap
            float fraction = 0.5f * (1 - (float)Math.Pow(2, -bitmapProgress));
            SKBitmap bitmap = bitmaps[bitmapIndex];
            int width = bitmap.Width;
            int height = bitmap.Height;
            SKRect sourceRect = new SKRect(fraction * width, fraction * height,
                                           (1 - fraction) * width, (1 - fraction) * height);

            // Display the bitmap
            canvas.DrawBitmap(bitmap, sourceRect, destRect);
        }
    }
    ···
}
```

Ecco il programma in esecuzione:

[![Animazione di Mandelbrot](animating-images/MandelbrotAnimation.png "Animazione di Mandelbrot")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>Immagine GIF animata

La specifica di formato GIF (Graphics Interchange) include una funzionalità che consente a un singolo file GIF contenga più frame sequenziali di una scena che possono essere visualizzati in successione, spesso in un ciclo. Questi file sono noti come _gif animate_. Web browser possono svolgere animate e SkiaSharp consente a un'applicazione per estrarre i frame da un file GIF animato e li visualizza in sequenza.

L'esempio [SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) include una risorsa gif animata denominata **Newtons_cradle_animation_book_2. gif** creata da DemonDeLuxe e scaricata dalla pagina della [culla di Newton](https://en.wikipedia.org/wiki/Newton%27s_cradle) in Wikipedia. La pagina **gif animata** include un file XAML che fornisce tali informazioni e crea un'istanza di un `SKCanvasView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.AnimatedGifPage"
             Title="Animated GIF">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="GIF file by DemonDeLuxe from Wikipedia Newton's Cradle page"
               Grid.Row="1"
               Margin="0, 5"
               HorizontalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

Il file code-behind non è generalizzato per riprodurre qualsiasi file GIF animate. Ignora alcune informazioni che sono disponibile, in particolare un numero di ripetizioni e viene riprodotto semplicemente GIF animato in un ciclo.

L'uso di SkisSharp per estrarre i frame di un GIF animato non sembra essere documentato ovunque, in modo che la descrizione del codice che segue è più dettagliata superiore al normale:

La decodifica del file GIF animato si verifica nel costruttore della pagina e richiede che l'oggetto `Stream` che fa riferimento alla bitmap venga usato per creare un oggetto `SKManagedStream` e quindi un oggetto [`SKCodec`](xref:SkiaSharp.SKCodec) . La proprietà [`FrameCount`](xref:SkiaSharp.SKCodec.FrameCount) indica il numero di frame che compongono l'animazione.

Questi frame vengono infine salvati come singole bitmap, quindi il costruttore usa `FrameCount` per allocare una matrice di tipo `SKBitmap`, nonché due matrici di `int` per la durata di ogni frame e (per semplificare la logica di animazione) le durate accumulate.

La proprietà [`FrameInfo`](xref:SkiaSharp.SKCodec.FrameInfo) della classe `SKCodec` è una matrice di valori [`SKCodecFrameInfo`](xref:SkiaSharp.SKCodecFrameInfo) , uno per ogni frame, ma l'unica cosa che questo programma prende da tale struttura è la [`Duration`](xref:SkiaSharp.SKCodecFrameInfo.Duration) del frame, in millisecondi.

`SKCodec` definisce una proprietà denominata [`Info`](xref:SkiaSharp.SKCodec.Info) di tipo [`SKImageInfo`](xref:SkiaSharp.SKImageInfo), ma tale valore `SKImageInfo` indica (almeno per questa immagine) che il tipo di colore è `SKColorType.Index8`, il che significa che ogni pixel è un indice in un tipo di colore. Per evitare problemi con le tabelle dei colori, il programma usa il [`Width`](xref:SkiaSharp.SKImageInfo.Width) e [`Height`](xref:SkiaSharp.SKImageInfo.Height) informazioni da tale struttura per costruire il proprio valore di `ImageInfo` a colori completo. Ogni `SKBitmap` viene creato da tale.

Il metodo `GetPixels` di `SKBitmap` restituisce un `IntPtr` che fa riferimento ai bit di pixel della bitmap. Questi bit di pixel non sono ancora state impostate. Il `IntPtr` viene passato a uno dei [`GetPixels`](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions)) metodi di `SKCodec`. Tale metodo copia il frame dal file GIF nello spazio di memoria a cui fa riferimento il `IntPtr`. Il costruttore [`SKCodecOptions`](xref:SkiaSharp.SKCodecOptions) indica il numero di frame:

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;
    ···

    public AnimatedGifPage ()
    {
        InitializeComponent ();

        string resourceID = "SkiaSharpFormsDemos.Media.Newtons_cradle_animation_book_2.gif";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        using (SKCodec codec = SKCodec.Create(skStream))
        {
            // Get frame count and allocate bitmaps
            int frameCount = codec.FrameCount;
            bitmaps = new SKBitmap[frameCount];
            durations = new int[frameCount];
            accumulatedDurations = new int[frameCount];

            // Note: There's also a RepetitionCount property of SKCodec not used here

            // Loop through the frames
            for (int frame = 0; frame < frameCount; frame++)
            {
                // From the FrameInfo collection, get the duration of each frame
                durations[frame] = codec.FrameInfo[frame].Duration;

                // Create a full-color bitmap for each frame
                SKImageInfo imageInfo = code.new SKImageInfo(codec.Info.Width, codec.Info.Height);
                bitmaps[frame] = new SKBitmap(imageInfo);

                // Get the address of the pixels in that bitmap
                IntPtr pointer = bitmaps[frame].GetPixels();

                // Create an SKCodecOptions value to specify the frame
                SKCodecOptions codecOptions = new SKCodecOptions(frame, false);

                // Copy pixels from the frame into the bitmap
                codec.GetPixels(imageInfo, pointer, codecOptions);
            }

            // Sum up the total duration
            for (int frame = 0; frame < durations.Length; frame++)
            {
                totalDuration += durations[frame];
            }

            // Calculate the accumulated durations
            for (int frame = 0; frame < durations.Length; frame++)
            {
                accumulatedDurations[frame] = durations[frame] +
                    (frame == 0 ? 0 : accumulatedDurations[frame - 1]);
            }
        }
    }
    ···
}
```

Nonostante il valore `IntPtr`, non è necessario alcun codice `unsafe` perché il `IntPtr` non viene mai convertito C# in un valore di puntatore.

Dopo avere estratto ogni fotogramma, il costruttore totali backup la durata di tutti i frame e inizializza quindi un'altra matrice con le durate accumulate.

Il resto del file code-behind è dedicato all'animazione. Il metodo `Device.StartTimer` viene utilizzato per avviare un timer in corso e il callback `OnTimerTick` utilizza un oggetto `Stopwatch` per determinare il tempo trascorso in millisecondi. Scorrimento in ciclo tra la matrice di durate accumulato è sufficiente per trovare il frame corrente:

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;

    Stopwatch stopwatch = new Stopwatch();
    bool isAnimating;

    int currentFrame;
    ···
    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        int msec = (int)(stopwatch.ElapsedMilliseconds % totalDuration);
        int frame = 0;

        // Find the frame based on the elapsed time
        for (frame = 0; frame < accumulatedDurations.Length; frame++)
        {
            if (msec < accumulatedDurations[frame])
            {
                break;
            }
        }

        // Save in a field and invalidate the SKCanvasView.
        if (currentFrame != frame)
        {
            currentFrame = frame;
            canvasView.InvalidateSurface();
        }

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Get the bitmap and center it
        SKBitmap bitmap = bitmaps[currentFrame];
        canvas.DrawBitmap(bitmap,info.Rect, BitmapStretch.Uniform);
    }
}
```

Ogni volta che cambia la variabile `currentframe`, il `SKCanvasView` viene invalidato e viene visualizzato il nuovo frame:

[![GIF animata](animating-images/AnimatedGif.png "GIF animata")](animating-images/AnimatedGif-Large.png#lightbox)

Naturalmente, è opportuno eseguire il programma autonomamente per visualizzare l'animazione.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Animazione di Mandelbrot (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)
