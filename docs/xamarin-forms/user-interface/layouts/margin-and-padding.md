---
title: Margini e spaziatura interna
description: I margini e spaziatura proprietà controllare il comportamento di layout quando viene eseguito il rendering di un elemento nell'interfaccia utente. Questo articolo illustra la differenza tra le due proprietà e come impostarli.
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 595e673c59d23a45cbaf923a0d58faff2000c296
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996616"
---
# <a name="margin-and-padding"></a>Margini e spaziatura interna

_I margini e spaziatura proprietà controllare il comportamento di layout quando viene eseguito il rendering di un elemento nell'interfaccia utente. Questo articolo illustra la differenza tra le due proprietà e come impostarli._

## <a name="overview"></a>Panoramica

Margini e spaziatura interna sono concetti correlati layout:

- Il [ `Margin` ](xref:Xamarin.Forms.View.Margin) rappresenta la distanza tra un elemento e i relativi elementi adiacenti, proprietà e viene utilizzata per controllare la posizione dell'elemento per il rendering e la posizione per il rendering dei vertici vicini. `Margin` è possibile specificare valori in [layout](~/xamarin-forms/user-interface/controls/layouts.md) e [visualizzazione](~/xamarin-forms/user-interface/controls/views.md) classi.
- Il [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) proprietà rappresenta la distanza tra un elemento e i relativi elementi figlio e viene utilizzata per separare il controllo dal proprio contenuto. `Padding` è possibile specificare valori in [layout](~/xamarin-forms/user-interface/controls/layouts.md) classi.

Il diagramma seguente illustra i due concetti:

[![](margin-and-padding-images/margins-and-padding-sml.png "Concetti di spaziatura interna e margini")](margin-and-padding-images/margins-and-padding.png#lightbox "concetti di spaziatura interna e margini")

Si noti che [ `Margin` ](xref:Xamarin.Forms.View.Margin) i valori sono additivi. Pertanto, se due elementi adiacenti specifica un margine pari a 20 pixel, la distanza tra gli elementi sarà 40 pixel. Inoltre, margini e spaziatura interna sono additive quando vengono applicati entrambi, in quanto la distanza tra un elemento e qualsiasi altro contenuto sarà il margine e spaziatura interna.

## <a name="specifying-a-thickness"></a>Specifica uno spessore

Il [ `Margin` ](xref:Xamarin.Forms.View.Margin) e [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) le proprietà sono entrambi di tipo [ `Thickness` ](xref:Xamarin.Forms.Thickness). Sono disponibili tre possibilità quando si crea un `Thickness` struttura:

- Creare un [ `Thickness` ](xref:Xamarin.Forms.Thickness) struttura definita da un singolo valore uniforme. Il singolo valore viene applicato a sinistra, superiore, destro e inferiore dell'elemento.
- Creare un [ `Thickness` ](xref:Xamarin.Forms.Thickness) struttura definita dai valori orizzontali e verticali. Il valore orizzontale in modo simmetrico viene applicato per i lati sinistro e destro dell'elemento, con il valore verticale simmetricamente applichino ai lati superiore e inferiore dell'elemento.
- Creare un [ `Thickness` ](xref:Xamarin.Forms.Thickness) struttura definita da quattro valori distinti che vengono applicati a sinistra, superiore, destro e inferiore dell'elemento.

Esempio di codice XAML seguente mostra tutti i tre possibilità:

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

Questo articolo sono state illustrate la differenza tra il [ `Margin` ](xref:Xamarin.Forms.View.Margin) e [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) proprietà e come impostarli. Le proprietà controllano il comportamento di layout quando viene eseguito il rendering di un elemento nell'interfaccia utente.


## <a name="related-links"></a>Collegamenti correlati

- [Margine](xref:Xamarin.Forms.View.Margin)
- [Spaziatura interna](xref:Xamarin.Forms.Layout.Padding)
- [Spessore](xref:Xamarin.Forms.Thickness)
