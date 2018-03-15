---
title: Disegnare un cerchio semplice
description: Nozioni di base del disegno SkiaSharp, tra cui aree e paint
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 10f741e853603ef22cd45004a6c726ae579f3675
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2018
---
# <a name="drawing-a-simple-circle"></a>Disegnare un cerchio semplice

_Nozioni di base del disegno SkiaSharp, tra cui aree e paint_

In questo articolo vengono illustrati i concetti di creazione di elementi grafici in xamarin. Forms usando SkiaSharp, inclusa la creazione di un `SKCanvasView` oggetto per ospitare la grafica, la gestione di `PaintSurface` evento e utilizzando un `SKPaint` oggetto per specificare il colore e l'altro disegno attributi.

Il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) programma contiene tutto il codice di esempio per questa serie di articoli SkiaSharp. La prima pagina è autorizzata a utilizzare **cerchio semplice** e richiama la classe page [ `SimpleCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs). Questo codice viene illustrato come disegnare un cerchio al centro della pagina con un raggio di 100 pixel. La struttura del cerchio rossa, e l'interno del cerchio blu.

![](circle-images/circleexample.png "Un cerchio blu evidenziato in rosso")

Il [ `SimpleCirle` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) pagina classe deriva da `ContentPage` e contiene due `using` direttive per gli spazi dei nomi SkiaSharp:

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

Il costruttore della classe seguente crea un [ `SKCanvasView` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKCanvasView/) oggetto, che associa un gestore per il [ `PaintSurface` ](https://developer.xamarin.com/api/event/SkiaSharp.Views.Forms.SKCanvasView.PaintSurface/) evento e imposta il `SKCanvasView` oggetto come il contenuto della pagina:

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

Il `SKCanvasView` occupa l'intera area del contenuto della pagina. In alternativa, è possibile combinare un `SKCanvasView` con altri xamarin. Forms `View` derivati, come si noterà negli altri esempi.

Il `PaintSurface` gestore eventi è in cui si tutti il disegno. Questo metodo viene in genere chiamato più volte durante l'esecuzione del programma, pertanto è necessario mantenere tutte le informazioni necessarie per ricreare il grafico visualizzato:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

Il [ `SKPaintSurfaceEventArgs` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs/) oggetto che accompagna l'evento ha due proprietà:

- [`Info`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info/) di tipo [`SKImageInfo`](https://developer.xamarin.com/api/type/SkiaSharp.SKImageInfo/)
- [`Surface`](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface/) di tipo [`SKSurface`](https://developer.xamarin.com/api/type/SkiaSharp.SKSurface/)

Il `SKImageInfo` struttura contiene informazioni sulla superficie di disegno, in particolare, è la larghezza e altezza in pixel. Il `SKSurface` oggetto rappresenta la superficie di disegno. In questo programma, l'area di disegno è un video, ma in altri programmi un `SKSurface` oggetto può anche rappresentare una bitmap che si utilizzano SkiaSharp sul quale per disegnare.

La proprietà più importante di `SKSurface` è [ `Canvas` ](https://developer.xamarin.com/api/property/SkiaSharp.SKSurface.Canvas/) di tipo [ `SKCanvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/). Questa classe è un contesto che consente di eseguire il disegno effettivo grafiche. Il `SKCanvas` uno stato di grafica, che include le trasformazioni di grafica e il ritaglio incapsulata dall'oggetto.

Ecco un avvio tipico di un `PaintSurface` gestore eventi:

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

Il [ `Clear` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Clear()/) metodo cancella l'area di disegno con un colore trasparente. Un overload consente di specificare un colore di sfondo per l'area di disegno.

L'obiettivo è per disegnare un cerchio rosso contenente blu. Poiché questo particolare immagine contiene due colori diversi, il processo deve essere eseguita in due passaggi. Il primo passaggio consiste nella disegnare la struttura del cerchio. Per specificare il colore e altre caratteristiche della linea, è necessario creare e inizializzare un [ `SKPaint` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaint/) oggetto:

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

Il [ `Style` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Style/) proprietà indica che si desidera *tratto* una riga (in questo caso, la struttura del cerchio) anziché *riempimento* l'interno. I tre membri del [ `SKPaintStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPaintStyle/) enumerazione sono i seguenti:

- [`Fill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Fill/)
- [`Stroke`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.Stroke/)
- [`StrokeAndFill`](https://developer.xamarin.com/api/field/SkiaSharp.SKPaintStyle.StrokeAndFill/)

Il valore predefinito è `Fill`. Utilizzare la terza opzione per tracciare la riga e riempire l'area interna con lo stesso colore.

Impostare il [ `Color` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.Color/) su un valore di tipo [ `SKColor` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColor/). Un modo per ottenere un `SKColor` è convertendo un xamarin. Forms `Color` valore un `SKColor` valore utilizzando il metodo di estensione [ `ToSKColor` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.Extensions.ToSKColor/p/Xamarin.Forms.Color/). Il [ `Extensions` ](https://developer.xamarin.com/api/type/SkiaSharp.Views.Forms.Extensions/) classe il `SkiaSharp.Views.Forms` spazio dei nomi include altri metodi di conversione tra i valori di xamarin. Forms e SkiaSharp.

Il [ `StrokeWidth` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeWidth/) proprietà indica lo spessore della linea. Di seguito viene impostato su 25 pixel.

Utilizzare che `SKPaint` oggetto su cui disegnare il cerchio:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

Vengono specificate le coordinate rispetto all'angolo superiore sinistro dell'area di visualizzazione. Le coordinate X aumento a destra e aumento delle coordinate Y risulti non disponibile. Nella discussione sulla grafica, la notazione matematica (x, y) viene spesso utilizzata per indicare un punto. Il punto (0, 0) è l'angolo superiore sinistro dell'area di visualizzazione e spesso viene chiamato il *origine*.

I primi due argomenti di `DrawCircle` indicano le coordinate X e Y del centro del cerchio. Questi sono assegnati a metà larghezza e altezza dell'area di visualizzazione per inserire il centro del cerchio al centro dell'area di visualizzazione. Il terzo argomento consente di specificare il raggio del cerchio, e l'ultimo argomento è il `SKPaint` oggetto.

Per riempire l'area interna di cerchio, è possibile modificare due proprietà del `SKPaint` oggetto e chiamare `DrawCircle` nuovamente. Questo codice viene inoltre illustrato un modo alternativo per ottenere un `SKColor` valore da uno dei molti campi del [ `SKColors` ](https://developer.xamarin.com/api/type/SkiaSharp.SKColors/) struttura:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```
Questa volta il `DrawCircle` chiamata riempie il cerchio utilizzando le proprietà di nuovo il `SKPaint` oggetto.

Di seguito è riportato il programma in esecuzione su iOS, Android e la piattaforma Windows universale:

[![](circle-images/simplecircle-small.png "Schermata di triplo della pagina cerchio semplice")](circle-images/simplecircle-large.png#lightbox "tripla schermata della pagina cerchio semplice")

Quando si esegue il programma, è possibile attivare il telefono o il simulatore lateralmente per vedere come l'immagine viene ridisegnato. Ogni volta che l'immagine deve essere ridisegnato il `PaintSurface` gestore eventi viene chiamato nuovamente.

Un `SKPaint` oggetto è poco più di una raccolta di proprietà di disegno delle immagini. Questi oggetti sono molto semplici. È possibile riutilizzare `SKPaint` oggetti come di questo programma, oppure è possibile creare più `SKPaint` gli oggetti per diverse combinazioni di proprietà di disegno. È possibile creare e inizializzare questi oggetti di fuori del `PaintSurface` un gestore eventi ed è possibile salvarli come campi nella classe della pagina.

Anche se la larghezza del contorno del cerchio è specificata come 25 pixel &mdash; o un quarto del raggio del cerchio &mdash; risulta essere sottili ed è un buon motivo per cui: metà della larghezza della riga è nascosto dal cerchio blu. Gli argomenti per il `DrawCircle` il metodo per definire le coordinate geometriche astratte di un cerchio. L'area interna di blu viene ridimensionato per tale dimensione per il pixel più vicino, ma la struttura del livello di pixel 25 attraversata dalla cerchio geometrico &mdash; metà all'interno e metà all'esterno.

Nell'esempio successivo nel [l'integrazione con xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md) articolo viene illustrata questa visivamente.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
