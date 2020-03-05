---
title: Disegno a mano libera in SkiaSharp
description: Questo articolo illustra come usare le dita per disegnare nell'area di disegno di SkiaSharp in un'applicazione xamarin. Forms e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
ms.openlocfilehash: d5cf0927c64732d6d0a44204db9509fae77f0d1d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291785"
---
# <a name="finger-painting-in-skiasharp"></a>Disegno a mano libera in SkiaSharp

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usare le dita per disegnare nell'area di disegno._

Un oggetto `SKPath` può essere continuamente aggiornato e visualizzato. Questa funzionalità consente a un percorso da utilizzare per il disegno interattiva, ad esempio in un programma dipingere.

![](finger-paint-images/fingerpaintsample.png "An exercise in finger painting")

Il supporto per il tocco in xamarin. Forms non consente di rilevamento delle singole dita sullo schermo, in modo che un effetto di tocco di rilevamento di xamarin. Forms è stato sviluppato per fornire il supporto di tocco aggiuntive. Questo effetto è descritto nell'articolo [**richiamo di eventi dagli effetti**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). Il programma di esempio demo per l' [**effetto di rilevamento del tocco**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) include due pagine che usano SkiaSharp, incluso un programma di disegno Finger.

La soluzione [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) include questo evento di rilevamento del tocco. Il progetto di libreria .NET Standard include la classe `TouchEffect`, l'enumerazione `TouchActionType`, il delegato `TouchActionEventHandler` e la classe `TouchActionEventArgs`. Ogni progetto della piattaforma include una classe `TouchEffect` per la piattaforma; il progetto iOS contiene anche una classe `TouchRecognizer`.

La pagina di **disegno del dito** in **SkiaSharpFormsDemos** è un'implementazione semplificata del disegno del dito. Non consentire la selezione di colore o tracciare larghezza non è possibile cancellare l'area di disegno e ovviamente non è possibile salvare l'oggetto grafico.

Il file [**FingerPaintPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml) inserisce il `SKCanvasView` in una `Grid` a cella singola e collega il `TouchEffect` a tale `Grid`:

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

Il fissaggio del `TouchEffect` direttamente al `SKCanvasView` non funziona in tutte le piattaforme.

Il file code-behind [**FingerPaintPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs) definisce due raccolte per archiviare gli oggetti `SKPath` e un oggetto `SKPaint` per il rendering di questi percorsi:

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

Come suggerisce il nome, il dizionario `inProgressPaths` archivia i percorsi attualmente disegnati da una o più dita. La chiave del dizionario è l'ID tocco che accompagna gli eventi di tocco. Il campo `completedPaths` è una raccolta di percorsi completati quando un dito che disegna il tracciato è stato rimosso dallo schermo.

Il gestore `TouchAction` gestisce queste due raccolte. Quando un dito tocca per la prima volta lo schermo, viene aggiunto un nuovo `SKPath` al `inProgressPaths`. Come si sposta il dito, punti aggiuntivi vengono aggiunti al percorso. Quando il dito viene rilasciato, il percorso viene trasferito alla raccolta di `completedPaths`. È possibile disegnare con più dita contemporaneamente. Dopo ogni modifica a uno dei percorsi o delle raccolte, il `SKCanvasView` viene invalidato:

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

I punti che accompagnano gli eventi di rilevamento di tocco sono coordinate di xamarin. Forms; questi devono essere convertiti alle coordinate di SkiaSharp, quali sono i pixel. Questo è lo scopo del metodo `ConvertToPixel`.

Il gestore `PaintSurface` quindi esegue semplicemente il rendering di entrambe le raccolte di percorsi. Sotto i percorsi in corso verranno visualizzati i percorsi completati in precedenza:

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

[![](finger-paint-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](finger-paint-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

A questo punto si è appreso come per disegnare linee e curve utilizzando equazioni parametriche definire. Una sezione successiva sulle [**curve e sui percorsi SkiaSharp**](../curves/index.md) copre i vari tipi di curve supportate da `SKPath`. Tuttavia, un prerequisito utile è un'esplorazione delle [**trasformazioni di SkiaSharp**](../transforms/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Demo sugli effetti di rilevamento del tocco (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [Richiamo di eventi dagli effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
