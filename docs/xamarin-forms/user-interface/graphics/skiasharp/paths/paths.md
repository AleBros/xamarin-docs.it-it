---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6ceac2d866e67af5cf3496fcf8c072ae83ecfe38
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140244"
---
# <a name="path-basics-in-skiasharp"></a>Nozioni di base sui percorsi in SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Esplorare l'oggetto SkiaSharp SKPath per combinare linee e curve connesse_

Una delle funzionalità più importanti del percorso grafico è la possibilità di definire quando devono essere connesse più righe e quando non devono essere connesse. La differenza può essere significativa, perché i vertici di questi due triangoli dimostrano:

![](paths-images/connectedlinesexample.png "Two triangles showing the difference between connected and disconnected lines")

Un percorso grafico è incapsulato dall' [`SKPath`](xref:SkiaSharp.SKPath) oggetto. Un percorso è una raccolta di uno o più *contorni*. Ogni contorno è una raccolta di curve e linee rette *connesse* . I contorni non sono connessi tra loro, ma potrebbero sovrapporsi visivamente. A volte un singolo contorno può sovrapporsi.

Un contorno in genere inizia con una chiamata al metodo seguente di `SKPath` :

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*)per iniziare un nuovo contorno

L'argomento del metodo è un singolo punto, che può essere espresso come `SKPoint` valore o come coordinate X e Y separate. La `MoveTo` chiamata stabilisce un punto all'inizio del contorno e un *punto corrente*iniziale. È possibile chiamare i metodi seguenti per continuare il contorno con una linea o una curva dal punto corrente a un punto specificato nel metodo, che diventa quindi il nuovo punto corrente:

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*)per aggiungere una linea retta al percorso
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*)per aggiungere un arco, ovvero una linea sulla circonferenza di un cerchio o di un'ellisse
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*)per aggiungere una spline di Bezier cubica
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*)per aggiungere una spline di Bézier quadratica
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*)per aggiungere una spline di Bezier quadratica razionale, che può eseguire il rendering accurato di sezioni coniche (ellissi, parabole ed iperbole)

Nessuno di questi cinque metodi contiene tutte le informazioni necessarie per descrivere la linea o la curva. Ognuno di questi cinque metodi funziona insieme al punto corrente stabilito dalla chiamata al metodo immediatamente precedente. Ad esempio, il `LineTo` metodo aggiunge una linea retta al contorno in base al punto corrente, quindi il parametro a `LineTo` è solo un singolo punto.

La `SKPath` classe definisce anche i metodi che hanno gli stessi nomi di questi sei metodi ma con un oggetto `R` all'inizio:

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

Il `R` sta per la *relativa*. Questi metodi hanno la stessa sintassi dei metodi corrispondenti senza `R` ma sono relativi al punto corrente. Queste sono utili per disegnare parti simili di un percorso in un metodo chiamato più volte.

Un contorno termina con un'altra chiamata a `MoveTo` o `RMoveTo` , che avvia un nuovo contorno o una chiamata a `Close` , che chiude il contorno. Il `Close` metodo aggiunge automaticamente una linea retta dal punto corrente al primo punto del contorno e contrassegna il tracciato come chiuso, il che significa che verrà eseguito il rendering senza alcuna estremità del tratto.

La differenza tra i contorni aperti e chiusi è illustrata nella pagina **due** contorni a triangolo, che usa un `SKPath` oggetto con due contorni per eseguire il rendering di due triangoli. Il primo contorno è aperto e il secondo è chiuso. Ecco la [`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs) classe:

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

Il primo contorno è costituito da una chiamata a [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) usando le coordinate X e Y anziché un `SKPoint` valore, seguito da tre chiamate a [`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) per creare i tre lati del triangolo. Il secondo contorno ha solo due chiamate a `LineTo` , ma termina il contorno con una chiamata a [`Close`](xref:SkiaSharp.SKPath.Close) , che chiude il contorno. La differenza è significativa:

[![](paths-images/twotrianglecontours-small.png "Triple screenshot of the Two Triangle Contours page")](paths-images/twotrianglecontours-large.png#lightbox "Triple screenshot of the Two Triangle Contours page")

Come si può notare, il primo contorno è ovviamente una serie di tre linee connesse, ma la fine non si connette con l'inizio. Le due righe si sovrappongono nella parte superiore. Il secondo contorno ovviamente è chiuso ed è stato eseguito con un minor numero `LineTo` di chiamate perché il `Close` metodo aggiunge automaticamente una riga finale per chiudere il contorno.

`SKCanvas`definisce un solo [`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) metodo, che in questa dimostrazione viene chiamato due volte per riempire e tracciare il percorso. Tutti i contorni sono pieni, anche quelli che non sono chiusi. Ai fini del riempimento dei percorsi non chiusi, si presuppone che esista una linea retta tra i punti di inizio e di fine dei contorni. Se si rimuove l'ultimo `LineTo` dal primo contorno o si rimuove la `Close` chiamata dal secondo contorno, ogni contorno avrà solo due lati, ma verrà compilato come se fosse un triangolo.

`SKPath`definisce molti altri metodi e proprietà. I metodi seguenti aggiungono interi contorni al percorso, che potrebbe essere chiuso o non chiuso a seconda del metodo:

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single))per aggiungere una curva sulla circonferenza di un'ellisse
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*)per aggiungere un altro percorso al percorso corrente
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath))per aggiungere un altro percorso in ordine inverso

Tenere presente che un `SKPath` oggetto definisce solo una geometria &mdash; di una serie di punti e connessioni. Solo quando un `SKPath` oggetto è combinato con un `SKPaint` oggetto è il percorso di cui viene eseguito il rendering con un colore, una larghezza del tratto e così via. Tenere inoltre presente che l' `SKPaint` oggetto passato al `DrawPath` metodo definisce le caratteristiche dell'intero percorso. Se si desidera creare un elemento che richiede più colori, è necessario utilizzare un percorso separato per ogni colore.

Proprio come l'aspetto dell'inizio e della fine di una riga è definito da un limite del tratto, l'aspetto della connessione tra due righe è definito da un *join di tratto*. Per specificare questa impostazione, impostare la [`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin) proprietà di `SKPaint` su un membro dell' [`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin) enumerazione:

- `Miter`per un join a punta
- `Round`per un join arrotondato
- `Bevel`per un join troncato

La pagina **join tratto** Mostra questi tre join di tratto con codice simile alla pagina dei **limiti del tratto** . Si tratta del `PaintSurface` gestore dell'evento nella [`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs) classe:

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

Il join Miter è costituito da un punto acuto in cui le linee si connettono. Quando due righe si uniscono a un angolo piccolo, il join di smussatura può diventare molto lungo. Per impedire il join di un Miter eccessivamente lungo, la lunghezza del join dell'acaro è limitata dal valore della [`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter) proprietà di `SKPaint` . Il join di un acaro che supera questa lunghezza viene troncato per diventare un join smussato.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
