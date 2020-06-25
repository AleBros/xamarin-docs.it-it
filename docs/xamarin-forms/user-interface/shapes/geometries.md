---
title: 'Xamarin.FormsForme: geometrie'
description: Xamarin.Formsle classi Geometry consentono di descrivere la geometria di una forma 2D.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 412b0255dafcbd752c7b350ddc25ecd13f8f98d8
ms.sourcegitcommit: 8f6cc5208f675c8cfb645bd9ffb0fc1f8ea71411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85326188"
---
# <a name="xamarinforms-shapes-geometries"></a>Xamarin.FormsForme: geometrie

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Geometry` classe e le classi che derivano da esso consentono di descrivere la geometria di una forma 2D. `Geometry`gli oggetti possono essere semplici, ad esempio rettangoli e cerchi, o composti, creati da due o più oggetti Geometry. È possibile creare geometrie più complesse usando la `PathGeometry` classe, che consente di descrivere archi e curve.

Le `Geometry` `Shape` classi e sembrano simili, in quanto entrambe descrivono forme 2D, ma presentano una differenza importante. La classe `Geometry` deriva dalla classe `BindableObject` , mentre la classe `Shape` deriva dalla classe `View` . `Shape`Gli oggetti possono pertanto eseguire il rendering e partecipare al sistema di layout, mentre `Geometry` gli oggetti non possono. Sebbene `Shape` gli oggetti siano più facilmente utilizzabili degli `Geometry` oggetti, `Geometry` gli oggetti sono più versatili. Mentre un `Shape` oggetto viene usato per eseguire il rendering di grafica 2D, un `Geometry` oggetto può essere usato per definire l'area geometrica per la grafica 2D e definire un'area per il ritaglio.

La classe base per tutte le geometrie è la classe astratta `Geometry` . Le classi che derivano da questa classe possono essere raggruppate in tre categorie: geometrie semplici, geometrie di percorso e geometrie composite.

## <a name="simple-geometries"></a>Geometrie semplici

Le classi Geometry semplici sono `EllipseGeometry` , `LineGeometry` e `RectangleGeometry` . Vengono usati per creare forme geometriche di base, ad esempio cerchi, linee e rettangoli. Queste stesse forme, oltre a forme più complesse, possono essere create usando `PathGeometry` o combinando oggetti Geometry, ma queste classi forniscono un mezzo più semplice per la produzione di queste forme geometriche di base.

### <a name="ellipsegeometry"></a>EllipseGeometry

Una geometria ellisse rappresenta la geometria, un'ellisse o un cerchio ed è definita da un punto centrale, da un raggio x e da un raggio y.

La `EllipseGeometry` classe definisce le proprietà seguenti:

- `Center`, di tipo `Point` , che rappresenta il punto centrale della geometria.
- `RadiusX`, di tipo `double` , che rappresenta il valore del raggio x della geometria. Il valore predefinito di questa proprietà è 0,0.
- `RadiusY`, di tipo `double` , che rappresenta il valore del raggio y della geometria. Il valore predefinito di questa proprietà è 0,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering di un oggetto `EllipseGeometry` :

```xaml
<Path Fill="Blue"
      Stroke="Red"
      StrokeThickness="1">
  <Path.Data>
    <EllipseGeometry Center="50,50"
                     RadiusX="50"
                     RadiusY="50" />
  </Path.Data>
</Path>
```

In questo esempio, il centro di `EllipseGeometry` è impostato su (50, 50) e il raggio x e il raggio y sono entrambi impostati su 50. Viene creato un cerchio con un diametro di 100, il cui interno viene disegnato come blu.

### <a name="linegeometry"></a>LineGeometry

Una geometria di linea rappresenta la geometria di una linea e viene definita specificando il punto iniziale della riga e il punto finale.

La `LimeGeometry` classe definisce le proprietà seguenti:

- `StartPoint`, di tipo `Point` , che rappresenta il punto iniziale della linea.
- `EndPoint`, di tipo `Point` , che rappresenta il punto finale della linea.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering di un oggetto `LineGeometry` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <LineGeometry StartPoint="10,20"
                  EndPoint="100,130" />
  </Path.Data>
</Path>
```

In questo esempio `LineGeometry` viene disegnato un oggetto da (10, 20) a (100.130).

### <a name="rectanglegeometry"></a>RectangleGeometry

Una geometria rettangolare rappresenta un rettangolo e viene definita con una `Rect` struttura che ne specifica la posizione relativa e l'altezza e la larghezza.

La `RectangleGeometry` classe definisce le proprietà seguenti:

- `Rect`, di tipo `FormsRect` , che rappresenta le dimensioni del rettangolo.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Nell'esempio seguente viene illustrato come creare ed eseguire il rendering di un oggetto `RectangleGeometry` :

```xaml
<Path Fill="Blue"
      Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <RectangleGeometry Rect="50,50,25,25" />
  </Path.Data>
</Path>
```

La posizione e le dimensioni del rettangolo sono definite da una `Rect` struttura. In questo esempio la posizione è (50, 50) e l'altezza e la larghezza sono entrambe 25, che crea un quadrato.

## <a name="path-geometries"></a>Geometrie percorso

Una geometria del percorso descrive una forma complessa che può essere composta da archi, curve, ellissi, linee e rettangoli.

La `PathGeometry` classe definisce le proprietà seguenti:

- `Figures`, di tipo `PathFigureCollection` , che rappresenta la raccolta di `PathFigure` oggetti che descrivono il contenuto del percorso.
- `FillRule`, di tipo `FillRule` , che determina come vengono combinate le aree di intersezione contenute nella geometria. Il valore predefinito di questa proprietà è `FillRule.EvenOdd`.

> [!NOTE]
> La `Figures` proprietà è l'oggetto `ContentProperty` della `PathGeometry` classe e pertanto non è necessario impostarlo in modo esplicito da XAML.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Per ulteriori informazioni sull' `FillRule` enumerazione, vedere [ Xamarin.Forms forme: regole di riempimento](fillrules.md).

Un oggetto `PathGeometry` è costituito da una raccolta di `PathFigure` oggetti, ognuno dei quali `PathFigure` descrive una forma nella geometria. Ognuno `PathFigure` è costituito da uno o più `PathSegment` oggetti, ognuno dei quali descrive un segmento della forma. Sono disponibili molti tipi di segmenti:

- `ArcSegment`, che crea un arco ellittico tra due punti.
- `BezierSegment`, che crea una curva di Bezier cubica tra due punti.
- `LineSegment`, che crea una linea tra due punti.
- `PolyBezierSegment`, che crea una serie di curve di Bézier cubiche.
- `PolyLineSegment`, che consente di creare una serie di righe.
- `PolyQuadraticBezierSegment`, che crea una serie di curve di Bézier quadratiche.
- `QuadraticBezierSegment`, che crea una curva di Bézier quadratica.

I segmenti all'interno di `PathFigure` vengono combinati in una singola forma geometrica con il punto finale di ogni segmento che è il punto iniziale del segmento successivo. La `StartPoint` proprietà di un oggetto `PathFigure` specifica il punto da cui viene disegnato il primo segmento. Ogni segmento successivo inizia in corrispondenza del punto finale del segmento precedente. È ad esempio possibile definire una linea verticale da a impostando `10,50` `10,150` la `StartPoint` proprietà su `10,50` e creando un oggetto `LineSegment` con un' `Point` impostazione della proprietà `10,150` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
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

## <a name="composite-geometries"></a>Geometrie composite

È possibile creare oggetti Geometry compositi utilizzando un oggetto `GeometryGroup` . La `GeometryGroup` classe crea una geometria composta da uno o più `Geometry` oggetti. `Geometry`È possibile aggiungere qualsiasi numero di oggetti a un oggetto `GeometryGroup` .

Nell'esempio seguente viene illustrato come combinare geometrie in un oggetto `GeometryGroup` :

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

In questo esempio, `EllipseGeometry` vengono combinati quattro oggetti con coordinate x-RADIUS e y-RADIUS identici, ma con coordinate del centro diverse. Questa operazione crea quattro cerchi sovrapposti, i cui interni sono pieni di colore arancione con la `EvenOdd` regola di riempimento.

## <a name="clip-geometries"></a>Ritagliare le geometrie

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

- `FlattenGeometry`
- `FlattenCubicBezier`
- `FlattenQuadraticBezier`
- `FlattenArc`

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsForme](index.md)
- [Xamarin.FormsForme: regole di riempimento](fillrules.md)
