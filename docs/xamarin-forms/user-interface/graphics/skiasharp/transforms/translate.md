---
title: La trasformazione di conversione
description: In questo articolo examiens come utilizzare la trasformazione Traduci da spostare grafica SkiaSharp nelle applicazioni di xamarin. Forms e viene illustrato quanto descritto con codice di esempio.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: dbc7ffe5c3828876579ba72a387c86d8221c1641
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244822"
---
# <a name="the-translate-transform"></a>La trasformazione di conversione

_Informazioni su come utilizzare la trasformazione di conversione da spostare SkiaSharp grafica_

Il tipo più semplice di trasformazione in SkiaSharp è il *tradurre* o *traduzione* trasformare. Questa trasformazione passa gli oggetti grafici nelle direzioni orizzontale e verticale. In un certo senso, traduzione è la trasformazione non più necessaria poiché è in genere possibile ottenere lo stesso effetto semplicemente modificando le coordinate che si sta utilizzando la funzione di disegno. Durante il rendering di un percorso, tuttavia, tutte le coordinate sono incapsulate nel percorso, pertanto è molto più semplice l'applicazione della trasformazione di traslazione per spostare l'intero percorso.

Conversione risulta utile anche per l'animazione e per gli effetti di testo semplice:

![](translate-images/translateexample.png "Ombreggiatura del testo, incisione e rilievo con la conversione")

Il [ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/System.Single/System.Single/) metodo `SKCanvas` presenta due parametri che determinano gli oggetti di grafica disegnati successivamente spostare orizzontalmente e verticalmente:

```csharp
public void Translate (Single dx, Single dy)
```

Questi argomenti possono essere negativi. Un secondo [ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/SkiaSharp.SKPoint/) metodo combina i valori di due traduzione in una singola `SKPoint` valore:

```csharp
public void Translate (SKPoint point)
```

Il **accumulati tradurre** pagina del [ **SkiaSharpForms** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) programma di esempio illustra che più chiamate di `Translate` metodo sono cumulativi. Il [ `AccumulatedTranslate` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs) classe visualizza 20 versioni dello stesso rettangolo, ogni valore di offset dal rettangolo precedente sufficiente in modo che l'estensione lungo la diagonale. Ecco il `PaintSurface` gestore eventi:

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

I rettangoli successivi inserimento in basso nella pagina:

[![](translate-images/accumulatedtranslate-small.png "Schermata triplo della pagina accumulati tradurre")](translate-images/accumulatedtranslate-large.png#lightbox "tripla schermata della pagina accumulati tradurre")

Se sono i fattori di conversione accumulato `dx` e `dy`, e il punto specificato in una funzione di disegno (`x`, `y`), quindi il rendering dell'oggetto grafico in corrispondenza del punto (`x'`, `y'`), dove:

x' = x + dx

y' = y + dy

Tali parametri sono noti come il *trasformare formule* per la conversione. I valori predefiniti di `dx` e `dy` per un nuovo `SKCanvas` sono pari a 0.

È comune per utilizzare la trasformazione di conversione per gli effetti di ombreggiatura e tecniche simili, come il **tradurre gli effetti del testo** illustra come. Di seguito è la parte rilevante del `PaintSurface` gestore nel [ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) classe:

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

In ognuno dei tre esempi, `Translate` viene chiamato per visualizzare il testo per eseguire l'offset dal percorso specificato per il `x` e `y` variabili. Quindi, il testo viene visualizzato nuovamente in un altro colore senza alcun effetto di conversione:

[![](translate-images/translatetexteffects-small.png "Schermata di triplo della pagina tradurre gli effetti del testo")](translate-images/translatetexteffects-large.png#lightbox "tripla schermata della pagina tradurre gli effetti del testo")

Ciascuno dei tre esempi viene illustrato un modo diverso per la negazione di `Translate` chiamare:

Nel primo esempio chiama semplicemente `Translate` di nuovo, ma con i valori negativi. Poiché il `Translate` chiamate sono cumulative, questa sequenza di chiamate Ripristina semplicemente la traduzione totale i valori predefiniti pari a zero.

Il secondo esempio viene chiamato [ `ResetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ResetMatrix()/). In questo modo tutte le trasformazioni restituire lo stato predefinito.

Il terzo esempio Salva lo stato del del `SKCanvas` oggetto con una chiamata a [ `Save` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Save()/) e quindi Ripristina lo stato con una chiamata a [ `Restore` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Restore/). Questo è il modo più versatile per modificare le trasformazioni per una serie di operazioni di disegno. Questi `Save` e `Restore` funzione come uno stack di chiamate: È possibile chiamare `Save` più tempo e quindi chiamare `Restore` in inverso della sequenza di ripristinare lo stato precedente. Il `Save` il metodo restituisce un valore integer, ed è possibile passare tale integer per [ `RestoreToCount` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RestoreToCount/) per chiamare in modo efficace `Restore` più volte. Il [ `SaveCount` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.SaveCount/) proprietà restituisce il numero di stati salvati attualmente nello stack.

Tuttavia, non è necessario preoccuparsi riportando da una chiamata di trasformazioni di `PaintSurface` gestore a quella successiva. Ogni nuova chiamata a `PaintSurface` offre una nuova `SKCanvas` oggetto con trasformazioni predefinita.

Un altro utilizzo comune del `Translate` trasformazione è per il rendering di un oggetto visivo è stato originariamente creato con le coordinate che sono utili per il disegno. Potrebbe ad esempio, si desidera specificare le coordinate per un orologio analogico con un centro nel punto (0, 0). È quindi possibile utilizzare le trasformazioni per visualizzarlo in cui si desidera. Questa funzionalità viene illustrata la [**Hendecagram matrice**] pagina. Il [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs) classe inizia creando un `SKPath` oggetto per un grafico 11 punte. Il `HendecagramPath` oggetto viene definito come public, static e sola lettura in modo che sia accessibile da altri programmi di dimostrazione. Viene creato in un costruttore statico:

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

Se il centro della stella è il punto (0, 0), tutti i punti della stella sono in un cerchio attorno a quel punto. Ogni punto è una combinazione dei valori di seno e coseno di un angolo che aumenta di 5/11ths di 360 gradi. (È anche possibile creare un grafico 11 punte aumentando l'angolo di 2/11, / 11ths 3 o 4/11 del cerchio.) Il raggio del quale viene impostato come 100.

Se questo percorso viene eseguito il rendering senza le eventuali trasformazioni, il centro verrà posizionato nell'angolo superiore sinistro del `SKCanvas`e saranno visibili solo a un trimestre. Il `PaintSurface` gestore di `HendecagramPage` utilizza invece `Translate` per riquadro area di lavoro con più copie della stella, ciascuno di essi in modo casuale di colore:

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

Di seguito è riportato il risultato:

[![](translate-images/hendecagramarray-small.png "Schermata triplo della pagina matrice Hendecagram")](translate-images/hendecagramarray-large.png#lightbox "tripla schermata della pagina Hendecagram matrice")

Le animazioni comportano spesso le trasformazioni. Il **Hendecagram animazione** pagina si sposta la stella 11 dei checkpoint in un cerchio. Il [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) classe inizia con alcuni campi ed esegue l'override del `OnAppearing` e `OnDisappearing` metodi per avviare e arrestare un timer di xamarin. Forms:

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

Il `angle` campo viene animato da 0 a 360 gradi ogni 5 secondi. Il `PaintSurface` gestore utilizza il `angle` proprietà in due modi: per specificare la tonalità del colore il `SKColor.FromHsl` (metodo) e come argomento per il `Math.Sin` e `Math.Cos` metodi per definire il percorso della stella:

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

Il `PaintSurface` chiamate del gestore di `Translate` metodo due volte, per eseguire la conversione al centro dell'area di disegno, quindi per tradurre la circonferenza del cerchio centrata (0, 0). Il raggio del cerchio viene impostato per essere più grande possibile mantenendo comunque la stella entro confini della pagina:

[![](translate-images/hendecagramanimation-small.png "Schermata triplo della pagina animazione Hendecagram")](translate-images/hendecagramanimation-large.png#lightbox "tripla schermata della pagina Hendecagram animazione")

Si noti che la stella mantenga lo stesso orientamento come che ruota intorno al centro della pagina. Non è possibile ruotare affatto. Che è un processo per una trasformazione rotativa.


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
