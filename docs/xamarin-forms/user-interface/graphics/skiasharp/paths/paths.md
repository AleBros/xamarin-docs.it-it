---
title: Nozioni di base di percorso in SkiaSharp
description: Questo articolo esamina l'oggetto di SkiaSharp SKPath per la combinazione di linee e curve collegate e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: c892adf2f75ec00c4a9ee171ded78f79bb8227e9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292747"
---
# <a name="path-basics-in-skiasharp"></a>Nozioni di base di percorso in SkiaSharp

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Esplorare l'oggetto SkiaSharp SKPath per combinare linee e curve connesse_

Una delle funzionalità più importanti del percorso della grafica è la possibilità di definire quando più righe devono essere connesse e quando non devono essere collegati. La differenza può essere significativa, come le parti superiori di queste due triangoli illustrano:

![](paths-images/connectedlinesexample.png "Two triangles showing the difference between connected and disconnected lines")

Un percorso grafico è incapsulato dall'oggetto [`SKPath`](xref:SkiaSharp.SKPath) . Un percorso è una raccolta di uno o più *contorni*. Ogni contorno è una raccolta di curve e linee rette *connesse* . Distribuzioni non sono connessi tra loro, ma sono visivamente potrebbe sovrapporsi. In alcuni casi una singola distribuzione può sovrapporsi a se stesso.

Un contorno in genere inizia con una chiamata al metodo di `SKPath`seguente:

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) per iniziare un nuovo contorno

L'argomento del metodo è un singolo punto, che può essere espresso come valore di `SKPoint` o come coordinate X e Y separate. La chiamata di `MoveTo` stabilisce un punto all'inizio del contorno e un *punto corrente*iniziale. È possibile chiamare i metodi seguenti per continuare la distribuzione con una riga o una curva dal punto corrente a un punto specificato nel metodo, che diventa il nuovo punto corrente:

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) aggiungere una linea retta al percorso
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) aggiungere un arco, ovvero una linea sulla circonferenza di un cerchio o di un'ellisse
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) aggiungere una spline di Bezier cubica
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) aggiungere una spline di Bezier quadratica
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) aggiungere una spline di Bezier quadratica razionale, che può eseguire il rendering accurato di sezioni coniche (ellissi, parabole ed iperbole)

Nessuno di questi cinque metodi contengono tutte le informazioni necessarie per descrivere la riga o una curva. Ognuno di questi cinque metodi funziona in combinazione con il punto corrente stabilito dalla chiamata al metodo precede immediatamente. Ad esempio, il metodo `LineTo` aggiunge una linea retta al contorno in base al punto corrente, quindi il parametro per `LineTo` è solo un singolo punto.

La classe `SKPath` definisce anche i metodi che hanno gli stessi nomi di questi sei metodi ma con un `R` all'inizio:

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

Il `R` sta per la *relativa*. Questi metodi hanno la stessa sintassi dei metodi corrispondenti senza il `R` ma sono relativi al punto corrente. Questi strumenti sono utili per la creazione di parti simili di un percorso in un metodo che viene chiamato più volte.

Un contorno termina con un'altra chiamata a `MoveTo` o `RMoveTo`, che inizia un nuovo contorno o una chiamata a `Close`, che chiude il contorno. Il metodo `Close` aggiunge automaticamente una linea retta dal punto corrente al primo punto del contorno e contrassegna il tracciato come chiuso, il che significa che verrà eseguito il rendering senza alcuna estremità del tratto.

La differenza tra i contorni aperti e chiusi è illustrata nella pagina due contorni a **triangolo** , che usa un oggetto `SKPath` con due contorni per eseguire il rendering di due triangoli. Il primo contorno è aperto e il secondo viene chiusa. Ecco la classe [`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs) :

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

Il primo contorno è costituito da una chiamata a [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) usando le coordinate X e Y anziché un valore `SKPoint`, seguito da tre chiamate a [`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) per creare i tre lati del triangolo. Il secondo contorno ha solo due chiamate a `LineTo` ma termina il contorno con una chiamata a [`Close`](xref:SkiaSharp.SKPath.Close), che chiude il contorno. La differenza è significativa.

[![](paths-images/twotrianglecontours-small.png "Triple screenshot of the Two Triangle Contours page")](paths-images/twotrianglecontours-large.png#lightbox "Triple screenshot of the Two Triangle Contours page")

Come può notare, il primo contorno è ovviamente una serie di tre linee collegate, ma non può connettersi con inizio alla fine. Le due righe sovrappongano nella parte superiore. Il secondo contorno ovviamente è chiuso ed è stato eseguito con un minor numero di chiamate `LineTo` perché il metodo `Close` aggiunge automaticamente una riga finale per chiudere il contorno.

`SKCanvas` definisce un solo metodo [`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) , che in questa dimostrazione viene chiamato due volte per riempire e tracciare il percorso. Tutte le distribuzioni sono riempiti, anche quelli che non sono chiusi. Per motivi di compilazione di percorsi non chiusi, una linea retta si presuppone che esiste tra i punti iniziale e finale delle distribuzioni. Se si rimuove l'ultimo `LineTo` dal primo contorno o si rimuove la chiamata `Close` dal secondo contorno, ogni contorno avrà solo due lati, ma verrà compilato come se fosse un triangolo.

`SKPath` definisce molti altri metodi e proprietà. I metodi seguenti aggiungono i contorni interi per il percorso, che potrebbe essere chiuso o non è stato chiuso a seconda del metodo:

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) aggiungere una curva sulla circonferenza di un'ellisse
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) aggiungere un altro percorso al percorso corrente
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) aggiungere un altro percorso in ordine inverso

Tenere presente che un oggetto `SKPath` definisce solo una geometria &mdash; una serie di punti e connessioni. Solo quando un `SKPath` viene combinato con un oggetto `SKPaint` è il percorso di cui viene eseguito il rendering con un colore, una larghezza del tratto e così via. Tenere inoltre presente che l'oggetto `SKPaint` passato al metodo `DrawPath` definisce le caratteristiche dell'intero percorso. Se si desidera disegnare qualcosa che richiedono vari colori, è necessario utilizzare un percorso distinto per ogni colore.

Proprio come l'aspetto dell'inizio e della fine di una riga è definito da un limite del tratto, l'aspetto della connessione tra due righe è definito da un *join di tratto*. Per specificare questa impostazione, impostare la proprietà [`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin) di `SKPaint` su un membro dell'enumerazione [`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin) :

- `Miter` per un join a punta
- `Round` per un join arrotondato
- `Bevel` per un join troncato

La pagina **join tratto** Mostra questi tre join di tratto con codice simile alla pagina dei **limiti del tratto** . Si tratta del gestore dell'evento `PaintSurface` nella classe [`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs) :

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

[![](paths-images/strokejoins-small.png "Triple screenshot of the Stroke Joins page")](paths-images/strokejoins-large.png#lightbox "Triple screenshot of the Stroke Joins page")

La giunzione è costituito da un punto ben strutturato di cui si connettono le righe. Quando due righe vengono aggiunti a un angolo di piccole dimensioni, la giunzione può diventare piuttosto lunga. Per impedire il join di un Miter eccessivamente lungo, la lunghezza del join dell'angolo acuto è limitata dal valore della proprietà [`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter) di `SKPaint`. Una giunzione che supera la lunghezza viene troncata per diventare un angolo smussato.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
