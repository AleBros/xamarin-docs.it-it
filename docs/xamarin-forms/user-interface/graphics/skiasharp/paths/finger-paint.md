---
title: Disegno a mano libera in SkiaSharp
description: Questo articolo illustra come usare le dita per disegnare nell'area di disegno di SkiaSharp in un'applicazione xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
ms.openlocfilehash: 03a6de3b6297e57620655e3697fe729e6fb06501
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "39615821"
---
# <a name="finger-painting-in-skiasharp"></a>Disegno a mano libera in SkiaSharp

_Usare le dita per disegnare nell'area di disegno._

Un `SKPath` oggetto può essere continuamente aggiornato e visualizzato. Questa funzionalità consente a un percorso da utilizzare per il disegno interattiva, ad esempio in un programma dipingere.

![](finger-paint-images/fingerpaintsample.png "Un esercizio di disegno a mano libera")

Il supporto per il tocco in xamarin. Forms non consente di rilevamento delle singole dita sullo schermo, in modo che un effetto di tocco di rilevamento di xamarin. Forms è stato sviluppato per fornire il supporto di tocco aggiuntive. Questo effetto è descritto nell'articolo [ **richiamo eventi dagli effetti**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). Il programma di esempio [ **Touch-rilevamento effetto demo** ](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/) include due pagine che utilizzano SkiaSharp, tra cui un programma dipingere.

Il [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/) soluzione include questo evento di tocco di rilevamento. Il progetto di libreria .NET Standard include la `TouchEffect` (classe), il `TouchActionType` enumerazione, la `TouchActionEventHandler` delegato e il `TouchActionEventArgs` classe. Ognuno dei progetti della piattaforma include un' `TouchEffect` classe per la piattaforma, il progetto iOS contiene anche un `TouchRecognizer` classe.

Il **dito Paint** nella pagina **SkiaSharpFormsDemos** è un'implementazione semplificata di disegno a mano libera. Non consentire la selezione di colore o tracciare larghezza non è possibile cancellare l'area di disegno e ovviamente non è possibile salvare l'oggetto grafico.

Il [ **FingerPaintPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml) file inserisce il `SKCanvasView` in una cella singola `Grid` e la collega il `TouchEffect` a quella `Grid`:

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

Collegare il `TouchEffect` direttamente al `SKCanvasView` non funziona in tutte le piattaforme.

Il [ **FingerPaintPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml.cs) file code-behind definisce due raccolte per l'archiviazione la `SKPath` oggetti, nonché un `SKPaint` oggetto per il rendering di questi percorsi:

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

Come suggerisce il nome, il `inProgressPaths` dizionario archivia i percorsi che sono attualmente in corso disegnati in una o più dita. La chiave del dizionario è l'ID tocco che accompagna gli eventi di tocco. Il `completedPaths` campo è una raccolta di percorsi che sono state terminate quando un dito che era il percorso di disegno sollevato dallo schermo.

Il `TouchAction` gestore gestisce questi due insiemi. Quando un dito tocca prima schermata, una nuova `SKPath` viene aggiunto a `inProgressPaths`. Come si sposta il dito, punti aggiuntivi vengono aggiunti al percorso. Quando viene rilasciato il dito, il percorso viene trasferito al `completedPaths` raccolta. È possibile disegnare con più dita contemporaneamente. Dopo ogni modifica apportata a uno dei percorsi o le raccolte, il `SKCanvasView` viene invalidata:

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

I punti che accompagnano gli eventi di rilevamento di tocco sono coordinate di xamarin. Forms; questi devono essere convertiti alle coordinate di SkiaSharp, quali sono i pixel. Lo scopo del `ConvertToPixel` (metodo).

Il `PaintSurface` gestore quindi semplicemente esegue il rendering di entrambe le raccolte dei percorsi. Sotto i percorsi in corso verranno visualizzati i percorsi completati in precedenza:

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
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

Dipinti i dito sono limitate solo dai dimostra il tuo talento:

[![](finger-paint-images/fingerpaint-small.png "Tripla screenshot della pagina di disegno con un dito")](finger-paint-images/fingerpaint-large.png#lightbox "tripla screenshot della pagina di disegno con un dito")

A questo punto si è appreso come per disegnare linee e curve utilizzando equazioni parametriche definire. Una sezione successiva sul [ **SkiaSharp curve e tracciati** ](../curves/index.md) illustra i vari tipi di curve che `SKPath` supporta. Ma un prerequisito utile prevede un'esplorazione dei [ **trasformazioni di SkiaSharp**](../transforms/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [Touch-rilevamento effetto demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/)
- [Richiamo di eventi dagli effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
