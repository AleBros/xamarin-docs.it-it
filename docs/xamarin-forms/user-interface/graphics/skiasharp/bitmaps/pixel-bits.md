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
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305360"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>L'accesso ai bit di pixel bitmap SkiaSharp

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Come illustrato nell'articolo [**salvataggio di bitmap SkiaSharp in file**](saving.md), le bitmap vengono in genere archiviate in file in formato compresso, ad esempio JPEG o png. Contrariamente, una bitmap di SkiaSharp archiviata nella memoria non viene compresso. Sono archiviati come una serie sequenziale di pixel. Questo formato non compresso facilita il trasferimento delle bitmap a una superficie di visualizzazione.

Il blocco di memoria occupato da una bitmap di SkiaSharp è organizzato in modo molto semplice: inizia con la prima riga del pixel, da sinistra a destra e continua quindi con la seconda riga. Per le bitmap a colori, ogni pixel costituita da quattro byte, il che significa che lo spazio di memoria totale necessario per la bitmap è quattro volte il prodotto di larghezza e altezza.

Questo articolo descrive come un'applicazione può ottenere l'accesso a tali pixel, direttamente tramite l'accesso a blocco di memoria della bitmap in pixel, o indirettamente. In alcuni casi, potrebbe essere necessario un programma analizzare i pixel di un'immagine e creare un istogramma di qualche tipo. Più comunemente, le applicazioni possono costruire immagini univoche creando modo algoritmico i pixel che compongono la bitmap:

![Esempi di bit pixel](pixel-bits-images/PixelBitsSample.png "Esempio di bit di pixel")

## <a name="the-techniques"></a>Le tecniche

SkiaSharp offre diverse tecniche per l'accesso ai bit di pixel della bitmap. Quello che si sceglie in genere è un compromesso tra la codifica di comodità (che è correlata alla manutenzione e la facilità di debug) e le prestazioni. Nella maggior parte dei casi, si userà uno dei seguenti metodi e proprietà di `SKBitmap` per accedere ai pixel della bitmap:

- I metodi `GetPixel` e `SetPixel` consentono di ottenere o impostare il colore di un singolo pixel.
- La proprietà `Pixels` ottiene una matrice di colori pixel per l'intera bitmap o imposta la matrice di colori.
- `GetPixels` restituisce l'indirizzo della memoria pixel utilizzata dalla bitmap.
- `SetPixels` sostituisce l'indirizzo della memoria pixel utilizzata dalla bitmap.

È possibile considerare le prime due tecniche come "generale" e i due secondi come "livello basso." Esistono alcuni altri metodi e proprietà che è possibile usare, ma questi sono i più importanti.

Per consentire all'utente di visualizzare le differenze di prestazioni tra queste tecniche, l'applicazione [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contiene una pagina denominata **bitmap con sfumature** che consente di creare una bitmap con pixel che combinano tonalità rosse e blu per creare una sfumatura. Il programma consente di creare otto copie diverse di questa bitmap, tutti con tecniche diverse per l'impostazione di pixel della bitmap. Ognuno di tali otto bitmap viene creato in un metodo separato che imposta una breve descrizione della tecnica e calcola il tempo necessario per impostare tutti i pixel. Ogni metodo esegue il ciclo attraverso la logica di impostazione di pixel 100 volte per ottenere una stima più accurata delle prestazioni.

### <a name="the-setpixel-method"></a>Il metodo SetPixel

Se è necessario impostare o ottenere solo diversi pixel, i metodi [`SetPixel`](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) e [`GetPixel`](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) sono ideali. Per ognuno di questi due metodi, specificare la colonna di tipo integer e riga. Indipendentemente dal formato pixel, questi due metodi consentono di ottenere o impostare il pixel come valore di `SKColor`:

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

Il `col` argomento deve essere compreso tra 0 e uno minore della proprietà `Width` della bitmap e `row` intervallo compreso tra 0 e uno minore della proprietà `Height`.

Ecco il metodo nella **bitmap sfumatura** che imposta il contenuto di una bitmap usando il metodo `SetPixel`. La bitmap è 256 di 256 pixel e i cicli `for` sono hardcoded con l'intervallo di valori:

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

Il metodo `SetPixel` è denominato 65.536 volte e indipendentemente dal livello di efficienza di questo metodo, in genere non è consigliabile fare in modo che molte chiamate API siano disponibili in alternativa. Per fortuna, sono disponibili numerose alternative.

### <a name="the-pixels-property"></a>La proprietà di pixel

`SKBitmap` definisce una proprietà [`Pixels`](xref:SkiaSharp.SKBitmap.Pixels) che restituisce una matrice di valori `SKColor` per l'intera bitmap. È anche possibile usare `Pixels` per impostare una matrice di valori di colore per la bitmap:

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

I pixel vengono disposti nella matrice a partire dalla prima riga, da sinistra a destra, quindi la seconda riga e così via. Il numero totale di colori nella matrice è uguale al prodotto di bitmap larghezza e altezza.

Sebbene questa proprietà risulti efficiente, tenere presente che i pixel vengono copiati dalla bitmap nella matrice e dalla matrice di nuovo nella bitmap e i pixel vengono convertiti da e in `SKColor` valori.

Ecco il metodo nella classe `GradientBitmapPage` che imposta la bitmap usando la proprietà `Pixels`. Il metodo alloca una matrice di `SKColor` della dimensione richiesta, ma potrebbe aver usato la proprietà `Pixels` per creare la matrice:

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

Si noti che l'indice della matrice di `pixels` deve essere calcolato dalle variabili `row` e `col`. La riga viene moltiplicata per il numero di pixel in ogni riga (256 in questo caso) e quindi la colonna viene aggiunta.

`SKBitmap` definisce anche una proprietà di `Bytes` simile, che restituisce una matrice di byte per l'intera bitmap, ma è più complessa per le bitmap a colori completi.

### <a name="the-getpixels-pointer"></a>Il puntatore GetPixels

Potenzialmente la tecnica più potente per accedere ai pixel bitmap è [`GetPixels`](xref:SkiaSharp.SKBitmap.GetPixels), da non confondere con il metodo `GetPixel` o la proprietà `Pixels`. Si noterà immediatamente una differenza con `GetPixels` in quanto restituisce un elemento non molto comune nella C# programmazione:

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

Il tipo di [`IntPtr`](xref:System.IntPtr) .NET rappresenta un puntatore. Viene chiamato `IntPtr` perché è la lunghezza di un numero intero nel processore nativo del computer in cui viene eseguito il programma, in genere 32 bit o 64 bit. Il `IntPtr` restituito da `GetPixels` è l'indirizzo del blocco di memoria effettivo usato dall'oggetto bitmap per archiviare i pixel.

È possibile convertire il `IntPtr` in un C# tipo di puntatore usando il metodo [`ToPointer`](xref:System.IntPtr.ToPointer) . La sintassi di puntatore in c# è lo stesso come C e C++:

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

Il `ptr` variabile è di tipo _byte Pointer_. Questa `ptr` variabile consente di accedere ai singoli byte di memoria utilizzati per archiviare i pixel della bitmap. Per leggere un byte la memoria o scrivere un byte per la memoria è usare codice simile al seguente:

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

In questo contesto, l'asterisco è l' C# _operatore di riferimento indiretto_ e viene usato per fare riferimento al contenuto della memoria a cui punta `ptr`. Inizialmente, `ptr` punta al primo byte del primo pixel della prima riga della bitmap, ma è possibile eseguire operazioni aritmetiche sulla variabile `ptr` per spostarla in altre posizioni all'interno della bitmap.

Uno svantaggio è che è possibile usare questa `ptr` variabile solo in un blocco di codice contrassegnato con la parola chiave `unsafe`. Inoltre, l'assembly deve essere contrassegnato in modo che blocchi unsafe. Questa operazione viene eseguita nelle proprietà del progetto.

Tramite i puntatori in c# è molto potente, ma anche molto pericolose. È necessario assicurarsi che sia non accedere memoria oltre ciò che dovrebbe per fare riferimento il puntatore del mouse. Si tratta di uno dei motivi per usare puntatore associato con la parola "unsafe".

Ecco il metodo nella classe `GradientBitmapPage` che usa il metodo `GetPixels`. Si noti il blocco `unsafe` che include tutto il codice utilizzando il puntatore byte:

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

Quando la variabile `ptr` viene ottenuta per la prima volta dal metodo `ToPointer`, punta al primo byte del pixel più a sinistra della prima riga della bitmap. I cicli di `for` per `row` e `col` sono configurati in modo che il `ptr` possa essere incrementato con l'operatore `++` dopo che è stato impostato ogni byte di ogni pixel. Per gli altri 99 cicli attraverso i pixel, il `ptr` deve essere impostato di nuovo all'inizio della bitmap.

Ogni pixel è quattro byte di memoria, pertanto è necessario impostare separatamente ogni byte. Il codice si basa sul presupposto che i byte siano in ordine rosso, verde, blu e alfa, coerente con il tipo di colore `SKColorType.Rgba8888`. Si ricorderà che questo è il tipo di colore predefinito per iOS e Android, ma non per la piattaforma Windows universale. Per impostazione predefinita, UWP crea bitmap con il tipo di colore `SKColorType.Bgra8888`. Per questo motivo, prevede di visualizzare alcuni risultati diversi che utilizzano tale piattaforma.

È possibile eseguire il cast del valore restituito da `ToPointer` a un puntatore `uint` invece che a un puntatore di `byte`. In questo modo un pixel intero accessibile in un'unica istruzione. Se si applica l'operatore `++` a tale puntatore, questo viene incrementato di quattro byte per puntare al pixel successivo:

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

Il pixel viene impostato usando il metodo `MakePixel`, che costruisce un pixel Integer dai componenti rosso, verde, blu e alfa. Tenere presente che il formato di `SKColorType.Rgba8888` ha un ordine di byte in pixel simile al seguente:

RR GG, BB AA

Ma il numero intero che corrisponde a tali byte è:

AABBGGRR

Il byte meno significativo dell'intero archiviato per primo in conformità con architettura little-endian. Questo metodo di `MakePixel` non funzionerà correttamente per le bitmap con il tipo di colore `Bgra8888`.

Il metodo `MakePixel` viene contrassegnato con l'opzione [`MethodImplOptions.AggressiveInlining`](xref:System.Runtime.CompilerServices.MethodImplOptions) per invitare il compilatore a evitare di creare questo metodo separato, ma di compilare invece il codice in cui viene chiamato il metodo. Questa vista migliora le prestazioni.

È interessante notare che la struttura `SKColor` definisce una conversione esplicita da `SKColor` a una Unsigned Integer, il che significa che è possibile creare un valore di `SKColor` ed è possibile utilizzare una conversione a `uint` anziché `MakePixel`:

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

L'unica domanda è la seguente: è il formato integer del valore `SKColor` nell'ordine del tipo di colore `SKColorType.Rgba8888`, o il tipo di colore `SKColorType.Bgra8888` oppure è un altro elemento interamente? La risposta alla domanda dovrà essere rivelata al più presto.

### <a name="the-setpixels-method"></a>Metodo SetPixels

`SKBitmap` definisce anche un metodo denominato [`SetPixels`](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)), che viene chiamato come segue:

```csharp
bitmap.SetPixels(intPtr);
```

Si ricordi che `GetPixels` ottiene un `IntPtr` che fa riferimento al blocco di memoria usato dalla bitmap per archiviare i pixel. La chiamata `SetPixels` _sostituisce_ il blocco di memoria con il blocco di memoria a cui fa riferimento il `IntPtr` specificato come argomento `SetPixels`. La bitmap di quindi libera il blocco di memoria che utilizzava in precedenza. La volta successiva che viene chiamato `GetPixels`, ottiene il blocco di memoria impostato con `SetPixels`.

In primo luogo, sembra che `SetPixels` non fornisca più potenza e prestazioni rispetto a `GetPixels` con una minore praticità. Con `GetPixels` è possibile ottenere il blocco di memoria bitmap e accedervi. Con `SetPixels` è possibile allocare e accedere a una certa memoria, quindi impostarla come blocco di memoria bitmap.

Tuttavia, l'uso di `SetPixels` offre un vantaggio sintattico distinto: consente di accedere ai bit di pixel bitmap usando una matrice. Ecco il metodo in `GradientBitmapPage` che illustra questa tecnica. Prima di tutto, il metodo definisce una matrice di byte multidimensionale corrispondenti ai byte di pixel della bitmap. La prima dimensione è la riga, la seconda dimensione è la colonna e corrisponde la terza dimensione a quattro componenti di ogni pixel:

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

Quindi, dopo che la matrice è stata riempita con pixel, viene usato un blocco `unsafe` e un'istruzione `fixed` per ottenere un puntatore di byte che punta a questa matrice. È possibile eseguire il cast di tale puntatore a un `IntPtr` per passare al `SetPixels`.

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

Il metodo `MakePixel` viene usato di nuovo per combinare i componenti di colore in un pixel a 32 bit.

Per completezza, di seguito è riportato lo stesso codice, ma con un `SKColor` valore di cui è stato eseguito il cast a una Unsigned Integer:

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

Il costruttore termina creando un `SKCanvasView` per visualizzare le bitmap risultanti. Il gestore `PaintSurface` divide la relativa superficie in otto rettangoli e chiama `Display` per visualizzare ciascuno di essi:

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

Per consentire al compilatore di ottimizzare il codice, questa pagina è stata eseguita in modalità di **rilascio** . Ecco la pagina in esecuzione in un simulatore di iPhone 8 in un MacBook Pro, un telefono Android di Nexus 5 e Surface Pro 3 che esegue Windows 10. A causa delle differenze di hardware, evitare di confronto tra i tempi di prestazioni tra i dispositivi, ma invece consultare i tempi relativi in ogni dispositivo:

[![Bitmap sfumatura](pixel-bits-images/GradientBitmap.png "Bitmap sfumatura")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

Ecco una tabella che consolida i tempi di esecuzione in millisecondi:

| API       | Tipo di dati | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3,17 |   10.77 | 3.49 |
| Pixel    |           | 0,32 |    1.23 | 0,07 |
| GetPixels | byte      | 0,09 |    0,24 | 0,10 |
|           | uint      | 0.06 |    0,26 | 0.05 |
|           | SKColor   | 0,29 |    0,99 | 0,07 |
| SetPixels | byte      | 1.33 |    6.78 | 0,11 |
|           | uint      | 0,14 |    0,69 | 0.06 |
|           | SKColor   | 0,35 |    1,93 | 0,10 |

Come previsto, la chiamata di `SetPixel` 65.536 volte è il modo meno effeicient per impostare i pixel di una bitmap. Il riempimento di una matrice di `SKColor` e l'impostazione della proprietà `Pixels` sono molto migliori e anche il confronto con alcune delle tecniche di `GetPixels` e `SetPixels`. L'utilizzo dei valori di `uint` pixel è in genere più veloce rispetto all'impostazione di componenti `byte` separati e la conversione del valore `SKColor` in un Unsigned Integer aggiunge un sovraccarico al processo.

È interessante confrontare le sfumature diverse: le prime righe di ogni piattaforma sono uguali e mostrare la sfumatura nel modo previsto. Ciò significa che il metodo `SetPixel` e la proprietà `Pixels` creano correttamente pixel dai colori indipendentemente dal formato pixel sottostante.

Anche le due righe successive degli screenshot iOS e Android sono le stesse, che confermano che il piccolo metodo di `MakePixel` è definito correttamente per il formato di `Rgba8888` pixel predefinito per queste piattaforme.

La riga inferiore degli screenshot iOS e Android è indietro, che indica che la Unsigned Integer ottenuta eseguendo il cast di un valore `SKColor` è nel formato seguente:

AARRGGBB

I byte sono nell'ordine:

GG BB RR AA

Si tratta dell'ordinamento `Bgra8888` anziché dell'ordine di `Rgba8888`. Il formato `Brga8888` è l'impostazione predefinita per la piattaforma Windows universale, motivo per cui le sfumature nell'ultima riga dello screenshot corrispondono alla prima riga. Tuttavia, le due righe centrali non sono corrette perché il codice che crea tali bitmap presuppone un `Rgba8888` ordinamento.

Se si vuole usare lo stesso codice per accedere a bit di pixel in ogni piattaforma, è possibile creare in modo esplicito un `SKBitmap` usando il formato `Rgba8888` o `Bgra8888`. Se si desidera eseguire il cast dei valori `SKColor` ai pixel bitmap, utilizzare `Bgra8888`.

## <a name="random-access-of-pixels"></a>Accesso casuale di pixel

I metodi `FillBitmapBytePtr` e `FillBitmapUintPtr` nella pagina **bitmap sfumatura** traggono vantaggio dai cicli `for` progettati per riempire la bitmap in sequenza, dalla riga superiore alla riga inferiore e in ogni riga da sinistra a destra. Il pixel è stato possibile impostare con la stessa istruzione il puntatore viene incrementato.

In alcuni casi è necessario accedere i pixel in modo casuale anziché in sequenza. Se si usa l'approccio `GetPixels`, sarà necessario calcolare i puntatori in base alla riga e alla colonna. Questa operazione viene illustrata nella pagina del **seno arcobaleno** , che consente di creare una bitmap che mostra un arcobaleno sotto forma di un ciclo di una curva seno.

I colori dell'arcobaleno sono più facili da creare usando il modello di colori HSL (hue, saturation, luminosità). Il metodo `SKColor.FromHsl` crea un valore `SKColor` usando valori di tonalità compresi tra 0 e 360 (ad esempio, gli angoli di un cerchio, ma passando da rosso, verde e blu e viceversa), e valori di saturazione e luminosità compresi tra 0 e 100. Per i colori di un arcobaleno, impostare la saturazione a un massimo di 100 e la luminosità per un punto medio pari a 50.

**Rainbow sine** crea questa immagine eseguendo il ciclo delle righe della bitmap e quindi scorrendo i valori di tonalità 360. Da ogni valore di tonalità, e calcola una colonna mappa di bit che si basa inoltre su un valore del seno:

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

Si noti che il costruttore crea la bitmap in base al formato `SKColorType.Bgra8888`:

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

Ciò consente al programma di utilizzare la conversione dei valori `SKColor` in `uint` pixel senza preoccuparsi. Sebbene non svolga un ruolo in questo particolare programma, ogni volta che si usa la conversione `SKColor` per impostare i pixel, è necessario specificare anche `SKAlphaType.Unpremul` perché `SKColor` non premoltiplica i componenti di colore per il valore alfa.

Il costruttore usa quindi il metodo `GetPixels` per ottenere un puntatore al primo pixel della bitmap:

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

Per ogni riga e colonna specifica, è necessario aggiungere un valore di offset a `basePtr`. Questo offset è la riga moltiplicata per la larghezza della bitmap, nonché la colonna:

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

Il valore `SKColor` viene archiviato in memoria utilizzando questo puntatore:

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

Nel gestore `PaintSurface` della `SKCanvasView`, la bitmap viene estesa in modo da riempire l'area di visualizzazione:

[![Seno arcobaleno](pixel-bits-images/RainbowSine.png "Seno arcobaleno")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>Da una singola bitmap a un'altra

Un numero molto elevato le attività di elaborazione di immagini includere la modifica pixel come vengono trasferiti da una singola bitmap a altro. Questa tecnica è illustrata nella pagina di **regolazione del colore** . La pagina carica una delle risorse bitmap e quindi consente di modificare l'immagine con tre visualizzazioni `Slider`:

[![Regolazione del colore](pixel-bits-images/ColorAdjustment.png "Regolazione del colore")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

Per ogni colore pixel, il primo `Slider` aggiunge un valore da 0 a 360 alla tonalità, ma usa quindi l'operatore modulo per tenere il risultato compreso tra 0 e 360, spostando in modo efficace i colori lungo lo spettro, come illustrato nella schermata UWP. Il secondo `Slider` consente di selezionare un fattore di moltiplicazione compreso tra 0,5 e 2 da applicare alla saturazione e la terza `Slider` esegue la stessa operazione per la luminosità, come illustrato nello screenshot di Android.

Il programma gestisce due bitmap, la bitmap di origine originale denominata `srcBitmap` e la bitmap di destinazione modificata denominata `dstBitmap`. Ogni volta che viene spostato un `Slider`, il programma calcola tutti i nuovi pixel `dstBitmap`. Naturalmente, gli utenti sperimentano spostando rapidamente le visualizzazioni `Slider`, quindi si desiderano le migliori prestazioni che è possibile gestire. Questo implica il metodo `GetPixels` per le bitmap di origine e di destinazione.

La pagina di **regolazione del colore** non controlla il formato dei colori delle bitmap di origine e di destinazione. Contiene invece una logica leggermente diversa per i formati `SKColorType.Rgba8888` e `SKColorType.Bgra8888`. L'origine e destinazione possono essere formati diversi, e il programma continuerà a funzionare.

Ecco il programma, ad eccezione del metodo di `TransferPixels` cruciale che trasferisce i pixel sotto forma di origine alla destinazione. Il costruttore imposta `dstBitmap` uguale a `srcBitmap`. Il gestore `PaintSurface` Visualizza `dstBitmap`:

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

Il gestore `ValueChanged` per le visualizzazioni `Slider` calcola i valori di regolazione e chiama `TransferPixels`.

L'intero `TransferPixels` metodo è contrassegnato come `unsafe`. Inizia ottenendo i puntatori di byte per i bit di pixel delle bitmap entrambi e quindi scorre in ciclo tutte le righe e colonne. Dalla bitmap di origine, il metodo ottiene quattro byte per ogni pixel. Questi possono essere nell'ordine `Rgba8888` o `Bgra8888`. Il controllo del tipo di colore consente la creazione di un valore `SKColor`. I componenti HSL vengono quindi estratti, modificati e usati per ricreare il valore `SKColor`. A seconda che la bitmap di destinazione sia `Rgba8888` o `Bgra8888`, i byte vengono archiviati nella bitmp di destinazione:

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

È probabile che le prestazioni di questo metodo può essere ulteriormente migliorati tramite la creazione di metodi separati per le varie combinazioni di tipi di colore delle bitmap di origine e di destinazione ed evitare il controllo del tipo per ogni pixel. Un'altra opzione consiste nel disporre di più cicli `for` per la variabile `col` in base al tipo di colore.

## <a name="posterization"></a>Posterizzazione

Un altro processo comune che prevede l'accesso a bits pixel è la _posterzzazione_. Il numero se colori codificata in pixel della bitmap viene ridotto in modo che il risultato è simile a un poster mano utilizzando una tavolozza dei colori limitato.

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

Il codice nel costruttore accede a ogni pixel, esegue un'operazione con AND bit per bit con il valore 0xE0E0E0FF e quindi archivia il risultato nella bitmap. I valori 0xE0E0E0FF mantiene 3 bit più significativi di ogni componente del colore e imposta i bit più bassi 5 su 0. Invece di 2<sup>24</sup> o 16.777.216 colori, la bitmap è ridotta a 2<sup>9</sup> o 512 colori:

[![Posterizzazione](pixel-bits-images/Posterize.png "Posterizzazione")](pixel-bits-images/Posterize-Large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
