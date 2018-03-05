---
title: Disegno di un dito
description: Utilizzare le dita per disegnare nell'area di disegno.
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 04/05/2017
ms.openlocfilehash: c9894cac0064b91049b0142971896d7dd67dec5f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="finger-painting"></a>Disegno di un dito

_Utilizzare le dita per disegnare nell'area di disegno._

Un `SKPath` oggetto può essere continuamente aggiornato e visualizzato. Questa funzionalità consente a un percorso da utilizzare per il disegno interattivo, ad esempio in un programma finger-painting.

![](finger-paint-images/fingerpaintsample.png "Un esercizio nel disegno dito")

Il supporto per il tocco in xamarin. Forms non consente di rilevamento singoli dita sullo schermo, in modo un effetto di rilevamento di tocco xamarin. Forms è stato sviluppato per fornire supporto per il tocco aggiuntive. Questo effetto è descritto nell'articolo [ **richiamare eventi dagli effetti**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). Il programma di esempio [ **tocco rilevamento effetto demo** ](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/) include due pagine che utilizzano SkiaSharp, tra cui un programma finger-painting.

Il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/) soluzione include questo evento di rilevamento di tocco. Il progetto libreria di classi portabile include il `TouchEffect` (classe), il `TouchActionType` enumerazione, il `TouchActionEventHandler` delegare e `TouchActionEventArgs` classe. Progetti di tutte le piattaforme includono un `TouchEffect` classe per la piattaforma; il progetto iOS contiene anche un `TouchRecognizer` classe.

Il **Paint dito** pagina **SkiaSharpFormsDemos** è un'implementazione semplificata di disegno con un dito. Non consentire la selezione del colore o tracciare larghezza non può cancellare l'area di disegno e naturalmente è possibile salvare l'oggetto grafico.

Il [ **FingerPaintPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml) file inserisce il `SKCanvasView` in una cella singola `Grid` e collega il `TouchEffect` a quella `Grid`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Paths.FingerPaintPage"
             Title="Finger Paint">

    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

Associare il `TouchEffect` direttamente il `SKCanvasView` non funziona in tutte le piattaforme.

Il [ **FingerPaintPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml.cs) file code-behind definisce due raccolte per l'archiviazione di `SKPath` oggetti, nonché un `SKPaint` oggetto per il rendering di questi percorsi:

```csharp
public partial class FingerPaintPage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };

    public FingerPaintPage()
    {
        InitializeComponent();
    }
    ...
}
```

Come suggerisce il nome, il `inProgressPaths` dizionario archivia i percorsi che sono in corso un da uno o più dita. La chiave del dizionario è l'ID tocco che accompagna gli eventi tocco. Il `completedPaths` campo è una raccolta di percorsi che sono state completate quando un dito dallo schermo di disegno il percorso di eliminazione.

Il `TouchAction` gestore gestisce questi due raccolte. Quando un dito tocca prima schermata di un nuovo `SKPath` viene aggiunto a `inProgressPaths`. Quando si sposta il dito, punti aggiuntivi vengono aggiunti al percorso. Quando viene rilasciato il dito, il percorso viene trasferito al `completedPaths` insieme. È possibile disegnare con le dita più contemporaneamente. Dopo ogni modifica apportata a uno dei percorsi o le raccolte, di `SKCanvasView` viene invalidata:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                           (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }
}
```

I punti che accompagna gli eventi di rilevamento di tocco vengono coordinate xamarin. Forms; questi devono essere convertiti in coordinate SkiaSharp, sono i pixel. Che è lo scopo del `ConvertToPixel` metodo.

Il `PaintSurface` gestore quindi semplicemente il rendering entrambe le raccolte di percorsi. I percorsi completati in precedenza vengono visualizzati sotto i percorsi in corso:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ,,,
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (SKPath path in completedPaths)
        {
            canvas.DrawPath(path, paint);
        }

        foreach (SKPath path in inProgressPaths.Values)
        {
            canvas.DrawPath(path, paint);
        }
    }
    ...
}
```

Dipinti i dito sono limitate solo dalle tue capacità:

[![](finger-paint-images/fingerpaint-small.png "Schermata triplo della pagina di disegno con un dito")](finger-paint-images/fingerpaint-large.png "tripla schermata della pagina di disegno con un dito")


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
- [Rilevamento di tocco effetto demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/)
- [Richiamo di eventi da effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
