---
title: Nozioni fondamentali di bitmap
description: Caricare immagini bitmap da diverse origini e visualizzarli.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: charlespetzold
ms.author: chape
ms.date: 04/03/2017
ms.openlocfilehash: b86068c2ed5063c25f76e81fdf477550b1437984
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="bitmap-basics"></a>Nozioni fondamentali di bitmap

_Caricare immagini bitmap da diverse origini e visualizzarli._

Il supporto delle bitmap in SkiaSharp è molto esteso. Questo articolo vengono illustrate le nozioni di base &mdash; come caricare bitmap e come visualizzarli:

![](bitmaps-images/bitmapssample.png "La visualizzazione di due bitmap")

Una bitmap SkiaSharp è un oggetto di tipo [ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/). Esistono diversi modi per creare una bitmap, ma in questo articolo si limita al [ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/SkiaSharp.SKStream/) metodo, che carica la bitmap da un [ `SKStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStream/) oggetto che fa riferimento a un file bitmap. È consigliabile usare il [ `SKManagedStream` ](https://developer.xamarin.com/api/type/SkiaSharp.SKManagedStream/) classe che deriva da `SKStream` perché contiene un costruttore che accetta un .NET [ `Stream` ](https://developer.xamarin.com/api/type/System.IO.Stream/) oggetto.

Il **bitmap base** nella pagina di **SkiaSharpFormsDemos** programma viene illustrato come caricare bitmap da tre origini diverse:

- Da Internet
- Da una risorsa incorporata nel file eseguibile
- Da Raccolta foto dell'utente

Tre `SKBitmap` oggetti per questi tre origini sono definite come campi di [ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) classe:

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

Per caricare una bitmap in base a un URL, è possibile utilizzare il [ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/) classe, come illustrato nel codice seguente eseguito nel `BasicBitmapsPage` costruttore. Qui l'URL punti a un'area del sito web Xamarin con alcune bitmap di esempio. Un pacchetto nel sito web consente di aggiungere una specifica per il ridimensionamento delle bitmap per una determinata larghezza:

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

Quando la bitmap è stata scaricata correttamente, il metodo di callback passato il `BeginGetResponse` esecuzione del metodo. Il `EndGetResponse` chiamata deve essere inserito in un `try` blocco nel caso in cui si è verificato un errore. Il `Stream` oggetto ottenuto dal `GetResponseStream` non è sufficiente in alcune piattaforme, pertanto il contenuto di bitmap viene copiato in un `MemoryStream` oggetto. A questo punto, il `SKManagedStream` oggetto può essere creato. A questo punto fa riferimento al file di mappa di bit, che è probabile che un file JPEG o PNG. Il `SKBitmap.Decode` metodo decodifica il file bitmap e archivia i risultati in un formato SkiaSharp interno.

Il metodo di callback passato a `BeginGetResponse` viene eseguito dopo che il costruttore ha completato l'esecuzione, il che significa che il `SKCanvasView` deve essere invalidati per consentire il `PaintSurface` gestore per aggiornare la visualizzazione. Tuttavia, il `BeginGetResponse` callback viene eseguito in un thread secondario di esecuzione, pertanto è necessario utilizzare `Device.BeginInvokeOnMainThread` per eseguire il `InvalidateSurface` metodo nel thread dell'interfaccia utente.

## <a name="loading-a-bitmap-resource"></a>Il caricamento di una risorsa Bitmap

In termini di codice, l'approccio più semplice per il caricamento delle bitmap includono una risorsa bitmap direttamente nell'applicazione. Il **SkiaSharpFormsDemos** programma include una cartella denominata **Media** contenente un file di mappa di bit denominato **monkey.png**. Nel **proprietà** finestra di dialogo per il file, è necessario assegnare tale file di un **azione di compilazione** di **risorsa incorporata**!

Ogni risorsa incorporata è un *ID risorsa* che include il nome del progetto, la cartella e il nome del file, tutti collegati da punti: **SkiaSharpFormsDemos.Media.monkey.png**. È possibile accedere a questa risorsa specificando tale risorsa ID come argomento per il [ `GetManifestResourceStream` ](https://developer.xamarin.com/api/member/System.Reflection.Assembly.GetManifestResourceStream/p/System.String/) metodo il [ `Assembly` ](https://developer.xamarin.com/api/type/System.Reflection.Assembly/) classe:

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
using (SKManagedStream skStream = new SKManagedStream(stream))
{
    resourceBitmap = SKBitmap.Decode(skStream);
}
```

Questo `Stream` oggetto può essere convertito direttamente a un `SKManagedStream` oggetto.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Il caricamento di una Bitmap dalla raccolta foto

È anche possibile che all'utente di caricare una foto dalla raccolta immagini del dispositivo. Questa funzionalità non è disponibile per xamarin. Forms stesso. Il processo richiede un servizio di dipendenza, ad esempio quello descritto nell'articolo [prelievo una foto dalla raccolta immagini di](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

Il **IPicturePicker.cs** file e i tre **PicturePickerImplementation.cs** sono stati copiati i file da tale articolo ai vari progetti del **SkiaSharpFormsDemos**soluzione e nuovi spazi dei nomi specificato. Inoltre, la Android **Mainactivity** file è stato modificato come descritto nell'articolo e il progetto iOS è stata concessa l'autorizzazione ad accedere alla raccolta foto con due righe nella parte inferiore del **Info. plist**  file.

Il `BasicBitmapsPage` costruttore aggiunge un `TapGestureRecognizer` per il `SKCanvasView` per ricevere una notifica di scelte. Al momento della ricezione di una scelta, il `Tapped` gestore ottiene accesso al servizio di dipendenza immagine di selezione e chiamate `GetImageStreamAsync`. Se un `Stream` viene restituito l'oggetto, quindi il contenuto viene copiato in un `MemoryStream`come richiesto da alcuni delle piattaforme. Il resto del codice è simile ad altre due tecniche:

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

Si noti che il `Tapped` chiamate del gestore di `InvalidateSurface` metodo il `SKCanvasView` oggetto. Questa operazione genera una nuova chiamata al `PaintSurface` gestore.

## <a name="displaying-the-bitmaps"></a>Visualizzazione di bitmap

Il `PaintSurface` gestore dovrà essere visualizzate tre immagini bitmap. Il gestore si presuppone che il telefono è in modalità verticale e divide verticalmente l'area di disegno in tre parti uguali.

Verrà visualizzata la bitmap prima con la più semplice [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/System.Single/System.Single/SkiaSharp.SKPaint/) metodo. Sono tutte che è necessario specificare le coordinate X e Y in cui verrà posizionato nell'angolo superiore sinistro della bitmap:

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Sebbene un `SKPaint` parametro è definito, ha un valore predefinito di `null` e può essere ignorato. Il pixel della bitmap semplicemente vengono trasferiti ai pixel dell'area di visualizzazione con un mapping uno a uno.

Un programma è possibile ottenere le dimensioni in pixel, di una bitmap con il [ `Width` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Width/) e [ `Height` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Height/) proprietà. Queste proprietà consentono di calcolare le coordinate per posizionare la mappa di bit al centro del terzo oggetto superiore dell'area di disegno:

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

Vengono visualizzate due bitmap con una versione di [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKPaint/) con un `SKRect` parametro:

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Una terza versione di [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/) dispone di due `SKRect` argomenti per la specifica di un sottoinsieme rettangolare della bitmap da visualizzare, ma tale versione non viene utilizzato in questo articolo.

Ecco il codice per visualizzare la mappa di bit caricato da una bitmap di risorse incorporato:

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

La bitmap viene estesa per le dimensioni del rettangolo, motivo per cui il monkey si estende orizzontalmente in queste schermate:

[![](bitmaps-images/basicbitmaps-small.png "Una schermata tripla della pagina base bitmap")](bitmaps-images/basicbitmaps-large.png#lightbox "una tripla schermata della pagina base bitmap")

La terza immagine &mdash; che è possibile visualizzare solo se si esegue il programma e se si carica una foto dalla raccolta di immagini &mdash; viene visualizzato anche all'interno di un rettangolo, ma il rettangolo di una posizione e le dimensioni vengono regolati per mantenere le proporzioni della bitmap. Questo calcolo è leggermente più complesso perché richiede il calcolo di un fattore in base alla dimensione della bitmap e il rettangolo di destinazione di scala e il rettangolo nell'area di allineamento al centro dei:

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

Se nessuna bitmap è ancora stata caricata dalla raccolta immagini, il `else` Visualizza il blocco di testo per richiedere all'utente di toccare lo schermo.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Selezione di una foto dalla raccolta immagini di](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
