---
title: Visualizzazione di bitmap di SkiaSharp
description: Informazioni su come visualizzare SkiaSharp bitmap in pixel, dimensioni ed espanso per riempire i rettangoli mantenendo le proporzioni.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 9955b68346c74435a3a141c69d02e1bec5856bd3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759517"
---
# <a name="displaying-skiasharp-bitmaps"></a>Visualizzazione di bitmap di SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Nell'articolo è stato introdotto il soggetto di bitmap di SkiaSharp  **[nozioni di base di Bitmap in SkiaSharp](../basics/bitmaps.md)** . Questo articolo ha illustrato tre modi per bitmap di carico e i tre modi per visualizzare le bitmap. Questo articolo vengono esaminate le tecniche per caricare le immagini bitmap e diventa più approfondito l'utilizzo dei `DrawBitmap` metodi di `SKCanvas`.

![Visualizzazione campione](displaying-images/DisplayingSample.png "la visualizzazione di esempio")

Il `DrawBitmapLattice` e `DrawBitmapNinePatch` metodi sono descritti nell'articolo  **[visualizzazione delle bitmap di SkiaSharp segmentata](segmented.md)** .

Esempi in questa pagina sono compresi i **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** dell'applicazione. Dalla home page dell'applicazione, scegliere **SkiaSharp Bitmaps**, quindi passare al **la visualizzazione di bitmap** sezione.

## <a name="loading-a-bitmap"></a>Il caricamento di una bitmap

Una bitmap utilizzata da un'applicazione di SkiaSharp in genere deriva da una delle tre origini diverse:

- Da Internet
- Da una risorsa incorporata nel file eseguibile
- Da Raccolta foto dell'utente

È anche possibile per un'applicazione di SkiaSharp creare una nuova bitmap, quindi disegnare su di esso o impostare i bit della bitmap tramite algoritmo. Queste tecniche vengono illustrate negli articoli **[creazione e la creazione su SkiaSharp Bitmaps](drawing.md)** e **[l'accesso a pixel Bitmap SkiaSharp](pixel-bits.md)** .

Negli esempi di codice di tre seguenti del caricamento di una bitmap, viene usata la classe contenga un campo di tipo `SKBitmap`:

```csharp
SKBitmap bitmap;
```

Come l'articolo **[nozioni di base di Bitmap in SkiaSharp](../basics/bitmaps.md)** indicato, è il modo migliore per caricare una bitmap tramite Internet con il [ `HttpClient` ](xref:System.Net.Http.HttpClient) classe. Una singola istanza della classe può essere definita come campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Quando si usa `HttpClient` con applicazioni iOS e Android, è opportuno impostare le proprietà del progetto come descritto nei documenti sul  **[Transport Layer Security (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

Codice che usa `HttpClient` spesso comporta il `await` operatore, in modo che si trova un `async` metodo:

```csharp
try
{
    using (Stream stream = await httpClient.GetStreamAsync("https:// ··· "))
    using (MemoryStream memStream = new MemoryStream())
    {
        await stream.CopyToAsync(memStream);
        memStream.Seek(0, SeekOrigin.Begin);

        bitmap = SKBitmap.Decode(stream);
        ···
    };
}
catch
{
    ···
}
```

Si noti che il `Stream` ottenuto dall'oggetto `GetStreamAsync` viene copiato in un `MemoryStream`. Android non supporta il `Stream` da `HttpClient` devono essere elaborati dal thread principale, ad eccezione di metodi asincroni. 

Il [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) esegue una grande quantità di lavoro: L' `Stream` oggetto passato fa riferimento a un blocco di memoria contenente un'intera bitmap in uno dei formati di file bitmap comuni, generalmente JPEG, PNG o gif. Il `Decode` metodo deve determinare il formato e quindi decodificare il file bitmap in formato bitmap interne di SkiaSharp.

Dopo il codice chiama `SKBitmap.Decode`, probabilmente invaliderà le `CanvasView` in modo che il `PaintSurface` gestore di è possibile visualizzare la bitmap appena caricata.

Il secondo modo per caricare una bitmap, includendo la mappa di bit come risorsa incorporata nella libreria .NET Standard fanno riferimento i singoli progetti di piattaforma. Una risorsa ID viene passato per il [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) (metodo). Questo ID di risorsa include il nome dell'assembly, nome della cartella e il nome della risorsa separato da punti:

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

File bitmap possono essere archiviati anche come risorse nel progetto singoli platform per iOS, Android e Universal Windows Platform (UWP). Tuttavia, il caricamento di tali bitmap richiede codice di cui si trova il progetto della piattaforma.

Un terzo approccio per ottenere una bitmap è dalla raccolta immagini dell'utente. Il codice seguente usa un servizio di dipendenza che è incluso nel **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** dell'applicazione. Il **SkiaSharpFormsDemo** libreria .NET Standard include la `IPhotoLibrary` interfaccia, mentre ciascun progetto della piattaforma contiene un `PhotoLibrary` classe che implementa tale interfaccia.

```csharp
IPhotoicturePicker picturePicker = DependencyService.Get<IPhotoLibrary>();

using (Stream stream = await picturePicker.GetImageStreamAsync())
{
    if (stream != null)
    {
        bitmap = SKBitmap.Decode(stream);
        ···
    }
}
```

In genere, tale codice invalida il `CanvasView` in modo che il `PaintSurface` gestore di è possibile visualizzare la nuova bitmap.

Il `SKBitmap` classe definisce diverse proprietà utili, tra cui [ `Width` ](xref:SkiaSharp.SKBitmap.Width) e [ `Height` ](xref:SkiaSharp.SKBitmap.Height), che mostra le dimensioni in pixel della bitmap, nonché molti metodi, tra cui metodi per creare bitmap, copiarli di esporre i bit di pixel. 

## <a name="displaying-in-pixel-dimensions"></a>Visualizzazione in dimensioni in pixel

Di SkiaSharp [ `Canvas` ](xref:SkiaSharp.SKCanvas) classe definisce quattro `DrawBitmap` metodi. Questi metodi consentono di bitmap da visualizzare in due modi fondamentalmente diversi: 

- Specifica un `SKPoint` valore (o separata `x` e `y` valori) consente di visualizzare la mappa di bit in relative dimensioni in pixel. I pixel della bitmap vengono eseguito il mapping direttamente al pixel dello schermo del video.
- Se si specifica un rettangolo, la bitmap per estenderla per le dimensioni del rettangolo. 

Visualizzare una bitmap nella usando le dimensioni in pixel [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) con un `SKPoint` parametro oppure [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) con separato `x` e `y` parametri:

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

Questi due metodi sono funzionalmente identici. Il punto specificato indica la posizione dell'angolo superiore sinistro della bitmap rispetto all'area di disegno. Poiché la risoluzione i pixel dei dispositivi mobili è talmente elevata, più piccole bitmap visualizzato in genere abbastanza ridotti in questi dispositivi.

L'opzione facoltativa `SKPaint` parametro consente di visualizzare la mappa di bit con la trasparenza. A questo scopo, creare un `SKPaint` dell'oggetto e impostare il `Color` a qualsiasi proprietà `SKColor` minore di 1 valore con un valore alfa del canale. Ad esempio:

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

Il 0x80 passato come ultimo argomento indica la trasparenza di 50%. È anche possibile impostare un canale alfa su uno dei colori predefiniti:

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

Tuttavia, al colore stesso è irrilevante. Viene esaminato solo il canale alfa quando si usa la `SKPaint` dell'oggetto un `DrawBitmap` chiamare.

Il `SKPaint` oggetto svolge un ruolo anche quando si nasconde le bitmap con le modalità di blend o filtrare gli effetti. Questi vengono illustrati negli articoli [modalità di composizione e blend SkiaSharp](../effects/blend-modes/index.md) e [filtri immagini di SkiaSharp](../effects/image-filters.md).

Il **dimensioni in Pixel** pagina il **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** programma di esempio consente di visualizzare una risorsa della bitmap che 320 pixel in larghezza 240 pixel di altezza:

```csharp
public class PixelDimensionsPage : ContentPage
{
    SKBitmap bitmap;

    public PixelDimensionsPage()
    {
        Title = "Pixel Dimensions";

        // Load the bitmap from a resource
        string resourceID = "SkiaSharpFormsDemos.Media.Banana.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        // Create the SKCanvasView and set the PaintSurface handler
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

        float x = (info.Width - bitmap.Width) / 2;
        float y = (info.Height - bitmap.Height) / 2;

        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

Il `PaintSurface` gestore consente di centrare la mappa di bit calcolando `x` e `y` valori basati sulle dimensioni in pixel dell'area di visualizzazione e le dimensioni in pixel della bitmap:

[![Dimensioni in pixel](displaying-images/PixelDimensions.png "dimensioni in Pixel")](displaying-images/PixelDimensions-Large.png#lightbox)

Se l'applicazione desidera visualizzare la bitmap all'angolo superiore sinistro, lo passerebbe semplicemente le coordinate di (0, 0). 

## <a name="a-method-for-loading-resource-bitmaps"></a>Un metodo per il caricamento delle bitmap di risorsa

Molti degli esempi di presentarsi sarà necessario caricare le risorse di bitmap. Il metodo statico `BitmapExtensions` classe la **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** soluzione contiene un metodo di supporto:

```csharp
static class BitmapExtensions
{
    public static SKBitmap LoadBitmapResource(Type type, string resourceID)
    {
        Assembly assembly = type.GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            return SKBitmap.Decode(stream);
        }
    }
    ···
}
```

Si noti che il `Type` parametro. Può trattarsi di `Type` oggetto associato a qualsiasi tipo nell'assembly che archivia la risorsa della bitmap.

Ciò `LoadBitmapResource` verrà utilizzato il metodo in tutti i campioni successivi che richiedono risorse bitmap.

## <a name="stretching-to-fill-a-rectangle"></a>L'estensione per riempire un rettangolo

Il `SKCanvas` classe definisce anche una [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) metodo che esegue il rendering di bitmap da un rettangolo e un altro [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) metodo che esegue il rendering di un sottoinsieme rettangolare della bitmap da un rettangolo:

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

In entrambi i casi, la bitmap viene adattata per riempire il rettangolo denominato `dest`. Il secondo metodo, il `source` rettangolo consente di selezionare un subset della bitmap. Il `dest` rettangolo è relativo alla periferica di output; i `source` rettangolo è relativo alla bitmap.

Il **rettangolo con riempimento** pagina viene illustrato il primo di questi due metodi tramite la visualizzazione stessa bitmap usato nell'esempio precedente in un rettangolo le stesse dimensioni come area di disegno: 

```csharp
public class FillRectanglePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public FillRectanglePage ()
    {
        Title = "Fill Rectangle";

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

Si noti l'uso della nuova `BitmapExtensions.LoadBitmapResource` per impostare il `SKBitmap` campo. Il rettangolo di destinazione viene ottenuto dal [ `Rect` ](xref:SkiaSharp.SKImageInfo.Rect) proprietà `SKImageInfo`, che vengono indicate le dimensioni dell'area di visualizzazione:

[![Riempire rettangolo](displaying-images/FillRectangle.png "riempire rettangolo")](displaying-images/FillRectangle-Large.png#lightbox)

Si tratta in genere _non_ risultato desiderato. L'immagine risulta distorta dal verrà estesa in modo diverso nelle direzioni orizzontale e verticale. La visualizzazione di una mappa di bit in un valore diverso da relative dimensioni in pixel, in genere si desidera mantenere le proporzioni originali della bitmap.

## <a name="stretching-while-preserving-the-aspect-ratio"></a>L'estensione mantenendo le proporzioni

L'estensione di una bitmap, mantenendo tuttavia le proporzioni è un processo noto anche come _ridimensionamento uniforme_. A tale termine algoritmico suggerite. Una possibile soluzione è illustrata nella **ridimensionamento uniforme** pagina:

```csharp
public class UniformScalingPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(UniformScalingPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public UniformScalingPage()
    {
        Title = "Uniform Scaling";

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

        float scale = Math.Min((float)info.Width / bitmap.Width, 
                               (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect destRect = new SKRect(x, y, x + scale * bitmap.Width, 
                                           y + scale * bitmap.Height);

        canvas.DrawBitmap(bitmap, destRect);
    }
}
```

Il `PaintSurface` gestore calcola un `scale` fattore che è il requisito minimo del rapporto tra l'altezza e la larghezza della bitmap e l'altezza e larghezza di visualizzazione. Il `x` e `y` valori possono quindi essere calcolati per centrare la mappa di bit con scalabilità all'interno della larghezza di visualizzazione e l'altezza. Il rettangolo di destinazione ha un angolo superiore sinistro di `x` e `y` e un angolo inferiore destro di tali valori oltre la larghezza in scala e l'altezza della bitmap:

[![La scalabilità uniforme](displaying-images/UniformScaling.png "ridimensionamento uniforme")](displaying-images/UniformScaling-Large.png#lightbox)

Ruotare il telefono lateralmente per visualizzare la bitmap adattata per quell'area:

[![Uniforme di scalabilità orizzontale](displaying-images/UniformScaling-Landscape.png "Uniform scalabilità orizzontale")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

Il vantaggio di usare lo `scale` fattore diventa evidente quando si desidera implementare un algoritmo leggermente diverso. Si supponga di che voler conservare le proporzioni della bitmap, ma anche riempire il rettangolo di destinazione. È l'unico modo è possibile per il ritaglio parte dell'immagine, ma è possibile implementare tale algoritmo semplicemente modificando `Math.Min` a `Math.Max` nel codice precedente. Ecco il risultato: 

[![In alternativa all'adattamento dei Uniform](displaying-images/UniformScaling-Alternative.png "ridimensionamento uniforme alternativa")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

Vengono mantenute le proporzioni della bitmap, ma vengono ritagliate le aree a sinistra e a destra della bitmap.

## <a name="a-versatile-bitmap-display-function"></a>Una funzione di visualizzazione di bitmap versatile

Ambienti di programmazione basato su XAML (ad esempio UWP e Xamarin.Forms) hanno una struttura per espandere o ridurre le dimensioni delle bitmap, mantenendo tuttavia le proporzioni. Sebbene SkiaSharp non include questa funzionalità, è possibile implementarlo manualmente. Il `BitmapExtensions` incluso nella classe la [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) applicazione viene illustrato come. La classe definisce due nuove `DrawBitmap` metodi che eseguono il calcolo delle proporzioni. Questi nuovi metodi sono metodi di estensione di `SKCanvas`.

Il nuovo `DrawBitmap` metodi includono un parametro di tipo `BitmapStretch`, un'enumerazione definita nel **BitmapExtensions.cs** file:

```csharp
public enum BitmapStretch
{
    None,
    Fill,
    Uniform,
    UniformToFill,
    AspectFit = Uniform,
    AspectFill = UniformToFill
}
```

Il `None`, `Fill`, `Uniform`, e `UniformToFill` membri sono identici a quelli nella piattaforma UWP [ `Stretch` ](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx) enumerazione. Xamarin.Forms simili [ `Aspect` ](xref:Xamarin.Forms.Aspect) enumerazione definisce i membri `Fill`, `AspectFit`, e `AspectFill`.

Il **ridimensionamento uniforme** pagina riportata sopra Centra la bitmap all'interno del rettangolo, ma è possibile altre opzioni, ad esempio il posizionamento della bitmap nel lato sinistro o destro del rettangolo, o la parte superiore o inferiore. Lo scopo del `BitmapAlignment` enumerazione:

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

Impostazioni di allineamento non hanno alcun effetto se usato con `BitmapStretch.Fill`.

Il primo `DrawBitmap` funzione di estensione contiene un rettangolo di destinazione, ma nessun rettangolo di origine. Valori predefiniti sono definiti in modo che se si desidera che la bitmap al centro, è necessario specificare solo un `BitmapStretch` membro:

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect dest, 
                                  BitmapStretch stretch, 
                                  BitmapAlignment horizontal = BitmapAlignment.Center, 
                                  BitmapAlignment vertical = BitmapAlignment.Center, 
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * bitmap.Width, scale * bitmap.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, display, paint);
        }
    }
    ···
}
```

Lo scopo principale di questo metodo consiste nel calcolare un fattore di scala denominato `scale` che viene quindi applicato alla larghezza della bitmap e all'altezza quando si chiama il `CalculateDisplayRect` (metodo). Si tratta del metodo che calcola un rettangolo per la visualizzazione della bitmap in base all'allineamento orizzontale e verticale:

```csharp
static class BitmapExtensions
{
    ···
    static SKRect CalculateDisplayRect(SKRect dest, float bmpWidth, float bmpHeight, 
                                       BitmapAlignment horizontal, BitmapAlignment vertical)
    {
        float x = 0;
        float y = 0;

        switch (horizontal)
        {
            case BitmapAlignment.Center:
                x = (dest.Width - bmpWidth) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                x = dest.Width - bmpWidth;
                break;
        }

        switch (vertical)
        {
            case BitmapAlignment.Center:
                y = (dest.Height - bmpHeight) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                y = dest.Height - bmpHeight;
                break;
        }

        x += dest.Left;
        y += dest.Top;

        return new SKRect(x, y, x + bmpWidth, y + bmpHeight);
    }
}
```

Il `BitmapExtensions` contiene un'ulteriore classe `DrawBitmap` metodo con un rettangolo di origine per la specifica di un subset della bitmap. Questo metodo è simile a quella del primo ad eccezione del fatto che il fattore di scala viene calcolato in base il `source` rettangolo e quindi applicate al `source` rettangolo nella chiamata a `CalculateDisplayRect`:

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect source, SKRect dest,
                                  BitmapStretch stretch,
                                  BitmapAlignment horizontal = BitmapAlignment.Center,
                                  BitmapAlignment vertical = BitmapAlignment.Center,
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, source, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / source.Width, dest.Height / source.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / source.Width, dest.Height / source.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * source.Width, scale * source.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, source, display, paint);
        }
    }
    ···
}
```

Il primo di questi due nuovi `DrawBitmap` metodi è illustrato nel **modalità di ridimensionamento** pagina. Il file XAML include tre `Picker` gli elementi che consentono di selezionare i membri del `BitmapStretch` e `BitmapAlignment` enumerazioni:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.ScalingModesPage"
             Title="Scaling Modes">

    <Grid Padding="10">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView" 
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="Stretch:"
               Grid.Row="1" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="stretchPicker"
                Grid.Row="1" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapStretch}">
                    <x:Static Member="local:BitmapStretch.None" />
                    <x:Static Member="local:BitmapStretch.Fill" />
                    <x:Static Member="local:BitmapStretch.Uniform" />
                    <x:Static Member="local:BitmapStretch.UniformToFill" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Horizontal Alignment:"
               Grid.Row="2" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="horizontalPicker" 
                Grid.Row="2" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Vertical Alignment:"
               Grid.Row="3" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="verticalPicker" 
                Grid.Row="3" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

Il file code-behind semplicemente invalida il `CanvasView` quando una qualsiasi `Picker` elemento è stato modificato. Il `PaintSurface` gestore accede a tre `Picker` viste per la chiamata di `DrawBitmap` metodo di estensione:

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public ScalingModesPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, dest, stretch, horizontal, vertical);
    }
}
```

Ecco alcune combinazioni di opzioni:

[![Modalità di ridimensionamento](displaying-images/ScalingModes.png "modalità di ridimensionamento")](displaying-images/ScalingModes-Large.png#lightbox)

Il **rettangolo Subset** pagina ha praticamente allo stesso file XAML **modalità di ridimensionamento**, ma il file code-behind definisce un sottoinsieme rettangolare della bitmap specificata dal `SOURCE` campo: 

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");

    static readonly SKRect SOURCE = new SKRect(94, 12, 212, 118);

    public RectangleSubsetPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, SOURCE, dest, stretch, horizontal, vertical);
    }
}
```

Questa origine rettangolo isola head della monkey, come illustrato nelle schermate illustrate:

[![Rettangolo Subset](displaying-images/RectangleSubset.png "Subset rettangolo")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
