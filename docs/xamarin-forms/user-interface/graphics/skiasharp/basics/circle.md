---
title: ''
description: Questo articolo illustra le nozioni di base del disegno SkiaSharp, incluse le Canvas e gli oggetti Paint, nelle Xamarin.Forms applicazioni e ne illustra il codice di esempio.
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fb873102bfb8568b8298a39ea2429fb6c27af175
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137722"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>Disegno di un cerchio semplice in SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Informazioni sulle nozioni di base del disegno SkiaSharp, incluse le Canvas e gli oggetti Paint_

Questo articolo presenta i concetti relativi al disegno di elementi grafici nell' Xamarin.Forms uso di SkiaSharp, tra cui la creazione `SKCanvasView` di un oggetto per ospitare la grafica, la gestione dell' `PaintSurface` evento e l'uso `SKPaint` di un oggetto per specificare il colore e altri attributi di disegno.

Il programma [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contiene tutto il codice di esempio per questa serie di articoli SkiaSharp. La prima pagina è denominata **semplice Circle** e richiama la classe Page [`SimpleCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) . Questo codice Mostra come creare un cerchio al centro della pagina con un raggio di 100 pixel. Il contorno del cerchio è rosso e l'interno del cerchio è blu.

![](circle-images/circleexample.png "A blue circle outlined in red")

La [`SimpleCircle`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) classe Page deriva da `ContentPage` e contiene due `using` direttive per gli spazi dei nomi SkiaSharp:

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

Il costruttore seguente della classe crea un [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) oggetto, connette un gestore per l' [`PaintSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.PaintSurface) evento e imposta l' `SKCanvasView` oggetto come contenuto della pagina:

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvasView`Occupa l'intera area di contenuto della pagina. In alternativa, è possibile combinare un `SKCanvasView` con altri Xamarin.Forms `View` derivati, come si vedrà in altri esempi.

Il `PaintSurface` gestore eventi è il punto in cui si esegue tutto il disegno. Questo metodo può essere chiamato più volte mentre il programma è in esecuzione, quindi deve mantenere tutte le informazioni necessarie per ricreare la visualizzazione grafica:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

L' [`SKPaintSurfaceEventArgs`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs) oggetto che accompagna l'evento ha due proprietà:

- [`Info`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info)di tipo[`SKImageInfo`](xref:SkiaSharp.SKImageInfo)
- [`Surface`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface)di tipo[`SKSurface`](xref:SkiaSharp.SKSurface)

La `SKImageInfo` struttura contiene informazioni sulla superficie di disegno, soprattutto la larghezza e l'altezza in pixel. L' `SKSurface` oggetto rappresenta la superficie di disegno stessa. In questo programma, la superficie di disegno è uno schermo video, ma in altri programmi un `SKSurface` oggetto può anche rappresentare una bitmap in cui si usa SkiaSharp per il disegno.

La proprietà più importante di `SKSurface` è [`Canvas`](xref:SkiaSharp.SKSurface.Canvas) di tipo [`SKCanvas`](xref:SkiaSharp.SKCanvas) . Questa classe è un contesto di disegno grafico utilizzato per eseguire il disegno effettivo. L' `SKCanvas` oggetto incapsula uno stato di grafica, che include le trasformazioni e il ritaglio della grafica.

Ecco un inizio tipico di un `PaintSurface` gestore eventi:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();
    ...
}

```

Il [`Clear`](xref:SkiaSharp.SKCanvas.Clear) metodo cancella l'area di disegno con un colore trasparente. Un overload consente di specificare un colore di sfondo per l'area di disegno.

L'obiettivo è quello di creare un cerchio rosso riempito con blu. Poiché questa particolare immagine grafica contiene due colori diversi, il processo deve essere eseguito in due passaggi. Il primo passaggio consiste nel disegnare il contorno del cerchio. Per specificare il colore e altre caratteristiche della linea, creare e inizializzare un [`SKPaint`](xref:SkiaSharp.SKPaint) oggetto:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 25
    };
    ...
}
```

La [`Style`](xref:SkiaSharp.SKPaint.Style) proprietà indica che si desidera *tracciare* una riga (in questo caso il contorno del cerchio) invece di *riempire* la parte interna. I tre membri dell' [`SKPaintStyle`](xref:SkiaSharp.SKPaintStyle) enumerazione sono i seguenti:

- [`Fill`](xref:SkiaSharp.SKPaintStyle.Fill)
- [`Stroke`](xref:SkiaSharp.SKPaintStyle.Stroke)
- [`StrokeAndFill`](xref:SkiaSharp.SKPaintStyle.StrokeAndFill)

Il valore predefinito è `Fill`. Usare la terza opzione per tracciare la linea e riempire l'interno con lo stesso colore.

Impostare la [`Color`](xref:SkiaSharp.SKPaint.Color) proprietà su un valore di tipo [`SKColor`](xref:SkiaSharp.SKColor) . Un modo per ottenere un `SKColor` valore consiste nel convertire un Xamarin.Forms `Color` valore in un `SKColor` valore usando il metodo di estensione [`ToSKColor`](xref:SkiaSharp.Views.Forms.Extensions.ToSKColor*) . La [`Extensions`](xref:SkiaSharp.Views.Forms.Extensions) classe nello `SkiaSharp.Views.Forms` spazio dei nomi include altri metodi che convertono tra Xamarin.Forms i valori e i valori SkiaSharp.

La [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) proprietà indica lo spessore della linea. Qui è impostato su 25 pixel.

Usare `SKPaint` l'oggetto per creare il cerchio:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

Le coordinate vengono specificate in relazione all'angolo superiore sinistro della superficie di visualizzazione. Le coordinate X aumentano a destra e le coordinate Y aumentano verso il basso. In discussione sui grafici, spesso la notazione matematica (x, y) viene usata per indicare un punto. Il punto (0, 0) è l'angolo superiore sinistro della superficie di visualizzazione e spesso viene chiamato *origine*.

I primi due argomenti di `DrawCircle` indicano le coordinate X e Y del centro del cerchio. Sono assegnati a metà della larghezza e dell'altezza della superficie di visualizzazione per posizionare il centro del cerchio al centro della superficie di visualizzazione. Il terzo argomento specifica il raggio del cerchio e l'ultimo argomento è l' `SKPaint` oggetto.

Per riempire l'area interna del cerchio, è possibile modificare due proprietà dell' `SKPaint` oggetto e chiamare di `DrawCircle` nuovo. Questo codice mostra anche un modo alternativo per ottenere un `SKColor` valore da uno dei diversi campi della [`SKColors`](xref:SkiaSharp.SKColors) struttura:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```

Questa volta, la `DrawCircle` chiamata riempie il cerchio usando le nuove proprietà dell' `SKPaint` oggetto.

Ecco il programma in esecuzione in iOS e Android:

[![](circle-images/simplecircle-small.png "Triple screenshot of the Simple Circle page")](circle-images/simplecircle-large.png#lightbox "Triple screenshot of the Simple Circle page")

Quando si esegue il programma manualmente, è possibile trasformare il telefono o il simulatore lateralmente per vedere come viene ridisegnato il grafico. Ogni volta che è necessario ricreare il grafico, il `PaintSurface` gestore eventi viene chiamato nuovamente.

È anche possibile colorare oggetti grafici con sfumature o riquadri bitmap. Queste opzioni sono descritte nella sezione relativa agli [**shader SkiaSharp**](../effects/shaders/index.md).

Un `SKPaint` oggetto è leggermente più di una raccolta di proprietà di disegno grafiche. Questi oggetti sono semplici. È possibile riutilizzare `SKPaint` gli oggetti come questo programma oppure è possibile creare più `SKPaint` oggetti per diverse combinazioni di proprietà del disegno. È possibile creare e inizializzare questi oggetti all'esterno del `PaintSurface` gestore eventi ed è possibile salvarli come campi nella classe della pagina.

> [!NOTE]
> La `SKPaint` classe definisce un oggetto [`IsAntialias`](xref:SkiaSharp.SKPaint.IsAntialias) per abilitare l'anti-aliasing nel rendering della grafica. L'anti-aliasing genera in genere bordi visivamente più smussati, pertanto è probabile che si desideri impostare questa proprietà su `true` nella maggior parte degli `SKPaint` oggetti. Per motivi di semplicità, questa proprietà _non_ è impostata nella maggior parte delle pagine di esempio.

Anche se la larghezza del contorno del cerchio viene specificata come 25 pixel &mdash; o un trimestre del raggio del cerchio, sembra &mdash; essere più sottile ed esiste un motivo valido: metà della lunghezza della linea è nascosta dal cerchio blu. Gli argomenti del `DrawCircle` Metodo definiscono le coordinate geometriche astratte di un cerchio. L'interno blu viene ridimensionato in tale dimensione al pixel più vicino, ma il contorno di 25 pixel a cavalcioni tra la metà geometrica all' &mdash; interno e la metà all'esterno.

L'esempio successivo nell'articolo relativo all' [integrazione Xamarin.Forms con](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) illustra questo aspetto visivo.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
