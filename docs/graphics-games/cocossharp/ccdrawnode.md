---
title: Disegno di geometria con CCDrawNode
description: Questo documento descrive CCDrawNode, che fornisce metodi per oggetti disegno di primitivi, ad esempio linee, cerchi e triangoli.
ms.prod: xamarin
ms.assetid: 46A3C3CE-74CC-4A3A-AB05-B694AE182ADB
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: b910e136366c429de8bd2ba1ac959882b4d7201d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123188"
---
# <a name="drawing-geometry-with-ccdrawnode"></a>Disegno di geometria con CCDrawNode

_`CCDrawNode` fornisce metodi per oggetti disegno di primitivi, ad esempio linee, cerchi e triangoli._

Il `CCDrawNode` classe in CocosSharp sono disponibili più metodi per il disegno di forme geometriche comune. Eredita dal `CCNode` classe e viene in genere aggiunto a `CCLayer` istanze. Questa guida illustra come usare `CCDrawNode` istanze per eseguire il rendering personalizzato. Fornisce inoltre un elenco completo delle funzioni disponibili disegno con schermate e gli esempi di codice.


## <a name="creating-a-ccdrawnode"></a>Creazione di un CCDrawNode

Il `CCDrawNode` classe può essere utilizzata per disegnare oggetti geometrici, ad esempio linee, rettangoli e cerchi. Ad esempio, l'esempio di codice seguente viene illustrato come creare un `CCDrawNode` istanza che consente di disegnare un cerchio in un `CCLayer` implementazione della classe:


```csharp
public class GameLayer : CCLayer
{
    public GameLayer ()
    {
        var drawNode = new CCDrawNode ();
        this.AddChild (drawNode);
        // Origin is bottom-left of the screen. This moves
        // the drawNode 100 pixels to the right and 100 pixels up
        drawNode.PositionX = 100;
        drawNode.PositionY = 100;

        drawNode.DrawCircle (
            center: new CCPoint (0, 0),
            radius: 20,
            color: CCColor4B.White);

    }
} 
```

Questo codice produce i seguenti circle in fase di esecuzione:

![](ccdrawnode-images/image1.png "Questo codice produce il cerchio in fase di esecuzione")


## <a name="draw-method-details"></a>Dettagli metodo di disegno

Esaminiamo ora alcuni dettagli relativi al disegno con un `CCDrawNode`:


### <a name="draw-methods-positions-are-relative-to-the-ccdrawnode"></a>Le posizioni di metodi di disegno sono relativi al CCDrawNode

Tutti i metodi di disegno richiedono posizione almeno un valore per il disegno. Questo valore di posizione è relativa al `CCDrawNode` istanza. Ciò significa che il `CCDrawNode` stesso ha una posizione e tutto tracciare le chiamate effettuate al `CCDrawNode` accettano anche uno o più valori di posizione. Per comprendere come combinano questi valori, verranno esaminati alcuni esempi.

Prima di tutto verrà esaminato il `DrawCircle` esempio precedente:


```csharp
...
drawNode.PositionX = 100;
drawNode.PositionY = 100;

drawNode.DrawCircle (center: new CCPoint (0, 0),
...
```

In questo caso, il `CCDrawNode` è posizionato in corrispondenza (100,100), e il cerchio disegnato è a (0,0) relativa al `CCDrawNode`, ottenendo il cerchio viene centrato 100 pixel in alto e a destra dell'angolo inferiore sinistro dello schermo del gioco.

Il `CCDrawNode` può inoltre essere posizionata in corrispondenza dell'origine (in basso a sinistra della schermata), basarsi sul cerchio per gli offset:


```csharp
...
drawNode.PositionX = 0;
drawNode.PositionY = 0;

drawNode.DrawCircle (center: new CCPoint (50, 60),
...
```

Il codice sopra i risultati nel centro del cerchio a 50 unità di (`drawNode.PositionX` + i `CCPoint.X`) a destra del lato sinistro della schermata e 60 (`drawNode.PositionY` + il `CCPoint.Y`) unità sopra la parte inferiore della schermata.

Dopo aver chiamato un metodo draw, l'oggetto non può essere modificato, a meno che il `CCDrawNode.Clear` viene chiamato il metodo, pertanto qualsiasi di riposizionamento deve essere eseguita su di `CCDrawNode` stesso.

Oggetti disegnati dal `CCNodes` sono inoltre conseguenze per il `CCNode` dell'istanza `Rotation` e `Scale` proprietà.


### <a name="draw-methods-do-not-need-to-be-called-every-frame"></a>Metodi di disegno non sono necessario essere chiamata a ogni fotogramma

Metodi di disegno devono essere chiamato solo una volta per creare un oggetto visivo permanente. Nell'esempio precedente, la chiamata a `DrawCircle` nel costruttore della `GameLayer` – `DrawCircle` non dovrà essere chiamato a ogni fotogramma per ridisegnare il controllo circle quando la schermata viene aggiornata.

Questo comportamento è diverso dai metodi di disegno in MonoGame, che in genere verrà eseguito il rendering qualcosa sullo schermo per un solo frame e che deve essere chiamato ogni fotogramma.

Se vengono chiamati i metodi di disegno per ogni fotogramma, gli oggetti verranno accumulati alla fine all'interno della chiamata `CCDrawNode` istanza, causando una diminuzione della frequenza dei fotogrammi come altri oggetti vengono disegnati.


### <a name="each-ccdrawnode-supports-multiple-draw-calls"></a>Ogni CCDrawNode supporta più chiamate di disegno

`CCDrawNode` le istanze possono essere utilizzate per disegnare più forme. In questo modo oggetti visivi complessi essere inserita in un singolo oggetto. Ad esempio, il codice seguente è utilizzabile per eseguire il rendering di più cerchi con uno `CCDrawNode`:


```csharp
for (int i = 0; i < 8; i++)
{
    drawNode.DrawCircle (
        center: new CCPoint (i*15, 0),
        radius: 20,
        color: CCColor4B.White);
} 
```

Ciò comporta l'immagine seguente:

![](ccdrawnode-images/image2.png "Ciò comporta in questo grafico")


## <a name="draw-call-examples"></a>Esempi di chiamate di disegno

Chiamate di disegno seguenti sono disponibili in `CCDrawNode`:

- [`DrawCatmullRom`](#drawcatmullrom)
- [`DrawCircle`](#drawcircle)
- [`DrawCubicBezier`](#drawcubicbezier)
- [`DrawEllipse`](#drawellipse)
- [`DrawLineList`](#drawlinelist)
- [`DrawPolygon`](#drawpolygon)
- [`DrawQuadBezier`](#drawquadbezier)
- [`DrawRect`](#drawrect)
- [`DrawSegment`](#drawsegment)
- [`DrawSolidArc`](#drawsolidarc)
- [`DrawSolidCircle`](#drawsolidcircle)
- [`DrawTriangleList`](#drawtrianglelist)


### <a name="drawcardinalspline"></a>DrawCardinalSpline

`DrawCardinalSpline` Crea una linea curva tramite un numero variabile di punti. 

Il `config` parametro definisce che fa riferimento la spline di tipo verrà pass-through. L'esempio seguente mostra una spline di tipo che attraversano quattro punti.

Il `tension` parametro determina come ben strutturata o arrotondare i punti sulla spline vengono visualizzati. Oggetto `tension` il valore 0 comporterà una curva spline e un `tension` valore pari a 1 comporterà una curva spline disegnata linee rette e bordi.

Sebbene spline linee curve, CocosSharp consente di disegnare spline con linee rette. Il `segments` parametro controlla quanti segmenti da utilizzare per disegnare spline. Un numero maggiore comporta una spline di tipo in modo uniforme curve al costo di prestazioni. 

Esempio di codice:


```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (50, 70));
splinePoints.Add (new CCPoint (0, 140));
splinePoints.Add (new CCPoint (100, 210));

drawNode.DrawCardinalSpline (
    config: splinePoints,
    tension: 0,
    segments: 64,
    color:CCColor4B.Red); 
```

![](ccdrawnode-images/image3.png "Il parametro segmenti controlla quanti segmenti da utilizzare per disegnare spline")


### <a name="drawcatmullrom"></a>DrawCatmullRom

`DrawCatmullRom` Crea una linea curva tramite un numero variabile di punti, simile a `DrawCardinalLine`. Questo metodo non include un parametro di tensione.

Esempio di codice:

```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (80, 90));
splinePoints.Add (new CCPoint (100, 0));
splinePoints.Add (new CCPoint (0, 130)); 

drawNode.DrawCatmullRom (
    points: splinePoints,
    segments: 64); 
```

![](ccdrawnode-images/image4.png "DrawCatmullRom crea una linea curva tramite un numero variabile di punti, simili a DrawCardinalLine")


### <a name="drawcircle"></a>DrawCircle

`DrawCircle` Crea un perimetro di un cerchio di un determinato `radius`.

Esempio di codice:

```csharp
drawNode.DrawCircle (
    center:new CCPoint (0, 0),
    radius:20,
    color:CCColor4B.Yellow); 
```

![](ccdrawnode-images/image5.png "DrawCircle viene creato un perimetro di un cerchio di un raggio specificato")


### <a name="drawcubicbezier"></a>DrawCubicBezier

`DrawCubicBezier` Disegna una linea curva tra due punti, usando i punti di controllo per impostare il percorso tra i due punti.

Esempio di codice:

```csharp
drawNode.DrawCubicBezier (
    origin: new CCPoint (0, 0),
    control1: new CCPoint (50, 150),
    control2: new CCPoint (250, 150),
    destination: new CCPoint (170, 0),
    segments: 64,
    lineWidth: 1,
    color: CCColor4B.Green); 
```

 ![](ccdrawnode-images/image6.png "DrawCubicBezier Disegna una linea curva tra due punti")


### <a name="drawellipse"></a>DrawEllipse

`DrawEllipse` Crea la struttura di un' *ellisse*, che è noto anche come un'ellissi (anche se i due non sono identici geometricamente). La forma dell'ellisse può essere definita da un `CCRect` istanza.

Esempio di codice:


```csharp
drawNode.DrawEllipse (
    rect: new CCRect (0, 0, 130, 90),
    lineWidth: 2,
    color: CCColor4B.Gray); 
```

![](ccdrawnode-images/image8.png "DrawEllipse crea la struttura di un'ellisse, che è noto anche come un ovale")


### <a name="drawline"></a>DrawLine

`DrawLine` si connette a punti con una linea di una larghezza specifica. Questo metodo è simile a `DrawSegment`, ad eccezione del fatto che crea gli endpoint flat anziché gli endpoint di arrotondamenti.

Esempio di codice:


```csharp
drawNode.DrawLine (
    from: new CCPoint (0, 0),
    to: new CCPoint (150, 30),
    lineWidth: 5,
    color:CCColor4B.Orange); 
```

![](ccdrawnode-images/image9.png "DrawLine si connette a punti con una linea di una larghezza specifica")


### <a name="drawlinelist"></a>DrawLineList

`DrawLineList` Crea più righe tramite la connessione di ogni coppia di punti specificati da un `CCV3F_C4B` matrice. Il `CCV3F_C4B` struct contiene valori per la posizione e il colore. Il `verts` parametro deve sempre contenere un numero pari di punti, perché ogni riga è definita da due punti.

Esempio di codice:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First line:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    // second line, will blend from white to red:
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(120,120), CCColor4B.Red)
};

drawNode.DrawLineList (verts); 
```

 ![](ccdrawnode-images/image10.png "Il parametro verts deve sempre contenere un numero pari di punti, perché ogni riga è definita da due punti")




### <a name="drawpolygon"></a>DrawPolygon

`DrawPolygon` Crea un'istanza polygon riempita con un contorno del colore e larghezza variabile.

Esempio di codice:


```csharp
CCPoint[] verts = new CCPoint[] {
    new CCPoint(0,0),
    new CCPoint(0, 100),
    new CCPoint(50, 150),
    new CCPoint(100, 100),
    new CCPoint(100, 0)
};

drawNode.DrawPolygon (verts,
    count: verts.Length,
    fillColor: CCColor4B.White,
    borderWidth: 5,
    borderColor: CCColor4B.Red,
    closePolygon: true); 
```

![](ccdrawnode-images/image11.png "DrawPolygon crea un'istanza polygon riempita con un contorno di larghezza variabile e il colore")


### <a name="drawquadbezier"></a>DrawQuadBezier

`DrawQuadBezier` connette i due punti con una riga. Si comporta in modo analogo al `DrawCubicBezier` ma supporta solo un punto di controllo singola.

Esempio di codice:


```csharp
drawNode.DrawQuadBezier (
    origin:new CCPoint (0, 0),
    control:new CCPoint (200, 0),
    destination:new CCPoint (0, 300),
    segments:64,
    lineWidth:1,
    color:CCColor4B.White);
```

![](ccdrawnode-images/image12.png "DrawQuadBezier collega due punti con una linea")


### <a name="drawrect"></a>DrawRect

`DrawRect` Crea un rettangolo riempita con un contorno del colore e larghezza variabile.

Esempio di codice: 


```csharp
var shape = new CCRect (
    0, 0, 100, 200);
drawNode.DrawRect(shape,
    fillColor:CCColor4B.Blue,
    borderWidth: 4,
    borderColor:CCColor4B.White); 
```

![](ccdrawnode-images/image13.png "DrawRect crea un rettangolo riempita con un contorno di larghezza variabile e il colore")


### <a name="drawsegment"></a>DrawSegment

`DrawSegment` connette i due punti con una riga di colore e larghezza variabile. È simile a `DrawLine`, ad eccezione del fatto che consente di creare endpoint round anziché flat endpoint.

Esempio di codice:


```csharp
drawNode.DrawSegment (from: new CCPoint (0, 0),
    to: new CCPoint (100, 200),
    radius: 5,
    color:new CCColor4F(1,1,1,1)); 
```

![](ccdrawnode-images/image14.png "DrawSegment collega due punti con una linea di spessore variabile e il colore")


### <a name="drawsolidarc"></a>DrawSolidArc

`DrawSolidArc` Crea un wedge compilato in un determinato colore e un raggio.

Esempio di codice:


```csharp
drawNode.DrawSolidArc(
    pos:new CCPoint(100, 100),
    radius:200,
    startAngle:0,
    sweepAngle:CCMathHelper.Pi/2, // this is in radians, clockwise
    color:CCColor4B.White); 
```

![](ccdrawnode-images/image15.png "DrawSolidArc crea un wedge riempita di un determinato colore e radius")


### <a name="drawsolidcircle"></a>DrawSolidCircle

`DrawCircle` Crea un cerchio riempita di un raggio specificato.

Esempio di codice:


```csharp
drawNode.DrawSolidCircle(
    pos: new CCPoint (100, 100),
    radius: 50,
    color: CCColor4B.Yellow); 
```

![](ccdrawnode-images/image16.png "DrawCircle crea un cerchio riempita di un raggio specificato")


### <a name="drawtrianglelist"></a>DrawTriangleList

`DrawTriangleList` Crea un elenco di triangoli. Ogni triangolo è definito da tre `CCV3F_C4B` istanze in una matrice. Il numero di vertici della matrice passato al `verts` parametro deve essere un multiplo pari a tre. Si noti che le uniche informazioni contenute nel `CCV3F_C4B` corrisponde alla posizione del verts e rispettivo colore – il `DrawTriangleList` metodo nepodporuje disegni triangoli con trame.

Esempio di codice:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First triangle:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    // second triangle, each point has different colors:
    new CCV3F_C4B( new CCPoint(90,0), CCColor4B.Yellow),
    new CCV3F_C4B( new CCPoint(120,60), CCColor4B.Red),
    new CCV3F_C4B( new CCPoint(150,0), CCColor4B.Blue)
};

drawNode.DrawTriangleList (verts); 
```

![](ccdrawnode-images/image17.png "DrawTriangleList crea un elenco di triangoli")


## <a name="summary"></a>Riepilogo

Questa guida illustra come creare un `CCDrawNode` ed eseguire il rendering basato su primitiva. Viene fornito un esempio della ognuno delle chiamate di disegno.

## <a name="related-links"></a>Collegamenti correlati

- [CCDrawNode API](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
- [Esempio completo](https://developer.xamarin.com/samples/mobile/CCDrawNode/)
