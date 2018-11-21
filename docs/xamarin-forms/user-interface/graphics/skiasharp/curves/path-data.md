---
title: Dati del tracciato SVG in SkiaSharp
description: Questo articolo illustra come definire i percorsi di SkiaSharp usando stringhe di testo nel formato Scalable Vector Graphics e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2017
ms.openlocfilehash: 3571da005f48bda630b4202b804f3efe9c892f60
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172327"
---
# <a name="svg-path-data-in-skiasharp"></a>Dati del tracciato SVG in SkiaSharp

_Definire i percorsi di uso delle stringhe di testo in formato Scalable Vector Graphics_

Il [ `SKPath` ](xref:SkiaSharp.SKPath) classe supporta la definizione di oggetti intero percorso da stringhe di testo in un formato stabilito dalla specifica Scalable Vector Graphics (SVG). Si noterà più avanti in questo articolo come è possibile rappresentare un intero percorso di questo tipo in una stringa di testo:

![](path-data-images/pathdatasample.png "Un tracciato di esempio definito con dati del tracciato SVG")

SVG è una linguaggio di programmazione per le pagine web della grafica basato su XML. Poiché SVG devono consentire i percorsi devono essere definite nella markup anziché a una serie di chiamate di funzione, il formato SVG standard include un metodo estremamente conciso specificando un percorso di grafica intero come stringa di testo.

All'interno di SkiaSharp, questo formato è detto "-dati del tracciato SVG." Il formato è supportato anche in ambienti programmazione basata su XAML di Windows, tra cui the Windows Presentation Foundation e la piattaforma Windows universale, in cui è noto come il [sintassi di Markup del percorso](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax) o il [spostare disegnare la sintassi di comandi](/windows/uwp/xaml-platform/move-draw-commands-syntax/). Può anche fungere da un formato di scambio per immagini di grafica vettoriale, in particolare nei file basata su testo, ad esempio XML.

Il [ `SKPath` ](xref:SkiaSharp.SKPath) classe definisce due metodi con le parole `SvgPathData` nei relativi nomi:

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

Il metodo statico [ `ParseSvgPathData` ](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) metodo converte una stringa in un `SKPath` oggetto, mentre [ `ToSvgPathData` ](xref:SkiaSharp.SKPath.ToSvgPathData) converte un `SKPath` oggetto in una stringa.

Di seguito è una stringa SVG per una stella a 5 punte centrata sul punto (0, 0) con un raggio pari a 100:

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

Le lettere sono comandi che creano un' `SKPath` oggetto: `M` indica una `MoveTo` chiamare, `L` viene `LineTo`, e `Z` è `Close` per chiudere un contorno. Ogni coppia di numeri fornisce una coordinata X e Y di un punto. Si noti che il `L` comando è seguito da più punti separati da virgole. In una serie di coordinate e i punti, virgole e spazi vuoti vengono considerati in modo identico. Alcuni programmatori preferiscono inserire virgole tra le coordinate X e Y invece che tra i punti, ma virgole o spazi sono necessarie solo per evitare ambiguità. Si tratta di una condizione perfettamente possibile:

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

La sintassi dei dati del tracciato SVG formalmente è documentata in [8.3 sezione della specifica di SVG](http://www.w3.org/TR/SVG11/paths.html#PathData). Ecco un riepilogo:

## <a name="moveto"></a>**MoveTo**

```
M x y
```

Viene avviato un nuovo contorno del percorso tramite l'impostazione della posizione corrente. I dati del percorso devono sempre iniziare con un `M` comando.

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

Questo comando aggiunge una linea retta (o le righe) al percorso e imposta la nuova posizione corrente alla fine dell'ultima riga. È possibile seguire le `L` comando con più coppie di *x* e *y* coordinate.

## <a name="horizontal-lineto"></a>**Orizzontale LineTo**

```
H x ...
```

Questo comando aggiunge una riga orizzontale al percorso e imposta la nuova posizione corrente alla fine della riga. È possibile seguire le `H` comando con più *x* coordinate, ma non ha molto senso.

## <a name="vertical-line"></a>**Linea verticale**

```
V y ...
```

Questo comando aggiunge una linea verticale al percorso e imposta la nuova posizione corrente alla fine della riga.

## <a name="close"></a>**Chiudi**

```
Z
```

Il `C` comando chiude il contorno mediante l'aggiunta di una linea retta dalla posizione corrente all'inizio del profilo.

## <a name="arcto"></a>**ArcTo**

Il comando per aggiungere un arco ellittico per la distribuzione è di gran lunga il più complesso nella specifica di dati del percorso SVG intera. È l'unico comando in cui i numeri possono rappresentare un valore diverso da valori di coordinate:

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

Il *rx* e *ry* i parametri sono i raggi orizzontali e verticali dell'ellisse. Il *angolo di rotazione* sia in senso orario espresso in gradi.

Impostare il *grandi arco-flag* su 1 per l'arco di grandi dimensioni o su 0 per l'arco di piccole dimensioni.

Impostare il *durante lo sweep flag* su 1 per in senso orario e su 0 per in senso antiorario.

Viene disegnato l'arco al punto (*x*, *y*), che diventa la nuova posizione corrente.

## <a name="cubicto"></a>**CubicTo**

```
C x1 y1 x2 y2 x3 y3 ...
```

Questo comando aggiunge una curva di Bézier cubica tra la posizione corrente a (*x3*, *y3*), che diventa la nuova posizione corrente. I punti (*x1*, *y1*) e (*x2*, *y2*) sono i punti di controllo.

È possibile specificare più curve di Bézier da un singolo `C` comando. Il numero di punti deve essere un multiplo di 3.

È anche un comando di curva di Bézier "smooth":

```
S x2 y2 x3 y3 ...
```

Questo comando deve seguire un comando di Bézier normale (sebbene che non sia strettamente necessario). Il comando di Bézier smooth calcola il primo punto di controllo in modo che risulti un riflesso del secondo punto di controllo di Bézier intorno a loro reciproca punto precedente. Queste tre punti sono collineari pertanto e la connessione tra le due curve di Bézier è uniforme.

## <a name="quadto"></a>**QuadTo**

```
Q x1 y1 x2 y2 ...
```

Per le curve di Bézier quadratiche, il numero di punti deve essere un multiplo di 2. Il punto di controllo (*x1*, *y1*) e il punto finale (e la nuova posizione corrente) sono (*x2*, *y2*)

È anche un comando di curva quadratica smooth:

```
T x2 y2 ...
```

Il punto di controllo viene calcolato in base il punto di controllo della curva quadratica precedente.

Tutti questi comandi sono anche disponibili nelle versioni "relative", dove sono i punti di coordinate rispetto alla posizione corrente. Questi comandi relativi iniziano con lettere minuscole, ad esempio `c` anziché `C` per la relativa versione del comando di Bézier cubica.

Questo è l'ambito della definizione del percorso dati SVG. Non sono disponibili funzionalità per la ripetizione di gruppi di comandi o per l'esecuzione di qualsiasi tipo di calcolo. I comandi per `ConicTo` oppure non sono disponibili altri tipi di specifiche dell'arco.

Il metodo statico [ `SKPath.ParseSvgPathData` ](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) metodo prevede una stringa valida di comandi SVG. Se viene rilevato un errore di sintassi, il metodo restituisce `null`. Ovvero l'indicazione di errore solo.

Il [ `ToSvgPathData` ](xref:SkiaSharp.SKPath.ToSvgPathData) metodo è utile per il recupero di dati del tracciato SVG da un oggetto esistente `SKPath` oggetti per trasferire a un altro programma, o per archiviare in un formato di file di testo come XML. (Il `ToSvgPathData` metodo non è illustrato nel codice di esempio in questo articolo.) Effettuare *non* aspettarsi `ToSvgPathData` per restituire una stringa che corrisponde esattamente alle chiamate al metodo che ha creato il percorso. In particolare, si scoprirà che gli archi vengono convertiti in più `QuadTo` comandi, ovvero come vengono visualizzati nei dati del percorso restituiti da `ToSvgPathData`.

Il **Hello di percorso dati** pagina dirompenti la parola "HELLO" usando dati del tracciato SVG. Sia la `SKPath` e `SKPaint` gli oggetti vengono definiti come campi nel [ `PathDataHelloPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) classe:

```csharp
public class PathDataHelloPage : ContentPage
{
    SKPath helloPath = SKPath.ParseSvgPathData(
        "M 0 0 L 0 100 M 0 50 L 50 50 M 50 0 L 50 100" +                // H
        "M 125 0 C 60 -10, 60 60, 125 50, 60 40, 60 110, 125 100" +     // E
        "M 150 0 L 150 100, 200 100" +                                  // L
        "M 225 0 L 225 100, 275 100" +                                  // L
        "M 300 50 A 25 50 0 1 0 300 49.9 Z");                           // O

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ...
}
```

Il percorso che definisce la stringa di testo in cui inizia l'angolo superiore sinistro in corrispondenza del punto (0, 0). Ogni lettera è 50 unità di larghezza e altezza 100 unità e lettere sono separate da un'altra unità 25, il che significa che l'intero percorso pari a 350 unità wide.

'H' "Hello" è costituito da tre i contorni di una sola riga, mentre la 'E' è due curve di Bézier cubiche collegate. Si noti che il `C` comando è seguito da sei punti e due dei punti di controllo hanno coordinata Y pari a 110, che li pone compreso nell'intervallo delle coordinate Y delle altre lettere e tra -10. 'L' è di due linee collegate, mentre il ' o ' è un'ellisse che viene eseguito il rendering con un `A` comando.

Si noti che il `M` che inizia il contorno ultimo comando imposta la posizione per il punto (350, 50), che è al centro verticale di sinistra del ' o '. Come indicato nell'esempio seguente i numeri primi di `A` comando, l'ellisse ha un raggio orizzontale del 25 e un raggio verticale di 50. Il punto finale è indicato dall'ultima coppia di numeri di `A` comando, che rappresenta il punto (300, 49.9). Che viene deliberatamente solo leggermente diverso dal punto di inizio. Se l'endpoint è uguale al punto di inizio, l'arco sarà non eseguire il rendering. Per disegnare un'ellisse completa, è necessario impostare l'endpoint simile a (ma non è uguale a) il punto di inizio oppure è necessario usare due o più `A` comandi, ciascuno per una parte dell'ellisse completo.

È possibile aggiungere l'istruzione seguente al costruttore della pagina e quindi impostare un punto di interruzione per esaminare la stringa risultante:

```csharp
string str = helloPath.ToSvgPathData();
```

Sarà possibile osservare che l'arco è stata sostituita con una lunga serie di `Q` comandi per un'approssimazione a fasi dell'arco usando curve di Bézier quadratiche.

Il `PaintSurface` gestore ottiene i limiti stretto del percorso, che non include i punti di controllo per la 'E' e ' o ' curve. Le tre trasformazioni spostare il centro del percorso fino al punto (0, 0), ridimensionare il percorso alla dimensione dell'area di disegno (ma considerando lo spessore del tratto anche) e quindi spostare il centro del percorso al centro dell'area di disegno:

```csharp
public class PathDataHelloPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect bounds;
        helloPath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(info.Width / (bounds.Width + paint.StrokeWidth),
                     info.Height / (bounds.Height + paint.StrokeWidth));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(helloPath, paint);
    }
}
```

Il percorso riempie l'area di disegno, che presenta un aspetto più ragionevole quando viene visualizzato in modalità orizzontale:

[![](path-data-images/pathdatahello-small.png "Tripla screenshot della pagina Hello di percorso dati")](path-data-images/pathdatahello-large.png#lightbox "tripla screenshot della pagina Hello di percorso dati")

Il **percorso dati Cat** pagina è simile. Gli oggetti di percorso e il disegno vengono entrambe definiti come campi nel [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe:

```csharp
public class PathDataCatPage : ContentPage
{
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 5
    };
    ...
}
```

Nell'intestazione della classe cat è un cerchio, e qui ne viene eseguito il rendering con due `A` comandi, ognuno dei quali consente di disegnare un semicerchio. Entrambi `A` i comandi per l'intestazione definiscono i raggi orizzontali e verticali pari a 100. Il primo arco inizia in corrispondenza (240, 100) e termina in corrispondenza (240, 300), che diventa il punto iniziale per l'arco secondo che termina in (240, 100).

Anche gli occhi vengono sottoposti a rendering con due `A` comandi e come con testina del team cat, il secondo `A` comando termina con lo stesso punto come punto di partenza del primo `A` comando. Tuttavia, queste coppie di `A` comandi non si definiscono un'ellisse. Il con di ogni arco è 40 unità e il raggio è anche 40 unità, vale a dire che questi sono archi non semicircles completo.

Il `PaintSurface` gestore esegue le trasformazioni simili dell'esempio precedente, ma imposta una singola `Scale` fattore per mantenere le proporzioni e fornire un margine minimo in modo baffi del gatto meglio non toccano i lati della schermata:

```csharp
public class PathDataCatPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        SKRect bounds;
        catPath.GetBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(0.9f * Math.Min(info.Width / bounds.Width,
                                     info.Height / bounds.Height));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(catPath, paint);
    }
}
```

Ecco il programma in esecuzione:

[![](path-data-images/pathdatacat-small.png "Tripla screenshot della pagina del percorso dati Cat")](path-data-images/pathdatacat-large.png#lightbox "tripla screenshot della pagina del percorso dati Cat")

In genere, quando un `SKPath` oggetto viene definito come un campo, i contorni del percorso devono essere definiti nel costruttore o un altro metodo. Quando si usano dati del tracciato SVG, tuttavia, si è visto che il percorso può essere specificato completamente nella definizione del campo.

Le versioni precedenti **confusa orologio analogico** esempio nel [ **l'omonima trasformazione** ](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md) articolo visualizzato il movimento delle lancette dell'orologio come semplici righe. Il **piuttosto orologio analogico** programma seguente sostituisce le righe con `SKPath` oggetti definiti come campi nel [ `PrettyAnalogClockPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) classe insieme a `SKPaint` oggetti:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    // Clock hands pointing straight up
    SKPath hourHandPath = SKPath.ParseSvgPathData(
        "M 0 -60 C   0 -30 20 -30  5 -20 L  5   0" +
                "C   5 7.5 -5 7.5 -5   0 L -5 -20" +
                "C -20 -30  0 -30  0 -60 Z");

    SKPath minuteHandPath = SKPath.ParseSvgPathData(
        "M 0 -80 C   0 -75  0 -70  2.5 -60 L  2.5   0" +
                "C   2.5 5 -2.5 5 -2.5   0 L -2.5 -60" +
                "C 0 -70  0 -75  0 -80 Z");

    SKPath secondHandPath = SKPath.ParseSvgPathData(
        "M 0 10 L 0 -80");

    // SKPaint objects
    SKPaint handStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2,
        StrokeCap = SKStrokeCap.Round
    };

    SKPaint handFillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Gray
    };
    ...
}
```

Il movimento delle lancette hour e minute have ora racchiusi tra le aree. Per rendere le mani distinti tra loro, vengono disegnati con un riquadro nero e con riempimento grigio il `handStrokePaint` e `handFillPaint` oggetti.

In precedenza **confusa orologio analogico** esempio piccola cerchi contrassegnato le ore e minuti sono stati disegnati in un ciclo. In questo **piuttosto orologio analogico** esempio, viene utilizzato un approccio completamente diverso: gli indicatori di ora e minuto sono linee tratteggiate disegnate con i `minuteMarkPaint` e `hourMarkPaint` oggetti:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    SKPaint minuteMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 3 * 3.14159f }, 0)
    };

    SKPaint hourMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 6,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 15 * 3.14159f }, 0)
    };
    ...
}
```

Il [ **da punti e trattini** ](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md) articolo descriveva come è possibile usare il [ `SKPathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash*) metodo per creare una linea tratteggiata. Il primo argomento è un `float` matrice a livello generale con due elementi: il primo elemento è la lunghezza dei trattini e il secondo elemento è il divario tra i trattini. Quando la `StrokeCap` è impostata su `SKStrokeCap.Round`, quindi le estremità arrotondate del trattino prolungare in modo efficace la lunghezza di dash per lo spessore del tratto su entrambi i lati del trattino. Pertanto, impostando il primo elemento della matrice su 0 consente di creare una linea punteggiata.

La distanza tra queste punti è disciplinata dal secondo elemento della matrice. Come si vedrà a breve, questi due `SKPaint` gli oggetti vengono utilizzati per disegnare cerchi con un raggio di unità di 90. La circonferenza del cerchio è pertanto 180π, il che significa che i segni di 60 minuti devono essere visualizzati ogni unità di 3 π, che corrisponde al valore nel secondo il `float` matrice `minuteMarkPaint`. I segni di 12 ore devono essere visualizzato ogni unità 15π, ovvero il valore nel secondo `float` matrice.

Il `PrettyAnalogClockPage` classe imposta un timer per invalidare l'area di ogni 16 millisecondi e il `PaintSurface` gestore viene chiamato con tale frequenza. Le definizioni delle versioni precedenti di `SKPath` e `SKPaint` oggetti consentono molto puliti codice di disegno:

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Transform for 100-radius circle in center
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / 200, info.Height / 200));

        // Draw circles for hour and minute marks
        SKRect rect = new SKRect(-90, -90, 90, 90);
        canvas.DrawOval(rect, minuteMarkPaint);
        canvas.DrawOval(rect, hourMarkPaint);

        // Get time
        DateTime dateTime = DateTime.Now;

        // Draw hour hand
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawPath(hourHandPath, handStrokePaint);
        canvas.DrawPath(hourHandPath, handFillPaint);
        canvas.Restore();

        // Draw minute hand
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawPath(minuteHandPath, handStrokePaint);
        canvas.DrawPath(minuteHandPath, handFillPaint);
        canvas.Restore();

        // Draw second hand
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        canvas.Save();
        canvas.RotateDegrees(6 * (dateTime.Second + (float)t));
        canvas.DrawPath(secondHandPath, handStrokePaint);
        canvas.Restore();
    }
}
```

Un'operazione particolare operazione viene eseguita con l'icona della mano, secondo tuttavia. Perché l'orologio viene aggiornato ogni 16 millisecondi, il `Millisecond` proprietà del `DateTime` valore potenzialmente può essere utilizzato per aggiungere un'animazione in secondo luogo manualmente uno sweep anziché uno che consente di spostare in collegamenti discreti da secondo a secondo. Ma questo codice consente lo spostamento da smooth. In alternativa, Usa xamarin. Forms [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) e [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) funzioni per un tipo diverso di spostamento di interpolazione di animazione. Queste funzioni di interpolazione causare della seconda lancetta spostare in modo più a scatti &mdash; ritirare un po' prima di spostarla, e quindi leggermente aumenti la destinazione, e un effetto che sfortunatamente non può essere riprodotta nelle schermate illustrate static:

[![](path-data-images/prettyanalogclock-small.png "Tripla screenshot della pagina piuttosto orologio analogico")](path-data-images/prettyanalogclock-large.png#lightbox "tripla screenshot della pagina piuttosto orologio analogico")


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
