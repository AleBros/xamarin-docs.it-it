---
title: Trasformazioni con matrice in SkiaSharp
description: Questo articolo più profondo approfondisce le trasformazioni di SkiaSharp con la matrice di trasformazione versatili e questo concetto è illustrato con esempio di codice.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4
author: davidbritch
ms.author: dabritch
ms.date: 04/12/2017
ms.openlocfilehash: 6e78e3930ec731bc970ef39ddb7fe7051d62f63a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770435"
---
# <a name="matrix-transforms-in-skiasharp"></a>Trasformazioni con matrice in SkiaSharp

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Approfondimenti di trasformazioni di SkiaSharp con la matrice di trasformazione versatili_

Tutte le trasformazioni applicate per il `SKCanvas` oggetto vengono consolidati in una singola istanza del [ `SKMatrix` ](xref:SkiaSharp.SKMatrix) struttura. Si tratta di una matrice di trasformazione di 3 per 3 standard simile a quelle di tutti i sistemi di grafica 2D moderne.

Come si è visto, è possibile usare trasformazioni di SkiaSharp senza conoscere la trasformazione di matrice, ma la matrice di trasformazione è importante dal punto di vista teorico, ed è essenziale quando si usa trasformazioni per modificare i percorsi o per la gestione dell'input tocco complesse, entrambi cui vengono illustrati in questo articolo e la successiva.

![](matrix-images/matrixtransformexample.png "Una bitmap sottoposta a una trasformazione affine")

La matrice di trasformazione corrente applicata per il `SKCanvas` è disponibile in qualsiasi momento mediante l'accesso di sola lettura [ `TotalMatrix` ](xref:SkiaSharp.SKCanvas.TotalMatrix) proprietà. È possibile impostare una nuova matrice di trasformazione usando il [ `SetMatrix` ](xref:SkiaSharp.SKCanvas.SetMatrix(SkiaSharp.SKMatrix)) metodo ed è possibile ripristinare tale matrice di trasformazione sui valori predefiniti chiamando [ `ResetMatrix` ](xref:SkiaSharp.SKCanvas.ResetMatrix).

Gli unici altri `SKCanvas` è membro che interagisce direttamente con trasformazione di matrice dell'area di disegno [ `Concat` ](xref:SkiaSharp.SKCanvas.Concat(SkiaSharp.SKMatrix@)) che consente di concatenare due matrici moltiplicando nel loro insieme.

Matrice di trasformazione predefinito è la matrice identità ed è costituito da 1 in diagonale celle e qualsiasi altro punto di 0:

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

È possibile creare una matrice di identità usando il metodo statico [ `SKMatrix.MakeIdentity` ](xref:SkiaSharp.SKMatrix.MakeIdentity) metodo:

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

Il `SKMatrix` il costruttore predefinito non *non* restituire una matrice di identità. Restituisce una matrice con tutte le celle impostate su zero. Non usare il `SKMatrix` costruttore a meno che non si prevede di impostare manualmente queste celle.

Quando SkiaSharp esegue il rendering di un oggetto grafico, ogni punto (x, y) viene convertito in modo efficace in una matrice da 1 a 3 con valore 1 nella terza colonna:

<pre>
| x  y  1 |
</pre>

Questa matrice dal-1-3 rappresenta un punto tridimensionale con la coordinata Z impostata su 1. Esistono motivi matematici (illustrati più avanti) perché una trasformazione di matrice bidimensionale richiede l'utilizzo in tre dimensioni. È possibile considerare questa matrice dal-1-3 che rappresenti un punto in un sistema di coordinate 3D, ma sempre sul piano 2D in cui Z è uguale a 1.

Questa matrice dal-1-3 viene quindi moltiplicata per la matrice di trasformazione e il risultato è il punto in cui viene eseguito il rendering nell'area di disegno:

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

I punti convertiti utilizzando moltiplicazione di matrici standard, sono i seguenti:

`x' = x`

`y' = y`

`z' = 1`

Che è la trasformazione predefinito.

Quando la `Translate` metodo viene chiamato sul `SKCanvas` oggetto, il `tx` e `ty` argomenti per il `Translate` metodo diventano le prime due celle nella terza riga della matrice di trasformazione:

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

La moltiplicazione viene ora come indicato di seguito:

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

Di seguito sono le formule di trasformazione:

`x' = x + tx`

`y' = y + ty`

Fattori di scalabilità hanno un valore predefinito pari a 1. Quando si chiama il `Scale` su un nuovo metodo `SKCanvas` dell'oggetto, matrice di trasformazione risultante contiene il `sx` e `sy` argomenti nelle celle diagonale:

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

Le formule di trasformazione sono i seguenti:

`x' = sx · x`

`y' = sy · y`

Matrice di trasformazione dopo la chiamata `Skew` contiene i due argomenti nelle celle della matrice accanto ai fattori di scala:

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

Le formule di trasformazione sono:

`x' = x + xSkew · y`

`y' = ySkew · x + y`

Per una chiamata a `RotateDegrees` o `RotateRadians` per un angolo di α, la matrice di trasformazione è come segue:

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

Di seguito sono le formule di trasformazione:

`x' = cos(α) · x - sin(α) · y`

`y' = sin(α) · x - cos(α) · y`

Una volta α 0 gradi, è la matrice di identità. Una volta α 180 gradi, la matrice di trasformazione è come indicato di seguito:

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

Una rotazione di 180 gradi equivale a capovolgimento di un oggetto in senso orizzontale e verticale, che avviene anche da fattori di scala di impostazione-1.

Tutti questi tipi di trasformazioni sono classificati come *affini* Trasforma. Le trasformazioni affini prevedono mai la terza colonna della matrice, che non cambia i valori predefiniti pari a 0, 0 e 1. L'articolo [ **trasformazioni Non affini** ](non-affine.md) descrive le trasformazioni non affini.

## <a name="matrix-multiplication"></a>Moltiplicazione di matrici

Un vantaggio importante di utilizzando la matrice di trasformazione è che le trasformazioni composite possono essere ottenute dalla moltiplicazione di matrici, cui si fa spesso riferimento nella documentazione di SkiaSharp *concatenazione*. Molti dei metodi correlati alla trasformazione in `SKCanvas` fare riferimento a "pre-concatenazione" o "pre-concat." Si riferisce all'ordine di moltiplicazione, è importante in quanto la moltiplicazione non è commutativa.

Ad esempio, la documentazione per il [ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) metodo viene indicato che il "Pre-concats della matrice corrente con la traslazione specificata," durante la documentazione per il [ `Scale` ](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) metodo viene indicato che il "Pre-concats della matrice corrente con la scala specificata."

Ciò significa che la trasformazione specificata dalla chiamata al metodo è il moltiplicatore (l'operando a sinistra) e la matrice di trasformazione corrente è il moltiplicando (l'operando destro).

Si supponga che `Translate` viene chiamato seguita da `Scale`:

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

Il `Scale` transform viene moltiplicato il `Translate` Trasforma per la matrice di trasformazione composito:

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale` può essere chiamato prima `Translate` simile al seguente:

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

Di seguito è riportato il `Scale` metodo con un punto di perno:

```csharp
canvas.Scale(sx, sy, px, py);
```

Ciò equivale alle chiamate di traslazione e la scalabilità seguenti:

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Le tre matrici trasformazione vengono moltiplicate in ordine inverso dal modo in cui i metodi vengono visualizzati nel codice:

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

## <a name="the-skmatrix-structure"></a>La struttura SKMatrix

Il `SKMatrix` struttura definisce nove proprietà di lettura/scrittura di tipo `float` corrispondente a nove celle della matrice di trasformazione:

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix` definisce anche una proprietà denominata [ `Values` ](xref:SkiaSharp.SKMatrix.Values) typu `float[]`. Questa proprietà può essere utilizzata per impostare o ottenere i valori in un unico passaggio nell'ordine nove `ScaleX`, `SkewX`, `TransX`, `SkewY`, `ScaleY`, `TransY`, `Persp0`, `Persp1`, e `Persp2`.

Il `Persp0`, `Persp1`, e `Persp2` le celle sono descritti nell'articolo [ **trasformazioni Non affini**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md). Se queste celle contengono valori predefiniti pari a 0, 0 e 1, la trasformazione viene moltiplicata per il punto di coordinata simile al seguente:

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

`x' = ScaleX · x + SkewX · y + TransX`

`y' = SkewX · x + ScaleY · y + TransY`

`z' = 1`

Si tratta di trasformazione affine bidimensionale completata. La trasformazione affine mantiene linee parallele, il che significa che un rettangolo mai viene trasformato in un valore diverso da un parallelogramma.

Il `SKMatrix` struttura definisce diversi metodi statici per creare `SKMatrix` valori. Questi restituiscono tutti `SKMatrix` valori:

- [`MakeTranslation`](xref:SkiaSharp.SKMatrix.MakeTranslation(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single,System.Single,System.Single)) con un punto di perno
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single)) per un angolo, espresso in radianti
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single,System.Single,System.Single)) per un angolo, espresso in radianti con un punto di perno
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single))
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single,System.Single,System.Single)) con un punto di perno
- [`MakeSkew`](xref:SkiaSharp.SKMatrix.MakeSkew(System.Single,System.Single))

`SKMatrix` definisce anche diversi metodi statici che consentono di concatenare due matrici, ovvero moltiplicati insieme. Questi metodi sono denominati [ `Concat` ](xref:SkiaSharp.SKMatrix.Concat*), [ `PostConcat` ](xref:SkiaSharp.SKMatrix.PostConcat*), e [ `PreConcat` ](xref:SkiaSharp.SKMatrix.PreConcat*), e sono disponibili due versioni della ognuno. Questi metodi non hanno nessun valore restituito; al contrario, fanno riferimento a esistenti `SKMatrix` i valori tramite `ref` argomenti. Nell'esempio riportato di seguito `A`, `B`, e `R` (per "risultato") sono tutti `SKMatrix` valori.

I due `Concat` metodi vengono chiamati simile al seguente:

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

Che eseguono la moltiplicazione seguente:

`R = B × A`

Gli altri metodi hanno solo due parametri. Il primo parametro è modificato e al ritorno dalla chiamata al metodo, contiene il prodotto di due matrici. I due `PostConcat` metodi vengono chiamati simile al seguente:

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

Queste chiamate di eseguono l'operazione seguente:

`A = A × B`

I due `PreConcat` metodi sono simili:

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

Queste chiamate di eseguono l'operazione seguente:

`A = B × A`

Le versioni di questi metodi con tutti i `ref` gli argomenti sono leggermente più efficienti chiamare le implementazioni sottostanti, ma potrebbe essere poco chiaro a chiunque la lettura del codice e supponendo che qualsiasi operazione in un `ref` sta modificando l'argomento il metodo. Inoltre, è spesso utile passare un argomento che è il risultato di uno del `Make` metodi, ad esempio:

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

Verrà creata la matrice seguente:

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

Questa è la trasformazione di scala moltiplicata per la trasformazione di traslazione. In questo caso particolare, il `SKMatrix` struttura fornisce un collegamento con un metodo denominato [ `SetScaleTranslate` ](xref:SkiaSharp.SKMatrix.SetScaleTranslate(System.Single,System.Single,System.Single,System.Single)):

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

Questo è uno del minor numero di volte quando è possibile usare in sicurezza i `SKMatrix` costruttore. Il `SetScaleTranslate` metodo imposta tutti i nove celle della matrice. È anche possibile usare la `SKMatrix` costruttore con il metodo statico `Rotate` e `RotateDegrees` metodi:

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

Questi metodi eseguono *non* concatenare una trasformazione di rotazione a una trasformazione esistente. I metodi di impostano tutte le celle della matrice. Sono funzionalmente identici per il `MakeRotation` e `MakeRotationDegrees` metodi ad eccezione del fatto che essi non creare un'istanza di `SKMatrix` valore.

Si supponga di avere un `SKPath` oggetto che si desidera visualizzare, ma si preferisce che disponga di un orientamento un po' diverso o un punto centrale diverso. È possibile modificare tutte le coordinate del percorso chiamando il [ `Transform` ](xref:SkiaSharp.SKPath.Transform(SkiaSharp.SKMatrix)) metodo `SKPath` con un `SKMatrix` argomento. Il **trasformare percorso** pagina viene illustrato come eseguire questa operazione. Il [ `PathTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs) classe riferimenti il `HendecagramPath` oggetto in un campo, ma usa il costruttore per applicare una trasformazione a tale percorso:

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

Il `HendecagramPath` oggetto dispone di un centro a (0, 0), e i punti di 11 della stella si estendono verso l'esterno da tale center dalle unità di 100 in tutte le direzioni. Ciò significa che il percorso contiene le coordinate positive e negative. Il **trasformare percorso** pagina preferisce lavorare con una stella a tre volte più ampio e con tutte le coordinate positive. Inoltre, non desidera un punto della stella in modo che punti verso l'alto. Vuole invece per un punto della stella in modo da puntare direttamente verso il basso. (Poiché la stella dispone di punti di 11, non può averle entrambe.) Ciò richiede la rotazione di stella di 360 gradi diviso per 22.

Il costruttore genera un `SKMatrix` oggetto da tre trasformazioni separate con il `PostConcat` metodo con il modello seguente, dove A, B e C sono istanze di `SKMatrix`:

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

Si tratta di una serie di moltiplicazioni successivi, in modo che il risultato è il seguente:

`A × B × C`

Facilitano la comprensione del funzionamento di ogni trasformazione moltiplicazioni consecutivi. Trasformazione di ridimensionamento aumenta la dimensione delle coordinate di percorso di un fattore pari a 3, in modo da variare le coordinate da –300 su 300. Trasformazione di rotazione consente di ruotare la stella intorno relativa origine. La trasformazione di traslazione passa quindi il pulsante destro del mouse 300 pixel e verso il basso, in modo che tutte le coordinate diventare positive.

Esistono altre sequenze che producono la stessa matrice. Ecco un altro:

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

Si ruota il percorso attorno al proprio centro prima di tutto e quindi lo converte 100 pixel a destra e verso il basso quindi tutte le coordinate sono positive. La stella viene quindi aumentata nelle dimensioni rispetto al relativo angolo superiore sinistro di nuovo, ovvero il punto (0, 0).

Il `PaintSurface` gestore di è sufficiente eseguire il rendering di questo percorso:

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

[![](matrix-images/pathtransform-small.png "Tripla screenshot della pagina del percorso trasformare")](matrix-images/pathtransform-large.png#lightbox "tripla screenshot della pagina del percorso Transform")

Il costruttore di questo programma si applica la matrice sul percorso con la chiamata seguente:

```csharp
transformedPath.Transform(matrix);
```

Il percorso non *non* conservare questa matrice come una proprietà. Al contrario, la trasformazione si applica a tutte le coordinate del percorso. Se `Transform` viene chiamato anche in questo caso, la trasformazione viene applicata nuovamente, e l'unico modo è possibile tornare indietro è applicando un'altra matrice che annulla la trasformazione. Per fortuna, il `SKMatrix` struttura definisce un [ `TryInvert` ](xref:SkiaSharp.SKMatrix.TryInvert*) metodo che ottiene la matrice che inverte una matrice specificata:

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

Viene chiamato il metodo `TryInverse` poiché non tutte le matrici sono invertibile o meno, ma che non possa essere usati per una trasformazione grafica una matrice non può essere invertita.

È inoltre possibile applicare una trasformazione di matrice a un `SKPoint` valore, una matrice di punti, un `SKRect`, o anche solo un numero singolo all'interno del programma. Il `SKMatrix` struttura supporta queste operazioni con una raccolta di metodi che iniziano con la parola `Map`, come i seguenti:

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

Se si usa l'ultimo metodo che, tenere presente che il `SKRect` struttura non è in grado di rappresentare un rettangolo ruotato. Il metodo risulta utile solo un `SKMatrix` valore che rappresenta la traduzione e la scalabilità.

## <a name="interactive-experimentation"></a>Sperimentazione interattiva

Un modo per avere un'idea di trasformazione affine è in modalità interattiva spostando tre angoli di una bitmap attorno alla schermata e visualizzare quali trasformazione risultante. Questo è l'idea alla base di **mostrare matrice Affine** pagina. Questa pagina richiede altre due classi che vengono usate anche nelle altre dimostrazioni:

Il [ `TouchPoint` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs) classe viene visualizzato un cerchio semitrasparente che può essere trascinato attorno alla schermata. `TouchPoint` richiede che un `SKCanvasView` o un elemento che è un elemento padre di un `SKCanvasView` dispone i [ `TouchEffect` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs) collegato. Impostare la proprietà `Capture` su `true`. Nel `TouchAction` gestore eventi, è necessario chiamare il programma di `ProcessTouchEvent` metodo nella `TouchPoint` per ogni `TouchPoint` istanza. Il metodo restituisce `true` se il punto di tocco lo spostamento ha generato l'evento di tocco. Inoltre, il `PaintSurface` gestore deve chiamare il `Paint` metodo in ogni `TouchPoint` istanza, passando a tale il `SKCanvas` oggetto.

`TouchPoint` viene illustrato un comune modo che un oggetto visivo SkiaSharp può essere incapsulato in una classe separata. La classe può definire le proprietà per specificare le caratteristiche dell'oggetto visivo e un metodo denominato `Paint` con un `SKCanvas` argomento possibile eseguirne il rendering.

Il `Center` proprietà di `TouchPoint` indica la posizione dell'oggetto. Questa proprietà può essere impostata per inizializzare la posizione. la proprietà viene modificata quando l'utente trascina il cerchio nell'area di lavoro.

Il **pagina Mostra matrice Affine** richiede inoltre il [ `MatrixDisplay` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs) classe. Questa classe consente di visualizzare le celle di un `SKMatrix` oggetto. Dispone di due metodi pubblici: `Measure` per ottenere le dimensioni della matrice sottoposta a rendering, e `Paint` per visualizzarlo. La classe contiene un `MatrixPaint` vlastnosti typu `SKPaint` che possono essere sostituiti per una dimensione diversa o un colore.

Il [ **ShowAffineMatrixPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml) file creare un'istanza il `SKCanvasView` e la collega un `TouchEffect`. Il [ **ShowAffineMatrixPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs) vengono creati tre file code-behind `TouchPoint` degli oggetti e quindi li imposta nelle posizioni corrispondenti a tre angoli di una bitmap che viene caricato da un embedded risorsa:

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

Una matrice affine è definita in modo univoco da tre punti. I tre `TouchPoint` oggetti corrispondono agli angoli superiore sinistro, superiore destro e inferiore sinistro della bitmap. Perché è in grado di trasformazione di un rettangolo in un parallelogramma solo una matrice affine, è implicito quarto punto le altre tre. Il costruttore si conclude con una chiamata a `ComputeMatrix`, che calcola le celle di un `SKMatrix` oggetto da questi tre punti.

Il `TouchAction` chiamate al gestore la `ProcessTouchEvent` metodo della ognuno `TouchPoint`. Il `scale` valore viene convertito da coordinate di Xamarin.Forms per pixel:

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

Eventuale `TouchPoint` è stato spostato, quindi viene chiamato il metodo `ComputeMatrix` nuovamente e invalida l'area.

Il `ComputeMatrix` metodo determina la matrice da questi tre punti in cui è inclusa. La matrice denominata `A` trasformazioni un rettangolo quadrato di un pixel in un parallelogramma basato sui tre punti, mentre la trasformazione di scala denominata `S` ridimensiona la bitmap per un rettangolo di un pixel quadrato. La matrice composta `S` × `A`:

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

Infine, il `PaintSurface` metodo esegue il rendering di bitmap basata su tale matrice, viene visualizzata la matrice nella parte inferiore della schermata ed esegue il rendering i punti di tocco nei tre angoli della bitmap:

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

Schermata iOS riportata di seguito viene illustrata la bitmap quando la pagina viene caricata, mentre le altre due schermate visualizzarla dopo alcune modifiche:

[![](matrix-images/showaffinematrix-small.png "Tripla screenshot della pagina mostrano matrice Affine")](matrix-images/showaffinematrix-large.png#lightbox "tripla screenshot della pagina mostrano matrice Affine")

Anche se sembra come se i punti di tocco trascinare gli angoli dell'immagine bitmap, che è solo un effetto ottico. La matrice calcolata dai punti di tocco Trasforma la mappa di bit in modo che gli angoli coincidano con i punti di tocco.

È più naturale per gli utenti di spostare, ridimensionare e ruotare le bitmap non trascinando gli angoli, ma con uno o due dita direttamente sull'oggetto a trascinamento, avvicinare le dita e ruotano. Questa procedura è illustrata nell'articolo successivo [ **Touch manipolazione**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md).

## <a name="the-reason-for-the-3-by-3-matrix"></a>Il motivo per la matrice 3 per 3

È possibile prevedere che un sistema di grafica bidimensionale richiede solo una matrice di trasformazione 2-da-2:

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

Questa opzione funziona per il ridimensionamento, rotazione e inclinazione anche, ma non è in grado di più elementare di trasformazioni, ovvero la traduzione.

Il problema è che la matrice 2 per 2 rappresenta un' *lineare* trasformare in due dimensioni. Una trasformazione lineare mantiene alcune operazioni aritmetiche di base, ma una delle implicazioni è che una trasformazione lineare non modifica mai il punto (0, 0). Una trasformazione lineare rende impossibili la traduzione.

In tre dimensioni, una matrice di trasformazione lineare aspetto simile al seguente:

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

La cella con l'etichetta `SkewXY` significa che il valore inclina la coordinata X in base ai valori y, la cella `SkewXZ` significa che il valore inclina la coordinata X in base ai valori di Z; e i valori inclinare allo stesso modo per gli altri `Skew` celle.

È possibile limitare la matrice di trasformazione 3D a un piano bidimensionale impostando `SkewZX` e `SkewZY` su 0, e `ScaleZ` su 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

Se la grafica bidimensionale viene disegnata interamente sul piano nello spazio 3D dove Z è uguale a 1, la moltiplicazione di trasformazione aspetto simile al seguente:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

Tutto ciò che rimane sul piano bidimensionale in cui Z è uguale a 1, ma il `SkewXZ` e `SkewYZ` celle diventeranno fattori traduzione bidimensionale.

Si tratta di come una trasformazione lineare tridimensionale funge da una trasformazione non lineare bidimensionale. (Per analogia, trasformazioni di grafica 3D si basano su una matrice 4 per 4).

Il `SKMatrix` struttura in SkiaSharp definisce le proprietà per la terza riga:

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

I valori di diverso da zero `Persp0` e `Persp1` comportare trasformazioni che si spostano oggetti disattivato il piano bidimensionale in cui Z è uguale a 1. Cosa succede quando gli oggetti vengono spostati indietro che il piano è descritto nell'articolo sul [ **trasformazioni Non affini**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md).

## <a name="related-links"></a>Collegamenti correlati

- [API di SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
