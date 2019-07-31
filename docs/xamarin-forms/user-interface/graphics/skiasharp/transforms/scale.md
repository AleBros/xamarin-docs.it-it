---
title: Trasformazione di ridimensionamento
description: Thhis articolo esamina la trasformazione di scala di SkiaSharp per ridimensionare gli oggetti per diverse dimensioni e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 2e9259bed6ad0ae5a926cb75ea74c1f379897220
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649282"
---
# <a name="the-scale-transform"></a>Trasformazione di ridimensionamento

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Individuare la trasformazione di scala di SkiaSharp per ridimensionare gli oggetti per diverse dimensioni_

Come si è visto nel [ **tradurre la trasformazione** ](translate.md) articolo, la trasformazione di traslazione può spostare un oggetto grafico da una posizione a un'altra. Al contrario, la trasformazione di scala modifica la dimensione dell'oggetto grafico:

![](scale-images/scaleexample.png "Una parola altezza ridimensionata di dimensioni")

Trasformazione di ridimensionamento causa spesso grafica coordinate da spostare non appena vengono eseguite più grande.

In precedenza si è visto due formule di trasformazione che descrivono gli effetti dei fattori di traduzione del `dx` e `dy`:

x' = x + dx

y' = y + dy

Fattori di scalabilità `sx` e `sy` sono moltiplicazione invece di addizione:

x' = sx · x

y' = · sy y

I valori predefiniti dei fattori translate sono 0. i valori predefiniti dei fattori di scala sono pari a 1.

Il `SKCanvas` classe definisce quattro `Scale` metodi. Il primo [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single)) metodo è per scomporre nei casi in cui si desidera che la stessa scalabilità orizzontale e verticale:

```csharp
public void Scale (Single s)
```

Questo è noto come *isotropo* ridimensionamento &mdash; ridimensionamento vale a dire la stessa in entrambe le direzioni. Ridimensionamento isotropo mantiene le proporzioni dell'oggetto.

La seconda [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) metodo consente di specificare valori diversi per la scalabilità orizzontale e verticale:

```csharp
public void Scale (Single sx, Single sy)
```

Di conseguenza *anisotropico* ridimensionamento.
La terza [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) metodo combina i due fattori di scala in un singolo `SKPoint` valore:

```csharp
public void Scale (SKPoint size)
```

Il quarto `Scale` metodo verrà illustrato tra breve.

Il **scalabilità di base** illustra come il `Scale` (metodo). Il [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) file contiene due `Slider` gli elementi che consentono di selezionare i fattori di scala orizzontali e verticali compreso tra 0 e 10. Il [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) file code-behind Usa tali valori per chiamare `Scale` prima visualizzazione di un rettangolo arrotondato tracciato con una linea tratteggiata e ridimensionati di testo in alto a sinistra angolo dell'area di disegno:

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

Ci si potrebbe chiedere: In che modo i fattori di scalabilità influiscono sul `MeasureText` valore restituito `SKPaint`dal metodo di? La risposta è: Assolutamente no. `Scale` è un metodo di `SKCanvas`. Non influiscono sulle operazioni eseguite con un `SKPaint` finché non si utilizza tale oggetto per eseguire il rendering di un elemento nell'area di disegno dell'oggetto.

Come può notare, tutti gli elementi disegnata dopo il `Scale` chiamare aumenta in proporzione:

[![](scale-images/basicscale-small.png "Tripla screenshot della pagina di scalabilità di base")](scale-images/basicscale-large.png#lightbox "tripla screenshot della pagina di scalabilità di base")

Il testo, la larghezza della linea tratteggiata, la lunghezza dei trattini in questo senso, l'arrotondamento degli angoli e il margine di 10 pixel tra i bordi superiore e sinistro dell'area di disegno e il rettangolo arrotondato soggetti a tutti i fattori di scalabilità stesso.

> [!IMPORTANT]
> La piattaforma Windows universale non correttamente il rendering del testo anisotropicly ridimensionato.

Filtraggio anisotropo della scalabilità cause lo spessore del tratto diventi diversi per le linee allineata con gli assi orizzontali e verticali. (Questo è anche evidente dall'immagine prima in questa pagina). Se non si desidera lo spessore del tratto che saranno interessati dai fattori di scala, impostarlo su 0 e sarà sempre pari a indipendentemente 1 pixel di `Scale` impostazione.

Il ridimensionamento è rispetto all'angolo superiore sinistro dell'area di disegno. Ciò potrebbe essere esattamente ciò che si desidera, ma potrebbe non essere. Si supponga che si desidera posizionare il testo e il rettangolo in un'altra posizione nell'area di disegno e si desidera che per la scalabilità rispetto al relativo centro. In tal caso è possibile usare la versione del quarta il [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) metodo, che include due parametri aggiuntivi per specificare il centro della scala:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

Il `px` e `py` parametri definiscono un punto che viene talvolta chiamato il *scalabilità center* ma nella finestra di SkiaSharp documentazione è definita una *punto di perno*. Questo è un punto rispetto all'angolo superiore sinistro dell'area di disegno che non è influenzato il ridimensionamento. Tutti il ridimensionamento viene eseguito rispetto al centro di.

Il [ **scalabilità centrato** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) pagina viene illustrato il funzionamento. Il `PaintSurface` gestore di è simile al **scalabilità di base** programmare con la differenza che il `margin` valore viene calcolato per allineare al centro il testo in senso orizzontale, che implica che il programma funziona meglio in modalità verticale:

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

È posizionato l'angolo superiore sinistro del rettangolo arrotondato `margin` pixel dal lato sinistro dell'area di disegno e `margin` pixel dal bordo superiore. Gli ultimi due argomenti per il `Scale` metodo sono impostate su quei valori oltre la larghezza e altezza del testo, che è anche la larghezza e altezza del rettangolo arrotondato. Ciò significa che tutti la scalabilità è rispetto al centro del rettangolo:

[![](scale-images/centeredscale-small.png "Tripla screenshot della pagina di scalabilità centrato")](scale-images/centeredscale-large.png#lightbox "tripla screenshot della pagina di scalabilità al centro")

Il `Slider` elementi in questo programma sono un intervallo di &ndash;10 a 10. Come può notare, i valori negativi di scalabilità (ad esempio nel Android schermata al centro) verticale che gli oggetti capovolgere intorno all'asse orizzontale che passa attraverso il centro della scala. I valori negativi di (ad esempio come illustrato nella schermata UWP a destra) la scalabilità orizzontale che gli oggetti capovolgere intorno all'asse verticale che passa attraverso il centro della scala.

La versione del [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) metodo con i punti di pivot è una scelta rapida per una serie di tre `Translate` e `Scale` chiamate. È possibile visualizzarne il funzionamento, sostituendo il `Scale` metodo nella **centrato scalabilità** pagina con il codice seguente:

```csharp
canvas.Translate(-px, -py);
```

Questi sono negativi delle coordinate del punto pivot.

Eseguire nuovamente il programma. Si noterà che il rettangolo e testo vengono spostate in modo che il centro è nell'angolo superiore sinistro dell'area di disegno. È possibile visualizzarlo poco. I dispositivi di scorrimento non funzionano ovviamente perché ora il programma non supporta la scalabilità affatto.

A questo punto aggiungere basic `Scale` chiamare (senza un centro della scala) *prima* che `Translate` chiamare:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Se si ha familiarità con questo esercizio in altri che sistemi di programmazione della grafica, si potrebbe pensare che non è corretto, ma non. Skia gestisce le chiamate successive trasformazione leggermente in modo diverso da ciò che è possibile avere familiarità con.

Con la successiva `Scale` e `Translate` chiamate, il centro del rettangolo arrotondato è ancora nell'angolo superiore sinistro, ma è possibile scalare ora rispetto all'angolo superiore sinistro dell'area di disegno, che corrisponde anche al centro del rettangolo arrotondato.

A questo punto, che li precedono `Scale` chiamare, aggiungere un altro `Translate` chiamata con valori di centratura:

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

Tenere presente che i valori predefiniti `sx` e `sy` sono pari a 1. È facile per indurre manualmente che il punto di perno (px, py) non viene trasformato da queste formule. Rimane nella stessa posizione rispetto all'area di disegno.

Quando si combinano `Translate` e `Scale` chiamate, l'ordine è importante. Se il `Translate` segue la `Scale`, i fattori di traduzione vengono ridotte in modo efficace i fattori di scala. Se il `Translate` precede il `Scale`, non vengono ridimensionati i fattori di traduzione. Questo processo diventa un po' più chiaro (anche se più matematica) quando è stato introdotto il soggetto di matrici di trasformazione.

Il `SKPath` classe definisce una proprietà di sola lettura [ `Bounds` ](xref:SkiaSharp.SKPath.Bounds) proprietà che restituisce un `SKRect` definire l'ambito delle coordinate di nel percorso. Ad esempio, quando il `Bounds` proprietà viene ottenuta dal percorso hendecagram creato in precedenza, il `Left` e `Top` le proprietà del rettangolo sono circa -100, il `Right` e `Bottom` sono di proprietà circa 100 e il `Width` e `Height` proprietà sono di circa 200. (La maggior parte dei valori effettivi sono un piccolo inferiore perché i punti delle stelle sono definiti da un cerchio con raggio pari a 100, ma solo il punto principale è parallelo con gli assi orizzontali o verticali).

La disponibilità di queste informazioni implica che dovrebbe essere possibile derivare la scala e tradurre fattori adatti per il ridimensionamento di un percorso per le dimensioni dell'area di disegno. Il [ **ridimensionamento anisotropico** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) viene illustrata questa pagina con la stella a 11 punte. Un' *anisotropico* scalabilità significa che è non uguali nelle direzioni orizzontale e verticale, il che significa che la stella non manterrà le proporzioni originali. Ecco il codice pertinente `PaintSurface` gestore:

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

Il `pathBounds` rettangolo viene ottenuto nella parte superiore di questo codice e quindi utilizzato in un secondo momento con la larghezza e altezza dell'area di disegno nel `Scale` chiamare. Che chiamata da se stesso ridurrà le coordinate del percorso quando è sottoposto a rendering per il `DrawPath` verrà centrata chiamata ma la stella nell'angolo superiore destro dell'area di disegno. Deve essere spostato verso il basso e verso sinistra. Questo è il compito del `Translate` chiamare. Queste due proprietà di `pathBounds` sono circa -100, i fattori di traduzione sono circa 100. Poiché il `Translate` viene chiamata dopo il `Scale` chiamare, tali valori vengono ridotte in modo efficace i fattori di scala, in modo che il centro della stella durante lo spostamento al centro dell'area di disegno:

[![](scale-images/anisotropicscaling-small.png "Tripla screenshot della pagina di ridimensionamento anisotropico")](scale-images/anisotropicscaling-large.png#lightbox "tripla screenshot della pagina di filtraggio anisotropo della scalabilità")

Un altro modo per considerare le `Scale` chiamate e `Translate` consiste nel determinare l'effetto nella sequenza inversa: La `Translate` chiamata sposta il percorso in modo che diventi completamente visibile ma orientato nell'angolo superiore sinistro dell'area di disegno. Il `Scale` metodo quindi effettua tale star più grandi rispetto all'angolo superiore sinistro.

In realtà, sembra che il registro star è leggermente maggiore area di disegno. Il problema è lo spessore del tratto. Il `Bounds` proprietà di `SKPath` indica le dimensioni delle coordinate codificato nel percorso e che viene usato il programma per la scalabilità. Quando il percorso viene eseguito il rendering con un valore particolare, il percorso viene eseguito il rendering è maggiore di area di disegno.

Per risolvere il problema è necessario compensare tale. Un approccio semplice in questo programma consiste nell'aggiungere la seguente istruzione subito prima di `Scale` chiamare:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

In questo modo aumenta il `pathBounds` rettangolo da 1,5 unità su tutti e quattro i lati. Questa è una soluzione ragionevole solo quando il join di tratto viene arrotondato. Una giunzione può essere più lunga e difficile da calcolare.

È inoltre possibile utilizzare una tecnica simile con testo, come le **anisotropico testo** illustra come. Ecco la parte interessata il `PaintSurface` gestore dal [ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) classe:

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

Si tratta di una logica simile, e si espande il testo per le dimensioni della pagina basata il rettangolo di limiti di testo restituito da `MeasureText` (che è un po' più grande rispetto al testo effettivo):

[![](scale-images/anisotropictext-small.png "Tripla screenshot della pagina di Test anisotropico")](scale-images/anisotropictext-large.png#lightbox "tripla screenshot della pagina di Test anisotropico")

Se si desidera conservare le proporzioni degli oggetti grafici, è opportuno usare la scalabilità isotropo. Il **ridimensionamento isotropo** viene illustrata questa pagina per la stella a 11 punte. Concettualmente, i passaggi per la visualizzazione di un oggetto grafico al centro della pagina con il ridimensionamento isotropo sono:

- Tradurre il centro dell'oggetto grafico nell'angolo superiore sinistro.
- Ridimensionare l'oggetto basata il valore minimo tra le dimensioni di pagina verticali e orizzontali diviso per le dimensioni di oggetti grafici.
- Tradurre il centro dell'oggetto ridimensionato al centro della pagina.

Il [ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) queste fasi vengono eseguite in ordine inverso prima di visualizzare la stella:

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

[![](scale-images/isotropicscaling-small.png "Tripla screenshot della pagina di ridimensionamento isotropo")](scale-images/isotropicscaling-large.png#lightbox "tripla screenshot della pagina di ridimensionamento isotropo")


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
