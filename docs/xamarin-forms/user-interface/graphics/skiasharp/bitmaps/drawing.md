---
title: Creazione e disegno nella bitmap di SkiaSharp
description: Informazioni su come creare SkiaSharp bitmap e quindi disegnare su tali bitmap tramite la creazione di un'area di disegno per ognuna di esse.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 1e2b50a260ed5f5bbbbfc3c4ba55a33075262f25
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228106"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>Creazione e disegno nella bitmap di SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Si è visto come un'applicazione può caricare bitmap dal Web, dalle risorse dell'applicazione e dalla libreria foto dell'utente. È anche possibile creare nuove bitmap all'interno dell'applicazione. L'approccio più semplice prevede uno dei costruttori del [ `SKBitmap` ](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)):

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

Il `width` e `height` parametri sono numeri interi e specificare le dimensioni in pixel della bitmap. Questo costruttore crea una bitmap a colori con quattro byte per pixel: un byte ogni per il rosso, verde, blu e i componenti alfa (opacity).

Dopo aver creato una nuova bitmap, è necessario ottenere un elemento sulla superficie della bitmap. In genere eseguire questa operazione in uno dei due modi:

- Disegnare sulla bitmap tramite standard `Canvas` i metodi di disegno.
- Accedere direttamente i bit di pixel.

Questo articolo illustra il primo approccio:

![Esempio di disegno](drawing-images/DrawingSample.png "disegno di esempio")

Il secondo approccio è descritto nell'articolo [ **l'accesso a pixel Bitmap SkiaSharp**](pixel-bits.md).

## <a name="drawing-on-the-bitmap"></a>Disegnare sulla bitmap

Disegno sulla superficie di una bitmap è lo stesso come disegno su uno schermo video. Per disegnare su uno schermo video, per ottenere un `SKCanvas` dall'oggetto di `PaintSurface` argomenti dell'evento. Per creare una bitmap, si crea un' `SKCanvas` utilizzando il [ `SKCanvas` ](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap)) costruttore:

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

Quando hai finito disegnare sulla bitmap, è possibile eliminare il `SKCanvas` oggetto. Per questo motivo, il `SKCanvas` costruttore viene chiamato in genere un `using` istruzione:

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

La mappa di bit può quindi essere visualizzato. In un secondo momento, è possibile creare un nuovo `SKCanvas` oggetto basato sulla stessa bitmap e disegnare su di esso ancora.

Il **Hello Bitmap** pagina il **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** applicazione scrive il testo "Hello, Bitmap!" in una bitmap e visualizza tale bitmap più volte.

Il costruttore del `HelloBitmapPage` inizia creando un `SKPaint` oggetto per la visualizzazione di testo. Determina le dimensioni della stringa di testo e crea una bitmap con tali dimensioni. Viene quindi creato un `SKCanvas` oggetto basato sull'immagine, chiamate `Clear`e quindi chiama `DrawText`. È sempre una buona idea per chiamare `Clear` con una nuova bitmap perché una bitmap appena creata potrebbe contenere dati casuali.

Il costruttore si conclude con la creazione di un `SKCanvasView` oggetto per visualizzare la mappa di bit:

```csharp
public partial class HelloBitmapPage : ContentPage
{
    const string TEXT = "Hello, Bitmap!";
    SKBitmap helloBitmap;

    public HelloBitmapPage()
    {
        Title = TEXT;

        // Create bitmap and draw on it
        using (SKPaint textPaint = new SKPaint { TextSize = 48 })
        {
            SKRect bounds = new SKRect();
            textPaint.MeasureText(TEXT, ref bounds);

            helloBitmap = new SKBitmap((int)bounds.Right,
                                       (int)bounds.Height);

            using (SKCanvas bitmapCanvas = new SKCanvas(helloBitmap))
            {
                bitmapCanvas.Clear();
                bitmapCanvas.DrawText(TEXT, 0, -bounds.Top, textPaint);
            }
        }

        // Create SKCanvasView to view result
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Aqua);

        for (float y = 0; y < info.Height; y += helloBitmap.Height)
            for (float x = 0; x < info.Width; x += helloBitmap.Width)
            {
                canvas.DrawBitmap(helloBitmap, x, y);
            }
    }
}
```

Il `PaintSurface` gestore esegue il rendering di bitmap più volte in righe e colonne della visualizzazione. Si noti che il `Clear` metodo nella `PaintSurface` gestore di è un argomento di `SKColors.Aqua`, quali i colori di sfondo dell'area di visualizzazione:

[![Hello, Bitmap. ](drawing-images/HelloBitmap.png "Hello, Bitmap.")](drawing-images/HelloBitmap-Large.png#lightbox)

L'aspetto dello sfondo azzurro rivela che la bitmap è trasparente ad eccezione del testo.

## <a name="clearing-and-transparency"></a>La cancellazione e la trasparenza

La visualizzazione delle **Hello Bitmap** pagina dimostra che la mappa di bit del programma creato è trasparente, ad eccezione di testo di colore nero. Ecco perché viene visualizzato il colore azzurro dell'area di visualizzazione attraverso.

La documentazione relativa ai `Clear` metodi di `SKCanvas` li descrive con l'istruzione: "Sostituisce tutti i pixel nell'area di disegno ' Current clip". L'uso della parola "sostituisce" rivela una caratteristica importante di questi metodi: Tutti i metodi di disegno `SKCanvas` di aggiungono qualcosa alla superficie di visualizzazione esistente. Il `Clear` metodi _sostituire_ ciò che è già presente.

`Clear` è disponibile in due diverse versioni:

- Il [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor)) metodo con un `SKColor` parametro sostituisce i pixel dell'area di visualizzazione con pixel del colore.

- Il [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear) metodo senza parametri consente di sostituire i pixel con le [ `SKColors.Empty` ](xref:SkiaSharp.SKColors.Empty) colore, che è un colore in cui tutti i componenti (rosso, verde, blu e alfa) vengono impostati su zero. Questo colore è talvolta detta "nero trasparente".

La chiamata `Clear` senza argomenti in una nuova bitmap Inizializza l'intera bitmap da completamente trasparente. Qualsiasi elemento successivamente disegnare sulla bitmap in genere è opaco o parzialmente opaco.

Ecco alcuni elementi da provare: Nella pagina **Hello bitmap** sostituire il `Clear` `bitmapCanvas` metodo applicato a con quello seguente:

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

L'ordine dei `SKColor` i parametri del costruttore è rosso, verde, blu e alfa, in cui ogni valore può variare da 0 a 255. Tenere presente che un valore alfa pari a 0 è trasparente, mentre un valore alfa pari a 255 è opaco.

Il valore (255, 0, 0, 128) Cancella pixel della bitmap per pixel rossa con un'opacità al 50%. Ciò significa che lo sfondo bitmap è semi-trasparente. Lo sfondo rosso semi-trasparente dell'immagine bitmap combina con lo sfondo dell'area di visualizzazione per creare uno sfondo grigio azzurro.

Provare a impostare il colore del testo su nero trasparente, inserendo l'assegnazione seguente `SKPaint` inizializzatore:

```csharp
Color = new SKColor(0, 0, 0, 0)
```

Si potrebbe pensare che questo testo trasparente creerebbe completamente trasparente aree della mappa di bit tramite cui è possibile visualizzare lo sfondo azzurro dell'area di visualizzazione. Ma che sono inesatto. Il testo viene disegnato sopra cosa si sta già la mappa di bit. Il testo trasparente non saranno visibile affatto.

Non `Draw` metodo mai effettua una bitmap più trasparente. Solo `Clear` a tale scopo.

## <a name="bitmap-color-types"></a>Tipi di bitmap colore

La più semplice `SKBitmap` costruttore consente di specificare una larghezza in pixel intero e l'altezza della bitmap. Altri `SKBitmap` costruttori sono più complessi. Questi costruttori richiedono argomenti di due tipi di enumerazione: [ `SKColorType` ](xref:SkiaSharp.SKColorType) e [ `SKAlphaType` ](xref:SkiaSharp.SKAlphaType). Altri costruttori usano il [ `SKImageInfo` ](xref:SkiaSharp.SKImageInfo) struttura, che consente di consolidare le informazioni.

Il `SKColorType` enumerazione è 9 membri. Ognuno di questi membri descrive una particolare modalità di archiviazione dei pixel della bitmap:

- `Unknown`
- `Alpha8` &mdash; ogni pixel è di 8 bit, che rappresenta un valore alfa da completamente trasparente a completamente opaco
- `Rgb565` &mdash; ogni pixel è di 16 bit, 5 bit per rosso e blu e 6 per il verde
- `Argb4444` &mdash; ogni pixel è di 16 bit, 4 per alfa, rosso, verde e blu
- `Rgba8888` &mdash; ogni pixel è di 32 bit, 8 per rosso, verde, blu e alfa
- `Bgra8888` &mdash; ogni pixel è di 32 bit, 8 per il blu, verde, rosso e alfa
- `Index8` &mdash; ogni pixel è 8 bit e rappresenta un indice in un [`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8` &mdash; ciascun pixel corrisponde a 8 bit che rappresenta una gradazione di grigio dal nero al bianco
- `RgbaF16` &mdash; ogni pixel è a 64 bit, rosso, verde, blu e alfa in un formato a virgola mobile a 16 bit

I due formati in cui ogni pixel ha una dimensione di 32 pixel (4 byte) sono spesso chiamati _a colori_ formati. Molte delle altre data formati da un tempo quando viene visualizzato se stessi video non è stato in grado di tutti i colori. Bitmap di colore limitato erano adeguate per questi display e bitmap a occupare meno spazio in memoria è consentito.

Oggi, i programmatori quasi sempre usano bitmap a colori e proviate con altri formati. L'eccezione è il `RgbaF16` formato, che consente la risoluzione di colore maggiore rispetto a anche i formati a colori. Tuttavia, questo formato viene usato per scopi particolari, ad esempio di diagnostica per immagini e non ha molto senso se usato con schermi a colori standard.

Questa serie di articoli limiterà per il `SKBitmap` formati utilizzati per impostazione predefinita se non si specifica di colori `SKColorType` membro viene specificato. Questo formato predefinito è basato sulla piattaforma sottostante. Per le piattaforme supportate da xamarin. Forms, il tipo di colore predefinito è:

- `Rgba8888` per iOS e Android
- `Bgra8888` per la piattaforma UWP

L'unica differenza è l'ordine di 4 byte in memoria e questo è solo un problema quando si accede direttamente i bit di pixel. Ciò non diventa importante finché non viene visualizzata l'articolo [ **l'accesso a pixel Bitmap SkiaSharp**](pixel-bits.md).

Il `SKAlphaType` enumerazione contiene quattro membri:

- `Unknown`
- `Opaque` &mdash; la bitmap non ha alcuna trasparenza
- `Premul` &mdash; componenti di colore sono pre-moltiplicati per il componente alfa
- `Unpremul` &mdash; componenti di colore non sono pre-moltiplicati per il componente alfa

Ecco un pixel bitmap rosso a 4 byte con trasparenza 50%, con i byte indicato in rosso ordine, verde, blu, alfa:

0xFF 0x00 0x00 0x80

Quando viene eseguito il rendering di una mappa di bit che contiene pixel semitrasparente in un'area di visualizzazione, i componenti di colore di ciascun pixel bitmap devono essere moltiplicati con valore alfa del pixel e i componenti di colore del pixel corrispondenti dell'area di visualizzazione devono essere moltiplicati. per 255 meno il valore alfa. Quindi è possibile combinare i due pixel. La bitmap possibile eseguire il rendering più veloce se i componenti di colore in pixel della bitmap sono già stato pre-mulitplied per il valore alfa. Tale stesso pixel rosso verrebbe archiviati in formato premoltiplicato per simile al seguente:

0x80 0x00 0x00 0x80

Il miglioramento delle prestazioni è per questo motivo `SkiaSharp` bitmap per impostazione predefinita vengono create con una `Premul` formato. Ma anche in questo caso, si rende necessario sapere solo quando si accede e modificare i bit di pixel.

## <a name="drawing-on-existing-bitmaps"></a>Disegnare sulla bitmap esistente

Non è necessario creare una nuova bitmap per disegnare su di esso. È anche possibile disegnare in una bitmap esistente.

Il **Monkey Moustache** pagina utilizza il costruttore per caricare le **MonkeyFace.png** immagine. Viene quindi creato un `SKCanvas` basato sull'immagine, oggetto e viene utilizzato `SKPaint` e `SKPath` oggetti per disegnare un moustache su di essa:

```csharp
public partial class MonkeyMoustachePage : ContentPage
{
    SKBitmap monkeyBitmap;

    public MonkeyMoustachePage()
    {
        Title = "Monkey Moustache";

        monkeyBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MonkeyFace.png");

        // Create canvas based on bitmap
        using (SKCanvas canvas = new SKCanvas(monkeyBitmap))
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 24;
                paint.StrokeCap = SKStrokeCap.Round;

                using (SKPath path = new SKPath())
                {
                    path.MoveTo(380, 390);
                    path.CubicTo(560, 390, 560, 280, 500, 280);

                    path.MoveTo(320, 390);
                    path.CubicTo(140, 390, 140, 280, 200, 280);

                    canvas.DrawPath(path, paint);
                }
            }
        }

        // Create SKCanvasView to view result
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
        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

Il costruttore si conclude con la creazione di un' `SKCanvasView` cui `PaintSurface` gestore viene semplicemente visualizzato il risultato:

[![Apportare Moustache](drawing-images/MonkeyMoustache.png "apportare Moustache")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>Copiare e modificare le bitmap

I metodi della `SKCanvas` che è possibile utilizzare per disegnare in una mappa di bit includono `DrawBitmap`. Ciò significa che è possibile disegnare una bitmap in un altro, in genere modificandola in qualche modo.

Il modo più versatile per modificare un'immagine bitmap è tramite l'accesso a effettivi bit di pixel, dell'oggetto descritto nell'articolo  **[pixel bitmap l'accesso a SkiaSharp](pixel-bits.md)** . Ma esistono diverse altre tecniche per modificare le bitmap che non richiedono l'accesso i bit di pixel.

La bitmap seguente inclusa con il **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** applicazione è 360 pixel di larghezza e 480 pixel in altezza:

![Mountain bungee](drawing-images/MountainClimbers.jpg "Mountain bungee")

Si supponga che dal monkey a sinistra per pubblicare questo fotografia non è stato ricevuto l'autorizzazione. Una soluzione consiste nel nascondere volto del monkey usando una tecnica denominata _pixelization_. I pixel della faccia vengono sostituiti con i blocchi di colore in modo che non è possibile apportare le funzionalità. I blocchi di colore vengono in genere derivati dall'immagine originale calcolando i colori dei pixel corrispondenti a questi blocchi. Ma non è necessario eseguire il calcolo della media manualmente. Si verifica automaticamente quando si copia una bitmap in un più piccole dimensioni in pixel.

Viso del monkey sinistro occupa circa un'area quadrata 72 pixel con un angolo superiore sinistro in corrispondenza del punto (112, 238). È possibile sostituire tale area quadrata 72 pixel con una matrice da-9-9 dei blocchi colorati, ognuno dei quali è 8 per 8 pixel quadrati.

Il **effetto pixel immagine** pagina viene caricata in quella bitmap e crea prima di tutto una bitmap quadrata piccola di 9 pixel chiamata `faceBitmap`. Si tratta di una destinazione per la copia appena volto del monkey. Il rettangolo di destinazione è solo di 9-pixel quadrati, ma il rettangolo di origine è 72 pixel quadrati. Ogni blocco di 8 per 8 pixel origine verrà consolidati fino a un solo pixel calcolando i colori.

Il passaggio successivo consiste nella copia della bitmap originale in una nuova bitmap della stessa dimensione denominata `pixelizedBitmap`. Il piccolo `faceBitmap` viene quindi copiato su questa con un rettangolo di destinazione quadrato 72 pixel in modo che ciascun pixel di `faceBitmap` viene espanso alle dimensioni 8 volte:

```csharp
public class PixelizedImagePage : ContentPage
{
    SKBitmap pixelizedBitmap;

    public PixelizedImagePage ()
    {
        Title = "Pixelize Image";

        SKBitmap originalBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        // Create tiny bitmap for pixelized face
        SKBitmap faceBitmap = new SKBitmap(9, 9);

        // Copy subset of original bitmap to that
        using (SKCanvas canvas = new SKCanvas(faceBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(originalBitmap,
                              new SKRect(112, 238, 184, 310),   // source
                              new SKRect(0, 0, 9, 9));          // destination

        }

        // Create full-sized bitmap for copy
        pixelizedBitmap = new SKBitmap(originalBitmap.Width, originalBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(pixelizedBitmap))
        {
            canvas.Clear();

            // Draw original in full size
            canvas.DrawBitmap(originalBitmap, new SKPoint());

            // Draw tiny bitmap to cover face
            canvas.DrawBitmap(faceBitmap,
                              new SKRect(112, 238, 184, 310));  // destination
        }

        // Create SKCanvasView to view result
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
        canvas.DrawBitmap(pixelizedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

Il costruttore si conclude con la creazione di un `SKCanvasView` per visualizzare il risultato:

[![Effetto pixel immagine](drawing-images/PixelizeImage.png "effetto pixel immagine")](drawing-images/PixelizeImage-Large.png#lightbox)

<a name="rotating-bitmaps" />

## <a name="rotating-bitmaps"></a>Rotazione di bitmap

Un'altra attività comune ruota bitmap. Ciò è particolarmente utile quando si recuperano le bitmap da una libreria di foto di iPhone o iPad. A meno che il dispositivo è stato mantenuto un orientamento specifico quando la foto è stata creata, l'immagine è probabilmente capovolto o lateralmente.

L'attivazione di una bitmap capovolta richiede la creazione di un altro bitmap le stesse dimensioni della prima e quindi impostando una trasformazione di rotazione di 180 gradi durante la copia del primo al secondo. In tutti gli esempi in questa sezione `bitmap` è il `SKBitmap` oggetto che è necessario eseguire la rotazione:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Quando la rotazione di 90 gradi, è necessario creare una mappa di bit che è una dimensione diversa rispetto all'originale mediante lo scambio di altezza e larghezza. Ad esempio, se la bitmap originale è 1200 pixel e 800 pixel in altezza, la bitmap ruotata è 800 pixel di larghezza e 1200 pixel di larghezza. Impostare traslazione e rotazione, in modo che la bitmap viene ruotata intorno all'angolo superiore sinistro e quindi spostata nella visualizzazione. (Tenere presente che il `Translate` e `RotateDegrees` metodi vengono chiamati in ordine inverso del modo in cui che vengono applicati.) Ecco il codice per la rotazione di 90 gradi in senso orario:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(bitmap.Height, 0);
    canvas.RotateDegrees(90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Di seguito è una funzione simile per la rotazione di 90 gradi in senso antiorario:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(0, bitmap.Width);
    canvas.RotateDegrees(-90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Questi due metodi vengono usati nel **foto rompicapo** pagine descritto nell'articolo [ **ritaglio bitmap SkiaSharp**](cropping.md#tile-division).

Un programma che consente all'utente di una bitmap in incrementi di 90 gradi di rotazione deve solo implementare una funzione per la rotazione di 90 gradi. L'utente può quindi ruotare in eventuali incrementi di 90 gradi tramite l'esecuzione ripetuta di questa uno funzione.

Un programma anche possibile ruotare una bitmap da qualsiasi quantità. Un approccio semplice consiste nel modificare la funzione che ruota di 180 gradi sostituendo 180 con generalizzata `angle` variabile:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

In generale, tuttavia, questa logica verrà ritagliare disattivare gli angoli dell'immagine bitmap della rotazione. Un approccio migliore consiste nel calcolare le dimensioni della bitmap ruotata utilizzando trigonometria per includere tali angoli.

Questo trigonometria viene visualizzato nei **Bitmap Rotator** pagina. Il file XAML crea un' `SKCanvasView` e un `Slider` che può essere compreso tra 0 e 360 gradi con un `Label` che mostra il valore corrente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapRotatorPage"
             Title="Bitmap Rotator">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="slider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Rotate by {0:F0}&#x00B0;'}"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

Il file code-behind carica una risorsa della bitmap e lo salva come un campo statico di sola lettura denominato `originalBitmap`. La bitmap visualizzata nei `PaintSurface` gestore di è `rotatedBitmap`, che inizialmente è impostata su `originalBitmap`:

```csharp
public partial class BitmapRotatorPage : ContentPage
{
    static readonly SKBitmap originalBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap rotatedBitmap = originalBitmap;

    public BitmapRotatorPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(rotatedBitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double angle = args.NewValue;
        double radians = Math.PI * angle / 180;
        float sine = (float)Math.Abs(Math.Sin(radians));
        float cosine = (float)Math.Abs(Math.Cos(radians));
        int originalWidth = originalBitmap.Width;
        int originalHeight = originalBitmap.Height;
        int rotatedWidth = (int)(cosine * originalWidth + sine * originalHeight);
        int rotatedHeight = (int)(cosine * originalHeight + sine * originalWidth);

        rotatedBitmap = new SKBitmap(rotatedWidth, rotatedHeight);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear(SKColors.LightPink);
            canvas.Translate(rotatedWidth / 2, rotatedHeight / 2);
            canvas.RotateDegrees((float)angle);
            canvas.Translate(-originalWidth / 2, -originalHeight / 2);
            canvas.DrawBitmap(originalBitmap, new SKPoint());
        }

        canvasView.InvalidateSurface();
    }
}
```

Il `ValueChanged` gestore del `Slider` esegue le operazioni che creano un nuovo `rotatedBitmap` in base all'angolo di rotazione. La nuova larghezza e altezza base absolute values di SENI e definiscono coseni del originale larghezze e altezze. Le trasformazioni utilizzate per disegnare la bitmap originale in bitmap ruotata spostare il centro di bitmap originale nell'origine, quindi ruoti di base al numero specificato di gradi e converte quindi tale area al centro della bitmap ruotata. (Il `Translate` e `RotateDegrees` metodi vengono chiamati in ordine inverso rispetto a come vengono applicati.)

Si noti l'uso del `Clear` metodo per lo sfondo di `rotatedBitmap` una rosa. Si tratta esclusivamente a illustrare le dimensioni di `rotatedBitmap` sullo schermo:

[![Bitmap Rotator](drawing-images/BitmapRotator.png "Bitmap Rotator")](drawing-images/BitmapRotator-Large.png#lightbox)

La bitmap ruotata è sufficiente a includere l'intera bitmap originale, ma non superiore.

## <a name="flipping-bitmaps"></a>Capovolgimento di bitmap

Un'altra operazione comunemente eseguita su bitmap viene chiamata _capovolgimento_. Concettualmente, la bitmap viene ruotata in tre dimensioni intorno a un asse verticale o asse orizzontale tramite il centro della bitmap. Capovolgimento verticale viene creata un'immagine speculare.

Il **Bitmap Flipper** pagina il **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** applicazione illustra questi processi. Il file XAML contiene una `SKCanvasView` e due pulsanti per capovolgere verticalmente e orizzontalmente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapFlipperPage"
             Title="Bitmap Flipper">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Flip Vertical"
                Grid.Row="1" Grid.Column="0"
                Margin="0, 10"
                Clicked="OnFlipVerticalClicked" />

        <Button Text="Flip Horizontal"
                Grid.Row="1" Grid.Column="1"
                Margin="0, 10"
                Clicked="OnFlipHorizontalClicked" />
    </Grid>
</ContentPage>
```

Il file code-behind implementa queste due operazioni di `Clicked` gestori per i pulsanti:

```csharp
public partial class BitmapFlipperPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public BitmapFlipperPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnFlipVerticalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(-1, 1, bitmap.Width / 2, 0);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnFlipHorizontalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(1, -1, 0, bitmap.Height / 2);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }
}
```

Il capovolgimento verticale avviene tramite una trasformazione in scala con un fattore di scala orizzontale di &ndash;1. Il centro della scala è al centro verticale della bitmap. Il capovolgimento orizzontale è una trasformazione in scala con un fattore di scala verticale di &ndash;1.

Capovolgimento di come può notare dal mancato recapito invertito su shirt del monkey, non è quello utilizzato per la rotazione. Ma, come illustrato nello screenshot UWP a destra, capovolgimento sia orizzontalmente che verticalmente equivale a rotazione di 180 gradi:

[![Bitmap Flipper](drawing-images/BitmapFlipper.png "Bitmap Flipper")](drawing-images/BitmapFlipper-Large.png#lightbox)

Un'altra attività comune che può essere gestita usando tecniche simili è ritagliare un'immagine bitmap per un sottoinsieme rettangolare. Come descritto nell'articolo successivo [ **ritaglio bitmap SkiaSharp**](cropping.md).

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
