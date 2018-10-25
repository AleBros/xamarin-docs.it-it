---
title: Disegnare un cerchio semplice in SkiaSharp
description: Questo articolo illustra le nozioni di base di disegno di SkiaSharp, tra cui Canvas e oggetti disegno, nelle applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 32fa126d1990839a98ce03cdbfa245a2df97671d
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "39615223"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>Disegnare un cerchio semplice in SkiaSharp

_Apprendere le nozioni di base di disegno di SkiaSharp, tra cui Canvas e disegnare gli oggetti_

Questo articolo vengono illustrati i concetti di creazione grafica in xamarin. Forms con SkiaSharp, compresa la creazione di un' `SKCanvasView` oggetto per ospitare la grafica, la gestione di `PaintSurface` evento e l'utilizzo un `SKPaint` oggetto per specificare colori e altri disegno attributi.

Il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programma contiene tutto il codice di esempio per questa serie di articoli di SkiaSharp. La prima pagina è autorizzata a utilizzare **cerchio semplice** e richiama la classe delle pagine [ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs). Questo codice illustra come disegnare un cerchio al centro della pagina con un raggio pari a 100 pixel. Il contorno del cerchio è rosso e l'interno del cerchio è blu.

![](circle-images/circleexample.png "Un cerchio blu evidenziato in rosso")

Il [ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) deriva dalla classe di pagina `ContentPage` e contiene due `using` le direttive per gli spazi dei nomi di SkiaSharp:

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

Crea il seguente costruttore della classe un' [ `SKCanvasView` ](xref:SkiaSharp.Views.Forms.SKCanvasView) oggetto, collega un gestore per il [ `PaintSurface` ](xref:SkiaSharp.Views.Forms.SKCanvasView.PaintSurface) evento e imposta il `SKCanvasView` oggetto come il contenuto della pagina:

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

Il `SKCanvasView` occupa l'intera area del contenuto della pagina. In alternativa, è possibile combinare un `SKCanvasView` con altri xamarin. Forms `View` derivati, come si vedrà in altri esempi.

Il `PaintSurface` gestore dell'evento è in cui si svolgono tutti il disegno. Questo metodo può essere chiamato più volte durante l'esecuzione del programma, pertanto è necessario gestire tutte le informazioni necessarie per ricreare il grafico visualizzato:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

Il [ `SKPaintSurfaceEventArgs` ](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs) che accompagna l'evento ha due proprietà:

- [`Info`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info) di tipo [`SKImageInfo`](xref:SkiaSharp.SKImageInfo)
- [`Surface`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface) di tipo [`SKSurface`](xref:SkiaSharp.SKSurface)

Il `SKImageInfo` struttura contiene informazioni sulla superficie di disegno, in particolare, la larghezza e altezza in pixel. Il `SKSurface` oggetto rappresenta l'area di disegno. In questo programma, l'area di disegno è uno schermo video, ma in altri programmi un `SKSurface` oggetto può anche rappresentare una bitmap che si usano SkiaSharp su cui per disegnare.

La proprietà più importante della `SKSurface` viene [ `Canvas` ](xref:SkiaSharp.SKSurface.Canvas) di tipo [ `SKCanvas` ](xref:SkiaSharp.SKCanvas). Questa classe è una disegno contesto utilizzato per eseguire il disegno effettivo delle immagini. Il `SKCanvas` uno stato di grafica, che include le trasformazioni di grafica e il ridimensionamento incapsulato dall'oggetto.

Ecco un tipico inizio di un `PaintSurface` gestore dell'evento:

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

Il [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear) metodo cancella l'area di disegno con un colore trasparente. Un overload consente di specificare un colore di sfondo per l'area di disegno.

L'obiettivo qui è per disegnare un cerchio rosso contenente blu. Poiché questa particolare immagine grafica contiene due colori diversi, il processo deve essere eseguita in due passaggi. Il primo passaggio è per disegnare il contorno del cerchio. Per specificare il colore e altre caratteristiche della linea, creare e inizializzare un' [ `SKPaint` ](xref:SkiaSharp.SKPaint) oggetto:

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

Il [ `Style` ](xref:SkiaSharp.SKPaint.Style) proprietà indica che si desidera *stroke* una riga, in questo caso il contorno del cerchio, anziché *riempimento* l'interno. I tre membri del [ `SKPaintStyle` ](xref:SkiaSharp.SKPaintStyle) enumerazione sono i seguenti:

- [`Fill`](xref:SkiaSharp.SKPaintStyle.Fill)
- [`Stroke`](xref:SkiaSharp.SKPaintStyle.Stroke)
- [`StrokeAndFill`](xref:SkiaSharp.SKPaintStyle.StrokeAndFill)

Il valore predefinito è `Fill`. Utilizzare la terza opzione per disegnare la linea e riempire l'area interna con lo stesso colore.

Impostare il [ `Color` ](xref:SkiaSharp.SKPaint.Color) la proprietà su un valore di tipo [ `SKColor` ](xref:SkiaSharp.SKColor). Un modo per ottenere un `SKColor` valore è convertendo un xamarin. Forms `Color` valore a un `SKColor` valore usando il metodo di estensione [ `ToSKColor` ](SkiaSharp.Views.Forms.Extensions.ToSKColor*). Il [ `Extensions` ](xref:SkiaSharp.Views.Forms.Extensions) classe la `SkiaSharp.Views.Forms` dello spazio dei nomi include altri metodi per la conversione tra valori di xamarin. Forms e SkiaSharp.

Il [ `StrokeWidth` ](xref:SkiaSharp.SKPaint.StrokeWidth) proprietà indica lo spessore della linea. In questo caso è impostato su 25 pixel.

Che è utilizzare `SKPaint` oggetto su cui disegnare il controllo circle:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

Coordinate vengono specificate in relazione all'angolo superiore sinistro dell'area di visualizzazione. X coordina l'aumento a destra e aumento delle coordinate Y verso la parte inferiore. Nella descrizione informazioni sugli elementi grafici, la notazione matematica (x, y) viene spesso utilizzata per indicare un punto. Il punto (0, 0) è l'angolo superiore sinistro dell'area di visualizzazione e viene spesso definito di *origin*.

I primi due argomenti di `DrawCircle` indicano le coordinate X e Y del centro del cerchio. Queste entità vengono assegnate a metà larghezza e altezza dell'area di visualizzazione per inserire il centro del cerchio al centro dell'area di visualizzazione. Il terzo argomento specifica il raggio del cerchio, e l'ultimo argomento è il `SKPaint` oggetto.

Per riempire l'interno del cerchio, è possibile modificare due proprietà del `SKPaint` oggetto e chiamare `DrawCircle` nuovamente. Questo codice mostra anche un modo alternativo per ottenere un `SKColor` valore da uno dei molti campi del [ `SKColors` ](xref:SkiaSharp.SKColors) struttura:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```
Questa volta, il `DrawCircle` chiamata riempie il cerchio usando le proprietà di nuovo il `SKPaint` oggetto.

Ecco il programma in esecuzione in iOS, Android e la piattaforma Windows universale:

[![](circle-images/simplecircle-small.png "Tripla screenshot della pagina del cerchio semplice")](circle-images/simplecircle-large.png#lightbox "tripla screenshot della pagina del cerchio semplice")

Quando si esegue il programma, è possibile attivare il telefono o il simulatore lateralmente per visualizzare come l'elemento grafico viene ridisegnato. Ogni volta che l'immagine deve essere ridisegnata, il `PaintSurface` gestore eventi viene chiamato nuovamente.

È anche possibile colorare gli oggetti grafici con sfumature o le tessere mappa di bit. Queste opzioni sono descritte nella sezione sul [ **shader SkiaSharp**](../effects/shaders/index.md).

Un `SKPaint` oggetto è poco più di una raccolta di proprietà di disegno delle immagini. Questi oggetti sono leggeri. È possibile riutilizzare `SKPaint` oggetti come questo programma di, oppure è possibile creare più `SKPaint` gli oggetti per diverse combinazioni di proprietà di disegno. È possibile creare e inizializzare questi oggetti di fuori del `PaintSurface` un gestore eventi ed è possibile salvarli come campi nella classe di pagina.

> [!NOTE]
> Il `SKPaint` classe definisce un [ `IsAntialias` ](xref:SkiaSharp.SKPaint.IsAntialias) per abilitare l'anti-aliasing per il rendering degli elementi grafici. Anti-aliasing comporta in genere archi visivamente più uniforme, pertanto è opportuno impostare questa proprietà su `true` nella maggior parte dei `SKPaint` oggetti. Per motivi di semplicità, questa proprietà è _non_ impostare nella maggior parte delle pagine di esempio.

Anche se la larghezza del contorno del cerchio è specificata come 25 pixel &mdash; o un quarto del raggio del cerchio &mdash; risulta essere più sottili e vi è un buon motivo per cui: metà della larghezza della riga è nascosto dai cerchio blu. Gli argomenti per il `DrawCircle` metodo definirà le coordinate geometriche astratte di un cerchio. L'interno blu viene ridimensionato su tale dimensione per il pixel più vicino, ma la struttura di larghezza di 25 pixel attraversa il cerchio geometrico &mdash; metà su interno e il metà all'esterno.

Nell'esempio successivo nel [l'integrazione con xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) articolo viene illustrata questa visivamente.


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
