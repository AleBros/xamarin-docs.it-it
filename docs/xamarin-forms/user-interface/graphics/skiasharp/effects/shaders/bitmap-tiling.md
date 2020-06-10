---
title: "SkiaSharp bitmap affiancation" Description: "affiancare un'area usando le bitmap ripetute orizzontalmente e verticalmente".
ms. prod: Novell MS. Technology: Novell-skiasharp ms. AssetID: 9ED14E07-4DC8-4B03-8A33-772838BF51EA autore: davidbritch ms. Author: dabritch ms. Date: 08/23/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="skiasharp-bitmap-tiling"></a>Affiancamento bitmap SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)

Come è stato illustrato nei due articoli precedenti, la [`SKShader`](xref:SkiaSharp.SKShader) classe può creare sfumature lineari o circolari. Questo articolo è incentrato sull' `SKShader` oggetto che usa una bitmap per affiancare un'area. La bitmap può essere ripetuta orizzontalmente e verticalmente, sia nell'orientamento originale che in alternativa invertita orizzontalmente e verticalmente. Il capovolgimento evita le discontinuità tra i riquadri:

![Esempio di affiancamento di bitmap](bitmap-tiling-images/BitmapTilingSample.png "Esempio di affiancamento di bitmap")

Il [`SKShader.CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode)) metodo statico che crea questo shader presenta un `SKBitmap` parametro e due membri dell' [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) enumerazione:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

I due parametri indicano le modalità usate per l'affiancamento orizzontale e l'affiancamento verticale. Si tratta della stessa `SKShaderTileMode` enumerazione utilizzata anche con i metodi di sfumatura.

Un [`CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) Overload include un `SKMatrix` argomento per eseguire una trasformazione sulle bitmap affiancate:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

Questo articolo contiene alcuni esempi di utilizzo di questa trasformazione matrice con bitmap affiancate.

## <a name="exploring-the-tile-modes"></a>Esplorazione delle modalità di affiancamento

Il primo programma nella sezione di **affiancamento bitmap** della pagina **shaders and other Effects** dell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) illustra gli effetti dei due `SKShaderTileMode` argomenti. Il file XAML delle **modalità Flip del riquadro bitmap** crea un'istanza di `SKCanvasView` e due `Picker` visualizzazioni che consentono di selezionare un `SKShaderTilerMode` valore per l'affiancamento orizzontale e verticale. Si noti che `SKShaderTileMode` nella sezione è definita una matrice di membri `Resources` :

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapTileFlipModesPage"
             Title="Bitmap Tile Flip Modes">

    <ContentPage.Resources>
        <x:Array x:Key="tileModes"
                 Type="{x:Type skia:SKShaderTileMode}">
            <x:Static Member="skia:SKShaderTileMode.Clamp" />
            <x:Static Member="skia:SKShaderTileMode.Repeat" />
            <x:Static Member="skia:SKShaderTileMode.Mirror" />
        </x:Array>
    </ContentPage.Resources>

    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="xModePicker"
                Title="Tile X Mode"
                Margin="10, 0"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

        <Picker x:Name="yModePicker"
                Title="Tile Y Mode"
                Margin="10, 10"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

    </StackLayout>
</ContentPage>
```

Il costruttore del file code-behind carica nella risorsa bitmap che mostra un Monkey sitting. Prima di tutto l'immagine viene ritagliata usando il [`ExtractSubset`](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) metodo di in `SKBitmap` modo che i bordi della bitmap tocchino i bordi. Il costruttore usa quindi il [`Resize`](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) metodo per creare un'altra bitmap della metà delle dimensioni. Queste modifiche rendono la bitmap leggermente più adatta per l'affiancamento:

```csharp
public partial class BitmapTileFlipModesPage : ContentPage
{
    SKBitmap bitmap;

    public BitmapTileFlipModesPage ()
    {
        InitializeComponent ();

        SKBitmap origBitmap = BitmapExtensions.LoadBitmapResource(
            GetType(), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

        // Define cropping rect
        SKRectI cropRect = new SKRectI(5, 27, 296, 260);

        // Get the cropped bitmap
        SKBitmap croppedBitmap = new SKBitmap(cropRect.Width, cropRect.Height);
        origBitmap.ExtractSubset(croppedBitmap, cropRect);

        // Resize to half the width and height
        SKImageInfo info = new SKImageInfo(cropRect.Width / 2, cropRect.Height / 2);
        bitmap = croppedBitmap.Resize(info, SKBitmapResizeMethod.Box);
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get tile modes from Pickers
        SKShaderTileMode xTileMode =
            (SKShaderTileMode)(xModePicker.SelectedIndex == -1 ?
                                        0 : xModePicker.SelectedItem);
        SKShaderTileMode yTileMode =
            (SKShaderTileMode)(yModePicker.SelectedIndex == -1 ?
                                        0 : yModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap, xTileMode, yTileMode);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Il `PaintSurface` gestore ottiene le `SKShaderTileMode` impostazioni dalle due `Picker` visualizzazioni e crea un `SKShader` oggetto in base alla bitmap e a questi due valori. Questo shader viene usato per riempire l'area di disegno:

[![Modalità Flip riquadro bitmap](bitmap-tiling-images/BitmapTileFlipModes.png "Modalità Flip riquadro bitmap")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

La schermata iOS a sinistra mostra l'effetto dei valori predefiniti di `SKShaderTileMode.Clamp` . La bitmap si trova nell'angolo superiore sinistro. Sotto la bitmap, la riga inferiore dei pixel viene ripetuta fino a questo punto. A destra della bitmap, la colonna più a destra di pixel viene ripetuta in tutti i modi. Il resto dell'area di disegno è colorato dal pixel marrone scuro nell'angolo inferiore destro della bitmap. Dovrebbe essere ovvio che l' `Clamp` opzione non è quasi mai utilizzata con la piastrellatura bitmap.

La schermata Android nel centro Mostra il risultato di `SKShaderTileMode.Repeat` per entrambi gli argomenti. Il riquadro viene ripetuto orizzontalmente e verticalmente. Viene visualizzata la schermata piattaforma UWP (Universal Windows Platform) `SKShaderTileMode.Mirror` . I riquadri vengono ripetuti ma in alternativa invertiti orizzontalmente e verticalmente. Il vantaggio di questa opzione è che non ci sono discontinuità tra i riquadri.

Tenere presente che è possibile utilizzare diverse opzioni per la ripetizione orizzontale e verticale. È possibile specificare `SKShaderTileMode.Mirror` come secondo argomento per `CreateBitmap` ma `SKShaderTileMode.Repeat` come terzo argomento. In ogni riga, le scimmie si alternano ancora tra l'immagine normale e l'immagine speculare, ma nessuna delle scimmie è capovolta.

## <a name="patterned-backgrounds"></a>Sfondi con pattern

L'affiancamento bitmap viene comunemente utilizzato per creare uno sfondo basato su pattern da una bitmap relativamente piccola. L'esempio classico è un muro Brick.

La pagina del **muro del Brick algoritmico** crea una bitmap di piccole dimensioni simile a un intero Brick e due metà di un Brick separato da Malta. Poiché anche questo Brick viene usato nell'esempio successivo, viene creato da un costruttore statico e reso pubblico con una proprietà statica:

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    static AlgorithmicBrickWallPage()
    {
        const int brickWidth = 64;
        const int brickHeight = 24;
        const int morterThickness = 6;
        const int bitmapWidth = brickWidth + morterThickness;
        const int bitmapHeight = 2 * (brickHeight + morterThickness);

        SKBitmap bitmap = new SKBitmap(bitmapWidth, bitmapHeight);

        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint brickPaint = new SKPaint())
        {
            brickPaint.Color = new SKColor(0xB2, 0x22, 0x22);

            canvas.Clear(new SKColor(0xF0, 0xEA, 0xD6));
            canvas.DrawRect(new SKRect(morterThickness / 2,
                                       morterThickness / 2,
                                       morterThickness / 2 + brickWidth,
                                       morterThickness / 2 + brickHeight),
                                       brickPaint);

            int ySecondBrick = 3 * morterThickness / 2 + brickHeight;

            canvas.DrawRect(new SKRect(0,
                                       ySecondBrick,
                                       bitmapWidth / 2 - morterThickness / 2,
                                       ySecondBrick + brickHeight),
                                       brickPaint);

            canvas.DrawRect(new SKRect(bitmapWidth / 2 + morterThickness / 2,
                                       ySecondBrick,
                                       bitmapWidth,
                                       ySecondBrick + brickHeight),
                                       brickPaint);
        }

        // Save as public property for other programs
        BrickWallTile = bitmap;
    }

    public static SKBitmap BrickWallTile { private set; get; }
    ···
}
```

La bitmap risultante è 70 pixel di larghezza e 60 pixel di altezza:

![Riquadro del muro del Brick algoritmico](bitmap-tiling-images/AlgorithmicBrickWallTile.png "Riquadro del muro del Brick algoritmico")

Il resto della pagina del **muro di Brick algoritmo** crea un `SKShader` oggetto che ripete l'immagine orizzontalmente e verticalmente:

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    ···
    public AlgorithmicBrickWallPage ()
    {
        Title = "Algorithmic Brick Wall";

        // Create SKCanvasView
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

        using (SKPaint paint = new SKPaint())
        {
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(BrickWallTile,
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Il risultato è il seguente:

[![Parete algoritmica Brick](bitmap-tiling-images/AlgorithmicBrickWall.png "Parete algoritmica Brick")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

Potrebbe essere preferibile un po' più realistico. In tal caso, è possibile fotografare un muro di mattoni effettivo e quindi ritagliarlo. Questa bitmap è 300 pixel di larghezza e 150 pixel di altezza:

![Riquadro Brick Wall](bitmap-tiling-images/BrickWallTile.jpg "Riquadro Brick Wall")

Questa bitmap viene usata nella pagina **Wall Brick fotografica** :

```csharp
public class PhotographicBrickWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PhotographicBrickWallPage),
                        "SkiaSharpFormsDemos.Media.BrickWallTile.jpg");

    public PhotographicBrickWallPage()
    {
        Title = "Photographic Brick Wall";

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

        using (SKPaint paint = new SKPaint())
        {
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Si noti che gli `SKShaderTileMode` argomenti di `CreateBitmap` sono entrambi `Mirror` . Questa opzione è in genere necessaria quando si usano i riquadri creati da immagini reali. Il mirroring dei riquadri evita le discontinuità:

[![Muro di Brick fotografico](bitmap-tiling-images/PhotographicBrickWall.png "Muro di Brick fotografico")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

Sono necessarie alcune operazioni per ottenere una bitmap adatta per il riquadro. Questo non funziona molto bene perché il Brick più scuro si presenta troppo spesso. Viene visualizzato regolarmente all'interno delle immagini ripetute, mostrando il fatto che questo muro Brick è stato costruito da una bitmap più piccola.

La cartella **media** dell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) include anche questa immagine di una parete di pietra:

![Riquadro della parete di pietra](bitmap-tiling-images/StoneWallTile.jpg "Riquadro della parete di pietra")

Tuttavia, la bitmap originale è leggermente troppo grande per un riquadro. Potrebbe essere `SKShader.CreateBitmap` ridimensionato, ma anche il metodo può ridimensionare il riquadro applicando una trasformazione. Questa opzione è illustrata nella pagina della **parete di pietra** :

```csharp
public class StoneWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(StoneWallPage),
                        "SkiaSharpFormsDemos.Media.StoneWallTile.jpg");

    public StoneWallPage()
    {
        Title = "Stone Wall";

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

        using (SKPaint paint = new SKPaint())
        {
            // Create scale transform
            SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);

            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror,
                                                 matrix);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

`SKMatrix`Viene creato un valore per ridimensionare l'immagine a metà delle dimensioni originali:

[![Muro di pietra](bitmap-tiling-images/StoneWall.png "Muro di pietra")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

La trasformazione opera sulla bitmap originale utilizzata nel `CreateBitmap` Metodo? O trasforma la matrice di riquadri risultante? 

Un modo semplice per rispondere a questa domanda consiste nell'includere una rotazione come parte della trasformazione:

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

Se la trasformazione viene applicata al singolo riquadro, ogni immagine ripetuta del riquadro dovrebbe essere ruotata e il risultato conterrebbe numerose discontinuità. Tuttavia è evidente da questo screenshot che la matrice composita di riquadri viene trasformata:

[![Muro di pietra ruotato](bitmap-tiling-images/StoneWallRotated.png "Muro di pietra ruotato")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

Nell'allineamento dei [**riquadri**](#tile-alignment)della sezione verrà visualizzato un esempio di trasformazione translate applicato allo shader.

L'esempio di [**Clock Cat**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock) autonomo, che non fa parte di **SkiaSharpFormsDemos**, simula uno sfondo con granularità del legno usando la piastrellatura bitmap basata su questa bitmap quadrata di 240 pixel:

![Granaggio](bitmap-tiling-images/WoodGrain.png "Granaggio")

Si tratta di una fotografia di un parquet. L' `SKShaderTileMode.Mirror` opzione consente di visualizzare come un'area di legno molto più ampia:

[![Orologio gatto](bitmap-tiling-images/CatClock.png "Orologio gatto")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>Allineamento del riquadro

Tutti gli esempi mostrati finora hanno usato lo shader creato da `SKShader.CreateBitmap` per coprire l'intera area di disegno. Nella maggior parte dei casi, si utilizzerà la piastrellatura bitmap per la creazione di aree più piccole o (più raramente) per riempire l'interno di linee spesse. Ecco il riquadro fotografico-muro di mattoni usato per un rettangolo più piccolo:

[![Allineamento del riquadro](bitmap-tiling-images/TileAlignment.png "Allineamento del riquadro")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

Questa operazione potrebbe essere utile per l'utente o forse no. È possibile che il modello di affiancamento non inizi con un Brick completo nell'angolo superiore sinistro del rettangolo. Questo perché gli shader sono allineati con l'area di disegno e non con l'oggetto grafico che decorano.

La correzione è semplice. Creare un `SKMatrix` valore basato su una trasformazione di conversione. La trasformazione sposta in modo efficace il modello affiancato al punto in cui si desidera che l'angolo superiore sinistro del riquadro venga allineato. Questo approccio è illustrato nella pagina di **allineamento** dei riquadri, che ha creato l'immagine dei riquadri non allineati mostrati in precedenza:

```csharp
public class TileAlignmentPage : ContentPage
{
    bool isAligned;

    public TileAlignmentPage()
    {
        Title = "Tile Alignment";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Add tap handler
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isAligned ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKRect rect = new SKRect(info.Width / 7,
                                     info.Height / 7,
                                     6 * info.Width / 7,
                                     6 * info.Height / 7);

            // Get bitmap from other program
            SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;

            // Create bitmap tiling
            if (!isAligned)
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
            }
            else
            {
                SKMatrix matrix = SKMatrix.MakeTranslation(rect.Left, rect.Top);

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     matrix);
            }

            // Draw rectangle
            canvas.DrawRect(rect, paint);
        }
    }
}
```

Nella pagina **allineamento riquadro** è incluso un oggetto `TapGestureRecognizer` . Toccare o fare clic sulla schermata e il programma passa al `SKShader.CreateBitmap` metodo con un `SKMatrix` argomento. Questa trasformazione sposta il modello in modo che l'angolo superiore sinistro contenga un Brick completo:

[![Allineamento del riquadro toccato](bitmap-tiling-images/TileAlignmentTapped.png "Allineamento del riquadro toccato")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

È anche possibile usare questa tecnica per garantire che il modello di bitmap affiancato sia centrato all'interno dell'area disegnata. Nella pagina **riquadri centrati** il `PaintSurface` gestore calcola innanzitutto le coordinate come se visualizzasse la singola bitmap al centro dell'area di disegno. USA quindi tali coordinate per creare una trasformazione di traduzione per `SKShader.CreateBitmap` . Questa trasformazione sposta l'intero modello in modo da centrare un riquadro:

```csharp
public class CenteredTilesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.monkey.png");

    public CenteredTilesPage ()
    {
        Title = "Centered Tiles";

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

        // Find coordinates to center bitmap in canvas...
        float x = (info.Width - bitmap.Width) / 2f;
        float y = (info.Height - bitmap.Height) / 2f;

        using (SKPaint paint = new SKPaint())
        {
            // ... but use them to create a translate transform
            SKMatrix matrix = SKMatrix.MakeTranslation(x, y);
            paint.Shader = SKShader.CreateBitmap(bitmap, 
                                                 SKShaderTileMode.Repeat, 
                                                 SKShaderTileMode.Repeat, 
                                                 matrix);

            // Use that tiled bitmap pattern to fill a circle
            canvas.DrawCircle(info.Rect.MidX, info.Rect.MidY,
                              Math.Min(info.Width, info.Height) / 2,
                              paint);
        }
    }
}
```

Il `PaintSurface` gestore si conclude disegnando un cerchio al centro dell'area di disegno. Certo, uno dei riquadri è esattamente al centro del cerchio, mentre gli altri sono disposti in un modello simmetrico:

[![Riquadri centrati](bitmap-tiling-images/CenteredTiles.png "Riquadri centrati")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

Un altro approccio di centramento è in realtà leggermente più semplice. Anziché creare una trasformazione trasla che inserisce un riquadro al centro, è possibile centrare un angolo del modello affiancato. Nella `SKMatrix.MakeTranslation` chiamata usare gli argomenti per il centro dell'area di disegno:

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

Il modello è ancora centrato e simmetrico, ma nessun riquadro è al centro:

[![Riquadri centrati alternativi](bitmap-tiling-images/CenteredTilesAlternate.png "Riquadri centrati alternativi")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>Semplificazione tramite rotazione

In alcuni casi l'uso di una trasformazione di rotazione nel `SKShader.CreateBitmap` metodo può semplificare il riquadro bitmap. Questa operazione risulta evidente quando si tenta di definire un riquadro per una recinzione di collegamento a catena. Il file **ChainLinkTile.cs** crea il riquadro visualizzato qui (con uno sfondo rosa per motivi di chiarezza):

![Catena di collegamenti a catena rigido](bitmap-tiling-images/HardChainLinkTile.png "Catena di collegamenti a catena rigido")

Il riquadro deve includere due collegamenti, in modo che il codice divida il riquadro in quattro quadranti. I quadranti superiore sinistro e inferiore destro sono uguali, ma non sono completi. I fili hanno piccoli intagli che devono essere gestiti con un disegno aggiuntivo nei quadranti in alto a destra e in basso a sinistra. Il file che esegue tutto questo lavoro è lungo 174 righe.

La creazione di questo riquadro risulta molto più semplice:

![Riquadro più semplice per il collegamento a catena](bitmap-tiling-images/EasierChainLinkTile.png "Riquadro più semplice per il collegamento a catena")

Se lo shader del riquadro bitmap è ruotato di 90 gradi, gli oggetti visivi sono quasi uguali.

Il codice per creare il riquadro collegamento a catena più semplice fa parte della pagina del **riquadro catena-collegamento** . Il costruttore determina una dimensione del riquadro in base al tipo di dispositivo in cui è in esecuzione il programma e quindi chiama `CreateChainLinkTile` , che disegna sulla bitmap utilizzando linee, percorsi e shader sfumatura:

```csharp
public class ChainLinkFencePage : ContentPage
{
    ···
    SKBitmap tileBitmap;

    public ChainLinkFencePage ()
    {
        Title = "Chain-Link Fence";

        // Create bitmap for chain-link tiling
        int tileSize = Device.Idiom == TargetIdiom.Desktop ? 64 : 128;
        tileBitmap = CreateChainLinkTile(tileSize);

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    SKBitmap CreateChainLinkTile(int tileSize)
    {
        tileBitmap = new SKBitmap(tileSize, tileSize);
        float wireThickness = tileSize / 12f;

        using (SKCanvas canvas = new SKCanvas(tileBitmap))
        using (SKPaint paint = new SKPaint())
        {
            canvas.Clear();
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = wireThickness;
            paint.IsAntialias = true;

            // Draw straight wires first
            paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                         new SKPoint(0, tileSize),
                                                         new SKColor[] { SKColors.Silver, SKColors.Black },
                                                         new float[] { 0.4f, 0.6f },
                                                         SKShaderTileMode.Clamp);

            canvas.DrawLine(0, tileSize / 2,
                            tileSize / 2, tileSize / 2 - wireThickness / 2, paint);

            canvas.DrawLine(tileSize, tileSize / 2,
                            tileSize / 2, tileSize / 2 + wireThickness / 2, paint);

            // Draw curved wires
            using (SKPath path = new SKPath())
            {
                path.MoveTo(tileSize / 2, 0);
                path.LineTo(tileSize / 2 - wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 + wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.Silver, SKColors.Black },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);

                path.Reset();
                path.MoveTo(tileSize / 2, tileSize);
                path.LineTo(tileSize / 2 + wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 - wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.White, SKColors.Silver },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);
            }
            return tileBitmap;
        }
    }
    ···
}
```

Fatta eccezione per i fili, il riquadro è trasparente, il che significa che è possibile visualizzarlo sopra un altro elemento. Il programma viene caricato in una delle risorse bitmap, lo Visualizza per riempire l'area di disegno e quindi disegna lo shader in alto:

```csharp
public class ChainLinkFencePage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(ChainLinkFencePage), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");
    ···

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.UniformToFill, 
                            BitmapAlignment.Center, BitmapAlignment.Start);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(tileBitmap, 
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat,
                                                 SKMatrix.MakeRotationDegrees(45));
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Si noti che lo shader è ruotato di 45 gradi, quindi è orientato come un vero e proprio collegamento a catena:

[![Recinzione collegamenti a catena](bitmap-tiling-images/ChainLinkFence.png "Recinzione collegamenti a catena")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>Animazione di riquadri bitmap

È possibile aggiungere un'animazione a un intero modello di riquadro bitmap animando la trasformazione matrice. Forse si vuole che il modello venga spostato orizzontalmente o verticalmente o entrambi. A tale scopo, è possibile creare una trasformazione di traslazione in base alle coordinate di spostamento.

È anche possibile creare una piccola bitmap o modificare i bit di pixel della bitmap alla frequenza di 60 volte al secondo. Tale bitmap può quindi essere utilizzata per l'affiancamento e l'intero modello affiancato può sembrare animato. 

Questo approccio viene illustrato nella pagina del **riquadro bitmap animato** . Viene creata un'istanza di bitmap come campo da 64-pixels Square. Il costruttore chiama `DrawBitmap` per assegnargli un aspetto iniziale. Se il `angle` campo è zero (così com'è quando il metodo viene chiamato per la prima volta), la bitmap contiene due righe incrociate come una X. La lunghezza delle righe è sufficiente per raggiungere sempre il bordo della bitmap indipendentemente dal `angle` valore: 

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    const int SIZE = 64;

    SKCanvasView canvasView;
    SKBitmap bitmap = new SKBitmap(SIZE, SIZE);
    float angle;
    ···

    public AnimatedBitmapTilePage ()
    {
        Title = "Animated Bitmap Tile";

        // Initialize bitmap prior to animation
        DrawBitmap();

        // Create SKCanvasView 
        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
    void DrawBitmap()
    {
        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = SIZE / 8;

            canvas.Clear();
            canvas.Translate(SIZE / 2, SIZE / 2);
            canvas.RotateDegrees(angle);
            canvas.DrawLine(-SIZE, -SIZE, SIZE, SIZE, paint);
            canvas.DrawLine(-SIZE, SIZE, SIZE, -SIZE, paint);
        }
    }
    ···
}
```

Il sovraccarico dell'animazione si verifica `OnAppearing` nelle `OnDisappearing` sostituzioni e. Il `OnTimerTick` metodo aggiunge un'animazione al `angle` valore compreso tra 0 e 360 gradi ogni 10 secondi per ruotare la figura X all'interno della bitmap:

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 10;     // seconds
        angle = (float)(360f * (stopwatch.Elapsed.TotalSeconds % duration) / duration);
        DrawBitmap();
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

A causa della simmetria della figura X, equivale a ruotare il `angle` valore da 0 gradi a 90 gradi ogni 2,5 secondi.

Il `PaintSurface` gestore crea uno shader dalla bitmap e usa l'oggetto Paint per colorare l'intera area di disegno:

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

Le `SKShaderTileMode.Mirror` opzioni assicurano che le braccia della x in ogni bitmap si uniscono alla x nelle bitmap adiacenti per creare un modello animato generale che sembra molto più complesso di quanto venga suggerito dall'animazione semplice:

[![Riquadro bitmap animata](bitmap-tiling-images/AnimatedBitmapTile.png "Riquadro bitmap animata")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [CatClock (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)
