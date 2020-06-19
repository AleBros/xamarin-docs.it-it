---
title: 'Xamarin.FormsForme: rettangolo'
description: La Xamarin.Forms classe Rectangle può essere utilizzata per creare rettangoli.
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: da9649a4abb2cb65930d98576eda81739b711886
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101365"
---
# <a name="xamarinforms-shapes-rectangle"></a>Xamarin.FormsForme: rettangolo

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Rectangle` classe deriva dalla `Shape` classe e può essere utilizzata per creare rettangoli. Per informazioni sulle proprietà `Rectangle` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

`Rectangle` definisce le proprietà seguenti:

- `RadiusX`, di tipo `double` , che è il raggio dell'asse x usato per arrotondare gli angoli del rettangolo. Il valore predefinito di questa proprietà è 0,0.
- `RadiusY`, di tipo `double` , che è il raggio dell'asse y usato per arrotondare gli angoli del rettangolo. Il valore predefinito di questa proprietà è 0,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

La `Rectangle` classe imposta la `Aspect` proprietà, ereditata dalla `Shape` classe, su `Stretch.Fill` .

## <a name="create-a-rectangle"></a>Creare un rettangolo

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un rettangolo pieno con angoli arrotondati:

```xaml
<Rectangle Fill="DarkBlue"
           Stroke="Red"
           StrokeThickness="4"
           RadiusX="12"
           RadiusY="24"           
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsForme](index.md)
