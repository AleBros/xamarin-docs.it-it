---
title: "accesso a bits pixel bitmap SkiaSharp" Descrizione: "individuare le varie tecniche per l'accesso e la modifica dei bit di pixel delle bitmap SkiaSharp".
ms. prod: Novell MS. Technology: Novell-skiasharp ms. AssetID: DBB58522-F816-4A8C-96A5-E0236F16A5C6 autore: davidbritch ms. Author: dabritch ms. Date: 07/11/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>Accesso a bit di pixel bitmap SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Come illustrato nell'articolo [**salvataggio di bitmap SkiaSharp in file**](saving.md), le bitmap vengono in genere archiviate in file in formato compresso, ad esempio JPEG o png. In conforme, una bitmap SkiaSharp archiviata in memoria non viene compressa. Viene archiviato come serie sequenziale di pixel. Questo formato non compresso semplifica il trasferimento di bitmap a una superficie di visualizzazione.

Il blocco di memoria occupato da una bitmap SkiaSharp è organizzato in modo molto semplice: inizia con la prima riga di pixel, da sinistra verso destra, quindi continua con la seconda riga. Per le bitmap a colori completi, ogni pixel è costituito da quattro byte, il che significa che lo spazio di memoria totale richiesto dalla bitmap è quattro volte il prodotto della larghezza e dell'altezza.

Questo articolo descrive il modo in cui un'applicazione può ottenere l'accesso a tali pixel, direttamente accedendo al blocco di memoria pixel della bitmap o indirettamente. In alcuni casi, un programma potrebbe voler analizzare i pixel di un'immagine e costruire un istogramma di qualche tipo. Più comunemente, le applicazioni possono costruire immagini univoche algoritmicamente creando i pixel che compongono la bitmap:

![Esempi di bit pixel](pixel-bits-images/PixelBitsSample.png "Esempio di bit di pixel")

## <a name="the-techniques"></a>Tecniche

SkiaSharp fornisce diverse tecniche per accedere ai bit di pixel di una bitmap. Quello scelto è in genere un compromesso tra la convenienza del codice (correlato alla manutenzione e alla facilità di debug) e alle prestazioni. Nella maggior parte dei casi, si userà uno dei seguenti metodi e proprietà di `SKBitmap` per accedere ai pixel della bitmap:

- I `GetPixel` `SetPixel` metodi e consentono di ottenere o impostare il colore di un singolo pixel.
- La `Pixels` proprietà ottiene una matrice di colori pixel per l'intera bitmap o imposta la matrice di colori.
- `GetPixels`Restituisce l'indirizzo della memoria pixel utilizzata dalla bitmap.
- `SetPixels`sostituisce l'indirizzo della memoria pixel utilizzata dalla bitmap.

È possibile considerare le prime due tecniche come "alto livello" e le seconde due come "livello basso". Sono disponibili altri metodi e proprietà che è possibile usare, ma questi sono i più importanti.

Per consentire all'utente di visualizzare le differenze di prestazioni tra queste tecniche, l'applicazione [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contiene una pagina denominata **bitmap con sfumature** che consente di creare una bitmap con pixel che combinano tonalità rosse e blu per creare una sfumatura. Il programma crea otto copie diverse di questa bitmap, con diverse tecniche per l'impostazione dei pixel bitmap. Ognuna di queste otto bitmap viene creata in un metodo separato che imposta anche una breve descrizione del testo della tecnica e calcola il tempo necessario per impostare tutti i pixel. Ogni metodo scorre la logica di impostazione dei pixel 100 volte per ottenere una stima migliore delle prestazioni.

### <a name="the-setpixel-method"></a>Metodo sepixel

Se è necessario impostare o ottenere solo diversi pixel, i [`SetPixel`](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) [`GetPixel`](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) metodi e sono ideali. Per ognuno di questi due metodi, è necessario specificare la colonna e la riga di tipo Integer. Indipendentemente dal formato pixel, questi due metodi consentono di ottenere o impostare il pixel come `SKColor` valore:

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

L' `col` argomento deve essere compreso tra 0 e uno minore della `Width` proprietà della bitmap e `row` varia da 0 a uno minore della `Height` Proprietà.

Ecco il metodo nella **bitmap sfumatura** che imposta il contenuto di una bitmap usando il `SetPixel` metodo. La bitmap è 256 di 256 pixel e i `for` cicli sono hardcoded con l'intervallo di valori:

```csharp
public class GradientBitmapPage : ContentPage
{
    const int REPS = 100;

    Stopwatch stopwatch = new Stopwatch();
    ···
    SKBitmap FillBitmapSetPixel(out string description, out int milliseconds)
    {
        description = "SetPixel";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        for (int rep = 0; rep < REPS; rep++)
            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    bitmap.SetPixel(col, row, new SKColor((byte)col, 0, (byte)row));
                }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
}
```

Il set di colori per ogni pixel ha un componente rosso uguale alla colonna bitmap e un componente blu uguale alla riga. La bitmap risultante è nera in alto a sinistra, rossa in alto a destra, blu in basso a sinistra e magenta in basso a destra, con sfumature altrove.

Il `SetPixel` metodo è denominato 65.536 volte e indipendentemente dal livello di efficienza di questo metodo, in genere non è consigliabile fare in modo che molte API chiamino se è disponibile un'alternativa. Fortunatamente, esistono diverse alternative.

### <a name="the-pixels-property"></a>Proprietà pixels

`SKBitmap`definisce una [`Pixels`](xref:SkiaSharp.SKBitmap.Pixels) proprietà che restituisce una matrice di `SKColor` valori per l'intera bitmap. È anche possibile usare `Pixels` per impostare una matrice di valori di colore per la bitmap:

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

I pixel vengono disposti nella matrice a partire dalla prima riga, da sinistra a destra, quindi dalla seconda riga e così via. Il numero totale di colori nella matrice è uguale al prodotto della larghezza e dell'altezza della bitmap.

Sebbene questa proprietà risulti efficiente, tenere presente che i pixel vengono copiati dalla bitmap nella matrice e dalla matrice di nuovo nella bitmap e i pixel vengono convertiti da e a `SKColor` valori.

Ecco il metodo nella `GradientBitmapPage` classe che imposta la bitmap usando la `Pixels` Proprietà. Il metodo alloca una `SKColor` matrice delle dimensioni necessarie, ma potrebbe aver usato la `Pixels` proprietà per creare la matrice:

```csharp
SKBitmap FillBitmapPixelsProp(out string description, out int milliseconds)
{
    description = "Pixels property";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    SKColor[] pixels = new SKColor[256 * 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                pixels[256 * row + col] = new SKColor((byte)col, 0, (byte)row);
            }

    bitmap.Pixels = pixels;

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Si noti che l'indice della `pixels` matrice deve essere calcolato dalle `row` `col` variabili e. La riga viene moltiplicata per il numero di pixel in ogni riga (256 in questo caso), quindi viene aggiunta la colonna.

`SKBitmap`definisce anche una `Bytes` proprietà simile, che restituisce una matrice di byte per l'intera bitmap, ma è più complessa per le bitmap a colori completi.

### <a name="the-getpixels-pointer"></a>Puntatore getPixels

Potenzialmente la tecnica più potente per accedere ai pixel della bitmap è [`GetPixels`](xref:SkiaSharp.SKBitmap.GetPixels) , da non confondere con il `GetPixel` metodo o la `Pixels` Proprietà. Si noterà immediatamente una differenza con `GetPixels` in quanto restituisce un elemento non molto comune nella programmazione C#:

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

Il [`IntPtr`](xref:System.IntPtr) tipo .NET rappresenta un puntatore. Viene chiamato `IntPtr` perché è la lunghezza di un numero intero nel processore nativo del computer in cui viene eseguito il programma, in genere 32 bit o 64 bit. Il `IntPtr` che `GetPixels` restituisce è l'indirizzo del blocco di memoria effettivo usato dall'oggetto bitmap per archiviare i pixel.

È possibile convertire `IntPtr` in un tipo di puntatore C# usando il [`ToPointer`](xref:System.IntPtr.ToPointer) metodo. La sintassi del puntatore C# è identica a quella di C e C++:

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

La `ptr` variabile è di tipo _byte Pointer_. Questa `ptr` variabile consente di accedere ai singoli byte di memoria utilizzati per archiviare i pixel della bitmap. Usare codice simile al seguente per leggere un byte dalla memoria o scrivere un byte nella memoria:

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

In questo contesto, l'asterisco è l'operatore di riferimento _indiretto_ C# e viene usato per fare riferimento al contenuto della memoria a cui punta `ptr` . Inizialmente, `ptr` punta al primo byte del primo pixel della prima riga della bitmap, ma è possibile eseguire operazioni aritmetiche sulla `ptr` variabile per spostarla in altre posizioni all'interno della bitmap.

Uno svantaggio è che è possibile usare questa `ptr` variabile solo in un blocco di codice contrassegnato con la `unsafe` parola chiave. Inoltre, è necessario contrassegnare l'assembly come che consenta blocchi unsafe. Questa operazione viene eseguita nelle proprietà del progetto.

L'uso di puntatori in C# è molto potente, ma anche molto pericoloso. È necessario prestare attenzione a non accedere alla memoria oltre a quella a cui deve fare riferimento il puntatore. Questo è il motivo per cui l'uso del puntatore è associato alla parola "unsafe".

Ecco il metodo nella `GradientBitmapPage` classe che usa il `GetPixels` metodo. Si noti il `unsafe` blocco che include tutto il codice usando il puntatore di byte:

```csharp
SKBitmap FillBitmapBytePtr(out string description, out int milliseconds)
{
    description = "GetPixels byte ptr";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            byte* ptr = (byte*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (byte)(col);   // red
                    *ptr++ = 0;             // green
                    *ptr++ = (byte)(row);   // blue
                    *ptr++ = 0xFF;          // alpha
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Quando la `ptr` variabile viene prima ottenuta dal `ToPointer` metodo, punta al primo byte del pixel più a sinistra della prima riga della bitmap. I `for` cicli per `row` e `col` sono configurati in modo che `ptr` possano essere incrementati con l' `++` operatore dopo che è stato impostato ogni byte di ogni pixel. Per gli altri 99 cicli attraverso i pixel, `ptr` è necessario impostare di nuovo l'inizio della bitmap.

Ogni pixel è costituito da quattro byte di memoria, quindi ogni byte deve essere impostato separatamente. Il codice si basa sul presupposto che i byte siano nell'ordine rosso, verde, blu e alfa, che è coerente con il `SKColorType.Rgba8888` tipo di colore. È possibile ricordare che questo è il tipo di colore predefinito per iOS e Android, ma non per il piattaforma UWP (Universal Windows Platform). Per impostazione predefinita, UWP crea bitmap con il `SKColorType.Bgra8888` tipo di colore. Per questo motivo, si prevede di visualizzare alcuni risultati diversi su tale piattaforma.

È possibile eseguire il cast del valore restituito da `ToPointer` a un `uint` puntatore anziché un `byte` puntatore. Questo consente l'accesso a un intero pixel in un'unica istruzione. `++`Se si applica l'operatore a tale puntatore, questo viene incrementato di quattro byte per puntare al pixel successivo:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    SKBitmap FillBitmapUintPtr(out string description, out int milliseconds)
    {
        description = "GetPixels uint ptr";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        IntPtr pixelsAddr = bitmap.GetPixels();

        unsafe
        {
            for (int rep = 0; rep < REPS; rep++)
            {
                uint* ptr = (uint*)pixelsAddr.ToPointer();

                for (int row = 0; row < 256; row++)
                    for (int col = 0; col < 256; col++)
                    {
                        *ptr++ = MakePixel((byte)col, 0, (byte)row, 0xFF);
                    }
            }
        }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
    [MethodImpl(MethodImplOptions.AggressiveInlining)]
    uint MakePixel(byte red, byte green, byte blue, byte alpha) =>
            (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

Il pixel viene impostato usando il `MakePixel` metodo, che costruisce un pixel Integer dai componenti rosso, verde, blu e alfa. Tenere presente che il `SKColorType.Rgba8888` formato ha un ordine di byte in pixel simile al seguente:

RR GG BB AA

Ma il numero intero corrispondente a questi byte è:

AABBGGRR

Il byte meno significativo dell'integer viene archiviato per primo in base all'architettura little-endian. Questo `MakePixel` metodo non funzionerà correttamente per le bitmap con il `Bgra8888` tipo di colore.

Il `MakePixel` metodo è contrassegnato con l' [`MethodImplOptions.AggressiveInlining`](xref:System.Runtime.CompilerServices.MethodImplOptions) opzione per incoraggiare il compilatore a evitare di creare questo metodo separato, ma di compilare invece il codice in cui viene chiamato il metodo. Questa operazione dovrebbe migliorare le prestazioni.

È interessante notare `SKColor` che la struttura definisce una conversione esplicita da `SKColor` a una Unsigned Integer, il che significa che è `SKColor` possibile creare un valore e che `uint` è possibile utilizzare una conversione in anziché `MakePixel` :

```csharp
SKBitmap FillBitmapUintPtrColor(out string description, out int milliseconds)
{
    description = "GetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            uint* ptr = (uint*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (uint)new SKColor((byte)col, 0, (byte)row);
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

L'unica domanda è: è il formato integer del `SKColor` valore nell'ordine del `SKColorType.Rgba8888` tipo di colore, o il `SKColorType.Bgra8888` tipo di colore, oppure è un altro elemento interamente? La risposta a tale domanda verrà rivelata a breve.

### <a name="the-setpixels-method"></a>Metodo sepixels

`SKBitmap`definisce anche un metodo denominato [`SetPixels`](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)) , che viene chiamato come segue:

```csharp
bitmap.SetPixels(intPtr);
```

Si ricordi che `GetPixels` ottiene un oggetto che fa `IntPtr` riferimento al blocco di memoria usato dalla bitmap per archiviare i pixel. La `SetPixels` chiamata _sostituisce_ il blocco di memoria con il blocco di memoria a cui fa riferimento l' `IntPtr` oggetto specificato come `SetPixels` argomento. La bitmap libera quindi il blocco di memoria usato in precedenza. La volta successiva `GetPixels` che viene chiamato, ottiene il blocco di memoria impostato con `SetPixels` .

Inizialmente, sembra che `SetPixels` non fornisca più potenza e prestazioni rispetto `GetPixels` a quelle meno convenienti. Con `GetPixels` si ottiene il blocco di memoria bitmap e vi si accede. Con `SetPixels` è possibile allocare e accedere a una certa memoria, quindi impostarla come blocco di memoria bitmap.

Tuttavia `SetPixels` , l'utilizzo di offre un vantaggio sintattico distinto: consente di accedere ai bit di pixel bitmap utilizzando una matrice. Ecco il metodo in `GradientBitmapPage` che illustra questa tecnica. Il metodo definisce prima di tutto una matrice di byte multidimensionali corrispondente ai byte dei pixel della bitmap. La prima dimensione è la riga, la seconda dimensione è la colonna e la terza dimensione corrisponde all'opzione ai quattro componenti di ogni pixel:

```csharp
SKBitmap FillBitmapByteBuffer(out string description, out int milliseconds)
{
    description = "SetPixels byte buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    byte[,,] buffer = new byte[256, 256, 4];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col, 0] = (byte)col;   // red
                buffer[row, col, 1] = 0;           // green
                buffer[row, col, 2] = (byte)row;   // blue
                buffer[row, col, 3] = 0xFF;        // alpha
            }

    unsafe
    {
        fixed (byte* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Quindi, dopo che la matrice è stata riempita con pixel, `unsafe` viene usato un blocco e un' `fixed` istruzione per ottenere un puntatore di byte che punta a questa matrice. È quindi possibile eseguire il cast del puntatore di byte a un oggetto `IntPtr` da passare a `SetPixels` .

La matrice creata non deve essere una matrice di byte. Può essere una matrice di interi con solo due dimensioni per la riga e la colonna:

```csharp
SKBitmap FillBitmapUintBuffer(out string description, out int milliseconds)
{
    description = "SetPixels uint buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = MakePixel((byte)col, 0, (byte)row, 0xFF);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Il `MakePixel` metodo viene di nuovo utilizzato per combinare i componenti di colore in un pixel a 32 bit.

Solo per completezza, di seguito è riportato lo stesso codice, ma con un `SKColor` valore di cui è stato eseguito il cast a un Unsigned Integer:

```csharp
SKBitmap FillBitmapUintBufferColor(out string description, out int milliseconds)
{
    description = "SetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = (uint)new SKColor((byte)col, 0, (byte)row);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

### <a name="comparing-the-techniques"></a>Confronto tra le tecniche

Il costruttore della pagina **colore sfumatura** chiama tutti gli otto metodi illustrati sopra e salva i risultati:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    string[] descriptions = new string[8];
    SKBitmap[] bitmaps = new SKBitmap[8];
    int[] elapsedTimes = new int[8];

    SKCanvasView canvasView;

    public GradientBitmapPage ()
    {
        Title = "Gradient Bitmap";

        bitmaps[0] = FillBitmapSetPixel(out descriptions[0], out elapsedTimes[0]);
        bitmaps[1] = FillBitmapPixelsProp(out descriptions[1], out elapsedTimes[1]);
        bitmaps[2] = FillBitmapBytePtr(out descriptions[2], out elapsedTimes[2]);
        bitmaps[4] = FillBitmapUintPtr(out descriptions[4], out elapsedTimes[4]);
        bitmaps[6] = FillBitmapUintPtrColor(out descriptions[6], out elapsedTimes[6]);
        bitmaps[3] = FillBitmapByteBuffer(out descriptions[3], out elapsedTimes[3]);
        bitmaps[5] = FillBitmapUintBuffer(out descriptions[5], out elapsedTimes[5]);
        bitmaps[7] = FillBitmapUintBufferColor(out descriptions[7], out elapsedTimes[7]);

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

Il costruttore termina creando un oggetto `SKCanvasView` per visualizzare le bitmap risultanti. Il `PaintSurface` gestore divide la relativa superficie in otto rettangoli e chiama `Display` per visualizzare ciascuno di essi:

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        int width = info.Width;
        int height = info.Height;

        canvas.Clear();

        Display(canvas, 0, new SKRect(0, 0, width / 2, height / 4));
        Display(canvas, 1, new SKRect(width / 2, 0, width, height / 4));
        Display(canvas, 2, new SKRect(0, height / 4, width / 2, 2 * height / 4));
        Display(canvas, 3, new SKRect(width / 2, height / 4, width, 2 * height / 4));
        Display(canvas, 4, new SKRect(0, 2 * height / 4, width / 2, 3 * height / 4));
        Display(canvas, 5, new SKRect(width / 2, 2 * height / 4, width, 3 * height / 4));
        Display(canvas, 6, new SKRect(0, 3 * height / 4, width / 2, height));
        Display(canvas, 7, new SKRect(width / 2, 3 * height / 4, width, height));
    }

    void Display(SKCanvas canvas, int index, SKRect rect)
    {
        string text = String.Format("{0}: {1:F1} msec", descriptions[index],
                                    (double)elapsedTimes[index] / REPS);

        SKRect bounds = new SKRect();

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.TextSize = (float)(12 * canvasView.CanvasSize.Width / canvasView.Width);
            textPaint.TextAlign = SKTextAlign.Center;
            textPaint.MeasureText("Tly", ref bounds);

            canvas.DrawText(text, new SKPoint(rect.MidX, rect.Bottom - bounds.Bottom), textPaint);
            rect.Bottom -= bounds.Height;
            canvas.DrawBitmap(bitmaps[index], rect, BitmapStretch.Uniform);
        }
    }
}
```

Per consentire al compilatore di ottimizzare il codice, questa pagina è stata eseguita in modalità di **rilascio** . Di seguito è illustrata la pagina in esecuzione su un simulatore iPhone 8 in un MacBook Pro, un telefono Android Nexus 5 e Surface Pro 3 che esegue Windows 10. A causa delle differenze hardware, evitare di confrontare i tempi di prestazioni tra i dispositivi, ma esaminare le ore relative in ogni dispositivo:

[![Bitmap sfumatura](pixel-bits-images/GradientBitmap.png "Bitmap sfumatura")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

Ecco una tabella che consolida i tempi di esecuzione in millisecondi:

| API       | Tipo di dati | iOS  | Android | Piattaforma UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3,17 |   10,77 | 3.49 |
| Pixel    |           | 0,32 |    1.23 | 0,07 |
| GetPixels | byte      | 0,09 |    0.24 | 0,10 |
|           | uint      | 0.06 |    0,26 | 0.05 |
|           | SKColor   | 0,29 |    0,99 | 0,07 |
| SetPixels | byte      | 1.33 |    6,78 | 0,11 |
|           | uint      | 0.14 |    0,69 | 0.06 |
|           | SKColor   | 0,35 |    1,93 | 0,10 |

Come previsto, la chiamata di `SetPixel` 65.536 volte è il modo meno effeicient per impostare i pixel di una bitmap. Il riempimento di una `SKColor` matrice e l'impostazione della `Pixels` proprietà sono molto migliori e persino il confronto con alcune delle `GetPixels` tecniche e `SetPixels` . L'utilizzo di `uint` valori pixel è in genere più veloce rispetto all'impostazione di `byte` componenti distinti e la conversione del `SKColor` valore in una Unsigned Integer aggiunge un sovraccarico al processo.

È anche interessante confrontare le varie sfumature: le prime righe di ogni piattaforma sono le stesse e mostrano la sfumatura come previsto. Ciò significa che il `SetPixel` metodo e la `Pixels` Proprietà creano correttamente pixel dai colori indipendentemente dal formato pixel sottostante.

Anche le due righe successive degli screenshot iOS e Android sono le stesse, il che conferma che il `MakePixel` Metodo piccolo è definito correttamente per il `Rgba8888` formato pixel predefinito per queste piattaforme.

La riga inferiore degli screenshot iOS e Android è indietro, che indica che il Unsigned Integer ottenuto eseguendo il cast di un `SKColor` valore ha il formato seguente:

AARRGGBB

I byte si trovano nell'ordine:

BB GG RR AA

Si tratta dell' `Bgra8888` ordinamento anziché dell' `Rgba8888` ordinamento. Il `Brga8888` formato è l'impostazione predefinita per la piattaforma Windows universale, motivo per cui le sfumature nell'ultima riga dello screenshot corrispondono alla prima riga. Tuttavia, le due righe centrali non sono corrette perché il codice che crea tali bitmap presuppone un `Rgba8888` ordinamento.

Se si desidera utilizzare lo stesso codice per accedere a bit di pixel in ogni piattaforma, è possibile creare in modo esplicito un oggetto `SKBitmap` utilizzando il `Rgba8888` `Bgra8888` formato o. Se si desidera eseguire il cast `SKColor` dei valori a bitmap pixel, utilizzare `Bgra8888` .

## <a name="random-access-of-pixels"></a>Accesso casuale dei pixel

I `FillBitmapBytePtr` `FillBitmapUintPtr` metodi e nella pagina **bitmap sfumatura** traggono vantaggio dai `for` cicli progettati per riempire la bitmap in sequenza, dalla riga superiore alla riga inferiore e in ogni riga da sinistra a destra. Il pixel può essere impostato con la stessa istruzione che ha incrementato il puntatore.

Talvolta è necessario accedere ai pixel in modo casuale anziché in sequenza. Se si usa l' `GetPixels` approccio, sarà necessario calcolare i puntatori in base alla riga e alla colonna. Questa operazione viene illustrata nella pagina del **seno arcobaleno** , che consente di creare una bitmap che mostra un arcobaleno sotto forma di un ciclo di una curva seno.

I colori dell'arcobaleno sono più semplici da creare usando il modello di colore HSL (Hue, Saturation, luminosità). Il `SKColor.FromHsl` metodo crea un `SKColor` valore usando valori di tonalità compresi tra 0 e 360 (ad esempio, gli angoli di un cerchio, ma passando da rosso, verde e blu e viceversa), i valori di saturazione e luminosità compresi tra 0 e 100. Per i colori di un arcobaleno, la saturazione deve essere impostata su un massimo di 100 e sulla luminosità fino a un punto medio di 50.

**Rainbow sine** crea questa immagine eseguendo il ciclo delle righe della bitmap e quindi scorrendo i valori di tonalità 360. Da ogni valore di tonalità viene calcolata una colonna bitmap basata anche su un valore seno:

```csharp
public class RainbowSinePage : ContentPage
{
    SKBitmap bitmap;

    public RainbowSinePage()
    {
        Title = "Rainbow Sine";

        bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);

        unsafe
        {
            // Pointer to first pixel of bitmap
            uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();

            // Loop through the rows
            for (int row = 0; row < bitmap.Height; row++)
            {
                // Calculate the sine curve angle and the sine value
                double angle = 2 * Math.PI * row / bitmap.Height;
                double sine = Math.Sin(angle);

                // Loop through the hues
                for (int hue = 0; hue < 360; hue++)
                {
                    // Calculate the column
                    int col = (int)(360 + 360 * sine + hue);

                    // Calculate the address
                    uint* ptr = basePtr + bitmap.Width * row + col;

                    // Store the color value
                    *ptr = (uint)SKColor.FromHsl(hue, 100, 50);
                }
            }
        }

        // Create the SKCanvasView
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

Si noti che il costruttore crea la bitmap in base al `SKColorType.Bgra8888` formato:

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

Ciò consente al programma di utilizzare la conversione dei `SKColor` valori in `uint` pixel senza preoccuparsi. Sebbene non svolga un ruolo in questo particolare programma, ogni volta che si usa la `SKColor` conversione per impostare i pixel, è necessario specificare anche `SKAlphaType.Unpremul` perché `SKColor` non premoltiplica i componenti di colore per il valore alfa.

Il costruttore usa quindi il `GetPixels` metodo per ottenere un puntatore al primo pixel della bitmap:

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

Per ogni riga e colonna specifica, è necessario aggiungere un valore di offset a `basePtr` . Questo offset è la riga per la larghezza della bitmap, più la colonna:

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

Il `SKColor` valore viene archiviato in memoria utilizzando questo puntatore:

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

Nel `PaintSurface` gestore di `SKCanvasView` , la bitmap viene estesa per riempire l'area di visualizzazione:

[![Seno arcobaleno](pixel-bits-images/RainbowSine.png "Seno arcobaleno")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>Da una bitmap a un'altra

Molte attività di elaborazione di immagini comportano la modifica dei pixel quando vengono trasferiti da una bitmap a un'altra. Questa tecnica è illustrata nella pagina di **regolazione del colore** . La pagina carica una delle risorse bitmap e quindi consente di modificare l'immagine usando tre `Slider` Visualizzazioni:

[![Regolazione del colore](pixel-bits-images/ColorAdjustment.png "Regolazione del colore")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

Per ogni colore pixel, il primo `Slider` aggiunge un valore da 0 a 360 alla tonalità, ma usa quindi l'operatore modulo per tenere il risultato compreso tra 0 e 360, spostando in modo efficace i colori lungo lo spettro, come illustrato nella schermata UWP. Il secondo `Slider` consente di selezionare un fattore di moltiplicazione compreso tra 0,5 e 2 da applicare alla saturazione e la terza `Slider` esegue la stessa operazione per la luminosità, come illustrato nello screenshot di Android.

Il programma gestisce due bitmap, la bitmap di origine originale denominata `srcBitmap` e la bitmap di destinazione modificata denominata `dstBitmap` . Ogni volta che `Slider` viene spostato un oggetto, il programma calcola tutti i nuovi pixel in `dstBitmap` . Naturalmente, gli utenti sperimentano spostando le `Slider` visualizzazioni molto rapidamente, quindi si desiderano le migliori prestazioni che è possibile gestire. Questo implica il `GetPixels` metodo per le bitmap di origine e di destinazione.

La pagina di **regolazione del colore** non controlla il formato dei colori delle bitmap di origine e di destinazione. Contiene invece una logica leggermente diversa per i `SKColorType.Rgba8888` `SKColorType.Bgra8888` formati e. L'origine e la destinazione possono essere formati diversi e il programma funzionerà comunque.

Ecco il programma, ad eccezione del metodo cruciale `TransferPixels` che trasferisce i pixel al formato di origine alla destinazione. Il costruttore imposta `dstBitmap` uguale a `srcBitmap` . Il `PaintSurface` gestore Visualizza `dstBitmap` :

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    SKBitmap srcBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKBitmap dstBitmap;

    public ColorAdjustmentPage()
    {
        InitializeComponent();

        dstBitmap = new SKBitmap(srcBitmap.Width, srcBitmap.Height);
        OnSliderValueChanged(null, null);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        float hueAdjust = (float)hueSlider.Value;
        hueLabel.Text = $"Hue Adjustment: {hueAdjust:F0}";

        float saturationAdjust = (float)Math.Pow(2, saturationSlider.Value);
        saturationLabel.Text = $"Saturation Adjustment: {saturationAdjust:F2}";

        float luminosityAdjust = (float)Math.Pow(2, luminositySlider.Value);
        luminosityLabel.Text = $"Luminosity Adjustment: {luminosityAdjust:F2}";

        TransferPixels(hueAdjust, saturationAdjust, luminosityAdjust);
        canvasView.InvalidateSurface();
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(dstBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

Il `ValueChanged` gestore per le `Slider` visualizzazioni calcola i valori di regolazione e le chiamate `TransferPixels` .

L'intero `TransferPixels` metodo è contrassegnato come `unsafe` . Inizia ottenendo puntatori di byte ai bit di pixel di entrambe le bitmap e quindi scorre tutte le righe e le colonne. Dalla bitmap di origine, il metodo ottiene quattro byte per ogni pixel. Questi possono essere nell' `Rgba8888` `Bgra8888` ordine o. Il controllo del tipo di colore consente la `SKColor` creazione di un valore. I componenti HSL vengono quindi estratti, modificati e usati per ricreare il `SKColor` valore. A seconda se la bitmap di destinazione è `Rgba8888` o `Bgra8888` , i byte vengono archiviati nella bitmp di destinazione:

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    ···
    unsafe void TransferPixels(float hueAdjust, float saturationAdjust, float luminosityAdjust)
    {
        byte* srcPtr = (byte*)srcBitmap.GetPixels().ToPointer();
        byte* dstPtr = (byte*)dstBitmap.GetPixels().ToPointer();

        int width = srcBitmap.Width;       // same for both bitmaps
        int height = srcBitmap.Height;

        SKColorType typeOrg = srcBitmap.ColorType;
        SKColorType typeAdj = dstBitmap.ColorType;

        for (int row = 0; row < height; row++)
        {
            for (int col = 0; col < width; col++)
            {
                // Get color from original bitmap
                byte byte1 = *srcPtr++;         // red or blue
                byte byte2 = *srcPtr++;         // green
                byte byte3 = *srcPtr++;         // blue or red
                byte byte4 = *srcPtr++;         // alpha

                SKColor color = new SKColor();

                if (typeOrg == SKColorType.Rgba8888)
                {
                    color = new SKColor(byte1, byte2, byte3, byte4);
                }
                else if (typeOrg == SKColorType.Bgra8888)
                {
                    color = new SKColor(byte3, byte2, byte1, byte4);
                }

                // Get HSL components
                color.ToHsl(out float hue, out float saturation, out float luminosity);

                // Adjust HSL components based on adjustments
                hue = (hue + hueAdjust) % 360;
                saturation = Math.Max(0, Math.Min(100, saturationAdjust * saturation));
                luminosity = Math.Max(0, Math.Min(100, luminosityAdjust * luminosity));

                // Recreate color from HSL components
                color = SKColor.FromHsl(hue, saturation, luminosity);

                // Store the bytes in the adjusted bitmap
                if (typeAdj == SKColorType.Rgba8888)
                {
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Alpha;
                }
                else if (typeAdj == SKColorType.Bgra8888)
                {
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Alpha;
                }
            }
        }
    }
    ···
}
```

È probabile che le prestazioni di questo metodo possano essere migliorate ulteriormente creando metodi distinti per le varie combinazioni di tipi di colore delle bitmap di origine e di destinazione ed evitare di controllare il tipo per ogni pixel. Un'altra opzione consiste nel disporre di più `for` cicli per la `col` variabile in base al tipo di colore.

## <a name="posterization"></a>Posterizzazione

Un altro processo comune che prevede l'accesso a bits pixel è la _posterzzazione_. Il numero se i colori codificati nei pixel di una bitmap viene ridotto in modo che il risultato sia simile a un poster disegnato a mano usando una tavolozza dei colori limitata.

La pagina **posterizzazione** esegue questo processo in una delle immagini Monkey:

```csharp
public class PosterizePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public PosterizePage()
    {
        Title = "Posterize";

        unsafe
        {
            uint* ptr = (uint*)bitmap.GetPixels().ToPointer();
            int pixelCount = bitmap.Width * bitmap.Height;

            for (int i = 0; i < pixelCount; i++)
            {
                *ptr++ &= 0xE0E0E0FF;
            }
        }

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform;
    }
}
```

Il codice nel costruttore accede a ogni pixel, esegue un'operazione con AND bit per bit con il valore 0xE0E0E0FF e quindi archivia il risultato nella bitmap. Il valore 0xE0E0E0FF mantiene i 3 bit alti di ogni componente colore e imposta i 5 bit inferiori su 0. Invece di 2<sup>24</sup> o 16.777.216 colori, la bitmap è ridotta a 2<sup>9</sup> o 512 colori:

[![Posterizzazione](pixel-bits-images/Posterize.png "Posterizzazione")](pixel-bits-images/Posterize-Large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
