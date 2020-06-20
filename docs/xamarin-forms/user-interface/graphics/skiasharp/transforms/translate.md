---
title: Trasformazione di traslazione
description: Questo articolo esamina come usare la trasformazione translate per spostare la grafica SkiaSharp nelle Xamarin.Forms applicazioni e illustra questa operazione con il codice di esempio.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0eb3b4a6b37d59363984c9248cc39de91a6819e0
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138255"
---
# <a name="the-translate-transform"></a>Trasformazione di traslazione

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Informazioni su come usare la trasformazione translate per spostare la grafica SkiaSharp_

*Il tipo* di trasformazione più semplice in SkiaSharp è la trasformazione di *conversione o traduzione* . Questa trasformazione sposta gli oggetti grafici nelle direzioni orizzontale e verticale. In un certo senso, la traduzione è la trasformazione più superflua, in quanto in genere è possibile ottenere lo stesso risultato modificando semplicemente le coordinate usate nella funzione Drawing. Quando si esegue il rendering di un percorso, tuttavia, tutte le coordinate sono incapsulate nel percorso, quindi è molto più semplice applicare una trasformazione translate per spostare l'intero percorso.

La traduzione è utile anche per l'animazione e per gli effetti di testo semplici:

![](translate-images/translateexample.png "Text shadow, engraving, and embossing with translation")

Il [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) metodo in `SKCanvas` ha due parametri che provocano lo spostamento in orizzontale e in verticale degli oggetti grafici disegnati in un secondo momento:

```csharp
public void Translate (Single dx, Single dy)
```

Questi argomenti possono essere negativi. Un secondo [`Translate`](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) metodo combina i due valori di conversione in un singolo `SKPoint` valore:

```csharp
public void Translate (SKPoint point)
```

La pagina di **conversione accumulata** del programma di esempio [**SkiaSharpForms**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) dimostra che più chiamate del `Translate` metodo sono cumulative. La [`AccumulatedTranslatePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) classe Visualizza 20 versioni dello stesso rettangolo, ciascuna con un offset rispetto al rettangolo precedente, in modo da estendersi lungo la diagonale. Ecco il `PaintSurface` gestore dell'evento:

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

I rettangoli successivi comprimano la pagina:

[![](translate-images/accumulatedtranslate-small.png "Triple screenshot of the Accumulated Translate page")](translate-images/accumulatedtranslate-large.png#lightbox "Triple screenshot of the Accumulated Translate page")

Se i fattori di traduzione accumulati sono `dx` e e `dy` il punto specificato in una funzione di disegno è ( `x` , `y` ), viene eseguito il rendering dell'oggetto grafico in corrispondenza del punto ( `x'` , `y'` ), dove:

x ' = x + DX

y ' = y + dy

Queste sono note come *formule di trasformazione* per la conversione. I valori predefiniti di `dx` e `dy` per un nuovo `SKCanvas` sono 0.

È comune utilizzare la trasformazione Traduci per gli effetti di ombreggiatura e tecniche simili, come illustrato nella pagina **tradurre gli effetti di testo** . Di seguito è illustrata la parte pertinente del `PaintSurface` gestore nella [`TranslateTextEffectsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) classe:

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

In ognuno dei tre esempi, `Translate` viene chiamato il metodo per visualizzare il testo per compensarlo dalla posizione specificata dalle `x` `y` variabili e. Il testo viene quindi visualizzato di nuovo in un altro colore senza effetto di traduzione:

[![](translate-images/translatetexteffects-small.png "Triple screenshot of the Translate Text Effects page")](translate-images/translatetexteffects-large.png#lightbox "Triple screenshot of the Translate Text Effects page")

Ognuno dei tre esempi mostra un modo diverso per negare la `Translate` chiamata:

Il primo esempio chiama semplicemente `Translate` di nuovo, ma con valori negativi. Poiché le `Translate` chiamate sono cumulative, questa sequenza di chiamate ripristina semplicemente la conversione totale ai valori predefiniti zero.

Nel secondo esempio viene chiamato [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix) . In questo modo tutte le trasformazioni torneranno al relativo stato predefinito.

Nel terzo esempio viene salvato lo stato dell' `SKCanvas` oggetto con una chiamata a [`Save`](xref:SkiaSharp.SKCanvas.Save) e quindi viene ripristinato lo stato con una chiamata a [`Restore`](xref:SkiaSharp.SKCanvas.Restore) . Questo è il modo più versatile per modificare le trasformazioni per una serie di operazioni di disegno. Queste `Save` chiamate e chiamano `Restore` funzioni come uno stack: è possibile chiamare `Save` più volte, quindi chiamare `Restore` in sequenza inversa per tornare agli stati precedenti. Il `Save` metodo restituisce un integer ed è possibile passare tale integer a [`RestoreToCount`](xref:SkiaSharp.SKCanvas.RestoreToCount*) per chiamare in modo efficace `Restore` più volte. La [`SaveCount`](xref:SkiaSharp.SKCanvas.SaveCount) proprietà restituisce il numero di stati attualmente salvati nello stack.

È anche possibile usare la [`SKAutoCanvasRestore`](xref:SkiaSharp.SKAutoCanvasRestore) classe per ripristinare lo stato dell'area di disegno. Il costruttore di questa classe è destinato a essere chiamato in un' `using` istruzione; lo stato dell'area di disegno viene ripristinato automaticamente alla fine del `using` blocco.

Tuttavia, non è necessario preoccuparsi delle trasformazioni che trasportano da una chiamata del `PaintSurface` gestore a quella successiva. Ogni nuova chiamata a `PaintSurface` Recapita un `SKCanvas` oggetto aggiornato con le trasformazioni predefinite.

Un altro uso comune della `Translate` trasformazione è per il rendering di un oggetto visivo creato originariamente usando le coordinate che risultano utili per il disegno. Ad esempio, si potrebbe voler specificare le coordinate per un orologio analogico con un centro in corrispondenza del punto (0,0). È quindi possibile usare le trasformazioni per visualizzare il clock in cui si vuole. Questa tecnica è illustrata nella pagina [**Hendecagram array**]. La [`HendecagramArrayPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramArrayPage.cs) classe inizia creando un `SKPath` oggetto per una stella a 11 punte. L' `HendecagramPath` oggetto è definito come Public, static e Read-Only, in modo che sia possibile accedervi da altri programmi dimostrativi. Viene creato in un costruttore statico:

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

Se il centro della stella è il punto (0, 0), tutti i punti della stella si trovano in un cerchio che circonda tale punto. Ogni punto è una combinazione di valori seno e coseno di un angolo che aumenta di 5/11 di 360 gradi. È anche possibile creare una stella a 11 punte aumentando l'angolo di 2/11, 3/11 o 4/11 del cerchio. Il raggio di tale cerchio è impostato su 100.

Se viene eseguito il rendering di questo percorso senza trasformazioni, il centro verrà posizionato nell'angolo superiore sinistro del e sarà `SKCanvas` visibile solo un trimestre. Il `PaintSurface` gestore di `HendecagramPage` Usa invece `Translate` per affiancare l'area di disegno a più copie della stella, ognuna con un colore casuale:

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

Le animazioni spesso coinvolgono le trasformazioni. La pagina di **animazione Hendecagram** sposta la stella a 11 punte intorno a un cerchio. La [`HendecagramAnimationPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) classe inizia con alcuni campi ed esegue l'override `OnAppearing` dei `OnDisappearing` metodi e per avviare e arrestare un Xamarin.Forms Timer:

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

Il `angle` campo viene animato da 0 gradi a 360 gradi ogni 5 secondi. Il `PaintSurface` gestore usa la `angle` Proprietà in due modi: per specificare la tonalità del colore nel `SKColor.FromHsl` metodo e come argomento per i `Math.Sin` `Math.Cos` metodi e per regolare la posizione della stella:

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

Il `PaintSurface` gestore chiama il `Translate` Metodo due volte, innanzitutto per tradursi nel centro dell'area di disegno e quindi per tradurlo nella circonferenza di un cerchio centrato (0,0). Il raggio del cerchio è impostato in modo da essere il più grande possibile mantenendo comunque la stella entro i confini della pagina:

[![](translate-images/hendecagramanimation-small.png "Triple screenshot of the Hendecagram Animation page")](translate-images/hendecagramanimation-large.png#lightbox "Triple screenshot of the Hendecagram Animation page")

Si noti che la stella mantiene lo stesso orientamento che ruota attorno al centro della pagina. Non ruota affatto. Si tratta di un processo per una trasformazione di rotazione.

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
