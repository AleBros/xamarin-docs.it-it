---
title: Filtri di colore in SkiaSharp
description: Usare i filtri di colore per convertire i colori con trasformazioni o le tabelle.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
ms.openlocfilehash: 71c0495520a5dd596be2e9cafec6b63e316fb627
ms.sourcegitcommit: c6ff24b524d025d7e87b7b9c25f04c740dd93497
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56240318"
---
# <a name="skiasharp-color-filters"></a>Filtri di colore in SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

Filtri del colore può convertire i colori in una mappa di bit (o altra immagine) per gli altri colori per gli effetti, ad esempio posterizzazione:

![Esempio di filtri di colore](color-filters-images/ColorFiltersExample.png "esempio filtri di colore")

Per usare un filtro di colore, impostare il [ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter) proprietà della `SKPaint` a un oggetto di tipo [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) creati mediante uno dei metodi statici nella classe. Questo articolo illustra: 

- creare una trasformazione di colore con il [ `CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) (metodo).
- una tabella creata con il [ `CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*) (metodo).

## <a name="the-color-transform"></a>La trasformazione di colore

La trasformazione di colore prevede l'uso di una matrice per modificare i colori. Come la maggior parte dei sistemi di grafica 2D, SkiaSharp matrici viene utilizzato principalmente per la trasformazione dei punti delle coordinate come iscussed nell'articolo [ **trasformazioni di matrice in SkiaSharp**](../transforms/matrix.md). Il [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) supporta anche le trasformazioni di matrice, ma le trasformazioni con matrice dei colori RGB. Familiarità con i concetti di matrice è necessario comprendere queste trasformazioni di colore. 

Colore-trasformazione matrice ha una dimensione di quattro righe e cinque colonne:

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

Trasforma un colore di origine RGB (rosso, verde e B, A) per il colore di destinazione (R', G', B ','). 

In preparazione per la moltiplicazione di matrici, il colore di origine viene convertito in una matrice di 5 × 1:

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

Questi valori R, G, B e oggetto sono i byte originali compreso tra 0 e 255. Si trovano _non_ normalizzati in base a valori a virgola mobile compreso nell'intervallo da 0 a 1.

La cella aggiuntiva è necessaria per un fattore di conversione. Questo comportamento è analogo all'utilizzo di una matrice 3x3 trasformare punti delle coordinate bidimensionali, come descritto nella sezione [ **il motivo per la matrice 3 per 3** ](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix) nell'articolo sull'uso di matrici per la trasformazione i punti delle coordinate.

Matrice 4 × 5 viene moltiplicata per la matrice di 5 × 1 e il prodotto è una matrice 4 × 1 con il colore trasformato:

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

Di seguito sono le formule separate per R', G', 'B e A':

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

La maggior parte della matrice è costituita da moltiplicazione fattori che sono in genere compreso nell'intervallo compreso tra 0 e 2. Tuttavia, l'ultima colonna (M15 tramite M45) contiene valori che vengono aggiunti nelle formule. Questi valori in genere compresa tra 0 e 255. I risultati sono compresi tra i valori 0 e 255.

Matrice di identità è:

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

Ciò non comporta alcuna modifica ai colori. Le formule di trasformazione sono:

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

La cella M44 è molto importante perché mantiene opacità. È in genere il caso che M41 M42 e M43 sono tutti pari a zero perché non è opportuno opacità deve essere basato sui valori rossi, verdi e blu. Tuttavia, se M44 è zero, l'oggetto ' sarà zero e non sarà visibile.

Uno degli usi più comuni della matrice di colori consiste nel convertire una bitmap a colori per una bitmap gradazioni di grigio. Ciò comporta una formula per una media ponderata dei valori di colore rosso, verde e blu. Per gli schermi video con spazio dei colori sRGB ("standard rosso verde blu"), questa formula è:

gradazione di grigio = 0.2126· R + 0.7152· G + 0.0722· B

Per convertire una bitmap a colori una bitmap gradazioni di grigio, l'oggetto R', G', e tutti i risultati di B' deve essere uguale quello stesso valore. La matrice è:

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

Non vi è alcun tipo di dati di SkiaSharp che corrisponde a questa matrice. È invece necessario rappresentare la matrice come matrice di 20 `float` valori in ordine di riga: prima riga, quindi seconda riga e così via.

Il metodo statico [ `SKColorFilter.CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) metodo ha la sintassi seguente:

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

in cui `matrix` è una matrice di 20 `float` valori. Quando si crea la matrice in C#, è facile formattare i numeri in modo assomigliano matrice 4 × 5. Come illustrato nel **matrice gradazioni di grigio** pagina il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio:

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

Il `DrawBitmap` metodo usato in questo codice è dal **BitmapExtension.cs** file incluso con il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) esempio. 

Ecco il risultato in esecuzione in iOS, Android e Universal Windows Platform:

[![Matrice di scala di grigi](color-filters-images/GrayScaleMatrix.png "matrice di scala di grigi")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

Il valore nella quarta riga e della quarta colonna, prestare attenzione. Questo è il fattore fondamentale che viene moltiplicato il valore del colore originale per l'oggetto "valore del colore trasformato. Se questa cella è zero, verrà visualizzato nulla e il problema potrebbe essere difficile da individuare.

Durante la sperimentazione con matrici di colore, è possibile considerare la trasformazione dal punto di vista dell'origine o la prospettiva della destinazione. Come il colore rosso pixel dell'origine incoraggiati a contribuire al pixel rosso, verde e blu di destinazione? Che è determinata dai valori nel primo _colonna_ della matrice. In alternativa, come il pixel di destinazione rossi saranno interessato dai pixel rossi, verdi e blu dell'origine? Che viene determinato dal primo _riga_ della matrice.

Per alcune idee su come usare le trasformazioni di colore, vedere la [ **ricolorazione di immagini** ](https://docs.microsoft.com/dotnet/framework/winforms/advanced/recoloring-images) pagine. La descrizione riguarda Windows Form e la matrice è un formato diverso, ma i concetti sono uguali.

Il **Pastello matrice** calcola il pixel di destinazione rossi attenuazione pixel origine rosso e leggermente che mettono in evidenza i pixel rossi e verdi. Questo processo si verifica in modo analogo per i pixel verdi e blu:

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

Il risultato è di disattivare l'intensità dei colori, come illustrato di seguito:

[![Matrice Pastello](color-filters-images/PastelMatrix.png "Pastello matrice")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>Tabelle di colore

Il metodo statico [ `SKColorFilter.CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*) metodo disponibile in due versioni:

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

Le matrici sempre contengono le voci di 256. Nel `CreateTable` metodo con una tabella, la stessa tabella viene utilizzato per i componenti rossi, verdi e blu. È una tabella di ricerca semplice: Se il colore di origine (R, G, B) e il colore di destinazione è (R', 'B, G'), quindi i componenti di destinazione vengono ottenuti dall'indicizzazione `table` con i componenti di origine:

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

Il secondo metodo, ognuno dei quattro componenti di colore può avere una tabella separata o le tabelle dei colori stesso potrebbero essere condivisa tra due o più componenti.

Se si desidera impostare uno degli argomenti al secondo `CreateTable` metodo di una tabella dei colori che contiene i valori 0 e 255 in sequenza, è possibile usare `null` invece. Molto spesso i `CreateTable` chiamata ha un `null` primo argomento per il canale alfa.

Nella sezione **posterizzazione** nell'articolo sulla [SkiaSharp l'accesso ai bit di pixel bitmap](../bitmaps/pixel-bits.md#posterization), è stato illustrato come modificare i bit dei singoli pixel della bitmap a ridurne la risoluzione di colore. Si tratta di una tecnica detta _posterizzazione_. 

È anche possibile posterizzazione una bitmap con una tabella dei colori. Il costruttore del **posterizzazione tabella** pagina consente di creare una tabella dei colori che esegue il mapping di indice corrispondente a un byte con la parte inferiore 6 bit impostati su zero:

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

Il programma sceglie di usare questa tabella colori solo per i canali di colore verde e blu. Il canale di colore rosso continua ad avere risoluzione completa:

[![Tabella posterizzazione](color-filters-images/PosterizeTable.png "posterizzazione tabella")](color-filters-images/PosterizeTable-Large.png#lightbox)

È possibile usare le diverse tabelle di colore per i canali di colore diverso per vari effetti. 

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
