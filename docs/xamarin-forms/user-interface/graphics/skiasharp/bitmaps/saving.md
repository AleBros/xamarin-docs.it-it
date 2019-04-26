---
title: Salvataggio di bitmap di SkiaSharp ai file
description: Esplorare i vari formati di file supportati da SkiaSharp per il salvataggio di bitmap nella libreria di foto dell'utente.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 885bba381e1371d273000fa0d970b465e9ca9c0b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61410753"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>Salvataggio di bitmap di SkiaSharp ai file

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Dopo che un'applicazione di SkiaSharp ha creato o modificato una bitmap, l'applicazione potrebbe essere necessario salvare la mappa di bit alla raccolta di foto dell'utente:

![Salvataggio di bitmap](saving-images/SavingSample.png "salvataggio di bitmap")

Questa attività include due passaggi:

- Conversione di bitmap SkiaSharp in dati in un formato di file specifico, ad esempio JPEG o PNG.
- Salvare il risultato nella raccolta di foto usando codice specifico della piattaforma.

## <a name="file-formats-and-codecs"></a>Codec e formati di file

La maggior parte dei file di bitmap più diffusi di oggi formatta utilizzano la compressione per ridurre lo spazio di archiviazione. Vengono chiamate due ampie categorie di tecniche di compressione _perdita di dati_ e _senza perdita di dati_. Le presenti condizioni di indicano se l'algoritmo di compressione comporta la perdita di dati.

Il formato più comune con perdita di dati è stato sviluppato da Joint Photographic Experts Group e viene chiamato JPEG. L'algoritmo di compressione JPEG analizza l'immagine utilizzando uno strumento matematico denominato il _discreta del coseno_e tenta di rimuovere i dati che non sono fondamentali per mantenere lo stesso aspetto dell'immagine. Il livello di compressione può essere controllato con un'impostazione a livello generale detta _qualità_. Impostazioni di qualità superiore hanno come risultato in file più grandi.

Al contrario, un algoritmo di compressione senza perdita di dati consente di analizzare l'immagine per la ripetizione e i modelli di pixel che possono essere codificati in modo che la riduzione dei dati, ma non comporta la perdita di tutte le informazioni. I dati bitmap originale possono essere ripristinati interamente dal file compresso. Il formato di file compressi senza perdita di dati primario in uso è oggi grafica PNG (Portable Network).

In generale, JPEG viene usato per le foto, mentre PNG viene usato per le immagini che sono state generate in modo algoritmico o manualmente. Qualsiasi algoritmo di compressione senza perdita di dati che riduce le dimensioni di alcuni file necessariamente deve aumentare le dimensioni di altri utenti. Fortunatamente, questo aumento delle dimensioni in genere si verifica solo per i dati che contiene numerose informazioni casuale (o apparentemente casuali).

La compressione algoritmi sono sufficientemente complessi da richiedere due termini che descrivono i processi di compressione e decompressione:

- _decodificare_ &mdash; leggere un file di formato di bitmap e decomprimerlo
- _codificare_ &mdash; comprimere la bitmap e scrivere in un formato di file bitmap

Il [ `SKBitmap` ](xref:SkiaSharp.SKBitmap) classe contiene più metodi denominati `Decode` che creano un `SKBitmap` da un'origine compressa. È necessario consiste nel fornire un nome file, flusso o matrice di byte. Il decodificatore può determinare il formato di file e trasferirlo a funzione interna corretta decodifica.

Inoltre, il [ `SKCodec` ](xref:SkiaSharp.SKCodec) classe ha due metodi denominati `Create` che consente di creare un `SKCodec` dell'oggetto da un'origine compressa e consentono a un'applicazione ottenere più coinvolti nel processo di decodifica. (Il `SKCodec` classe è illustrata nell'articolo [ **animazione bitmap SkiaSharp** ](animating.md#gif-animation) in relazione alla decodifica un GIF animato.)

Quando si codifica una bitmap, sono necessarie ulteriori informazioni: Il codificatore deve conoscere che il formato di file specifico dell'applicazione desidera utilizzare (JPEG o PNG o qualcos'altro). Se si desidera usare un formato con perdita di dati, la codifica deve inoltre sapere il livello di qualità desiderato.

Il `SKBitmap` classe definisce uno [ `Encode` ](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) metodo con la sintassi seguente:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

Questo metodo viene descritto in dettaglio tra breve. La bitmap codificata è scritta in un flusso scrivibile. (il 'W' in `SKWStream` è l'acronimo di "scrittura".) Il secondo e terzo argomento specifica il formato di file e i formati con perdita di dati) (di qualità desiderata compreso tra 0 e 100.

Inoltre, il [ `SKImage` ](xref:SkiaSharp.SKImage) e [ `SKPixmap` ](xref:SkiaSharp.SKPixmap) classi definiscono inoltre `Encode` metodi che fanno un po' più versatile e quale potrebbe essere preferibile. È possibile creare facilmente un' `SKImage` dell'oggetto da un `SKBitmap` usando il metodo statico dell'oggetto [ `SKImage.FromBitmap` ](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap)) (metodo). È possibile ottenere un' `SKPixmap` dell'oggetto da un `SKBitmap` dell'oggetto usando la [ `PeekPixels` ](xref:SkiaSharp.SKBitmap.PeekPixels) (metodo).

Uno dei [ `Encode` ](xref:SkiaSharp.SKImage.Encode) metodi definiti da `SKImage` non presenta parametri e Salva automaticamente in formato PNG. Tale metodo senza parametri è molto facile da usare.

## <a name="platform-specific-code-for-saving-bitmap-files"></a>Codice specifico della piattaforma per il salvataggio dei file bitmap

Quando si codifica un `SKBitmap` oggetto in un determinato file di formato, in genere sarà rimossa con un oggetto di flusso di qualche tipo, o una matrice di dati. Alcune delle `Encode` metodi (incluso quello con nessun parametro definito dal `SKImage`) restituiscono un [ `SKData` ](xref:SkiaSharp.SKData) oggetto, che può essere convertito in una matrice di byte usando il [ `ToArray` ](xref:SkiaSharp.SKData.ToArray) metodo. Questi dati devono essere quindi salvati in un file.

Salvataggio in un file nell'archiviazione locale dell'applicazione è piuttosto semplice, poiché è possibile utilizzare standard `System.IO` classi e metodi per questa attività. Questa tecnica è dimostrata nell'articolo [ **animazione bitmap SkiaSharp** ](animating.md#bitmap-animation) in relazione a una serie di bitmap del set di Mandelbrot l'animazione.

Se si desidera che il file deve essere condiviso da altre applicazioni, deve essere salvato alla raccolta di foto dell'utente. Questa attività richiede codice specifico della piattaforma e l'uso di xamarin. Forms [ `DependencyService` ](xref:Xamarin.Forms.DependencyService).

Il **SkiaSharpFormsDemo** del progetto nel [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) applicazione definisce un `IPhotoLibrary` interfaccia utilizzata con il `DependencyService` classe. Definisce la sintassi di un `SavePhotoAsync` metodo:

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

Questa interfaccia definisce anche il `PickPhotoAsync` metodo, che consente di aprire il selettore di file specifiche della piattaforma per la libreria foto del dispositivo.

Per `SavePhotoAsync`, il primo argomento è una matrice di byte che contiene la mappa di bit già codificato in un formato di file specifico, ad esempio JPEG o PNG. È possibile che un'applicazione potrebbe voler isolare tutte le bitmap che viene creato in una particolare cartella, viene specificata nel parametro successivo, seguito dal nome del file. Il metodo restituisce un valore booleano che indica la riuscita o No.

Le sezioni seguenti illustrano come `SavePhotoAsync` viene implementata su ogni piattaforma.

### <a name="the-ios-implementation"></a>L'implementazione di iOS

L'implementazione di iOS del `SavePhotoAsync` utilizza le [ `SaveToPhotosAlbum` ](xref:UIKit.UIImage.SaveToPhotosAlbum*) metodo `UIImage`:

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

Il **Info. plist** file nel progetto iOS richiede una chiave che indica che le immagini aggiunge alla raccolta foto:

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

Attento! La chiave di autorizzazione per semplicemente l'accesso a raccolta foto è molto simile ma non lo stesso:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>L'implementazione di Android

L'implementazione di Android `SavePhotoAsync` controlla innanzitutto se il `folder` l'argomento è `null` o una stringa vuota. In questo caso, la bitmap viene salvata nella directory radice della libreria di foto. In caso contrario, si ottiene la cartella e se non esiste, crearla:

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

La chiamata a `MediaScannerConnection.ScanFile` non è strettamente necessaria, ma se si sta testando il programma controllando immediatamente la raccolta di foto, risulta utile molto aggiornando la visualizzazione della raccolta della raccolta.

Il **androidmanifest. XML** file richiede il seguente tag di autorizzazione:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>L'implementazione di UWP

L'implementazione di UWP di `SavePhotoAsync` è molto simile nella struttura per l'implementazione di Android:

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

Il **funzionalità** sezione il **package. appxmanifest** file richiede **raccolta immagini**.

## <a name="exploring-the-image-formats"></a>Esplorare i formati di immagine

Di seguito è riportato il [ `Encode` ](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) metodo `SKImage` nuovamente:

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat) è un'enumerazione con membri che fanno riferimento ai formati di file bitmap undici, alcune delle quali sono piuttosto poco chiari:

- `Astc` &mdash; Compressione di trama scalabile adattivo
- `Bmp` &mdash; Bitmap di Windows
- `Dng` &mdash; Negativo digitale di Adobe
- `Gif` &mdash; Graphics Interchange Format
- `Ico` &mdash; Immagini icona di Windows
- `Jpeg` &mdash; Joint Photographic Experts Group
- `Ktx` &mdash; Formato della trama Khronos per OpenGL
- `Pkm` &mdash; Pokémon salvataggio file
- `Png` &mdash; Immagine PNG
- `Wbmp` &mdash; Formato di Bitmap di Wireless Application Protocol (1 bit per pixel)
- `Webp` &mdash; Formato Google WebP

Come si vedrà a breve, solo tre di questi formati di file (`Jpeg`, `Png`, e `Webp`) sono effettivamente supportati da SkiaSharp.

Per salvare un' `SKBitmap` oggetto denominato `bitmap` alla raccolta di foto dell'utente, è necessario anche un membro del `SKEncodedImageFormat` enumerazione denominata `imageFormat` e i formati con perdita di dati) (di un numero intero `quality` variabile. È possibile usare il codice seguente per salvare la mappa di bit in un file con il nome `filename` nella `folder` cartella:

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

Il `SKManagedWStream` deriva dalla classe `SKWStream` (che è l'acronimo di "flusso accessibile in scrittura"). Il `Encode` metodo scrive il file con codifica bitmap in tale flusso. Per alcuni errori che potrebbe essere necessario eseguire, vedere i commenti nel codice.

Il **salvare formati di File** pagina il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) applicazione usa un codice simile per consentire di sperimentare il salvataggio di una bitmap in vari formati.

Il file XAML contiene un' `SKCanvasView` che consente di visualizzare una bitmap, mentre il resto della pagina contiene tutto ciò che l'applicazione deve chiamare il `Encode` metodo `SKBitmap`. Ha un `Picker` per un membro del `SKEncodedImageFormat` enumerazione, un `Slider` per l'argomento di qualità per i formati bitmap con perdita di dati, due `Entry` visualizzazioni per un nome di nome file e cartelle e un `Button` per il salvataggio del file.

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

Il file code-behind carica una risorsa della bitmap e utilizza il `SKCanvasView` per visualizzarlo. Che bitmap mai le modifiche. Il `SelectedIndexChanged` gestore per il `Picker` modifica il nome del file con un'estensione che corrisponde al membro dell'enumerazione:

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

Il `Clicked` gestore per il `Button` funziona tutto il reale. Ottiene due argomenti per `Encode` dal `Picker` e `Slider`e quindi Usa il codice illustrato in precedenza per creare un' `SKManagedWStream` per il `Encode` (metodo). I due `Entry` viste forniscono i nomi di file e cartelle per il `SavePhotoAsync` (metodo).

La maggior parte di questo metodo è dedicato alla gestione degli errori o problemi. Se `Encode` crea una matrice vuota, significa che non è supportato il formato di file specifico. Se `SavePhotoAsync` restituisce `false`, quindi non è stato salvato il file.

Ecco il programma in esecuzione:

[![Salvare i formati di File](saving-images/SaveFileFormats.png "salvare formati di File")](saving-images/SaveFileFormats-Large.png#lightbox)

Questo screenshot Mostra solo tre formati supportati su queste piattaforme:

- JPEG
- PNG
- E WebP

Per tutte le altri formati, il `Encode` metodo scrive niente nel flusso e la matrice di byte risultante è vuota.

La mappa di bit che la **salvare formati di File** pagina Salva è 600 pixel quadrati. Con 4 byte per pixel, che equivale a un totale di byte 1,440,000 in memoria. Nella tabella seguente mostra le dimensioni del file delle varie combinazioni di formato di file e la qualità:

|Formato|Qualità|Dimensione|
|------|------:|---:|
| PNG | N/D | 492K |
| JPEG | 0 | 2,95 K |
|      | 50 | 22.1 K |
|      | 100 | K 206 |
| E WebP | 0 | 2.71K |
|      | 50 | 11,9 K |
|      | 100 | K 101 |

È possibile sperimentare diverse impostazioni di qualità ed esaminare i risultati.

## <a name="saving-finger-paint-art"></a>Salvataggio finger-paint art

Viene in genere utilizzato di una bitmap nella creazione di programmi, in cui funziona come un elemento denominato una _bitmap shadow_. Tutti il disegno viene mantenuto su una bitmap, che viene quindi visualizzata dal programma. La bitmap anche molto utile per il salvataggio del disegno.

Il [ **disegno a mano libera in SkiaSharp** ](../paths/finger-paint.md) articolo è stato illustrato come usare le funzionalità touch di rilevamento per implementare un programma dipingere primitivo. Il programma è supportato un solo colore e spessore del unico tratto, ma conservato l'intero disegno in una raccolta di `SKPath` oggetti.

Il **Paint dito con salvare** pagina il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio mantiene anche l'intero disegno in una raccolta di `SKPath` oggetti, ma anche esegue il rendering del disegno su una bitmap, che consente di risparmiare alla raccolta di foto.

Gran parte di questo programma è simile all'istanza originale **dito Paint** programma. Un miglioramento è che il file XAML ora creata un'istanza di pulsanti contrassegnati **chiaro** e **salvare**:

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

Il file code-behind gestisce un campo di tipo `SKBitmap` denominato `saveBitmap`. Questa bitmap viene creata o ricreata nel `PaintSurface` gestore ogni volta che le dimensioni dello schermo della superficie di attacco delle modifiche. Se la mappa di bit deve essere ricreato, il contenuto della mappa di bit esistente viene copiato nella nuova bitmap in modo che tutto ciò che viene mantenuto indipendentemente da come area di visualizzazione Cambia dimensioni:

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

Il disegno scopo il `PaintSurface` gestore si verifica alla fine ed è costituito esclusivamente per il rendering della bitmap.

L'elaborazione di tocco è simile al programma precedente. Il programma gestisce due raccolte, `inProgressPaths` e `completedPaths`, che contengono tutto quello che l'utente è disegnato dall'ultima volta che la visualizzazione è stata cancellata. Per ogni evento di tocco, il `OnTouchEffectAction` chiamate del gestore `UpdateBitmap`:

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

Il `UpdateBitmap` metodo ridisegna `saveBitmap` creando un nuovo `SKCanvas`deselezionandolo e quindi il rendering di tutti i percorsi nella mappa di bit. Si conclude invalidando `canvasView` in modo che la bitmap può essere disegnata sullo schermo.

Ecco i gestori per i due pulsanti. Il **chiaro** pulsante Cancella entrambe le raccolte di percorso, gli aggiornamenti `saveBitmap` (che comporta la cancellazione della bitmap) e invalida il `SKCanvasView`:

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

Il **salvare** gestore del pulsante Usa la semplificata [ `Encode` ](xref:SkiaSharp.SKImage.Encode) metodo `SKImage`. Questo metodo codifica nel formato PNG. Il `SKImage` oggetto viene creato in base `saveBitmap`e il `SKData` oggetto contiene i file PNG con codifica.

Il `ToArray` metodo di `SKData` Ottiene una matrice di byte. Questo è ciò che viene passato per il `SavePhotoAsync` (metodo), insieme a un nome di cartella predefinito e un nome file univoco creato dalla data e ora correnti.

Ecco il programma in azione:

[![Finger Paint salvataggio](saving-images/FingerPaintSave.png "Finger Paint di salvataggio")](saving-images/FingerPaintSave-Large.png#lightbox)

Viene utilizzata una tecnica molto simile nel [ **SpinPaint** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SpinPaint/) esempio. Questo è anche un programma dipingere ad eccezione del fatto che l'utente disegna su un disco rotante quindi riproduce le progettazioni nel relativo altri quattro quadranti. Il colore delle modifiche con un dito paint come disco di rotazione:

[![Rotazione Paint](saving-images/SpinPaint.png "Spin Paint")](saving-images/SpinPaint-Large.png#lightbox)

Il **salvare** pulsante del `SpinPaint` classe è simile al **Paint dito** che salva l'immagine a un nome di cartella predefinito (**SpainPaint**) e un nome file creato da Data e ora.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [SpinPaint (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SpinPaint/)
