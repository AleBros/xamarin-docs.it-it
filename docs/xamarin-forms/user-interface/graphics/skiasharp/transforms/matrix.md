---
title: Trasformazioni di matrice
description: Esaminare i SkiaSharp trasformazioni con la matrice di trasformazione versatili
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4
author: charlespetzold
ms.author: chape
ms.date: 04/12/2017
ms.openlocfilehash: 9d5e65abe675ded48e9239f2cd10ceed4a7c3a52
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="matrix-transforms"></a>Trasformazioni di matrice

_Esaminare i SkiaSharp trasformazioni con la matrice di trasformazione versatili_

Tutte le trasformazioni applicate al `SKCanvas` oggetto vengono consolidate in una singola istanza di [ `SKMatrix` ](https://developer.xamarin.com/api/type/SkiaSharp.SKMatrix/) struttura. Si tratta di una matrice di trasformazione di 3 per 3 standard simile a quelle di tutti i sistemi di grafica 2D moderne.

Come si è visto, è possibile utilizzare le trasformazioni in SkiaSharp senza conoscere la trasformazione di matrice, ma la matrice di trasformazione è importante da una prospettiva teorica ed è fondamentale quando si utilizzano le trasformazioni per modificare i percorsi o per la gestione dell'input tocco complessi, entrambi cui vengono illustrati in questo articolo e la successiva.

![](matrix-images/matrixtransformexample.png "Una bitmap soggetto a una trasformazione affine")

La matrice di trasformazione corrente applicata per la `SKCanvas` è disponibile in qualsiasi momento mediante l'accesso di sola lettura [ `TotalMatrix` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.TotalMatrix/) proprietà. È possibile impostare una nuova matrice di trasformazione usando il [ `SetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.SetMatrix/p/SkiaSharp.SKMatrix/) metodo ed è possibile ripristinare tale matrice di trasformazione per i valori predefiniti chiamando [ `ResetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ResetMatrix/).

L'unico altro `SKCanvas` membro che interagisce direttamente con trasformazione di matrice dell'area di disegno è [ `Concat` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Concat/p/SkiaSharp.SKMatrix@/) che consente di concatenare due matrici moltiplicando nel loro insieme.

La matrice di trasformazione del valore predefinito è la matrice identità ed è costituito da 1 in diagonale celle e qualsiasi altro punto di 0:

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

È possibile creare una matrice di identità mediante il [ `SKMatrix.MakeIdentity` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeIdentity()/) metodo:

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

Il `SKMatrix` costruttore predefinito non *non* restituire una matrice di identità. Restituisce una matrice con tutte le celle impostate su zero. Non utilizzare il `SKMatrix` costruttore a meno che non si prevede di impostare manualmente le celle.

Quando SkiaSharp esegue il rendering di un oggetto grafico, ogni punto (x, y) viene convertito in modo efficace in una matrice da 1 a 3 con valore 1 nella terza colonna:

<pre>
| x  y  1 |
</pre>

Questa matrice da 1-3 rappresenta un punto tridimensionale con la coordinata Z impostata su 1. Esistono motivi matematiche (illustrati più avanti), il motivo per cui una trasformazione di matrice bidimensionale richiede l'utilizzo di tre dimensioni. È possibile pensare di questa matrice da 1-3 come rappresenta un punto in un sistema di coordinate 3D, ma sempre sul piano 2D dove Z è uguale a 1.

Questa matrice da 1-3 viene quindi moltiplicata per la matrice di trasformazione e il risultato è il punto di rendering nell'area di disegno:

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

I punti di convertire di moltiplicazione standard sono i seguenti:

x' = x

y' = y

z' = 1

Che è la trasformazione predefinito.

Quando il `Translate` metodo viene chiamato sul `SKCanvas` oggetto, il `tx` e `ty` argomenti per il `Translate` metodo diventano le prime due celle nella terza riga della matrice di trasformazione:

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

La moltiplicazione è come segue:

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

Ecco le formule di trasformazione:

x' = x + tx

y' = y + ty

Fattori di scala hanno un valore predefinito di 1. Quando si chiama il `Scale` metodo in un nuovo `SKCanvas` dell'oggetto, la matrice di trasformazione risultante contiene il `sx` e `sy` argomenti nelle celle diagonale:

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

Di seguito sono elencate le formule di trasformazione:

x' = sx · x

y' = sy · y

La matrice di trasformazione dopo la chiamata `Skew` contiene due argomenti nelle celle della matrice accanto ai fattori di scala:

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

Le formule di trasformazione sono:

x' = x + xSkew · y

y' = ySkew · x + y

Per una chiamata a `RotateDegrees` o `RotateRadians` di un angolo di α, la matrice di trasformazione è il seguente:

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

Ecco le formule di trasformazione:

x' = cos(α) · x - sin(α) · y

y' = sin(α) · x - cos(α) · y

Una volta α 0 gradi, è la matrice identità. Una volta α 180 gradi, la matrice di trasformazione è il seguente:

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

Una rotazione di 180 gradi equivale a capovolgere un oggetto in senso orizzontale e verticale, che viene eseguita anche impostando i fattori di scala pari a – 1.

Tutti questi tipi di trasformazioni sono classificati come *affine* Trasforma. Le trasformazioni affini coinvolgono mai la terza colonna della matrice, che rimane il valore predefinito pari a 0, 0 e 1. L'articolo [trasformazioni affini Non](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md) vengono illustrate le trasformazioni non affini.

## <a name="matrix-multiplication"></a>La moltiplicazione

Un grande vantaggio con la matrice di trasformazione è che le trasformazioni composite possono essere ottenute tramite la moltiplicazione, che viene spesso definita nella documentazione di SkiaSharp come *concatenazione*. Molti dei metodi relativi alla trasformazione `SKCanvas` fare riferimento a pre-""concatenazione o "pre-concat." Si riferisce all'ordine di moltiplicazione, è importante in quanto la moltiplicazione non è commutativa.

Ad esempio, la documentazione per il [ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/System.Single/System.Single/) metodo indica che il "Pre-concats la matrice corrente con la traslazione specificata," durante la documentazione per il [ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/) metodo indica che il "Pre-concats la matrice corrente con la scala specificata."

Ciò significa che la trasformazione specificata dalla chiamata al metodo è il moltiplicatore (l'operando sinistro) e la matrice di trasformazione corrente è moltiplicando (l'operando destro).

Si supponga che `Translate` è seguito da `Scale`:

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

Il `Scale` trasformazione viene moltiplicata per il `Translate` Trasforma per la matrice di trasformazione composito:

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale` può essere chiamato prima `Translate` come segue:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

In tal caso, viene invertito l'ordine della moltiplicazione e i fattori di scala vengono applicati in modo efficace il fattore di conversione:

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

Ecco il `Scale` metodo con un punto pivot:

```csharp
canvas.Scale(sx, sy, px, py);
```

Ciò equivale alle chiamate di traduzione e la scala seguenti:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Le tre matrici di trasformazione vengono moltiplicate in ordine inverso dal modo in cui vengono visualizzati i metodi nel codice:

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

### <a name="the-skmatrix-structure"></a>La struttura SKMatrix

Il `SKMatrix` struttura definisce nove proprietà di lettura/scrittura di tipo `float` corrispondente a nove celle della matrice di trasformazione:

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix` definisce inoltre una proprietà denominata [ `Values` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix.Values/) di tipo `float[]`. Questa proprietà può essere utilizzata per impostare o ottenere i valori in un'unica sequenza nell'ordine nove `ScaleX`, `SkewX`, `TransX`, `SkewY`, `ScaleY`, `TransY`, `Persp0`, `Persp1`, e `Persp2`.

Il `Persp0`, `Persp1`, e `Persp2` celle sono descritti nell'articolo, [trasformazioni affini Non](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md). Se le celle hanno valori predefiniti pari a 0, 0 e 1, la trasformazione viene moltiplicata per un punto di coordinate simile al seguente:

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

x' = ScaleX · x + SkewX · y + TransX

y' = SkewX · x + ScaleY · y + TransY

z' = 1

Si tratta di trasformazione affine bidimensionale completezza. Il file di trasformazione affine mantiene le linee parallele, il che significa che un rettangolo mai viene trasformato in un valore diverso da un parallelogramma.

Il `SKMatrix` struttura definisce i diversi metodi statici per creare `SKMatrix` valori. Questi tutte restituito `SKMatrix` valori:

- [`MakeTranslation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeTranslation/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/System.Single/System.Single/) con un punto pivot
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/) per un angolo in radianti
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/System.Single/System.Single/) per un angolo in radianti con un punto pivot
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/)
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/System.Single/System.Single/) con un punto pivot
- [`MakeSkew`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeSkew/p/System.Single/System.Single/)

`SKMatrix` definisce anche diversi metodi statici che consentono di concatenare due matrici, ovvero moltiplicati insieme. Questi metodi sono denominati `Concat`, `PostConcat`, e `PreConcat`, e sono disponibili due versioni di ognuno. Questi metodi non hanno nessun valore restituito; In alternativa, fanno riferimento esistente `SKMatrix` valori tramite `ref` argomenti. Nell'esempio seguente, `A`, `B`, e `R` (per "risultato") sono tutti `SKMatrix` valori.

I due `Concat` metodi vengono chiamati simile al seguente:

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

Questi eseguire la moltiplicazione seguente:

R = B × A

Gli altri metodi hanno solo due parametri. Il primo parametro viene modificato e nell'output restituito dalla chiamata al metodo, contiene il prodotto di due matrici. I due `PostConcat` metodi vengono chiamati simile al seguente:

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

Queste chiamate eseguono le seguenti operazioni:

A = A × B

I due `PreConcat` metodi sono simili:

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

Queste chiamate eseguono le seguenti operazioni:

A = B × A

Le versioni di queste chiamate al metodo con tutti `ref` gli argomenti sono leggermente più efficienti chiamare le implementazioni sottostante, ma potrebbero confondere a un utente durante la lettura del codice e supponendo che qualsiasi valore con un `ref` argomento modificato dal metodo. Inoltre, è spesso utile passare un argomento che è il risultato di uno del `Make` metodi, ad esempio:

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

Verrà creata la seguente matrice:

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

Questa è la trasformazione di scala moltiplicata per la trasformazione di traslazione. In questo caso specifico, il `SKMatrix` struttura fornisce un collegamento con un metodo denominato [ `SetScaleTranslate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.SetScaleTranslate/p/System.Single/System.Single/System.Single/System.Single/):

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

Si tratta di uno di quando è possibile utilizzare il minor numero di volte il `SKMatrix` costruttore. Il `SetScaleTranslate` metodo imposta tutte le nove celle della matrice. È inoltre possibile utilizzare il `SKMatrix` costruttore con statica `Rotate` e `RotateDegrees` metodi:

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

Questi metodi eseguono *non* concatenare una trasformazione rotativa per una trasformazione esistente. I metodi di impostano tutte le celle della matrice. Sono identici al `MakeRotation` e `MakeRotationDegrees` metodi ad eccezione del fatto che essi non creare un'istanza di `SKMatrix` valore.

Si supponga di avere un `SKPath` oggetto che si desidera visualizzare, ma si preferisce che ha un orientamento diverso o un punto centrale diverso. È possibile modificare tutte le coordinate del percorso chiamando il [ `Transform` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Transform/p/SkiaSharp.SKMatrix/) metodo `SKPath` con un `SKMatrix` argomento. Il **percorso trasformare** pagina viene illustrato come eseguire questa operazione. Il [ `PathTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs) classe riferimenti di `HendecagramPath` oggetto in un campo, ma utilizza il costruttore per applicare una trasformazione a tale percorso:

```csharp
public class PathTransformPage : ContentPage
{
    SKPath transformedPath = HendecagramArrayPage.HendecagramPath;

    public PathTransformPage()
    {
        Title = "Path Transform";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        SKMatrix matrix = SKMatrix.MakeScale(3, 3);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(360f / 22));
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(300, 300));

        transformedPath.Transform(matrix);
    }
    ...
}
```

Il `HendecagramPath` oggetto dispone di un centro (0, 0), e i punti della stella undici estendono verso l'esterno da tale center da 100 unità in tutte le direzioni. Ciò significa che il percorso ha coordinate positive e negative. Il **percorso trasformare** pagina preferisce lavorare con una stella tre volte più ampio e con tutte le coordinate positivo. Inoltre, non desidera un unico punto di stella in modo da puntare verso l'alto. Vuole invece per un punto della stella in modo da puntare verso il. (La stella dispone di undici punti, pertanto non può includere entrambi.) Ciò richiede la rotazione di stella di 360 gradi diviso 22.

Il costruttore genera un `SKMatrix` oggetto da tre trasformazioni separate utilizzando il `PostConcat` metodo con il modello seguente, dove A, B e C sono istanze di `SKMatrix`:

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

Si tratta di una serie di moltiplicazioni successive, pertanto il risultato è come segue:

A × B × C

Aiuto moltiplicazioni consecutivi comprendere cosa ogni trasformazione. La trasformazione di scala aumenta la dimensione delle coordinate di percorso di un fattore pari a 3, pertanto le coordinate compreso tra –300 e 300. La trasformazione rotativa ruota stella intorno relativa origine. La trasformazione di traslazione quindi il comando Sposta destra di 300 pixel e verso il basso, in modo che tutte le coordinate diventare positivo.

Esistono altre sequenze di produrre la matrice stessa. Ecco un altro:

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

Questo ruota innanzitutto il percorso al centro e converte quindi 100 pixel a destra e verso il basso così tutte le coordinate sono positive. Stella è aumentata quindi dimensioni relazione all'angolo superiore sinistro di nuovo, che è il punto (0, 0).

Il `PaintSurface` gestore può eseguire semplicemente il rendering di questo percorso:

```csharp
public class PathTransformPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Magenta;
            paint.StrokeWidth = 5;

            canvas.DrawPath(transformedPath, paint);
        }
    }
}

```

Viene visualizzato nell'angolo superiore sinistro dell'area di disegno:

[![](matrix-images/pathtransform-small.png "Schermata triplo della pagina percorso trasformare")](matrix-images/pathtransform-large.png#lightbox "tripla schermata della pagina percorso Transform")

Il costruttore di questo programma si applica la matrice per il percorso con la chiamata seguente:

```csharp
transformedPath.Transform(matrix);
```

Il percorso non *non* mantenere questa matrice come proprietà. Al contrario, la trasformazione si applica a tutte le coordinate del percorso. Se `Transform` viene chiamato nuovamente, la trasformazione viene applicata nuovamente, senza che sia l'unico modo è possibile tornare applicando un'altra matrice che annulla la trasformazione. Fortunatamente, la `SKMatrix` struttura definisce un [ `TryInverse` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.TryInvert/p/SkiaSharp.SKMatrix@/) metodo che ottiene la matrice che inverte una matrice specificata:

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

Il metodo viene chiamato `TryInverse` poiché non tutte le matrici sono invertibile, ma che non possa essere usati per una trasformazione grafica una matrice non invertibile.

È inoltre possibile applicare una trasformazione di matrice da un `SKPoint` valore, una matrice di punti, un `SKRect`, o semplicemente un singolo numero all'interno del programma. Il `SKMatrix` struttura supporta queste operazioni con una raccolta di metodi che iniziano con la parola `Map`, come i seguenti:

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

Se si utilizza tale metodo last, tenere presente che il `SKRect` struttura non è in grado di rappresentare un rettangolo ruotato. Il metodo ha senso solo un `SKMatrix` valore che rappresenta la traduzione e la scalabilità.

### <a name="interactive-experimentation"></a>Sperimentazione interattiva

Un modo per acquisire familiarità con la trasformazione affine è in modo interattivo spostando altri tre angoli di una bitmap sullo schermo e visualizzare quali trasformazione risultante. Questo è l'idea alla base di **Mostra matrice Affine** pagina. Questa pagina richiede altre due classi che vengono usate anche in altri dimostrazioni:

Il [ `TouchPoint` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/TouchPoint.cs) classe viene visualizzato un cerchio semitrasparente che può essere trascinato sullo schermo. `TouchPoint` è necessario che un `SKCanvasView` o un elemento che è un elemento padre di un `SKCanvasView` hanno il [ `TouchEffect` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/TouchEffect.cs) associata. Impostare la proprietà `Capture` su `true`. Nel `TouchAction` gestore dell'evento, il programma è necessario chiamare il `ProcessTouchEvent` metodo `TouchPoint` per ogni `TouchPoint` istanza. Il metodo restituisce `true` se è stato generato l'evento tocco nel punto di tocco lo spostamento. Inoltre, il `PaintSurface` gestore deve chiamare il `Paint` metodo in ogni `TouchPoint` istanza, passando il `SKCanvas` oggetto.

`TouchPoint` viene illustrato un comune modo che un oggetto visivo SkiaSharp può essere incapsulato in una classe separata. La classe può definire le proprietà per specificare caratteristiche dell'oggetto visivo e un metodo denominato `Paint` con un `SKCanvas` argomento possibile eseguirne il rendering.

Il `Center` proprietà `TouchPoint` indica la posizione dell'oggetto. Questa proprietà può essere impostata per inizializzare il percorso; le modifiche alle proprietà quando l'utente trascina il cerchio dell'area di disegno.

Il **pagina Mostra una matrice Affine** richiede anche il [ `MatrixDisplay` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/MatrixDisplay.cs) classe. Questa classe consente di visualizzare le celle di un `SKMatrix` oggetto. Dispone di due metodi pubblici: `Measure` per ottenere le dimensioni della matrice, rendering e `Paint` per visualizzarlo. La classe contiene un `MatrixPaint` proprietà di tipo `SKPaint` che può essere sostituito per una dimensione diversa o un colore.

Il [ **ShowAffineMatrixPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml) file crea un'istanza di `SKCanvasView` e collega un `TouchEffect`. Il [ **ShowAffineMatrixPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs) file code-behind crea tre `TouchPoint` oggetti e quindi li imposta in posizioni corrispondenti tre angoli di una bitmap che vengono caricate dal incorporato risorse:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    SKMatrix matrix;
    SKBitmap bitmap;
    SKSize bitmapSize;

    TouchPoint[] touchPoints = new TouchPoint[3];

    MatrixDisplay matrixDisplay = new MatrixDisplay();

    public ShowAffineMatrixPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }

        touchPoints[0] = new TouchPoint(100, 100);                  // upper-left corner
        touchPoints[1] = new TouchPoint(bitmap.Width + 100, 100);   // upper-right corner
        touchPoints[2] = new TouchPoint(100, bitmap.Height + 100);  // lower-left corner

        bitmapSize = new SKSize(bitmap.Width, bitmap.Height);
        matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                           touchPoints[1].Center,
                                           touchPoints[2].Center);
    }
    ...
}
```

Una matrice affine è definita in modo univoco da tre punti. I tre `TouchPoint` oggetti corrispondono agli angoli superiore sinistro, superiore destro e inferiore sinistro della bitmap. Perché è in grado di trasformazione di un rettangolo in un parallelogramma solo una matrice affine, gli altri tre implicito quarto punto. Il costruttore termina con una chiamata a `ComputeMatrix`, che consente di calcolare le celle di un `SKMatrix` oggetto da queste tre punti.

Il `TouchAction` chiamate del gestore di `ProcessTouchEvent` metodo ciascun `TouchPoint`. Il `scale` valore viene convertito da coordinate xamarin. Forms in pixel:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = canvasView.CanvasSize.Width / (float)canvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                               touchPoints[1].Center,
                                               touchPoints[2].Center);
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

Se qualsiasi `TouchPoint` è stato spostato, quindi chiama il metodo `ComputeMatrix` nuovamente e invalida l'area.

Il `ComputeMatrix` metodo determina la matrice da questi tre punti in cui è inclusa. La matrice chiamato `A` trasformazioni di un rettangolo un pixel in un parallelogramma basato sui tre punti, mentre la trasformazione di scala chiamata `S` ridimensiona la bitmap a un rettangolo quadrata di un pixel. La matrice composta è `S` × `A`:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL)
    {
        // Scale transform
        SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

        // Affine transform
        SKMatrix A = new SKMatrix
        {
            ScaleX = ptUR.X - ptUL.X,
            SkewY = ptUR.Y - ptUL.Y,
            SkewX = ptLL.X - ptUL.X,
            ScaleY = ptLL.Y - ptUL.Y,
            TransX = ptUL.X,
            TransY = ptUL.Y,
            Persp2 = 1
        };

        SKMatrix result;
        SKMatrix.Concat(ref result, A, S);
        return result;
    }
    ...
}
```

Infine, il `PaintSurface` metodo esegue il rendering della bitmap in base a tale matrice, viene visualizzata la matrice nella parte inferiore dello schermo ed esegue il rendering i punti di tocco tre sugli angoli della bitmap:

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap using the matrix
        canvas.Save();
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(matrix);

        matrixDisplay.Paint(canvas, matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));

        // Display the touchpoints
        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }
  }
```

La schermata di iOS seguente mostra la bitmap quando la pagina viene caricata, mentre le altre due schermate viene visualizzato dopo alcuni modifica:

[![](matrix-images/showaffinematrix-small.png "Schermata di triplo della pagina Mostra matrice Affine")](matrix-images/showaffinematrix-large.png#lightbox "tripla schermata della pagina Mostra matrice Affine")

Anche se sembra come se i punti di tocco trascinare gli angoli dell'immagine bitmap, che è solo un effetto ottico. La matrice calcolata dai punti di tocco Trasforma la bitmap in modo che gli angoli coincidano con i punti di tocco.

È più semplice per gli utenti di spostare, ridimensionare e ruotare bitmap non trascinando gli angoli, ma usando uno o due dita direttamente sull'oggetto per trascinare, avvicinare le dita e ruotare. Questo aspetto è illustrato nell'articolo successivo [modifica tocco](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md).

### <a name="the-reason-for-the-3-by-3-matrix"></a>Il motivo per la matrice 3 per 3

È possibile prevedere che un sistema grafico bidimensionale richiede solo una matrice di trasformazione di 2 per 2:

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

Questo metodo funziona per la scala, rotazione e inclinazione anche, ma non è in grado di più elementare di trasformazioni, ovvero la traduzione.

Il problema è che la matrice 2 per 2 rappresenta un *lineare* trasforma in due dimensioni. Una trasformazione lineare mantiene alcune operazioni aritmetiche di base, ma una delle implicazioni è che una trasformazione lineare non modifica mai il punto (0, 0). Una trasformazione lineare rende impossibile traduzione.

In tre dimensioni, una matrice di trasformazione lineare simile al seguente:

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

La cella con etichettata `SkewXY` indica che il valore inclina la coordinata X in base ai valori y, la cella `SkewXZ` significa che il valore inclina la coordinata X in base ai valori di Z; e i valori dello sfasamento dei segnali in modo analogo per gli altri `Skew` celle.

È possibile limitare la matrice di trasformazione 3D a un piano bidimensionale impostando `SkewZX` e `SkewZY` su 0, e `ScaleZ` su 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

Se la grafica bidimensionale viene disegnata interamente nel piano nello spazio 3D dove Z è uguale a 1, la moltiplicazione di trasformazione simile al seguente:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

Nulla sul piano bidimensionale dove Z è uguale a 1, ma la `SkewXZ` e `SkewYZ` celle diventeranno i fattori di conversione bidimensionale.

Si tratta come una trasformazione lineare tridimensionale funge da una trasformazione non lineare bidimensionale. (Per analogia, trasformazioni nella grafica 3D sono basate su una matrice 4 per 4).

Il `SKMatrix` struttura SkiaSharp definisce le proprietà per la terza riga:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

Valore diverso da zero `Persp0` e `Persp1` risultato nelle trasformazioni che si spostano gli oggetti fuori dal piano bidimensionale dove Z è uguale a 1. Cosa succede quando gli oggetti vengono spostati che il piano viene descritta nell'articolo su [trasformazioni affini Non](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md).


## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
