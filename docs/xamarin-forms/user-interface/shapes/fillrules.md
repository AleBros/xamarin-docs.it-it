---
title: 'Xamarin.FormsForme: regole di riempimento'
description: Xamarin.FormsLe regole di riempimento delle forme determinano se un punto si trova nell'area di riempimento di una forma.
ms.prod: xamarin
ms.assetid: 5CABB22B-C6BE-43D1-91D9-6E90A4BD5622
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f2ad0104ee7ea5b44df0c6c24ac20e750d1b1be0
ms.sourcegitcommit: 8f6cc5208f675c8cfb645bd9ffb0fc1f8ea71411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85326419"
---
# <a name="xamarinforms-shapes-fill-rules"></a>Xamarin.FormsForme: regole di riempimento

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Diverse Xamarin.Forms classi di forme hanno `FillRule` proprietà di tipo `FillRule` . Sono inclusi `Polygon` , `Polyline` e `GeometryGroup` .

L' `FillRule` enumerazione definisce `EvenOdd` `Nonzero` i membri e. Ogni membro rappresenta una regola diversa per determinare se un punto si trova nell'area di riempimento di una forma.

> [!IMPORTANT]
> Tutte le forme vengono considerate chiuse per gli scopi delle regole di riempimento.

## <a name="evenodd"></a>EvenOdd

La `EvenOdd` regola di riempimento disegna un raggio dal punto all'infinito in qualsiasi direzione e conta il numero di segmenti all'interno della forma intersecata dal raggio. Se questo numero è dispari, il punto si trova all'interno. Se questo numero è pari, il punto è all'esterno.

Nell'esempio di codice XAML seguente viene creata ed eseguito il rendering di una forma composta, con il `FillRule` valore predefinito `EvenOdd` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <!-- FillRule doesn't need to be set, because EvenOdd is the default. -->
        <GeometryGroup>
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

In questo esempio viene visualizzata una forma composta costituita da una serie di anelli concentrici:

![Forma composta con regola di riempimento EvenOdd](fillrule-images/evenodd.png "Forma composta con regola di riempimento EvenOdd")

Nella forma composta si noti che il centro e il terzo anello non sono riempiti. Questo perché un raggio tracciato da qualsiasi punto all'interno di uno di questi due anelli passa attraverso un numero pari di segmenti:

![Forma composita con annotazioni con regola di riempimento EvenOdd](fillrule-images/evenodd-annotated.png "Forma composita con annotazioni con regola di riempimento EvenOdd")

Nell'immagine precedente, i cerchi rossi rappresentano punti e le linee rappresentano raggi arbitrari. Per il punto superiore, i due raggi arbitrari passano ciascuno attraverso un numero pari di segmenti di linea. Pertanto, l'anello in cui si trova il punto non viene riempito. Per il punto inferiore, i due raggi arbitrari passano ciascuno attraverso un numero dispari di segmenti di linea. Pertanto, l'anello in cui si trova il punto viene riempito.

## <a name="nonzero"></a>Diverso da zero

La `Nonzero` regola Fill disegna un raggio dal punto all'infinito in qualsiasi direzione, quindi esamina i punti in cui un segmento della forma incrocia il raggio. Iniziando con un conteggio pari a zero, il conteggio viene incrementato ogni volta che un segmento interseca il raggio da sinistra a destra e diminuisce ogni volta che un segmento interseca il raggio da destra a sinistra. Dopo aver conteggiato le incrociate, se il risultato è zero, il punto è all'esterno del poligono. In caso contrario, è all'interno.

Nell'esempio di codice XAML seguente viene creata ed eseguito il rendering di una forma composta, con `FillRule` impostato su `Nonzero` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <GeometryGroup FillRule="Nonzero">
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

In questo esempio viene visualizzata una forma composta costituita da una serie di anelli concentrici:

![Forma composta con regola di riempimento diversa da zero](fillrule-images/nonzero.png "Forma composta con regola di riempimento diversa da zero")

Nella forma composta si noti che tutti gli anelli sono pieni. Ciò è dovuto al fatto che tutti i segmenti sono in esecuzione nella stessa direzione, quindi un raggio tracciato da qualsiasi punto attraverserà uno o più segmenti e la somma degli incroci sarà diversa da zero:

![Forma composita con annotazioni con regola di riempimento diversa da zero](fillrule-images/nonzero-annotated.png "Forma composita con annotazioni con regola di riempimento diversa da zero")

Nell'immagine sopra le frecce rosse rappresentano la direzione in cui vengono disegnati i segmenti e la freccia nera rappresenta un raggio arbitrario che viene eseguito da un punto nell'anello più interno. A partire da un valore pari a zero, per ogni segmento intersecato dal raggio viene aggiunto un valore di uno, poiché il segmento interseca il raggio da sinistra a destra.

Una forma più complessa con segmenti eseguiti in direzioni diverse è necessaria per illustrare meglio il comportamento della `Nonzero` regola di riempimento. Nell'esempio di codice XAML riportato di seguito viene creata una forma simile all'esempio precedente, con la differenza che viene creata con un `PathGeometry` anziché un oggetto `EllipseGeometry` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF">
     <Path.Data>
         <GeometryGroup FillRule="Nonzero">
             <PathGeometry>
                 <PathGeometry.Figures>
                     <!-- Inner ring -->
                     <PathFigure StartPoint="120,120">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="50,50"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,120" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Second ring -->
                     <PathFigure StartPoint="120,100">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="70,70"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,100" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Third ring  -->
                         <PathFigure StartPoint="120,70">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="100,100"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,70" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Outer ring -->
                     <PathFigure StartPoint="120,300">
                         <PathFigure.Segments>
                             <ArcSegment Size="130,130"
                                         IsLargeArc="True"
                                         SweepDirection="Clockwise"
                                         Point="140,300" />
                         </PathFigure.Segments>
                     </PathFigure>
                 </PathGeometry.Figures>
             </PathGeometry>
         </GeometryGroup>
     </Path.Data>
 </Path>
```

In questo esempio viene disegnata una serie di segmenti di arco che non sono chiusi:

![Forma composta con regola di riempimento diversa da zero](fillrule-images/nonzero-gaps.png "Forma composta con regola di riempimento diversa da zero")

Nell'immagine precedente il terzo arco dal centro non è pieno. Ciò è dovuto al fatto che la somma dei valori di un determinato raggio che attraversa i segmenti nel suo percorso è zero:

![Forma composita con annotazioni con regola di riempimento diversa da zero](fillrule-images/nonzero-gaps-annotated.png "Forma composita con annotazioni con regola di riempimento diversa da zero")

Nell'immagine precedente il cerchio rosso rappresenta un punto, le linee nere rappresentano raggi arbitrari che si spostano dal punto nell'area non riempita e le frecce rosse rappresentano la direzione in cui vengono disegnati i segmenti. Come si può notare, la somma dei valori dei raggi che attraversano i segmenti è zero:

- Il raggio arbitrario che viaggia in diagonale a destra interseca due segmenti eseguiti in direzioni diverse. Pertanto, i segmenti si annullano reciprocamente, restituendo un valore pari a zero.
- Il raggio arbitrario che viaggia in diagonale a sinistra attraversa un totale di sei segmenti. Tuttavia, le intersezioni si annullano reciprocamente in modo che zero sia la somma finale.

Non viene compilata una somma di zero risultati nell'anello.

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsForme](index.md)
