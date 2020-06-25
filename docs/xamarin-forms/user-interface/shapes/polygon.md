---
title: 'Xamarin.FormsForme: poligono'
description: La Xamarin.Forms classe Polygon può essere utilizzata per creare poligoni, che sono serie connesse di linee che formano forme chiuse.
ms.prod: xamarin
ms.assetid: D6539F60-A5AC-46EF-86EB-E9F508EB1FA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9c90b0ae4ae3d241dcd6d6d749f220dcfb683693
ms.sourcegitcommit: 8f6cc5208f675c8cfb645bd9ffb0fc1f8ea71411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85326146"
---
# <a name="xamarinforms-shapes-polygon"></a>Xamarin.FormsForme: poligono

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Polygon` classe deriva dalla `Shape` classe e può essere usata per creare poligoni, che sono serie connesse di linee che formano forme chiuse. Per informazioni sulle proprietà `Polygon` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

`Polygon` definisce le proprietà seguenti:

- `Points`, di tipo `PointCollection` , che è una raccolta di [`Point`](xref:Xamarin.Forms.Point) strutture che descrivono i punti vertice del poligono.
- `FillRule`, di tipo `FillRule` , che specifica il modo in cui viene determinato il riempimento interno della forma. Il valore predefinito di questa proprietà è `FillRule.EvenOdd`.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Il `PointsCollection` tipo è un oggetto `ObservableCollection` di [`Point`](xref:Xamarin.Forms.Point) oggetti. La `Point` struttura definisce `X` le `Y` proprietà e, di tipo `double` , che rappresentano una coppia di coordinate x e y nello spazio 2D. Pertanto, la `Points` proprietà deve essere impostata su un elenco di coppie di coordinate x e y che descrivono i punti vertice del poligono, delimitati da una singola virgola e/o uno o più spazi. Ad esempio, "40, 10 70, 80" e "40 10, 70 80" sono entrambi validi.

Per ulteriori informazioni sull' `FillRule` enumerazione, vedere [ Xamarin.Forms forme: regole di riempimento](fillrules.md).

## <a name="create-a-polygon"></a>Creare un poligono

Per disegnare un poligono, creare un `Polygon` oggetto e impostare la relativa `Points` proprietà sui vertici di una forma. Viene disegnata automaticamente una linea che connette il primo e l'ultimo punto. Per disegnare l'interno del poligono, impostarne la `Fill` proprietà su un oggetto [`Color`](xref:Xamarin.Forms.Color) . Per assegnare un contorno al poligono, impostarne la `Stroke` proprietà su un oggetto [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` proprietà specifica lo spessore del contorno poligono.

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un poligono pieno:

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5" />
```

In questo esempio viene disegnato un poligono pieno che rappresenta un triangolo:

![Poligono pieno](polygon-images/filled.png "Poligono pieno")

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un poligono tratteggiato:

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         StrokeDashArray="1,1"
         StrokeDashOffset="6" />
```

In questo esempio, il contorno poligono è tratteggiato:

![Poligono tratteggiato](polygon-images/dashed.png "Poligono tratteggiato")

Per altre informazioni su come disegnare un poligono tratteggiato, vedere [disegnare forme tratteggiate](index.md#draw-dashed-shapes).

Nell'esempio di codice XAML seguente viene illustrato un poligono che usa la regola di riempimento predefinita:

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Blue"
         Stroke="Red"
         StrokeThickness="3" />
```

In questo esempio, il comportamento di riempimento di ogni poligono viene determinato mediante la `EvenOdd` regola Fill.

![Poligono EvenOdd](polygon-images/evenodd.png "Poligono EvenOdd")

Nell'esempio di codice XAML riportato di seguito viene illustrato un poligono che utilizza la `Nonzero` regola di riempimento:

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Black"
         FillRule="Nonzero"
         Stroke="Yellow"
         StrokeThickness="3" />
```

![Poligono diverso da zero](polygon-images/nonzero.png "Poligono diverso da zero")

In questo esempio, il comportamento di riempimento di ogni poligono viene determinato mediante la `Nonzero` regola Fill.

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsForme](index.md)
- [Xamarin.FormsForme: regole di riempimento](fillrules.md)
