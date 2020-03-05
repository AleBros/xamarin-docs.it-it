---
title: Trasformazione di ridimensionamento
description: Thhis articolo esamina la trasformazione di scala di SkiaSharp per ridimensionare gli oggetti per diverse dimensioni e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 7dc7a2faabef86aa63b52739edda696efcb54aba
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292748"
---
# <a name="the-scale-transform"></a>Trasformazione di ridimensionamento

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Individuare la trasformazione scalabilità SkiaSharp per ridimensionare gli oggetti a diverse dimensioni_

Come illustrato nell'articolo [**tradurre la trasformazione**](translate.md) , la trasformazione translate può spostare un oggetto grafico da una posizione a un'altra. Al contrario, la trasformazione di scala modifica la dimensione dell'oggetto grafico:

![](scale-images/scaleexample.png "A tall word scaled in size")

Trasformazione di ridimensionamento causa spesso grafica coordinate da spostare non appena vengono eseguite più grande.

In precedenza sono state riportate due formule di trasformazione che descrivono gli effetti dei fattori di traduzione di `dx` e `dy`:

x' = x + dx

y' = y + dy

I fattori di scala di `sx` e `sy` sono moltiplicativi anziché additivi:

x' = sx · x

y' = · sy y

I valori predefiniti dei fattori translate sono 0. i valori predefiniti dei fattori di scala sono pari a 1.

La classe `SKCanvas` definisce quattro metodi di `Scale`. Il primo metodo di [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single)) è per i casi in cui si desidera lo stesso fattore di scala orizzontale e verticale:

```csharp
public void Scale (Single s)
```

Questa operazione è nota come ridimensionamento del formato di *filtro* dei &mdash; ridimensionamento in entrambe le direzioni. Ridimensionamento isotropo mantiene le proporzioni dell'oggetto.

Il secondo metodo di [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) consente di specificare valori diversi per il ridimensionamento orizzontale e verticale:

```csharp
public void Scale (Single sx, Single sy)
```

In questo modo si ottiene la scalabilità *anisotropico* .
Il terzo metodo [`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) combina i due fattori di scalabilità in un singolo valore `SKPoint`:

```csharp
public void Scale (SKPoint size)
```

Il quarto `Scale` metodo verrà descritto a breve.

La pagina **scala di base** illustra il metodo `Scale`. Il file [**BasicScalePage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) contiene due elementi `Slider` che consentono di selezionare i fattori di scala orizzontale e verticale compresi tra 0 e 10. Il file code-behind [**BasicScalePage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) usa questi valori per chiamare `Scale` prima di visualizzare un rettangolo arrotondato con una linea tratteggiata e dimensionato per adattarlo a un testo nell'angolo superiore sinistro dell'area di disegno:

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

Ci si potrebbe chiedere: in che modo i fattori di scala influiscono sul valore restituito dal metodo `MeasureText` di `SKPaint`? La risposta è: non è affatto. `Scale` è un metodo di `SKCanvas`. Non influisce sulle operazioni eseguite con un oggetto `SKPaint` fino a quando non si usa tale oggetto per eseguire il rendering di un elemento nell'area di disegno.

Come si può notare, tutto ciò che viene creato dopo la chiamata di `Scale` aumenta proporzionalmente:

[![](scale-images/basicscale-small.png "Triple screenshot of the Basic Scale page")](scale-images/basicscale-large.png#lightbox "Triple screenshot of the Basic Scale page")

Il testo, la larghezza della linea tratteggiata, la lunghezza dei trattini in questo senso, l'arrotondamento degli angoli e il margine di 10 pixel tra i bordi superiore e sinistro dell'area di disegno e il rettangolo arrotondato soggetti a tutti i fattori di scalabilità stesso.

> [!IMPORTANT]
> La piattaforma Windows universale non correttamente il rendering del testo anisotropicly ridimensionato.

Filtraggio anisotropo della scalabilità cause lo spessore del tratto diventi diversi per le linee allineata con gli assi orizzontali e verticali. Questa operazione è anche evidente dalla prima immagine in questa pagina. Se non si desidera che la larghezza del tratto sia influenzata dai fattori di scala, impostarla su 0 e sarà sempre di un pixel di larghezza indipendentemente dall'impostazione del `Scale`.

Il ridimensionamento è rispetto all'angolo superiore sinistro dell'area di disegno. Ciò potrebbe essere esattamente ciò che si desidera, ma potrebbe non essere. Si supponga che si desidera posizionare il testo e il rettangolo in un'altra posizione nell'area di disegno e si desidera che per la scalabilità rispetto al relativo centro. In tal caso, è possibile usare la quarta versione del metodo [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) , che include due parametri aggiuntivi per specificare il centro di ridimensionamento:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

I parametri `px` e `py` definiscono un punto che talvolta è denominato *centro di ridimensionamento* , ma nella documentazione di SkiaSharp viene definito *punto di perno*. Questo è un punto rispetto all'angolo superiore sinistro dell'area di disegno che non è influenzato il ridimensionamento. Tutti il ridimensionamento viene eseguito rispetto al centro di.

La pagina [**scala centrata**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) Mostra il funzionamento. Il gestore `PaintSurface` è simile al programma di **scalabilità di base** , ad eccezione del fatto che il valore `margin` viene calcolato in modo da centrare il testo orizzontalmente, il che significa che il programma funziona meglio in modalità verticale:

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

L'angolo superiore sinistro del rettangolo arrotondato è posizionato `margin` pixel a partire dal lato sinistro dell'area di disegno e `margin` pixel dalla parte superiore. Gli ultimi due argomenti per il metodo `Scale` sono impostati su tali valori più la larghezza e l'altezza del testo, che è anche la larghezza e l'altezza del rettangolo arrotondato. Ciò significa che tutti la scalabilità è rispetto al centro del rettangolo:

[![](scale-images/centeredscale-small.png "Triple screenshot of the Centered Scale page")](scale-images/centeredscale-large.png#lightbox "Triple screenshot of the Centered Scale page")

Gli elementi `Slider` in questo programma hanno un intervallo di &ndash;da 10 a 10. Come può notare, i valori negativi di scalabilità (ad esempio nel Android schermata al centro) verticale che gli oggetti capovolgere intorno all'asse orizzontale che passa attraverso il centro della scala. I valori negativi di (ad esempio come illustrato nella schermata UWP a destra) la scalabilità orizzontale che gli oggetti capovolgere intorno all'asse verticale che passa attraverso il centro della scala.

La versione del metodo [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) con punti pivot è un collegamento per una serie di tre chiamate `Translate` e `Scale`. È possibile vedere come funziona sostituendo il metodo `Scale` nella pagina della **scala centrata** con quanto segue:

```csharp
canvas.Translate(-px, -py);
```

Questi sono negativi delle coordinate del punto pivot.

Eseguire nuovamente il programma. Si noterà che il rettangolo e testo vengono spostate in modo che il centro è nell'angolo superiore sinistro dell'area di disegno. È possibile visualizzarlo poco. I dispositivi di scorrimento non funzionano ovviamente perché ora il programma non supporta la scalabilità affatto.

A questo punto, aggiungere la chiamata di base `Scale` (senza un centro di ridimensionamento) *prima* che `Translate` chiamare:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Se si ha familiarità con questo esercizio in altri che sistemi di programmazione della grafica, si potrebbe pensare che non è corretto, ma non. Skia gestisce le chiamate successive trasformazione leggermente in modo diverso da ciò che è possibile avere familiarità con.

Con le chiamate successive `Scale` e `Translate`, il centro del rettangolo arrotondato è ancora nell'angolo superiore sinistro, ma ora è possibile ridimensionarlo rispetto all'angolo superiore sinistro dell'area di disegno, che rappresenta anche il centro del rettangolo arrotondato.

Ora, prima che `Scale` chiami, aggiungere un'altra chiamata `Translate` con i valori di centramento:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Il risultato in scala si sposta nuovamente nella posizione originale. Questi tre chiamate sono equivalenti a:

```csharp
canvas.Scale(sx, sy, px, py);
```

Le trasformazioni singole si accumulano in modo che la formula di trasformazione totale è:

 x' = sx · (x – px) + px

 y' = · sy (y: py) + py

Tenere presente che i valori predefiniti di `sx` e `sy` sono pari a 1. È facile per indurre manualmente che il punto di perno (px, py) non viene trasformato da queste formule. Rimane nella stessa posizione rispetto all'area di disegno.

Quando si combinano `Translate` e `Scale` chiamate, l'ordine è importante. Se il `Translate` viene eseguito dopo l'`Scale`, i fattori di traduzione vengono effettivamente ridimensionati in base ai fattori di scala. Se il `Translate` precede l'`Scale`, i fattori di conversione non vengono ridimensionati. Questo processo diventa un po' più chiaro (anche se più matematica) quando è stato introdotto il soggetto di matrici di trasformazione.

La classe `SKPath` definisce una proprietà di [`Bounds`](xref:SkiaSharp.SKPath.Bounds) di sola lettura che restituisce un `SKRect` che definisce l'ambito delle coordinate nel percorso. Ad esempio, quando la proprietà `Bounds` viene ottenuta dal percorso hendecagram creato in precedenza, le proprietà `Left` e `Top` del rettangolo sono approssimativamente-100, le proprietà `Right` e `Bottom` sono pari a circa 100 e le proprietà `Width` e `Height` sono approssimativamente 200. (La maggior parte dei valori effettivi sono un piccolo inferiore perché i punti delle stelle sono definiti da un cerchio con raggio pari a 100, ma solo il punto principale è parallelo con gli assi orizzontali o verticali).

La disponibilità di queste informazioni implica che dovrebbe essere possibile derivare la scala e tradurre fattori adatti per il ridimensionamento di un percorso per le dimensioni dell'area di disegno. La pagina di [**ridimensionamento anisotropico**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) Mostra questo problema con la stella a 11 punte. Una scala *anisotropico* indica che è diverso nelle direzioni orizzontale e verticale, il che significa che la stella non manterrà le proporzioni originali. Ecco il codice pertinente nel gestore `PaintSurface`:

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

Il rettangolo `pathBounds` viene ottenuto nella parte superiore del codice, quindi viene usato in un secondo momento con la larghezza e l'altezza dell'area di disegno nella chiamata `Scale`. Tale chiamata verrà ridimensionata in base alle coordinate del tracciato quando viene sottoposto a rendering dalla chiamata `DrawPath` ma la stella verrà centrata nell'angolo superiore destro dell'area di disegno. Deve essere spostato verso il basso e verso sinistra. Questo è il processo della chiamata `Translate`. Queste due proprietà di `pathBounds` sono approssimativamente-100, quindi i fattori di traduzione sono pari a circa 100. Poiché la chiamata di `Translate` si trova dopo la chiamata `Scale`, tali valori vengono ridimensionati in modo efficace in base ai fattori di scalabilità, quindi spostano il centro della stella al centro dell'area di disegno:

[![](scale-images/anisotropicscaling-small.png "Triple screenshot of the Anisotropic Scaling page")](scale-images/anisotropicscaling-large.png#lightbox "Triple screenshot of the Anisotropic Scaling page")

Un altro modo per considerare la `Scale` e `Translate` le chiamate consiste nel determinare l'effetto della sequenza inversa: la chiamata `Translate` sposta il percorso in modo che diventi completamente visibile ma orientato nell'angolo superiore sinistro dell'area di disegno. Il metodo `Scale` rende quindi tale stella più grande rispetto all'angolo superiore sinistro.

In realtà, sembra che il registro star è leggermente maggiore area di disegno. Il problema è lo spessore del tratto. La proprietà `Bounds` di `SKPath` indica le dimensioni delle coordinate codificate nel percorso ed è ciò che il programma usa per la scalabilità. Quando il percorso viene eseguito il rendering con un valore particolare, il percorso viene eseguito il rendering è maggiore di area di disegno.

Per risolvere il problema è necessario compensare tale. Un approccio semplice in questo programma consiste nell'aggiungere l'istruzione seguente subito prima della chiamata a `Scale`:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Questo aumenta il rettangolo di `pathBounds` di 1,5 unità su tutti e quattro i lati. Questa è una soluzione ragionevole solo quando il join di tratto viene arrotondato. Una giunzione può essere più lunga e difficile da calcolare.

È anche possibile usare una tecnica simile con testo, come illustrato nella pagina di **testo anisotropico** . Di seguito è illustrata la parte pertinente del gestore `PaintSurface` dalla classe [`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) :

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

Si tratta di una logica simile e il testo si espande fino alla dimensione della pagina in base al rettangolo dei limiti di testo restituito da `MeasureText` (che è leggermente più grande del testo effettivo):

[![](scale-images/anisotropictext-small.png "Triple screenshot of the Anisotropic Test page")](scale-images/anisotropictext-large.png#lightbox "Triple screenshot of the Anisotropic Test page")

Se si desidera conservare le proporzioni degli oggetti grafici, è opportuno usare la scalabilità isotropo. Per la stella a 11 punte viene illustrata la pagina di **ridimensionamento** di un filtro. Concettualmente, i passaggi per la visualizzazione di un oggetto grafico al centro della pagina con il ridimensionamento isotropo sono:

- Tradurre il centro dell'oggetto grafico nell'angolo superiore sinistro.
- Ridimensionare l'oggetto basata il valore minimo tra le dimensioni di pagina verticali e orizzontali diviso per le dimensioni di oggetti grafici.
- Tradurre il centro dell'oggetto ridimensionato al centro della pagina.

Il [`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) esegue questi passaggi in ordine inverso prima di visualizzare la stella:

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

Il codice visualizza anche le stelle 10 volte, ogni volta che diminuendo il ridimensionamento fattore del 10% e progressivamente modificando il colore da rosso a blu:

[![](scale-images/isotropicscaling-small.png "Triple screenshot of the Isotropic Scaling page")](scale-images/isotropicscaling-large.png#lightbox "Triple screenshot of the Isotropic Scaling page")

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
