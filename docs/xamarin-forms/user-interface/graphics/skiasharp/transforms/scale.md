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
ms.openlocfilehash: bdf33f499bf43d99436cef815c03d35b27866b80
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140179"
---
# <a name="the-scale-transform"></a>Trasformazione di ridimensionamento

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Individuare la trasformazione scalabilità SkiaSharp per ridimensionare gli oggetti a diverse dimensioni_

Come illustrato nell'articolo [**tradurre la trasformazione**](translate.md) , la trasformazione translate può spostare un oggetto grafico da una posizione a un'altra. Al contrario, la trasformazione scala modifica la dimensione dell'oggetto grafico:

![](scale-images/scaleexample.png "A tall word scaled in size")

Anche la trasformazione scala causa spesso lo spostamento delle coordinate grafiche Man mano che vengono rese più grandi.

In precedenza sono state riportate due formule di trasformazione che descrivono gli effetti dei fattori di traduzione di `dx` e `dy` :

x ' = x + DX

y ' = y + dy

I fattori di scala di `sx` e `sy` sono moltiplicativi anziché additivi:

x ' = SX · x

y ' = sy · y

I valori predefiniti dei fattori di conversione sono 0; i valori predefiniti dei fattori di scala sono 1.

La `SKCanvas` classe definisce quattro `Scale` metodi. Il primo [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single)) metodo è per i casi in cui si desidera lo stesso fattore di scala orizzontale e verticale:

```csharp
public void Scale (Single s)
```

Questa operazione è nota *isotropic* come &mdash; ridimensionamento del ridimensionamento del formato del filtro dei propri in entrambe le direzioni. Il ridimensionamento del formato del filtro dei test conserva le proporzioni dell'oggetto.

Il secondo [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) metodo consente di specificare valori diversi per la scalabilità orizzontale e verticale:

```csharp
public void Scale (Single sx, Single sy)
```

In questo modo si ottiene la scalabilità *anisotropico* .
Il terzo [`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) metodo combina i due fattori di scalabilità in un singolo `SKPoint` valore:

```csharp
public void Scale (SKPoint size)
```

Il quarto `Scale` metodo verrà descritto a breve.

La pagina **scala di base** illustra il `Scale` metodo. Il file [**BasicScalePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) contiene due `Slider` elementi che consentono di selezionare i fattori di scala orizzontale e verticale compresi tra 0 e 10. Il file code-behind [**BasicScalePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) usa questi valori per chiamare `Scale` prima di visualizzare un rettangolo arrotondato con una linea tratteggiata e dimensionato per adattarlo a un testo nell'angolo superiore sinistro dell'area di disegno:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

Ci si potrebbe chiedere: in che modo i fattori di scala influiscono sul valore restituito dal `MeasureText` metodo di `SKPaint` ? La risposta è: non tutti. `Scale`è un metodo di `SKCanvas` . Non influisce sulle operazioni eseguite con un `SKPaint` oggetto fino a quando non si usa tale oggetto per eseguire il rendering di un elemento nell'area di disegno.

Come si può notare, tutto ciò che viene creato dopo la `Scale` chiamata aumenta proporzionalmente:

[![](scale-images/basicscale-small.png "Triple screenshot of the Basic Scale page")](scale-images/basicscale-large.png#lightbox "Triple screenshot of the Basic Scale page")

Il testo, la larghezza della linea tratteggiata, la lunghezza dei trattini della linea, l'arrotondamento degli angoli e il margine di 10 pixel tra i bordi sinistro e superiore dell'area di disegno e il rettangolo arrotondato sono tutti soggetti agli stessi fattori di scala.

> [!IMPORTANT]
> Il piattaforma UWP (Universal Windows Platform) non esegue correttamente il rendering del testo con scalabilità proporzionale.

Con il ridimensionamento anisotropico la larghezza del tratto diventa diversa per le linee allineate con gli assi orizzontali e verticali. Questa operazione è anche evidente dalla prima immagine in questa pagina. Se non si desidera che la larghezza del tratto sia influenzata dai fattori di scala, impostarla su 0 e sarà sempre di un pixel di larghezza indipendentemente dall' `Scale` impostazione.

Il ridimensionamento è relativo all'angolo superiore sinistro dell'area di disegno. Potrebbe trattarsi esattamente di ciò che si desidera, ma potrebbe non essere. Si supponga di voler posizionare il testo e il rettangolo in un altro punto dell'area di disegno e di ridimensionarlo rispetto al relativo centro. In tal caso, è possibile usare la quarta versione del [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) metodo, che include due parametri aggiuntivi per specificare il centro di ridimensionamento:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

I `px` `py` parametri e definiscono un punto che talvolta è denominato *centro di ridimensionamento* , ma nella documentazione di SkiaSharp viene definito punto di *perno*. Si tratta di un punto relativo all'angolo superiore sinistro dell'area di disegno che non è influenzato dal ridimensionamento. Tutti i ridimensionamenti si verificano in relazione al centro.

La pagina [**scala centrata**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) Mostra il funzionamento. Il `PaintSurface` gestore è simile al programma di **scalabilità di base** , ad eccezione del fatto che il `margin` valore viene calcolato in modo da centrare il testo orizzontalmente, il che significa che il programma funziona meglio in modalità verticale:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

L'angolo superiore sinistro del rettangolo arrotondato è posizionato `margin` in pixel a partire dalla parte superiore sinistra dell'area di disegno e `margin` dai pixel. Gli ultimi due argomenti per il `Scale` metodo sono impostati su tali valori più la larghezza e l'altezza del testo, che è anche la larghezza e l'altezza del rettangolo arrotondato. Ciò significa che tutto il ridimensionamento è relativo al centro di tale rettangolo:

[![](scale-images/centeredscale-small.png "Triple screenshot of the Centered Scale page")](scale-images/centeredscale-large.png#lightbox "Triple screenshot of the Centered Scale page")

Gli `Slider` elementi del programma hanno un intervallo compreso tra &ndash; 10 e 10. Come si può notare, i valori negativi della scalabilità verticale, ad esempio nella schermata di Android al centro, fanno sì che gli oggetti scorrano attorno all'asse orizzontale che passa attraverso il centro della scala. I valori negativi della scalabilità orizzontale, ad esempio nella schermata UWP a destra, fanno sì che gli oggetti scorrano attorno all'asse verticale che passa attraverso il centro della scala.

La versione del [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) metodo con punti pivot è un collegamento per una serie di tre `Translate` chiamate e `Scale` . È possibile vedere come funziona sostituendo il `Scale` metodo nella pagina della **scala centrata** con quanto segue:

```csharp
canvas.Translate(-px, -py);
```

Si tratta dei negativi delle coordinate dei punti pivot.

Eseguire nuovamente il programma. Si noterà che il rettangolo e il testo vengono spostati in modo che il centro si trovi nell'angolo superiore sinistro dell'area di disegno. È possibile visualizzarlo a malapena. I dispositivi di scorrimento non funzionano ovviamente perché ora il programma non viene ridimensionato.

A questo punto, aggiungere la chiamata di base `Scale` (senza un centro di ridimensionamento) *prima* della `Translate` chiamata:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Se si ha familiarità con questo esercizio in altri sistemi di programmazione grafica, si potrebbe pensare che si tratta di un problema, ma non lo è. Skia gestisce le chiamate successive alle trasformazioni in modo leggermente diverso rispetto a quanto potrebbe essere familiare.

Con le chiamate successive `Scale` e `Translate` , il centro del rettangolo arrotondato è ancora nell'angolo superiore sinistro, ma è ora possibile ridimensionarlo rispetto all'angolo superiore sinistro dell'area di disegno, che rappresenta anche il centro del rettangolo arrotondato.

A questo punto, prima `Scale` della chiamata, aggiungere un'altra `Translate` chiamata con i valori di centramento:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

In questo modo il risultato ridimensionato viene spostato nuovamente nella posizione originale. Queste tre chiamate sono equivalenti a:

```csharp
canvas.Scale(sx, sy, px, py);
```

Le singole trasformazioni sono composte in modo che la formula di trasformazione totale sia:

 x ' = SX · (x – px) + px

 y ' = sy · (y-py) + py

Tenere presente che i valori predefiniti di `sx` e sono pari a `sy` 1. È facile convincersi che il punto di perno (px, py) non venga trasformato da queste formule. Rimane nello stesso percorso relativo all'area di disegno.

Quando si combinano `Translate` e `Scale` chiamano, l'ordine è importante. Se l'oggetto `Translate` deriva `Scale` da, i fattori di conversione vengono ridimensionati in modo efficace in base ai fattori di scala. Se `Translate` precede `Scale` , i fattori di conversione non vengono ridimensionati. Questo processo diventa leggermente più chiaro (anche se più matematico) quando viene introdotto l'oggetto delle matrici di trasformazione.

La `SKPath` classe definisce una [`Bounds`](xref:SkiaSharp.SKPath.Bounds) proprietà di sola lettura che restituisce un oggetto che `SKRect` definisce l'ambito delle coordinate nel percorso. Ad esempio, quando la `Bounds` proprietà viene ottenuta dal percorso hendecagram creato in precedenza, `Left` le `Top` proprietà e del rettangolo sono approssimativamente-100, le `Right` `Bottom` proprietà e sono approssimativamente 100 e le `Width` proprietà e `Height` sono approssimativamente 200. La maggior parte dei valori effettivi è leggermente inferiore perché i punti delle stelle sono definiti da un cerchio con un raggio di 100, ma solo il punto superiore è parallelo con gli assi orizzontali o verticali.

La disponibilità di queste informazioni implica che dovrebbe essere possibile derivare i fattori di ridimensionamento e di conversione adatti per ridimensionare un percorso alle dimensioni dell'area di disegno. La pagina di [**ridimensionamento anisotropico**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) Mostra questo problema con la stella a 11 punte. Una scala *anisotropico* indica che è diverso nelle direzioni orizzontale e verticale, il che significa che la stella non manterrà le proporzioni originali. Ecco il codice pertinente nel `PaintSurface` gestore:

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

Il `pathBounds` rettangolo viene ottenuto in prossimità della parte superiore del codice, quindi viene usato in un secondo momento con la larghezza e l'altezza dell'area di disegno nella `Scale` chiamata. Tale chiamata verrà ridimensionata in base alle coordinate del tracciato quando viene sottoposto a rendering dalla `DrawPath` chiamata, ma la stella verrà centrata nell'angolo superiore destro dell'area di disegno. Deve essere spostato verso il basso e verso sinistra. Questo è il processo della `Translate` chiamata. Queste due proprietà di `pathBounds` sono approssimativamente-100, quindi i fattori di traduzione sono pari a circa 100. Poiché la `Translate` chiamata è successiva alla `Scale` chiamata, i valori vengono ridimensionati in modo efficace in base ai fattori di scala, in modo da spostare il centro della stella al centro dell'area di disegno:

[![](scale-images/anisotropicscaling-small.png "Triple screenshot of the Anisotropic Scaling page")](scale-images/anisotropicscaling-large.png#lightbox "Triple screenshot of the Anisotropic Scaling page")

Un altro modo per considerare le `Scale` chiamate e `Translate` è determinare l'effetto della sequenza inversa: la `Translate` chiamata sposta il percorso in modo che diventi completamente visibile ma orientato nell'angolo superiore sinistro dell'area di disegno. Il `Scale` metodo rende quindi la stella più grande rispetto all'angolo superiore sinistro.

In realtà, sembra che la stella sia leggermente più grande dell'area di disegno. Il problema è la larghezza del tratto. La `Bounds` proprietà di `SKPath` indica le dimensioni delle coordinate codificate nel percorso ed è ciò che il programma usa per ridimensionarlo. Quando viene eseguito il rendering del percorso con una particolare larghezza del tratto, il percorso di cui è stato eseguito il rendering è maggiore dell'area di disegno.

Per risolvere il problema, è necessario compensarlo. Un approccio semplice in questo programma consiste nell'aggiungere la seguente istruzione immediatamente prima della `Scale` chiamata:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Questo aumenta il `pathBounds` rettangolo di 1,5 unità su tutti e quattro i lati. Si tratta di una soluzione ragionevole solo quando viene arrotondato il join del tratto. Il join di un acaro può essere più lungo ed è difficile da calcolare.

È anche possibile usare una tecnica simile con testo, come illustrato nella pagina di **testo anisotropico** . Di seguito è illustrata la parte pertinente del `PaintSurface` gestore della [`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) classe:

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

Si tratta di una logica simile e il testo si espande fino alla dimensione della pagina in base al rettangolo di delimitazione del testo restituito da `MeasureText` (che è leggermente più grande del testo effettivo):

[![](scale-images/anisotropictext-small.png "Triple screenshot of the Anisotropic Test page")](scale-images/anisotropictext-large.png#lightbox "Triple screenshot of the Anisotropic Test page")

Se è necessario mantenere le proporzioni degli oggetti grafici, è opportuno usare il ridimensionamento del filtro di ridimensionamento. Per la stella a 11 punte viene illustrata la pagina di **ridimensionamento** di un filtro. A livello concettuale, la procedura per la visualizzazione di un oggetto grafico al centro della pagina con il ridimensionamento del filtro dei dati è:

- Traslare il centro dell'oggetto grafico nell'angolo superiore sinistro.
- Ridimensionare l'oggetto in base al valore minimo delle dimensioni orizzontali e verticali della pagina divise per le dimensioni dell'oggetto grafico.
- Trasla il centro dell'oggetto ridimensionato al centro della pagina.

[`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs)Esegue questi passaggi in ordine inverso prima di visualizzare la stella:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

Il codice visualizza anche la stella 10 più volte, ogni volta riducendo il fattore di scala del 10% e cambiando progressivamente il colore da rosso a blu:

[![](scale-images/isotropicscaling-small.png "Triple screenshot of the Isotropic Scaling page")](scale-images/isotropicscaling-large.png#lightbox "Triple screenshot of the Isotropic Scaling page")

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
