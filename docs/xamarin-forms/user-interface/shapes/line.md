---
title: 'Xamarin.FormsForme: linea'
description: La Xamarin.Forms classe line può essere utilizzata per creare linee.
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a69c505fe83618f06bafe6a49b8b5ce84aef096b
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101286"
---
# <a name="xamarinforms-shapes-line"></a>Xamarin.FormsForme: linea

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Line` classe deriva dalla `Shape` classe e può essere usata per creare linee. Per informazioni sulle proprietà `Line` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

`Line` definisce le proprietà seguenti:

- `X1`, di tipo Double, indica la coordinata x del punto iniziale della linea. Il valore predefinito di questa proprietà è 0,0.
- `X2`, di tipo Double, indica la coordinata x del punto finale della linea. Il valore predefinito di questa proprietà è 0,0.
- `Y1`, di tipo Double, indica la coordinata y del punto iniziale della linea. Il valore predefinito di questa proprietà è 0,0.
- `Y2`, di tipo Double, indica la coordinata y del punto finale della linea. Il valore predefinito di questa proprietà è 0,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

## <a name="create-a-line"></a>Crea una riga

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare una riga:

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="DarkBlue"
      StrokeThickness="4"
      HeightRequest="120"
      WidthRequest="120" />
```

> [!NOTE]
> L'impostazione della `Fill` proprietà di un oggetto `Line` non ha alcun effetto, perché una riga non ha interni.

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsForme](index.md)
