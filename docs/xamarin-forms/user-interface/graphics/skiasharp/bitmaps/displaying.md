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
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304016"
---
# <a name="displaying-skiasharp-bitmaps"></a>Visualizzazione di bitmap di SkiaSharp

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

L'oggetto delle bitmap SkiaSharp è stato introdotto nell'articolo **[nozioni di base sulla bitmap in SkiaSharp](../basics/bitmaps.md)** . Questo articolo ha illustrato tre modi per bitmap di carico e i tre modi per visualizzare le bitmap. Questo articolo esamina le tecniche per caricare le bitmap e approfondisce l'uso dei metodi `DrawBitmap` di `SKCanvas`.

![Visualizzazione dell'esempio](displaying-images/DisplayingSample.png "Visualizzazione dell'esempio")

I metodi `DrawBitmapLattice` e `DrawBitmapNinePatch` sono descritti nell'articolo **[visualizzazione segmentata delle bitmap SkiaSharp](segmented.md)** .

Gli esempi in questa pagina sono dall'applicazione **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** . Dalla home page di tale applicazione scegliere **bitmap SkiaSharp**, quindi passare alla sezione **visualizzazione delle bitmap** .

## <a name="loading-a-bitmap"></a>Il caricamento di una bitmap

Una bitmap utilizzata da un'applicazione di SkiaSharp in genere deriva da una delle tre origini diverse:

- Da Internet
- Da una risorsa incorporata nel file eseguibile
- Da Raccolta foto dell'utente

È anche possibile per un'applicazione di SkiaSharp creare una nuova bitmap, quindi disegnare su di esso o impostare i bit della bitmap tramite algoritmo. Queste tecniche sono illustrate negli articoli **[creazione e disegno di bitmap SkiaSharp](drawing.md)** e **[accesso a SkiaSharp bitmap pixel](pixel-bits.md)** .

Nei tre esempi di codice seguenti relativi al caricamento di una bitmap, si presuppone che la classe contenga un campo di tipo `SKBitmap`:

```csharp
SKBitmap bitmap;
```

Come illustrato nell'articolo **[nozioni di base sulle bitmap in SkiaSharp](../basics/bitmaps.md)** , il modo migliore per caricare una bitmap su Internet è la classe [`HttpClient`](xref:System.Net.Http.HttpClient) . Una singola istanza della classe può essere definita come campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Quando si usa `HttpClient` con le applicazioni iOS e Android, è necessario impostare le proprietà del progetto come descritto nei documenti su **[Transport Layer Security (TLS) 1,2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

Il codice che usa `HttpClient` spesso implica l'operatore `await`, quindi deve trovarsi in un metodo di `async`:

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

Si noti che l'oggetto `Stream` ottenuto da `GetStreamAsync` viene copiato in un `MemoryStream`. Android non consente l'elaborazione da parte del `Stream` di `HttpClient` dal thread principale, eccetto nei metodi asincroni. 

Il [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) esegue una grande quantità di lavoro: l'oggetto `Stream` passato fa riferimento a un blocco di memoria contenente un'intera bitmap in uno dei formati di file bitmap comuni, generalmente JPEG, PNG o gif. Il metodo `Decode` deve determinare il formato e quindi decodificare il file bitmap nel formato bitmap interno di SkiaSharp.

Dopo che il codice ha chiamato `SKBitmap.Decode`, probabilmente invalida la `CanvasView` in modo che il gestore `PaintSurface` possa visualizzare la bitmap appena caricata.

Il secondo modo per caricare una bitmap, includendo la mappa di bit come risorsa incorporata nella libreria .NET Standard fanno riferimento i singoli progetti di piattaforma. Un ID di risorsa viene passato al metodo [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) . Questo ID di risorsa include il nome dell'assembly, nome della cartella e il nome della risorsa separato da punti:

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

Un terzo approccio per ottenere una bitmap è dalla raccolta immagini dell'utente. Il codice seguente usa un servizio di dipendenza incluso nell'applicazione **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** . La libreria .NET Standard **SkiaSharpFormsDemo** include l'interfaccia `IPhotoLibrary`, mentre ognuno dei progetti della piattaforma contiene una classe `PhotoLibrary` che implementa tale interfaccia.

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

In genere, tale codice invalida anche il `CanvasView` in modo che il gestore di `PaintSurface` possa visualizzare la nuova bitmap.

La classe `SKBitmap` definisce diverse proprietà utili, tra cui [`Width`](xref:SkiaSharp.SKBitmap.Width) e [`Height`](xref:SkiaSharp.SKBitmap.Height), che rivelano le dimensioni in pixel della bitmap, nonché molti metodi, inclusi i metodi per la creazione di bitmap, la copia e l'esposizione dei bit di pixel. 

## <a name="displaying-in-pixel-dimensions"></a>Visualizzazione in dimensioni in pixel

La classe SkiaSharp [`Canvas`](xref:SkiaSharp.SKCanvas) definisce quattro `DrawBitmap` metodi. Questi metodi consentono di bitmap da visualizzare in due modi fondamentalmente diversi: 

- Se si specifica un valore `SKPoint` (o valori `x` e `y` separati), la bitmap viene visualizzata nelle dimensioni dei pixel. I pixel della bitmap vengono eseguito il mapping direttamente al pixel dello schermo del video.
- Se si specifica un rettangolo, la bitmap per estenderla per le dimensioni del rettangolo. 

È possibile visualizzare una bitmap nelle dimensioni in pixel usando [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) con un parametro di `SKPoint` o [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) con parametri `x` e `y` distinti:

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

Questi due metodi sono funzionalmente identici. Il punto specificato indica la posizione dell'angolo superiore sinistro della bitmap rispetto all'area di disegno. Poiché la risoluzione i pixel dei dispositivi mobili è talmente elevata, più piccole bitmap visualizzato in genere abbastanza ridotti in questi dispositivi.

Il parametro `SKPaint` facoltativo consente di visualizzare la bitmap utilizzando la trasparenza. A tale scopo, creare un oggetto `SKPaint` e impostare la proprietà `Color` su qualsiasi valore `SKColor` con un canale alfa minore di 1. Ad esempio,

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

Il 0x80 passato come ultimo argomento indica la trasparenza di 50%. È anche possibile impostare un canale alfa su uno dei colori predefiniti:

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

Tuttavia, al colore stesso è irrilevante. Quando si usa l'oggetto `SKPaint` in una chiamata `DrawBitmap`, viene esaminato solo il canale alfa.

L'oggetto `SKPaint` inoltre svolge un ruolo quando si visualizzano le bitmap utilizzando le modalità di Blend o gli effetti di filtro. Questi argomenti sono illustrati negli articoli relativi alle [modalità di composizione e fusione di SkiaSharp](../effects/blend-modes/index.md) e ai [filtri di immagini SkiaSharp](../effects/image-filters.md).

La pagina **dimensioni pixel** nel programma di esempio **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** Visualizza una risorsa bitmap di 320 pixel di larghezza per 240 pixel di altezza:

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

Il gestore di `PaintSurface` centra la bitmap calcolando `x` e `y` i valori in base alle dimensioni in pixel della superficie di visualizzazione e alle dimensioni in pixel della bitmap:

[![Dimensioni pixel](displaying-images/PixelDimensions.png "Dimensioni pixel")](displaying-images/PixelDimensions-Large.png#lightbox)

Se l'applicazione desidera visualizzare la bitmap all'angolo superiore sinistro, lo passerebbe semplicemente le coordinate di (0, 0). 

## <a name="a-method-for-loading-resource-bitmaps"></a>Un metodo per il caricamento delle bitmap di risorsa

Molti degli esempi di presentarsi sarà necessario caricare le risorse di bitmap. La classe `BitmapExtensions` statica nella soluzione **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** contiene un metodo che consente di eseguire le operazioni seguenti:

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

Si noti il parametro `Type`. Può trattarsi dell'oggetto `Type` associato a qualsiasi tipo nell'assembly in cui è archiviata la risorsa bitmap.

Questo metodo di `LoadBitmapResource` verrà usato in tutti gli esempi successivi che richiedono risorse bitmap.

## <a name="stretching-to-fill-a-rectangle"></a>L'estensione per riempire un rettangolo

La classe `SKCanvas` definisce anche un metodo [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) che esegue il rendering della bitmap in un rettangolo e un altro metodo [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) che esegue il rendering di un subset rettangolare della bitmap in un rettangolo:

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

In entrambi i casi, la bitmap viene estesa in modo da riempire il rettangolo denominato `dest`. Nel secondo metodo, il rettangolo di `source` consente di selezionare un subset della bitmap. Il `dest` rettangolo è relativo al dispositivo di output; il `source` rettangolo è relativo alla bitmap.

Nella pagina **rettangolo di riempimento** viene illustrato il primo di questi due metodi visualizzando la stessa bitmap utilizzata nell'esempio precedente in un rettangolo con le stesse dimensioni dell'area di disegno: 

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

Si noti l'uso del nuovo metodo `BitmapExtensions.LoadBitmapResource` per impostare il campo `SKBitmap`. Il rettangolo di destinazione viene ottenuto dalla proprietà [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) di `SKImageInfo`, che viene descritto le dimensioni della superficie di visualizzazione:

[![Riempimento rettangolo](displaying-images/FillRectangle.png "Riempimento rettangolo")](displaying-images/FillRectangle-Large.png#lightbox)

Questo _non_ è in genere quello che si desidera. L'immagine risulta distorta dal verrà estesa in modo diverso nelle direzioni orizzontale e verticale. La visualizzazione di una mappa di bit in un valore diverso da relative dimensioni in pixel, in genere si desidera mantenere le proporzioni originali della bitmap.

## <a name="stretching-while-preserving-the-aspect-ratio"></a>L'estensione mantenendo le proporzioni

L'estensione di una bitmap mentre si conservano le proporzioni è un processo noto anche come _scalabilità uniforme_. A tale termine algoritmico suggerite. Una possibile soluzione è illustrata nella pagina **scalabilità uniforme** :

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

Il gestore di `PaintSurface` calcola un fattore di `scale` che rappresenta il valore minimo del rapporto tra la larghezza e l'altezza di visualizzazione e la larghezza e l'altezza della bitmap. È quindi possibile calcolare i valori `x` e `y` per centrare la bitmap ridimensionata all'interno della larghezza e dell'altezza di visualizzazione. Il rettangolo di destinazione ha un angolo superiore sinistro di `x` e `y` e un angolo inferiore destro di tali valori più la larghezza e l'altezza ridimensionate della bitmap:

[![Scalabilità uniforme](displaying-images/UniformScaling.png "Scalabilità uniforme")](displaying-images/UniformScaling-Large.png#lightbox)

Ruotare il telefono lateralmente per visualizzare la bitmap adattata per quell'area:

[![Orizzontale con scalabilità uniforme](displaying-images/UniformScaling-Landscape.png "Orizzontale con scalabilità uniforme")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

Il vantaggio dell'uso di questo `scale` fattore diventa evidente quando si desidera implementare un algoritmo leggermente diverso. Si supponga di che voler conservare le proporzioni della bitmap, ma anche riempire il rettangolo di destinazione. L'unico modo possibile è ritagliare una parte dell'immagine, ma è possibile implementare tale algoritmo semplicemente modificando `Math.Min` per `Math.Max` nel codice riportato sopra. Il risultato è il seguente: 

[![Alternativa di scalabilità uniforme](displaying-images/UniformScaling-Alternative.png "Alternativa di scalabilità uniforme")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

Vengono mantenute le proporzioni della bitmap, ma vengono ritagliate le aree a sinistra e a destra della bitmap.

## <a name="a-versatile-bitmap-display-function"></a>Una funzione di visualizzazione di bitmap versatile

Ambienti di programmazione basato su XAML (ad esempio UWP e xamarin. Forms) hanno una struttura per espandere o ridurre le dimensioni delle bitmap, mantenendo tuttavia le proporzioni. Sebbene SkiaSharp non include questa funzionalità, è possibile implementarlo manualmente. La classe `BitmapExtensions` inclusa nell'applicazione [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) Mostra come. La classe definisce due nuovi metodi di `DrawBitmap` che eseguono il calcolo delle proporzioni. Questi nuovi metodi sono metodi di estensione di `SKCanvas`.

I nuovi metodi di `DrawBitmap` includono un parametro di tipo `BitmapStretch`, un'enumerazione definita nel file **BitmapExtensions.cs** :

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

I membri `None`, `Fill`, `Uniform`e `UniformToFill` corrispondono a quelli dell'enumerazione [`Stretch`](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx) UWP. L'enumerazione [`Aspect`](xref:Xamarin.Forms.Aspect) Novell. Forms analoga definisce i membri `Fill`, `AspectFit`e `AspectFill`.

La pagina di **scalabilità uniforme** illustrata sopra centra la bitmap all'interno del rettangolo, ma è possibile che si desiderino altre opzioni, ad esempio il posizionamento della bitmap sul lato sinistro o destro del rettangolo o la parte superiore o inferiore. Questo è lo scopo dell'enumerazione `BitmapAlignment`:

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

Le impostazioni di allineamento non hanno effetto se utilizzate con `BitmapStretch.Fill`.

La prima funzione di estensione `DrawBitmap` contiene un rettangolo di destinazione ma nessun rettangolo di origine. Le impostazioni predefinite sono definite in modo che, se si desidera che la bitmap sia centrata, è necessario specificare solo un membro `BitmapStretch`:

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

Lo scopo principale di questo metodo è quello di calcolare un fattore di scala denominato `scale` che viene quindi applicato alla larghezza e all'altezza della bitmap quando si chiama il metodo `CalculateDisplayRect`. Si tratta del metodo che calcola un rettangolo per la visualizzazione della bitmap in base all'allineamento orizzontale e verticale:

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

La classe `BitmapExtensions` contiene un metodo di `DrawBitmap` aggiuntivo con un rettangolo di origine per la specifica di un subset della bitmap. Questo metodo è simile al primo, ad eccezione del fatto che il fattore di scala viene calcolato in base al rettangolo `source` e quindi applicato al rettangolo di `source` nella chiamata a `CalculateDisplayRect`:

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

Il primo di questi due nuovi metodi di `DrawBitmap` viene illustrato nella pagina **modalità di ridimensionamento** . Il file XAML contiene tre elementi `Picker` che consentono di selezionare i membri delle enumerazioni `BitmapStretch` e `BitmapAlignment`:

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

Il file code-behind invalida semplicemente il `CanvasView` quando viene modificato un elemento di `Picker`. Il gestore `PaintSurface` accede alle tre visualizzazioni `Picker` per chiamare il metodo di estensione `DrawBitmap`:

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

[![Modalità di ridimensionamento](displaying-images/ScalingModes.png "Modalità di ridimensionamento")](displaying-images/ScalingModes-Large.png#lightbox)

La pagina di **subset rettangolo** presenta praticamente lo stesso file XAML delle **modalità di ridimensionamento**, ma il file code-behind definisce un subset rettangolare della bitmap specificata dal campo `SOURCE`: 

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

[![Subset rettangolo](displaying-images/RectangleSubset.png "Subset rettangolo")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
