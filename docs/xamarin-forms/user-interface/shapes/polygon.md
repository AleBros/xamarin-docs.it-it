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
ms.openlocfilehash: 2d67a6a4bbea6a4be27f0c0440d9c28ffd5a188f
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101307"
---
# <a name="xamarinforms-shapes-polygon"></a>Xamarin.FormsForme: poligono

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Polygon` classe deriva dalla `Shape` classe e può essere usata per creare poligoni, che sono serie connesse di linee che formano forme chiuse. Per informazioni sulle proprietà `Polygon` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

`Polygon` definisce le proprietà seguenti:

- `Points`, di tipo `PointCollection` , che è una raccolta di `Point` strutture che descrivono i punti vertice del poligono.
- `FillRule`, di tipo `FillRule` , che specifica il modo in cui viene determinato il riempimento interno della forma. Il valore predefinito di questa proprietà è `FillRule.EvenOdd`.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

## <a name="create-a-polygon"></a>Creare un poligono

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un poligono:

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         HeightRequest="100"
         WidthRequest="200" />
```

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsForme](index.md)
