---
title: "margin e Padding" Description: "le proprietà Margin e Padding controllano il comportamento del layout quando viene eseguito il rendering di un elemento nell'interfaccia utente. Questo articolo illustra la differenza tra le due proprietà e come impostarle ".
ms. prod: Novell MS. AssetID: BEB096BB-51DF-410F-B0F1-D235287B0F4A ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/27/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="margin-and-padding"></a>Margine e spaziatura interna

_Le proprietà Margin e Padding controllano il comportamento del layout quando viene eseguito il rendering di un elemento nell'interfaccia utente. Questo articolo illustra la differenza tra le due proprietà e come impostarle._

## <a name="overview"></a>Panoramica

I margini e la spaziatura interna sono concetti di layout correlati:

- La [`Margin`](xref:Xamarin.Forms.View.Margin) proprietà rappresenta la distanza tra un elemento e i relativi elementi adiacenti e viene utilizzata per controllare la posizione di rendering dell'elemento e la posizione di rendering degli elementi adiacenti. `Margin`è possibile specificare i valori nelle classi [layout](~/xamarin-forms/user-interface/controls/layouts.md) e [visualizzazione](~/xamarin-forms/user-interface/controls/views.md) .
- La [`Padding`](xref:Xamarin.Forms.Layout.Padding) proprietà rappresenta la distanza tra un elemento e i relativi elementi figlio e viene utilizzata per separare il controllo dal proprio contenuto. `Padding`è possibile specificare i valori nelle classi di [layout](~/xamarin-forms/user-interface/controls/layouts.md) .

Il diagramma seguente illustra i due concetti:

[![](margin-and-padding-images/margins-and-padding-sml.png "Margins and Padding Concepts")](margin-and-padding-images/margins-and-padding.png#lightbox "Margins and Padding Concepts")

Si noti che [`Margin`](xref:Xamarin.Forms.View.Margin) i valori sono additivi. Pertanto, se due elementi adiacenti specificano un margine di 20 pixel, la distanza tra gli elementi sarà di 40 pixel. Inoltre, i margini e la spaziatura interna sono additivi quando vengono applicati entrambi, in quanto la distanza tra un elemento e qualsiasi contenuto sarà il margine più la spaziatura interna.

## <a name="specifying-a-thickness"></a>Specifica di uno spessore

Le [`Margin`](xref:Xamarin.Forms.View.Margin) [`Padding`](xref:Xamarin.Forms.Layout.Padding) proprietà e sono entrambe di tipo [`Thickness`](xref:Xamarin.Forms.Thickness) . Sono disponibili tre possibilità per la creazione di una `Thickness` struttura:

- Creare una [`Thickness`](xref:Xamarin.Forms.Thickness) struttura definita da un singolo valore uniforme. Il valore singolo viene applicato ai lati sinistro, superiore, destro e inferiore dell'elemento.
- Creare una [`Thickness`](xref:Xamarin.Forms.Thickness) struttura definita da valori orizzontali e verticali. Il valore orizzontale viene applicato in modo simmetrico ai lati sinistro e destro dell'elemento, con il valore verticale applicato in modo simmetrico ai lati superiore e inferiore dell'elemento.
- Creare una [`Thickness`](xref:Xamarin.Forms.Thickness) struttura definita da quattro valori distinti che vengono applicati ai lati sinistro, superiore, destro e inferiore dell'elemento.

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
> `Thickness`i valori possono essere negativi, che in genere ritagliano o sovratraggono il contenuto.

## <a name="summary"></a>Summary

In questo articolo è stata illustrata la differenza tra le [`Margin`](xref:Xamarin.Forms.View.Margin) [`Padding`](xref:Xamarin.Forms.Layout.Padding) proprietà e e come impostarle. Le proprietà controllano il comportamento del layout quando viene eseguito il rendering di un elemento nell'interfaccia utente.

## <a name="related-links"></a>Collegamenti correlati

- [Margin](xref:Xamarin.Forms.View.Margin)
- [Spaziatura interna](xref:Xamarin.Forms.Layout.Padding)
- [Thickness](xref:Xamarin.Forms.Thickness)
