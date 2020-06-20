---
title: Creazione e disegno di bitmap SkiaSharp
description: Informazioni su come creare bitmap SkiaSharp, quindi disegnarle su tali bitmap creando un'area di disegno basata su di essi.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c045e297beca675c0582efc2f75b1d6b2bcedcf8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573293"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>Creazione e disegno di bitmap SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Si è visto come un'applicazione può caricare bitmap dal Web, dalle risorse dell'applicazione e dalla raccolta foto dell'utente. È anche possibile creare nuove bitmap all'interno dell'applicazione. L'approccio più semplice prevede uno dei costruttori di [`SKBitmap`](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)) :

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

I `width` `height` parametri e sono numeri interi e specificano le dimensioni in pixel della bitmap. Questo costruttore crea una bitmap a colori completi con quattro byte per pixel: un byte per i componenti rosso, verde, blu e alfa (opacità).

Dopo aver creato una nuova bitmap, è necessario ottenere un elemento sulla superficie della bitmap. Questa operazione viene in genere eseguita in uno dei due modi seguenti:

- Disegnare sulla bitmap usando metodi di `Canvas` disegno standard.
- Accedere direttamente ai bit di pixel.

Questo articolo illustra il primo approccio:

![Esempio di disegno](drawing-images/DrawingSample.png "Esempio di disegno")

Il secondo approccio viene illustrato nell'articolo [**accesso a SkiaSharp bitmap pixels**](pixel-bits.md).

## <a name="drawing-on-the-bitmap"></a>Disegno sulla bitmap

Il disegno sulla superficie di una bitmap è identico a quello del disegno in una visualizzazione video. Per creare una visualizzazione video, è possibile ottenere un `SKCanvas` oggetto dagli `PaintSurface` argomenti dell'evento. Per creare una bitmap, è necessario creare un `SKCanvas` oggetto usando il [`SKCanvas`](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap)) costruttore:

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

Al termine del disegno sulla bitmap, è possibile eliminare l' `SKCanvas` oggetto. Per questo motivo, il `SKCanvas` costruttore viene in genere chiamato in un' `using` istruzione:

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

La bitmap può quindi essere visualizzata. In un secondo momento, il programma può creare un nuovo `SKCanvas` oggetto in base alla stessa bitmap ed estrarne altro.

La pagina **Hello bitmap** nell'applicazione **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** scrive il testo "Hello, bitmap!" in una bitmap, quindi la bitmap viene visualizzata più volte.

Il costruttore di `HelloBitmapPage` inizia creando un `SKPaint` oggetto per la visualizzazione del testo. Determina le dimensioni di una stringa di testo e crea una bitmap con tali dimensioni. Viene quindi creato un `SKCanvas` oggetto basato su tale bitmap, viene chiamato `Clear` , quindi viene chiamato `DrawText` . È sempre consigliabile chiamare `Clear` con una nuova bitmap perché una bitmap appena creata potrebbe contenere dati casuali.

Il costruttore termina creando un `SKCanvasView` oggetto per visualizzare la bitmap:

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

Il `PaintSurface` gestore esegue il rendering della bitmap più volte nelle righe e nelle colonne della visualizzazione. Si noti che il `Clear` metodo nel `PaintSurface` gestore ha un argomento di `SKColors.Aqua` , che colora lo sfondo della superficie di visualizzazione:

[![Hello, bitmap!](drawing-images/HelloBitmap.png "Hello, bitmap!")](drawing-images/HelloBitmap-Large.png#lightbox)

L'aspetto dello sfondo Aqua rivela che la bitmap è trasparente tranne che per il testo.

## <a name="clearing-and-transparency"></a>Cancellazione e trasparenza

La visualizzazione della pagina **Hello bitmap** Mostra che la bitmap creata dal programma è trasparente, ad eccezione del testo nero. Questo è il motivo per cui viene visualizzato il colore Aqua della superficie di visualizzazione.

La documentazione dei `Clear` metodi di `SKCanvas` li descrive con l'istruzione: "sostituisce tutti i pixel nell'area di disegno ' Current clip". L'uso della parola "sostituisce" rivela una caratteristica importante di questi metodi: tutti i metodi di disegno di `SKCanvas` aggiungono qualcosa alla superficie di visualizzazione esistente. I `Clear` metodi _sostituiscono_ quello che è già presente.

`Clear`esiste in due diverse versioni:

- Il [`Clear`](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor)) metodo con un `SKColor` parametro sostituisce i pixel della superficie di visualizzazione con i pixel di tale colore.

- Il [`Clear`](xref:SkiaSharp.SKCanvas.Clear) metodo senza parametri sostituisce i pixel con il [`SKColors.Empty`](xref:SkiaSharp.SKColors.Empty) colore, ovvero un colore in cui tutti i componenti (rosso, verde, blu e alfa) sono impostati su zero. Questo colore viene talvolta definito "nero trasparente".

Se si chiama `Clear` senza argomenti in una nuova bitmap, l'intera bitmap sarà completamente trasparente. Qualsiasi elemento disegnato successivamente sulla bitmap sarà in genere opaco o parzialmente opaco.

Ecco un tentativo: nella pagina **Hello bitmap** sostituire il `Clear` metodo applicato al `bitmapCanvas` con questo:

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

L'ordine dei `SKColor` parametri del costruttore è rosso, verde, blu e alfa, dove ogni valore può variare da 0 a 255. Tenere presente che un valore alfa pari a 0 è trasparente, mentre un valore alfa pari a 255 è opaco.

Il valore (255, 0, 0, 128) cancella i pixel della bitmap in pixel rossi con un'opacità del 50%. Ciò significa che lo sfondo della bitmap è semitrasparente. Lo sfondo rosso semi trasparente della bitmap si combina con lo sfondo Aqua della superficie di visualizzazione per creare uno sfondo grigio.

Provare a impostare il colore del testo su nero trasparente inserendo la seguente assegnazione nell' `SKPaint` inizializzatore:

```csharp
Color = new SKColor(0, 0, 0, 0)
```

Si potrebbe pensare che questo testo trasparente creerebbe aree completamente trasparenti della bitmap attraverso le quali verrà visualizzato lo sfondo Aqua della superficie di visualizzazione. Ma non è così. Il testo viene disegnato sopra quello che è già presente nella bitmap. Il testo trasparente non sarà visibile.

Nessun `Draw` metodo rende mai una bitmap più trasparente. `Clear`Questa operazione può essere eseguita solo da.

## <a name="bitmap-color-types"></a>Tipi di colore bitmap

Il costruttore più semplice `SKBitmap` consente di specificare la larghezza e l'altezza di un pixel integer per la bitmap. Altri `SKBitmap` costruttori sono più complessi. Questi costruttori richiedono argomenti di due tipi di enumerazione: [`SKColorType`](xref:SkiaSharp.SKColorType) e [`SKAlphaType`](xref:SkiaSharp.SKAlphaType) . Altri costruttori utilizzano la [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) struttura, che consente di consolidare tali informazioni.

L' `SKColorType` enumerazione include 9 membri. Ognuno di questi membri descrive un particolare metodo di archiviazione dei pixel bitmap:

- `Unknown`
- `Alpha8`&mdash;ogni pixel è a 8 bit, che rappresenta un valore alfa da completamente trasparente a completamente opaco
- `Rgb565`&mdash;ogni pixel è 16 bit, 5 bit per il colore rosso e blu e 6 per il verde
- `Argb4444`&mdash;ogni pixel è 16 bit, 4 per alfa, rosso, verde e blu
- `Rgba8888`&mdash;ogni pixel è 32 bit, 8 per rosso, verde, blu e alfa
- `Bgra8888`&mdash;ogni pixel è 32 bit, 8 per ogni colore blu, verde, rosso e alfa
- `Index8`&mdash;ogni pixel è a 8 bit e rappresenta un indice in un[`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8`&mdash;ogni pixel è a 8 bit che rappresenta una sfumatura grigia da nero a bianco
- `RgbaF16`&mdash;ogni pixel è 64 bit, con rosso, verde, blu e alfa in un formato a virgola mobile a 16 bit

I due formati in cui ogni pixel è 32 pixel (4 byte) sono spesso denominati formati _a colori completi_ . Molti degli altri formati sono date da un'ora in cui i video non sono in grado di visualizzare il colore completo. Le bitmap con un colore limitato sono adatte per queste visualizzazioni e le bitmap consentite occupano meno spazio in memoria.

In questi giorni, i programmatori usano quasi sempre le bitmap a colori completi e non hanno alcun problema con altri formati. L'eccezione è rappresentata dal `RgbaF16` formato, che consente una risoluzione dei colori maggiore rispetto ai formati a colori completi. Tuttavia, questo formato viene usato per scopi specializzati, ad esempio per la creazione di immagini medicali, e non è molto utile se usato con schermi a colori standard.

Questa serie di articoli si limiterà ai `SKBitmap` formati di colore utilizzati per impostazione predefinita quando non `SKColorType` viene specificato alcun membro. Questo formato predefinito è basato sulla piattaforma sottostante. Per le piattaforme supportate da Xamarin.Forms , il tipo di colore predefinito è:

- `Rgba8888`per iOS e Android
- `Bgra8888`per UWP

L'unica differenza è l'ordine dei 4 byte in memoria, che diventa un problema solo quando si accede direttamente ai bit di pixel. Questa operazione non diventa importante fino a quando non si arriva all'articolo [**accesso a SkiaSharp bitmap pixels**](pixel-bits.md).

L' `SKAlphaType` enumerazione ha quattro membri:

- `Unknown`
- `Opaque`&mdash;la bitmap non ha trasparenza
- `Premul`&mdash;i componenti dei colori sono pre-moltiplicati per il componente alfa
- `Unpremul`&mdash;i componenti dei colori non sono pre-moltiplicati per il componente alfa

Di seguito è riportato un pixel della bitmap rossa a 4 byte con trasparenza del 50%, con i byte visualizzati nell'ordine rosso, verde, blu, alfa:

0xFF 0x00 0x00 0x80

Quando viene eseguito il rendering di una bitmap contenente pixel semi-trasparenti in una superficie di visualizzazione, i componenti colore di ogni pixel bitmap devono essere moltiplicati per il valore alfa di tale pixel e i componenti colore del pixel corrispondente della superficie di visualizzazione devono essere moltiplicati per 255 meno il valore alfa. È quindi possibile combinare i due pixel. È possibile eseguire il rendering della bitmap più velocemente se i componenti di colore nei pixel della bitmap sono già stati pre-mulitplied dal valore alfa. Lo stesso pixel rosso verrebbe archiviato come questo in un formato pre-moltiplicato:

0x80 0x00 0x00 0x80

Questo miglioramento delle prestazioni è il motivo `SkiaSharp` per cui le bitmap vengono create per impostazione predefinita con un `Premul` formato. Ma anche in questo caso è necessario conoscerlo solo quando si accede e si modificano i bit di pixel.

## <a name="drawing-on-existing-bitmaps"></a>Disegno su bitmap esistenti

Non è necessario creare una nuova bitmap per il progetto. È anche possibile creare una bitmap esistente.

La pagina **Monkey baffi** usa il costruttore per caricare l'immagine del **MonkeyFace.png** . Crea quindi un `SKCanvas` oggetto basato su tale bitmap e USA `SKPaint` `SKPath` gli oggetti e per trarne un baffo:

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

Il costruttore termina creando un oggetto `SKCanvasView` il cui `PaintSurface` gestore Visualizza semplicemente il risultato:

[![Baffi scimmia](drawing-images/MonkeyMoustache.png "Baffi scimmia")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>Copia e modifica di bitmap

I metodi di `SKCanvas` che è possibile utilizzare per creare una bitmap includono `DrawBitmap` . Ciò significa che è possibile creare una bitmap in un'altra, in genere modificarla in qualche modo.

Il modo più versatile per modificare una bitmap consiste nell'accedere ai bit di pixel effettivi, un oggetto trattato nell'articolo **[accesso ai pixel della bitmap SkiaSharp](pixel-bits.md)**. Tuttavia, esistono molte altre tecniche per modificare le bitmap che non richiedono l'accesso ai bit di pixel.

La bitmap seguente inclusa nell'applicazione **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** è 360 pixel di larghezza e 480 pixel in altezza:

![Climber di montagna](drawing-images/MountainClimbers.jpg "Climber di montagna")

Si supponga di non avere ricevuto l'autorizzazione dalla scimmia a sinistra per pubblicare la foto. Una soluzione consiste nel nascondere il volto della scimmia usando una tecnica detta _pixelzzazione_. I pixel della superficie sono sostituiti con blocchi di colore, quindi non è possibile creare le funzionalità. I blocchi di colore sono in genere derivati dall'immagine originale, calcolando la media dei colori dei pixel corrispondenti a questi blocchi. Ma non è necessario eseguire questa operazione in media. Viene eseguita automaticamente quando si copia una bitmap in una dimensione in pixel più piccola.

Il volto della scimmia sinistra occupa approssimativamente un'area quadrata di 72 pixel con un angolo superiore sinistro al punto (112, 238). Si sostituirà l'area quadrata di 72 pixel con una matrice 9 per 9 di blocchi colorati, ognuno dei quali è un quadrato da 8 a 8 pixel.

La pagina **immagine trasformare** viene caricata nella bitmap e prima crea una piccola bitmap quadrata di 9 pixel denominata `faceBitmap` . Si tratta di una destinazione per la copia solo del volto della scimmia. Il rettangolo di destinazione è solo un quadrato di 9 pixel, ma il rettangolo di origine è di 72 pixel. Ogni blocco di 8 per 8 dei pixel di origine viene consolidato fino a un solo pixel, calcolando la media dei colori.

Il passaggio successivo consiste nel copiare la bitmap originale in una nuova bitmap con le stesse dimensioni denominate `pixelizedBitmap` . Il piccolo `faceBitmap` viene quindi copiato sopra di esso con un rettangolo di destinazione quadrato di 72 pixel, in modo che ogni pixel di `faceBitmap` venga espanso fino a 8 volte le sue dimensioni:

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

Il costruttore termina creando un oggetto `SKCanvasView` per visualizzare il risultato:

[![Immagine di trasformare](drawing-images/PixelizeImage.png "Immagine di trasformare")](drawing-images/PixelizeImage-Large.png#lightbox)

## <a name="rotating-bitmaps"></a>Rotazione di bitmap

Un'altra attività comune è la rotazione delle bitmap. Questa operazione è particolarmente utile quando si recuperano le bitmap da una raccolta foto di iPhone o iPad. A meno che il dispositivo non sia stato mantenuto in un particolare orientamento quando è stata eseguita la foto, è probabile che l'immagine sia capovolta o lateralmente.

Per attivare una bitmap, è necessario creare un'altra bitmap con le stesse dimensioni del primo, quindi impostare una trasformazione in modo che ruoti di 180 gradi, copiando il primo al secondo. In tutti gli esempi di questa sezione, `bitmap` è l' `SKBitmap` oggetto che è necessario ruotare:

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Quando si esegue la rotazione di 90 gradi, è necessario creare una bitmap con dimensioni diverse rispetto a quella originale scambiando l'altezza e la larghezza. Se, ad esempio, la bitmap originale ha una larghezza di 1200 pixel e 800 pixel di altezza, la bitmap ruotata è 800 pixel di larghezza e 1200 pixel di larghezza. Impostare la traslazione e la rotazione in modo che la bitmap venga ruotata intorno all'angolo superiore sinistro e quindi spostata nella visualizzazione. Tenere presente che i `Translate` `RotateDegrees` metodi e vengono chiamati nell'ordine opposto rispetto al modo in cui vengono applicati. Ecco il codice per la rotazione di 90 gradi in senso orario:

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

Ecco una funzione simile per la rotazione di 90 gradi in senso antiorario:

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

Questi due metodi vengono usati nelle pagine di **puzzle foto** descritte nell'articolo [**ritaglio di bitmap SkiaSharp**](cropping.md#cropping-skiasharp-bitmaps).

Un programma che consente all'utente di ruotare una bitmap con incrementi di 90 gradi deve implementare una sola funzione per la rotazione di 90 gradi. L'utente può quindi ruotare per un incremento di 90 gradi eseguendo ripetutamente questa funzione.

Un programma può anche ruotare una bitmap per qualsiasi quantità. Un approccio semplice consiste nel modificare la funzione che ruota di 180 gradi sostituendo 180 con una variabile generalizzata `angle` :

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

Tuttavia, nel caso generale, questa logica ridurrà gli angoli della bitmap ruotata. Un approccio migliore consiste nel calcolare la dimensione della bitmap ruotata usando la trigonometria per includere gli angoli.

Questa trigonometria viene visualizzata nella pagina di **rotazione della bitmap** . Il file XAML crea un'istanza di `SKCanvasView` e un oggetto `Slider` che può variare da 0 a 360 gradi con un oggetto che `Label` Mostra il valore corrente:

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

Il file code-behind carica una risorsa bitmap e la Salva come campo statico di sola lettura denominato `originalBitmap` . La bitmap visualizzata nel `PaintSurface` gestore è `rotatedBitmap` , che inizialmente è impostata su `originalBitmap` :

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

Il `ValueChanged` gestore di `Slider` esegue le operazioni che creano un nuovo oggetto `rotatedBitmap` in base all'angolo di rotazione. La nuova larghezza e altezza sono basate sui valori assoluti di seno e sui coseni delle larghezze e delle altezze originali. Le trasformazioni usate per creare la bitmap originale sulla bitmap ruotata spostano il centro bitmap originale nell'origine, quindi lo ruotano in base al numero di gradi specificato e quindi traslano il centro al centro della bitmap ruotata. I `Translate` metodi e `RotateDegrees` vengono chiamati nell'ordine opposto rispetto al modo in cui vengono applicati.

Si noti l'uso del `Clear` metodo per fare lo sfondo di `rotatedBitmap` una rosa chiara. Questo è solo per illustrare le dimensioni di `rotatedBitmap` sullo schermo:

[![Rotatore bitmap](drawing-images/BitmapRotator.png "Rotatore bitmap")](drawing-images/BitmapRotator-Large.png#lightbox)

La bitmap ruotata è sufficiente per includere l'intera bitmap originale, ma non più grande.

## <a name="flipping-bitmaps"></a>Capovolgimento di bitmap

Un'altra operazione comunemente eseguita sulle bitmap è detta _capovolgimento_. Concettualmente, la bitmap viene ruotata in tre dimensioni intorno a un asse verticale o asse orizzontale attraverso il centro della bitmap. Il capovolgimento verticale crea un'immagine speculare.

La pagina **flipper bitmap** nell'applicazione **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** illustra questi processi. Il file XAML contiene un `SKCanvasView` e due pulsanti per capovolgere verticalmente e orizzontalmente:

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

Il file code-behind implementa queste due operazioni nei `Clicked` gestori per i pulsanti:

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

Il capovolgimento verticale viene eseguito da una trasformazione di ridimensionamento con un fattore di scala orizzontale pari a &ndash; 1. Il centro di ridimensionamento è il centro verticale della bitmap. Il capovolgimento orizzontale è una trasformazione di ridimensionamento con un fattore di scala verticale pari a &ndash; 1.

Come si può notare dalla lettera invertita sulla camicia della scimmia, il capovolgimento non è uguale alla rotazione. Tuttavia, come illustrato nella schermata UWP a destra, il capovolgimento orizzontale e verticale corrisponde alla rotazione di 180 gradi:

[![Flipper bitmap](drawing-images/BitmapFlipper.png "Flipper bitmap")](drawing-images/BitmapFlipper-Large.png#lightbox)

Un'altra attività comune che può essere gestita utilizzando tecniche simili consiste nel ritagliare una bitmap in un subset rettangolare. Questa operazione è descritta nell'articolo successivo [**ritaglio di bitmap SkiaSharp**](cropping.md).

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
