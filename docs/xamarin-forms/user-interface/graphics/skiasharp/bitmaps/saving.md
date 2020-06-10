---
title: "salvataggio di bitmap SkiaSharp nei file" Descrizione: "esplorazione dei diversi formati di file supportati da SkiaSharp per il salvataggio di bitmap nella raccolta foto dell'utente".
ms. prod: Novell MS. Technology: Novell-skiasharp ms. AssetID: 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C autore: davidbritch ms. Author: dabritch ms. Date: 07/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="saving-skiasharp-bitmaps-to-files"></a>Salvataggio di bitmap SkiaSharp nei file

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Dopo che un'applicazione SkiaSharp ha creato o modificato una bitmap, l'applicazione potrebbe voler salvare la bitmap nella raccolta foto dell'utente:

![Salvataggio di bitmap](saving-images/SavingSample.png "Salvataggio di bitmap")

Questa attività comprende due passaggi:

- Conversione della bitmap SkiaSharp in dati in un formato di file specifico, ad esempio JPEG o PNG.
- Salvataggio del risultato nella raccolta foto mediante codice specifico della piattaforma.

## <a name="file-formats-and-codecs"></a>Formati di file e codec

La maggior parte dei formati di file bitmap più diffusi attualmente usa la compressione per ridurre lo spazio di archiviazione. Le due grandi categorie di tecniche di compressione sono denominate _lossal_ e _Lossless_. Questi termini indicano se l'algoritmo di compressione comporta la perdita di dati.

Il formato con i Lossi più diffusi è stato sviluppato dal Joint Photographic Experts Group ed è denominato JPEG. L'algoritmo di compressione JPEG analizza l'immagine usando uno strumento matematico denominato _trasformazione del coseno discreto_e tenta di rimuovere i dati non cruciali per preservare la fedeltà visiva dell'immagine. Il grado di compressione può essere controllato con un'impostazione generalmente definita _qualità_. Le impostazioni di qualità superiore generano file di dimensioni maggiori.

Al contrario, un algoritmo di compressione senza perdita di dati analizza l'immagine per la ripetizione e i modelli di pixel che possono essere codificati in modo da ridurre i dati, ma non comporta la perdita di informazioni. I dati bitmap originali possono essere ripristinati interamente dal file compresso. Il formato di file compresso con perdita di perdite principale attualmente in uso è Portable Network Graphics (PNG).

In genere, il formato JPEG viene usato per le fotografie, mentre PNG viene usato per le immagini che sono state generate manualmente o algoritmicamente. Qualsiasi algoritmo di compressione senza perdita di dati che riduce le dimensioni di alcuni file deve necessariamente aumentare le dimensioni degli altri. Fortunatamente, questo aumento delle dimensioni in genere si verifica solo per i dati che contengono numerose informazioni casuali (o apparentemente casuali).

Gli algoritmi di compressione sono sufficientemente complessi da giustificare due termini che descrivono i processi di compressione e decompressione:

- _decodifica_ &mdash; leggere un formato di file bitmap e decomprimerlo
- _codifica_ &mdash; comprimere la bitmap e scrivere in un formato di file bitmap

La [`SKBitmap`](xref:SkiaSharp.SKBitmap) classe contiene diversi metodi denominati `Decode` che creano un `SKBitmap` da un'origine compressa. È necessario fornire un nome file, un flusso o una matrice di byte. Il decodificatore può determinare il formato del file e passarlo alla funzione di decodifica interna appropriata.

Inoltre, la [`SKCodec`](xref:SkiaSharp.SKCodec) classe dispone di due metodi denominati `Create` che possono creare un `SKCodec` oggetto da un'origine compressa e consentire a un'applicazione di essere più impegnati nel processo di decodifica. La `SKCodec` classe è illustrata nell'articolo [**animazione di bitmap SkiaSharp**](animating.md#gif-animation) in connessione alla decodifica di un file GIF animato.

Quando si codifica una bitmap, sono necessarie altre informazioni: il codificatore deve conoscere il formato di file specifico che l'applicazione vuole usare (JPEG o PNG o qualcos'altro). Se si desidera un formato con perdita di informazioni, è necessario che la codifica conosca anche il livello di qualità desiderato.

La `SKBitmap` classe definisce un [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) metodo con la sintassi seguente:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

Questo metodo viene descritto più in dettaglio a breve. La bitmap codificata viene scritta in un flusso scrivibile. ("W" in `SKWStream` sta per "scrivibile"). Il secondo e il terzo argomento specificano il formato del file e (per i formati con perdita di perdite) la qualità desiderata compreso tra 0 e 100.

Inoltre, le [`SKImage`](xref:SkiaSharp.SKImage) classi e [`SKPixmap`](xref:SkiaSharp.SKPixmap) definiscono anche `Encode` metodi che sono un po' più versatili e che potrebbero essere preferibili. È possibile creare facilmente un `SKImage` oggetto da un `SKBitmap` oggetto usando il [`SKImage.FromBitmap`](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap)) metodo statico. È possibile ottenere un `SKPixmap` oggetto da un `SKBitmap` oggetto usando il [`PeekPixels`](xref:SkiaSharp.SKBitmap.PeekPixels) metodo.

Uno dei [`Encode`](xref:SkiaSharp.SKImage.Encode) metodi definiti da `SKImage` non dispone di parametri e viene salvato automaticamente in un formato png. Questo metodo senza parametri è molto semplice da usare.

## <a name="platform-specific-code-for-saving-bitmap-files"></a>Codice specifico della piattaforma per il salvataggio dei file bitmap

Quando si codifica un `SKBitmap` oggetto in un formato di file specifico, in genere si rimane con un oggetto flusso di qualche tipo o una matrice di dati. Alcuni dei `Encode` metodi (incluso quello senza parametri definiti da `SKImage` ) restituiscono un [`SKData`](xref:SkiaSharp.SKData) oggetto, che può essere convertito in una matrice di byte usando il [`ToArray`](xref:SkiaSharp.SKData.ToArray) metodo. Questi dati devono quindi essere salvati in un file.

Il salvataggio in un file nella risorsa di archiviazione locale dell'applicazione è piuttosto semplice perché è possibile usare `System.IO` classi e metodi standard per questa attività. Questa tecnica è illustrata nell'articolo [**animazione di bitmap SkiaSharp**](animating.md#bitmap-animation) in relazione all'animazione di una serie di bitmap del set di Mandelbrot.

Se si desidera che il file sia condiviso da altre applicazioni, è necessario salvarlo nella raccolta foto dell'utente. Questa attività richiede codice specifico della piattaforma e l'utilizzo di Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

Il progetto **SkiaSharpFormsDemo** nell'applicazione [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) definisce un' `IPhotoLibrary` interfaccia usata con la `DependencyService` classe. Definisce la sintassi di un `SavePhotoAsync` Metodo:

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

Questa interfaccia definisce anche il `PickPhotoAsync` metodo, che viene usato per aprire il selettore di file specifico della piattaforma per la raccolta foto del dispositivo.

Per `SavePhotoAsync` , il primo argomento è una matrice di byte che contiene la bitmap già codificata in un formato di file specifico, ad esempio JPEG o png. È possibile che un'applicazione voglia isolare tutte le bitmap create in una cartella specifica, specificata nel parametro successivo, seguito dal nome del file. Il metodo restituisce un valore booleano che indica l'esito positivo o negativo.

Le sezioni seguenti illustrano come `SavePhotoAsync` viene implementato in ogni piattaforma.

### <a name="the-ios-implementation"></a>Implementazione di iOS

L'implementazione di iOS di `SavePhotoAsync` Usa il [`SaveToPhotosAlbum`](xref:UIKit.UIImage.SaveToPhotosAlbum*) metodo di `UIImage` :

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        NSData nsData = NSData.FromArray(data);
        UIImage image = new UIImage(nsData);
        TaskCompletionSource<bool> taskCompletionSource = new TaskCompletionSource<bool>();

        image.SaveToPhotosAlbum((UIImage img, NSError error) =>
        {
            taskCompletionSource.SetResult(error == null);
        });

        return taskCompletionSource.Task;
    }
}
```

Sfortunatamente, non è possibile specificare un nome file o una cartella per l'immagine.

Il file **info. plist** nel progetto iOS richiede una chiave che indica che aggiunge immagini alla raccolta foto:

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

Attento! La chiave di autorizzazione per accedere semplicemente alla raccolta foto è molto simile, ma non identica:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>Implementazione di Android

L'implementazione Android di `SavePhotoAsync` controlla prima di tutto se l' `folder` argomento è `null` o una stringa vuota. In tal caso, la bitmap viene salvata nella directory radice della raccolta foto. In caso contrario, la cartella viene ottenuta e, se non esiste, viene creata:

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        try
        {
            File picturesDirectory = Environment.GetExternalStoragePublicDirectory(Environment.DirectoryPictures);
            File folderDirectory = picturesDirectory;

            if (!string.IsNullOrEmpty(folder))
            {
                folderDirectory = new File(picturesDirectory, folder);
                folderDirectory.Mkdirs();
            }

            using (File bitmapFile = new File(folderDirectory, filename))
            {
                bitmapFile.CreateNewFile();

                using (FileOutputStream outputStream = new FileOutputStream(bitmapFile))
                {
                    await outputStream.WriteAsync(data);
                }

                // Make sure it shows up in the Photos gallery promptly.
                MediaScannerConnection.ScanFile(MainActivity.Instance,
                                                new string[] { bitmapFile.Path },
                                                new string[] { "image/png", "image/jpeg" }, null);
            }
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

La chiamata a `MediaScannerConnection.ScanFile` non è strettamente necessaria, ma se si sta testando il programma controllando immediatamente la raccolta foto, è molto utile aggiornare la visualizzazione raccolta libreria.

Il file di **AndroidManifest.xml** richiede il seguente tag di autorizzazione:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>Implementazione di UWP

L'implementazione di UWP di `SavePhotoAsync` è molto simile alla struttura per l'implementazione di Android:

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        StorageFolder picturesDirectory = KnownFolders.PicturesLibrary;
        StorageFolder folderDirectory = picturesDirectory;

        // Get the folder or create it if necessary
        if (!string.IsNullOrEmpty(folder))
        {
            try
            {
                folderDirectory = await picturesDirectory.GetFolderAsync(folder);
            }
            catch
            { }

            if (folderDirectory == null)
            {
                try
                {
                    folderDirectory = await picturesDirectory.CreateFolderAsync(folder);
                }
                catch
                {
                    return false;
                }
            }
        }

        try
        {
            // Create the file.
            StorageFile storageFile = await folderDirectory.CreateFileAsync(filename,
                                                CreationCollisionOption.GenerateUniqueName);

            // Convert byte[] to Windows buffer and write it out.
            IBuffer buffer = WindowsRuntimeBuffer.Create(data, 0, data.Length, data.Length);
            await FileIO.WriteBufferAsync(storageFile, buffer);
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

La sezione delle **funzionalità** del file **Package. appxmanifest** richiede la **libreria di immagini**.

## <a name="exploring-the-image-formats"></a>Esplorazione dei formati di immagine

Ecco il [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) metodo di `SKImage` nuovo:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat)è un'enumerazione con membri che fanno riferimento a undici formati di file bitmap, alcuni dei quali sono piuttosto nascosti:

- `Astc`&mdash;Compressione della trama scalabile adattiva
- `Bmp`&mdash;Bitmap di Windows
- `Dng`&mdash;Adobe Digital negative
- `Gif`&mdash;Graphics Interchange Format
- `Ico`&mdash;Immagini icona di Windows
- `Jpeg`&mdash;Joint Photographic Experts Group
- `Ktx`&mdash;Formato trama Khronos per OpenGL
- `Pkm`&mdash;File di salvataggio Pokémon
- `Png`&mdash;Portable Network Graphics
- `Wbmp`&mdash;Formato Bitmap wireless Application Protocol (1 bit per pixel)
- `Webp`&mdash;Formato Google WebP

Come si vedrà a breve, solo tre di questi formati di file ( `Jpeg` , `Png` e `Webp` ) sono effettivamente supportati da SkiaSharp.

Per salvare un `SKBitmap` oggetto denominato nella `bitmap` raccolta foto dell'utente, è necessario anche un membro dell' `SKEncodedImageFormat` enumerazione denominata `imageFormat` e (per i formati con perdite) una variabile di tipo integer `quality` . È possibile usare il codice seguente per salvare la bitmap in un file con il nome `filename` nella `folder` cartella:

```csharp
using (MemoryStream memStream = new MemoryStream())
using (SKManagedWStream wstream = new SKManagedWStream(memStream))
{
    bitmap.Encode(wstream, imageFormat, quality);
    byte[] data = memStream.ToArray();

    // Check the data array for content!

    bool success = await DependencyService.Get<IPhotoLibrary>().SavePhotoAsync(data, folder, filename);

    // Check return value for success!
}
```

La `SKManagedWStream` classe deriva da `SKWStream` (che corrisponde a "flusso scrivibile"). Il `Encode` metodo scrive il file bitmap codificato nel flusso. I commenti nel codice fanno riferimento a un controllo degli errori che potrebbe essere necessario eseguire.

La pagina **Salva formati file** nell'applicazione [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) usa codice simile per consentire di sperimentare il salvataggio di una bitmap nei vari formati.

Il file XAML contiene un oggetto `SKCanvasView` che visualizza una bitmap, mentre il resto della pagina contiene tutti gli elementi necessari all'applicazione per chiamare il `Encode` metodo di `SKBitmap` . Dispone di un oggetto `Picker` per un membro dell' `SKEncodedImageFormat` enumerazione, un oggetto `Slider` per l'argomento Quality per i formati di bitmap con perdita di bit, due `Entry` visualizzazioni per un nome di file e una cartella e un `Button` per salvare il file.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.SaveFileFormatsPage"
             Title="Save Bitmap Formats">

    <StackLayout Margin="10">
        <skiaforms:SKCanvasView PaintSurface="OnCanvasViewPaintSurface"
                                VerticalOptions="FillAndExpand" />

        <Picker x:Name="formatPicker"
                Title="image format"
                SelectedIndexChanged="OnFormatPickerChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKEncodedImageFormat}">
                    <x:Static Member="skia:SKEncodedImageFormat.Astc" />
                    <x:Static Member="skia:SKEncodedImageFormat.Bmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Dng" />
                    <x:Static Member="skia:SKEncodedImageFormat.Gif" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ico" />
                    <x:Static Member="skia:SKEncodedImageFormat.Jpeg" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ktx" />
                    <x:Static Member="skia:SKEncodedImageFormat.Pkm" />
                    <x:Static Member="skia:SKEncodedImageFormat.Png" />
                    <x:Static Member="skia:SKEncodedImageFormat.Wbmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Webp" />
                </x:Array>
            </Picker.ItemsSource>
        </Picker>

        <Slider x:Name="qualitySlider"
                Maximum="100"
                Value="50" />

        <Label Text="{Binding Source={x:Reference qualitySlider},
                              Path=Value,
                              StringFormat='Quality = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal">
            <Label Text="Folder Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="folderNameEntry"
                   Text="SaveFileFormats"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="File Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="fileNameEntry"
                   Text="Sample.xxx"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <Button Text="Save"
                Clicked="OnButtonClicked">
            <Button.Triggers>
                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference formatPicker},
                                               Path=SelectedIndex}"
                             Value="-1">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>

                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference fileNameEntry},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Button.Triggers>
        </Button>

        <Label x:Name="statusLabel"
               Text="OK"
               Margin="10, 0" />
    </StackLayout>
</ContentPage>
```

Il file code-behind carica una risorsa bitmap e USA `SKCanvasView` per visualizzarla. Questa bitmap non cambia mai. Il `SelectedIndexChanged` gestore per `Picker` modifica il nome file con un'estensione uguale al membro di enumerazione:

```csharp
public partial class SaveFileFormatsPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(typeof(SaveFileFormatsPage),
        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    public SaveFileFormatsPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        args.Surface.Canvas.DrawBitmap(bitmap, args.Info.Rect, BitmapStretch.Uniform);
    }

    void OnFormatPickerChanged(object sender, EventArgs args)
    {
        if (formatPicker.SelectedIndex != -1)
        {
            SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
            fileNameEntry.Text = Path.ChangeExtension(fileNameEntry.Text, imageFormat.ToString());
            statusLabel.Text = "OK";
        }
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
        int quality = (int)qualitySlider.Value;

        using (MemoryStream memStream = new MemoryStream())
        using (SKManagedWStream wstream = new SKManagedWStream(memStream))
        {
            bitmap.Encode(wstream, imageFormat, quality);
            byte[] data = memStream.ToArray();

            if (data == null)
            {
                statusLabel.Text = "Encode returned null";
            }
            else if (data.Length == 0)
            {
                statusLabel.Text = "Encode returned empty array";
            }
            else
            {
                bool success = await DependencyService.Get<IPhotoLibrary>().
                    SavePhotoAsync(data, folderNameEntry.Text, fileNameEntry.Text);

                if (!success)
                {
                    statusLabel.Text = "SavePhotoAsync return false";
                }
                else
                {
                    statusLabel.Text = "Success!";
                }
            }
        }
    }
}
```

Il `Clicked` gestore per l'oggetto `Button` esegue tutte le operazioni effettive. Ottiene due argomenti per `Encode` da `Picker` e `Slider` , quindi usa il codice illustrato in precedenza per creare un oggetto `SKManagedWStream` per il `Encode` metodo. Le due `Entry` visualizzazioni forniscono i nomi di file e cartelle per il `SavePhotoAsync` metodo.

La maggior parte di questo metodo è dedicata alla gestione di problemi o errori. Se `Encode` Crea una matrice vuota, significa che il formato di file specifico non è supportato. Se `SavePhotoAsync` restituisce `false` , il file non è stato salvato correttamente.

Ecco il programma che esegue:

[![Salva formati di file](saving-images/SaveFileFormats.png "Salva formati di file")](saving-images/SaveFileFormats-Large.png#lightbox)

Questo screenshot Mostra gli unici tre formati supportati su queste piattaforme:

- JPEG
- PNG
- WebP

Per tutti gli altri formati, il `Encode` metodo non scrive nulla nel flusso e la matrice di byte risultante è vuota.

La bitmap che la pagina **Salva formati file** salva è 600 pixel. Con 4 byte per pixel, questo è un totale di 1.440.000 byte in memoria. Nella tabella seguente vengono illustrate le dimensioni del file per diverse combinazioni di formato e qualità del file:

|Formato|Qualità|Dimensione|
|------|------:|---:|
| PNG | N/D | 492K |
| JPEG | 0 | 2.95 k |
|      | 50 | 22.1 k |
|      | 100 | 206K |
| WebP | 0 | 2.71 k |
|      | 50 | 11,9 k |
|      | 100 | 101K |

È possibile sperimentare diverse impostazioni di qualità ed esaminare i risultati.

## <a name="saving-finger-paint-art"></a>Salvataggio dell'arte del disegno Finger

Un uso comune di una bitmap è nei programmi di disegno, dove funziona come una _bitmap ombreggiata_. Tutto il disegno viene mantenuto sulla bitmap, che viene quindi visualizzata dal programma. La bitmap è utile anche per salvare il disegno.

Il [**disegno del dito nell'articolo SkiaSharp**](../paths/finger-paint.md) ha illustrato come usare il rilevamento del tocco per implementare un programma di disegno di fingere primitive. Il programma supporta un solo colore e una sola larghezza del tratto, ma conserva l'intero disegno in una raccolta di `SKPath` oggetti.

La pagina di disegno **con il dito con Save** nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) mantiene anche l'intero disegno in una raccolta di `SKPath` oggetti, ma esegue anche il rendering del disegno su una bitmap, che può essere salvata nella raccolta foto.

Gran parte di questo programma è simile al programma originale per il **Finger Paint** . Un miglioramento è che il file XAML ora crea un'istanza dei pulsanti con etichetta **Clear** e **Save**:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Bitmaps.FingerPaintSavePage"
             Title="Finger Paint Save">

    <StackLayout>
        <Grid BackgroundColor="White"
              VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
        </Grid>

        <Button Text="Clear"
                Grid.Row="0"
                Margin="50, 5"
                Clicked="OnClearButtonClicked" />

        <Button Text="Save"
                Grid.Row="1"
                Margin="50, 5"
                Clicked="OnSaveButtonClicked" />

    </StackLayout>
</ContentPage>
```

Il file code-behind mantiene un campo di tipo `SKBitmap` denominato `saveBitmap` . Questa bitmap viene creata o ricreata nel `PaintSurface` gestore ogni volta che viene modificata la dimensione della superficie di visualizzazione. Se è necessario ricreare la bitmap, il contenuto della bitmap esistente viene copiato nella nuova bitmap, in modo che tutti gli elementi vengano conservati indipendentemente dalla dimensione della superficie di visualizzazione.

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    SKBitmap saveBitmap;

    public FingerPaintSavePage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        // Create bitmap the size of the display surface
        if (saveBitmap == null)
        {
            saveBitmap = new SKBitmap(info.Width, info.Height);
        }
        // Or create new bitmap for a new size of display surface
        else if (saveBitmap.Width < info.Width || saveBitmap.Height < info.Height)
        {
            SKBitmap newBitmap = new SKBitmap(Math.Max(saveBitmap.Width, info.Width),
                                              Math.Max(saveBitmap.Height, info.Height));

            using (SKCanvas newCanvas = new SKCanvas(newBitmap))
            {
                newCanvas.Clear();
                newCanvas.DrawBitmap(saveBitmap, 0, 0);
            }

            saveBitmap = newBitmap;
        }

        // Render the bitmap
        canvas.Clear();
        canvas.DrawBitmap(saveBitmap, 0, 0);
    }
    ···
}
```

Il disegno eseguito dal `PaintSurface` gestore si verifica alla fine ed è costituito esclusivamente dal rendering della bitmap.

L'elaborazione del tocco è simile al programma precedente. Il programma gestisce due raccolte, `inProgressPaths` e `completedPaths` , che contengono tutti gli elementi che l'utente ha disegnato dall'ultima volta in cui lo schermo è stato cancellato. Per ogni evento Touch, il `OnTouchEffectAction` gestore chiama `UpdateBitmap` :

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                            (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }

    void UpdateBitmap()
    {
        using (SKCanvas saveBitmapCanvas = new SKCanvas(saveBitmap))
        {
            saveBitmapCanvas.Clear();

            foreach (SKPath path in completedPaths)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }

            foreach (SKPath path in inProgressPaths.Values)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }
        }

        canvasView.InvalidateSurface();
    }
    ···
}
```

Il `UpdateBitmap` metodo viene ridisegnato creando `saveBitmap` un nuovo oggetto `SKCanvas` , deselezionando e quindi eseguendo il rendering di tutti i percorsi sulla bitmap. Si conclude con l'invalidamento `canvasView` in modo che la bitmap possa essere disegnata sullo schermo.

Ecco i gestori per i due pulsanti. Il pulsante **Cancella** cancella sia le raccolte di percorsi, gli aggiornamenti (che comportano `saveBitmap` la cancellazione della bitmap) che invalida `SKCanvasView` :

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    void OnClearButtonClicked(object sender, EventArgs args)
    {
        completedPaths.Clear();
        inProgressPaths.Clear();
        UpdateBitmap();
        canvasView.InvalidateSurface();
    }

    async void OnSaveButtonClicked(object sender, EventArgs args)
    {
        using (SKImage image = SKImage.FromBitmap(saveBitmap))
        {
            SKData data = image.Encode();
            DateTime dt = DateTime.Now;
            string filename = String.Format("FingerPaint-{0:D4}{1:D2}{2:D2}-{3:D2}{4:D2}{5:D2}{6:D3}.png",
                                            dt.Year, dt.Month, dt.Day, dt.Hour, dt.Minute, dt.Second, dt.Millisecond);

            IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
            bool result = await photoLibrary.SavePhotoAsync(data.ToArray(), "FingerPaint", filename);

            if (!result)
            {
                await DisplayAlert("FingerPaint", "Artwork could not be saved. Sorry!", "OK");
            }
        }
    }
}
```

Il gestore del pulsante **Salva** usa il [`Encode`](xref:SkiaSharp.SKImage.Encode) metodo semplificato da `SKImage` . Questo metodo codifica usando il formato PNG. L' `SKImage` oggetto viene creato in base a `saveBitmap` e l' `SKData` oggetto contiene il file png codificato.

Il `ToArray` metodo di `SKData` ottiene una matrice di byte. Questo è ciò che viene passato al `SavePhotoAsync` metodo, insieme a un nome di cartella fisso e a un nome file univoco costruito dalla data e dall'ora correnti.

Ecco il programma in azione:

[![Disegno del dito Salva](saving-images/FingerPaintSave.png "Disegno del dito Salva")](saving-images/FingerPaintSave-Large.png#lightbox)

Nell'esempio [**SpinPaint**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint) viene usata una tecnica molto simile. Si tratta anche di un programma di disegno con un dito, ad eccezione del fatto che l'utente dipinge su un disco rotante che riproduce quindi le progettazioni negli altri quattro quadranti. Colore delle modifiche apportate al disegno del dito durante la rotazione del disco:

[![Disegno di spin](saving-images/SpinPaint.png "Disegno di spin")](saving-images/SpinPaint-Large.png#lightbox)

Il pulsante **Salva** della `SpinPaint` classe è simile al **disegno del dito** in quanto salva l'immagine in un nome di cartella fissa (**SpainPaint**) e un nome file creato dalla data e dall'ora.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SpinPaint (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)
