---
title: Dati del percorso SVG in SkiaSharp
description: In questo articolo viene illustrato come definire i percorsi di SkiaSharp usando stringhe di testo nel formato Scalable Vector Graphics e viene illustrato quanto descritto con codice di esempio.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: charlespetzold
ms.author: chape
ms.date: 05/24/2017
ms.openlocfilehash: 0453374c59c3b12842b7fb1524cc150329d84b7f
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243974"
---
# <a name="svg-path-data-in-skiasharp"></a>Dati del percorso SVG in SkiaSharp

_Definire i percorsi di utilizzo delle stringhe di testo nel formato Scalable Vector Graphics_

La `SKPath` classe supporta la definizione degli oggetti intero percorso da stringhe di testo in un formato stabilito dalla specifica Scalable Vector Graphics (SVG). Verrà visualizzato più avanti in questo articolo come è possibile rappresentare un percorso completo, ad esempio questo in una stringa di testo:

![](path-data-images/pathdatasample.png "Un percorso di esempio definito con i dati del percorso SVG")

SVG è un linguaggio di programmazione per le pagine web elementi grafici basati su XML. Poiché SVG deve consentire i percorsi essere definita nel markup anziché una serie di chiamate di funzione, il SVG standard include un modo estremamente rapido della specifica di un tracciato intera come una stringa di testo.

All'interno di SkiaSharp, questo formato è detto "SVG percorso-data". Il formato è supportato anche in ambienti programmazione basata su XAML di Windows, tra cui Windows Presentation Foundation e la piattaforma Windows universale, in cui è noto come il [sintassi del percorso di Markup](https://msdn.microsoft.com/library/ms752293%28v=vs.110%29.aspx) o [spostare e disegnare sintassi comandi](/windows/uwp/xaml-platform/move-draw-commands-syntax/). Può inoltre costituire un formato di exchange per le immagini vettoriali, in particolare nei file di testo, ad esempio XML.

SkiaSharp definisce due metodi con le parole `SvgPathData` nei relativi nomi:

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

Il metodo statico [ `ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/) metodo converte una stringa in un `SKPath` oggetto, mentre [ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/) converte un `SKPath` oggetto in una stringa.

Di seguito è una stringa SVG per un cinque punte centrata il punto (0, 0) con un raggio pari a 100:

```csharp
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

Le lettere sono comandi che compilano un `SKPath` oggetto. `M` indica un `MoveTo` chiama, `L` è `LineTo`, e `Z` è `Close` per chiudere un contorno. Ogni coppia di numeri di fornisce una coordinata X e Y di un punto. Si noti che il `L` comando è seguito da più punti separati da virgole. In una serie di coordinate e punti, virgole e gli spazi vuoti vengono considerati in modo identico. Alcuni programmatori preferiscono inserire una virgola tra le coordinate X e Y anziché tra i punti e virgole o spazi sono necessarie solo per evitare ambiguità. Questo è perfettamente valido:

```csharp
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

La sintassi di dati del percorso SVG è formalmente documentata [8.3 sezione della specifica SVG](http://www.w3.org/TR/SVG11/paths.html#PathData). Di seguito è riportato un riepilogo:

## <a name="moveto"></a>**MoveTo**

```csharp
M x y
```

Si inizia un nuovo contorno nel percorso impostando la posizione corrente. Dati del percorso devono iniziare sempre con un `M` comando.

## <a name="lineto"></a>**LineTo**

```csharp
L x y ...
```

Questo comando aggiunge una linea retta (o righe) al percorso e imposta la nuova posizione corrente alla fine dell'ultima riga. È possibile seguire il `L` con più coppie di *x* e *y* coordinate.

## <a name="horizontal-lineto"></a>**LineTo orizzontale**

```csharp
H x ...
```

Questo comando aggiunge una riga orizzontale al percorso e imposta la nuova posizione corrente alla fine della riga. È possibile seguire il `H` comando con più *x* coordinate, ma non ha senso quantità.

## <a name="vertical-line"></a>**Linea verticale**

```csharp
V y ...
```

Questo comando aggiunge una linea verticale al percorso e imposta la nuova posizione corrente alla fine della riga.

## <a name="close"></a>**Chiudi**

```csharp
Z
```

Il `C` comando chiude il contorno aggiungendo una linea retta dalla posizione corrente all'inizio del profilo.

## <a name="arcto"></a>**ArcTo**

Il comando per aggiungere un arco ellittico per la distribuzione è di gran lunga il comando più complesso nella specifica del percorso dati SVG intero. È l'unico comando in cui i numeri possono rappresentare valori diversi da quelli di coordinate:

```csharp
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

Il *rx* e *ry* i parametri sono il raggio orizzontale e verticale dell'ellisse. Il *angolo di rotazione* è in senso orario espresso in gradi.

Impostare il *grandi arco-flag* su 1 per l'arco di grandi dimensioni o su 0 per l'arco di piccole dimensioni.

Impostare il *durante lo sweep flag* su 1 per in senso orario e su 0 per in senso antiorario.

Fino a quando viene disegnata l'arco (*x*, *y*), che diventa la nuova posizione corrente.

## <a name="cubicto"></a>**CubicTo**

```csharp
C x1 y1 x2 y2 x3 y3 ...
```

Questo comando aggiunge una curva di Bézier cubica dalla posizione corrente per (*x3*, *y3*), che diventa la nuova posizione corrente. I punti (*x1*, *y1*) e (*x2*, *y2*) sono punti di controllo.

È possibile specificare più di Bézier da un singolo `C` comando. Il numero di punti deve essere un multiplo di 3.

È anche un comando di curva di Bézier "smooth":

```csharp
S x2 y2 x3 y3 ...
```

Questo comando deve seguire un comando di Bézier regolare (sebbene che non sia strettamente necessario). Il comando di Bézier smooth calcola il primo punto di controllo in modo che sia un riflesso del secondo punto di controllo di Bézier loro reciproca punto precedente. Pertanto, queste tre punti sono collineari, e la connessione tra le due curve di Bézier è uniforme.

## <a name="quadto"></a>**QuadTo**

```csharp
Q x1 y1 x2 y2 ...
```

Per le curve di Bézier quadratiche, il numero di punti deve essere un multiplo di 2. È il punto di controllo (*x1*, *y1*) e il punto finale (e la nuova posizione corrente) è (*x2*, *y2*)

È anche un comando quadratica smooth:

```csharp
T x2 y2 ...
```

Il punto di controllo viene calcolato il punto di controllo della curva quadratica precedente.

Tutti questi comandi sono inoltre disponibili nelle versioni "relative", in cui i punti di coordinate sono relative alla posizione corrente. Questi comandi relativi iniziano con lettere minuscole, ad esempio `c` anziché `C` per la relativa versione del comando di Bézier cubica.

Questo è l'estensione della definizione del percorso dati SVG. Non è possibile per la ripetizione di gruppi di comandi o per l'esecuzione di qualsiasi tipo di calcolo. I comandi per `ConicTo` o altri tipi di specifiche arco non sono disponibili.

Il metodo statico [ `SKPath.ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/) metodo prevede una stringa valida di comandi SVG. Se viene rilevato un errore di sintassi, il metodo restituisce `null`. Che rappresenta l'indicazione di errore solo.

Il [ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/) metodo è utile per ottenere i dati del percorso SVG da un oggetto esistente `SKPath` oggetto per il trasferimento a un altro programma, o per archiviare in un formato di file di testo, ad esempio XML. (Il `ToSvgPathData` (metodo) non è illustrato nell'esempio di codice in questo articolo.) Eseguire *non* prevede `ToSvgPathData` per restituire una stringa corrispondente esattamente le chiamate al metodo che ha creato il percorso. In particolare, sarà possibile osservare che archi vengono convertiti in più `QuadTo` comandi, e come vengono visualizzati nel percorso restituito da `ToSvgPathData`.

Il **Hello di percorso dati** pagina dirompenti la parola "HELLO" utilizzando i dati del percorso SVG. Sia il `SKPath` e `SKPaint` oggetti vengono definiti come campi di [ `PathDataHelloPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) classe:

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

Il percorso che definisce la stringa di testo inizia in corrispondenza del punto (0, 0) nell'angolo superiore sinistro. Ogni lettera è 50 unità di larghezza e altezza 100 unità e lettere sono separate da un altro 25 unità, che indica che l'intero percorso 350 unità wide.

'H' di "Hello" è composta da tre contorni una sola riga, mentre 'E' è di due curve di Bézier cubiche collegate. Si noti che il `C` comando è seguito da sei punti, e due dei punti di controllo dispongono di coordinate Y -10 e 110, li inserisce nell'intervallo delle coordinate Y delle altre lettere. "L" è di due linee collegate, mentre il ' o ' è un'ellisse che viene eseguito il rendering con un `A` comando.

Si noti che il `M` inizia il contorno ultimo comando imposta la posizione per il punto (350, 50), ovvero il centro verticale di sinistra del ' o '. Come indicato dal primo seguente i numeri di `A` comando, l'ellisse ha un raggio orizzontale del 25 e un raggio verticale del 50. Il punto di fine è indicato per l'ultima coppia di numeri di `A` comando, che rappresenta il punto (300, 49.9). Che viene deliberatamente leggermente diverso dal punto di inizio. Se l'endpoint è uguale al punto iniziale, non verrà visualizzato l'arco. Per disegnare un'ellisse completa, è necessario impostare l'endpoint simile a (ma non uguale a) il punto di inizio, oppure è necessario utilizzare due o più `A` comandi, ogni parte dell'ellisse completa.

Si potrebbe voler aggiungere l'istruzione seguente al costruttore della pagina e quindi impostare un punto di interruzione per esaminare la stringa risultante:

```csharp
string str = helloPath.ToSvgPathData();
```

È possibile osservare che l'arco è stata sostituita con una lunga serie di `Q` comandi per un'approssimazione dell'arco Usa curve di Bézier quadratiche a fasi.

Il `PaintSurface` gestore ottiene i limiti di una stretta del percorso, che non include i punti di controllo per il 'E' e ' o ' curve. Le tre trasformazioni spostare il centro del percorso per il punto (0, 0), scalare il percorso alla dimensione dell'area di disegno (ma anche considerando lo spessore del tratto account) e quindi spostare il centro del percorso al centro dell'area di disegno:

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

Il percorso riempie l'area di disegno, è più ragionevole quando viene visualizzato in modalità orizzontale:

[![](path-data-images/pathdatahello-small.png "Schermata della pagina Hello di percorso dati di tripla")](path-data-images/pathdatahello-large.png#lightbox "tripla schermata della pagina Hello di percorso dati")

Il **percorso dati Cat** pagina è simile. Entrambi gli oggetti di percorso e di disegno sono definiti come campi di [ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) classe:

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

L'intestazione di un gatto è un cerchio e qui viene visualizzato con due `A` comandi, ognuno dei quali disegna un semicerchio. Entrambi `A` i comandi per l'intestazione definiscono i raggi orizzontali e verticali pari a 100. Inizia il primo arco (240, 100) e termina in corrispondenza (240, 300), che diventa il punto di inizio per l'arco secondo che termina in (240, 100).

Anche gli occhi vengono sottoposti a rendering con due `A` comandi e come con tag, il secondo `A` comando termina nello stesso punto l'inizio del primo `A` comando. Tuttavia, queste coppie di `A` comandi non si definiscono un'ellisse. La con di ciascun arco è 40 unità e il raggio è 40 unità, il che significa che questi sono archi non semicircles completo.

Il `PaintSurface` gestore esegue le trasformazioni simili dell'esempio precedente, ma imposta una singola `Scale` fattore per mantenere le proporzioni e fornire un margine minimo, in modo baffi del cat non toccano i lati della schermata:

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

Di seguito è riportato il programma in esecuzione in tutti e tre le piattaforme:

[![](path-data-images/pathdatacat-small.png "Schermata triplo della pagina percorso dati Cat")](path-data-images/pathdatacat-large.png#lightbox "tripla schermata della pagina percorso dati Cat")

In genere, quando un `SKPath` oggetto viene definito come un campo, le distribuzioni del percorso devono essere definite nel costruttore o un altro metodo. Quando si utilizzano dati del percorso SVG, tuttavia, si è visto che è possibile specificare il percorso completamente nella definizione del campo.

Il precedente **propriamente orologio analogico** sample nel [ **ruota la trasformazione** ](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md) articolo visualizzato lancette dell'orologio come semplici righe. Il **piuttosto l'orologio analogico** programma seguente sostituisce le righe con `SKPath` gli oggetti definiti come campi di [ `PrettyAnalogClockPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) classe insieme a `SKPaint` oggetti:

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

Gli indicatori di ora e minuto ora sono racchiusi tra aree, in modo da rendere tali mani distinti tra loro, vengono disegnati con un riquadro nero e grigio utilizzando il `handStrokePaint` e `handFillPaint` oggetti.

In precedenza **propriamente orologio analogico** esempio, le poche cerchi contrassegnati le ore e minuti sono stati creati in un ciclo. In questo **piuttosto l'orologio analogico** esempio, viene utilizzato un approccio completamente diverso: i segni di ora e minuto sono linee tratteggiate disegnate con il `minuteMarkPaint` e `hourMarkPaint` oggetti:

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

Il [ **punti e trattini** ](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md) Guida illustrato come utilizzare il `SKPathEffect.CreateDash` metodo per creare una linea tratteggiata. Il primo argomento è un `float` matrice che in genere sono presenti due elementi: il primo elemento è la lunghezza dei trattini e il secondo elemento è il gap tra i trattini. Quando il `StrokeCap` è impostata su `SKStrokeCap.Round`, quindi le estremità del trattino arrotondate allungare in modo efficace la lunghezza dash per lo spessore del tratto su entrambi i lati del trattino. Pertanto, impostando il primo elemento della matrice su 0 consente di creare una linea punteggiata.

La distanza tra questi punti è controllata dal secondo elemento della matrice. Come verrà illustrato più avanti, queste due `SKPaint` gli oggetti vengono utilizzati per disegnare cerchi con un raggio di unità di 90. La circonferenza di questo cerchio viene pertanto 180π, il che significa che i segni di 60 minuti devono essere presente ogni unità 3 π, che corrisponde al valore nel secondo il `float` matrice `minuteMarkPaint`. I segni di dodici ore devono essere visualizzato ogni unità 15π, ovvero il valore del secondo `float` matrice.

Il `PrettyAnalogClockPage` classe ogni 16 millisecondi, di un timer per invalidare l'area di imposta e `PaintSurface` gestore viene chiamato con tale tasso. Le definizioni delle versioni precedenti di `SKPath` e `SKPaint` oggetti consentono molto puliti codice di disegno:

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

Un'operazione particolare viene eseguita con le lancette, tuttavia. Perché l'orologio viene aggiornato ogni 16 millisecondi, di `Millisecond` proprietà del `DateTime` valore potenzialmente può essere usato per animare secondo manualmente uno sweep anziché a uno che consente di spostare in collegamenti discreti dal secondo al secondo. Ma questo codice consente lo spostamento da smooth. Utilizza invece di xamarin. Forms [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) e [ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) animazione interpolazione funzioni per un tipo diverso di spostamento. Queste funzioni di interpolazione causare le lancette spostare in modo più a scatti &mdash; ritirare un poco prima che lo spostamento e quindi leggermente aumenti la destinazione, un effetto che purtroppo non possano essere riprodotti in queste schermate statiche:

[![](path-data-images/prettyanalogclock-small.png "Schermata di triplo della pagina piuttosto l'orologio analogico")](path-data-images/prettyanalogclock-large.png#lightbox "tripla schermata della pagina piuttosto l'orologio analogico")


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
