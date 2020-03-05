---
title: Trasformazione di traslazione
description: Questo articolo viene esaminato come utilizzare la trasformazione di traslazione da spostare in SkiaSharp grafica nelle applicazioni xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: f1efd7610b32e6a3903d34fc2f8b5a6e20c9da8a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291786"
---
# <a name="the-translate-transform"></a>Trasformazione di traslazione

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Informazioni su come usare la trasformazione translate per spostare la grafica SkiaSharp_

*Il tipo* di trasformazione più semplice in SkiaSharp è la trasformazione di *conversione o traduzione* . Questa trasformazione passa gli oggetti grafici nelle direzioni orizzontale e verticale. In un certo senso, la traduzione è la trasformazione non più necessaria poiché è in genere possibile ottenere lo stesso effetto modificando semplicemente le coordinate in uso nella funzione di disegno. Durante il rendering di un percorso, tuttavia, tutte le coordinate sono incapsulate nel percorso, pertanto è molto più facile applicare una trasformazione di traslazione per spostare l'intero percorso.

La traduzione risulta utile anche per l'animazione e per gli effetti di testo semplice:

![](translate-images/translateexample.png "Text shadow, engraving, and embossing with translation")

Il metodo [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) in `SKCanvas` include due parametri che comportano lo spostamento orizzontale e verticale degli oggetti grafici disegnati successivamente:

```csharp
public void Translate (Single dx, Single dy)
```

Questi argomenti possono essere negativi. Un secondo [`Translate`](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) metodo combina i due valori di conversione in un singolo valore `SKPoint`:

```csharp
public void Translate (SKPoint point)
```

La pagina di **conversione accumulata** del programma di esempio [**SkiaSharpForms**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) dimostra che più chiamate del metodo `Translate` sono cumulative. La classe [`AccumulatedTranslatePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) Visualizza 20 versioni dello stesso rettangolo, ciascuna con un offset rispetto al rettangolo precedente, in modo da estendersi lungo la diagonale. Ecco il gestore dell'evento `PaintSurface`:

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

[![](translate-images/accumulatedtranslate-small.png "Triple screenshot of the Accumulated Translate page")](translate-images/accumulatedtranslate-large.png#lightbox "Triple screenshot of the Accumulated Translate page")

Se i fattori di traduzione accumulati sono `dx` e `dy`e il punto specificato in una funzione di disegno è (`x`, `y`), viene eseguito il rendering dell'oggetto grafico in corrispondenza del punto (`x'`, `y'`), in cui:

x' = x + dx

y' = y + dy

Queste sono note come *formule di trasformazione* per la conversione. I valori predefiniti di `dx` e `dy` per un nuovo `SKCanvas` sono 0.

È comune utilizzare la trasformazione Traduci per gli effetti di ombreggiatura e tecniche simili, come illustrato nella pagina **tradurre gli effetti di testo** . Di seguito è illustrata la parte pertinente del gestore `PaintSurface` nella classe [`TranslateTextEffectsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) :

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

In ognuno dei tre esempi, viene chiamato `Translate` per visualizzare il testo in modo da compensarlo dalla posizione specificata dalle variabili `x` e `y`. Quindi, il testo viene visualizzato nuovamente in un altro colore senza effetti traduzione:

[![](translate-images/translatetexteffects-small.png "Triple screenshot of the Translate Text Effects page")](translate-images/translatetexteffects-large.png#lightbox "Triple screenshot of the Translate Text Effects page")

Ognuno dei tre esempi mostra un modo diverso per negare la chiamata di `Translate`:

Nel primo esempio viene semplicemente chiamato di nuovo `Translate` ma con valori negativi. Poiché le chiamate `Translate` sono cumulative, questa sequenza di chiamate ripristina semplicemente la conversione totale ai valori predefiniti pari a zero.

Nel secondo esempio viene chiamato [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix). In questo modo tutte le trasformazioni tornare al relativo stato predefinito.

Nel terzo esempio viene salvato lo stato dell'oggetto `SKCanvas` con una chiamata a [`Save`](xref:SkiaSharp.SKCanvas.Save) e quindi viene ripristinato lo stato con una chiamata a [`Restore`](xref:SkiaSharp.SKCanvas.Restore). Questo è il modo più versatile per manipolare le trasformazioni per una serie di operazioni di disegno. Queste `Save` e `Restore` chiamano funzione come uno stack: è possibile chiamare `Save` più volte, quindi chiamare `Restore` in sequenza inversa per tornare agli stati precedenti. Il metodo `Save` restituisce un valore integer ed è possibile passare tale integer a [`RestoreToCount`](xref:SkiaSharp.SKCanvas.RestoreToCount*) per chiamare efficacemente `Restore` più volte. La proprietà [`SaveCount`](xref:SkiaSharp.SKCanvas.SaveCount) restituisce il numero di stati attualmente salvati nello stack.

È anche possibile usare la classe [`SKAutoCanvasRestore`](xref:SkiaSharp.SKAutoCanvasRestore) per ripristinare lo stato dell'area di disegno. Il costruttore di questa classe è destinato a essere chiamato in un'istruzione `using`; lo stato dell'area di disegno viene ripristinato automaticamente alla fine del blocco `using`.

Tuttavia, non è necessario preoccuparsi delle trasformazioni che trasportano da una chiamata del gestore `PaintSurface` al successivo. Ogni nuova chiamata a `PaintSurface` recapita un oggetto `SKCanvas` aggiornato con le trasformazioni predefinite.

Un altro uso comune della trasformazione `Translate` è per il rendering di un oggetto visivo creato originariamente usando le coordinate che risultano utili per il disegno. Potrebbe ad esempio, si desidera specificare le coordinate per un orologio analogico con un centro in corrispondenza del punto (0, 0). È possibile quindi usare le trasformazioni per visualizzare l'orologio in cui si desidera. Questa tecnica è illustrata nella pagina [**Hendecagram array**]. La classe [`HendecagramArrayPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramArrayPage.cs) inizia creando un oggetto `SKPath` per una stella a 11 punte. L'oggetto `HendecagramPath` è definito come Public, static e Read-Only, in modo che sia possibile accedervi da altri programmi dimostrativi. Viene creato in un costruttore statico:

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

Se il centro della stella è il punto (0, 0), tutti i punti della stella sono in un cerchio circostanti tale punto. Ogni punto è una combinazione di valori seno e il coseno di un angolo che aumenta di 5/11ths di 360 gradi. È anche possibile creare una stella a 11 punte aumentando l'angolo di 2/11, 3/11 o 4/11 del cerchio. Il raggio di tale cerchio è impostato su 100.

Se questo percorso viene sottoposto a rendering senza alcuna trasformazione, il centro verrà posizionato nell'angolo superiore sinistro del `SKCanvas`e solo un trimestre sarà visibile. Il gestore `PaintSurface` di `HendecagramPage` usa invece `Translate` per affiancare l'area di disegno con più copie della stella, ognuna con un colore casuale:

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

Il risultato è il seguente:

[![](translate-images/hendecagramarray-small.png "Triple screenshot of the Hendecagram Array page")](translate-images/hendecagramarray-large.png#lightbox "Triple screenshot of the Hendecagram Array page")

Le animazioni comportano spesso trasformazioni. La pagina di **animazione Hendecagram** sposta la stella a 11 punte intorno a un cerchio. La classe [`HendecagramAnimationPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) inizia con alcuni campi ed esegue l'override dei metodi `OnAppearing` e `OnDisappearing` per avviare e arrestare un timer Novell. Forms:

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

Il campo `angle` viene animato da 0 gradi a 360 gradi ogni 5 secondi. Il gestore `PaintSurface` usa la proprietà `angle` in due modi: per specificare la tonalità del colore nel metodo `SKColor.FromHsl` e come argomento per i metodi `Math.Sin` e `Math.Cos` per gestire la posizione della stella:

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

Il gestore di `PaintSurface` chiama due volte il metodo `Translate`, prima per tradurlo nel centro dell'area di disegno e quindi per tradurlo nella circonferenza di un cerchio centrato (0,0). Il raggio del cerchio è impostato per essere più grande possibile garantendo nel contempo la stella entro i confini della pagina:

[![](translate-images/hendecagramanimation-small.png "Triple screenshot of the Hendecagram Animation page")](translate-images/hendecagramanimation-large.png#lightbox "Triple screenshot of the Hendecagram Animation page")

Si noti che la stella manterrà l'orientamento stesso mentre ruota intorno al centro della pagina. Non è possibile ruotare affatto. Questo è un processo per una trasformazione di rotazione.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
