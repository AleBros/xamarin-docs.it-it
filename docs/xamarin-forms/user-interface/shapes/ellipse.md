---
title: 'Xamarin.FormsForme: ellisse'
description: La Xamarin.Forms classe Ellipse può essere utilizzata per creare puntini di sospensione e cerchi.
ms.prod: xamarin
ms.assetid: 5BF81E25-12E5-49F0-A40C-0CF4C5D63B9B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aed8d902291272a6bcbdc1ec542b5edacb7668e2
ms.sourcegitcommit: 7fc658bbdcb8130cd9d611e55e79a1830fc5d5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133048"
---
# <a name="xamarinforms-shapes-ellipse"></a>Xamarin.FormsForme: ellisse

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Ellipse` classe deriva dalla `Shape` classe e può essere usata per creare ellissi e cerchi. Per informazioni sulle proprietà `Ellipse` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

La `Ellipse` classe imposta la `Aspect` proprietà, ereditata dalla `Shape` classe, su `Stretch.Fill` . Per altre informazioni sulla `Aspect` proprietà, vedere [forme di estensione](index.md#stretch-shapes).

## <a name="create-an-ellipse"></a>Creare un'ellisse

Per creare un'ellisse, creare un `Ellipse` oggetto e impostare le relative `WidthRequest` `HeightRequest` proprietà e. Per disegnare l'interno dell'ellisse, impostarne la `Fill` proprietà su un oggetto [`Color`](xref:Xamarin.Forms.Color) . Per assegnare un contorno all'ellisse, impostarne la `Stroke` proprietà su un oggetto [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` proprietà specifica lo spessore del contorno ellisse.

Per creare un cerchio, rendere `WidthRequest` uguali le `HeightRequest` proprietà e dell' `Ellipse` oggetto.

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un'ellisse piena:

```xaml
<Ellipse Fill="Red"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

In questo esempio viene disegnata un'ellisse piena rossa con dimensioni 150x50 (unità indipendenti dal dispositivo):

![Ellisse piena](ellipse-images/filled.png "Ellisse piena")

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un cerchio:

```xaml
<Ellipse Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="150"
         HorizontalOptions="Start" />
```

In questo esempio viene disegnato un cerchio rosso con dimensioni 150x150 (unità indipendenti dal dispositivo):

![Cerchio](ellipse-images/circle.png "Circle")

Per informazioni sul disegno di un'ellisse tratteggiata, vedere [forme tratteggiate](index.md#dashed-shapes).

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsForme](index.md)
