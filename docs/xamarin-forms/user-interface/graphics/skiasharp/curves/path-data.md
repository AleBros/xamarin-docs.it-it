---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 680c924280f8f3a6080b2fcc1968ecaf308f33a0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138060"
---
# <a name="svg-path-data-in-skiasharp"></a>Dati del percorso SVG in SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Definire i percorsi usando stringhe di testo nel formato di grafica vettoriale scalabile_

La [`SKPath`](xref:SkiaSharp.SKPath) classe supporta la definizione di interi oggetti percorso dalle stringhe di testo in un formato stabilito dalla specifica svg (Scalable Vector Graphics). Più avanti in questo articolo viene illustrato come è possibile rappresentare un percorso intero, ad esempio in una stringa di testo:

![](path-data-images/pathdatasample.png "A sample path defined with SVG path data")

SVG è un linguaggio di programmazione grafica basato su XML per le pagine Web. Poiché SVG deve consentire la definizione di percorsi nel markup anziché una serie di chiamate di funzione, lo standard SVG include un modo estremamente conciso di specificare un intero percorso grafico come una stringa di testo.

All'interno di SkiaSharp, questo formato è denominato "SVG Path-data". Il formato è supportato anche negli ambienti di programmazione basati su XAML di Windows, tra cui il Windows Presentation Foundation e il piattaforma UWP (Universal Windows Platform), in cui è noto come [sintassi di markup del percorso](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax) o la sintassi dei [comandi di spostamento e di traccia](/windows/uwp/xaml-platform/move-draw-commands-syntax/). Può anche fungere da formato di scambio per le immagini di grafica vettoriale, in particolare nei file basati su testo, ad esempio XML.

La [`SKPath`](xref:SkiaSharp.SKPath) classe definisce due metodi con le parole `SvgPathData` nei rispettivi nomi:

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

Il [`ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) metodo statico converte una stringa in un `SKPath` oggetto, mentre [`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData) converte un `SKPath` oggetto in una stringa.

Ecco una stringa SVG per una stella a cinque punte centrata sul punto (0, 0) con un raggio di 100:

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

Le lettere sono comandi che compilano un `SKPath` oggetto: `M` indica una `MoveTo` chiamata, `L` è `LineTo` e deve `Z` `Close` chiudere un contorno. Ogni coppia di numeri fornisce una coordinata X e Y di un punto. Si noti che il `L` comando è seguito da più punti separati da virgole. In una serie di coordinate e punti, le virgole e gli spazi vuoti vengono trattati in modo identico. Alcuni programmatori preferiscono inserire virgole tra le coordinate X e Y anziché tra i punti, ma le virgole o gli spazi sono necessari solo per evitare ambiguità. Questo è perfettamente valido:

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

La sintassi dei dati del percorso SVG è documentata formalmente nella [sezione 8,3 della specifica SVG](https://www.w3.org/TR/SVG11/paths.html#PathData). Ecco un riepilogo:

## <a name="moveto"></a>**MoveTo**

```
M x y
```

Viene avviato un nuovo contorno nel percorso impostando la posizione corrente. I dati del percorso devono sempre iniziare con un `M` comando.

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

Questo comando aggiunge una linea retta (o linee) al percorso e imposta la nuova posizione corrente alla fine dell'ultima riga. È possibile seguire il `L` comando con più coppie di coordinate *x* e *y* .

## <a name="horizontal-lineto"></a>**LineTo orizzontale**

```
H x ...
```

Questo comando aggiunge una linea orizzontale al percorso e imposta la nuova posizione corrente alla fine della riga. È possibile seguire il `H` comando con più coordinate *x* , ma non è molto utile.

## <a name="vertical-line"></a>**Linea verticale**

```
V y ...
```

Questo comando aggiunge una linea verticale al percorso e imposta la nuova posizione corrente alla fine della riga.

## <a name="close"></a>**Close**

```
Z
```

Il `C` comando chiude il contorno aggiungendo una linea retta dalla posizione corrente fino all'inizio del contorno.

## <a name="arcto"></a>**ArcTo**

Il comando per aggiungere un arco ellittico al contorno è di gran lunga il comando più complesso nell'intero percorso SVG-specifica dei dati. Si tratta dell'unico comando in cui i numeri possono rappresentare elementi diversi dai valori delle coordinate:

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

I parametri *RX* e *ry* sono i raggi orizzontali e verticali dell'ellisse. L' *angolo di rotazione* è in senso orario, in gradi.

Impostare il *flag large-Arc* su 1 per l'arco grande o su 0 per l'arco di piccole dimensioni.

Impostare *Sweep-flag* su 1 per in senso orario e su 0 per il contatore in senso antiorario.

L'arco viene disegnato fino al punto (*x*, *y*), che diventa la nuova posizione corrente.

## <a name="cubicto"></a>**CubicTo**

```
C x1 y1 x2 y2 x3 y3 ...
```

Questo comando aggiunge una curva di Bézier cubica dalla posizione corrente a (*X3*, *Y3*), che diventa la nuova posizione corrente. I punti (*X1*, *Y1*) e (*X2*, *Y2*) sono punti di controllo.

È possibile specificare più curve di Bézier con un unico `C` comando. Il numero di punti deve essere un multiplo di 3.

Esiste anche un comando "Smooth" della curva di Bézier:

```
S x2 y2 x3 y3 ...
```

Questo comando deve seguire un normale comando di Bézier (anche se non è strettamente obbligatorio). Il comando Smooth Bézier calcola il primo punto di controllo in modo che sia una reflection del secondo punto di controllo della Bézier precedente intorno al punto reciproco. Questi tre punti sono quindi colineari e la connessione tra le due curve di Bézier è uniforme.

## <a name="quadto"></a>**QuadTo**

```
Q x1 y1 x2 y2 ...
```

Per le curve di Bézier quadratiche, il numero di punti deve essere un multiplo di 2. Il punto di controllo è (*X1*, *Y1*), il punto finale (e la nuova posizione corrente) è (*X2*, *Y2*)

È anche disponibile un comando curva quadratica uniforme:

```
T x2 y2 ...
```

Il punto di controllo viene calcolato in base al punto di controllo della curva quadratica precedente.

Tutti questi comandi sono disponibili anche nelle versioni "relative", in cui i punti delle coordinate sono relativi alla posizione corrente. Questi comandi relativi iniziano con lettere minuscole, ad esempio `c` anziché `C` per la versione relativa del comando cubico di Bézier.

Si tratta dell'extent del percorso SVG, ovvero la definizione dei dati. Non è disponibile alcuna funzionalità per la ripetizione di gruppi di comandi o per l'esecuzione di qualsiasi tipo di calcolo. I comandi per `ConicTo` o gli altri tipi di specifiche di arco non sono disponibili.

Il [`SKPath.ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) metodo statico prevede una stringa valida di comandi svg. Se viene rilevato un errore di sintassi, il metodo restituisce `null` . Questa è l'unica indicazione di errore.

Il [`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData) metodo è utile per ottenere i dati del percorso svg da un oggetto esistente da `SKPath` trasferire a un altro programma o per archiviare in un formato di file basato su testo, ad esempio XML. Il `ToSvgPathData` metodo non è illustrato nel codice di esempio in questo articolo. *Non* è previsto che venga `ToSvgPathData` restituita una stringa corrispondente esattamente alle chiamate al metodo che hanno creato il percorso. In particolare, si noterà che gli archi vengono convertiti in più `QuadTo` comandi e questo è il modo in cui vengono visualizzati nei dati del percorso restituiti da `ToSvgPathData` .

La pagina **Hello Data Path** scrive la parola "Hello" con i dati del percorso svg. Entrambi gli `SKPath` `SKPaint` oggetti e sono definiti come campi nella [`PathDataHelloPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) classe:

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

Il percorso che definisce la stringa di testo inizia nell'angolo superiore sinistro del punto (0,0). Ogni lettera è 50 unità di larghezza e 100 unità di misura e le lettere sono separate da altre 25 unità, il che significa che l'intero percorso è 350 unità di larghezza.

Il ' H ' di "Hello" è costituito da contorni di riga 3 1, mentre là È è due curve di Bézier cubiche connesse. Si noti che il `C` comando è seguito da sei punti, mentre due dei punti di controllo hanno le coordinate y-10 e 110, che le inserisce al di fuori dell'intervallo delle coordinate y delle altre lettere. Il valore di ' L'è due linee connesse, mentre ' O ' è un'ellisse di cui viene eseguito il rendering con un `A` comando.

Si noti che il `M` comando che inizia l'ultimo contorno imposta la posizione sul punto (350, 50), ovvero il centro verticale del lato sinistro di ' O '. Come indicato dai primi numeri che seguono il `A` comando, l'ellisse ha un raggio orizzontale di 25 e un raggio verticale di 50. Il punto finale è indicato dall'ultima coppia di numeri nel `A` comando, che rappresenta il punto (300, 49,9). Questo è volutamente leggermente diverso dal punto di partenza. Se l'endpoint è impostato in modo uguale al punto iniziale, non verrà eseguito il rendering dell'arco. Per creare un'ellisse completa, è necessario impostare l'endpoint vicino a (ma non uguale a) come punto iniziale oppure è necessario usare due o più `A` comandi, ognuno per parte dell'ellisse completa.

Potrebbe essere necessario aggiungere l'istruzione seguente al costruttore della pagina, quindi impostare un punto di interruzione per esaminare la stringa risultante:

```csharp
string str = helloPath.ToSvgPathData();
```

Si noterà che l'arco è stato sostituito da una serie di comandi lunghi `Q` per un'approssimazione a fasi dell'arco usando curve di Bézier quadratiche.

Il `PaintSurface` gestore ottiene i limiti stretti del percorso, che non include i punti di controllo per le curve ' è è O '. Le tre trasformazioni spostano il centro del percorso al punto (0, 0), ridimensionano il percorso alla dimensione dell'area di disegno (ma anche prendendo in considerazione la larghezza del tratto), quindi spostano il centro del tracciato al centro dell'area di disegno:

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

Il percorso riempie l'area di disegno, che sembra più ragionevole quando viene visualizzato in modalità orizzontale:

[![](path-data-images/pathdatahello-small.png "Triple screenshot of the Path Data Hello page")](path-data-images/pathdatahello-large.png#lightbox "Triple screenshot of the Path Data Hello page")

La pagina cat per i **dati del percorso** è simile. Gli oggetti Path e Paint sono entrambi definiti come campi nella [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe:

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

Il capo di un gatto è un cerchio e qui viene eseguito il rendering con due `A` comandi, ciascuno dei quali disegna un semicerchio. Entrambi `A` i comandi per la testa definiscono i raggi orizzontali e verticali di 100. Il primo arco inizia a (240, 100) e termina in corrispondenza di (240, 300), che diventa il punto iniziale per il secondo arco che termina in corrispondenza di (240, 100).

Anche i due occhi vengono sottoposti a rendering con due `A` comandi e, come per la testa del gatto, il secondo `A` comando termina nello stesso punto dell'inizio del primo `A` comando. Tuttavia, queste coppie di `A` comandi non definiscono un'ellisse. Il con di ogni arco è pari a 40 unità e il raggio è anche 40 unità, il che significa che questi archi non sono semicerchio completi.

Il `PaintSurface` gestore esegue trasformazioni simili a quelle dell'esempio precedente, ma imposta un singolo `Scale` fattore per mantenere le proporzioni e fornire un piccolo margine, in modo che i baffi del gatto non tocchino i lati della schermata:

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

[![](path-data-images/pathdatacat-small.png "Triple screenshot of the Path Data Cat page")](path-data-images/pathdatacat-large.png#lightbox "Triple screenshot of the Path Data Cat page")

In genere, quando un `SKPath` oggetto viene definito come campo, i contorni del percorso devono essere definiti nel costruttore o in un altro metodo. Quando si usano i dati del percorso SVG, tuttavia, si è notato che il percorso può essere specificato interamente nella definizione del campo.

L'esempio precedente di **Clock analogo a Ugly** nell'articolo [**ruota trasformazione**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md) Visualizza le lancette del clock come linee semplici. Il **grazioso programma di clock analogico** seguente sostituisce le righe con `SKPath` gli oggetti definiti come campi nella [`PrettyAnalogClockPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) classe insieme agli `SKPaint` oggetti:

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

Le lancette ora e minuto hanno ora aree racchiuse. Per distinguerle tra loro, vengono disegnate con un contorno nero e un riempimento grigio usando gli `handStrokePaint` `handFillPaint` oggetti e.

Nell'esempio precedente di **Clock analogico** , i piccoli cerchi che contrassegnavano le ore e i minuti venivano disegnati in un ciclo. In questo esempio di **Clock analogo** viene utilizzato un approccio completamente diverso: i contrassegni di ora e minuto sono linee tratteggiate disegnate con gli `minuteMarkPaint` `hourMarkPaint` oggetti e:

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

L'articolo [**punti e trattini**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md) ha illustrato come usare il [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash*) metodo per creare una linea tratteggiata. Il primo argomento è una `float` matrice che in genere contiene due elementi: il primo elemento è la lunghezza dei trattini e il secondo elemento è il gap tra i trattini. Quando la `StrokeCap` proprietà è impostata su `SKStrokeCap.Round` , le estremità arrotondate del trattino estendendo in modo efficace la lunghezza del trattino per la larghezza del tratto su entrambi i lati del trattino. Impostando quindi il primo elemento della matrice su 0, viene creata una linea tratteggiata.

La distanza tra questi punti è regolata dal secondo elemento della matrice. Come si vedrà a breve, questi due `SKPaint` oggetti vengono usati per creare cerchi con un raggio di 90 unità. La circonferenza di questo cerchio è quindi 180 π, il che significa che i segni di 60 minuti devono essere visualizzati ogni unità 3π, che è il secondo valore nella `float` matrice in `minuteMarkPaint` . I contrassegni di 12 ore devono essere visualizzati ogni unità 15π, ovvero il valore nella seconda `float` matrice.

La `PrettyAnalogClockPage` classe imposta un timer per invalidare la superficie ogni 16 millisecondi e il `PaintSurface` gestore viene chiamato a tale velocità. Le definizioni precedenti degli `SKPath` oggetti e `SKPaint` consentono un codice di disegno molto pulito:

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

Tuttavia, una cosa speciale viene eseguita con la seconda mano. Poiché il clock viene aggiornato ogni 16 millisecondi, la `Millisecond` proprietà del `DateTime` valore può essere potenzialmente utilizzata per animare un secondo Sweep anziché uno che si sposta in salti discreti dal secondo al secondo. Questo codice, tuttavia, non consente lo spostamento in modo uniforme. USA invece le funzioni di Xamarin.Forms [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) interpolazione dell'animazione e per un tipo di spostamento diverso. Queste funzioni di interpolazione fanno sì che la seconda mano venga spostata in una modalità jerkier &mdash; , ritirando leggermente prima che venga spostata e quindi leggermente più volte alla sua destinazione, un effetto che purtroppo non può essere riprodotto in queste schermate statiche:

[![](path-data-images/prettyanalogclock-small.png "Triple screenshot of the Pretty Analog Clock page")](path-data-images/prettyanalogclock-large.png#lightbox "Triple screenshot of the Pretty Analog Clock page")

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
