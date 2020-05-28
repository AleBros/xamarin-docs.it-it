---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e8d11add988828fa4e26d3f6728dd0b4319b3630
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84133302"
---
# <a name="matrix-transforms-in-skiasharp"></a>Trasformazioni matrice in SkiaSharp

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Approfondimenti sulle trasformazioni SkiaSharp con la matrice di trasformazione versatile_

Tutte le trasformazioni applicate all' `SKCanvas` oggetto vengono consolidate in una singola istanza della [`SKMatrix`](xref:SkiaSharp.SKMatrix) struttura. Si tratta di una matrice di trasformazione standard 3 per 3 simile a quella di tutti i moderni sistemi grafici 2D.

Come si è visto, è possibile usare le trasformazioni in SkiaSharp senza conoscere la matrice di trasformazione, ma la matrice di trasformazione è importante dal punto di vista teorico ed è fondamentale quando si usano le trasformazioni per modificare i percorsi o per gestire un input tocco complesso, entrambi illustrati in questo articolo e il successivo.

![](matrix-images/matrixtransformexample.png "A bitmap subjected to an affine transform")

La matrice di trasformazione corrente applicata a `SKCanvas` è disponibile in qualsiasi momento accedendo alla proprietà di sola lettura [`TotalMatrix`](xref:SkiaSharp.SKCanvas.TotalMatrix) . È possibile impostare una nuova matrice di trasformazione usando il [`SetMatrix`](xref:SkiaSharp.SKCanvas.SetMatrix(SkiaSharp.SKMatrix)) Metodo ed è possibile ripristinare i valori predefiniti per la matrice di trasformazione chiamando [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix) .

L'unico altro `SKCanvas` membro che funziona direttamente con la trasformazione matrice dell'area di disegno è [`Concat`](xref:SkiaSharp.SKCanvas.Concat(SkiaSharp.SKMatrix@)) che concatena due matrici moltiplicando insieme.

La matrice di trasformazione predefinita è la matrice di identità ed è costituita da 1 nelle celle diagonali e da 0 in qualsiasi altra posizione:

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

È possibile creare una matrice di identità usando il [`SKMatrix.MakeIdentity`](xref:SkiaSharp.SKMatrix.MakeIdentity) metodo statico:

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

Il `SKMatrix` costruttore predefinito non *not* restituisce una matrice di identità. Restituisce una matrice con tutte le celle impostate su zero. Non usare il costruttore a meno che non `SKMatrix` si preveda di impostare manualmente tali celle.

Quando SkiaSharp esegue il rendering di un oggetto grafico, ogni punto (x, y) viene convertito in modo efficace in una matrice 1 per 3 con 1 nella terza colonna:

<pre>
| x  y  1 |
</pre>

Questa matrice 1 per 3 rappresenta un punto tridimensionale con la coordinata Z impostata su 1. Esistono motivi matematici, illustrati più avanti, perché una trasformazione matrice bidimensionale richiede l'utilizzo di tre dimensioni. È possibile pensare a questa matrice 1 per 3 come rappresentare un punto in un sistema di coordinate 3D, ma sempre sul piano 2D dove Z è uguale a 1.

Questa matrice 1 per 3 viene moltiplicata per la matrice di trasformazione e il risultato è il punto sottoposto a rendering nell'area di disegno:

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

Utilizzando la moltiplicazione di matrici standard, i punti convertiti sono i seguenti:

`x' = x`

`y' = y`

`z' = 1`

Si tratta della trasformazione predefinita.

Quando il `Translate` metodo viene chiamato sull' `SKCanvas` oggetto, gli `tx` argomenti e `ty` per il `Translate` Metodo diventano le prime due celle nella terza riga della matrice di trasformazione:

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

La moltiplicazione è ora la seguente:

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

Ecco le formule di trasformazione:

`x' = x + tx`

`y' = y + ty`

I fattori di scala hanno un valore predefinito di 1. Quando si chiama il `Scale` metodo su un nuovo `SKCanvas` oggetto, la matrice di trasformazione risultante contiene gli `sx` `sy` argomenti e nelle celle diagonali:

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

Le formule di trasformazione sono le seguenti:

`x' = sx · x`

`y' = sy · y`

La matrice di trasformazione dopo la chiamata `Skew` di contiene i due argomenti nelle celle della matrice adiacenti ai fattori di scalabilità:

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

Le formule di trasformazione sono:

`x' = x + xSkew · y`

`y' = ySkew · x + y`

Per una chiamata a `RotateDegrees` o `RotateRadians` per un angolo di α, la matrice di trasformazione è la seguente:

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

Ecco le formule di trasformazione:

`x' = cos(α) · x - sin(α) · y`

`y' = sin(α) · x - cos(α) · y`

Quando α è 0 gradi, è la matrice di identità. Quando α è 180 gradi, la matrice di trasformazione è la seguente:

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

Una rotazione di 180 gradi equivale a capovolgere un oggetto orizzontalmente e verticalmente, operazione eseguita anche impostando fattori di scala di-1.

Tutti questi tipi di trasformazioni sono classificati come trasformazioni *affini* . Le trasformazioni affini non coinvolgono mai la terza colonna della matrice, che rimane in corrispondenza dei valori predefiniti 0, 0 e 1. L'articolo [**trasformazioni non affini**](non-affine.md) illustra le trasformazioni non affini.

## <a name="matrix-multiplication"></a>Moltiplicazione di matrici

Un vantaggio significativo nell'uso della matrice di trasformazione è che le trasformazioni composite possono essere ottenute dalla moltiplicazione di matrici, che è spesso definita nella documentazione di SkiaSharp come *concatenazione*. Molti dei metodi correlati alla trasformazione in `SKCanvas` fanno riferimento a "pre-concatenazione" o "pre-Concat". Questo si riferisce all'ordine di moltiplicazione, che è importante perché la moltiplicazione di matrici non è commutativa.

Ad esempio, la documentazione relativa al [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) metodo indica che "pre-cat the current Matrix con la traduzione specificata", mentre la documentazione relativa al [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) metodo indica che "pre-cat the current Matrix con la scala specificata".

Ciò significa che la trasformazione specificata dalla chiamata al metodo è il moltiplicatore (l'operando sinistro) e la matrice di trasformazione corrente è multiplicand (l'operando destro).

Si supponga che `Translate` venga chiamato seguito da `Scale` :

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

La `Scale` trasformazione viene moltiplicata per la `Translate` trasformazione per la matrice di trasformazione composita:

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale`potrebbe essere chiamato prima di procedere `Translate` come segue:

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

In tal caso, l'ordine della moltiplicazione viene invertito e i fattori di scalabilità vengono applicati in modo efficace ai fattori di traduzione:

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

Ecco il `Scale` metodo con un punto pivot:

```csharp
canvas.Scale(sx, sy, px, py);
```

Questa operazione equivale alle seguenti chiamate di conversione e scalabilità:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Le tre matrici di trasformazione vengono moltiplicate in ordine inverso rispetto al modo in cui i metodi vengono visualizzati nel codice:

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

## <a name="the-skmatrix-structure"></a>Struttura SKMatrix

La `SKMatrix` struttura definisce nove proprietà di lettura/scrittura di tipo `float` corrispondenti alle nove celle della matrice di trasformazione:

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix`definisce anche una proprietà denominata [`Values`](xref:SkiaSharp.SKMatrix.Values) di tipo `float[]` . Questa proprietà può essere usata per impostare o ottenere i nove valori in un unico scatto nell'ordine `ScaleX` ,,,,, `SkewX` `TransX` `SkewY` `ScaleY` `TransY` , `Persp0` , `Persp1` e `Persp2` .

Le `Persp0` `Persp1` celle, e `Persp2` sono descritte nell'articolo [**trasformazioni non affini**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md). Se queste celle hanno i valori predefiniti 0, 0 e 1, la trasformazione viene moltiplicata per un punto di coordinate simile al seguente:

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

`x' = ScaleX · x + SkewX · y + TransX`

`y' = SkewX · x + ScaleY · y + TransY`

`z' = 1`

Si tratta dell'intera trasformazione affine bidimensionale. La trasformazione affine conserva le linee parallele, il che significa che un rettangolo non viene mai trasformato in un valore diverso da un parallelogramma.

La `SKMatrix` struttura definisce diversi metodi statici per la creazione di `SKMatrix` valori. Tutti i `SKMatrix` valori restituiti:

- [`MakeTranslation`](xref:SkiaSharp.SKMatrix.MakeTranslation(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single,System.Single,System.Single))con un punto di perno
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single))per un angolo in radianti
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single,System.Single,System.Single))per un angolo in radianti con un punto di perno
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single))
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single,System.Single,System.Single))con un punto di perno
- [`MakeSkew`](xref:SkiaSharp.SKMatrix.MakeSkew(System.Single,System.Single))

`SKMatrix`definisce inoltre diversi metodi statici che concatenano due matrici, il che significa moltiplicarli. Questi metodi sono denominati [`Concat`](xref:SkiaSharp.SKMatrix.Concat*) , [`PostConcat`](xref:SkiaSharp.SKMatrix.PostConcat*) e e sono disponibili [`PreConcat`](xref:SkiaSharp.SKMatrix.PreConcat*) due versioni di ognuna. Questi metodi non restituiscono valori. al contrario, fanno riferimento ai `SKMatrix` valori esistenti tramite `ref` argomenti. Nell'esempio seguente,, `A` `B` e `R` (per "result") sono tutti `SKMatrix` valori.

I due `Concat` metodi vengono chiamati come segue:

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

Che eseguono la moltiplicazione seguente:

`R = B × A`

Gli altri metodi hanno solo due parametri. Il primo parametro viene modificato e, al ritorno dalla chiamata al metodo, contiene il prodotto delle due matrici. I due `PostConcat` metodi vengono chiamati come segue:

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

Queste chiamate eseguono le operazioni seguenti:

`A = A × B`

I due `PreConcat` metodi sono simili:

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

Queste chiamate eseguono le operazioni seguenti:

`A = B × A`

Le versioni di questi metodi con tutti gli `ref` argomenti sono leggermente più efficienti nella chiamata alle implementazioni sottostanti, ma potrebbe essere confuso per qualcuno che legge il codice e presupponendo che qualsiasi elemento con un `ref` argomento venga modificato dal metodo. Inoltre, è spesso utile passare un argomento che è il risultato di uno dei `Make` metodi, ad esempio:

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

Viene creata la matrice seguente:

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

Si tratta della trasformazione di scala moltiplicata per la trasformazione translate. In questo caso particolare, la `SKMatrix` struttura fornisce un collegamento con un metodo denominato [`SetScaleTranslate`](xref:SkiaSharp.SKMatrix.SetScaleTranslate(System.Single,System.Single,System.Single,System.Single)) :

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

Questa è una delle poche volte in cui è possibile usare il costruttore in modo sicuro `SKMatrix` . Il `SetScaleTranslate` metodo imposta tutte le nove celle della matrice. È anche possibile usare il `SKMatrix` costruttore con i `Rotate` metodi statici e `RotateDegrees` :

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

Questi metodi *non* concatenano una trasformazione di rotazione a una trasformazione esistente. I metodi impostano tutte le celle della matrice. Sono funzionalmente identici ai `MakeRotation` metodi e, `MakeRotationDegrees` ad eccezione del fatto che non creano un'istanza del `SKMatrix` valore.

Si supponga di avere un `SKPath` oggetto che si desidera visualizzare, ma si preferisce che abbia un orientamento leggermente diverso o un punto centrale diverso. È possibile modificare tutte le coordinate di tale percorso chiamando il [`Transform`](xref:SkiaSharp.SKPath.Transform(SkiaSharp.SKMatrix)) metodo di `SKPath` con un `SKMatrix` argomento. Nella pagina **trasformazione percorso** viene illustrato come eseguire questa operazione. La [`PathTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs) classe fa riferimento all' `HendecagramPath` oggetto in un campo, ma usa il relativo costruttore per applicare una trasformazione a tale percorso:

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

L' `HendecagramPath` oggetto ha un centro in corrispondenza di (0, 0) e gli 11 punti della stella si estendono verso l'esterno dal centro di 100 unità in tutte le direzioni. Ciò significa che il percorso contiene coordinate sia positive che negative. La pagina **trasformazione percorso** preferisce usare una stella tre volte più grande e con tutte le coordinate positive. Inoltre, non è necessario un punto della stella per puntare verso l'alto. Desidera invece un punto della stella da puntare verso il basso. (Poiché la stella ha 11 punti, non può avere entrambi). A tale scopo, è necessario ruotare il asterisco di 360 gradi diviso per 22.

Il costruttore compila un `SKMatrix` oggetto da tre trasformazioni separate usando il `PostConcat` metodo con il modello seguente, dove A, B e C sono istanze di `SKMatrix` :

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

Si tratta di una serie di moltiplicazioni successive, pertanto il risultato è il seguente:

`A × B × C`

Le moltiplicazioni consecutive aiutano a comprendere il funzionamento di ogni trasformazione. La trasformazione scala aumenta le dimensioni delle coordinate del tracciato per un fattore 3, quindi le coordinate variano da – 300 a 300. La trasformazione ruota ruota la stella intorno all'origine. La trasformazione translate sposta quindi il valore di 300 pixel a destra e in basso, in modo che tutte le coordinate diventino positive.

Sono presenti altre sequenze che producono la stessa matrice. Eccone un altro:

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

In questo modo, il tracciato viene ruotato per primo al centro e quindi convertito 100 pixel a destra e in basso, in modo che tutte le coordinate siano positive. La stella viene quindi aumentata di dimensione rispetto al nuovo angolo superiore sinistro, ovvero il punto (0,0).

Il `PaintSurface` gestore può semplicemente eseguire il rendering del percorso:

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

[![](matrix-images/pathtransform-small.png "Triple screenshot of the Path Transform page")](matrix-images/pathtransform-large.png#lightbox "Triple screenshot of the Path Transform page")

Il costruttore di questo programma applica la matrice al percorso con la chiamata seguente:

```csharp
transformedPath.Transform(matrix);
```

Il percorso non *mantiene questa* matrice come proprietà. Viene invece applicata la trasformazione a tutte le coordinate del percorso. Se `Transform` viene chiamato di nuovo, la trasformazione viene nuovamente applicata e l'unico modo per tornare indietro consiste nell'applicare un'altra matrice che annulla la trasformazione. Fortunatamente, la `SKMatrix` struttura definisce un [`TryInvert`](xref:SkiaSharp.SKMatrix.TryInvert*) metodo che ottiene la matrice che inverte una data matrice:

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

Il metodo viene chiamato `TryInverse` perché non tutte le matrici sono invertibili, ma una matrice non invertibile non è probabilmente utilizzata per una trasformazione grafica.

È anche possibile applicare una trasformazione matrice a un `SKPoint` valore, a una matrice di punti, a `SKRect` o anche a un solo numero all'interno del programma. La `SKMatrix` struttura supporta queste operazioni con una raccolta di metodi che iniziano con la parola `Map` , ad esempio:

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

Se si usa l'ultimo metodo, tenere presente che la `SKRect` struttura non è in grado di rappresentare un rettangolo ruotato. Il metodo è utile solo per un `SKMatrix` valore che rappresenta la traduzione e la scalabilità.

## <a name="interactive-experimentation"></a>Sperimentazione interattiva

Un modo per ottenere un'idea per la trasformazione affine consiste nello spostarsi in modo interattivo di tre angoli di una bitmap sullo schermo e visualizzare i risultati della trasformazione. Si tratta dell'idea alla base della pagina **Mostra matrice affine** . Questa pagina richiede altre due classi che vengono usate anche in altre dimostrazioni:

La [`TouchPoint`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs) classe Visualizza un cerchio trasparente che può essere trascinato intorno allo schermo. `TouchPoint`richiede che un `SKCanvasView` o un elemento padre di un oggetto `SKCanvasView` disponga dell'oggetto [`TouchEffect`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs) collegato. Impostare la proprietà `Capture` su `true`. Nel `TouchAction` gestore eventi il programma deve chiamare il `ProcessTouchEvent` metodo in `TouchPoint` per ogni `TouchPoint` istanza. Il metodo restituisce `true` se l'evento Touch ha causato lo stato del punto di tocco. Il `PaintSurface` gestore deve inoltre chiamare il `Paint` metodo in ogni `TouchPoint` istanza, passando all' `SKCanvas` oggetto.

`TouchPoint`viene illustrato un modo comune in cui un oggetto visivo SkiaSharp può essere incapsulato in una classe separata. La classe può definire proprietà per specificare le caratteristiche dell'oggetto visivo e un metodo denominato `Paint` con un `SKCanvas` argomento può eseguirne il rendering.

La `Center` proprietà di `TouchPoint` indica la posizione dell'oggetto. Questa proprietà può essere impostata per inizializzare la posizione; la proprietà viene modificata quando l'utente trascina il cerchio intorno all'area di disegno.

La **pagina mostra matrice affine** richiede anche la [`MatrixDisplay`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs) classe. Questa classe Visualizza le celle di un `SKMatrix` oggetto. Dispone di due metodi pubblici: `Measure` per ottenere le dimensioni della matrice di cui è stato eseguito il rendering e `Paint` per visualizzarlo. La classe contiene una `MatrixPaint` proprietà di tipo `SKPaint` che può essere sostituita con una dimensione o un colore del carattere diverso.

Il file [**ShowAffineMatrixPage. XAML**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml) crea un'istanza di `SKCanvasView` e connette un oggetto `TouchEffect` . Il file code-behind [**ShowAffineMatrixPage.XAML.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs) crea tre `TouchPoint` oggetti e li imposta su posizioni corrispondenti a tre angoli di una bitmap che viene caricata da una risorsa incorporata:

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
        {
            bitmap = SKBitmap.Decode(stream);
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

Una matrice di affinità è definita in modo univoco da tre punti. I tre `TouchPoint` oggetti corrispondono agli angoli superiore sinistro, superiore destro e inferiore sinistro della bitmap. Poiché una matrice affine è in grado di trasformare un rettangolo in un parallelogramma, il quarto punto è implicito per gli altri tre. Il costruttore termina con una chiamata a `ComputeMatrix` , che calcola le celle di un `SKMatrix` oggetto da questi tre punti.

Il `TouchAction` gestore chiama il `ProcessTouchEvent` metodo di ogni `TouchPoint` . Il `scale` valore viene convertito dalle Xamarin.Forms coordinate ai pixel:

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

Se ne `TouchPoint` è stato spostato uno, il metodo chiama `ComputeMatrix` di nuovo e invalida la superficie.

Il `ComputeMatrix` metodo determina la matrice implicita in questi tre punti. La matrice denominata trasforma `A` un rettangolo quadrato da un pixel in un parallelogramma in base ai tre punti, mentre la trasformazione scala denominata `S` Ridimensiona la bitmap a un rettangolo quadrato a un pixel. La matrice composita è `S` × `A` :

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

        SKMatrix result = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref result, A, S);
        return result;
    }
    ...
}
```

Infine, il `PaintSurface` metodo esegue il rendering della bitmap in base a tale matrice, Visualizza la matrice nella parte inferiore dello schermo ed esegue il rendering dei punti di tocco nei tre angoli della bitmap:

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

La schermata iOS seguente mostra la bitmap quando la pagina viene caricata per la prima volta, mentre le altre due schermate le visualizzano dopo alcune modifiche:

[![](matrix-images/showaffinematrix-small.png "Triple screenshot of the Show Affine Matrix page")](matrix-images/showaffinematrix-large.png#lightbox "Triple screenshot of the Show Affine Matrix page")

Sebbene sembri che i punti di tocco trascinano gli angoli della bitmap, si tratta solo di un'illusione. La matrice calcolata dai punti di tocco trasforma la bitmap in modo che gli angoli coincidano con i punti di tocco.

È più naturale per gli utenti spostare, ridimensionare e ruotare le bitmap senza trascinare gli angoli, ma utilizzando una o due dita direttamente sull'oggetto per trascinare, pizzicare e ruotare. Questa operazione viene illustrata nel prossimo articolo [**manipolazione del tocco**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md).

## <a name="the-reason-for-the-3-by-3-matrix"></a>Motivo della matrice 3 per 3

È probabile che un sistema grafico bidimensionale richieda solo una matrice di trasformazione 2 per 2:

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

Questo approccio funziona per la scalabilità, la rotazione e persino l'inclinazione, ma non supporta la maggior parte delle trasformazioni, ovvero la traduzione.

Il problema è che la matrice 2 per 2 rappresenta una trasformazione *lineare* in due dimensioni. Una trasformazione lineare conserva alcune operazioni aritmetiche di base, ma una delle implicazioni è che una trasformazione lineare non modifica mai il punto (0, 0). Una trasformazione lineare rende impossibile la traduzione.

In tre dimensioni, una matrice di trasformazione lineare ha un aspetto simile al seguente:

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

La cella con etichetta `SkewXY` significa che il valore inclina la coordinata x in base ai valori di Y. la cella `SkewXZ` indica che il valore inclina la coordinata x in base ai valori della Z e che i valori vengono inclinati in modo analogo per le altre `Skew` celle.

È possibile limitare questa matrice di trasformazione 3D a un piano bidimensionale impostando `SkewZX` e `SkewZY` su 0 e `ScaleZ` su 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

Se la grafica bidimensionale viene disegnata interamente sul piano nello spazio 3D in cui Z è uguale a 1, la moltiplicazione della trasformazione è simile alla seguente:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

Tutto rimane sul piano bidimensionale in cui Z è uguale a 1, ma le `SkewXZ` `SkewYZ` celle e diventano effettivamente fattori di traduzione bidimensionali.

Questo è il modo in cui una trasformazione lineare tridimensionale funge da trasformazione non lineare bidimensionale. Analogamente, le trasformazioni in grafica 3D sono basate su una matrice 4 per 4.

La `SKMatrix` struttura in SkiaSharp definisce le proprietà per la terza riga:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

I valori diversi da zero di `Persp0` e `Persp1` generano trasformazioni che spostano gli oggetti dal piano bidimensionale dove Z è uguale a 1. Cosa accade quando questi oggetti vengono spostati di nuovo su tale piano è trattato nell'articolo sulle [**trasformazioni non affini**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md).

## <a name="related-links"></a>Collegamenti correlati

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
