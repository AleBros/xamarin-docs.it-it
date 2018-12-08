---
title: Visualizzazione segmentata del bitmap di SkiaSharp
description: Visualizzare una bitmap di SkiaSharp in modo che alcuni area viene allungato e non sono alcune aree.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79AE2033-C41C-4447-95A6-76D22E913D19
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: be2d998916c81cb2216251e2112ff8ca2af5d87a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061368"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>Visualizzazione segmentata del bitmap di SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Di SkiaSharp `SKCanvas` oggetto definisce un metodo denominato `DrawBitmapNinePatch` e due metodi denominati `DrawBitmapLattice` che sono molto simili. Entrambi questi metodi eseguire il rendering di una bitmap per le dimensioni di un rettangolo di destinazione, ma invece di adattamento della bitmap in modo uniforme, visualizzare parti della bitmap in relative dimensioni in pixel e stretch altre parti della bitmap in modo che quest'ultima rientri il rettangolo:

![Segmentato campioni](segmented-images/SegmentedSample.png "segmentata di esempio")

Questi metodi vengono in genere usati per il rendering di bitmap che fanno parte di oggetti dell'interfaccia utente, ad esempio i pulsanti. Quando si progetta un pulsante, in generale è consigliabile la dimensione di un pulsante deve essere basato sul contenuto del pulsante, ma è preferibile il bordo del pulsante per la stessa larghezza indipendentemente dal contenuto del pulsante. Che è un'applicazione ideale di `DrawBitmapNinePatch`.

`DrawBitmapNinePatch` è un caso speciale di `DrawBitmapLattice` ma è il modo più semplice dei due metodi da utilizzare e comprendere.

## <a name="the-nine-patch-display"></a>La visualizzazione di nove-patch 

Concettualmente [ `DrawBitmapNinePatch` ](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint)) divide una bitmap in nove rettangoli:

![Patch di nove](segmented-images/NinePatch.png "nove Patch")

I rettangoli nei quattro angoli vengono visualizzati nelle relative dimensioni in pixel. Come le frecce indicano, le altre aree sui bordi della bitmap vengono allungate orizzontalmente o verticalmente all'area del rettangolo di destinazione. Il rettangolo al centro è esteso sia orizzontalmente che verticalmente. 

Se non vi è spazio sufficiente nel rettangolo di destinazione per visualizzare anche i quattro angoli nelle dimensioni in pixel, sono stati ridotti per le dimensioni disponibili e nothing ma vengono visualizzati i quattro angoli.

Per dividere una bitmap in questi nove rettangoli, è necessario solo specificare il rettangolo al centro. Questa è la sintassi del `DrawBitmapNinePatch` metodo:

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

Il rettangolo centrale è relativo alla bitmap. Si tratta di un' `SKRectI` valore (la versione di numero intero di `SKRect`) e tutte le coordinate e le dimensioni sono espressi in unità di pixel. Il rettangolo di destinazione è relativo alla superficie di visualizzazione. L'argomento `paint` è facoltativo.

Il **nove Patch Display** nella pagina il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio Usa prima di tutto un costruttore statico per creare una proprietà statica pubblica di tipo `SKBitmap`:

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

Due altre pagine in questo articolo usano la stessa bitmap. La bitmap è 500 pixel quadrati ed è costituito da una matrice di 25 cerchi, le stesse dimensioni, ogni che occupa un'area di quadrati di 100 pixel:

![Cerchio griglia](segmented-images/CircleGrid.png "Circle griglia")

Costruttore di istanza del programma crea un' `SKCanvasView` con un `PaintSurface` gestore che usa `DrawBitmapNinePatch` per visualizzare la bitmap allungata sulla sua superficie schermo intero:

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

Il `centerRect` rettangolo comprende la matrice centrale dei 16 cerchi. I cerchi negli angoli vengono visualizzati nelle dimensioni in pixel e tutto il resto viene adattata conseguenza:

[![Visualizzazione di nove-Patch](segmented-images/NinePatchDisplay.png "Display nove-Patch")](segmented-images/NinePatchDisplay-Large.png#lightbox)

La pagina UWP è 500 pixel di larghezza e pertanto vengono visualizzate le righe superiore e inferiore come una serie di cerchi le stesse dimensioni. In caso contrario, tutti i cerchi che non sono presenti negli angoli vengono adattati in ellissi form.

Per una visualizzazione strano di oggetti costituiti da una combinazione di cerchi e sui puntini di sospensione, provare a eseguire che definisce il rettangolo di center in modo che si sovrappone a righe e colonne di cerchi:

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>La visualizzazione reticolo

I due `DrawBitmapLattice` metodi sono simili a `DrawBitmapNinePatch`, ma sono generalizzati per qualsiasi numero di divisioni orizzontali o verticali. Queste divisioni sono definite da matrici di interi corrispondenti a pixel. 

Il [ `DrawBitmapLattice` ](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint)) metodo con parametri per le matrici di interi non sembra funzionare. Il [ `DrawBitmapLattice` ](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint)) metodo con un parametro di tipo `SKLattice` funziona, e che corrisponde a quella usata negli esempi illustrati di seguito.

Il [ `SKLattice` ](xref:SkiaSharp.SKLattice) struttura definisce quattro proprietà:

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs), una matrice di interi
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs), una matrice di interi
- [`Flags`](xref:SkiaSharp.SKLattice.Flags), una matrice di `SKLatticeFlags`, un tipo di enumerazione
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds) di tipo `Nullable<SKRectI>` per specificare un rettangolo di origine facoltativa della bitmap

Il `XDivs` matrice divide la larghezza della bitmap in strisce verticali. La prima striscia si estende dal pixel da 0 a sinistra per `XDivs[0]`. Questo elenco viene visualizzato in larghezza in pixel. Elenco di secondo compreso tra `XDivs[0]` a `XDivs[1]`e viene ridimensionato. Il terzo elenco compreso tra `XDivs[1]` a `XDivs[2]` e viene eseguito il rendering nella relativa larghezza in pixel. Striscia di ultima compreso tra l'ultimo elemento della matrice e il bordo destro della bitmap. Se la matrice ha un numero pari di elementi, viene visualizzato nel relativo larghezza in pixel. In caso contrario, viene estesa. Il numero totale di Sfoglia verticale è una maggiore del numero di elementi nella matrice.

Il `YDivs` matrice è simile. Divide l'altezza della matrice in strisce orizzontali.

Insieme, il `XDivs` e `YDivs` matrice consentono di dividere la bitmap in rettangoli. Il numero di rettangoli è uguale al prodotto del numero di strisce orizzontali e il numero di strisce verticali.

In base alla documentazione, Skia il `Flags` matrice contiene un elemento per ogni rettangolo, prima di tutto la riga superiore dei rettangoli, quindi la seconda riga e così via. Il `Flags` matrice è di tipo [ `SKLatticeFlags` ](xref:SkiaSharp.SKLatticeFlags), un'enumerazione con i membri seguenti:

- `Default` con il valore 0
- `Transparent` con valore 1

Tuttavia, questi flag non sembrano funzionare come che sono autorizzati a e si consiglia di ignorarli. Ma non vengono impostate le `Flags` proprietà `null`. Impostarla su una matrice di `SKLatticeFlags` valori sufficientemente ampio da contenere il numero totale di rettangoli.

Il **reticolo nove Patch** pagina Usa `DrawBitmapLattice` simulare `DrawBitmapNinePatch`. Usa la stessa bitmap creata in `NinePatchDisplayPage`:

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

Sia la `XDivs` e `YDivs` proprietà vengono impostate su matrici di due numeri interi, dividere la bitmap in tre strisce sia orizzontalmente che verticalmente: dal pixel 0 a 100 (con rendering le dimensioni in pixel), in pixel dal pixel 100 a 400 pixel (esteso), e dal pixel 400 e 500 (le dimensioni in pixel). Insieme, `XDivs` e `YDivs` definiscono un totale di 9 rettangoli, ovvero le dimensioni del `Flags` matrice. È sufficiente creare la matrice è sufficiente per creare una matrice di `SKLatticeFlags.Default` valori.

La visualizzazione è identica al precedente programma:

[![Reticolo nove-Patch](segmented-images/LatticeNinePatch.png "reticolo nove-Patch")](segmented-images/LatticeNinePatch-Large.png#lightbox)

Il **reticolo visualizzazione** pagina divide la bitmap in 16 rettangoli:

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

Il `XDivs` e `YDivs` le matrici sono piuttosto diverse, causando la visualizzazione a non essere abbastanza come simmetrico come negli esempi precedenti:

[![Visualizzazione reticolo](segmented-images/LatticeDisplay.png "reticolo visualizzazione")](segmented-images/LatticeDisplay-Large.png#lightbox)

In iOS e Android immagini a sinistra, vengono visualizzati solo i più piccoli cerchi nelle relative dimensioni in pixel. Tutto il resto viene adattata.

Il **reticolo Display** pagina generalizza la creazione delle `Flags` matrice, che consente di sperimentare `XDivs` e `YDivs` più facilmente. In particolare, è opportuno vedere cosa accade quando si imposta il primo elemento della `XDivs` o `YDivs` array su 0. 

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
