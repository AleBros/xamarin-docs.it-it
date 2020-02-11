---
title: L'accesso ai bit di pixel bitmap SkiaSharp
description: Scopri le diverse tecniche per l'accesso e modifica i bit di pixel delle bitmap di SkiaSharp.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: DBB58522-F816-4A8C-96A5-E0236F16A5C6
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
ms.openlocfilehash: 6c066f89dc8f558a9154138bf38ad4326fe21291
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642528"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>L'accesso ai bit di pixel bitmap SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Come illustrato nell'articolo [ **SkiaSharp salvataggio di bitmap per i file**](saving.md), generalmente le bitmap vengono archiviate nei file in un formato compresso, come JPEG o PNG. Contrariamente, una bitmap di SkiaSharp archiviata nella memoria non viene compresso. Sono archiviati come una serie sequenziale di pixel. Questo formato non compresso facilita il trasferimento delle bitmap a una superficie di visualizzazione.

Il blocco di memoria occupato da una bitmap SkiaSharp è organizzato in modo molto semplice: Inizia con la prima riga di pixel, da sinistra a destra, quindi continua con la seconda riga. Per le bitmap a colori, ogni pixel costituita da quattro byte, il che significa che lo spazio di memoria totale necessario per la bitmap è quattro volte il prodotto di larghezza e altezza.

Questo articolo descrive come un'applicazione può ottenere l'accesso a tali pixel, direttamente tramite l'accesso a blocco di memoria della bitmap in pixel, o indirettamente. In alcuni casi, potrebbe essere necessario un programma analizzare i pixel di un'immagine e creare un istogramma di qualche tipo. Più comunemente, le applicazioni possono costruire immagini univoche creando modo algoritmico i pixel che compongono la bitmap:

![Esempi di bit di pixel](pixel-bits-images/PixelBitsSample.png "Pixel bit di esempio")

## <a name="the-techniques"></a>Le tecniche

SkiaSharp offre diverse tecniche per l'accesso ai bit di pixel della bitmap. Quello che si sceglie in genere è un compromesso tra la codifica di comodità (che è correlata alla manutenzione e la facilità di debug) e le prestazioni. Nella maggior parte dei casi, si userà uno dei seguenti metodi e proprietà di `SKBitmap` per l'accesso ai pixel della bitmap:

- Il `GetPixel` e `SetPixel` metodi consentono di ottenere o impostare il colore di un singolo pixel.
- Il `Pixels` proprietà ottiene una matrice di colori dei pixel per l'intera bitmap, o imposta la matrice di colori.
- `GetPixels` Restituisce l'indirizzo della memoria pixel utilizzata per la bitmap.
- `SetPixels` sostituisce l'indirizzo della memoria pixel utilizzata per la bitmap.

È possibile considerare le prime due tecniche come "generale" e i due secondi come "livello basso." Esistono alcuni altri metodi e proprietà che è possibile usare, ma questi sono i più importanti.

Consente di visualizzare le differenze di prestazioni tra queste tecniche, il [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) applicazione contiene una pagina denominata **Bitmap sfumatura** che Crea una bitmap con pixel che combinano le sfumature di colore rosso e blue per creare una sfumatura. Il programma consente di creare otto copie diverse di questa bitmap, tutti con tecniche diverse per l'impostazione di pixel della bitmap. Ognuno di tali otto bitmap viene creato in un metodo separato che imposta una breve descrizione della tecnica e calcola il tempo necessario per impostare tutti i pixel. Ogni metodo esegue il ciclo attraverso la logica di impostazione di pixel 100 volte per ottenere una stima più accurata delle prestazioni.

### <a name="the-setpixel-method"></a>Il metodo SetPixel

Se è sufficiente impostare o ottenere singoli pixel diverse, il [ `SetPixel` ](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) e [ `GetPixel` ](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) metodi sono ideali. Per ognuno di questi due metodi, specificare la colonna di tipo integer e riga. Indipendentemente dal formato pixel, questi due metodi consentono di ottenere o impostare il numero di pixel come un `SKColor` valore:

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

Il `col` argomento deve essere compresa tra 0 e uno minore del `Width` proprietà bitmap e `row` compreso tra 0 e uno minore del `Height` proprietà.

Ecco il metodo **Bitmap sfumatura** che imposta il contenuto di un bitmap utilizzando il `SetPixel` (metodo). La bitmap è 256 x 256 pixel e il `for` i cicli sono impostate come hardcoded con l'intervallo di valori:

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

Il set di colori per ogni pixel ha un componente rossa uguale alla colonna di bitmap e un componente blu uguale alla riga. La bitmap risultante è nera in alto a sinistra, rosso in alto a destra, blu in basso a sinistra e magenta in basso a destra, con sfumature altrove.

Il `SetPixel` metodo è chiamato 65.536 volte e indipendentemente dal fatto che come efficiente questo metodo potrebbe essere, in genere non è una buona idea eseguire che molte chiamate di API se è disponibile un'alternativa. Per fortuna, sono disponibili numerose alternative.

### <a name="the-pixels-property"></a>La proprietà di pixel

`SKBitmap` definisce un [ `Pixels` ](xref:SkiaSharp.SKBitmap.Pixels) proprietà che restituisce una matrice di `SKColor` i valori per l'intera bitmap. È anche possibile usare `Pixels` per impostare una matrice di valori di colore per la bitmap:

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

I pixel vengono disposti nella matrice a partire dalla prima riga, da sinistra a destra, quindi la seconda riga e così via. Il numero totale di colori nella matrice è uguale al prodotto di bitmap larghezza e altezza.

Anche se questa proprietà viene visualizzata sia efficiente, tenere presente che i pixel vengono copiati dalla bitmap nella matrice e dalla matrice in bitmap e i pixel vengono convertiti da e verso `SKColor` valori.

Ecco il metodo `GradientBitmapPage` classe che consente di impostare la mappa di bit usando la `Pixels` proprietà. Il metodo alloca un' `SKColor` matrice di dimensioni necessarie, ma sarebbe possibile utilizzare il `Pixels` proprietà per creare tale matrice:

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

Si noti che l'indice della `pixels` deve essere calcolato dalla matrice di `row` e `col` variabili. La riga viene moltiplicata per il numero di pixel in ogni riga (256 in questo caso) e quindi la colonna viene aggiunta.

`SKBitmap` definisce inoltre una simile `Bytes` proprietà, che restituisce una matrice di byte per l'intera bitmap, ma è più complessa per le bitmap a colori.

### <a name="the-getpixels-pointer"></a>Il puntatore GetPixels

Potenzialmente è la tecnica più potente per accedere ai pixel della bitmap [ `GetPixels` ](xref:SkiaSharp.SKBitmap.GetPixels), non deve essere confusa con il `GetPixel` metodo o il `Pixels` proprietà. Si noterà immediatamente una differenza con `GetPixels` in quanto restituisce un elemento non è molto comune nella programmazione c#:

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

.NET [ `IntPtr` ](xref:System.IntPtr) tipo rappresenta un puntatore. Viene chiamato `IntPtr` perché è la lunghezza di un intero nel processore nativo del computer in cui il programma viene eseguito, in genere a 32 bit o 64 bit di lunghezza. Il `IntPtr` che `GetPixels` restituisce è l'indirizzo del blocco di memoria usata per archiviare i pixel dell'oggetto bitmap effettivo.

È possibile convertire le `IntPtr` in un linguaggio c# puntatore tipo usando il [ `ToPointer` ](xref:System.IntPtr.ToPointer) (metodo). La sintassi di puntatore in c# è lo stesso come C e C++:

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

Il `ptr` variabile è di tipo _puntatore ai byte_. Ciò `ptr` variabile consente di accedere ai singoli byte di memoria che vengono usati per archiviare i pixel della bitmap. Per leggere un byte la memoria o scrivere un byte per la memoria è usare codice simile al seguente:

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

In questo contesto, l'asterisco è il linguaggio c# _operatore di riferimento indiretto_ e viene usato per fare riferimento al contenuto della memoria a cui punta `ptr`. Inizialmente `ptr` punta al primo byte del pixel prima della prima riga della bitmap, ma è possibile eseguire operazioni aritmetiche sul `ptr` variabile per spostarlo in altre posizioni all'interno della bitmap.

Uno svantaggio è che è possibile usare ciò `ptr` variabile solo in un blocco di codice contrassegnato con il `unsafe` (parola chiave). Inoltre, l'assembly deve essere contrassegnato in modo che blocchi unsafe. Questa operazione viene eseguita nelle proprietà del progetto.

Tramite i puntatori in c# è molto potente, ma anche molto pericolose. È necessario assicurarsi che sia non accedere memoria oltre ciò che dovrebbe per fare riferimento il puntatore del mouse. Si tratta di uno dei motivi per usare puntatore associato con la parola "unsafe".

Ecco il metodo `GradientBitmapPage` classe che utilizza il `GetPixels` (metodo). Si noti che il `unsafe` blocco che comprende tutto il codice usando il puntatore ai byte:

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

Quando la `ptr` variabile prima di tutto viene ottenuta dal `ToPointer` (metodo), punta al primo byte del pixel più a sinistra della prima riga della bitmap. Il `for` loop per `row` e `col` sono impostati in modo che `ptr` può essere incrementato con la `++` operatore dopo ogni byte di ogni pixel è impostato. Per le altre 99 scorre in ciclo i pixel, di `ptr` deve essere impostata torna all'inizio della bitmap.

Ogni pixel è quattro byte di memoria, pertanto è necessario impostare separatamente ogni byte. Qui il codice si presuppone che i byte sono in rosso ordine, verde, blu e alfa, che è coerente con il `SKColorType.Rgba8888` tipo dei colori. Si ricorderà che questo è il tipo di colore predefinito per iOS e Android, ma non per la piattaforma Windows universale. Per impostazione predefinita, la piattaforma UWP crea le bitmap con il `SKColorType.Bgra8888` tipo dei colori. Per questo motivo, prevede di visualizzare alcuni risultati diversi che utilizzano tale piattaforma.

È possibile eseguire il cast del valore restituito da `ToPointer` a un `uint` puntatore anziché un `byte` puntatore. In questo modo un pixel intero accessibile in un'unica istruzione. Applicando la `++` operatore da tale puntatore aumenta in modo da quattro byte in modo che punti al pixel Avanti:

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

Il pixel è impostato tramite la `MakePixel` metodo che costruisce un pixel intero dai componenti rossi, verdi, blu e alfa. Tenere presente che il `SKColorType.Rgba8888` formato ha un byte pixel ordinamento simile al seguente:

RR GG, BB AA

Ma il numero intero che corrisponde a tali byte è:

AABBGGRR

Il byte meno significativo dell'intero archiviato per primo in conformità con architettura little-endian. Ciò `MakePixel` metodo non funziona correttamente per le bitmap con il `Bgra8888` tipo dei colori.

Il `MakePixel` metodo è contrassegnato con il [ `MethodImplOptions.AggressiveInlining` ](xref:System.Runtime.CompilerServices.MethodImplOptions) opzione a incoraggiare al compilatore di evitare rendendo questo un metodo separato, ma per compilare il codice in cui viene chiamato il metodo. Questa vista migliora le prestazioni.

È interessante notare che il `SKColor` struttura definisce una conversione esplicita dal `SKColor` in un intero senza segno, il che significa che un `SKColor` valore può essere creato e una conversione `uint` può essere usato al posto di `MakePixel`:

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

L'unica domanda è la seguente: È il formato intero del `SKColor` valore nell'ordine `SKColorType.Rgba8888` del tipo di colore o del `SKColorType.Bgra8888` tipo di colore oppure è interamente un altro? La risposta alla domanda dovrà essere rivelata al più presto.

### <a name="the-setpixels-method"></a>Metodo SetPixels

`SKBitmap` definisce anche un metodo denominato [ `SetPixels` ](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)), che viene chiamato come segue:

```csharp
bitmap.SetPixels(intPtr);
```

È importante ricordare che `GetPixels` Ottiene un `IntPtr` che fa riferimento il blocco di memoria utilizzato da bitmap per archiviare i pixel. Il `SetPixels` chiamare _sostituisce_ tale blocco di memoria con il blocco di memoria a cui fanno riferimento le `IntPtr` specificato come il `SetPixels` argomento. La bitmap di quindi libera il blocco di memoria che utilizzava in precedenza. La volta successiva `GetPixels` viene chiamato, ottiene il blocco di memoria impostato con `SetPixels`.

Inizialmente, può sembrare come se `SetPixels` ti offre non sono più potenza e prestazioni rispetto a `GetPixels` pur essendo meno utile. Con `GetPixels` è ottenere il blocco di memoria della bitmap e accedervi. Con `SetPixels` è possibile allocare e accedere a parte della memoria e quindi impostare questo valore come il blocco di memoria della bitmap.

Tuttavia, `SetPixels` l'utilizzo di offre un vantaggio sintattico distinto: Consente di accedere ai bit di pixel bitmap usando una matrice. Ecco il metodo `GradientBitmapPage` che illustra questa tecnica. Prima di tutto, il metodo definisce una matrice di byte multidimensionale corrispondenti ai byte di pixel della bitmap. La prima dimensione è la riga, la seconda dimensione è la colonna e corrisponde la terza dimensione a quattro componenti di ogni pixel:

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

Quindi, dopo che la matrice è stata compilata con pixel, un `unsafe` blocco e una `fixed` istruzione consente di ottenere un puntatore ai byte che fa riferimento a questa matrice. Tale puntatore ai byte può quindi essere convertito in un `IntPtr` da passare al `SetPixels`.

La matrice creata non deve essere una matrice di byte. Può essere una matrice di interi con solo due dimensioni per la colonna e riga:

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

Il `MakePixel` metodo anche in questo caso viene usato per combinare i componenti di colore in un pixel a 32 bit.

Solo per motivi di completezza, ecco lo stesso codice, ma con un `SKColor` cast a un intero senza segno:

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

Il costruttore del **colore sfumato** pagina chiama tutte le otto versioni dei metodi illustrati in precedenza e Salva i risultati:

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

Il costruttore si conclude con la creazione di un `SKCanvasView` per visualizzare le bitmap risultante. Il `PaintSurface` gestore divide la superficie in otto rettangoli e le chiamate `Display` visualizzare ciascuna di esse:

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

Per consentire al compilatore di ottimizzare il codice, questa pagina è stata eseguita in **rilascio** modalità. Ecco la pagina in esecuzione in un simulatore di iPhone 8 in un MacBook Pro, un telefono Android di Nexus 5 e Surface Pro 3 che esegue Windows 10. A causa delle differenze di hardware, evitare di confronto tra i tempi di prestazioni tra i dispositivi, ma invece consultare i tempi relativi in ogni dispositivo:

[![Bitmap sfumatura](pixel-bits-images/GradientBitmap.png "Bitmap sfumatura")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

Ecco una tabella che consolida i tempi di esecuzione in millisecondi:

| API       | Tipo di dati | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3.17 |   10.77 | versione 3.49 |
| Pixel    |           | per 0,32 |    1,23 | 0,07 |
| GetPixels | byte      | 0,09 |    0,24 | 0.10 |
|           | uint      | 0,06 |    0,26 | 0,05 |
|           | SKColor   | 0,29 |    0,99 | 0,07 |
| SetPixels | byte      | 1.33 |    6.78 | 0.11 |
|           | uint      | 0,14 |    0.69 | 0,06 |
|           | SKColor   | 0.35 |    1.93 | 0.10 |

Come previsto, la chiamata `SetPixel` volte 65.536 è il modo effeicient minimi per impostare i pixel della bitmap. La compilazione un' `SKColor` matrice e impostando il `Pixels` proprietà è molto meglio e persino Confronta agevolmente con alcune del `GetPixels` e `SetPixels` tecniche. Lavora `uint` valori di pixel è veloce rispetto all'impostazione separato `byte` componenti e la conversione il `SKColor` valore intero senza segno aggiunge overhead per il processo.

È anche interessante confrontare le varie sfumature: Le prime righe di ogni piattaforma sono le stesse e mostrano la sfumatura come previsto. Ciò significa che il `SetPixel` metodo e `Pixels` proprietà creare correttamente pixel da colori indipendentemente dal formato pixel sottostante.

Le due righe successive di iOS e Android schermate sono anche gli stessi, a conferma del fatto che il piccolo `MakePixel` per il valore predefinito è correttamente definito metodo `Rgba8888` formato pixel per queste piattaforme.

La riga nella parte inferiore di iOS e Android schermate con le versioni precedenti, è che indica che l'intero senza segno ottenuto eseguendo il cast di un `SKColor` valore è nel formato:

AARRGGBB

I byte sono nell'ordine:

GG BB RR AA

Questo è il `Bgra8888` ordinamento anziché il `Rgba8888` ordering. Il `Brga8888` formato è il valore predefinito per la piattaforma Windows universale, motivo per cui le sfumature nell'ultima riga di tale schermata sono gli stessi della prima riga. Ma le due righe centrale non sono corrette perché si presuppone che il codice di creazione di tali bitmap un `Rgba8888` ordering.

Se si desidera usare lo stesso codice per l'accesso ai bit di pixel in ogni piattaforma, è possibile creare in modo esplicito un' `SKBitmap` usando il `Rgba8888` o `Bgra8888` formato. Se si desidera eseguire il cast `SKColor` usare i valori per pixel delle bitmap, `Bgra8888`.

## <a name="random-access-of-pixels"></a>Accesso casuale di pixel

Il `FillBitmapBytePtr` e `FillBitmapUintPtr` metodi nel **Bitmap sfumatura** pagina tratto vantaggi da `for` cicli progettati per riempire la bitmap in sequenza, dalla prima riga per riga inferiore e in ogni riga da sinistra a destra. Il pixel è stato possibile impostare con la stessa istruzione il puntatore viene incrementato.

In alcuni casi è necessario accedere i pixel in modo casuale anziché in sequenza. Se si usa il `GetPixels` approccio, è necessario calcolare i puntatori basati sulla riga e colonna. Questa funzionalità viene illustrata la **arcobaleno seno** pagina, che crea una bitmap che mostra un arcobaleno sotto forma di un ciclo di una curva seno.

I colori dell'arcobaleno sono più facili da creare usando il modello di colori HSL (hue, saturation, luminosità). Il `SKColor.FromHsl` metodo crea un `SKColor` valore usando i valori di tonalità compresi tra 0 e 360 (ad esempio gli angoli di un cerchio, ma passa da rosso, verde e blu e nuovamente su rosso) e i valori di saturazione e luminosità compreso tra 0 e 100. Per i colori di un arcobaleno, impostare la saturazione a un massimo di 100 e la luminosità per un punto medio pari a 50.

**Seno arcobaleno** crea l'immagine da scorrendo le righe della bitmap e quindi eseguire i cicli di valori di tonalità 360. Da ogni valore di tonalità, e calcola una colonna mappa di bit che si basa inoltre su un valore del seno:

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

Si noti che il costruttore crea la mappa di bit in base il `SKColorType.Bgra8888` formato:

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

Ciò consente al programma di usare la conversione di `SKColor` i valori in `uint` pixel estrema semplicità. Anche se non svolgono un ruolo in questo programma specifico, ogni volta che utilizza il `SKColor` conversione impostare pixel, è necessario specificare anche `SKAlphaType.Unpremul` perché `SKColor` non premoltiplicare relativi componenti di colore per il valore alfa.

Il costruttore utilizza quindi il `GetPixels` metodo per ottenere un puntatore al primo pixel della bitmap:

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

Per qualsiasi particolare riga e colonna, un valore di offset deve essere aggiunto a `basePtr`. Questo offset è la riga moltiplicata per la larghezza della bitmap, nonché la colonna:

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

Il `SKColor` viene archiviato in memoria con il puntatore ' this ':

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

Nel `PaintSurface` gestore del `SKCanvasView`, la bitmap viene adattata per riempire l'area di visualizzazione:

[![Seno arcobaleno](pixel-bits-images/RainbowSine.png "arcobaleno seno")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>Da una singola bitmap a un'altra

Un numero molto elevato le attività di elaborazione di immagini includere la modifica pixel come vengono trasferiti da una singola bitmap a altro. Questa tecnica è dimostrata nel **regolazione del colore** pagina. La pagina viene caricata una delle risorse di bitmap e quindi consente di modificare l'immagine utilizzando tre `Slider` viste:

[![La regolazione dei colori](pixel-bits-images/ColorAdjustment.png "la regolazione dei colori")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

Per ogni colore del pixel, il primo `Slider` aggiunge un valore compreso tra 0 e 360, di tonalità, ma usa quindi l'operatore per mantenere il risultato compreso tra 0 e 360, modulo shifting in modo efficace i colori nell'ambito (come illustrato nella schermata UWP). La seconda `Slider` consente di scegliere un fattore di moltiplicazione tra lo 0,5 e 2 da applicare alla saturazione e il terzo `Slider` esegue la stessa operazione per la luminosità, come illustrato nello screenshot di Android.

Il programma mantiene due bitmap, bitmap di origine denominata `srcBitmap` e la bitmap di destinazione modificato denominato `dstBitmap`. Ogni volta che un `Slider` viene spostato, viene calcolato automaticamente tutti i pixel nuovo `dstBitmap`. Naturalmente, gli utenti verranno utilizzate spostando il `Slider` viste molto rapidamente, in modo che si desiderano ottenere prestazioni ottimali è possibile gestire. Questa operazione comporta il `GetPixels` metodo per la bitmap di origine e di destinazione.

Il **regolazione del colore** pagina non di controllare il formato di colore delle bitmap di origine e destinazione. Contiene invece leggermente diverso per la logica per `SKColorType.Rgba8888` e `SKColorType.Bgra8888` formati. L'origine e destinazione possono essere formati diversi, e il programma continuerà a funzionare.

Ecco il programma, ad eccezione di essenziale `TransferPixels` metodo che consente di trasferire i pixel formano l'origine alla destinazione. Il costruttore imposta `dstBitmap` uguale a `srcBitmap`. Il `PaintSurface` gestore visualizza `dstBitmap`:

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

Il `ValueChanged` gestore per il `Slider` viste calcola i valori di regolazione e chiamate `TransferPixels`.

L'intera `TransferPixels` metodo è contrassegnato come `unsafe`. Inizia ottenendo i puntatori di byte per i bit di pixel delle bitmap entrambi e quindi scorre in ciclo tutte le righe e colonne. Dalla bitmap di origine, il metodo ottiene quattro byte per ogni pixel. Questi potrebbero essere in entrambi i `Rgba8888` o `Bgra8888` ordine. Verifica per il tipo di colore consente un `SKColor` valore deve essere creato. I componenti HSL sono quindi estratti, modificati e utilizzati per ricreare il `SKColor` valore. A seconda che la bitmap di destinazione sia `Rgba8888` o `Bgra8888`, i byte vengono archiviati in bitmp la destinazione:

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

È probabile che le prestazioni di questo metodo può essere ulteriormente migliorati tramite la creazione di metodi separati per le varie combinazioni di tipi di colore delle bitmap di origine e di destinazione ed evitare il controllo del tipo per ogni pixel. Un'altra opzione consiste nell'utilizzare più `for` loop per la `col` variabile basata sul tipo di colore.

## <a name="posterization"></a>Posterizzazione

È un altro processo comune che prevede l'accesso ai bit di pixel _posterizzazione_. Il numero se colori codificata in pixel della bitmap viene ridotto in modo che il risultato è simile a un poster mano utilizzando una tavolozza dei colori limitato.

Il **Posterizza** pagina esegue questo processo su una delle immagini monkey:

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

Il codice nel costruttore accede a ogni pixel, esegue un'operazione con AND bit per bit con il valore 0xE0E0E0FF e quindi archivia il risultato nella bitmap. I valori 0xE0E0E0FF mantiene 3 bit più significativi di ogni componente del colore e imposta i bit più bassi 5 su 0. Anziché 2<sup>24</sup> o 16.777.216 colori, la bitmap viene ridotto a 2<sup>9</sup> o 512 colori:

[![Posterizzazione](pixel-bits-images/Posterize.png "posterizzazione")](pixel-bits-images/posterizzazione-Large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
