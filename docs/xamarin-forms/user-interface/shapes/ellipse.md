---
title: 'Xamarin.FormsForme: ellisse'
description: La Xamarin.Forms classe Ellipse può essere utilizzata per creare puntini di sospensione e cerchi.
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 142deccbcd29548e2d72b7144a01322f9909d98f
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101277"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.FormsForme: ellisse

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Ellipse` classe deriva dalla `Shape` classe e può essere usata per creare ellissi e cerchi. Per informazioni sulle proprietà `Ellipse` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

La `Ellipse` classe imposta la `Aspect` proprietà, ereditata dalla `Shape` classe, su `Stretch.Fill` .

## <a name="create-an-ellipse"></a>Creare un'ellisse

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un'ellisse piena:

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un cerchio:

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsForme](index.md)
