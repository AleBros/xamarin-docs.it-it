---
title: Pixel e unità indipendenti dal dispositivo
description: Questo articolo vengono esaminate le differenze tra coordinate SkiaSharp e xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: 9da46f128eeb502e0f40e5861f3d04c66491565b
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "39615301"
---
# <a name="pixels-and-device-independent-units"></a>Pixel e unità indipendenti dal dispositivo

_Esplorare le differenze tra coordinate SkiaSharp e xamarin. Forms_

Questo articolo esamina le differenze nel sistema di coordinate utilizzato in SkiaSharp e xamarin. Forms. È possibile ottenere informazioni per eseguire la conversione tra i due sistemi di coordinate e inoltre disegnare immagini che è riempire un'area specifica:

![](pixels-images/screenfillexample.png "Un forma ovale che riempie la schermata")

Se è stata programmatore in xamarin. Forms per un periodo di tempo, potrebbe essere un'idea per dimensioni e le coordinate di xamarin. Forms. I cerchi disegnati in due articoli precedenti potrebbero sembrare un po' piccoli all'utente.

Tali cerchi *sono* piccole rispetto alle dimensioni di xamarin. Forms. Per impostazione predefinita, mentre le coordinate e le dimensioni si basa xamarin. Forms in un'unità indipendenti dal dispositivo stabilita dalla piattaforma sottostante SkiaSharp disegna in unità di pixel. (Altre informazioni sul sistema di coordinate di xamarin. Forms sono reperibile [capitolo 5. Gestione delle dimensioni](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) del libro *creazione di App per dispositivi mobili con xamarin. Forms*.)

La pagina nel [ **SkewSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programma intitolata **nell'area dimensioni** Usa SkiaSharp output di testo per visualizzare le dimensioni dell'area di visualizzazione da tre origini diverse:

- Xamarin. Forms normali [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) e [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) le proprietà del `SKCanvasView` oggetto.
- Il [ `CanvasSize` ](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize) proprietà del `SKCanvasView` oggetto.
- Il [ `Size` ](xref:SkiaSharp.SKImageInfo.Size) proprietà della `SKImageInfo` valore, che è coerente con il `Width` e `Height` proprietà utilizzate nei due pagine precedenti.

Il [ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) classe illustra come visualizzare questi valori. Il costruttore salva la `SKCanvasView` oggetto come un campo, in modo che sia accessibile nel `PaintSurface` gestore dell'evento:

```csharp
SKCanvasView canvasView;

public SurfaceSizePage()
{
    Title = "Surface Size";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvas` include sei diversi `DrawText` metodi, ma ciò [ `DrawText` ](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) metodo è la più semplice:

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Si specifica la stringa di testo, le coordinate X e Y in cui il testo verrà avviato e un `SKPaint` oggetto. Specifica la coordinata X in cui è posizionato il lato sinistro del testo, ma le espressioni di controllo: la coordinata Y specifica la posizione del *baseline* del testo. Se hai mai scritto a mano su carta sottolineato, linea di base è la riga in cui sit di caratteri, e versioni precedenti discendono quali tratti discendenti (ad esempio quelli nelle lettere g, p, q e y).

Il `SKPaint` oggetto consente di specificare il colore del testo, la famiglia di caratteri e le dimensioni del testo. Per impostazione predefinita, il [ `TextSize` ](xref:SkiaSharp.SKPaint.TextSize) proprietà ha un valore pari a 12, con conseguente testo piccoli dispositivi ad alta risoluzione, come gli Smartphone. In qualsiasi valore tranne le applicazioni più semplice, è necessario inoltre alcune informazioni sulle dimensioni del testo che viene visualizzato. Il `SKPaint` classe definisce un [ `FontMetrics` ](xref:SkiaSharp.SKPaint.FontMetrics) proprietà e alcuni [ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String)) metodi, ma per le esigenze meno elaborate, il [ `FontSpacing` ](xref:SkiaSharp.SKPaint.FontSpacing) proprietà fornisce un valore consigliato per le righe successive di spaziatura del testo.

Quanto segue `PaintSurface` gestore crea un' `SKPaint` dell'oggetto per un `TextSize` di 40 pixel, ovvero l'altezza desiderata verticale del testo nella parte superiore dei tratti ascendenti nella parte inferiore di tratti discendenti. Il `FontSpacing` valore che la `SKPaint` oggetto restituisce è un po' più grande di quello, sui 47 pixel.

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 40
    };

    float fontSpacing = paint.FontSpacing;
    float x = 20;               // left margin
    float y = fontSpacing;      // first baseline
    float indent = 100;

    canvas.DrawText("SKCanvasView Height and Width:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(String.Format("{0:F2} x {1:F2}",
                                  canvasView.Width, canvasView.Height),
                    x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKCanvasView CanvasSize:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(canvasView.CanvasSize.ToString(), x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKImageInfo Size:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(info.Size.ToString(), x + indent, y, paint);
}
```

Il metodo inizia la prima riga del testo con una coordinata X di 20 (per un piccolo margine a sinistra) e una coordinata Y di `fontSpacing`, che è un po' più di ciò che è necessario visualizzare all'altezza della prima riga del testo nella parte superiore dell'area di visualizzazione. Dopo ogni chiamata a `DrawText`, la coordinata Y è aumentata di uno o due incrementi di `fontSpacing`.

Ecco il programma in esecuzione in tutti e tre le piattaforme:

[![](pixels-images/surfacesize-small.png "Tripla screenshot della pagina di dimensioni di superficie")](pixels-images/surfacesize-large.png#lightbox "tripla screenshot della pagina di dimensioni di Surface")

Come può notare, il `CanvasSize` proprietà del `SKCanvasView` e il `Size` proprietà del `SKImageInfo` valore siano consistenti nei report le dimensioni in pixel. Il `Height` e `Width` delle proprietà del `SKCanvasView` le proprietà di xamarin. Forms e segnalare le dimensioni della visualizzazione nelle unità indipendenti dal dispositivo definite dalla piattaforma.

Simulatore iOS sette a sinistra ha due pixel per ogni unità indipendenti dal dispositivo e di Android Nexus 5 al centro ha tre pixel per ogni unità. Ecco perché il cerchio semplice mostrato in precedenza dispone di diverse dimensioni su piattaforme diverse.

Se si preferisce usare interamente in unità indipendenti dal dispositivo, è possibile farlo impostando il `IgnorePixelScaling` proprietà del `SKCanvasView` a `true`. Tuttavia, potrebbe non i risultati sono soddisfacenti. SkiaSharp esegue il rendering di grafica in un'area dispositivo più piccola, con una dimensione in pixel, pari alla dimensione della visualizzazione in unità indipendenti dal dispositivo. (Ad esempio, SkiaSharp utilizzerà una superficie di visualizzazione di 360 x 512 pixel su Nexus 5.) Ridimensiona quindi tale immagine nella dimensione contorni frastagliati bitmap evidenti.

Per mantenere la stessa risoluzione dell'immagine, una soluzione migliore consiste nello scrivere funzioni semplici personalizzate per la conversione tra i due sistemi di coordinate.

Oltre al `DrawCircle` metodo `SKCanvas` definisce inoltre due `DrawOval` metodi che consentono di disegnare un'ellisse. Un'ellisse è definita da due raggi anziché un singolo radius. Questi sono conosciuti come le *radius principali* e il *radius secondaria*. Il `DrawOval` metodo disegna un'ellisse con i due raggi parallele gli assi X e Y. (Se è necessario disegnare un'ellisse con gli assi non paralleli gli assi X e Y, è possibile usare una trasformazione di rotazione, come descritto nell'articolo [ **l'omonima trasformazione** ](../transforms/rotate.md) o un percorso della grafica come descritto di articolo [ **tre modi per disegnare un arco**](../curves/arcs.md)). Questo overload del metodo di [ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) i parametri due raggi i nomi dei metodi `rx` e `ry` per indicare che sono paralleli gli assi X e Y:

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

È possibile disegnare un'ellisse che riempie l'area di visualizzazione? Il **ellisse è riempire** pagina viene illustrato come. Il `PaintSurface` gestore dell'evento nel [ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) classe sottrae metà lo spessore del tratto dal `xRadius` e `yRadius` valori in base alle intere puntini di sospensione e la relativa all'interno dell'area di visualizzazione di struttura:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float strokeWidth = 50;
    float xRadius = (info.Width - strokeWidth) / 2;
    float yRadius = (info.Height - strokeWidth) / 2;

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = strokeWidth
    };
    canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
}
```

Qui è in esecuzione sulle tre piattaforme:

[![](pixels-images/ellipsefill-small.png "Tripla screenshot della pagina di dimensioni di superficie")](pixels-images/ellipsefill-large.png#lightbox "tripla screenshot della pagina di dimensioni di Surface")

L'altra [ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) metodo dispone di un [ `SKRect` ](xref:SkiaSharp.SKRect) argomento, che è un rettangolo definito in termini di coordinate X e Y del relativo angolo superiore sinistro e l'angolo inferiore destro. La forma ovale riempie il rettangolo, che suggerisce che è possibile usarlo nel **ellisse è riempire** pagina simile alla seguente:

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

Tuttavia, che tronca tutti i bordi del contorno dell'ellisse sui quattro lati. È necessario modificare tutti i `SKRect` argomenti del costruttore base il `strokeWidth` per ottenere questo risultato a destra:

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
