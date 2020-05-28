---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6c5e340818b702d79a1157f29c1ecec19bf1db76
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139945"
---
# <a name="cropping-skiasharp-bitmaps"></a>Ritaglio di bitmap SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

L'articolo [**creazione e disegno di bitmap SkiaSharp**](drawing.md) descrive come un `SKBitmap` oggetto può essere passato a un `SKCanvas` costruttore. Qualsiasi metodo di disegno chiamato sul Canvas causa il rendering della grafica sulla bitmap. Questi metodi di disegno includono `DrawBitmap` , il che significa che questa tecnica consente di trasferire parte o tutte le bitmap in un'altra bitmap, probabilmente con le trasformazioni applicate.

È possibile utilizzare questa tecnica per ritagliare una bitmap chiamando il [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) metodo con rettangoli di origine e di destinazione:

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

Tuttavia, le applicazioni che implementano il ritaglio spesso forniscono un'interfaccia per consentire all'utente di selezionare in modo interattivo il rettangolo di ritaglio:

![Esempio di ritaglio](cropping-images/CroppingSample.png "Esempio di ritaglio")

Questo articolo è incentrato su tale interfaccia.

## <a name="encapsulating-the-cropping-rectangle"></a>Incapsulamento del rettangolo di ritaglio

È utile isolare parte della logica di ritaglio in una classe denominata `CroppingRectangle` . I parametri del costruttore includono un rettangolo massimo, che corrisponde in genere alla dimensione della bitmap da ritagliare e a proporzioni facoltative. Il costruttore innanzitutto definisce un rettangolo di ritaglio iniziale, che rende pubblico nella `Rect` proprietà di tipo `SKRect` . Il rettangolo di ritaglio iniziale è il 80% della larghezza e dell'altezza del rettangolo bitmap, ma viene quindi regolato se viene specificata una proporzioni:

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

Una delle informazioni utili che `CroppingRectangle` rende anche disponibile è una matrice di `SKPoint` valori corrispondenti ai quattro angoli del rettangolo di ritaglio nell'ordine superiore sinistro, in alto a destra, in basso a destra e in basso a sinistra:

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

Questa matrice viene usata nel metodo seguente, che viene chiamato `HitTest` . Il `SKPoint` parametro è un punto corrispondente al tocco di un dito o a un clic del mouse. Il metodo restituisce un indice (0, 1, 2 o 3) corrispondente all'angolo con cui è stato toccato il dito o il puntatore del mouse, all'interno di una distanza specificata dal `radius` parametro: 

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

Se il punto di tocco o del mouse non si trovava in `radius` unità di un angolo, il metodo restituirà &ndash; 1.

Il metodo finale in `CroppingRectangle` viene chiamato `MoveCorner` , che viene chiamato in risposta al tocco o al movimento del mouse. I due parametri indicano l'indice dell'angolo spostato e la nuova posizione dell'angolo. La prima metà del metodo regola il rettangolo di ritaglio in base alla nuova posizione dell'angolo, ma sempre all'interno dei limiti di `maxRect` , che corrisponde alla dimensione della bitmap. Questa logica prende anche in considerazione il `MINIMUM` campo per evitare di comprimere il rettangolo di ritaglio in nulla:

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

La seconda metà del metodo viene regolata per le proporzioni facoltative.

Tenere presente che tutto ciò che si trova in questa classe è in unità di pixel.

## <a name="a-canvas-view-just-for-cropping"></a>Visualizzazione canvas solo per il ritaglio

La `CroppingRectangle` classe appena visualizzata viene utilizzata dalla `PhotoCropperCanvasView` classe, che deriva da `SKCanvasView` . Questa classe è responsabile della visualizzazione della bitmap e del rettangolo di ritaglio, nonché della gestione degli eventi di tocco o del mouse per la modifica del rettangolo di ritaglio.

Il `PhotoCropperCanvasView` costruttore richiede una bitmap. Una proporzioni è facoltativa. Il costruttore crea un'istanza di un oggetto di tipo `CroppingRectangle` in base a questa bitmap e alle proporzioni e lo salva come campo:

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

Poiché questa classe deriva da `SKCanvasView` , non è necessario installare un gestore per l' `PaintSurface` evento. Può invece eseguire l'override del relativo `OnPaintSurface` metodo. Il metodo Visualizza la bitmap e usa un paio di `SKPaint` oggetti salvati come campi per creare il rettangolo di ritaglio corrente:

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

Il codice nella `CroppingRectangle` classe basa il rettangolo di ritaglio sulle dimensioni in pixel della bitmap. Tuttavia, la visualizzazione della bitmap da parte della `PhotoCropperCanvasView` classe viene ridimensionata in base alle dimensioni dell'area di visualizzazione. L'oggetto `bitmapScaleMatrix` calcolato nell' `OnPaintSurface` override esegue il mapping tra i pixel bitmap e le dimensioni e la posizione della bitmap mentre viene visualizzata. Questa matrice viene quindi utilizzata per trasformare il rettangolo di ritaglio in modo che possa essere visualizzato rispetto alla bitmap.

L'ultima riga dell' `OnPaintSurface` override accetta l'inverso di e la `bitmapScaleMatrix` Salva come `inverseBitmapMatrix` campo. Viene usato per l'elaborazione del tocco.

`TouchEffect`Viene creata un'istanza di un oggetto come campo e il costruttore connette un gestore all' `TouchAction` evento, ma `TouchEffect` deve essere aggiunto alla `Effects` raccolta dell' _elemento padre_ del `SKCanvasView` derivato, in modo che venga eseguito nell' `OnParentSet` override:

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

Gli eventi di tocco elaborati dal `TouchAction` gestore si trovano in unità indipendenti dal dispositivo. Questi primi devono essere convertiti in pixel usando il `ConvertToPixel` metodo nella parte inferiore della classe e quindi convertiti in `CroppingRectangle` unità usando `inverseBitmapMatrix` .

Per `Pressed` gli eventi, il `TouchAction` gestore chiama il `HitTest` metodo di `CroppingRectangle` . Se restituisce un indice diverso da &ndash; 1, viene modificato uno degli angoli del rettangolo di ritaglio. Tale indice e un offset del punto di tocco effettivo dall'angolo vengono archiviati in un `TouchPoint` oggetto e aggiunti al `touchPoints` dizionario.

Per l' `Moved` evento, `MoveCorner` viene chiamato il metodo di `CroppingRectangle` per spostare l'angolo, con possibili regolazioni per le proporzioni.

In qualsiasi momento, un programma che utilizza `PhotoCropperCanvasView` può accedere alla `CroppedBitmap` Proprietà. Questa proprietà utilizza la `Rect` proprietà di `CroppingRectangle` per creare una nuova bitmap della dimensione ritagliata. La versione di `DrawBitmap` con i rettangoli di origine e di destinazione estrae un subset della bitmap originale:

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

## <a name="hosting-the-photo-cropper-canvas-view"></a>Hosting della visualizzazione Canvas di Photo Cropper

Con queste due classi che gestiscono la logica di ritaglio, la pagina di **ritaglio delle foto** nell'applicazione **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** ha un lavoro molto ridotto. Il file XAML crea un'istanza `Grid` di per ospitare `PhotoCropperCanvasView` e un pulsante **done** :

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

`PhotoCropperCanvasView`Non è possibile creare un'istanza di nel file XAML perché richiede un parametro di tipo `SKBitmap` .

Al contrario, `PhotoCropperCanvasView` viene creata un'istanza di nel costruttore del file code-behind utilizzando una delle bitmap della risorsa:

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

L'utente può quindi modificare il rettangolo di ritaglio:

[![Foto Cropper 1](cropping-images/PhotoCropping1.png "Foto Cropper 1")](cropping-images/PhotoCropping1-Large.png#lightbox)

Quando è stato definito un rettangolo di ritaglio valido, fare clic sul pulsante **fine** . Il `Clicked` gestore ottiene la bitmap ritagliata dalla `CroppedBitmap` proprietà di `PhotoCropperCanvasView` e sostituisce tutto il contenuto della pagina con un nuovo `SKCanvasView` oggetto che visualizza questa bitmap ritagliata:

[![Foto Cropper 2](cropping-images/PhotoCropping2.png "Foto Cropper 2")](cropping-images/PhotoCropping2-Large.png#lightbox)

Provare a impostare il secondo argomento di `PhotoCropperCanvasView` su 1,78 f (ad esempio):

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

Verrà visualizzato il rettangolo di ritaglio limitato a una caratteristica di proporzioni da 16 a 9 della televisione ad alta definizione.

<a name="tile-division" />

## <a name="dividing-a-bitmap-into-tiles"></a>Suddivisione di una bitmap in riquadri

Xamarin.FormsNel capitolo 22 del libro [_creazione di app per dispositivi mobili con Novell. Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) è stata rilevata una versione del famoso puzzle 14-15, che può essere scaricata come [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle). Tuttavia, il puzzle diventa più divertente (e spesso più complesso) quando si basa su un'immagine della propria raccolta foto.

Questa versione di 14-15 puzzle fa parte dell'applicazione **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** ed è costituita da una serie di pagine intitolate **Photo puzzle**.

Il file **PhotoPuzzlePage1. XAML** è costituito da un `Button` :

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

Il file code-behind implementa un `Clicked` gestore che usa il `IPhotoLibrary` servizio di dipendenza per consentire all'utente di scegliere una foto dalla raccolta foto:

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

Il metodo passa quindi a `PhotoPuzzlePage2` , passando alla bitmap selezionata costruttore.

È possibile che la foto selezionata dalla libreria non sia orientata come appare nella raccolta foto, ma è ruotata o capovolta. Si tratta in particolare di un problema relativo ai dispositivi iOS. Per questo motivo, `PhotoPuzzlePage2` consente di ruotare l'immagine in un orientamento desiderato. Il file XAML contiene tre pulsanti con etichetta **90&#x00B0; destra** (senso orario), **90&#x00B0; Left** (in senso antiorario) e **done**.

Il file code-behind implementa la logica di rotazione bitmap illustrata nell'articolo **[creazione e disegno di bitmap SkiaSharp](drawing.md#rotating-bitmaps)**. L'utente può ruotare l'immagine di 90 gradi in senso orario o antiorario per un numero qualsiasi di volte: 

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

Quando l'utente fa clic sul pulsante **fine** , il `Clicked` gestore passa a `PhotoPuzzlePage3` , passando la bitmap finale ruotata nel costruttore della pagina.

`PhotoPuzzlePage3`consente di ritagliare la foto. Il programma richiede la suddivisione di una bitmap quadrata in una griglia di riquadri 4 per 4.

Il file **PhotoPuzzlePage3. XAML** contiene un `Label` , un `Grid` per ospitare `PhotoCropperCanvasView` e un altro pulsante **done** :

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

Il file code-behind crea un'istanza di `PhotoCropperCanvasView` con la bitmap passata al relativo costruttore. Si noti che 1 viene passato come secondo argomento a `PhotoCropperCanvasView` . Questa proporzioni di 1 impone al rettangolo di ritaglio di essere un quadrato:

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

Il gestore del pulsante **done** ottiene la larghezza e l'altezza della bitmap ritagliata (questi due valori devono essere uguali), quindi li divide in 15 bitmap separate, ognuna delle quali è 1/4 la larghezza e l'altezza dell'originale. Non viene creata l'ultima delle 16 bitmap possibili. Il `DrawBitmap` metodo con rettangolo di origine e di destinazione consente di creare una bitmap in base al subset di una bitmap più grande.

## <a name="converting-to-xamarinforms-bitmaps"></a>Conversione in Xamarin.Forms bitmap

Nel `OnDoneButtonClicked` Metodo la matrice creata per le 15 bitmap è di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) :

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource`è il Xamarin.Forms tipo di base che incapsula una bitmap. Fortunatamente, SkiaSharp consente la conversione da bitmap SkiaSharp a Xamarin.Forms bitmap. L'assembly **SkiaSharp. views. Forms** definisce una [`SKBitmapImageSource`](xref:SkiaSharp.Views.Forms.SKBitmapImageSource) classe che deriva da `ImageSource` ma può essere creata in base a un `SKBitmap` oggetto SkiaSharp. `SKBitmapImageSource`definisce anche le conversioni tra `SKBitmapImageSource` e e `SKBitmap` questo è il modo `SKBitmap` in cui gli oggetti vengono archiviati in una matrice come Xamarin.Forms bitmap:

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

Questa matrice di bitmap viene passata come costruttore a `PhotoPuzzlePage4` . Questa pagina è interamente Xamarin.Forms e non usa alcun SkiaSharp. È molto simile a [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle), quindi non verrà descritta qui, ma visualizzerà la foto selezionata divisa in 15 riquadri:

[![Puzzle foto 1](cropping-images/PhotoPuzzle1.png "Puzzle foto 1")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

Premendo il pulsante **casuale** tutti i riquadri vengono combinati:

[![Foto rompicapo 2](cropping-images/PhotoPuzzle2.png "Foto rompicapo 2")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

A questo punto è possibile riportarle nell'ordine corretto. È possibile toccare tutti i riquadri nella stessa riga o colonna del quadrato vuoto per spostarli nel quadrato vuoto. 

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
