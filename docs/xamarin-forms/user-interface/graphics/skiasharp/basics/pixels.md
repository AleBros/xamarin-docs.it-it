---
title: "pixel e unità indipendenti dal dispositivo" Descrizione: "in questo articolo vengono esaminate le differenze tra le coordinate e le coordinate di SkiaSharp e Xamarin.Forms viene illustrato questo problema con il codice di esempio".
ms. prod: Novell MS. Technology: Novell-skiasharp ms. AssetID: 26C25BB8-FBE8-4B77-B01D-16A163A16890 autore: davidbritch ms. Author: dabritch ms. Date: 02/09/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="pixels-and-device-independent-units"></a>Pixel e unità di misura indipendenti dal dispositivo

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Esaminare le differenze tra coordinate e coordinate di SkiaSharp Xamarin.Forms_

In questo articolo vengono esaminate le differenze nel sistema di coordinate usato in SkiaSharp e Xamarin.Forms . È possibile ottenere informazioni per la conversione tra i due sistemi di coordinate e creare grafica che occupano un'area specifica:

![](pixels-images/screenfillexample.png "An oval that fills the screen")

Se si sta programmando Xamarin.Forms per un certo periodo di tempo, è possibile che si disponga di un'idea per Xamarin.Forms le coordinate e le dimensioni. I cerchi creati nei due articoli precedenti potrebbero sembrare un po' piccoli.

Tali cerchi *sono* di dimensioni ridotte rispetto alle Xamarin.Forms dimensioni. Per impostazione predefinita, SkiaSharp disegna in unità di pixel mentre Xamarin.Forms basa le coordinate e le dimensioni in un'unità indipendente dal dispositivo stabilita dalla piattaforma sottostante. Altre informazioni sul Xamarin.Forms sistema di coordinate sono disponibili nel [capitolo 5. Gestione delle dimensioni](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md) del libro creazione di *app per dispositivi mobili Xamarin.Forms con *.

La pagina del programma [**SkewSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) intitolato **Surface size** usa l'output di testo SkiaSharp per visualizzare le dimensioni della superficie di visualizzazione da tre origini diverse:

- Xamarin.Forms [`Width`](xref:Xamarin.Forms.VisualElement.Width) Proprietà e normali [`Height`](xref:Xamarin.Forms.VisualElement.Height) dell' `SKCanvasView` oggetto.
- [`CanvasSize`](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize)Proprietà dell' `SKCanvasView` oggetto.
- [`Size`](xref:SkiaSharp.SKImageInfo.Size)Proprietà del `SKImageInfo` valore, coerente con le `Width` `Height` proprietà e utilizzate nelle due pagine precedenti.

La [`SurfaceSizePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs) classe Mostra come visualizzare questi valori. Il costruttore salva l' `SKCanvasView` oggetto come campo, quindi è possibile accedervi nel `PaintSurface` gestore eventi:

```csharp
SKCanvasView canvasView;

public SurfaceSizePage()
{
    Title = "Surface Size";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvas`include sei `DrawText` metodi diversi, ma questo [`DrawText`](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) metodo è il più semplice:

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

Specificare la stringa di testo, le coordinate X e Y in cui deve iniziare il testo e un `SKPaint` oggetto. La coordinata X specifica il punto in cui è posizionato il lato sinistro del testo, ma Watch out: la coordinata Y specifica la posizione della *linea di base* del testo. Se si è mai scritto manualmente su un foglio di lavoro, la linea di base è la riga in cui si trovano i caratteri e sotto quali discendenti, ad esempio le lettere g, p, q e y, scendono.

L' `SKPaint` oggetto consente di specificare il colore del testo, la famiglia di caratteri e le dimensioni del testo. Per impostazione predefinita, la [`TextSize`](xref:SkiaSharp.SKPaint.TextSize) proprietà ha un valore pari a 12, che consente di ottenere un testo di dimensioni ridotte nei dispositivi ad alta risoluzione, ad esempio i telefoni. In tutto tranne le applicazioni più semplici, sono necessarie anche alcune informazioni sulle dimensioni del testo visualizzato. La `SKPaint` classe definisce una [`FontMetrics`](xref:SkiaSharp.SKPaint.FontMetrics) proprietà e diversi [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) metodi, ma per esigenze meno sofisticate la [`FontSpacing`](xref:SkiaSharp.SKPaint.FontSpacing) proprietà fornisce un valore consigliato per la spaziatura delle righe di testo successive.

Il `PaintSurface` gestore seguente crea un `SKPaint` oggetto per un oggetto `TextSize` di 40 pixel, che corrisponde all'altezza verticale desiderata del testo dalla parte superiore del valore ascendente alla parte inferiore dei discendenti. Il `FontSpacing` valore restituito dall' `SKPaint` oggetto è leggermente superiore a circa 47 pixel.

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 40
    };

    float fontSpacing = paint.FontSpacing;
    float x = 20;               // left margin
    float y = fontSpacing;      // first baseline
    float indent = 100;

    canvas.DrawText("SKCanvasView Height and Width:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(String.Format("{0:F2} x {1:F2}",
                                  canvasView.Width, canvasView.Height),
                    x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKCanvasView CanvasSize:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(canvasView.CanvasSize.ToString(), x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKImageInfo Size:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(info.Size.ToString(), x + indent, y, paint);
}
```

Il metodo inizia la prima riga di testo con una coordinata X di 20 (per un piccolo margine a sinistra) e una coordinata Y di `fontSpacing` , che è leggermente più di quanto necessario per visualizzare l'altezza completa della prima riga di testo nella parte superiore della superficie di visualizzazione. Dopo ogni chiamata a `DrawText` , la coordinata Y viene aumentata di uno o due incrementi di `fontSpacing` .

Ecco il programma in esecuzione:

[![](pixels-images/surfacesize-small.png "Triple screenshot of the Surface Size  page")](pixels-images/surfacesize-large.png#lightbox "Triple screenshot of the Surface Size  page")

Come si può notare, la `CanvasSize` proprietà di `SKCanvasView` e la `Size` proprietà del `SKImageInfo` valore sono coerenti per la segnalazione delle dimensioni in pixel. Le `Height` `Width` proprietà e di `SKCanvasView` sono Xamarin.Forms proprietà e segnalano la dimensione della visualizzazione nelle unità indipendenti dal dispositivo definite dalla piattaforma.

Il simulatore iOS Seven a sinistra ha due pixel per unità indipendente dal dispositivo e Android Nexus 5 al centro ha tre pixel per unità. Questo è il motivo per cui il cerchio semplice illustrato in precedenza ha dimensioni diverse su piattaforme diverse.

Se si preferisce lavorare interamente in unità indipendenti dal dispositivo, è possibile impostare la `IgnorePixelScaling` proprietà di `SKCanvasView` su `true` . Tuttavia, i risultati potrebbero non essere simili. SkiaSharp esegue il rendering della grafica su una superficie del dispositivo più piccola, con una dimensione in pixel uguale alla dimensione della visualizzazione in unità indipendenti dal dispositivo. Ad esempio, SkiaSharp utilizzerebbe una superficie di visualizzazione di 360 x 512 pixel in Nexus 5. Viene quindi ridimensionata l'immagine di dimensioni, con conseguente irregolarità delle bitmap.

Per mantenere la stessa risoluzione dell'immagine, una soluzione migliore consiste nel scrivere funzioni semplici per la conversione tra i due sistemi di coordinate.

Oltre al `DrawCircle` metodo, `SKCanvas` definisce anche due `DrawOval` metodi che consentono di creare un'ellisse. Un'ellisse è definita da due raggi anziché da un singolo raggio. Queste sono note come *raggio principale* e *raggio secondario*. Il `DrawOval` metodo disegna un'ellisse con due raggi paralleli agli assi X e Y. Se è necessario creare un'ellisse con assi che non sono paralleli agli assi X e Y, è possibile usare una trasformazione di rotazione come illustrato nell'articolo [**la trasformazione ruota**](../transforms/rotate.md) o un percorso grafico come illustrato nell'articolo [**tre modi per tracciare un arco**](../curves/arcs.md). Questo overload del [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) Metodo denomina i due parametri di raggi `rx` e `ry` per indicare che sono paralleli agli assi X e Y:

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

È possibile creare un'ellisse che riempie la superficie di visualizzazione? Nella pagina **riempimento ellisse** viene illustrato come. Il `PaintSurface` gestore eventi nella classe [**EllipseFillPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs) sottrae la metà della larghezza del tratto dai `xRadius` `yRadius` valori e per adattarsi all'intera ellisse e al relativo contorno all'interno della superficie di visualizzazione:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float strokeWidth = 50;
    float xRadius = (info.Width - strokeWidth) / 2;
    float yRadius = (info.Height - strokeWidth) / 2;

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = strokeWidth
    };
    canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
}
```

In esecuzione:

[![](pixels-images/ellipsefill-small.png "Triple screenshot of the Surface Size  page")](pixels-images/ellipsefill-large.png#lightbox "Triple screenshot of the Surface Size  page")

L'altro [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) metodo dispone di un [`SKRect`](xref:SkiaSharp.SKRect) argomento, ovvero un rettangolo definito in termini di coordinate X e Y dell'angolo superiore sinistro e dell'angolo inferiore destro. L'ovale riempie il rettangolo, il che suggerisce che potrebbe essere possibile usarlo nella pagina di **riempimento dell'ellisse** come segue:

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

Tuttavia, che tronca tutti i bordi del contorno dell'ellisse sui quattro lati. Per eseguire questa operazione, è necessario modificare tutti gli `SKRect` argomenti del costruttore in base a `strokeWidth` .

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
