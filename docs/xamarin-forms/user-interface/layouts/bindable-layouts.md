---
title: Layout associabile in xamarin. Forms
description: Layout associabile Abilita classi di layout generare il contenuto mediante l'associazione a una raccolta di elementi, con l'opzione per impostare l'aspetto di ogni elemento con un DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: b0e2d5e3c7923e5c3cf2adcc1dd104a97b78e727
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61321560"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Layout associabile in xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)

Layout associabile consentono a qualsiasi classe di layout che deriva dal [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) classe per cui generare il contenuto mediante l'associazione a una raccolta di elementi, con l'opzione per impostare l'aspetto di ogni elemento con un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Layout associabile forniti dal `BindableLayout` (classe), che espone le proprietà associate seguenti:

- `ItemsSource` : specifica la raccolta di `IEnumerable` gli elementi da visualizzare per il layout.
- `ItemTemplate` : specifica la [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) da applicare a ogni elemento nella raccolta di elementi visualizzati dal layout.
- `ItemTemplateSelector` : specifica la [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) che verrà usato per scegliere una [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) per un elemento in fase di esecuzione.

Queste proprietà possono essere collegate per la [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout), [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout), [ `Grid` ](xref:Xamarin.Forms.Grid), [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout) , e [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) classi che derivano da tutti i [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) classe.

> [!NOTE]
> Il `ItemTemplate` proprietà ha la precedenza quando sia il `ItemTemplate` e `ItemTemplateSelector` proprietà vengono impostate.

Il `Layout<T>` classe espone un [ `Children` ](xref:Xamarin.Forms.Layout`1.Children) insieme, in cui vengono aggiunti gli elementi figlio di un layout. Quando la `BinableLayout.ItemsSource` proprietà è impostata su una raccolta di elementi e collegato a un [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)-viene aggiunta la classe derivata, ogni elemento nella raccolta la `Layout<T>.Children` raccolta per la visualizzazione per il layout. Il `Layout<T>`-classe derivata aggiornerà le relative visualizzazioni figlio quando viene modificata la raccolta sottostante. Per altre informazioni sul ciclo di layout di xamarin. Forms, vedere [creazione di un Layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md).

> [!IMPORTANT]
> Layout associabili deve essere utilizzato solo quando la raccolta di elementi da visualizzare è piccola e lo scorrimento e la selezione non è necessaria. Durante lo scorrimento è possibile specificare eseguendo il wrapping di un layout associabile in una [ `ScrollView` ](xref:Xamarin.Forms.ScrollView), ciò non è consigliata perché layout associabile non dispongono di virtualizzazione dell'interfaccia utente. Una visualizzazione scorrevole che comprende la virtualizzazione dell'interfaccia utente, ad esempio, quando lo scorrimento è necessario [ `ListView` ](xref:Xamarin.Forms.ListView) o `CollectionView`, deve essere utilizzato. Il mancato rispetto di questa raccomandazione può causare problemi di prestazioni.

## <a name="populating-a-bindable-layout-with-data"></a>Popolamento di un layout con i dati associabile

Un layout associabile viene popolato con i dati tramite l'impostazione relativa `ItemsSource` a qualsiasi insieme che implementa `IEnumerable`e allegarlo a un [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)-classe derivata:

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

Il codice C# equivalente è:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Quando la `BindableLayout.ItemsSource` proprietà associata è impostata su un layout, ma la `BindableLayout.ItemTemplate` proprietà associata non è impostato, tutti gli elementi `IEnumerable` raccolta verrà visualizzata da una [ `Label` ](xref:Xamarin.Forms.Label) creato dal `BindableLayout` classe.

## <a name="defining-item-appearance"></a>La definizione dell'aspetto degli elementi

L'aspetto di ogni elemento nel layout associabile può essere definita impostando il `BindableLayout.ItemTemplate` associata a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding User.TopFollowers}"
             Orientation="Horizontal"
             ...>
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CircleImage Source="{Binding}"
                                  Aspect="AspectFill"
                                  WidthRequest="44"
                                  HeightRequest="44"
                                  ... />
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

Il codice C# equivalente è:

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

In questo esempio, tutti gli elementi di `TopFollowers` raccolta verrà visualizzata da un `CircleImage` le viste definite nel [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

![Layout associabili con un DataTemplate](bindable-layouts-images/top-followers.png "associabile layout con un modello di dati")

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choosing-item-appearance-at-runtime"></a>Scelta dell'aspetto degli elementi in fase di esecuzione

È possibile scegliere l'aspetto di ogni elemento nel layout associabile in fase di esecuzione, basati sul valore dell'elemento, impostando il `BindableLayout.ItemTemplateSelector` associata a un [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

Il codice C# equivalente è:

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

Il [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) usato nell'esempio di applicazione è illustrata nell'esempio seguente:

```csharp
public class TechItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinFormsTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return (string)item == "Xamarin.Forms" ? XamarinFormsTemplate : DefaultTemplate;
    }
}
```

Il `TechItemTemplateSelector` classe definisce `DefaultTemplate` e `XamarinFormsTemplate` [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) le proprietà impostate per i modelli di dati diversi. Il `OnSelectTemplate` restituzione del metodo di `XamarinFormsTemplate`, che consente di visualizzare un elemento in rosso scuro con un cuore accanto a esso, quando l'elemento è uguale a "Xamarin. Forms". Quando l'elemento non è uguale a "Xamarin. Forms", il `OnSelectTemplate` metodo restituisce il `DefaultTemplate`, che consente di visualizzare un elemento utilizzando il colore predefinito di un [ `Label` ](xref:Xamarin.Forms.Label):

![Layout associabili con un DataTemplateSelector](bindable-layouts-images/favorite-tech.png "associabile layout con un selettore di modello di dati")

Per altre informazioni sui selettori di modello di dati, vedere [creando un DataTemplateSelector xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Collegamenti correlati

- [Demo di Layout associabile (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)
- [Creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md)
- [Modelli di dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creazione di un DataTemplateSelector xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
