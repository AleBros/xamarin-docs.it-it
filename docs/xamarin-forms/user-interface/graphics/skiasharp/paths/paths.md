---
title: Nozioni fondamentali di percorso
description: Esplorare l'oggetto SkiaSharp SKPath per la combinazione di linee e curve collegate
ms.topic: article
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: f02c5cfd75fd9d9cd97d28ca276b32808f7a45ae
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="path-basics"></a>Nozioni fondamentali di percorso

_Esplorare l'oggetto SkiaSharp SKPath per la combinazione di linee e curve collegate_

Tra le funzionalità più importanti del percorso di grafica è la possibilità di definire quando devono essere connessa più righe e quando non devono essere connessi. La differenza può essere piuttosto visibile, come illustrano le parti superiori di queste due triangoli:

![](paths-images/connectedlinesexample.png "Due triangoli che mostrano le differenze tra le linee connesse e disconnesse")

Un percorso di grafica viene incapsulata dalle classi di [ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/) oggetto. Un percorso è una raccolta di uno o più *contorni*. Ogni profilo è una raccolta di *connesso* linee rette e curve. Distribuzione non sono connessi tra loro, ma potrebbe visivamente si sovrappongono. In alcuni casi una singola distribuzione può sovrapporsi stesso.

Una distribuzione in genere inizia con una chiamata al metodo seguente di `SKPath`:

- `MoveTo` Per iniziare una nuova distribuzione

L'argomento a tale metodo è un singolo punto, è possibile esprimere come un `SKPoint` come separato X e Y coordinate o valore. Il `MoveTo` chiamata stabilisce un punto all'inizio di contorno e iniziale *punto corrente*. È possibile chiamare i metodi seguenti per continuare la distribuzione con una riga o una curva dal punto corrente a un punto specificato nel metodo, che diventa il nuovo punto corrente:

- `LineTo` Per aggiungere il percorso di una linea retta
- `ArcTo` Per aggiungere un arco, che è una linea lungo la circonferenza di un cerchio o puntini di sospensione
- `CubicTo` Per aggiungere una spline di Bézier cubica
- `QuadTo` Per aggiungere una spline di Bézier quadratica
- `ConicTo` Per aggiungere una razionale quadratica spline di Bézier, che può eseguire in modo accurato il rendering di sezioni conica (puntini di sospensione, parabolas e hyperbolas)

Nessuno di questi cinque metodi contiene tutte le informazioni necessarie per descrivere la riga o la curva. Ognuno di questi cinque metodi funziona in abbinamento con il punto corrente stabilito dalla chiamata al metodo precedono immediatamente. Ad esempio, il `LineTo` metodo aggiunge una linea retta per il contorno basate sul punto corrente, pertanto il parametro `LineTo` è solo un singolo punto.

Il `SKPath` classe definisce inoltre metodi che hanno gli stessi nomi di questi sei metodi ma con un `R` all'inizio:

- `RMoveTo`
- `RLineTo`
- `RArcTo`
- `RCubicTo`
- `RQuadTo`
- `RConicTo`

Il `R` è l'acronimo di *relativo*. Hanno la stessa sintassi dei metodi corrispondenti senza il `R` ma sono rispetto al punto corrente. Questi sono utili per la creazione di parti simili di un percorso in un metodo che viene chiamato più volte.

Una distribuzione termina con un'altra chiamata a `MoveTo` o `RMoveTo`, che inizia un nuovo profilo o una chiamata a `Close`, che chiude il contorno. Il `Close` metodo automaticamente aggiunge una linea retta dal punto corrente il primo punto del contorno e contrassegna il percorso come chiuso, il che significa che viene sottoposto a rendering senza le maiuscole tratto.

È illustrata la differenza tra i contorni aperte e chiuse nel **due distribuzioni del triangolo** pagina, che utilizza un `SKPath` oggetto con due distribuzioni per il rendering di due triangoli. Il primo contorno è aperto e il secondo è chiuso. Ecco il [ `TwoTriangleContours` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/TwoTriangleContoursPage.cs) classe:

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

Il primo contorno è costituito da una chiamata a [ `MoveTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.MoveTo/p/System.Single/System.Single/) usando le coordinate X e Y anziché un' `SKPoint` valore, seguita da tre chiamate al [ `LineTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.LineTo/p/System.Single/System.Single/) per disegnare i lati di tre il triangolo. Il secondo contorno ha solo due chiamate a `LineTo` ma viene completata la distribuzione con una chiamata a [ `Close` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Close()/), che chiude il contorno. La differenza è significativa.

[![](paths-images/twotrianglecontours-small.png "La schermata della pagine dei contorni triangolo due")](paths-images/twotrianglecontours-large.png "tripla schermata della pagine dei contorni triangolo due")

Come si può notare, il primo contorno ovviamente è una serie di tre righe collegate, ma la fine non connettersi con l'inizio. Le due righe si sovrappongono nella parte superiore. Il secondo contorno ovviamente viene chiusa ed è stato ottenuto con uno meno `LineTo` chiama perché il `Close` metodo aggiunge automaticamente una riga finale per chiudere il contorno.

`SKCanvas` definisce un solo [ `DrawPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawPath/p/SkiaSharp.SKPath/SkiaSharp.SKPaint/) metodo, che in questa demo viene chiamato due volte per compilare e tracciare il percorso. Tutti i contorni sono riempiti, anche quelli che non sono chiusi. Per motivi di compilazione i percorsi non chiusi, una linea retta presume che esiste tra i punti iniziale e finale dei contorni. Se si rimuove l'ultimo `LineTo` dal primo contorno o rimuovere il `Close` chiamata dal secondo contorno, ciascun contorno avrà solo due lati ma verrà compilata come se fosse un triangolo.

`SKPath` definisce molti altri metodi e proprietà. I metodi seguenti aggiungono contorni interi per il percorso, che potrebbe essere chiusa o non è stato chiuso a seconda del metodo:

- `AddRect`
- [`AddRoundedRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddRoundedRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddCircle`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddCircle/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddOval`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddOval/p/SkiaSharp.SKRect/SkiaSharp.SKPathDirection/)
- [`AddArc`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/) Per aggiungere una curva alla circonferenza di un'ellisse
- `AddPath` Per aggiungere un altro percorso per il percorso corrente
- [`AddPathReverse`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddPathReverse/p/SkiaSharp.SKPath/) Per aggiungere un altro percorso in ordine inverso

Tenere presente che un `SKPath` oggetto definisce solo una geometria & #x 2014; una serie di punti e le connessioni. Solo quando un `SKPath` viene combinato con un `SKPaint` oggetto è il percorso in cui il rendering di un determinato colore, spessore e così via. Inoltre, tenere presente che il `SKPaint` oggetto passato per il `DrawPath` metodo consente di definire le caratteristiche dell'intero percorso. Se si desidera creare un elemento che richiedono vari colori, è necessario utilizzare un percorso separato per ogni colore.

Come l'aspetto di inizio e alla fine di una riga è definito da un'estremità della traccia, l'aspetto della connessione tra due linee è definito da un *join tratto*. Specificare impostando il [ `StrokeJoin` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeJoin/) proprietà di `SKPaint` a un membro del [ `SKStrokeJoin` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeJoin/) enumerazione:

- [`Miter`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Miter/) creare un join indossano
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Round/) creare un join arrotondato
- [`Bevel`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Bevel/) creare un join abbassate-off

Il **tratto join** pagina vengono visualizzati questi tre tracciare join con codice simile di **maiuscole tratto** pagina. Si tratta di `PaintSurface` gestore dell'evento nel [ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs) classe:

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

Di seguito è riportato il programma in esecuzione su tre piattaforme:

[![](paths-images/strokejoins-small.png "Schermata triplo della pagina tratto join")](paths-images/strokejoins-large.png "tripla schermata della pagine dei join tratto")

La giunzione è costituito da un punto acuto in cui le righe di connettono. Quando un piccolo angolo unito in join due righe, la giunzione può diventare piuttosto lunga. Per evitare l'angolo acuto eccessivamente lunghe join, la lunghezza di giunzione è limitata dal valore della [ `StrokeMiter` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeMiter/) proprietà `SKPaint`. Una giunzione che supera la lunghezza viene troncata per diventare un join della smussatura.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
