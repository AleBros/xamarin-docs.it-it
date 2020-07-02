---
title: 'Xamarin.FormsForme: geometrie'
description: Xamarin.Formsle classi Geometry consentono di descrivere la geometria di una forma 2D.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 00c11c07e796ffa24c3ed1f23ca3ea29885a3d8b
ms.sourcegitcommit: 91b4d2f93687fadec5c3f80aadc8f7298d911624
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85795082"
---
# <a name="xamarinforms-shapes-geometries"></a>Xamarin.FormsForme: geometrie

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scarica esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Geometry` classe e le classi che derivano da esso consentono di descrivere la geometria di una forma 2D. `Geometry`gli oggetti possono essere semplici, ad esempio rettangoli e cerchi, o composti, creati da due o più oggetti Geometry. Inoltre, è possibile creare geometrie più complesse che includono archi e curve.

La `Geometry` classe è la classe padre per diverse classi che definiscono diverse categorie di geometrie:

- `EllipseGeometry`, che rappresenta la geometria di un'ellisse o un cerchio.
- `GeometryGroup`, che rappresenta un contenitore in grado di combinare più oggetti Geometry in un unico oggetto.
- `LineGeometry`, che rappresenta la geometria di una linea.
- `PathGeometry`, che rappresenta la geometria di una forma complessa che può essere composta da archi, curve, ellissi, linee e rettangoli.
- `RectangleGeometry`, che rappresenta la geometria di un rettangolo o di un quadrato.

Le `Geometry` `Shape` classi e sembrano simili, in quanto entrambe descrivono forme 2D, ma presentano una differenza importante. La classe `Geometry` deriva dalla classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) , mentre la classe `Shape` deriva dalla classe [`View`](xref:Xamarin.Forms.View) . `Shape`Gli oggetti possono pertanto eseguire il rendering e partecipare al sistema di layout, mentre `Geometry` gli oggetti non possono. Sebbene `Shape` gli oggetti siano più facilmente utilizzabili degli `Geometry` oggetti, `Geometry` gli oggetti sono più versatili. Mentre un `Shape` oggetto viene usato per eseguire il rendering di grafica 2D, un `Geometry` oggetto può essere usato per definire l'area geometrica per la grafica 2D e definire un'area per il ritaglio.

Le classi seguenti dispongono di proprietà che possono essere impostate su `Geometry` oggetti:

- La `Path` classe usa un oggetto `Geometry` per descrivere il contenuto. È possibile eseguire il rendering di un oggetto impostando `Geometry` la `Path.Data` proprietà su un `Geometry` oggetto e impostando le `Path` `Fill` proprietà e dell'oggetto `Stroke` .
- La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe dispone di una `Clip` proprietà, di tipo `Geometry` , che definisce il contorno del contenuto di un elemento. Quando la `Clip` proprietà è impostata su un `Geometry` oggetto, sarà visibile solo l'area che si trova all'interno dell'area del `Geometry` . Per altre informazioni, vedere [clip con una geometria](#clip-with-a-geometry).

Le classi che derivano dalla `Geometry` classe possono essere raggruppate in tre categorie: geometrie semplici, geometrie di percorso e geometrie composite.

## <a name="simple-geometries"></a>Geometrie semplici

Le classi Geometry semplici sono `EllipseGeometry` , `LineGeometry` e `RectangleGeometry` . Vengono usati per creare forme geometriche di base, ad esempio cerchi, linee e rettangoli. Queste stesse forme, oltre a forme più complesse, possono essere create usando `PathGeometry` o combinando oggetti Geometry, ma queste classi forniscono un approccio più semplice per la produzione di queste forme geometriche di base.

### <a name="ellipsegeometry"></a>EllipseGeometry

Una geometria ellisse rappresenta la geometria, un'ellisse o un cerchio ed è definita da un punto centrale, da un raggio x e da un raggio y.

La `EllipseGeometry` classe definisce le proprietà seguenti:

- `Center`, di tipo [`Point`](xref:Xamarin.Forms.Point) , che rappresenta il punto centrale della geometria.
- `RadiusX`, di tipo `double` , che rappresenta il valore del raggio x della geometria. Il valore predefinito di questa proprietà è 0,0.
- `RadiusY`, di tipo `double` , che rappresenta il valore del raggio y della geometria. Il valore predefinito di questa proprietà è 0,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering `EllipseGeometry` di un oggetto in un `Path` oggetto:

```xaml
<Path Fill="Blue"
      Stroke="Red">
  <Path.Data>
    <EllipseGeometry Center="50,50"
                     RadiusX="50"
                     RadiusY="50" />
  </Path.Data>
</Path>
```

In questo esempio, il centro di `EllipseGeometry` è impostato su (50, 50) e il raggio x e il raggio y sono entrambi impostati su 50. Viene creato un cerchio rosso con un diametro di 100 unità indipendenti dal dispositivo, la cui parte interna è dipinta blu:

![EllipseGeometry](geometry-images/ellipse.png "EllipseGeometry")

### <a name="linegeometry"></a>LineGeometry

Una geometria di linea rappresenta la geometria di una linea e viene definita specificando il punto iniziale della riga e il punto finale.

La `LineGeometry` classe definisce le proprietà seguenti:

- `StartPoint`, di tipo [`Point`](xref:Xamarin.Forms.Point) , che rappresenta il punto iniziale della linea.
- `EndPoint`, di tipo [`Point`](xref:Xamarin.Forms.Point) , che rappresenta il punto finale della linea.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering di un `LineGeometry` oggetto in un `Path` oggetto:

```xaml
<Path Stroke="Black">
  <Path.Data>
    <LineGeometry StartPoint="10,20"
                  EndPoint="100,130" />
  </Path.Data>
</Path>
```

In questo esempio `LineGeometry` viene disegnato un oggetto da (10, 20) a (100.130):

![LineGeometry](geometry-images/line.png "LineGeometry")

> [!NOTE]
> L'impostazione della `Fill` proprietà di un oggetto `Path` che esegue il rendering di `LineGeometry` non avrà alcun effetto, perché una riga non ha interni.

### <a name="rectanglegeometry"></a>RectangleGeometry

Una geometria rettangolare rappresenta la geometria di un rettangolo o di un quadrato e viene definita con una `Rect` struttura che specifica la posizione relativa e l'altezza e la larghezza.

La `RectangleGeometry` classe definisce la `Rect` proprietà, di tipo [`Rectangle`](xref:Xamarin.Forms.Rectangle) , che rappresenta le dimensioni del rettangolo. Questa proprietà è supportata da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che può essere la destinazione di data binding e stile.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering di un `RectangleGeometry` oggetto in un `Path` oggetto:

```xaml
<Path Fill="Blue"
      Stroke="Red">
  <Path.Data>
    <RectangleGeometry Rect="10,10,150,100" />
  </Path.Data>
</Path>
```

La posizione e le dimensioni del rettangolo sono definite da una `Rect` struttura. In questo esempio, la posizione è (10, 10), la larghezza è 150 e l'altezza è 100 unità indipendenti dal dispositivo:

![RectangleGeometry](geometry-images/rectangle.png "RectangleGeometry")

## <a name="path-geometries"></a>Geometrie percorso

Una geometria del percorso descrive una forma complessa che può essere composta da archi, curve, ellissi, linee e rettangoli.

La `PathGeometry` classe definisce le proprietà seguenti:

- `Figures`, di tipo `PathFigureCollection` , che rappresenta la raccolta di `PathFigure` oggetti che descrivono il contenuto del percorso.
- `FillRule`, di tipo `FillRule` , che determina come vengono combinate le aree di intersezione contenute nella geometria. Il valore predefinito di questa proprietà è `FillRule.EvenOdd`.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Per ulteriori informazioni sull' `FillRule` enumerazione, vedere [ Xamarin.Forms forme: regole di riempimento](fillrules.md).

> [!NOTE]
> La `Figures` proprietà è l'oggetto `ContentProperty` della `PathGeometry` classe e pertanto non è necessario impostarlo in modo esplicito da XAML.

Un oggetto `PathGeometry` è costituito da una raccolta di `PathFigure` oggetti, ognuno dei quali `PathFigure` descrive una forma nella geometria. Ognuno `PathFigure` è costituito da uno o più `PathSegment` oggetti, ognuno dei quali descrive un segmento della forma. Sono disponibili molti tipi di segmenti:

- `ArcSegment`, che crea un arco ellittico tra due punti.
- `BezierSegment`, che crea una curva di Bezier cubica tra due punti.
- `LineSegment`, che crea una linea tra due punti.
- `PolyBezierSegment`, che crea una serie di curve di Bézier cubiche.
- `PolyLineSegment`, che consente di creare una serie di righe.
- `PolyQuadraticBezierSegment`, che crea una serie di curve di Bézier quadratiche.
- `QuadraticBezierSegment`, che crea una curva di Bézier quadratica.

Tutte le classi precedenti derivano dalla `PathSegment` classe astratta.

I segmenti all'interno di `PathFigure` vengono combinati in una singola forma geometrica con il punto finale di ogni segmento che è il punto iniziale del segmento successivo. La `StartPoint` proprietà di un oggetto `PathFigure` specifica il punto da cui viene disegnato il primo segmento. Ogni segmento successivo inizia in corrispondenza del punto finale del segmento precedente. È ad esempio possibile definire una linea verticale da a impostando `10,50` `10,150` la `StartPoint` proprietà su `10,50` e creando un oggetto `LineSegment` con un' `Point` impostazione della proprietà `10,150` :

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,50">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="10,150" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

È possibile creare geometrie più complesse usando una combinazione di `PathSegment` oggetti e usando più `PathFigure` oggetti all'interno di un oggetto `PathGeometry` .

### <a name="create-an-arcsegment"></a>Creare un ArcSegment

Un oggetto `ArcSegment` Crea un arco ellittico tra due punti. Un arco ellittico viene definito in base ai punti iniziale e finale, al fattore x e y, al fattore di rotazione dell'asse x, un valore che indica se l'arco deve essere maggiore di 180 gradi e un valore che descrive la direzione in cui viene disegnato l'arco.

La `ArcSegment` classe definisce le proprietà seguenti:

- `Point`, di tipo [`Point`](xref:Xamarin.Forms.Point) , che rappresenta l'endpoint dell'arco ellittico. Il valore predefinito di questa proprietà è (0, 0).
- `Size`, di tipo [`Size`](xref:Xamarin.Forms.Size) , che rappresenta il raggio x e y dell'arco. Il valore predefinito di questa proprietà è (0, 0).
- `RotationAngle`, di tipo `double` , che rappresenta il valore espresso in gradi in base al quale l'ellisse viene ruotata intorno all'asse x. Il valore predefinito di questa proprietà è 0.
- `SweepDirection`, di tipo `SweepDirection` , che specifica la direzione in cui viene disegnato l'arco. Il valore predefinito di questa proprietà è `SweepDirection.CounterClockwise`.
- `IsLargeArc`, di tipo `bool` , che indica se l'arco deve essere maggiore di 180 gradi. Il valore predefinito di questa proprietà è `false`.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

> [!NOTE]
> La `ArcSegment` classe non contiene una proprietà per il punto iniziale dell'arco. Definisce solo il punto finale dell'arco che rappresenta. Il punto iniziale dell'arco è il punto corrente dell'oggetto `PathFigure` a cui `ArcSegment` viene aggiunto l'oggetto.

L'enumerazione `SweepDirection` definisce i membri seguenti:

- `CounterClockwise`, che specifica che gli archi vengono disegnati in senso orario.
- `Clockwise`, che specifica che gli archi vengono disegnati in senso antiorario.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering `ArcSegment` di un oggetto in un `Path` oggetto:

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,100">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <ArcSegment Size="100,50"
                                            RotationAngle="45"
                                            IsLargeArc="True"
                                            SweepDirection="CounterClockwise"
                                            Point="200,100" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

In questo esempio viene disegnato un arco ellittico da (10.100) a (200.100).

### <a name="create-a-beziersegment"></a>Creare un BezierSegment

Un oggetto `BezierSegment` Crea una curva di Bezier cubica tra due punti. Una curva di Bezier cubica è definita da quattro punti: un punto iniziale, un punto finale e due punti di controllo.

La `BezierSegment` classe definisce le proprietà seguenti:

- `Point1`, di tipo [`Point`](xref:Xamarin.Forms.Point) , che rappresenta il primo punto di controllo della curva. Il valore predefinito di questa proprietà è (0, 0).
- `Point2`, di tipo [`Point`](xref:Xamarin.Forms.Point) , che rappresenta il secondo punto di controllo della curva. Il valore predefinito di questa proprietà è (0, 0).
- `Point3`, di tipo [`Point`](xref:Xamarin.Forms.Point) , che rappresenta il punto finale della curva. Il valore predefinito di questa proprietà è (0, 0).

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

> [!NOTE]
> La `BezierSegment` classe non contiene una proprietà per il punto iniziale della curva. Il punto iniziale della curva è il punto corrente dell'oggetto `PathFigure` a cui `BezierSegment` viene aggiunto l'oggetto.

I due punti di controllo di una curva di Bezier cubica si comportano come magneti, attirando parti di che altrimenti sarebbero una linea retta verso se stessi e producendo una curva. Il primo punto di controllo influiscono sulla parte iniziale della curva. Il secondo punto di controllo influiscono sulla parte finale della curva. La curva non passa necessariamente attraverso uno dei punti di controllo. Al contrario, ogni punto di controllo sposta la propria parte della linea verso se stessa, ma non attraverso se stessa.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering di un `BezierSegment` oggetto in un `Path` oggetto:

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <BezierSegment Point1="100,0"
                                               Point2="200,200"
                                               Point3="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

In questo esempio viene disegnata una curva di Bezier cubica da (10, 10) a (300, 10). La curva ha due punti di controllo in (100, 0) e (200.200):

![BezierSegment](geometry-images/beziersegment.png "BezierSegment")

### <a name="create-a-linesegment"></a>Creare un LineSegment

Un oggetto `LineSegment` Crea una linea tra due punti.

La `LineSegment` classe definisce la `Point` proprietà, di tipo [`Point`](xref:Xamarin.Forms.Point) , che rappresenta il punto finale del segmento di linea. Il valore predefinito di questa proprietà è (0, 0) ed è supportato da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che può essere la destinazione di data binding e con stile.

> [!NOTE]
> La `LineSegment` classe non contiene una proprietà per il punto iniziale della riga. Definisce solo l'endpoint. Il punto iniziale della linea è il punto corrente dell'oggetto `PathFigure` a cui `LineSegment` viene aggiunto l'oggetto.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering `LineSegment` di oggetti in un `Path` oggetto:

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure IsClosed="True"
                                StartPoint="10,100">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="100,100" />
                                <LineSegment Point="100,50" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

In questo esempio viene disegnato un segmento di linea da (10.100) a (100.100) e da (100.100) a (100, 50). Inoltre, l'oggetto `PathFigure` è chiuso perché la relativa `IsClosed` proprietà è impostata su `true` . Ciò comporta il disegno di un triangolo:

![LineSegments](geometry-images/linesegments.png "LineSegments")

### <a name="create-a-polybeziersegment"></a>Creare un PolyBezierSegment

Un oggetto `PolyBezierSegment` Crea una o più curve di Bézier cubiche.

La `PolyBezierSegment` classe definisce la `Points` proprietà, di tipo `PointCollection` , che rappresenta i punti che definiscono l'oggetto `PolyBezierSegment` . Un oggetto `PointCollection` è un oggetto `ObservableCollection` di [`Point`](xref:Xamarin.Forms.Point) oggetti. Questa proprietà è supportata da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che può essere la destinazione di data binding e stile.

> [!NOTE]
> La `PolyBezierSegment` classe non contiene una proprietà per il punto iniziale della curva. Il punto iniziale della curva è il punto corrente dell'oggetto `PathFigure` a cui `PolyBezierSegment` viene aggiunto l'oggetto.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering di un `PolyBezierSegment` oggetto in un `Path` oggetto:

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyBezierSegment Points="0,0 100,0 150,100 150,0 200,0 300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

In questo esempio, `PolyBezierSegment` specifica due curve di Bézier cubiche. La prima curva è da (10, 10) a (150.100) con un punto di controllo di (0, 0) e un altro punto di controllo di (100, 0). La seconda curva è da (150.100) a (300, 10) con un punto di controllo di (150, 0) e un altro punto di controllo di (200, 0):

![PolyBezierSegment](geometry-images/polybeziersegment.png "PolyBezierSegment")

### <a name="create-a-polylinesegment"></a>Creare un PolyLineSegment

Un oggetto `PolyLineSegment` crea uno o più segmenti di linea.

La `PolyLineSegment` classe definisce la `Points` proprietà, di tipo `PointCollection` , che rappresenta i punti che definiscono l'oggetto `PolyLineSegment` . Un oggetto `PointCollection` è un oggetto `ObservableCollection` di [`Point`](xref:Xamarin.Forms.Point) oggetti. Questa proprietà è supportata da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che può essere la destinazione di data binding e stile.

> [!NOTE]
> La `PolyLineSegment` classe non contiene una proprietà per il punto iniziale della riga. Il punto iniziale della linea è il punto corrente dell'oggetto `PathFigure` a cui `PolyLineSegment` viene aggiunto l'oggetto.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering di un `PolyLineSegment` oggetto in un `Path` oggetto:

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,10">
                    <PathFigure.Segments>
                        <PolyLineSegment Points="50,10 50,50" />
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

In questo esempio l'oggetto `PolyLineSegment` specifica due righe. La prima riga è da (10, 10) a (50, 10) e la seconda riga è da (50, 10) a (50, 50):

![PolyLineSegment](geometry-images/polylinesegment.png "PolyLineSegment")

### <a name="create-a-polyquadraticbeziersegment"></a>Creare un PolyQuadraticBezierSegment

Un oggetto `PolyQuadraticBezierSegment` Crea una o più curve di Bézier quadratiche.

La `PolyQuadraticBezierSegment` classe definisce la `Points` proprietà, di tipo `PointCollection` , che rappresenta i punti che definiscono l'oggetto `PolyQuadraticBezierSegment` . Un oggetto `PointCollection` è un oggetto `ObservableCollection` di [`Point`](xref:Xamarin.Forms.Point) oggetti. Questa proprietà è supportata da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che può essere la destinazione di data binding e stile.

> [!NOTE]
> La `PolyQuadraticBezierSegment` classe non contiene una proprietà per il punto iniziale della curva. Il punto iniziale della curva è il punto corrente dell'oggetto `PathFigure` a cui `PolyQuadraticBezierSegment` viene aggiunto l'oggetto.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering di un `PolyQuadraticBezierSegment` oggetto in un `Path` oggetto:

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyQuadraticBezierSegment Points="100,100 150,50 0,100 15,200" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

In questo esempio, `PolyQuadraticBezierSegment` specifica due curve di Bezier. La prima curva è da (10, 10) a (150, 50) con un punto di controllo in (100.100). La seconda curva è da (100.100) a (15.200) con un punto di controllo in (0100):

![PolyQuadraticBezierSegment](geometry-images/polyquadraticbeziersegment.png "PolyQuadraticBezierSegment")

### <a name="create-a-quadraticbeziersegment"></a>Creare un QuadraticBezierSegment

Un oggetto `QuadraticBezierSegment` Crea una curva di Bézier quadratica tra due punti.

La `QuadraticBezierSegment` classe definisce le proprietà seguenti:

- `Point1`, di tipo [`Point`](xref:Xamarin.Forms.Point) , che rappresenta il punto di controllo della curva. Il valore predefinito di questa proprietà è (0, 0).
- `Point2`, di tipo [`Point`](xref:Xamarin.Forms.Point) , che rappresenta il punto finale della curva. Il valore predefinito di questa proprietà è (0, 0).

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

> [!NOTE]
> La `QuadraticBezierSegment` classe non contiene una proprietà per il punto iniziale della curva. Il punto iniziale della curva è il punto corrente dell'oggetto `PathFigure` a cui `QuadraticBezierSegment` viene aggiunto l'oggetto.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering di un `QuadraticBezierSegment` oggetto in un `Path` oggetto:

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <QuadraticBezierSegment Point1="200,200"
                                                        Point2="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

In questo esempio viene disegnata una curva di Bézier quadratica da (10, 10) a (300, 10). La curva dispone di un punto di controllo in (200.200):

![QuadraticBezierSegment](geometry-images/quadraticbeziersegment.png "QuadraticBezierSegment")

### <a name="create-complex-geometries"></a>Creare geometrie complesse

È possibile creare geometrie più complesse utilizzando una combinazione di `PathSegment` oggetti. Nell'esempio seguente viene creata una forma utilizzando un oggetto `BezierSegment` , un oggetto `LineSegment` e un oggetto `ArcSegment` :

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,50">
                    <PathFigure.Segments>
                        <BezierSegment Point1="100,0"
                                       Point2="200,200"
                                       Point3="300,100"/>
                        <LineSegment Point="400,100" />
                        <ArcSegment Size="50,50"
                                    RotationAngle="45"
                                    IsLargeArc="True"
                                    SweepDirection="Clockwise"
                                    Point="200,100"/>
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

In questo esempio, un `BezierSegment` viene innanzitutto definito utilizzando quattro punti. Nell'esempio viene quindi aggiunto un oggetto `LineSegment` , che viene tracciato tra il punto finale dell'oggetto e `BezierSegment` il punto specificato da `LineSegment` . Infine, `ArcSegment` viene disegnato un oggetto dal punto finale di `LineSegment` al punto specificato da `ArcSegment` .

È possibile creare geometrie ancora più complesse usando più `PathFigure` oggetti all'interno di un oggetto `PathGeometry` . Nell'esempio seguente viene creato un oggetto `PathGeometry` da sette `PathFigure` oggetti, alcuni dei quali contengono più `PathSegment` oggetti:

```xaml
<Path Stroke="Red"
      StrokeThickness="12"
      StrokeLineJoin="Round">
    <Path.Data>
        <PathGeometry>
            <!-- H -->
            <PathFigure StartPoint="0,0">
                <LineSegment Point="0,100" />
            </PathFigure>
            <PathFigure StartPoint="0,50">
                <LineSegment Point="50,50" />
            </PathFigure>
            <PathFigure StartPoint="50,0">
                <LineSegment Point="50,100" />
            </PathFigure>

            <!-- E -->
            <PathFigure StartPoint="125, 0">
                <BezierSegment Point1="60, -10"
                               Point2="60, 60"
                               Point3="125, 50" />
                <BezierSegment Point1="60, 40"
                               Point2="60, 110"
                               Point3="125, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="150, 0">
                <LineSegment Point="150, 100" />
                <LineSegment Point="200, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="225, 0">
                <LineSegment Point="225, 100" />
                <LineSegment Point="275, 100" />
            </PathFigure>

            <!-- O -->
            <PathFigure StartPoint="300, 50">
                <ArcSegment Size="25, 50"
                            Point="300, 49.9"
                            IsLargeArc="True" />
            </PathFigure>
        </PathGeometry>
    </Path.Data>
</Path>
```

In questo esempio la parola "Hello" viene disegnata utilizzando una combinazione `LineSegment` di `BezierSegment` oggetti e, insieme a un singolo `ArcSegment` oggetto:

![Più oggetti PathFigure](geometry-images/multiple-pathfigures.png "Più oggetti PathFigure")

## <a name="composite-geometries"></a>Geometrie composite

È possibile creare oggetti Geometry compositi utilizzando un oggetto `GeometryGroup` . La `GeometryGroup` classe crea una geometria composta da uno o più `Geometry` oggetti. `Geometry`È possibile aggiungere qualsiasi numero di oggetti a un oggetto `GeometryGroup` .

La `GeometryGroup` classe definisce le proprietà seguenti:

- `Children`, di tipo `GeometryCollection` , che specie gli oggetti che definiscono l'oggetto `GeomtryGroup` . Un oggetto `GeometryCollection` è un oggetto `ObservableCollection` di `Geometry` oggetti.
- `FillRule`, di tipo `FillRule` , che specifica il modo in cui le aree di intersezione nell'oggetto `GeometryGroup` vengono combinate. Il valore predefinito di questa proprietà è `FillRule.EvenOdd`.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

> [!NOTE]
> La `Children` proprietà è l'oggetto `ContentProperty` della `GeometryGroup` classe e pertanto non è necessario impostarlo in modo esplicito da XAML.

Per ulteriori informazioni sull' `FillRule` enumerazione, vedere [ Xamarin.Forms forme: regole di riempimento](fillrules.md).

Per creare una geometria composita, impostare gli `Geometry` oggetti richiesti come elementi figlio di un `GeometryGroup` oggetto e visualizzarli con un `Path` oggetto. Il codice XAML seguente mostra un esempio di questo:

```xaml
<Path Stroke="Green"
      StrokeThickness="2"
      Fill="Orange">
    <Path.Data>
        <GeometryGroup>
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,250" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,250" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

In questo esempio, `EllipseGeometry` vengono combinati quattro oggetti con coordinate x-RADIUS e y-RADIUS identici, ma con coordinate del centro diverse. In questo modo vengono creati quattro cerchi sovrapposti, i cui interni sono pieni di arancione a causa della `EvenOdd` regola di riempimento predefinita:

![GeometryGroup](geometry-images/geometrygroup.png "GeometryGroup")

## <a name="clip-with-a-geometry"></a>Ritagliare con una geometria

La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe dispone di una `Clip` proprietà, di tipo `Geometry` , che definisce il contorno del contenuto di un elemento. Quando la `Clip` proprietà è impostata su un `Geometry` oggetto, sarà visibile solo l'area che si trova all'interno dell'area del `Geometry` .

Nell'esempio seguente viene illustrato come utilizzare un `Geometry` oggetto come area di ritaglio per un oggetto [`Image`](xref:Xamarin.Forms.Image) :

```xaml
<Image Source="monkeyface.png">
    <Image.Clip>
        <EllipseGeometry RadiusX="100"
                         RadiusY="100"
                         Center="180,180" />
    </Image.Clip>
</Image>
```

In questo esempio, un `EllipseGeometry` oggetto `RadiusX` con `RadiusY` i valori 100 e il `Center` valore (180.180) viene impostato sulla `Clip` proprietà di un oggetto [`Image`](xref:Xamarin.Forms.Image) . Verrà visualizzata solo la parte dell'immagine che si trova all'interno dell'area dell'ellisse:

![Ritagliare un'immagine con un EllipseGeometry](geometries-images/clip-ellipsegeometry.png "Ritagliare un'immagine con un EllipseGeometry")

> [!NOTE]
> Per ritagliare gli oggetti è possibile usare geometrie semplici, geometrie di percorso e geometrie composite [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

## <a name="other-features"></a>Altre funzionalità

La `GeometryHelper` classe fornisce i metodi helper seguenti:

- `FlattenGeometry`, che rende Flat un oggetto `Geometry` in un oggetto `PathGeometry` .
- `FlattenCubicBezier`, che rende flat una curva di Bezier cubica in una `List<Point>` raccolta.
- `FlattenQuadraticBezier`, che rende flat una curva di Bézier quadratica in una `List<Point>` raccolta.
- `FlattenArc`, che rende Flat un arco ellittico in una `List<Point>` raccolta.

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsForme](index.md)
- [Xamarin.FormsForme: regole di riempimento](fillrules.md)
