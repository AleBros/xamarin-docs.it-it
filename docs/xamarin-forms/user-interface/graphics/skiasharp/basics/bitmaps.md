---
title: Nozioni di base di bitmap in SkiaSharp
description: Questo articolo illustra come caricare le immagini bitmap in SkiaSharp da diverse origini e visualizzarli nelle applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: 92863ff9e843cabc26c568e95aab52c6d199c35e
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615209"
---
# <a name="bitmap-basics-in-skiasharp"></a>Nozioni di base di bitmap in SkiaSharp

_Caricare immagini bitmap da diverse origini e visualizzarli._

Il supporto delle bitmap in SkiaSharp è piuttosto ampio. Questo articolo illustra le nozioni di base &mdash; come caricare bitmap e come visualizzarli:

![](bitmaps-images/bitmapssample.png "La visualizzazione delle due bitmap")

Una quantità eccessiva un'esplorazione più approfondita delle bitmap sono reperibili nella sezione [SkiaSharp Bitmaps](../bitmaps/index.md).

Una bitmap di SkiaSharp è un oggetto di tipo [ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/). Esistono diversi modi per creare una bitmap, ma in questo articolo si limita al [ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/System.IO.Stream/) metodo, che carica la bitmap da un .NET `Stream` oggetto.

Il **base bitmap** pagina il **SkiaSharpFormsDemos** programma illustra come caricare bitmap da tre origini diverse:

- Da Internet
- Da una risorsa incorporata nel file eseguibile
- Da Raccolta foto dell'utente

Tre `SKBitmap` gli oggetti per questi tre origini vengono definite come campi nel [ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) classe:

```csharp
public class BasicBitmapsPage : ContentPage
{
    SKCanvasView canvasView;
    SKBitmap webBitmap;
    SKBitmap resourceBitmap;
    SKBitmap libraryBitmap;

    public BasicBitmapsPage()
    {
        Title = "Basic Bitmaps";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ...
    }
    ...
}
```

## <a name="loading-a-bitmap-from-the-web"></a>Il caricamento di una Bitmap dal Web

Per caricare una bitmap in base a un URL, è possibile usare la [ `HttpClient` ](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) classe. È consigliabile creare solo un'istanza di `HttpClient` e usarlo di nuovo, quindi archiviarlo come campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Quando si usa `HttpClient` con applicazioni iOS e Android, è opportuno impostare le proprietà del progetto come descritto nei documenti sul  **[Transport Layer Security (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)**.

Perché è più comodo usare la `await` operatore con `HttpClient`, il codice non può essere eseguito nel `BasicBitmapsPage` costruttore. Al contrario, fa parte di `OnAppearing` eseguire l'override. Qui l'URL punti a un'area del sito web Xamarin con esempio abbiamo delle bitmap. Un pacchetto nel sito web consente di aggiungere una specifica per il ridimensionamento delle bitmap a una determinata larghezza:


```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Load web bitmap.
    string url = "https://developer.xamarin.com/demo/IMG_3256.JPG?width=480";

    try
    {
        using (Stream stream = await httpClient.GetStreamAsync(url))
        using (MemoryStream memStream = new MemoryStream())
        {
            await stream.CopyToAsync(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            webBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

Android genererà un'eccezione quando si usa la `Stream` restituito da `GetStreamAsync` nel `SKBitmap.Decode` metodo perché sta eseguendo un'operazione di lunga durata su un thread principale. Per questo motivo, il contenuto del file bitmap viene copiato a una `MemoryStream` dell'oggetto usando `CopyToAsync`.

Il metodo statico `SKBitmap.Decode` metodo è responsabile per la decodifica di file bitmap. Funziona con JPEG, PNG, GIF e numerosi altri formati di bitmap più diffusi e archivia i risultati in un formato di SkiaSharp interno. A questo punto, il `SKCanvasView` deve essere invalidata per consentire il `PaintSurface` gestore per aggiornare la visualizzazione. 

## <a name="loading-a-bitmap-resource"></a>Il caricamento di una risorsa Bitmap

In termini di codice, l'approccio più semplice per il caricamento delle bitmap è inclusa una risorsa bitmap direttamente nell'applicazione. Il **SkiaSharpFormsDemos** programma include una cartella denominata **supporti** contenenti un file di mappa di bit denominato **monkey.png**. Nel **delle proprietà** finestra di dialogo per questo file, è necessario specificare tale file di un **azione di compilazione** del **risorsa incorporata**!

Ogni risorsa incorporata è una *l'ID di risorsa* costituito il nome del progetto, la cartella e il nome del file, connesse tutte le soluzioni da un punto: **SkiaSharpFormsDemos.Media.monkey.png**. È possibile ottenere l'accesso a questa risorsa, specificando tale risorsa ID come argomento per il [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) metodo per il [ `Assembly` ](xref:System.Reflection.Assembly) classe:

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

Ciò `Stream` oggetto può essere passato direttamente al `SKBitmap.Decode` (metodo).

## <a name="loading-a-bitmap-from-the-photo-library"></a>Il caricamento di una Bitmap da Raccolta foto

È anche possibile che l'utente caricare una foto dalla raccolta immagini del dispositivo. Questa funzionalità non viene fornita da xamarin. Forms stesso. Il processo richiede un servizio di dipendenza, come quello descritto nell'articolo [selezionando una foto dalla raccolta immagini di](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

Il **IPhotoLibrary.cs** del file nei **SkiaSharpFormsDemos** progetti e i tre **PhotoLibrary.cs** file nei progetti di piattaforma sono stati adattati in quell'articolo. Inoltre, l'Android **MainActivity.cs** file modificato come descritto nell'articolo e il progetto iOS è stato assegnato l'autorizzazione per accedere alla libreria di foto con due linee nella parte inferiore del **Info. plist**  file.

Il `BasicBitmapsPage` costruttore aggiunge un `TapGestureRecognizer` per il `SKCanvasView` per ricevere una notifica di elementi di ritardo. Al momento della ricezione di un tocco, il `Tapped` gestore ottiene accesso al servizio di dipendenza di selezione di immagini e le chiamate `GetImageStreamAsync`. Se un `Stream` viene restituito l'oggetto, quindi il contenuto viene copiato in un `MemoryStream`, come richiesto da alcune delle piattaforme. Il resto del codice è simile per le altre due tecniche:

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();

    using (Stream stream = await photoLibrary.PickPhotoAsync())
    {
        if (stream != null)
        {
            libraryBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        }
    }
};
canvasView.GestureRecognizers.Add(tapRecognizer);
```

Si noti che il `Tapped` chiamate del gestore di `InvalidateSurface` metodo il `SKCanvasView` oggetto. Questo genera una nuova chiamata per il `PaintSurface` gestore.

## <a name="displaying-the-bitmaps"></a>Visualizzare le bitmap

Il `PaintSurface` gestore deve visualizzare tre immagini bitmap. Il gestore si presuppone che il telefono è in modalità verticale e divide verticalmente l'area di disegno in tre parti uguali.

Verrà visualizzata la bitmap prima con la più semplice [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/System.Single/System.Single/SkiaSharp.SKPaint/) (metodo). Sono tutte che è necessario specificare le coordinate X e Y in cui deve essere posizionato l'angolo superiore sinistro della bitmap:

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Sebbene un' `SKPaint` parametro è definito, ha un valore predefinito di `null` ed è possibile ignorarlo. I pixel dell'area di visualizzazione con un mapping uno a uno vengono semplicemente trasferiti i pixel della bitmap.

Un programma può ottenere le dimensioni in pixel della bitmap con il [ `Width` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Width/) e [ `Height` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Height/) proprietà. Queste proprietà consentono di calcolare le coordinate per posizionare la bitmap al centro della terza superiore dell'area di disegno:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    if (webBitmap != null)
    {
        float x = (info.Width - webBitmap.Width) / 2;
        float y = (info.Height / 3 - webBitmap.Height) / 2;
        canvas.DrawBitmap(webBitmap, x, y);
    }
    ...
}
```

Le altre due bitmap vengono visualizzate con una versione di [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKPaint/) con un `SKRect` parametro:

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Una terza versione di [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/) dispone di due `SKRect` argomenti che consentono di specificare un sottoinsieme rettangolare della bitmap da visualizzare, ma tale versione non viene usato in questo articolo.

Ecco il codice per visualizzare la bitmap caricata da una bitmap di risorsa incorporata:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (resourceBitmap != null)
    {
        canvas.DrawBitmap(resourceBitmap,
            new SKRect(0, info.Height / 3, info.Width, 2 * info.Height / 3));
    }
    ...
}
```

La bitmap viene adattata alle dimensioni del rettangolo, motivo per cui il monkey in senso orizzontale si estende in tali schermate:

[![](bitmaps-images/basicbitmaps-small.png "Triplo screenshot della pagina base bitmap")](bitmaps-images/basicbitmaps-large.png#lightbox "triplo screenshot della pagina base bitmap")

La terza immagine &mdash; che è possibile visualizzare solo se si esegue il programma e carica una foto dalla raccolta immagini &mdash; viene visualizzato anche all'interno di un rettangolo, ma il rettangolo posizione e dimensioni vengono regolate per mantenere le proporzioni della bitmap. Questo calcolo è leggermente più complesso perché richiede il calcolo di un fattore in base alla dimensione della bitmap e il rettangolo di destinazione di scala e ruotava il rettangolo in quell'area:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (libraryBitmap != null)
    {
        float scale = Math.Min((float)info.Width / libraryBitmap.Width,
                               info.Height / 3f / libraryBitmap.Height);

        float left = (info.Width - scale * libraryBitmap.Width) / 2;
        float top = (info.Height / 3 - scale * libraryBitmap.Height) / 2;
        float right = left + scale * libraryBitmap.Width;
        float bottom = top + scale * libraryBitmap.Height;
        SKRect rect = new SKRect(left, top, right, bottom);
        rect.Offset(0, 2 * info.Height / 3);

        canvas.DrawBitmap(libraryBitmap, rect);
    }
    else
    {
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Blue;
            paint.TextAlign = SKTextAlign.Center;
            paint.TextSize = 48;

            canvas.DrawText("Tap to load bitmap",
                info.Width / 2, 5 * info.Height / 6, paint);
        }
    }
}
```

Se nessuna bitmap è ancora stata caricata dalla raccolta immagini, il `else` blocca la visualizzazione di testo per richiedere all'utente di toccare la schermata.


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Selezione di una foto dalla raccolta immagini di](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
