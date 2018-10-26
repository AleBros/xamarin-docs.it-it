---
title: Affiancamento di bitmap di SkiaSharp
description: Affiancare un'area mediante bitmap ripetuta orizzontalmente e verticalmente.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 5bd063f82cc1d09c6b2e9100429889a23a2eda7f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111585"
---
# <a name="skiasharp-bitmap-tiling"></a>Affiancamento di bitmap di SkiaSharp

Come si è visto in due articoli precedenti, il [ `SKShader` ](xref:SkiaSharp.SKShader) classe può creare sfumature lineari o circolare. Questo articolo illustra il `SKShader` oggetto che usa un'immagine bitmap per affiancare un'area. La bitmap può essere ripetuta orizzontalmente e verticalmente, nell'orientamento originale o in alternativa capovolta orizzontalmente e verticalmente. Il capovolgimento evita discontinuità tra i riquadri:

![Esempio di affiancamento bitmap](bitmap-tiling-images/BitmapTilingSample.png "esempio di affiancamento Bitmap")

Il metodo statico [ `SKShader.CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode)) metodo che crea lo shader è un `SKBitmap` parametro e due membri del [ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode) enumerazione:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

I due parametri indicano le modalità utilizzate per l'espansione orizzontale e verticale per l'affiancamento. Questo è lo stesso `SKShaderTileMode` enumerazione che viene usato anche con i metodi della sfumatura.

Oggetto [ `CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) overload include un `SKMatrix` argomento per eseguire una trasformazione sulle bitmap affiancate:

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

Questo articolo contiene alcuni esempi di uso di questa trasformazione di matrice con bitmap affiancate.

## <a name="exploring-the-tile-modes"></a>Esplorare la modalità di affiancamento

Il primo programma nel **Bitmap affiancamento** sezione del **shader e altri effetti** pagina del [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio vengono illustrati i risultati dei due `SKShaderTileMode` argomenti. Il **Capovolgi modalità di affiancamento Bitmap** crea un'istanza di file XAML un' `SKCanvasView` e due `Picker` viste che consentono di selezionare un `SKShaderTilerMode` valore per l'espansione orizzontale e verticale. Si noti che una matrice del `SKShaderTileMode` membri è definito nel `Resources` sezione:

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

Il costruttore del file code-behind carica nella risorsa bitmap che mostra un meccanico che si trova. Innanzitutto Ritaglia l'immagine usando il [ `ExtractSubset` ](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) metodo `SKBitmap` in modo che head e feet stanno toccando i bordi della bitmap. Il costruttore utilizza quindi il [ `Resize` ](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) metodo per creare un'altra immagine bitmap della metà delle dimensioni. Queste modifiche rendono più adatto per l'espansione della bitmap:

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

Il `PaintSurface` gestore ottiene il `SKShaderTileMode` delle impostazioni dei due `Picker` visualizzazioni e crea un `SKShader` oggetto basato sulla bitmap e questi due valori. Questo shader viene usato per riempire l'area di disegno:

[![Bitmap di modalità di riquadro capovolto](bitmap-tiling-images/BitmapTileFlipModes.png "Bitmap modalità di riquadro capovolto")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

La schermata iOS a sinistra mostra l'effetto dei valori predefiniti di `SKShaderTileMode.Clamp`. La mappa di bit si trova nell'angolo superiore sinistro. Sotto la mappa di bit, la riga inferiore dei pixel viene ripetuta verso il basso. A destra della bitmap, la colonna all'estrema destra di pixel viene ripetuta completamente. Il resto dell'area di disegno viene colorato in base pixel marrone scuro nell'angolo inferiore destro della mappa di bit. Dovrebbe essere evidente che il `Clamp` opzione non viene utilizzata quasi mai con affiancamento di bitmap.

La schermata Android al centro mostra il risultato di `SKShaderTileMode.Repeat` per entrambi gli argomenti. Il riquadro viene ripetuto orizzontalmente e verticalmente. Mostra la schermata (Universal Windows Platform) `SKShaderTileMode.Mirror`. I riquadri sono ripetuti ma alternativamente capovolta orizzontalmente e verticalmente. Il vantaggio di questa opzione è che non esistono Nessun discontinuità tra i riquadri.

Tenere presente che è possibile usare diverse opzioni per la ripetizione orizzontale e verticale. È possibile specificare `SKShaderTileMode.Mirror` come secondo argomento per `CreateBitmap` ma `SKShaderTileMode.Repeat` come terzo argomento. In ogni riga, esistono i programmatori alternare l'immagine normale e l'immagine speculare, ma nessuna delle esistono i programmatori devono capovolto.

## <a name="patterned-backgrounds"></a>Sfondi con motivi

Affiancamento di bitmap viene comunemente utilizzato per creare uno sfondo con motivi da una bitmap relativamente ridotto. L'esempio classico è una parete brick.

Il **Wall Brick algoritmico** pagina Crea una bitmap di ridotte dimensioni che è simile a un brick intero e due metà di un brick separati da tradizionale. Poiché questo brick viene usato nell'esempio successivo, viene creato da un costruttore statico e reso pubblico con una proprietà statica:

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

La bitmap risultante è 70 pixel di larghezza e altezza pari a 60:

![Brick algoritmico parete Tile](bitmap-tiling-images/AlgorithmicBrickWallTile.png "Brick algoritmico parete riquadro")

Il resto del **Wall Brick algoritmico** pagina Crea un `SKShader` oggetto che si ripete l'immagine orizzontalmente e verticalmente:

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

Ecco il risultato:

[![Brick algoritmico Wall](bitmap-tiling-images/AlgorithmicBrickWall.png "parete algoritmico Brick")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

Si potrebbe preferire qualcosa di leggermente più realistico. In tal caso, è possibile richiedere una fotografia di una parete brick effettivo e quindi ritagliarla. Questa bitmap è 300 pixel in larghezza e 150 pixel di altezza:

![Brick parete Tile](bitmap-tiling-images/BrickWallTile.jpg "Brick parete riquadro")

Questa bitmap viene utilizzata la **Photographic parete di Brick** pagina:

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

Si noti che il `SKShaderTileMode` argomenti `CreateBitmap` sono entrambi `Mirror`. Questa opzione viene in genere necessaria quando si utilizzano i riquadri creati da immagini del mondo reale. Il mirroring di riquadri consente di evitare discontinuità:

[![Wall Brick Photographic](bitmap-tiling-images/PhotographicBrickWall.png "parete Photographic Brick")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

Sono necessarie alcune operazioni per ottenere una bitmap appropriata per il riquadro. Questo non funziona molto bene perché il brick più scuro è l'acronimo una quantità eccessiva. Viene visualizzata regolarmente nelle immagini ripetute, rivelare il fatto che la bacheca brick è stata costruita da una bitmap più piccola.

Il **supporti** cartella della [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio include anche l'immagine di una roccia parete:

![Dovranno essere parete Tile](bitmap-tiling-images/StoneWallTile.jpg "Stone parete riquadro")

Tuttavia, la bitmap originale è un po' troppo grande per un riquadro. Può essere ridimensionato, ma il `SKShader.CreateBitmap` metodo anche possibile ridimensionare il riquadro, applicare una trasformazione all'elemento. Questa opzione è illustrata nel **Wall Stone** pagina:

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

Un `SKMatrix` valore viene creato per ridimensionare l'immagine alla metà dimensione originale:

[![Pietra Wall](bitmap-tiling-images/StoneWall.png "pietra parete")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

Il la trasformazione opera su una bitmap originale usato nel `CreateBitmap` metodo? Oppure Trasforma la matrice risultante dei riquadri? 

Un modo semplice per rispondere a questa domanda è includere una rotazione come parte della trasformazione:

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

Se la trasformazione viene applicata al singolo riquadro, quindi deve essere ruotate ogni immagine ripetute del riquadro e il risultato contiene molti discontinuità. Ma è evidente da questa schermata che viene trasformata la matrice composta dei riquadri:

[![Pietra parete ruotato](bitmap-tiling-images/StoneWallRotated.png "pietra parete ruotata")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

Nella sezione [ **riquadro allineamento**](#tile-alignment), si noterà un esempio di una trasformazione di traslazione applicato allo shader.

La versione autonoma [ **Clock Cat** ](https://developer.xamarin.com/samples/xamarin-forms/CatClock) esempio (non fa parte del **SkiaSharpFormsDemos**) simula uno sfondo wood granulare con affiancamento bitmap basato su questa mappa di bit 240 pixel quadrato:

![Il livello di dettaglio di legno](bitmap-tiling-images/WoodGrain.png "legno-livello di dettaglio")

Ovvero una fotografia di un piano di legno. Il `SKShaderTileMode.Mirror` consente che venga visualizzato come un'area molto più grande di legno:

[![Cat orologio](bitmap-tiling-images/CatClock.png "Cat orologio")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>Allineamento riquadro

Tutti gli esempi illustrati finora è sono utilizzato lo shader creato da `SKShader.CreateBitmap` per coprire l'intera area di disegno. Nella maggior parte dei casi, si useranno in porzioni di bitmap per l'invio di aree più piccole o (più raramente) per il riempimento di righe spesse. Ecco il riquadro photographic brick-wall usato per un rettangolo più piccolo:

[![Riquadro di allineamento](bitmap-tiling-images/TileAlignment.png "riquadro di allineamento")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

Questo potrebbe avere un aspetto corretto all'utente, o forse no. Si supponga di essere disturbati che la modalità di affiancamento non inizia con un brick completo nell'angolo superiore sinistro del rettangolo. Ciò avviene perché gli shader sono allineati con l'area di disegno e non all'oggetto grafico sono contenuti.

La correzione è semplice. Creare un `SKMatrix` valore basato su una trasformazione di traslazione. La trasformazione passa in modo efficace il modello affiancato al punto in cui si desidera che l'angolo superiore sinistro del riquadro allineato. Questo metodo viene dimostrato nel **allineamento riquadro** pagina, che ha creato l'immagine dei riquadri non allineati illustrato in precedenza:

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

Il **allineamento Tile** pagina include un `TapGestureRecognizer`. Toccare o fare clic su schermo e i parametri del programma per la `SKShader.CreateBitmap` metodo con un `SKMatrix` argomento. Questa trasformazione passa il modello in modo che l'angolo superiore sinistro contenga un brick completo:

[![Riquadro di allineamento toccato](bitmap-tiling-images/TileAlignmentTapped.png "riquadro allineamento toccato")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

È anche possibile usare questa tecnica per garantire che il modello affiancato bitmap è centrato all'interno dell'area che il processo di generazione. Nel **riquadri centrato** pagina il `PaintSurface` gestore calcola prima di tutto le coordinate come se sta per visualizzare la bitmap singola al centro dell'area di disegno. Queste coordinate viene quindi utilizzato per creare una trasformazione di traslazione per `SKShader.CreateBitmap`. Questa trasformazione passa l'intero modello in modo che un riquadro è centrato:

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

Il `PaintSurface` gestore si conclude con disegno un cerchio nel centro dell'area di disegno. Naturalmente, uno dei riquadri è esattamente il centro del cerchio, e gli altri vengono disposte in un modello simmetrico:

[![Centrato tessere](bitmap-tiling-images/CenteredTiles.png "centrato riquadri")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

Un altro approccio centratura viene effettivamente un po' più semplice. Anziché costruire una trasformazione di traslazione che inserisce un riquadro al centro, è possibile centrare un angolo del modello affiancato. Nel `SKMatrix.MakeTranslation` chiamare, usare gli argomenti per il centro dell'area di disegno:

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

Il modello è ancora centrato e simmetrico, ma nessun riquadro si trova al centro:

[![Centrato Alternate di tessere](bitmap-tiling-images/CenteredTilesAlternate.png "centrato Alternate di tessere")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>Semplificazione con rotazione

Talvolta l'utilizzo di una trasformazione rotativa nel `SKShader.CreateBitmap` metodo può semplificare la tessera mappa di bit. Ciò diventa evidente quando si tenta di definire un riquadro per un limite di collegamento a catena. Il **ChainLinkTile.cs** file crea il riquadro visualizzato di seguito (con uno sfondo di colore rosa per maggiore chiarezza):

![Riquadro di collegamenti reali catena](bitmap-tiling-images/HardChainLinkTile.png "riquadro catena collegamenti reali")

Il riquadro deve includere due collegamenti, in modo che il codice il riquadro viene suddivisa in quattro quadranti. I quadranti alto a sinistra e basso a destra sono gli stessi, ma non sono completi. I cavi sono cui poco che devono essere gestiti con alcuni altri disegno in alto a destra e in basso a sinistra quadranti. Il file che esegue tutte queste operazioni è lunga 174 righe.

Si passerà a essere molto più semplice creare questo riquadro:

![Semplice riquadro catena](bitmap-tiling-images/EasierChainLinkTile.png "semplice riquadro collegamento a catena")

Se lo shader bitmap-riquadro viene ruotata di 90 gradi, gli oggetti visivi sono quasi gli stessi.

Il codice per creare il riquadro più facilmente di collegamento a catena fa parte il **catena riquadro** pagina. Il costruttore determina una dimensione in base al tipo di dispositivo su cui viene eseguita il programma e quindi chiama `CreateChainLinkTile`, che consente di disegnare sulla bitmap tramite linee, i percorsi e gli shader sfumatura:

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

Ad eccezione di cavi, il riquadro è trasparente, il che significa che è possibile visualizzarlo nella parte superiore di un altro elemento. Il programma carica in una delle risorse bitmap, consente di visualizzare in modo da riempire l'area di disegno e disegna lo shader in primo piano:

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

Si noti che lo shader è ruotata di 45 gradi, pertanto è orientata ai servizi, ad esempio un limite di catena reale:

[![Limite di collegamento a catena](bitmap-tiling-images/ChainLinkFence.png "recinto a catena")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>Animazione tessere mappa di bit

È possibile animare una serie intera bitmap-riquadro animando la trasformazione di matrice. Si supponga il motivo per spostare orizzontalmente o verticalmente o entrambi. È possibile farlo mediante la creazione di una trasformazione di conversione basata sulle coordinate mutevoli.

È anche possibile disegnare su una bitmap di ridotte dimensioni, o per modificare i bit di pixel della bitmap alla tariffa di 60 volte al secondo. Tale bitmap può essere utilizzato quindi per l'espansione e l'intero modello affiancato può sembrare da animare. 

Il **riquadro animato di Bitmap** è illustrato questo approccio. Una bitmap viene creata un'istanza come campo da 64 pixel quadrati. Il costruttore chiama `DrawBitmap` per conferirgli un aspetto iniziale. Se il `angle` campo è zero (come avviene quando il metodo viene chiamato prima di tutto), la mappa di bit include due righe incrociate come una X. Le righe vengono apportate sufficientemente lungo per raggiungere sempre al bordo della bitmap indipendentemente il `angle` valore: 

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

Si verifica l'overhead di animazione nel `OnAppearing` e `OnDisappearing` esegue l'override. Il `OnTimerTick` metodo anima la `angle` valore da 0 a 360 gradi ogni 10 secondi per ruotare la figura X della bitmap:

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

A causa la simmetria della figura X, questo è lo stesso come ruotare la `angle` valore compreso tra 0 gradi e 90 gradi ogni 2,5 secondi.

Il `PaintSurface` gestore consente di creare uno shader dalla mappa di bit e Usa l'oggetto di disegno per l'intera area di disegno dei colori:

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

Il `SKShaderTileMode.Mirror` opzioni assicurano che i rami di X in ogni bitmap join con la X nella bitmap di adiacenti per creare un modello animato complessivo che risulta molto più complesso rispetto a suggerirei di animazione semplice:

[![Bitmap riquadro animato](bitmap-tiling-images/AnimatedBitmapTile.png "Bitmap riquadro animato")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [CatClock (esempio)](https://developer.xamarin.com/samples/xamarin-forms/CatClock/)
