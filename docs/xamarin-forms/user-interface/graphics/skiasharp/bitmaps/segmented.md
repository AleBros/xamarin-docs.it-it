---
title: "visualizzazione segmentata di bitmap SkiaSharp" Description: "visualizzare una bitmap SkiaSharp in modo che alcune aree siano allungate e che alcune aree non lo siano".
ms. prod: Novell MS. Technology: Novell-skiasharp ms. AssetID: 79AE2033-C41C-4447-95A6-76D22E913D19 autore: davidbritch ms. Author: dabritch ms. Date: 07/17/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="segmented-display-of-skiasharp-bitmaps"></a>Visualizzazione segmentata delle bitmap SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

L' `SKCanvas` oggetto SkiaSharp definisce un metodo denominato `DrawBitmapNinePatch` e due metodi denominati `DrawBitmapLattice` molto simili. Entrambi questi metodi eseguono il rendering di una bitmap sulle dimensioni di un rettangolo di destinazione, ma invece di allungare la bitmap in modo uniforme, visualizzano parti della bitmap nelle dimensioni dei pixel ed estendono altre parti della bitmap in modo che corrispondano al rettangolo:

![Esempi segmentati](segmented-images/SegmentedSample.png "Esempio segmentato")

Questi metodi vengono in genere utilizzati per il rendering di bitmap che fanno parte di oggetti dell'interfaccia utente, ad esempio i pulsanti. Quando si progetta un pulsante, in genere si vuole che le dimensioni di un pulsante siano basate sul contenuto del pulsante, ma è probabile che il bordo del pulsante sia la stessa larghezza indipendentemente dal contenuto del pulsante. Si tratta di un'applicazione ideale di `DrawBitmapNinePatch` .

`DrawBitmapNinePatch`è un caso speciale di, `DrawBitmapLattice` ma è più semplice dei due metodi da usare e comprendere.

## <a name="the-nine-patch-display"></a>Visualizzazione di nove patch 

Concettualmente, [`DrawBitmapNinePatch`](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint)) divide una bitmap in nove rettangoli:

![Nove patch](segmented-images/NinePatch.png "Nove patch")

I rettangoli ai quattro angoli vengono visualizzati nelle dimensioni dei pixel. Come indicato dalle frecce, le altre aree sui bordi della bitmap sono allungate orizzontalmente o verticalmente all'area del rettangolo di destinazione. Il rettangolo al centro è allungato orizzontalmente e verticalmente. 

Se lo spazio nel rettangolo di destinazione non è sufficiente per visualizzare anche i quattro angoli nelle dimensioni dei pixel, vengono ridimensionati fino alla dimensione disponibile e non vengono visualizzati i quattro angoli.

Per dividere una bitmap in questi nove rettangoli, è sufficiente specificare il rettangolo al centro. Questa è la sintassi del `DrawBitmapNinePatch` Metodo:

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

Il rettangolo centrale è relativo alla bitmap. Si tratta di un `SKRectI` valore (la versione Integer di `SKRect` ) e tutte le coordinate e le dimensioni sono espresse in unità di pixel. Il rettangolo di destinazione è relativo alla superficie di visualizzazione. L'argomento `paint` è facoltativo.

La pagina di **visualizzazione di nove patch** nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) USA innanzitutto un costruttore statico per creare una proprietà statica pubblica di tipo `SKBitmap` :

```csharp
public partial class NinePatchDisplayPage : ContentPage
{
    static NinePatchDisplayPage()
    {
        using (SKCanvas canvas = new SKCanvas(FiveByFiveBitmap))
        using (SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 10
        })
        {
            for (int x = 50; x < 500; x += 100)
                for (int y = 50; y < 500; y += 100)
                {
                    canvas.DrawCircle(x, y, 40, paint);
                }
        }
    }

    public static SKBitmap FiveByFiveBitmap { get; } = new SKBitmap(500, 500);
    ···
}
```

Altre due pagine in questo articolo usano la stessa bitmap. La bitmap è 500 pixel al quadrato ed è costituita da una matrice di 25 cerchi, tutte le stesse dimensioni, ognuno dei quali occupa un'area quadrata di 100 pixel:

![Griglia cerchio](segmented-images/CircleGrid.png "Griglia cerchio")

Il costruttore di istanze del programma crea un oggetto `SKCanvasView` con un `PaintSurface` gestore che utilizza `DrawBitmapNinePatch` per visualizzare la bitmap estesa all'intera superficie di visualizzazione:

```csharp
public class NinePatchDisplayPage : ContentPage
{
    ···
    public NinePatchDisplayPage()
    {
        Title = "Nine-Patch Display";

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

        SKRectI centerRect = new SKRectI(100, 100, 400, 400);
        canvas.DrawBitmapNinePatch(FiveByFiveBitmap, centerRect, info.Rect);
    }
}
```

Il `centerRect` rettangolo comprende la matrice centrale di 16 cerchi. I cerchi negli angoli vengono visualizzati nelle dimensioni in pixel e tutti gli altri elementi vengono allungati di conseguenza:

[![Visualizzazione a nove patch](segmented-images/NinePatchDisplay.png "Visualizzazione a nove patch")](segmented-images/NinePatchDisplay-Large.png#lightbox)

La pagina UWP ha una larghezza di 500 pixel, quindi Visualizza le righe superiore e inferiore come una serie di cerchi delle stesse dimensioni. In caso contrario, tutti i cerchi che non sono negli angoli vengono allungati per formare ellissi.

Per una visualizzazione sconosciuta di oggetti costituiti da una combinazione di cerchi e ellissi, provare a definire il rettangolo centrale in modo che si sovrappongano a righe e colonne di cerchi:

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>Visualizzazione Reticolo

I due `DrawBitmapLattice` metodi sono simili a `DrawBitmapNinePatch` , ma sono generalizzati per un numero qualsiasi di divisioni orizzontali o verticali. Queste divisioni sono definite da matrici di numeri interi corrispondenti ai pixel. 

Il [`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint)) metodo con parametri per queste matrici di integer non sembra funzionare. Il [`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint)) metodo con un parametro di tipo funziona `SKLattice` e questo è quello usato negli esempi riportati di seguito.

La [`SKLattice`](xref:SkiaSharp.SKLattice) struttura definisce quattro proprietà:

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs), una matrice di numeri interi
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs), una matrice di numeri interi
- [`Flags`](xref:SkiaSharp.SKLattice.Flags), una matrice di `SKLatticeFlags` , un tipo di enumerazione
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds)di tipo `Nullable<SKRectI>` per specificare un rettangolo di origine facoltativo nella bitmap

La `XDivs` matrice divide la larghezza della bitmap in strisce verticali. La prima striscia si estende dal pixel 0 a sinistra a `XDivs[0]` . Il rendering di questa striscia viene eseguito con la larghezza in pixel. La seconda striscia si estende da `XDivs[0]` a `XDivs[1]` e viene allungata. La terza striscia si estende da `XDivs[1]` a e viene sottoposta `XDivs[2]` a rendering con la larghezza in pixel. L'ultima striscia si estende dall'ultimo elemento della matrice al bordo destro della bitmap. Se la matrice ha un numero pari di elementi, viene visualizzata con la larghezza in pixel. In caso contrario, viene esteso. Il numero totale di strisce verticali è maggiore di uno rispetto al numero di elementi nella matrice.

La `YDivs` matrice è simile. Divide l'altezza della matrice in strisce orizzontali.

Insieme, la `XDivs` `YDivs` matrice e divide la bitmap in rettangoli. Il numero di rettangoli equivale al prodotto del numero di strisce orizzontali e al numero di strisce verticali.

Secondo la documentazione di Skia, la `Flags` matrice contiene un elemento per ogni rettangolo, prima la riga superiore dei rettangoli, la seconda riga e così via. La `Flags` matrice è di tipo [`SKLatticeFlags`](xref:SkiaSharp.SKLatticeFlags) , un'enumerazione con i membri seguenti:

- `Default`con valore 0
- `Transparent`con valore 1

Tuttavia, questi flag non sembrano funzionare come previsto ed è preferibile ignorarli. Ma non impostare la `Flags` proprietà su `null` . Impostarla su una matrice di `SKLatticeFlags` valori sufficientemente grande da includere il numero totale di rettangoli.

La pagina del **reticolo Nine (patch Nine** ) USA `DrawBitmapLattice` per simulare `DrawBitmapNinePatch` . Usa la stessa bitmap creata in `NinePatchDisplayPage` :

```csharp
public class LatticeNinePatchPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeNinePatchPage ()
    {
        Title = "Lattice Nine-Patch";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    `
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 400 };
        lattice.YDivs = new int[] { 100, 400 };
        lattice.Flags = new SKLatticeFlags[9]; 

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

Entrambe le `XDivs` `YDivs` proprietà e sono impostate su matrici costituite solo da due numeri interi, dividendo la bitmap in tre strisce sia orizzontalmente che verticalmente: dal pixel 0 al pixel 100 (sottoposto a rendering in pixel), dal pixel 100 al pixel 400 (con estensione) e dal pixel 400 al pixel 500 (dimensioni pixel). Insieme, `XDivs` e `YDivs` definiscono un totale di 9 rettangoli, ovvero la dimensione della `Flags` matrice. La semplice creazione della matrice è sufficiente per creare una matrice di `SKLatticeFlags.Default` valori.

La visualizzazione è identica a quella del programma precedente:

[![Reticolo nove patch](segmented-images/LatticeNinePatch.png "Reticolo nove patch")](segmented-images/LatticeNinePatch-Large.png#lightbox)

La pagina di **visualizzazione del reticolo** divide la bitmap in 16 rettangoli:

```csharp
public class LatticeDisplayPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeDisplayPage()
    {
        Title = "Lattice Display";

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

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 200, 400 };
        lattice.YDivs = new int[] { 100, 300, 400 };

        int count = (lattice.XDivs.Length + 1) * (lattice.YDivs.Length + 1);
        lattice.Flags = new SKLatticeFlags[count];

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

Le `XDivs` `YDivs` matrici e sono leggermente diverse, causando la visualizzazione non simmetrica degli esempi precedenti:

[![Visualizzazione Reticolo](segmented-images/LatticeDisplay.png "Visualizzazione Reticolo")](segmented-images/LatticeDisplay-Large.png#lightbox)

Nelle immagini iOS e Android a sinistra vengono visualizzati solo i cerchi più piccoli nelle dimensioni dei pixel. Tutto il resto è allungato.

La pagina di **visualizzazione del reticolo** generalizza la creazione della `Flags` matrice, consentendo di sperimentare `XDivs` e `YDivs` più facilmente. In particolare, è opportuno vedere cosa accade quando si imposta il primo elemento della `XDivs` `YDivs` matrice o su 0. 

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
