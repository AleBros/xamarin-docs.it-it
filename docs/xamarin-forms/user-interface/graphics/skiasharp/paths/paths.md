---
title: Nozioni di base di percorso in SkiaSharp
description: Questo articolo esamina l'oggetto di SkiaSharp SKPath per la combinazione di linee e curve collegate e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: eee338461593ad131f679d32cadf63fe3b1a4c40
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759338"
---
# <a name="path-basics-in-skiasharp"></a>Nozioni di base di percorso in SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Esplorare l'oggetto di SkiaSharp SKPath per la combinazione di linee e curve collegate_

Una delle funzionalità più importanti del percorso della grafica è la possibilità di definire quando più righe devono essere connesse e quando non devono essere collegati. La differenza può essere significativa, come le parti superiori di queste due triangoli illustrano:

![](paths-images/connectedlinesexample.png "Due triangoli che mostrano le differenze tra le linee connesse e disconnesse")

Un percorso di grafica viene incapsulato dal [ `SKPath` ](xref:SkiaSharp.SKPath) oggetto. Un percorso è una raccolta di uno o più *contorni*. Ogni distribuzione è una raccolta di *connessi* linee rette e curve. Distribuzioni non sono connessi tra loro, ma sono visivamente potrebbe sovrapporsi. In alcuni casi una singola distribuzione può sovrapporsi a se stesso.

Una distribuzione a livello generale inizia con una chiamata al metodo seguente della `SKPath`:

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) Per iniziare una nuova distribuzione

L'argomento al metodo è un singolo punto, è possibile esprimere come un `SKPoint` come separato X e Y coordinate o valore. Il `MoveTo` chiamata stabilisce un punto all'inizio di un'iniziale e il contorno *punto corrente*. È possibile chiamare i metodi seguenti per continuare la distribuzione con una riga o una curva dal punto corrente a un punto specificato nel metodo, che diventa il nuovo punto corrente:

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) Per aggiungere una linea retta nel percorso
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) Per aggiungere un arco, vale a dire una riga nella circonferenza di un cerchio o dell'ellisse
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) Per aggiungere una spline di Bézier cubica
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) Per aggiungere una spline di Bézier quadratica
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) Per aggiungere una razionale spline Bezier quadratica, che può eseguire in modo accurato il rendering di sezioni conica (puntini di sospensione, parabolas e hyperbolas)

Nessuno di questi cinque metodi contengono tutte le informazioni necessarie per descrivere la riga o una curva. Ognuno di questi cinque metodi funziona in combinazione con il punto corrente stabilito dalla chiamata al metodo precede immediatamente. Ad esempio, il `LineTo` metodo aggiunge una linea retta per la distribuzione basata sul punto corrente di, il parametro `LineTo` è solo un singolo punto.

Il `SKPath` classe definisce anche i metodi che hanno gli stessi nomi di questi sei metodi ma con un `R` all'inizio:

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

Il `R` è l'acronimo *relativo*. Questi metodi hanno la stessa sintassi dei metodi corrispondenti senza il `R` ma sono relativo al punto corrente. Questi strumenti sono utili per la creazione di parti simili di un percorso in un metodo che viene chiamato più volte.

Una distribuzione termina con un'altra chiamata a `MoveTo` oppure `RMoveTo`, che inizia una nuova distribuzione o una chiamata a `Close`, che chiude il contorno. Il `Close` metodo automaticamente aggiunge una linea retta dal punto corrente e il primo punto del contorno e contrassegna il percorso come chiuso, il che significa che verrà visualizzata senza eventuali estremità dei tratti.

È illustrata la differenza tra i contorni aperte e chiuse nel **due distribuzioni del triangolo** pagina, che usa un `SKPath` oggetto con due distribuzioni per il rendering due triangoli. Il primo contorno è aperto e il secondo viene chiusa. Di seguito è riportato il [ `TwoTriangleContoursPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/TwoTriangleContoursPage.cs) classe:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create the path
    SKPath path = new SKPath();

    // Define the first contour
    path.MoveTo(0.5f * info.Width, 0.1f * info.Height);
    path.LineTo(0.2f * info.Width, 0.4f * info.Height);
    path.LineTo(0.8f * info.Width, 0.4f * info.Height);
    path.LineTo(0.5f * info.Width, 0.1f * info.Height);

    // Define the second contour
    path.MoveTo(0.5f * info.Width, 0.6f * info.Height);
    path.LineTo(0.2f * info.Width, 0.9f * info.Height);
    path.LineTo(0.8f * info.Width, 0.9f * info.Height);
    path.Close();

    // Create two SKPaint objects
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Magenta,
        StrokeWidth = 50
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    // Fill and stroke the path
    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

Il primo contorno è costituito da una chiamata a [ `MoveTo` ](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) usando le coordinate X e Y anziché un' `SKPoint` valore, seguita da tre chiamate a [ `LineTo` ](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) disegnare tre lati del triangolo. La seconda distribuzione ha solo due chiamate a `LineTo` ma viene completata la distribuzione con una chiamata a [ `Close` ](xref:SkiaSharp.SKPath.Close), che chiude il contorno. La differenza è significativa.

[![](paths-images/twotrianglecontours-small.png "Tripla screenshot della pagina di due distribuzioni di triangolo")](paths-images/twotrianglecontours-large.png#lightbox "tripla screenshot della pagina di due distribuzioni triangolo")

Come può notare, il primo contorno è ovviamente una serie di tre linee collegate, ma non può connettersi con inizio alla fine. Le due righe sovrappongano nella parte superiore. La seconda distribuzione ovviamente viene chiuso ed è stata eseguita con una minore `LineTo` chiama perché il `Close` metodo aggiunge automaticamente una riga finale per chiudere il contorno.

`SKCanvas` definisce un solo [ `DrawPath` ](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) metodo, che in questa dimostrazione viene chiamato due volte per riempire e tracciare il percorso. Tutte le distribuzioni sono riempiti, anche quelli che non sono chiusi. Per motivi di compilazione di percorsi non chiusi, una linea retta si presuppone che esiste tra i punti iniziale e finale delle distribuzioni. Se si rimuove l'ultima `LineTo` dal primo contorno o rimuovere il `Close` chiamata dal contorno secondo, ogni contour avrà solo due lati ma essere compilato come se fosse un triangolo.

`SKPath` definisce molti altri metodi e proprietà. I metodi seguenti aggiungono i contorni interi per il percorso, che potrebbe essere chiuso o non è stato chiuso a seconda del metodo:

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) Per aggiungere una curva sulla circonferenza di un'ellisse
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) Per aggiungere un altro percorso per il percorso corrente
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) Per aggiungere un altro percorso in ordine inverso

Tenere presente che un `SKPath` oggetto definisce solo una geometria &mdash; una serie di punti e le connessioni. Solo quando un `SKPath` combinata con un `SKPaint` oggetto è il percorso in cui viene eseguito il rendering con un colore specifico, spessore del tratto e così via. Inoltre, tenere presente che il `SKPaint` oggetto passato per il `DrawPath` metodo consente di definire le caratteristiche dell'intero percorso. Se si desidera disegnare qualcosa che richiedono vari colori, è necessario utilizzare un percorso distinto per ogni colore.

Proprio come l'aspetto di inizio e alla fine di una riga è definito da un'estremità della traccia, l'aspetto della connessione tra due righe è definito da una *join stroke*. Viene specificata impostando il [ `StrokeJoin` ](xref:SkiaSharp.SKPaint.StrokeJoin) proprietà della `SKPaint` a un membro del [ `SKStrokeJoin` ](xref:SkiaSharp.SKStrokeJoin) enumerazione:

- `Miter` per un join indossano
- `Round` per un join arrotondato
- `Bevel` per un join abbassate-off

Il **join tratto** pagina mostra tre tracciare join con codice simile al **estremità dei tratti** pagina. Questo è il `PaintSurface` gestore dell'evento nel [ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs) classe:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Right
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width - 100;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = 2 * textPaint.FontSpacing;
    string[] strStrokeJoins = { "Miter", "Round", "Bevel" };

    foreach (string strStrokeJoin in strStrokeJoins)
    {
        // Display text
        canvas.DrawText(strStrokeJoin, xText, y, textPaint);

        // Get stroke-join value
        SKStrokeJoin strokeJoin;
        Enum.TryParse(strStrokeJoin, out strokeJoin);

        // Create path
        SKPath path = new SKPath();
        path.MoveTo(xLine1, y - 80);
        path.LineTo(xLine1, y + 80);
        path.LineTo(xLine2, y + 80);

        // Display thick line
        thickLinePaint.StrokeJoin = strokeJoin;
        canvas.DrawPath(path, thickLinePaint);

        // Display thin line
        canvas.DrawPath(path, thinLinePaint);
        y += 3 * textPaint.FontSpacing;
    }
}
```

Ecco il programma in esecuzione:

[![](paths-images/strokejoins-small.png "Tripla screenshot della pagina di tratto unisce")](paths-images/strokejoins-large.png#lightbox "tripla screenshot della pagina viene aggiunto tratto")

La giunzione è costituito da un punto ben strutturato di cui si connettono le righe. Quando due righe vengono aggiunti a un angolo di piccole dimensioni, la giunzione può diventare piuttosto lunga. Per evitare join acuto eccessivamente lunga, la lunghezza del join acuto è limitata dal valore della [ `StrokeMiter` ](xref:SkiaSharp.SKPaint.StrokeMiter) proprietà della `SKPaint`. Una giunzione che supera la lunghezza viene troncata per diventare un angolo smussato.

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
