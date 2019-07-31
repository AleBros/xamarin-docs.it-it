---
title: Trasformazione di traslazione
description: Questo articolo viene esaminato come utilizzare la trasformazione di traslazione da spostare in SkiaSharp grafica nelle applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: b4fae33f7de8c6022b2298c462ea510ec8ed623f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657200"
---
# <a name="the-translate-transform"></a>Trasformazione di traslazione

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Informazioni su come usare la trasformazione di traslazione per spostare elementi grafici di SkiaSharp_

Il tipo più semplice di trasformazione in SkiaSharp è il *tradurre* o *traduzione* trasformare. Questa trasformazione passa gli oggetti grafici nelle direzioni orizzontale e verticale. In un certo senso, la traduzione è la trasformazione non più necessaria poiché è in genere possibile ottenere lo stesso effetto modificando semplicemente le coordinate in uso nella funzione di disegno. Durante il rendering di un percorso, tuttavia, tutte le coordinate sono incapsulate nel percorso, pertanto è molto più facile applicare una trasformazione di traslazione per spostare l'intero percorso.

La traduzione risulta utile anche per l'animazione e per gli effetti di testo semplice:

![](translate-images/translateexample.png "Ombreggiatura del testo, incisione e rilievi con la traduzione")

Il [ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) metodo `SKCanvas` presenta due parametri che determinano gli oggetti di grafica disegnati successivamente da spostare orizzontalmente e verticalmente:

```csharp
public void Translate (Single dx, Single dy)
```

Questi argomenti possono essere negativi. Una seconda [ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) metodo combina i valori di due traduzione in una singola `SKPoint` valore:

```csharp
public void Translate (SKPoint point)
```

Il **accumulati tradurre** pagina della [ **SkiaSharpForms** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) programma di esempio dimostra che più chiamate il `Translate` metodo sono cumulativi. Il [ `AccumulatedTranslatePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) classe visualizza 20 versioni dello stesso rettangolo, a ognuno di essi offset dal rettangolo precedente quanto basta in modo che il ridimensionamento diagonale. Di seguito è riportato il `PaintSurface` gestore dell'evento:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Color = SKColors.Black;
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = 3;

        int rectangleCount = 20;
        SKRect rect = new SKRect(0, 0, 250, 250);
        float xTranslate = (info.Width - rect.Width) / (rectangleCount - 1);
        float yTranslate = (info.Height - rect.Height) / (rectangleCount - 1);

        for (int i = 0; i < rectangleCount; i++)
        {
            canvas.DrawRect(rect, strokePaint);
            canvas.Translate(xTranslate, yTranslate);
        }
    }
}
```

I rettangoli successivi propagazione della pagina:

[![](translate-images/accumulatedtranslate-small.png "Tripla screenshot della pagina accumulati tradurre")](translate-images/accumulatedtranslate-large.png#lightbox "tripla screenshot della pagina accumulati tradurre")

Se sono i fattori translation accumulata `dx` e `dy`, ed è il punto specificato in una funzione di disegno (`x`, `y`), quindi l'oggetto grafico viene eseguito il rendering in corrispondenza del punto (`x'`, `y'`), dove:

x' = x + dx

y' = y + dy

Questi sono conosciuti come le *trasforma le formule* per la conversione. I valori predefiniti `dx` e `dy` di un nuovo `SKCanvas` sono pari a 0.

È comune per utilizzare la trasformazione di traslazione per gli effetti di ombreggiatura e tecniche simili, come le **tradurre gli effetti di testo** dimostra di pagina. Ecco la parte interessata il `PaintSurface` gestore nel [ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) classe:

```csharp
float textSize = 150;

using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = textSize;
    textPaint.FakeBoldText = true;

    float x = 10;
    float y = textSize;

    // Shadow
    canvas.Translate(10, 10);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("SHADOW", x, y, textPaint);
    canvas.Translate(-10, -10);
    textPaint.Color = SKColors.Pink;
    canvas.DrawText("SHADOW", x, y, textPaint);

    y += 2 * textSize;

    // Engrave
    canvas.Translate(-5, -5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("ENGRAVE", x, y, textPaint);
    canvas.ResetMatrix();
    textPaint.Color = SKColors.White;
    canvas.DrawText("ENGRAVE", x, y, textPaint);

    y += 2 * textSize;

    // Emboss
    canvas.Save();
    canvas.Translate(5, 5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("EMBOSS", x, y, textPaint);
    canvas.Restore();
    textPaint.Color = SKColors.White;
    canvas.DrawText("EMBOSS", x, y, textPaint);
}
```

In ognuna delle tre esempi `Translate` viene chiamato per la visualizzazione del testo per eseguire l'offset dalla posizione specificata dal `x` e `y` variabili. Quindi, il testo viene visualizzato nuovamente in un altro colore senza effetti traduzione:

[![](translate-images/translatetexteffects-small.png "Tripla screenshot della pagina di tradurre gli effetti di testo")](translate-images/translatetexteffects-large.png#lightbox "tripla screenshot della pagina di tradurre gli effetti di testo")

Ognuna delle tre esempi viene illustrato un modo diverso la negazione di `Translate` chiamare:

Nel primo esempio consente di chiamare semplicemente `Translate` nuovamente, ma con i valori negativi. Poiché il `Translate` chiamate sono cumulative, questa sequenza di chiamate Ripristina semplicemente la traduzione totale sui valori predefiniti pari a zero.

Il secondo esempio viene chiamato [ `ResetMatrix` ](xref:SkiaSharp.SKCanvas.ResetMatrix). In questo modo tutte le trasformazioni tornare al relativo stato predefinito.

Nel terzo esempio Salva lo stato del `SKCanvas` oggetto con una chiamata a [ `Save` ](xref:SkiaSharp.SKCanvas.Save) e quindi Ripristina lo stato con una chiamata a [ `Restore` ](xref:SkiaSharp.SKCanvas.Restore). Questo è il modo più versatile per manipolare le trasformazioni per una serie di operazioni di disegno. Queste `Save` e`Restore` chiamate funzionano come uno stack: È possibile chiamare `Save` più volte, quindi chiamare `Restore` in sequenza inversa per tornare agli stati precedenti. Il `Save` metodo restituisce un valore integer, ed è possibile passare tale integer al [ `RestoreToCount` ](xref:SkiaSharp.SKCanvas.RestoreToCount*) per chiamare in modo efficace `Restore` più volte. Il [ `SaveCount` ](xref:SkiaSharp.SKCanvas.SaveCount) proprietà restituisce il numero di stati salvati attualmente nello stack.

È anche possibile usare la [ `SKAutoCanvasRestore` ](xref:SkiaSharp.SKAutoCanvasRestore) classe per il ripristino dello stato dell'area di disegno. Il costruttore di questa classe è destinato a essere chiamato in un `using` istruzione; l'area di disegno automaticamente ripristinato lo stato in fondo il `using` blocco. 

Tuttavia, non devi preoccuparti trasformazioni riportando da un'unica chiamata del `PaintSurface` gestore a quella successiva. Ogni nuova chiamata a `PaintSurface` offre una nuova `SKCanvas` oggetto con trasformazioni predefinite.

Un altro uso comune del `Translate` transform è per il rendering di un oggetto visivo che è stato originariamente creato usando le coordinate che sono utili per il disegno. Potrebbe ad esempio, si desidera specificare le coordinate per un orologio analogico con un centro in corrispondenza del punto (0, 0). È possibile quindi usare le trasformazioni per visualizzare l'orologio in cui si desidera. Questa tecnica è dimostrata nel [**Hendecagram matrice**] pagina. Il [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs) classe inizia creando un `SKPath` oggetto per una stella a 11 punte. Il `HendecagramPath` oggetto viene definito come pubblici, statici e sola lettura in modo che sia accessibile da altri programmi di dimostrazione. Viene creato in un costruttore statico:

```csharp
public class HendecagramArrayPage : ContentPage
{
    ...
    public static readonly SKPath HendecagramPath;

    static HendecagramArrayPage()
    {
        // Create 11-pointed star
        HendecagramPath = new SKPath();
        for (int i = 0; i < 11; i++)
        {
            double angle = 5 * i * 2 * Math.PI / 11;
            SKPoint pt = new SKPoint(100 * (float)Math.Sin(angle),
                                    -100 * (float)Math.Cos(angle));
            if (i == 0)
            {
                HendecagramPath.MoveTo(pt);
            }
            else
            {
                HendecagramPath.LineTo(pt);
            }
        }
        HendecagramPath.Close();
    }
}
```

Se il centro della stella è il punto (0, 0), tutti i punti della stella sono in un cerchio circostanti tale punto. Ogni punto è una combinazione di valori seno e il coseno di un angolo che aumenta di 5/11ths di 360 gradi. (È anche possibile creare un grafico 11 punte aumentando l'angolo per 2/11, / 11ths 3 o 4/dall'11 del cerchio.) Il raggio del cerchio che viene impostato come 100.

Se questo percorso viene eseguito il rendering senza eventuali trasformazioni, il centro verrà posizionato nell'angolo superiore sinistro del `SKCanvas`e solo un quarto di esso sarà visibile. Il `PaintSurface` gestore `HendecagramPage` Usa invece `Translate` per riquadro area di lavoro con più copie della stella, ciascuno di essi colorato in modo casuale:

```csharp
public class HendecagramArrayPage : ContentPage
{
    Random random = new Random();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            for (int x = 100; x < info.Width + 100; x += 200)
                for (int y = 100; y < info.Height + 100; y += 200)
                {
                    // Set random color
                    byte[] bytes = new byte[3];
                    random.NextBytes(bytes);
                    paint.Color = new SKColor(bytes[0], bytes[1], bytes[2]);

                    // Display the hendecagram
                    canvas.Save();
                    canvas.Translate(x, y);
                    canvas.DrawPath(HendecagramPath, paint);
                    canvas.Restore();
                }
        }
    }
}

```

Ecco il risultato:

[![](translate-images/hendecagramarray-small.png "Tripla screenshot della pagina di matrice Hendecagram")](translate-images/hendecagramarray-large.png#lightbox "tripla screenshot della pagina Hendecagram matrice")

Le animazioni comportano spesso trasformazioni. Il **animazione Hendecagram** pagina non si sposta la stella a punte 11 in un cerchio. Il [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) classe inizia con alcuni campi ed esegue l'override del `OnAppearing` e `OnDisappearing` metodi per avviare e arrestare un timer di xamarin. Forms:

```csharp
public class HendecagramAnimationPage : ContentPage
{
    const double cycleTime = 5000;      // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float angle;

    public HendecagramAnimationPage()
    {
        Title = "Hedecagram Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            angle = (float)(360 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

Il `angle` campo viene animato da 0 gradi a 360 gradi ogni 5 secondi. Il `PaintSurface` gestore Usa la `angle` proprietà in due modi: per specificare la tonalità del colore nel `SKColor.FromHsl` metodo e come argomento per il `Math.Sin` e `Math.Cos` metodi per regolare la posizione della stella:

```csharp
public class HendecagramAnimationPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.Translate(info.Width / 2, info.Height / 2);
        float radius = (float)Math.Min(info.Width, info.Height) / 2 - 100;

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColor.FromHsl(angle, 100, 50);

            float x = radius * (float)Math.Sin(Math.PI * angle / 180);
            float y = -radius * (float)Math.Cos(Math.PI * angle / 180);
            canvas.Translate(x, y);
            canvas.DrawPath(HendecagramPage.HendecagramPath, paint);
        }
    }
}
```

Il `PaintSurface` chiamate del gestore di `Translate` metodo due volte, prima di tutto per eseguire la conversione al centro dell'area di disegno e quindi in cui tradurre la circonferenza del cerchio incentrato (0, 0). Il raggio del cerchio è impostato per essere più grande possibile garantendo nel contempo la stella entro i confini della pagina:

[![](translate-images/hendecagramanimation-small.png "Tripla screenshot della pagina di animazione Hendecagram")](translate-images/hendecagramanimation-large.png#lightbox "tripla screenshot della pagina di animazione Hendecagram")

Si noti che la stella manterrà l'orientamento stesso mentre ruota intorno al centro della pagina. Non è possibile ruotare affatto. Questo è un processo per una trasformazione di rotazione.


## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
