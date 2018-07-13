---
title: Nozioni di base di bitmap in SkiaSharp
description: Questo articolo illustra come caricare le immagini bitmap in SkiaSharp da diverse origini e visualizzarli nelle applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: charlespetzold
ms.author: chape
ms.date: 04/03/2017
ms.openlocfilehash: dec6fa1534f14836ae98677ad33e280ff510fb97
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995189"
---
# <a name="bitmap-basics-in-skiasharp"></a>Nozioni di base di bitmap in SkiaSharp

_Caricare immagini bitmap da diverse origini e visualizzarli._

Il supporto delle bitmap in SkiaSharp è piuttosto ampio. Questo articolo illustra le nozioni di base &mdash; come caricare bitmap e come visualizzarli:

![](bitmaps-images/bitmapssample.png "La visualizzazione delle due bitmap")

Una bitmap di SkiaSharp è un oggetto di tipo [ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/). Esistono diversi modi per creare una bitmap, ma in questo articolo si limita al [ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/SkiaSharp.SKStream/) metodo, che carica la bitmap da un [ `SKStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStream/) oggetto che fa riferimento a un file bitmap. È consigliabile usare la [ `SKManagedStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKManagedStream/) classe che deriva da `SKStream` perché contiene un costruttore che accetta un .NET [ `Stream` ](xref:System.IO.Stream) oggetto.

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

Per caricare una bitmap in base a un URL, è possibile usare la [ `WebRequest` ](xref:System.Net.WebRequest) classe, come illustrato nel codice seguente eseguito nel `BasicBitmapsPage` costruttore. Qui l'URL punti a un'area del sito web Xamarin con esempio abbiamo delle bitmap. Un pacchetto nel sito web consente di aggiungere una specifica per il ridimensionamento delle bitmap a una determinata larghezza:

```csharp
Uri uri = new Uri("http://developer.xamarin.com/demo/IMG_3256.JPG?width=480");
WebRequest request = WebRequest.Create(uri);
request.BeginGetResponse((IAsyncResult arg) =>
{
    try
    {
        using (Stream stream = request.EndGetResponse(arg).GetResponseStream())
        using (MemoryStream memStream = new MemoryStream())
        {
            stream.CopyTo(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            using (SKManagedStream skStream = new SKManagedStream(memStream))
            {
                webBitmap = SKBitmap.Decode(skStream);
            }
        }
    }
    catch
    {
    }

    Device.BeginInvokeOnMainThread(() => canvasView.InvalidateSurface());

}, null);
```

Quando la bitmap è stata scaricata correttamente, il metodo di callback passato al `BeginGetResponse` esecuzione del metodo. Il `EndGetResponse` chiamata deve essere inserito in un `try` blocco nel caso in cui si è verificato un errore. Il `Stream` ottenuto dall'oggetto `GetResponseStream` non è adeguata in alcune piattaforme, pertanto il contenuto di bitmap viene copiato in un `MemoryStream` oggetto. A questo punto, il `SKManagedStream` oggetto può essere creato. In questo momento fa riferimento al file bitmap, che è probabile che un file JPEG o PNG. Il `SKBitmap.Decode` metodo decodifica il file bitmap e archivia i risultati in un formato di SkiaSharp interno.

Il metodo di callback passato a `BeginGetResponse` viene eseguito dopo che il costruttore è stata eseguita, il che significa che il `SKCanvasView` deve essere invalidata per consentire il `PaintSurface` gestore per aggiornare la visualizzazione. Tuttavia, il `BeginGetResponse` callback viene eseguito in un thread secondario dell'esecuzione, pertanto è necessario usare `Device.BeginInvokeOnMainThread` per eseguire il `InvalidateSurface` (metodo) nel thread dell'interfaccia utente.

## <a name="loading-a-bitmap-resource"></a>Il caricamento di una risorsa Bitmap

In termini di codice, l'approccio più semplice per il caricamento delle bitmap è inclusa una risorsa bitmap direttamente nell'applicazione. Il **SkiaSharpFormsDemos** programma include una cartella denominata **supporti** contenenti un file di mappa di bit denominato **monkey.png**. Nel **delle proprietà** finestra di dialogo per questo file, è necessario specificare tale file di un **azione di compilazione** del **risorsa incorporata**!

Ogni risorsa incorporata è una *l'ID di risorsa* costituito il nome del progetto, la cartella e il nome del file, connesse tutte le soluzioni da un punto: **SkiaSharpFormsDemos.Media.monkey.png**. È possibile ottenere l'accesso a questa risorsa, specificando tale risorsa ID come argomento per il [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) metodo per il [ `Assembly` ](xref:System.Reflection.Assembly) classe:

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
using (SKManagedStream skStream = new SKManagedStream(stream))
{
    resourceBitmap = SKBitmap.Decode(skStream);
}
```

Ciò `Stream` oggetto può essere convertito direttamente in un `SKManagedStream` oggetto.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Il caricamento di una Bitmap da Raccolta foto

È anche possibile che l'utente caricare una foto dalla raccolta immagini del dispositivo. Questa funzionalità non viene fornita da xamarin. Forms stesso. Il processo richiede un servizio di dipendenza, come quello descritto nell'articolo [selezionando una foto dalla raccolta immagini di](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

Il **IPicturePicker.cs** file e i tre **PicturePickerImplementation.cs** sono stati copiati i file in quell'articolo i vari progetti del **SkiaSharpFormsDemos**soluzione viene assegnato il nuovo spazio dei nomi. Inoltre, l'Android **MainActivity.cs** file modificato come descritto nell'articolo e il progetto iOS è stato assegnato l'autorizzazione per accedere alla libreria di foto con due linee nella parte inferiore del **Info. plist**  file.

Il `BasicBitmapsPage` costruttore aggiunge un `TapGestureRecognizer` per il `SKCanvasView` per ricevere una notifica di elementi di ritardo. Al momento della ricezione di un tocco, il `Tapped` gestore ottiene accesso al servizio di dipendenza di selezione di immagini e le chiamate `GetImageStreamAsync`. Se un `Stream` viene restituito l'oggetto, quindi il contenuto viene copiato in un `MemoryStream`, come richiesto da alcune delle piattaforme. Il resto del codice è simile per le altre due tecniche:

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPicturePicker picturePicker = DependencyService.Get<IPicturePicker>();

    using (Stream stream = await picturePicker.GetImageStreamAsync())
    {
        if (stream != null)
        {
            using (MemoryStream memStream = new MemoryStream())
            {
                stream.CopyTo(memStream);
                memStream.Seek(0, SeekOrigin.Begin);

                using (SKManagedStream skStream = new SKManagedStream(memStream))
                {
                    libraryBitmap = SKBitmap.Decode(skStream);
                }
            }
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
