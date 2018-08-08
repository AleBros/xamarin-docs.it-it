---
title: Il ritaglio di immagini bitmap in SkiaSharp
description: Informazioni su come usare SkiaSharp per progettare un'interfaccia utente per desribing in modo interattivo un rettangolo di ritaglio.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 0A79AB27-C69F-4376-8FFE-FF46E4783F30
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: 3dd9011d19e77f52d1fe89a37e4d992c23c72ab1
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615548"
---
# <a name="cropping-skiasharp-bitmaps"></a>Il ritaglio di immagini bitmap in SkiaSharp

Il [ **creazione e disegno in SkiaSharp bitmap** ](drawing.md) articolo descritta come un `SKBitmap` oggetto può essere passato a un `SKCanvas` costruttore. Qualsiasi metodo di disegno chiamato sull'oggetto grafico cause tale area di disegno deve essere sottoposto a rendering nella bitmap. Questi metodi di disegno includono `DrawBitmap`, il che significa che questa tecnica permette di trasferimento o parte di una singola bitmap a un'altra mappa di bit, probabilmente con trasformazioni applicate.

È possibile usare tale tecnica per ritagliare un'immagine bitmap chiamando il [ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/) metodo con i rettangoli di origine e di destinazione:

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

Tuttavia, le applicazioni che implementano il ritaglio spesso offrono un'interfaccia per l'utente di selezionare in modo interattivo il rettangolo di ritaglio:

![Esempio di ritaglio](cropping-images/CroppingSample.png "il ritaglio di esempio")

Questo articolo è incentrato su tale interfaccia.

## <a name="encapsulating-the-cropping-rectangle"></a>Che incapsula il rettangolo di ritaglio

È utile isolare parte della logica di ritaglio in una classe denominata `CroppingRectangle`. I parametri del costruttore includono un rettangolo di massimo, che corrisponde in genere le dimensioni della bitmap viene ritagliata, e le proporzioni facoltativa. Il costruttore prima di tutto definisce un rettangolo di ritaglio iniziale, che rende pubblico nel `Rect` vlastnosti typu `SKRect`. Questo rettangolo di ritaglio iniziale è l'80% della larghezza e altezza del rettangolo di bitmap, ma e quindi adeguato se viene specificato un rapporto di aspetto:

```csharp
class CroppingRectangle
{
    ···
    SKRect maxRect;             // generally the size of the bitmap
    float? aspectRatio;

    public CroppingRectangle(SKRect maxRect, float? aspectRatio = null)
    {
        this.maxRect = maxRect;
        this.aspectRatio = aspectRatio;

        // Set initial cropping rectangle
        Rect = new SKRect(0.9f * maxRect.Left + 0.1f * maxRect.Right,
                          0.9f * maxRect.Top + 0.1f * maxRect.Bottom,
                          0.1f * maxRect.Left + 0.9f * maxRect.Right,
                          0.1f * maxRect.Top + 0.9f * maxRect.Bottom);

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            SKRect rect = Rect;
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;
                rect.Left = (maxRect.Width - width) / 2;
                rect.Right = rect.Left + width;
            }
            else
            {
                float height = rect.Width / aspect;
                rect.Top = (maxRect.Height - height) / 2;
                rect.Bottom = rect.Top + height;
            }

            Rect = rect;
        }
    }
    
    public SKRect Rect { set; get; }
    ···
}
```

Un'informazione utile che `CroppingRectangle` inoltre rende disponibile è una matrice di `SKPoint` i valori corrispondenti ai quattro angoli del rettangolo di ritaglio nell'ordine in alto a sinistra, alto a destra, in basso a destra e in basso a sinistra:

```csharp
class CroppingRectangle
{
    ···
    public SKPoint[] Corners
    {
        get
        {
            return new SKPoint[]
            {
                new SKPoint(Rect.Left, Rect.Top),
                new SKPoint(Rect.Right, Rect.Top),
                new SKPoint(Rect.Right, Rect.Bottom),
                new SKPoint(Rect.Left, Rect.Bottom)
            };
        }
    }
    ···
}
```

Questa matrice viene usata nel metodo seguente, che viene chiamato `HitTest`. Il `SKPoint` parametro è un punto corrisponde a un tocco con un dito o un clic del mouse. Il metodo restituisce un indice (0, 1, 2 o 3) corrispondente all'angolo che il puntatore del mouse o del dito manipolato, entro una distanza specificata dal `radius` parametro: 

```csharp
class CroppingRectangle
{
    ···
    public int HitTest(SKPoint point, float radius)
    {
        SKPoint[] corners = Corners;

        for (int index = 0; index < corners.Length; index++)
        {
            SKPoint diff = point - corners[index];
                
            if ((float)Math.Sqrt(diff.X * diff.X + diff.Y * diff.Y) < radius)
            {
                return index;
            }
        }

        return -1;
    }
    ···
}
```

Se il punto di tocco o mouse non è stato interno `radius` unità di un angolo, il metodo restituisce &ndash;1.

Il metodo finale in `CroppingRectangle` viene chiamato `MoveCorner`, che viene chiamato in risposta a tocco o mouse lo spostamento. I due parametri indicano l'indice dell'angolo lo spostamento e la nuova posizione dell'angolo corrispondente. Nella prima metà del metodo consente di regolare il rettangolo di ritaglio basato nella nuova posizione dell'angolo, ma sempre all'interno di `maxRect`, che rappresentano le dimensioni della bitmap. Questa logica inoltre prende in considerazione il `MINIMUM` campo per evitare di comprimere il rettangolo di ritaglio in nulla:

```csharp
class CroppingRectangle
{
    const float MINIMUM = 10;   // pixels width or height
    ···
    public void MoveCorner(int index, SKPoint point)
    {
        SKRect rect = Rect;

        switch (index)
        {
            case 0: // upper-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 1: // upper-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 2: // lower-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;

            case 3: // lower-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;
        }

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;

                switch (index)
                {
                    case 0:
                    case 3: rect.Left = rect.Right - width; break;
                    case 1:
                    case 2: rect.Right = rect.Left + width; break;
                }
            }
            else
            {
                float height = rect.Width / aspect;

                switch (index)
                {
                    case 0:
                    case 1: rect.Top = rect.Bottom - height; break;
                    case 2:
                    case 3: rect.Bottom = rect.Top + height; break;
                }
            }
        }

        Rect = rect;
    }
}
```

La seconda metà del metodo regola per le proporzioni facoltativa.

Tenere presente che tutti gli elementi di questa classe è espressa in unità di pixel.

## <a name="a-canvas-view-just-for-cropping"></a>Una vista di canvas solo per il ritaglio

Il `CroppingRectangle` classe riportato in precedenza viene usata per il `PhotoCropperCanvasView` classe che deriva da `SKCanvasView`. Questa classe è responsabile della visualizzazione di bitmap e il rettangolo di ritaglio, nonché la gestione degli eventi di tocco o mouse per modificare il rettangolo di ritaglio.

Il `PhotoCropperCanvasView` costruttore richiede una bitmap. Le proporzioni è facoltativa. Costruttore crea un'istanza di un oggetto di tipo `CroppingRectangle` basato su questa mappa di bit e le proporzioni e lo salva come un campo:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        this.bitmap = bitmap;

        SKRect bitmapRect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
        croppingRect = new CroppingRectangle(bitmapRect, aspectRatio);
        ···
    }
    ···
}
```

Poiché questa classe deriva da `SKCanvasView`, non è necessario installare un gestore per il `PaintSurface` evento. Invece possibile eseguire l'override relativo `OnPaintSurface` (metodo). Il metodo consente di visualizzare la mappa di bit e Usa un paio di `SKPaint` salvati come campi per disegnare il rettangolo di ritaglio corrente:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    const int CORNER = 50;      // pixel length of cropper corner
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;
    ···
    // Drawing objects
    SKPaint cornerStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 10
    };

    SKPaint edgeStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 2
    };
    ···
    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        base.OnPaintSurface(args);

        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Gray);

        // Calculate rectangle for displaying bitmap 
        float scale = Math.Min((float)info.Width / bitmap.Width, (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect bitmapRect = new SKRect(x, y, x + scale * bitmap.Width, y + scale * bitmap.Height);
        canvas.DrawBitmap(bitmap, bitmapRect);

        // Calculate a matrix transform for displaying the cropping rectangle
        SKMatrix bitmapScaleMatrix = SKMatrix.MakeIdentity();
        bitmapScaleMatrix.SetScaleTranslate(scale, scale, x, y);

        // Display rectangle
        SKRect scaledCropRect = bitmapScaleMatrix.MapRect(croppingRect.Rect);
        canvas.DrawRect(scaledCropRect, edgeStroke);

        // Display heavier corners
        using (SKPath path = new SKPath())
        {
            path.MoveTo(scaledCropRect.Left, scaledCropRect.Top + CORNER);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Left + CORNER, scaledCropRect.Top);

            path.MoveTo(scaledCropRect.Right - CORNER, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top + CORNER);

            path.MoveTo(scaledCropRect.Right, scaledCropRect.Bottom - CORNER);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Right - CORNER, scaledCropRect.Bottom);

            path.MoveTo(scaledCropRect.Left + CORNER, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom - CORNER);

            canvas.DrawPath(path, cornerStroke);
        }

        // Invert the transform for touch tracking
        bitmapScaleMatrix.TryInvert(out inverseBitmapMatrix);
    }
    ···
}
```

Il codice nel `CroppingRectangle` classe rettangolo di ritaglio si basa sulle dimensioni in pixel della bitmap. Tuttavia, la visualizzazione della bitmap dal `PhotoCropperCanvasView` classe viene ridimensionata in base alle dimensioni dell'area di visualizzazione. Il `bitmapScaleMatrix` calcolato nel `OnPaintSurface` eseguire l'override delle mappe dei pixel della bitmap per le dimensioni e la posizione della bitmap che viene visualizzato. Questa matrice viene quindi utilizzata per trasformare il rettangolo di ritaglio in modo che possa essere visualizzato rispetto alla bitmap.

L'ultima riga del `OnPaintSurface` override preleva l'inverso del `bitmapScaleMatrix` e lo salva come il `inverseBitmapMatrix` campo. Viene utilizzato per l'elaborazione di tocco.

A `TouchEffect` viene creata un'istanza di oggetto come un campo e il costruttore ne allega un gestore per il `TouchAction` evento, ma il `TouchEffect` deve essere aggiunto al `Effects` raccolta del _padre_ del `SKCanvasView`derivativo, in modo che del richiede solo pochi i `OnParentSet` eseguire l'override:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    const int RADIUS = 100;     // pixel radius of touch hit-test
    ···
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;

    // Touch tracking 
    TouchEffect touchEffect = new TouchEffect();
    struct TouchPoint
    {
        public int CornerIndex { set; get; }
        public SKPoint Offset { set; get; }
    }

    Dictionary<long, TouchPoint> touchPoints = new Dictionary<long, TouchPoint>();
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        ···
        touchEffect.TouchAction += OnTouchEffectTouchAction;
    }
    ···
    protected override void OnParentSet()
    {
        base.OnParentSet();

        // Attach TouchEffect to parent view
        Parent.Effects.Add(touchEffect);
    }
    ···
    void OnTouchEffectTouchAction(object sender, TouchActionEventArgs args)
    {
        SKPoint pixelLocation = ConvertToPixel(args.Location);
        SKPoint bitmapLocation = inverseBitmapMatrix.MapPoint(pixelLocation);

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Convert radius to bitmap/cropping scale
                float radius = inverseBitmapMatrix.ScaleX * RADIUS;

                // Find corner that the finger is touching
                int cornerIndex = croppingRect.HitTest(bitmapLocation, radius);

                if (cornerIndex != -1 && !touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = new TouchPoint
                    {
                        CornerIndex = cornerIndex,
                        Offset = bitmapLocation - croppingRect.Corners[cornerIndex]
                    };

                    touchPoints.Add(args.Id, touchPoint);
                }
                break;

            case TouchActionType.Moved:
                if (touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = touchPoints[args.Id];
                    croppingRect.MoveCorner(touchPoint.CornerIndex, 
                                            bitmapLocation - touchPoint.Offset);
                    InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchPoints.ContainsKey(args.Id))
                {
                    touchPoints.Remove(args.Id);
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Xamarin.Forms.Point pt)
    {
        return new SKPoint((float)(CanvasSize.Width * pt.X / Width),
                           (float)(CanvasSize.Height * pt.Y / Height));
    }
}
```

Gli eventi di tocco elaborato dal `TouchAction` gestore sono espressi in unità indipendenti dal dispositivo. Questi innanzitutto elementi dovranno essere convertite in pixel usando il `ConvertToPixel` metodo nella parte inferiore della classe e quindi convertito in `CroppingRectangle` unità utilizzando `inverseBitmapMatrix`.

Per `Pressed` gli eventi, il `TouchAction` chiamate del gestore di `HitTest` metodo `CroppingRectangle`. Se il risultato è diverso da un indice &ndash;1, quindi uno degli angoli del rettangolo di ritaglio è in corso la modifica. Che l'indice e un offset del punto di tocco effettivo nell'angolo viene archiviata in una `TouchPoint` dell'oggetto e quindi aggiungervi il `touchPoints` dizionario.

Per il `Moved` evento, il `MoveCorner` metodo `CroppingRectangle` viene chiamato per spostare l'angolo, con possibili modifiche per le proporzioni.

In qualsiasi momento, un programma tramite `PhotoCropperCanvasView` può accedere il `CroppedBitmap` proprietà. Questa proprietà Usa il `Rect` proprietà del `CroppingRectangle` per creare una nuova bitmap della dimensione ritagliata. La versione di `DrawBitmap` con origine e destinazione rettangoli estrae quindi un subset della bitmap originale:

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public SKBitmap CroppedBitmap
    {
        get
        {
            SKRect cropRect = croppingRect.Rect;
            SKBitmap croppedBitmap = new SKBitmap((int)cropRect.Width, 
                                                  (int)cropRect.Height);
            SKRect dest = new SKRect(0, 0, cropRect.Width, cropRect.Height);
            SKRect source = new SKRect(cropRect.Left, cropRect.Top, 
                                       cropRect.Right, cropRect.Bottom);

            using (SKCanvas canvas = new SKCanvas(croppedBitmap))
            {
                canvas.DrawBitmap(bitmap, source, dest);
            }

            return croppedBitmap;
        }
    }
    ···
}
```

## <a name="hosting-the-photo-cropper-canvas-view"></a>La visualizzazione di canvas Ritaglia foto di hosting

Con queste due classi che gestiscono la logica di ritaglio, il **foto ritaglio** pagina il **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** applicazione dispone di un impegno minimo per eseguire. Il file XAML crea un'istanza di un `Grid` all'host la `PhotoCropperCanvasView` e un **eseguita** pulsante:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoCroppingPage"
             Title="Photo Cropping">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid x:Name="canvasViewHost"
              Grid.Row="0"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="1"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

Il `PhotoCropperCanvasView` non è possibile creare istanze nel file XAML perché richiede un parametro di tipo `SKBitmap`.

Al contrario, il `PhotoCropperCanvasView` viene creata un'istanza nel costruttore del file code-behind usando una delle bitmap di risorsa:

```csharp
public partial class PhotoCroppingPage : ContentPage
{
    PhotoCropperCanvasView photoCropper;
    SKBitmap croppedBitmap;

    public PhotoCroppingPage ()
    {
        InitializeComponent ();

        SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        photoCropper = new PhotoCropperCanvasView(bitmap);
        canvasViewHost.Children.Add(photoCropper);
    }

    void OnDoneButtonClicked(object sender, EventArgs args)
    {
        croppedBitmap = photoCropper.CroppedBitmap;

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
        canvas.DrawBitmap(croppedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

È possibile quindi manipolare il rettangolo di ritaglio:

[![Ritaglia 1 di foto](cropping-images/PhotoCropping1.png "foto Ritaglia 1")](cropping-images/PhotoCropping1-Large.png#lightbox)

Quando è stato definito un rettangolo di ritaglio ottimo, scegliere il pulsante **.** Il `Clicked` gestore ottiene bitmap ritagliata dal `CroppedBitmap` proprietà di `PhotoCropperCanvasView`e sostituisce tutto il contenuto della pagina con un nuovo `SKCanvasView` oggetto che consente di visualizzare questa bitmap ritagliata:

[![Ritaglia 2 di foto](cropping-images/PhotoCropping2.png "foto Ritaglia 2")](cropping-images/PhotoCropping2-Large.png#lightbox)

Provare a impostare il secondo argomento della `PhotoCropperCanvasView` a 1.78f (ad esempio):

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

Si noterà il rettangolo di ritaglio limitato a un rapporto di aspetto 16-a-9 caratteristiche di HD.

<a name="tile-division" />

## <a name="dividing-a-bitmap-into-tiles"></a>Suddivisione di una bitmap in riquadri

Una versione di xamarin. Forms del famoso puzzle di 14 o 15 è presente in 22 capitolo del libro [ _creazione di App per dispositivi mobili con xamarin. Forms_ ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) e possono essere scaricati come [  **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle). Tuttavia, il puzzle diventa più divertente (e spesso più complesso) se si basa su un'immagine dalla propria libreria di foto.

Questa versione del puzzle 14 o 15 fa parte del **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** dell'applicazione ed è costituito da una serie di pagine intitolata **foto rompicapo**.

Il **PhotoPuzzlePage1.xaml** file costituito da un `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage1"
             Title="Photo Puzzle">
    
    <Button Text="Pick a photo from your library"
            VerticalOptions="CenterAndExpand" 
            HorizontalOptions="CenterAndExpand"
            Clicked="OnPickButtonClicked"/>
    
</ContentPage>
```

Il file code-behind implementa una `Clicked` gestore che utilizza il `IPhotoLibrary` servizio di dipendenza per consentire all'utente di scegliere una foto dalla raccolta foto:

```csharp
public partial class PhotoPuzzlePage1 : ContentPage
{
    public PhotoPuzzlePage1 ()
    {
        InitializeComponent ();
    }

    async void OnPickButtonClicked(object sender, EventArgs args)
    {
        IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
        using (Stream stream = await photoLibrary.PickPhotoAsync())
        {
            if (stream != null)
            {
                SKBitmap bitmap = SKBitmap.Decode(stream);

                await Navigation.PushAsync(new PhotoPuzzlePage2(bitmap));
            }
        }
    }
}
```

Il metodo passa quindi al `PhotoPuzzlePage2`, passando al costruttore la mappa di bit selezionato.

È possibile che la foto selezionata dalla libreria non è orientata come veniva visualizzato nella raccolta foto, ma viene ruotato o capovolto. (Si tratta in particolare un problema con i dispositivi iOS). Per questo motivo, `PhotoPuzzlePage2` consente di ruotare l'immagine da un orientamento desiderato. Il file XAML contiene tre pulsanti contrassegnati **90&#x00B0; a destra** (vale a dire in senso orario), **90&#x00B0; sinistra** (in senso antiorario), e **eseguita**.

Il file code-behind implementa la logica di bitmap-rotazione illustrata nell'articolo  **[creazione e la creazione su SkiaSharp Bitmaps](drawing.md#rotating-bitmaps)**. L'utente può ruotare l'immagine di 90 gradi in senso orario o antiorario un numero qualsiasi di volte in cui: 

```csharp
public partial class PhotoPuzzlePage2 : ContentPage
{
    SKBitmap bitmap;

    public PhotoPuzzlePage2 (SKBitmap bitmap)
    {
        this.bitmap = bitmap;

        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnRotateRightButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(bitmap.Height, 0);
            canvas.RotateDegrees(90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnRotateLeftButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(0, bitmap.Width);
            canvas.RotateDegrees(-90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        await Navigation.PushAsync(new PhotoPuzzlePage3(bitmap));
    }
}
```

Quando l'utente fa clic il **eseguite** pulsante, il `Clicked` gestore passa a `PhotoPuzzlePage3`, passando la bitmap ruotata finale nel costruttore della pagina.

`PhotoPuzzlePage3` consente la foto da ritagliare. Il programma richiede una bitmap per dividere in una griglia 4 per 4 riquadri quadrata.

Il **PhotoPuzzlePage3.xaml** file contiene una `Label`, una `Grid` all'host il `PhotoCropperCanvasView`e un altro **eseguita** pulsante:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage3"
             Title="Photo Puzzle">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Label Text="Crop the photo to a square"
               Grid.Row="0"
               FontSize="Large"
               HorizontalTextAlignment="Center"
               Margin="5" />

        <Grid x:Name="canvasViewHost"
              Grid.Row="1"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="2"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

Il file code-behind creare un'istanza di `PhotoCropperCanvasView` con la bitmap passata al costruttore. Si noti che un valore 1 viene passato come secondo argomento per `PhotoCropperCanvasView`. Questo rapporto di 1 forza il rettangolo di ritaglio da un quadrato:

```csharp
public partial class PhotoPuzzlePage3 : ContentPage
{
    PhotoCropperCanvasView photoCropper;

    public PhotoPuzzlePage3(SKBitmap bitmap)
    {
        InitializeComponent ();

        photoCropper = new PhotoCropperCanvasView(bitmap, 1f);
        canvasViewHost.Children.Add(photoCropper);
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        SKBitmap croppedBitmap = photoCropper.CroppedBitmap;
        int width = croppedBitmap.Width / 4;
        int height = croppedBitmap.Height / 4;

        ImageSource[] imgSources = new ImageSource[15];

        for (int row = 0; row < 4; row++)
        {
            for (int col = 0; col < 4; col++)
            {
                // Skip the last one!
                if (row == 3 && col == 3)
                    break;

                // Create a bitmap 1/4 the width and height of the original
                SKBitmap bitmap = new SKBitmap(width, height);
                SKRect dest = new SKRect(0, 0, width, height);
                SKRect source = new SKRect(col * width, row * height, (col + 1) * width, (row + 1) * height);

                // Copy 1/16 of the original into that bitmap
                using (SKCanvas canvas = new SKCanvas(bitmap))
                {
                    canvas.DrawBitmap(croppedBitmap, source, dest);
                }

                imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
            }
        }

        await Navigation.PushAsync(new PhotoPuzzlePage4(imgSources));
    }
}
```

Il gestore del pulsante Ottiene la larghezza e altezza della bitmap ritagliata (questi due valori devono essere lo stesso) e quindi si divide in 15 bitmap separate, ognuno dei quali è 1 e 4 la larghezza e altezza dell'originale **.** (L'ultima delle bitmap di 16 possibili non viene creato). Il `DrawBitmap` metodo con rettangolo di origine e di destinazione consente a una bitmap essere creata in base a subset di una bitmap di dimensioni maggiori.

## <a name="converting-to-xamarinforms-bitmaps"></a>Conversione di bitmap di xamarin. Forms

Nel `OnDoneButtonClicked` metodo, la matrice creata per le 15 bitmap JE typu [ `ImageSource` ](xref:Xamarin.Forms.ImageSource):

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource` è il tipo di base di xamarin. Forms che incapsula una bitmap. Fortunatamente, SkiaSharp consente la conversione dalle bitmap di SkiaSharp in xamarin. Forms bitmap. Il **SkiaSharp.Views.Forms** assembly definisce un' [ `SKBitmapImageSource` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKBitmapImageSource/) classe che deriva da `ImageSource` ma possono essere creati come base un SkiaSharp `SKBitmap` oggetto. `SKBitmapImageSource` definisce anche le conversioni tra `SKBitmapImageSource` e `SKBitmap`e di come `SKBitmap` gli oggetti vengono archiviati in una matrice come le bitmap di xamarin. Forms:

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

Questa matrice delle bitmap viene passata come un costruttore `PhotoPuzzlePage4`. Tale pagina è completamente xamarin. Forms e non usa alcun SkiaSharp. È molto simile a [ **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle), pertanto non verrà descritta di seguito, ma visualizza la foto selezionata suddivisa in riquadri quadrati 15:

[![Foto 1 Puzzle](cropping-images/PhotoPuzzle1.png "foto Puzzle 1")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

Premere il **Randomize** una combinazione di pulsante backup di tutti i riquadri:

[![Foto di 2 Puzzle](cropping-images/PhotoPuzzle2.png "foto Puzzle 2")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

A questo punto è possibile inserirli nuovamente nell'ordine corretto. Tutte le sezioni nella stessa riga o colonna sotto forma di quadrato vuoto possono essere toccate trasferendoli poi nel quadrato vuoto. 

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
