---
title: La trasformazione di scala
description: Individuare la trasformazione di scala SkiaSharp per ridimensionare gli oggetti per diverse dimensioni
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: feecfc923903a20332bf3a1a188ab9d7cd2ce1c0
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="the-scale-transform"></a>La trasformazione di scala

_Individuare la trasformazione di scala SkiaSharp per ridimensionare gli oggetti per diverse dimensioni_

Come si è visto [tradurre la trasformazione](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md) articolo, la trasformazione di traslazione possibile spostare un oggetto grafico da una posizione a altra. Al contrario, la trasformazione di scala modifica la dimensione dell'oggetto grafico:

![](scale-images/scaleexample.png "Una parola verticale in scala di dimensioni")

La trasformazione di scala comporta spesso coordinate grafica da spostare non appena vengono eseguite più grande.

In precedenza si è visto due formule di trasformazione che descrivono gli effetti dei fattori di traduzione di `dx` e `dy`:

x' = x + dx

y' = y + dy

Fattori di scala `sx` e `sy` sono moltiplicazione anziché additivo:

x' = sx · x

y' = sy · y

I valori predefiniti dei fattori di conversione sono 0; i valori predefiniti dei fattori di scala sono 1.

Il `SKCanvas` classe definisce quattro `Scale` metodi. Il primo [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/) metodo è per i casi in cui si desidera che la stessa ridimensionamento orizzontale e verticale fattori:

```csharp
public void Scale (Single s)
```

Questo è noto come *isotropico* scalabilità & #x 2014; la scala che è la stessa in entrambe le direzioni. Scalabilità isotropico mantiene le proporzioni dell'oggetto.

Il secondo [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/) metodo consente di specificare valori diversi per la scalabilità orizzontale e verticale:

```csharp
public void Scale (Single sx, Single sy)
```

Di conseguenza, *anisotropico* scalabilità.
Il terzo [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/SkiaSharp.SKPoint/) metodo consente di combinare i due fattori di scala in un singolo `SKPoint` valore:

```csharp
public void Scale (SKPoint size)
```

Il quarto `Scale` metodo verrà descritta a breve.

Il **base scala** è illustrato il `Scale` metodo. Il [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) file XAML contiene due `Slider` gli elementi che consentono di selezionare i fattori di scala orizzontali e verticali tra 0 e 10. Il [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) file code-behind utilizza tali valori per chiamare `Scale` prima visualizzazione di un rettangolo arrotondato tracciato con una linea tratteggiata e per adattarlo del testo in alto a sinistra angolo dell'area di disegno:

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

Ci si potrebbe chiedere: come i fattori di scala influiscono sul valore restituito dal `MeasureText` metodo `SKPaint`? La risposta è: non è affatto. `Scale` è un metodo di `SKCanvas`. Non si applica operazioni eseguite con un `SKPaint` finché non si utilizza tale oggetto per eseguire il rendering di un elemento nell'area di disegno dell'oggetto.

Come si può notare, tutti gli elementi creato dopo il `Scale` chiamare aumenta proporzionalmente:

[![](scale-images/basicscale-small.png "Schermata di triplo della pagina base scala")](scale-images/basicscale-large.png#lightbox "tripla schermata della pagina per la scalabilità di base")

Il testo, la larghezza della linea tratteggiata, la lunghezza dei trattini in tale riga, l'arrotondamento degli angoli e il margine di 10 pixel tra i bordi superiore e sinistro dell'area di disegno e il rettangolo arrotondato sottoposte tutti i fattori di scala stesso.

> [!IMPORTANT]
> La piattaforma UWP non correttamente il rendering del testo anisotropicly scalato.

Filtraggio anisotropo della scalabilità cause lo spessore del tratto diventi diversi per le righe allineata con gli assi orizzontali e verticali. (Questo è evidente dall'immagine prima in questa pagina.) Se non si desidera lo spessore del tratto interessa i fattori di scala, impostarla su 0 e sarà sempre pari a 1 pixel indipendentemente dal valore di `Scale` impostazione.

La scalabilità è rispetto all'angolo superiore sinistro dell'area di disegno. Potrebbe trattarsi di esattamente ciò che si desidera, ma potrebbe non essere. Si supponga che si desidera posizionare il testo e il rettangolo in un'altra posizione nell'area di disegno e si desidera applicarvi la scalabilità rispetto al centro. In questo caso è possibile utilizzare la versione del quarto di [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/System.Single/System.Single/) metodo, che include due parametri aggiuntivi per specificare il centro della scala:

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

Il `px` e `py` parametri definiscono un punto che talvolta viene denominato il *scalabilità center* ma nel SkiaSharp documentazione viene considerata un *punto pivot*. Questo è un punto rispetto all'angolo superiore sinistro dell'area di disegno che non è interessata della scalabilità. Ridimensionamento di tutti, si verifica rispetto al centro di.

Il [ **centrata scala** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) pagina viene illustrato il funzionamento. Il `PaintSurface` è simile al gestore la **base scala** programma con la differenza che il `margin` valore viene calcolato per allineare al centro il testo orizzontalmente, che implica che il programma funziona meglio in modalità verticale:

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

È posizionato nell'angolo superiore sinistro del rettangolo arrotondato `margin` pixel dal bordo sinistro dell'area di disegno e `margin` pixel dal bordo superiore. Gli ultimi due argomenti per il `Scale` metodo impostati per tali valori oltre la larghezza e altezza del testo, che è anche la larghezza e altezza del rettangolo arrotondato. Ciò significa che tutte è rispetto al centro del rettangolo:

[![](scale-images/centeredscale-small.png "Schermata triplo della pagina centrata scala")](scale-images/centeredscale-large.png#lightbox "tripla schermata della pagina scala centrata")

Il `Slider` elementi in questo programma dispongono di un intervallo di & #x 2013; 10 a 10. Come si può notare, i valori negativi (ad esempio in di Android schermata al centro) la scalabilità verticale provocare capovolgere intorno all'asse orizzontale che passa attraverso il centro della scala degli oggetti. I valori negativi di (ad esempio la schermata di Windows a destra) la scalabilità orizzontale che gli oggetti da capovolgere intorno all'asse verticale che passa attraverso il centro della scala.

Questa versione di quarto di `Scale` metodo è effettivamente un collegamento. Si potrebbe voler vedere il funzionamento sostituendo il `Scale` metodo in questo codice con quanto segue:

```csharp
canvas.Translate(-px, -py);
```

Questi sono i segni di sottrazione delle coordinate del punto pivot.

Eseguire nuovamente il programma. Si noterà che il testo e il rettangolo vengono spostati in modo che il centro è nell'angolo superiore sinistro dell'area di disegno. È possibile visualizzarlo lo spazio disponibile. I dispositivi di scorrimento non funzionano naturalmente poiché ora il programma non è facilmente scalabile affatto.

A questo punto aggiungere gli elementi di base `Scale` chiamata (senza un centro della scala) *prima* che `Translate` chiamare:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Se si ha familiarità con questo esercizio in altri che sistemi di programmazione di grafica, si potrebbe pensare che non è corretto, ma non è. Skia gestisce le chiamate successive trasformazione leggermente in modo diverso da ciò che potrebbe acquisire familiarità con.

Con la successiva `Scale` e `Translate` chiamate, il centro del rettangolo arrotondato è ancora nell'angolo superiore sinistro, ma è possibile scalare ora rispetto all'angolo superiore sinistro dell'area di disegno, è anche il centro del rettangolo arrotondato.

Ora, prima che `Scale` chiamata aggiungere un altro `Translate` chiamare con i valori di distribuzione:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Il risultato in scala viene torna alla posizione originale. Tali tre chiamate sono equivalenti a:

```csharp
canvas.Scale(sx, sy, px, py);
```

Le trasformazioni singoli sono composti in modo che la formula di trasformazione totale è:

 x' = sx · (x – px) + px

 y' = sy · (y: py) + rispetto all'anno precedente

Tenere presente che i valori predefiniti di `sx` e `sy` sono pari a 1. È facile per indurre manualmente che il punto pivot (px, py) non viene trasformato da queste formule. Rimane nello stesso percorso relativo dell'area di disegno.

Quando si combinano `Translate` e `Scale` chiamate, l'ordine è importante. Se il `Translate` segue il `Scale`, i fattori di conversione vengono ridimensionati in modo efficace per i fattori di scala. Se il `Translate` precede il `Scale`, non vengono ridimensionati i fattori di conversione. Questo processo diventa leggermente più chiaro (sebbene più matematiche) quando è stato introdotto il soggetto di matrici di trasformazione.

Il `SKPath` classe definisce una proprietà di sola lettura [ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/) proprietà che restituisce un `SKRect` definire l'estensione delle coordinate di nel percorso. Ad esempio, quando il `Bounds` proprietà viene ottenuta dal percorso hendecagram creato in precedenza, il `Left` e `Top` le proprietà del rettangolo sono circa -100, il `Right` e `Bottom` sono di proprietà circa 100 e `Width` e `Height` proprietà sono di circa 200. (La maggior parte dei valori effettivi sono un piccolo minore poiché i punti delle stelle vengono definiti da un cerchio con un raggio pari a 100, ma solo il punto principale è parallelo con gli assi orizzontali o verticali).

La disponibilità di queste informazioni implica che deve essere possibile derivare scala e tradurre fattori adatti per la scalabilità in un percorso per le dimensioni dell'area di disegno. Il [ **scalabilità anisotropico** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) viene illustrata questa pagina con la stella a cui punta 11. Un *anisotropico* scala significa che è diverso nelle direzioni orizzontale e verticale, il che significa che la stella non mantiene le proporzioni originali. Ecco il codice pertinente `PaintSurface` gestore:

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

Il `pathBounds` rettangolo viene ottenuto nella parte superiore di questo codice e quindi utilizzato in un secondo momento con la larghezza e l'altezza dell'area di disegno nel `Scale` chiamare. Che chiamata autonomamente ridimensionerà le coordinate del percorso quando è sottoposto a rendering per il `DrawPath` verrà centrata chiamata ma stella nell'angolo superiore destro dell'area di disegno. Deve essere spostato verso il basso e a sinistra. Si tratta del processo della `Translate` chiamare. Queste due proprietà di `pathBounds` sono circa -100, pertanto i fattori di conversione sono circa 100. Poiché il `Translate` viene chiamata dopo il `Scale` chiama, tali valori vengono ridimensionati in modo efficace per i fattori di scala, in modo spostare il centro della stella il centro dell'area di disegno:

[![](scale-images/anisotropicscaling-small.png "Schermata triplo della pagina scalabilità anisotropico")](scale-images/anisotropicscaling-large.png#lightbox "tripla schermata della pagina scalabilità anisotropico")

Un altro modo, si pensi di `Scale` e `Translate` chiamate consiste nel determinare l'effetto in sequenza inversa: il `Translate` chiamata passa il percorso in modo che diventi completamente visibile ma orientato nell'angolo superiore sinistro dell'area di disegno. Il `Scale` metodo quindi effettua tale stella maggiore rispetto all'angolo superiore sinistro.

In realtà, risulta che stella è leggermente maggiore area di disegno. Il problema è lo spessore del tratto. Il `Bounds` proprietà `SKPath` indica le dimensioni delle coordinate di codifica nel percorso e che viene usato il programma per ridimensionarlo. Quando il percorso viene eseguito il rendering con un valore particolare, il percorso viene eseguito il rendering è maggiore dell'area di disegno.

Per risolvere il problema è necessario che compensare. Un approccio semplice in questo programma consiste nell'aggiungere il diritto di istruzione seguenti prima di `Scale` chiamare:

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

In questo modo aumenta il `pathBounds` rettangolo da 1,5 unità su tutti e quattro i lati. Si tratta di una soluzione ragionevole solo quando il join di traccia viene arrotondato. Una giunzione può essere più lungo e difficile da calcolare.

È inoltre possibile utilizzare una tecnica simile con testo, come il **testo anisotropico** illustra come. Di seguito è la parte rilevante del `PaintSurface` gestore il [ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) classe:

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

Si tratta di una logica simile, e il testo viene espanso alle dimensioni della pagina di base del rettangolo di limiti di testo restituiti da `MeasureText` (che è leggermente maggiore il testo effettivo):

[![](scale-images/anisotropictext-small.png "Schermata triplo della pagina di prova anisotropico")](scale-images/anisotropictext-large.png#lightbox "tripla schermata della pagina di prova anisotropico")

Se si desidera mantenere le proporzioni degli oggetti grafici, sarà possibile utilizzare il ridimensionamento isotropico. Il **scalabilità isotropico** viene illustrata questa pagina per la stella a cui punta 11. Concettualmente, i passaggi per la visualizzazione di un oggetto grafico al centro della pagina con scalabilità isotropico sono:

- Tradurre il centro dell'oggetto grafico nell'angolo superiore sinistro.
- Ridimensionare l'oggetto in base al valore minimo delle dimensioni di pagina orizzontali e verticali diviso per le dimensioni dell'oggetto grafico.
- Tradurre il centro dell'oggetto scalato al centro della pagina.

Il [ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) queste fasi vengono eseguite in ordine inverso prima di visualizzare la stella:

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

Il codice visualizza anche la stella dieci volte, ogni volta diminuendo il ridimensionamento fattori 10% e progressivamente modificando il colore da rosso a blu:

[![](scale-images/isotropicscaling-small.png "Schermata triplo della pagina scalabilità isotropico")](scale-images/isotropicscaling-large.png#lightbox "tripla schermata della pagina scalabilità isotropico")


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
