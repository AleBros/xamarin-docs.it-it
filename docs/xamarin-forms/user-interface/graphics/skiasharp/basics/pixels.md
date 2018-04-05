---
title: Pixel e unità indipendenti dal dispositivo
description: Esplorare le differenze tra coordinate SkiaSharp e xamarin. Forms
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: e01d3e228a0684865fb09dd7a4cbb0e2f0b49125
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2018
---
# <a name="pixels-and-device-independent-units"></a>Pixel e unità indipendenti dal dispositivo

_Esplorare le differenze tra coordinate SkiaSharp e xamarin. Forms_

In questo articolo vengono esaminate le differenze nel sistema di coordinate utilizzato in SkiaSharp e xamarin. Forms. È possibile ottenere informazioni per eseguire la conversione tra i due sistemi di coordinate e anche disegnare la grafica di riempimento di una determinata area:

![](pixels-images/screenfillexample.png "Un'ellissi che riempie la schermata")

Se è stata programmazione in xamarin. Forms per un periodo di tempo, potrebbe essere un'idea di coordinate di xamarin. Forms e dimensioni. I cerchi disegnati nei due articoli precedenti potrebbero sembrare poco small all'utente.

Tali cerchi *sono* piccoli rispetto al numero di dimensioni di xamarin. Forms. Per impostazione predefinita, mentre le coordinate e le dimensioni si basa xamarin. Forms in un'unità indipendenti dal dispositivo stabilita dalla piattaforma sottostante SkiaSharp disegna in unità di pixel. (Ulteriori informazioni sul sistema di coordinate di xamarin. Forms sono reperibile [capitolo 5. Gestione delle dimensioni](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) del libro *la creazione di App per dispositivi mobili con xamarin. Forms*.)

La pagina nel [ **SkewSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programma intitolata **area dimensioni** utilizza SkiaSharp output di testo per visualizzare le dimensioni dell'area di visualizzazione da tre origini diverse:

- Il normale xamarin. Forms [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) e [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) le proprietà del `SKCanvasView` oggetto.
- Il [ `CanvasSize` ](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKCanvasView.CanvasSize/) proprietà del `SKCanvasView` oggetto.
- Il [ `Size` ](https://developer.xamarin.com/api/property/SkiaSharp.SKImageInfo.Size/) proprietà del `SKImageInfo` valore, che è coerente con il `Width` e `Height` proprietà utilizzate nei due pagine precedenti.

Il [ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) classe viene illustrato come visualizzare questi valori. Salva il costruttore di `SKCanvasView` oggetto come un campo, pertanto è possibile accedervi nel `PaintSurface` gestore eventi:

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

`SKCanvas` include sei diversi `DrawText` metodi, ma questo [ `DrawText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawText/p/System.String/System.Single/System.Single/SkiaSharp.SKPaint/) è il più semplice:

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Si specifica la stringa di testo, le coordinate X e Y in cui ha inizio, il testo e un `SKPaint` oggetto. La coordinata X specifica in cui è posizionato il lato sinistro del testo, ma le espressioni di controllo: la coordinata Y specifica la posizione del *baseline* del testo. Se hai mai scritto manualmente in un documento, la linea di base è la riga in cui sit di caratteri e sotto discendono quali tratti discendenti (ad esempio quelle di lettere g, p, q e y).

Il `SKPaint` oggetto consente di specificare il colore del testo, la famiglia di caratteri e le dimensioni del testo. Per impostazione predefinita, il [ `TextSize` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.TextSize/) proprietà ha un valore pari a 12, determinando in testo molto piccolo su dispositivi ad alta risoluzione, ad esempio telefoni. In non può essere il più semplice applicazioni, è necessario anche alcune informazioni sulle dimensioni del testo che viene visualizzato. Il `SKPaint` classe definisce un [ `FontMetrics` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.FontMetrics/) proprietà e diverse [ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/) metodi, ma per esigenze meno elaborate, il [ `FontSpacing` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.FontSpacing/) proprietà fornisce un valore consigliato per le righe successive di spaziatura del testo.

Nell'esempio `PaintSurface` gestore crea un `SKPaint` dell'oggetto per un `TextSize` di 40 pixel, che è l'altezza desiderata verticale del testo in cima i tratti ascendenti verso il basso di tratti discendenti. Il `FontSpacing` valore di `SKPaint` oggetto restituito è un po' più grandi rispetto a quello sul 47 pixel.

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

Il metodo inizia la prima riga di testo con una coordinata X di 20 (per un piccolo margine a sinistra) e una coordinata Y del `fontSpacing`, che è un po' più di quanto è necessario per visualizzare l'altezza della prima riga di testo nella parte superiore dell'area di visualizzazione. Dopo ogni chiamata a `DrawText`, la coordinata Y viene incrementata di uno o due incrementi di `fontSpacing`.

Di seguito è riportato il programma in esecuzione in tutti e tre le piattaforme:

[![](pixels-images/surfacesize-small.png "Schermata triplo della pagina area dimensioni")](pixels-images/surfacesize-large.png#lightbox "schermata triplo della dimensione di area")

Come si può notare, la `CanvasSize` proprietà del `SKCanvasView` e `Size` proprietà del `SKImageInfo` valore siano consistenti nei report le dimensioni in pixel. Il `Height` e `Width` le proprietà del `SKCanvasView` proprietà xamarin. Forms e segnalare le dimensioni della visualizzazione nell'unità indipendenti dal dispositivo definite dalla piattaforma.

Il simulatore di iOS 7 a sinistra con 2 pixel per unità indipendenti dal dispositivo, Android 5 di Nexus al centro è di 3 pixel per ogni unità, e il 925 Lumia Nokia a destra con 2,25 pixel per unità. Che del motivo per cui il semplice circle precedente ha un aspetto illustrato sulla stessa dimensione su iPhone e Windows phone, ma è più piccolo sul telefono Android.

Se si preferisce utilizzare interamente in unità indipendenti dal dispositivo, è possibile farlo impostando la `IgnorePixelScaling` proprietà del `SKCanvasView` a `true`. Tuttavia, potrebbe non i risultati sono soddisfacenti. SkiaSharp esegue il rendering della grafica in un'area di dispositivi più piccola, con una dimensione in pixel, pari alla dimensione della visualizzazione in unità indipendenti dal dispositivo. (Ad esempio, SkiaSharp utilizzerà una superficie di visualizzazione di 360 x 512 pixel sul 5 Nexus.) Quindi si adatta quell'immagine dimensioni, risultante in contorni frastagliati bitmap evidenti.

Per mantenere la stessa risoluzione dell'immagine, una soluzione migliore consiste nello scrivere funzioni personalizzate semplice per la conversione tra i due sistemi di coordinate.

Oltre al `DrawCircle` metodo `SKCanvas` definisce anche due `DrawOval` metodi che consentono di disegnare un'ellisse. Un'ellisse è definita da due raggi anziché un singolo radius. Tali parametri sono noti come il *radius principali* e *raggio minore*. Il `DrawOval` metodo disegna un'ellisse con i due raggi parallela gli assi X e Y. Con le trasformazioni o l'utilizzo di un tracciato di grafica (per essere analizzate in un secondo momento), è possibile superare tale restrizione ma [questo `DrawOval` metodo](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawOval/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/) l'argomento due raggi nomi `rx` e `ry` per indicare che sono paralleli per gli assi X e Y:

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

È possibile disegnare un'ellisse che riempie l'area di visualizzazione? Il **ellisse riempimento** è illustrato come. Il `PaintSurface` gestore dell'evento nel [ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) classe sottrae metà della larghezza del tratto dal `xRadius` e `yRadius` i valori per adattare l'intero ellisse e il relativo struttura all'interno dell'area di visualizzazione:

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

Qui è in esecuzione su tre piattaforme:

[![](pixels-images/ellipsefill-small.png "Schermata triplo della pagina area dimensioni")](pixels-images/ellipsefill-large.png#lightbox "schermata triplo della dimensione di area")

Il [altri `DrawOval` metodo](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawOval/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/) ha un [ `SGRect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRect/) argomento, costituito da un rettangolo definito in termini di coordinate X e Y del relativo angolo superiore sinistro e l'angolo inferiore destro. Ovale riempie il rettangolo, che suggerisce che è possibile utilizzarla nel **riempimento ellisse** pagina simile al seguente:

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

Tuttavia, che tronca tutti i bordi del contorno dell'ellisse su quattro lati. È necessario modificare tutti i `SKRect` gli argomenti del costruttore in base al `strokeWidth` destro a tale scopo:

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
