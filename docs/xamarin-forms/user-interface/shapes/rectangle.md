---
title: 'Xamarin.FormsForme: rettangolo'
description: La Xamarin.Forms classe Rectangle può essere utilizzata per creare rettangoli.
ms.prod: xamarin
ms.assetid: 2DD663D3-DAEC-495C-AB6D-8A143FC97637
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1fd985aa2997be2b35fe3b22606b891aa0b66cf3
ms.sourcegitcommit: ef3d4a70e70927c4f231b763842c5355f1571d15
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243737"
---
# <a name="xamarinforms-shapes-rectangle"></a>Xamarin.FormsForme: rettangolo

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Rectangle` classe deriva dalla `Shape` classe e può essere usata per creare rettangoli e quadrati. Per informazioni sulle proprietà `Rectangle` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

`Rectangle` definisce le proprietà seguenti:

- `RadiusX`, di tipo `double` , che è il raggio dell'asse x usato per arrotondare gli angoli del rettangolo. Il valore predefinito di questa proprietà è 0,0.
- `RadiusY`, di tipo `double` , che è il raggio dell'asse y usato per arrotondare gli angoli del rettangolo. Il valore predefinito di questa proprietà è 0,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

La `Rectangle` classe imposta la `Aspect` proprietà, ereditata dalla `Shape` classe, su `Stretch.Fill` . Per altre informazioni sulla `Aspect` proprietà, vedere [forme di estensione](index.md#stretch-shapes).

## <a name="create-a-rectangle"></a>Creare un rettangolo

Per creare un rettangolo, creare un `Rectangle` oggetto e impostare le `WidthRequest` relative `HeightRequest` proprietà e. Per disegnare l'interno del rettangolo, impostarne la `Fill` proprietà su un oggetto [`Color`](xref:Xamarin.Forms.Color) . Per assegnare un contorno al rettangolo, impostarne la `Stroke` proprietà su un oggetto [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` proprietà specifica lo spessore del contorno del rettangolo.

Per assegnare gli angoli arrotondati al rettangolo, impostarne le `RadiusX` `RadiusY` proprietà e. Queste proprietà impostano l'asse x e il raggio dell'asse y utilizzati per arrotondare gli angoli del rettangolo.

Per creare un quadrato, rendere `WidthRequest` uguali le `HeightRequest` proprietà e dell' `Rectangle` oggetto.

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un rettangolo pieno:

```xaml
<Rectangle Fill="Red"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

In questo esempio viene disegnato un rettangolo con riempimento rosso con dimensioni 150x50 (unità indipendenti dal dispositivo):

![Rettangolo pieno](rectangle-images/filled.png "Rettangolo pieno")

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un rettangolo pieno con angoli arrotondati:

```xaml
<Rectangle Fill="Blue"
           Stroke="Black"
           StrokeThickness="3"
           RadiusX="50"
           RadiusY="10"
           WidthRequest="200"
           HeightRequest="100"
           HorizontalOptions="Start" />
```

In questo esempio viene disegnato un rettangolo con riempimento blu con angoli arrotondati:

![Rettangolo con angoli arrotondati](rectangle-images/rounded.png "Rettangolo con angoli arrotondati")

Per informazioni sul disegno di un rettangolo tratteggiato, vedere [disegnare forme tratteggiate](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsForme](index.md)
