---
title: 'Xamarin.FormsForme: polilinea'
description: La Xamarin.Forms classe polilinea può essere usata per creare una serie di linee rette collegate.
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fee7dd2a2e5b713b3a82fc2e1227b21caddbceaa
ms.sourcegitcommit: ef3d4a70e70927c4f231b763842c5355f1571d15
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243820"
---
# <a name="xamarinforms-shapes-polyline"></a>Xamarin.FormsForme: polilinea

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Polyline` classe deriva dalla `Shape` classe e può essere usata per creare una serie di linee rette collegate. Una polilinea è simile a un poligono, ad eccezione del fatto che l'ultimo punto in una polilinea non è connesso al primo punto. Per informazioni sulle proprietà `Polyline` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

`Polyline` definisce le proprietà seguenti:

- `Points`, di tipo `PointCollection` , che è una raccolta di `Point` strutture che descrivono i punti vertice della polilinea.
- `FillRule`, di tipo `FillRule` , che specifica come vengono combinate le aree di intersezione della polilinea. Il valore predefinito di questa proprietà è `FillRule.EvenOdd`.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Il `PointsCollection` tipo è un oggetto `ObservableCollection` di [`Point`](xref:Xamarin.Forms.Point) oggetti. La `Point` struttura definisce `X` le `Y` proprietà e, di tipo `double` , che rappresentano una coppia di coordinate x e y nello spazio 2D. Pertanto, la `Points` proprietà deve essere impostata su un elenco di coppie di coordinate x e y che descrivono i punti di vertice della polilinea, delimitati da una singola virgola e/o uno o più spazi. Ad esempio, "40, 10 70, 80" e "40 10, 70 80" sono entrambi validi.

L'enumerazione `FillRule` definisce i membri seguenti:

- `EvenOdd`rappresenta una regola che determina se un punto si trova nell'area di riempimento della polilinea. Disegna un raggio dal punto all'infinito in qualsiasi direzione e conta il numero di segmenti all'interno della forma intersecata dal raggio. Se questo numero è dispari, il punto si trova all'interno. Se questo numero è pari, il punto è all'esterno.
- `Nonzero`rappresenta una regola che determina se un punto si trova nell'area di riempimento della polilinea. Disegna un raggio dal punto all'infinito in qualsiasi direzione e quindi esamina i punti in cui un segmento della forma incrocia il raggio. Iniziando con un conteggio pari a zero, il conteggio viene incrementato ogni volta che un segmento interseca il raggio da sinistra a destra e diminuisce ogni volta che un segmento interseca il raggio da destra a sinistra. Dopo aver conteggiato le incrociate, se il risultato è zero, il punto è all'esterno della polilinea. In caso contrario, è all'interno.

## <a name="create-a-polyline"></a>Creare una polilinea

Per disegnare una polilinea, creare un `Polyline` oggetto e impostare la relativa `Points` proprietà sui vertici di una forma. Per assegnare una struttura alla polilinea, impostarne la `Stroke` proprietà su un oggetto [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` proprietà specifica lo spessore del contorno della polilinea.

> [!IMPORTANT]
> Se si imposta la `Fill` proprietà di un `Polyline` su un oggetto [`Color`](xref:Xamarin.Forms.Color) , lo spazio interno della polilinea viene disegnato anche se il punto iniziale e il punto finale non si intersecano.

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare una polilinea:

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red" />
```

In questo esempio viene disegnata una polilinea rossa:

![Polilinea](polyline-images/stroke.png "Polilinea")

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare una polilinea tratteggiata:

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          StrokeThickness="2"
          StrokeDashArray="1,1"
          StrokeDashOffset="6" />
```

In questo esempio, la polilinea è tratteggiata:

![Polilinea tratteggiata](polyline-images/dashed.png "Polilinea tratteggiata")

Per altre informazioni sul disegno di una polilinea tratteggiata, vedere [disegnare forme tratteggiate](index.md#draw-dashed-shapes).

Nell'esempio di codice XAML riportato di seguito viene illustrata una polilinea che utilizza la regola di riempimento predefinita:

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Blue"
          Stroke="Red"
          StrokeThickness="3" />
```

In questo esempio, il comportamento di riempimento della polilinea viene determinato mediante la `EvenOdd` regola Fill.

![Polilinea EvenOdd](polyline-images/evenodd.png "Polyine EvenOdd")

Nell'esempio di codice XAML riportato di seguito viene illustrata una polilinea che utilizza la `Nonzero` regola di riempimento:

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Black"
          FillRule="Nonzero"
          Stroke="Yellow"
          StrokeThickness="3" />
```

![Polilinea diversa da zero](polyline-images/nonzero.png "Polilinea diversa da zero")

In questo esempio, il comportamento di riempimento della polilinea viene determinato mediante la `Nonzero` regola Fill.

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsForme](index.md)
