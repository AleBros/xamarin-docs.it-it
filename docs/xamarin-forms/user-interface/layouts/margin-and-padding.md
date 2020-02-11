---
title: Margine e spaziatura interna
description: Le proprietà Margin e Padding controllano il comportamento del layout quando viene eseguito il rendering di un elemento nell'interfaccia utente. Questo articolo illustra la differenza tra le due proprietà e come impostarle.
ms.prod: xamarin
ms.assetid: BEB096BB-51DF-410F-B0F1-D235287B0F4A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 66ac81631466131cf1ef44dde39aa768d31b65a1
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70772482"
---
# <a name="margin-and-padding"></a>Margine e spaziatura interna

_Le proprietà Margin e Padding controllano il comportamento del layout quando viene eseguito il rendering di un elemento nell'interfaccia utente. Questo articolo illustra la differenza tra le due proprietà e come impostarle._

## <a name="overview"></a>Panoramica

I margini e la spaziatura interna sono concetti di layout correlati:

- La proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) rappresenta la distanza tra un elemento e i relativi elementi adiacenti e viene utilizzata per controllare la posizione di rendering dell'elemento e la posizione di rendering degli elementi adiacenti. è possibile specificare valori `Margin` nelle classi [layout](~/xamarin-forms/user-interface/controls/layouts.md) e [visualizzazione](~/xamarin-forms/user-interface/controls/views.md) .
- La proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) rappresenta la distanza tra un elemento e i relativi elementi figlio e viene utilizzata per separare il controllo dal proprio contenuto. per le classi di [layout](~/xamarin-forms/user-interface/controls/layouts.md) è possibile specificare valori `Padding`.

Il diagramma seguente illustra i due concetti:

[![](margin-and-padding-images/margins-and-padding-sml.png "Margins and Padding Concepts")](margin-and-padding-images/margins-and-padding.png#lightbox "Margins and Padding Concepts")

Si noti che [`Margin`](xref:Xamarin.Forms.View.Margin) valori sono additivi. Pertanto, se due elementi adiacenti specificano un margine di 20 pixel, la distanza tra gli elementi sarà di 40 pixel. Inoltre, i margini e la spaziatura interna sono additivi quando vengono applicati entrambi, in quanto la distanza tra un elemento e qualsiasi contenuto sarà il margine più la spaziatura interna.

## <a name="specifying-a-thickness"></a>Specifica di uno spessore

Le proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) e [`Padding`](xref:Xamarin.Forms.Layout.Padding) sono entrambe di tipo [`Thickness`](xref:Xamarin.Forms.Thickness). Quando si crea una struttura di `Thickness`, sono disponibili tre possibilità:

- Creare una struttura [`Thickness`](xref:Xamarin.Forms.Thickness) definita da un singolo valore uniforme. Il valore singolo viene applicato ai lati sinistro, superiore, destro e inferiore dell'elemento.
- Creare una struttura [`Thickness`](xref:Xamarin.Forms.Thickness) definita da valori orizzontali e verticali. Il valore orizzontale viene applicato in modo simmetrico ai lati sinistro e destro dell'elemento, con il valore verticale applicato in modo simmetrico ai lati superiore e inferiore dell'elemento.
- Creare una struttura [`Thickness`](xref:Xamarin.Forms.Thickness) definita da quattro valori distinti che vengono applicati ai lati sinistro, superiore, destro e inferiore dell'elemento.

Nell'esempio di codice XAML seguente sono illustrate tutte e tre le possibilità:

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
> i valori di `Thickness` possono essere negativi, che in genere ritagliano o sovratraggono il contenuto.

## <a name="summary"></a>Riepilogo

In questo articolo è stata illustrata la differenza tra le proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) e [`Padding`](xref:Xamarin.Forms.Layout.Padding) e come impostarle. Le proprietà controllano il comportamento del layout quando viene eseguito il rendering di un elemento nell'interfaccia utente.

## <a name="related-links"></a>Collegamenti correlati

- [Margine](xref:Xamarin.Forms.View.Margin)
- [Imbottitura](xref:Xamarin.Forms.Layout.Padding)
- [Spessore](xref:Xamarin.Forms.Thickness)
