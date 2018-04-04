---
title: Margine e spaziatura interna
description: Il margine e spaziatura interna delle proprietà controllare il comportamento di layout quando viene eseguito il rendering di un elemento nell'interfaccia utente. In questo articolo viene illustrata la differenza tra le due proprietà e come impostarle.
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 545468d3b02f9651c45fcaebe159351aafea6432
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="margin-and-padding"></a>Margine e spaziatura interna

_Il margine e spaziatura interna delle proprietà controllare il comportamento di layout quando viene eseguito il rendering di un elemento nell'interfaccia utente. In questo articolo viene illustrata la differenza tra le due proprietà e come impostarle._

## <a name="overview"></a>Panoramica

Margine e spaziatura interna sono concetti di layout correlate:

- Il [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) proprietà rappresenta la distanza tra un elemento e i relativi elementi adiacenti e viene utilizzata per controllare la posizione dell'elemento per il rendering e la posizione di rendering dei vertici vicini. `Margin` è possibile specificare valori in [layout](~/xamarin-forms/user-interface/controls/layouts.md) e [vista](~/xamarin-forms/user-interface/controls/views.md) classi.
- Il [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) proprietà rappresenta la distanza tra un elemento e i relativi elementi figlio e viene utilizzata per separare il controllo dal proprio contenuto. `Padding` è possibile specificare valori in [layout](~/xamarin-forms/user-interface/controls/layouts.md) classi.

Il diagramma seguente illustra due concetti:

[![](margin-and-padding-images/margins-and-padding-sml.png "Margini e spaziatura interna concetti")](margin-and-padding-images/margins-and-padding.png#lightbox "concetti di spaziatura interna e margini")

Si noti che [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) i valori sono additive. Pertanto, se due elementi adiacenti specificano un margine di 20 pixel, la distanza tra gli elementi sono 40 pixel. Inoltre, margini e spaziatura interna sono additive quando entrambi sono applicati, in quanto la distanza tra un elemento e qualsiasi contenuto sarà il margine e spaziatura interna.

## <a name="specifying-a-thickness"></a>Specifica uno spessore

Il [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) e [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) proprietà sono entrambi di tipo [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/). Vi sono tre possibilità quando si crea un `Thickness` struttura:

- Creare un [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) struttura definita da un singolo valore uniforme. Il valore viene applicato a sinistra, superiore, destro e inferiore dell'elemento.
- Creare un [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) struttura definita dai valori orizzontali e verticali. Il valore orizzontale viene applicato in modo simmetrico a sinistra e a destra dell'elemento, con il valore verticale in modo simmetrico viene applicato ai lati superiore e inferiore dell'elemento.
- Creare un [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) struttura definita da quattro valori distinti che vengono applicati a sinistra, superiore, destro e inferiore dell'elemento.

L'esempio di codice XAML seguente mostra tutte le tre possibilità:

```xaml
<StackLayout Padding="0,20,0,0">
  <Label Text="Xamarin.Forms" Margin="20" />
  <Label Text="Xamarin.iOS" Margin="10, 15" />
  <Label Text="Xamarin.Android" Margin="0, 20, 15, 5" />
</StackLayout>
```

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
var stackLayout = new StackLayout {
  Padding = new Thickness(0,20,0,0),
  Children = {
    new Label { Text = "Xamarin.Forms", Margin = new Thickness (20) },
    new Label { Text = "Xamarin.iOS", Margin = new Thickness (10, 25) },
    new Label { Text = "Xamarin.Android", Margin = new Thickness (0, 20, 15, 5) }
  }
};
```

> [!NOTE]
> `Thickness` i valori possono essere negativi, che in genere Taglia o overdraws il contenuto.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrata la differenza tra il [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) e [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) , proprietà e come impostarle. Le proprietà controllano il comportamento di layout quando viene eseguito il rendering di un elemento nell'interfaccia utente.


## <a name="related-links"></a>Collegamenti correlati

- [Margin](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/)
- [Spaziatura interna](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)
- [Spessore](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/)
