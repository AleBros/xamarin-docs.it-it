---
title: 'Xamarin.FormsForme: polilinea'
description: La Xamarin.Forms classe polilinea può essere usata per creare una serie di linee rette collegate.
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b6c884caa7bfb301f949f353f3c6c3445704aa89
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101362"
---
# <a name="xamarinforms-shapes-polyline"></a>Xamarin.FormsForme: polilinea

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Polyline` classe deriva dalla `Shape` classe e può essere usata per creare una serie di linee rette collegate. Per informazioni sulle proprietà `Polyline` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

`Polyline` definisce le proprietà seguenti:

- `Points`, di tipo `PointCollection` , che è una raccolta di `Point` strutture che descrivono i punti vertice della polilinea.
- `FillRule`, di tipo `FillRule` , che specifica come vengono combinate le aree di intersezione della polilinea. Il valore predefinito di questa proprietà è `FillRule.EvenOdd`.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

## <a name="create-a-polyline"></a>Creare una polilinea

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un poligono:

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          HeightRequest="100"
          WidthRequest="500" />
```

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsForme](index.md)
