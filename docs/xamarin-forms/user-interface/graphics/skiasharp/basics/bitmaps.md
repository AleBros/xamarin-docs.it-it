---
title: Nozioni fondamentali sulla bitmap in SkiaSharp
description: Questo articolo illustra come caricare le bitmap in SkiaSharp da diverse origini e visualizzarle nelle applicazioni Xamarin.Forms e illustra questa operazione con il codice di esempio.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 4aa73e1cba3f970396e5a52679372a160f47f7af
ms.sourcegitcommit: 3bf02ecac9a8855779e07eb1e7e27abb9fc38934
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2019
ms.locfileid: "74658268"
---
# <a name="bitmap-basics-in-skiasharp"></a>Nozioni fondamentali sulla bitmap in SkiaSharp

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Caricare le bitmap da varie origini e visualizzarle._

Il supporto delle bitmap in SkiaSharp è molto esteso. Questo articolo illustra solo le nozioni di base &mdash; come caricare le bitmap e come visualizzarle:

![](bitmaps-images/basicbitmaps-small.png "The display of two bitmaps")

Un'esplorazione più approfondita delle bitmap è disponibile nella sezione [bitmap SkiaSharp](../bitmaps/index.md).

Una bitmap SkiaSharp è un oggetto di tipo [`SKBitmap`](xref:SkiaSharp.SKBitmap). Esistono diversi modi per creare una bitmap, ma questo articolo si limita al metodo [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) , che carica la bitmap da un oggetto .NET `Stream`.

Nella pagina **bitmap di base** del programma **SkiaSharpFormsDemos** viene illustrato come caricare bitmap da tre origini diverse:

- Da Internet
- Da una risorsa incorporata nell'eseguibile
- Dalla raccolta foto dell'utente

Tre oggetti `SKBitmap` per queste tre origini sono definiti come campi nella classe [`BasicBitmapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) :

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

## <a name="loading-a-bitmap-from-the-web"></a>Caricamento di una bitmap dal Web

Per caricare una bitmap basata su un URL, è possibile usare la classe [`HttpClient`](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) . È necessario creare un'istanza di una sola istanza di `HttpClient` e riutilizzarla, quindi archiviarla come campo:

```csharp
HttpClient httpClient = new HttpClient();
```

Quando si usa `HttpClient` con le applicazioni iOS e Android, è necessario impostare le proprietà del progetto come descritto nei documenti su **[Transport Layer Security (TLS) 1,2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

Poiché è più pratico usare l'operatore `await` con `HttpClient`, il codice non può essere eseguito nel costruttore di `BasicBitmapsPage`. Ma fa parte dell'override del `OnAppearing`. L'URL fa riferimento a un'area nel sito Web Xamarin con alcune bitmap di esempio. Un pacchetto nel sito Web consente di aggiungere una specifica per ridimensionare la bitmap a una particolare larghezza:

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

            webBitmap = SKBitmap.Decode(memStream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

Il sistema operativo Android genera un'eccezione quando si usa il `Stream` restituito da `GetStreamAsync` nel metodo `SKBitmap.Decode` perché sta eseguendo un'operazione di lunga durata su un thread principale. Per questo motivo, il contenuto del file bitmap viene copiato in un oggetto `MemoryStream` usando `CopyToAsync`.

Il metodo `SKBitmap.Decode` statico è responsabile della decodifica dei file bitmap. Funziona con i formati di bitmap JPEG, PNG e GIF e archivia i risultati in un formato SkiaSharp interno. A questo punto, il `SKCanvasView` deve essere invalidato per consentire al gestore `PaintSurface` di aggiornare la visualizzazione.

## <a name="loading-a-bitmap-resource"></a>Caricamento di una risorsa bitmap

In termini di codice, l'approccio più semplice per il caricamento di bitmap è l'inclusione di una risorsa bitmap direttamente nell'applicazione. Il programma **SkiaSharpFormsDemos** include una cartella denominata **media** contenente diversi file bitmap, incluso un nome **Monkey. png**. Per le bitmap archiviate come risorse del programma, è necessario usare la finestra di dialogo **Proprietà** per assegnare al file un' **azione di compilazione** della **risorsa incorporata**.

Ogni risorsa incorporata dispone di un *ID di risorsa* costituito dal nome del progetto, dalla cartella e dal nome del file, tutti connessi da punti: **SkiaSharpFormsDemos. Media. Monkey. png**. È possibile ottenere l'accesso a questa risorsa specificando tale ID di risorsa come argomento per il metodo [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) della classe [`Assembly`](xref:System.Reflection.Assembly) :

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

Questo `Stream` oggetto può essere passato direttamente al metodo `SKBitmap.Decode`.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Caricamento di una bitmap dalla raccolta foto

È anche possibile che l'utente carichi una foto dalla libreria immagini del dispositivo. Questa funzionalità non viene fornita da Xamarin.Forms. Il processo richiede un servizio di dipendenza, ad esempio quello descritto nell'articolo [selezione di una foto dalla raccolta immagini](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

Il file **IPhotoLibrary.cs** nel progetto **SkiaSharpFormsDemos** e i tre file **Photolibrary.cs** nei progetti della piattaforma sono stati adattati a partire da questo articolo. Inoltre, il file Android **MainActivity.cs** è stato modificato come descritto nell'articolo e al progetto iOS è stata assegnata l'autorizzazione per accedere alla raccolta foto con due righe verso la fine del file **info. plist** .

Il costruttore `BasicBitmapsPage` aggiunge una `TapGestureRecognizer` al `SKCanvasView` per ricevere una notifica dei rubinetti. Alla ricezione di un tap, il gestore `Tapped` Ottiene l'accesso al servizio di dipendenza della selezione immagini e chiama `PickPhotoAsync`. Se viene restituito un oggetto `Stream`, viene passato al metodo `SKBitmap.Decode`:

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

Si noti che il gestore di `Tapped` chiama anche il metodo `InvalidateSurface` dell'oggetto `SKCanvasView`. Viene generata una nuova chiamata al gestore `PaintSurface`.

## <a name="displaying-the-bitmaps"></a>Visualizzazione delle bitmap

Il gestore `PaintSurface` deve visualizzare tre bitmap. Il gestore presuppone che il telefono sia in modalità verticale e divide verticalmente l'area di disegno in tre parti uguali.

La prima bitmap viene visualizzata con il metodo [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) più semplice. È sufficiente specificare le coordinate X e Y in cui posizionare l'angolo superiore sinistro della bitmap:

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Sebbene venga definito un `SKPaint` parametro, il valore predefinito è `null` ed è possibile ignorarlo. I pixel della bitmap vengono semplicemente trasferiti ai pixel della superficie di visualizzazione con un mapping uno-a-uno. Verrà visualizzata un'applicazione per questo `SKPaint` argomento nella sezione successiva sulla trasparenza di [**SkiaSharp**](transparency.md).

Un programma può ottenere le dimensioni in pixel di una bitmap con le proprietà [`Width`](xref:SkiaSharp.SKBitmap.Width) e [`Height`](xref:SkiaSharp.SKBitmap.Height) . Queste proprietà consentono al programma di calcolare le coordinate per posizionare la bitmap al centro del terzo superiore dell'area di disegno:

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

Le altre due bitmap vengono visualizzate con una versione di [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) con un parametro di `SKRect`:

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Una terza versione di [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) dispone di due argomenti `SKRect` per specificare un subset rettangolare della bitmap da visualizzare, ma tale versione non viene usata in questo articolo.

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

La bitmap viene estesa alle dimensioni del rettangolo, motivo per cui la scimmia viene estesa orizzontalmente nelle schermate seguenti:

[![](bitmaps-images/basicbitmaps-small.png "A triple screenshot of the Basic Bitmaps page")](bitmaps-images/basicbitmaps-large.png#lightbox "A triple screenshot of the Basic Bitmaps page")

La terza immagine &mdash; che è possibile vedere solo se si esegue il programma e si carica una foto dalla raccolta immagini &mdash; viene visualizzata anche all'interno di un rettangolo, ma la posizione e le dimensioni del rettangolo vengono modificate per mantenere le proporzioni della bitmap. Questo calcolo è un po' più impegnativo perché richiede il calcolo di un fattore di scalabilità in base alle dimensioni della bitmap e del rettangolo di destinazione e al centramento del rettangolo in tale area:

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

Se non è ancora stata caricata alcuna bitmap dalla raccolta immagini, il blocco `else` Visualizza del testo per richiedere all'utente di toccare la schermata.

È possibile visualizzare bitmap con diversi gradi di trasparenza e il prossimo articolo sulla trasparenza di [**SkiaSharp**](transparency.md) descrive come.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Selezione di una foto dalla Raccolta immagini](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
