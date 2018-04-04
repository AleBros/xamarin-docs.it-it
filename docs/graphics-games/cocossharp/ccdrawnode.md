---
title: Geometria di disegno con CCDrawNode
description: CCDrawNode fornisce metodi per oggetti primitivi, ad esempio linee, cerchi e triangoli.
ms.prod: xamarin
ms.assetid: 46A3C3CE-74CC-4A3A-AB05-B694AE182ADB
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 2a4278f97a69ef9eb705c6fedf1a5c7ec98875b8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="drawing-geometry-with-ccdrawnode"></a>Geometria di disegno con CCDrawNode

_`CCDrawNode` fornisce metodi per oggetti primitivi, ad esempio linee, cerchi e triangoli._

La `CCDrawNode` classe CocosSharp fornisce più metodi per disegnare forme geometriche comuni. Eredita dal `CCNode` classe e viene in genere aggiunto alla `CCLayer` istanze. Questa guida viene illustrato come utilizzare `CCDrawNode` istanze per eseguire il rendering personalizzato. Fornisce inoltre un elenco completo delle funzioni di disegno disponibili con schermate e negli esempi di codice.


## <a name="creating-a-ccdrawnode"></a>Creazione di un CCDrawNode

La `CCDrawNode` classe può essere utilizzata per disegnare oggetti geometrici, ad esempio linee, cerchi e rettangoli. Ad esempio, l'esempio di codice seguente viene illustrato come creare un `CCDrawNode` istanza che consente di disegnare un cerchio in un `CCLayer` classe di implementazione:


```csharp
public class GameLayer : CCLayer
{
    public GameLayer ()
    {
        var drawNode = new CCDrawNode ();
        this.AddChild (drawNode);
        // Origin is bottom-left of the screen. This moves
        // the drawNode 100 pixels to the right and 100 pixels up
        drawNode.PositionX = 100;
        drawNode.PositionY = 100;

        drawNode.DrawCircle (
            center: new CCPoint (0, 0),
            radius: 20,
            color: CCColor4B.White);

    }
} 
```

Questo codice produce il cerchio in fase di esecuzione seguente:

![](ccdrawnode-images/image1.png "Questo codice genera questo cerchio in fase di esecuzione")


## <a name="draw-method-details"></a>Disegnare i dettagli (metodo)

Esaminiamo ora alcuni dettagli relativi al disegno con un `CCDrawNode`:


### <a name="draw-methods-positions-are-relative-to-the-ccdrawnode"></a>Metodi posizioni riguardano il CCDrawNode Draw

Tutti i metodi di disegno richiedono il valore di almeno una posizione per il disegno. Questo valore di posizione è relativo al `CCDrawNode` istanza. Ciò significa che il `CCDrawNode` a sua volta ha una posizione e tutte le chiamate effettuate a disegnare il `CCDrawNode` accettano anche uno o più valori di posizione. Per comprendere come combinano i valori sono i seguenti, ecco alcuni esempi.

Innanzitutto verrà esaminato il `DrawCircle` esempio precedente:


```csharp
...
drawNode.PositionX = 100;
drawNode.PositionY = 100;

drawNode.DrawCircle (center: new CCPoint (0, 0),
...
```

In questo caso, il `CCDrawNode` è posizionato in corrispondenza di (100,100), e il cerchio disegnato è a (0,0) relativo al `CCDrawNode`, ottenendo il cerchio viene centrato 100 pixel in alto e a destra dell'angolo inferiore sinistro dello schermo del gioco.

Il `CCDrawNode` può anche essere posizionata in corrispondenza dell'origine (in basso a sinistra dello schermo), basarsi sul cerchio per offset:


```csharp
...
drawNode.PositionX = 0;
drawNode.PositionY = 0;

drawNode.DrawCircle (center: new CCPoint (50, 60),
...
```

Il codice sopra produce al centro del cerchio a 50 unità (`drawNode.PositionX` + il `CCPoint.X`) a destra del lato sinistro dello schermo e 60 (`drawNode.PositionY` + il `CCPoint.Y`) unità sopra la parte inferiore della schermata.

Dopo aver chiamato un metodo di disegno, l'oggetto creato non può essere modificato, a meno che il `CCDrawNode.Clear` metodo viene chiamato, pertanto, qualsiasi riposizionamento deve essere eseguita su di `CCDrawNode` stesso.

Oggetti disegnati mediante `CCNodes` sono inoltre conseguenze per la `CCNode` dell'istanza `Rotation` e `Scale` proprietà.


### <a name="draw-methods-do-not-need-to-be-called-every-frame"></a>I metodi di disegno non è necessario chiamare ogni frame

Metodi di disegno devono essere chiamato solo una volta per creare un oggetto visivo permanente. Nell'esempio precedente, la chiamata a `DrawCircle` nel costruttore del `GameLayer` – `DrawCircle` non dovrà essere chiamato per disegnare nuovamente il cerchio quando la schermata viene aggiornata ogni fotogramma.

Questo comportamento è diverso dai metodi di disegno in MonoGame, che in genere verrà eseguito il rendering a schermo per un solo frame e che deve essere chiamato ogni fotogramma.

Se ogni frame vengono chiamati i metodi di disegno, infine accumulerà gli oggetti all'interno della chiamata `CCDrawNode` istanza, determinando un calo frequenza dei fotogrammi come vengono creati più oggetti.


### <a name="each-ccdrawnode-supports-multiple-draw-calls"></a>Ogni CCDrawNode supporta più chiamate di disegno

`CCDrawNode` per disegnare più forme, è possono utilizzare le istanze. In questo modo oggetti visivi complessi essere inserita in un singolo oggetto. Ad esempio, il codice seguente consente di eseguire il rendering più cerchi con uno `CCDrawNode`:


```csharp
for (int i = 0; i < 8; i++)
{
    drawNode.DrawCircle (
        center: new CCPoint (i*15, 0),
        radius: 20,
        color: CCColor4B.White);
} 
```

Ciò comporta l'immagine seguente:

![](ccdrawnode-images/image2.png "Di conseguenza, nel grafico")


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

Il `config` parametro definisce quali punti passerà la spline. L'esempio seguente mostra una spline di tipo che passano attraverso quattro punti.

Il `tension` controlli di parametro come acuto o arrotondare i punti della spline vengono visualizzati. Oggetto `tension` valore pari a 0 comporterà una curva spline di tipo e un `tension` valore 1 comporterà una spline di tipo disegnata linee rette e bordi.

Anche se spline sono linee curve, CocosSharp consente di disegnare spline rette. Il `segments` parametro controlla il numero di segmenti da utilizzare per disegnare la spline. Un numero maggiore comporta una spline di tipo lineare curva al costo di prestazioni. 

Esempio di codice:


```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (50, 70));
splinePoints.Add (new CCPoint (0, 140));
splinePoints.Add (new CCPoint (100, 210));

drawNode.DrawCardinalSpline (
    config: splinePoints,
    tension: 0,
    segments: 64,
    color:CCColor4B.Red); 
```

![](ccdrawnode-images/image3.png "Il parametro di segmenti controlla il numero di segmenti da utilizzare per disegnare spline")


### <a name="drawcatmullrom"></a>DrawCatmullRom

`DrawCatmullRom` Crea una linea curva tramite un numero variabile di punti, simile a `DrawCardinalLine`. Questo metodo non include un parametro di tensione.

Esempio di codice:

```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (80, 90));
splinePoints.Add (new CCPoint (100, 0));
splinePoints.Add (new CCPoint (0, 130)); 

drawNode.DrawCatmullRom (
    points: splinePoints,
    segments: 64); 
```

![](ccdrawnode-images/image4.png "DrawCatmullRom crea una linea curva tramite un numero variabile di punti, simili a DrawCardinalLine")


### <a name="drawcircle"></a>DrawCircle

`DrawCircle` Crea un perimetro di un cerchio di un determinato `radius`.

Esempio di codice:

```csharp
drawNode.DrawCircle (
    center:new CCPoint (0, 0),
    radius:20,
    color:CCColor4B.Yellow); 
```

![](ccdrawnode-images/image5.png "DrawCircle viene creato un perimetro di un cerchio di un raggio specificato")


### <a name="drawcubicbezier"></a>DrawCubicBezier

`DrawCubicBezier` Disegna una linea curva tra due punti, utilizzando i punti di controllo per impostare il percorso tra due punti.

Esempio di codice:

```csharp
drawNode.DrawCubicBezier (
    origin: new CCPoint (0, 0),
    control1: new CCPoint (50, 150),
    control2: new CCPoint (250, 150),
    destination: new CCPoint (170, 0),
    segments: 64,
    lineWidth: 1,
    color: CCColor4B.Green); 
```

 ![](ccdrawnode-images/image6.png "DrawCubicBezier Disegna una linea curva tra due punti")


### <a name="drawellipse"></a>DrawEllipse

`DrawEllipse` Crea la struttura di un *ellisse*, che è noto anche come un'ellissi (anche se non sono identici geometricamente). La forma dell'ellisse può essere definita da un `CCRect` istanza.

Esempio di codice:


```csharp
drawNode.DrawEllipse (
    rect: new CCRect (0, 0, 130, 90),
    lineWidth: 2,
    color: CCColor4B.Gray); 
```

![](ccdrawnode-images/image8.png "DrawEllipse crea la struttura di un'ellisse, che viene spesso definita come un'ellissi")


### <a name="drawline"></a>DrawLine

`DrawLine` Consente di connettersi ai punti con una riga di una determinata larghezza. Questo metodo è simile a `DrawSegment`, ma crea endpoint flat anziché round endpoint.

Esempio di codice:


```csharp
drawNode.DrawLine (
    from: new CCPoint (0, 0),
    to: new CCPoint (150, 30),
    lineWidth: 5,
    color:CCColor4B.Orange); 
```

![](ccdrawnode-images/image9.png "DrawLine si connette a punti con una riga di una determinata larghezza")


### <a name="drawlinelist"></a>DrawLineList

`DrawLineList` Crea più righe tramite la connessione a ogni coppia di punti specificati da un `CCV3F_C4B` matrice. Il `CCV3F_C4B` struct contiene valori per posizione e il colore. Il `verts` parametro dovrebbe sempre contenere un numero pari di punti, poiché ogni riga è definito da due punti.

Esempio di codice:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First line:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    // second line, will blend from white to red:
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(120,120), CCColor4B.Red)
};

drawNode.DrawLineList (verts); 
```

 ![](ccdrawnode-images/image10.png "Il parametro verts deve sempre contenere un numero pari di punti, poiché ogni riga è definito da due punti")




### <a name="drawpolygon"></a>DrawPolygon

`DrawPolygon` Crea un'istanza polygon riempita con un contorno di larghezza variabile e il colore.

Esempio di codice:


```csharp
CCPoint[] verts = new CCPoint[] {
    new CCPoint(0,0),
    new CCPoint(0, 100),
    new CCPoint(50, 150),
    new CCPoint(100, 100),
    new CCPoint(100, 0)
};

drawNode.DrawPolygon (verts,
    count: verts.Length,
    fillColor: CCColor4B.White,
    borderWidth: 5,
    borderColor: CCColor4B.Red,
    closePolygon: true); 
```

![](ccdrawnode-images/image11.png "DrawPolygon crea un'istanza polygon riempita con un contorno di larghezza variabile e il colore")


### <a name="drawquadbezier"></a>DrawQuadBezier

`DrawQuadBezier` Collega i due punti con una riga. Si comporta in modo analogo a `DrawCubicBezier` ma supporta solo un unico punto di controllo.

Esempio di codice:


```csharp
drawNode.DrawQuadBezier (
    origin:new CCPoint (0, 0),
    control:new CCPoint (200, 0),
    destination:new CCPoint (0, 300),
    segments:64,
    lineWidth:1,
    color:CCColor4B.White);
```

![](ccdrawnode-images/image12.png "DrawQuadBezier collega i due punti con una riga")


### <a name="drawrect"></a>DrawRect

`DrawRect` creazione di un rettangolo riempita con un contorno di larghezza variabile e il colore.

Esempio di codice: 


```csharp
var shape = new CCRect (
    0, 0, 100, 200);
drawNode.DrawRect(shape,
    fillColor:CCColor4B.Blue,
    borderWidth: 4,
    borderColor:CCColor4B.White); 
```

![](ccdrawnode-images/image13.png "DrawRect crea un rettangolo riempita con un contorno di larghezza variabile e colore")


### <a name="drawsegment"></a>DrawSegment

`DrawSegment` Collega i due punti con una riga di larghezza variabile e il colore. È simile a `DrawLine`, ad eccezione del fatto che crea gli endpoint di arrotondamenti anziché flat endpoint.

Esempio di codice:


```csharp
drawNode.DrawSegment (from: new CCPoint (0, 0),
    to: new CCPoint (100, 200),
    radius: 5,
    color:new CCColor4F(1,1,1,1)); 
```

![](ccdrawnode-images/image14.png "DrawSegment collega i due punti con una riga di larghezza variabile e il colore")


### <a name="drawsolidarc"></a>DrawSolidArc

`DrawSolidArc` Crea un spicchio compilato in un determinato colore e il raggio.

Esempio di codice:


```csharp
drawNode.DrawSolidArc(
    pos:new CCPoint(100, 100),
    radius:200,
    startAngle:0,
    sweepAngle:CCMathHelper.Pi/2, // this is in radians, clockwise
    color:CCColor4B.White); 
```

![](ccdrawnode-images/image15.png "DrawSolidArc crea un spicchio compilato in un determinato colore e il raggio")


### <a name="drawsolidcircle"></a>DrawSolidCircle

`DrawCircle` Crea un cerchio compilato di un raggio specificato.

Esempio di codice:


```csharp
drawNode.DrawSolidCircle(
    pos: new CCPoint (100, 100),
    radius: 50,
    color: CCColor4B.Yellow); 
```

![](ccdrawnode-images/image16.png "DrawCircle crea un cerchio compilato di un raggio specificato")


### <a name="drawtrianglelist"></a>DrawTriangleList

`DrawTriangleList` Crea un elenco di triangoli. Ogni triangolo è definito da tre `CCV3F_C4B` istanze in una matrice. Il numero di vertici della matrice passato per il `verts` parametro deve essere un multiplo di tre. Si noti che le uniche informazioni contenute in `CCV3F_C4B` è la posizione del verts e il colore: il `DrawTriangleList` metodo non supporta il disegnati triangoli con trame.

Esempio di codice:


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First triangle:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    // second triangle, each point has different colors:
    new CCV3F_C4B( new CCPoint(90,0), CCColor4B.Yellow),
    new CCV3F_C4B( new CCPoint(120,60), CCColor4B.Red),
    new CCV3F_C4B( new CCPoint(150,0), CCColor4B.Blue)
};

drawNode.DrawTriangleList (verts); 
```

![](ccdrawnode-images/image17.png "DrawTriangleList crea un elenco di triangoli")


## <a name="summary"></a>Riepilogo

Questa guida viene illustrato come creare un `CCDrawNode` ed eseguire il rendering basato sulle primitive. Viene fornito un esempio di ognuna delle chiamate di disegno.

## <a name="related-links"></a>Collegamenti correlati

- [CCDrawNode API](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
- [Per un esempio completo](https://developer.xamarin.com/samples/mobile/CCDrawNode/)
