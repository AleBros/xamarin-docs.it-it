---
title: "illustrazione del dito in SkiaSharp" Descrizione: "in questo articolo viene illustrato come usare le dita per disegnare nell'area di disegno SkiaSharp in un' Xamarin.Forms applicazione e come illustrato nel codice di esempio".
ms. prod: Novell MS. Technology: Novell-skiasharp ms. AssetID: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B autore: davidbritch ms. Author: dabritch ms. Date: 04/05/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="finger-painting-in-skiasharp"></a>Disegno Finger in SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Usare le dita per disegnare nell'area di disegno._

Un `SKPath` oggetto può essere continuamente aggiornato e visualizzato. Questa funzionalità consente di usare un percorso per il disegno interattivo, ad esempio in un programma di disegno di un dito.

![](finger-paint-images/fingerpaintsample.png "An exercise in finger painting")

Il supporto tocco in Xamarin.Forms non consente di tenere traccia delle singole dita sullo schermo, quindi Xamarin.Forms è stato sviluppato un effetto di rilevamento del tocco per fornire ulteriore supporto per il tocco. Questo effetto è descritto nell'articolo [**richiamo di eventi dagli effetti**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md). Il programma di esempio demo per l' [**effetto di rilevamento del tocco**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) include due pagine che usano SkiaSharp, incluso un programma di disegno Finger.

La soluzione [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) include questo evento di rilevamento del tocco. Il progetto di libreria .NET Standard include la `TouchEffect` classe, l' `TouchActionType` enumerazione, il `TouchActionEventHandler` delegato e la `TouchActionEventArgs` classe. Ognuno dei progetti della piattaforma include una `TouchEffect` classe per la piattaforma. il progetto iOS contiene anche una `TouchRecognizer` classe.

La pagina di **disegno del dito** in **SkiaSharpFormsDemos** è un'implementazione semplificata del disegno del dito. Non consente di selezionare il colore o la larghezza del tratto, non ha alcun modo per cancellare l'area di disegno e, ovviamente, non è possibile salvare la grafica.

Il file [**FingerPaintPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml) inserisce `SKCanvasView` in una sola cella `Grid` e collega `TouchEffect` a tale oggetto `Grid` :

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

Il connessione `TouchEffect` diretta a `SKCanvasView` non funziona in tutte le piattaforme.

Il file code-behind [**FingerPaintPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs) definisce due raccolte per archiviare gli `SKPath` oggetti, nonché un `SKPaint` oggetto per il rendering dei percorsi seguenti:

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

Come suggerisce il nome, il `inProgressPaths` dizionario archivia i percorsi attualmente disegnati da una o più dita. La chiave del dizionario è l'ID tocco che accompagna gli eventi di tocco. Il `completedPaths` campo è una raccolta di percorsi completati quando un dito che disegna il tracciato è stato rimosso dallo schermo.

Il `TouchAction` gestore gestisce queste due raccolte. Quando un dito tocca per la prima volta lo schermo, viene aggiunto un nuovo oggetto `SKPath` a `inProgressPaths` . Quando il dito si sposta, vengono aggiunti punti aggiuntivi al percorso. Quando il dito viene rilasciato, il percorso viene trasferito alla `completedPaths` raccolta. È possibile disegnare più dita simultaneamente. Dopo ogni modifica a uno dei percorsi o delle raccolte, l'oggetto `SKCanvasView` viene invalidato:

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

I punti che accompagnano gli eventi di rilevamento del tocco sono Xamarin.Forms coordinate, che devono essere convertite in coordinate SkiaSharp, che sono pixel. Questo è lo scopo del `ConvertToPixel` metodo.

Il `PaintSurface` gestore esegue quindi semplicemente il rendering di entrambe le raccolte di percorsi. I percorsi completati in precedenza vengono visualizzati sotto i percorsi in corso:

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

I dipinti Finger sono limitati solo dal talento:

[![](finger-paint-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](finger-paint-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

A questo punto è stato illustrato come creare linee e definire curve usando equazioni parametriche. Una sezione successiva sulle [**curve e sui percorsi SkiaSharp**](../curves/index.md) copre i vari tipi di curve `SKPath` supportate da. Tuttavia, un prerequisito utile è un'esplorazione delle [**trasformazioni di SkiaSharp**](../transforms/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Demo sugli effetti di rilevamento del tocco (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [Richiamo di eventi da effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
