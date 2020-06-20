---
title: Filtri colori SkiaSharp
description: Usare i filtri colori per convertire i colori con le trasformazioni o le tabelle.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b9c89d4d426884d678e77687ffa226cced97be58
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136383"
---
# <a name="skiasharp-color-filters"></a>Filtri colori SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

I filtri colori possono convertire i colori in una bitmap (o un'altra immagine) in altri colori per gli effetti, ad esempio la posterzzazione:

![Esempio di filtri colori](color-filters-images/ColorFiltersExample.png "Esempio di filtri colori")

Per usare un filtro colori, impostare la [`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) proprietà di `SKPaint` su un oggetto di tipo [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) creato da uno dei metodi statici in tale classe. Questo articolo illustra: 

- trasformazione colore creata con il [`CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) metodo.
- tabella dei colori creata con il [`CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) metodo.

## <a name="the-color-transform"></a>Trasformazione colore

La trasformazione colore prevede l'utilizzo di una matrice per modificare i colori. Come per la maggior parte dei sistemi grafici 2D, SkiaSharp USA matrici per la trasformazione di punti di coordinate come imprecate nelle [**trasformazioni di matrici di articoli in SkiaSharp**](../transforms/matrix.md). [`SKColorFilter`](xref:SkiaSharp.SKColorFilter)Supporta inoltre le trasformazioni di matrice, ma la matrice trasforma i colori RGB. Una certa familiarità con i concetti di matrice è necessaria per comprendere le trasformazioni dei colori. 

La matrice di trasformazione dei colori ha una dimensione di quattro righe e cinque colonne:

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

Trasforma un colore di origine RGB (R, G, B, A) nel colore di destinazione (R ', G ', B ', A '). 

In preparazione per la moltiplicazione di matrici, il colore di origine viene convertito in una matrice di 5 × 1:

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

Questi valori R, G, B e sono i byte originali compresi tra 0 e 255. _Non_ vengono normalizzati in valori a virgola mobile nell'intervallo compreso tra 0 e 1.

La cella aggiuntiva è necessaria per un fattore di conversione. Questo è analogo all'uso di una matrice 3 × 3 per trasformare punti di coordinate bidimensionali, come descritto nella sezione [**motivo della matrice 3 per 3**](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix) nell'articolo sull'utilizzo di matrici per la trasformazione di punti di coordinate.

La matrice 4 × 5 viene moltiplicata per la matrice 5 × 1 e il prodotto è una matrice di 4 × 1 con il colore trasformato:

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

Di seguito sono riportate le formule separate per R ', G ', B ' è:

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

La maggior parte della matrice è costituita da fattori moltiplicativi generalmente compresi tra 0 e 2. L'ultima colonna (M15 tramite M45), tuttavia, contiene valori aggiunti nelle formule. Questi valori sono generalmente compresi tra 0 e 255. I risultati vengono fissati tra i valori 0 e 255.

La matrice di identità è:

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

Questa operazione non comporta alcuna modifica ai colori. Le formule di trasformazione sono:

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

La cella M44 è molto importante perché preserva l'opacità. Si tratta in genere del caso in cui M41, M42 e M43 sono tutti zero, perché probabilmente non si vuole che l'opacità sia basata sui valori rosso, verde e blu. Ma se M44 è zero,' sarà zero e non sarà visibile alcun elemento.

Uno degli usi più comuni della matrice di colori è la conversione di una mappa di bit di colore in una bitmap con scala in grigio. Si tratta di una formula per una media ponderata dei valori rosso, verde e blu. Per le visualizzazioni video che usano lo spazio colore sRGB ("Red Red Green Blue"), questa formula è:

grigio-ombreggiatura = 0,2126 · R + 0,7152 · G + 0,0722 · B

Per convertire una mappa di bit di colore in una bitmap con scala di grigia, i risultati di R ', G ' e B ' devono essere tutti uguali allo stesso valore. La matrice è:

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

Nessun tipo di dati SkiaSharp corrispondente a questa matrice. È invece necessario rappresentare la matrice come matrice di 20 `float` valori nell'ordine delle righe: prima riga, poi seconda riga e così via.

Il [`SKColorFilter.CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) metodo statico presenta la sintassi seguente:

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

dove `matrix` è una matrice di 20 `float` valori. Quando si crea la matrice in C#, è facile formattare i numeri in modo che siano simili alla matrice 4 × 5. Questa operazione viene illustrata nella pagina **matrice della scala di grigi** nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

```csharp
public class GrayScaleMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");

    public GrayScaleMatrixPage()
    {
        Title = "Gray-Scale Matrix";

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
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0,     0,     0,     1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Il `DrawBitmap` metodo usato in questo codice è dal file **BitmapExtension.cs** incluso nell'esempio [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . 

Ecco il risultato eseguito in iOS, Android e piattaforma UWP (Universal Windows Platform):

[![Matrice con scalabilità grigia](color-filters-images/GrayScaleMatrix.png "Matrice con scalabilità grigia")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

Osservare il valore della quarta riga e della quarta colonna. Questo è il fattore cruciale moltiplicato per il valore del colore originale per il valore di A del colore trasformato. Se la cella è zero, non verrà visualizzato nulla e il problema potrebbe essere difficile da individuare.

Quando si sperimentano matrici di colore, è possibile considerare la trasformazione dal punto di vista dell'origine o dalla prospettiva della destinazione. In che modo il pixel rosso dell'origine contribuisce ai pixel rosso, verde e blu della destinazione? Determinato dai valori nella prima _colonna_ della matrice. In alternativa, in che modo deve essere influenzato il pixel rosso di destinazione dai pixel rosso, verde e blu dell'origine? Determinato dalla prima _riga_ della matrice.

Per alcune idee su come usare le trasformazioni dei colori, vedere le pagine relative alle [**Immagini di ricoloring**](https://docs.microsoft.com/dotnet/framework/winforms/advanced/recoloring-images) . La discussione riguarda Windows Forms e la matrice ha un formato diverso, ma i concetti sono gli stessi.

La **matrice pastella** calcola il pixel rosso di destinazione attenuando il pixel rosso di origine e evidenziando leggermente i pixel rossi e verdi. Questo processo si verifica in modo analogo per i pixel verde e blu:

```csharp
public class PastelMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PastelMatrixPage),
                        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    public PastelMatrixPage()
    {
        Title = "Pastel Matrix";

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
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.75f, 0.25f, 0.25f, 0, 0,
                    0.25f, 0.75f, 0.25f, 0, 0,
                    0.25f, 0.25f, 0.75f, 0, 0,
                    0, 0, 0, 1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Il risultato è quello di disattivare l'intensità dei colori come è possibile vedere qui:

[![Matrice pastello](color-filters-images/PastelMatrix.png "Matrice pastello")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>Tabelle colori

Il metodo statico è costituito [`SKColorFilter.CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) da due versioni:

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

Le matrici contengono sempre 256 voci. Nel `CreateTable` metodo con una tabella, viene utilizzata la stessa tabella per i componenti rosso, verde e blu. Si tratta di una tabella di ricerca semplice: se il colore di origine è (R, G, B) e il colore di destinazione è (R ', B ', G '), i componenti di destinazione vengono ottenuti tramite l'indicizzazione `table` con i componenti di origine:

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

Nel secondo metodo, ognuno dei quattro componenti colore può avere una tabella dei colori separata oppure le stesse tabelle dei colori possono essere condivise tra due o più componenti.

Se si vuole impostare uno degli argomenti sul secondo `CreateTable` metodo su una tabella dei colori che contiene i valori da 0 a 255 in sequenza, è possibile usare `null` invece. Molto spesso la `CreateTable` chiamata ha un `null` primo argomento per il canale alfa.

Nella sezione relativa alla **poster** nell'articolo relativo all' [accesso a SkiaSharp bitmap pixel bits](../bitmaps/pixel-bits.md#posterization)è stato illustrato come modificare i singoli bit di pixel di una bitmap per ridurne la risoluzione dei colori. Si tratta di una tecnica denominata _posterzzazione_. 

È anche possibile posterizzazione una bitmap con una tabella dei colori. Il costruttore della pagina della **tabella posterizzazione** crea una tabella dei colori che esegue il mapping dell'indice a un byte con i 6 bit inferiori impostati su zero:

```csharp
public class PosterizeTablePage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PosterizeTablePage),
                        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    byte[] colorTable = new byte[256];

    public PosterizeTablePage()
    {
        Title = "Posterize Table";

        // Create color table
        for (int i = 0; i < 256; i++)
        {
            colorTable[i] = (byte)(0xC0 & i);
        }

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
            paint.ColorFilter =
                SKColorFilter.CreateTable(null, null, colorTable, colorTable);

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Il programma sceglie di usare questa tabella dei colori solo per i canali verde e blu. Il canale rosso continua a avere la risoluzione completa:

[![Tabella Posterizzazione](color-filters-images/PosterizeTable.png "Tabella Posterizzazione")](color-filters-images/PosterizeTable-Large.png#lightbox)

È possibile utilizzare varie tabelle dei colori per i diversi canali di colore per diversi effetti. 

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
