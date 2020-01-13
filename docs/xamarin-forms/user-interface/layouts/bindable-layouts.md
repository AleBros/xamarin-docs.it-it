---
title: Layout associabili in Xamarin.Forms
description: I layout associabili consentono alle classi di layout di generare il contenuto mediante associazione a una raccolta di elementi, con l'opzione per impostare l'aspetto di ogni elemento con un oggetto DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: a824c892d21df9264b772bed09a4aef893f3b949
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68647900"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Layout associabili in Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

I layout associabili abilitano qualsiasi classe di layout che deriva dalla classe [`Layout<T>`](xref:Xamarin.Forms.Layout`1) per generare il contenuto mediante l'associazione a una raccolta di elementi, con l'opzione per impostare l'aspetto di ogni elemento con una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). I layout associabili sono forniti dalla classe `BindableLayout`, che espone le proprietà associate seguenti:

- `ItemsSource`: specifica la raccolta di elementi `IEnumerable` da visualizzare nel layout.
- `ItemTemplate`: specifica il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) da applicare a ogni elemento della raccolta di elementi visualizzati dal layout.
- `ItemTemplateSelector`: specifica il [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) che verrà usato per scegliere un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) per un elemento in fase di esecuzione.

Queste proprietà possono essere associate alle classi [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [`FlexLayout`](xref:Xamarin.Forms.FlexLayout), [`Grid`](xref:Xamarin.Forms.Grid), [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)e [`StackLayout`](xref:Xamarin.Forms.StackLayout) , che derivano tutte dalla classe [1](xref:Xamarin.Forms.Layout`1) .

> [!NOTE]
> La proprietà `ItemTemplate` ha la precedenza quando vengono impostate entrambe le proprietà `ItemTemplate` e `ItemTemplateSelector`.

La classe `Layout<T>` espone una raccolta [`Children`](xref:Xamarin.Forms.Layout`1.Children) a cui vengono aggiunti gli elementi figlio di un layout. Quando la proprietà `BinableLayout.ItemsSource` è impostata su una raccolta di elementi e collegata a una classe derivata da [`Layout<T>`](xref:Xamarin.Forms.Layout`1), ogni elemento della raccolta viene aggiunto alla raccolta di `Layout<T>.Children` per la visualizzazione da parte del layout. La classe derivata da `Layout<T>` aggiornerà quindi le visualizzazioni figlio quando la raccolta sottostante viene modificata. Per ulteriori informazioni sul ciclo di layout di Xamarin.Forms, vedere [creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md).

I layout associabili devono essere usati solo quando la raccolta di elementi da visualizzare è di piccole dimensioni e lo scorrimento e la selezione non sono necessari. Sebbene sia possibile fornire lo scorrimento eseguendo il wrapping di un layout associabile in una [`ScrollView`](xref:Xamarin.Forms.ScrollView), questa operazione non è consigliata perché i layout associabili non hanno la virtualizzazione dell'interfaccia utente. Quando è necessario lo scorrimento, è necessario utilizzare una visualizzazione scorrevole che includa la virtualizzazione dell'interfaccia utente, ad esempio [`ListView`](xref:Xamarin.Forms.ListView) o [`CollectionView`](xref:Xamarin.Forms.CollectionView). L'impossibilità di osservare questa raccomandazione può causare problemi di prestazioni.

> [!IMPORTANT]
>Sebbene sia tecnicamente possibile aggiungere un layout associabile a qualsiasi classe di layout che deriva dalla classe [`Layout<T>`](xref:Xamarin.Forms.Layout`1) , non è sempre praticabile, in particolare per le classi [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [`Grid`](xref:Xamarin.Forms.Grid)e [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Si consideri, ad esempio, lo scenario in cui si desidera visualizzare una raccolta di dati in un [`Grid`](xref:Xamarin.Forms.Grid) utilizzando un layout associabile, in cui ogni elemento della raccolta è un oggetto che contiene più proprietà. Ogni riga nel `Grid` dovrebbe visualizzare un oggetto dalla raccolta, in cui ogni colonna della `Grid` Visualizza una delle proprietà dell'oggetto. Poiché le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) per il layout associabile possono contenere solo un singolo oggetto, è necessario che l'oggetto sia una classe layout contenente più visualizzazioni che ognuna Visualizza una delle proprietà dell'oggetto in una colonna `Grid` specifica. Sebbene questo scenario possa essere realizzato con layout associabili, il risultato è un `Grid` padre contenente un `Grid` figlio per ogni elemento nella raccolta associata, che è un utilizzo estremamente inefficiente e problematico del layout di `Grid`.

## <a name="populating-a-bindable-layout-with-data"></a>Popolamento di un layout associabile con dati

Un layout associabile viene popolato con i dati impostando la relativa proprietà `ItemsSource` su qualsiasi raccolta che implementi `IEnumerable` e la associa a una classe derivata da [`Layout<T>`](xref:Xamarin.Forms.Layout`1):

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

Il codice C# equivalente è il seguente:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Quando il `BindableLayout.ItemsSource` proprietà associata è impostata su un layout, ma la proprietà `BindableLayout.ItemTemplate` associata non è impostata, ogni elemento nella raccolta `IEnumerable` verrà visualizzato da un [`Label`](xref:Xamarin.Forms.Label) creato dalla classe `BindableLayout`.

## <a name="defining-item-appearance"></a>Definizione dell'aspetto dell'elemento

L'aspetto di ogni elemento nel layout associabile può essere definito impostando la proprietà associata `BindableLayout.ItemTemplate` su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

Il codice C# equivalente è il seguente:

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

In questo esempio, ogni elemento nella raccolta `TopFollowers` sarà visualizzato da una visualizzazione `CircleImage` definita nella [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

![Layout associabile a un oggetto DataTemplate](bindable-layouts-images/top-followers.png "Layout associabile con un modello di dati")

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choosing-item-appearance-at-runtime"></a>Scelta dell'aspetto dell'elemento in fase di esecuzione

È possibile scegliere l'aspetto di ogni elemento nel layout associabile in fase di esecuzione, in base al valore dell'elemento, impostando il `BindableLayout.ItemTemplateSelector` proprietà associata su un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

Il codice C# equivalente è il seguente:

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

Il [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) usato nell'applicazione di esempio è illustrato nell'esempio seguente:

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

La classe `TechItemTemplateSelector` definisce `DefaultTemplate` e `XamarinFormsTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà impostate su modelli di dati diversi. Il metodo `OnSelectTemplate` restituisce il `XamarinFormsTemplate`, che visualizza un elemento in rosso scuro con un cuore accanto, quando l'elemento è uguale a "Xamarin.Forms". Quando l'elemento non è uguale a "Xamarin.Forms", il metodo `OnSelectTemplate` restituisce il `DefaultTemplate`, che visualizza un elemento usando il colore predefinito di un [`Label`](xref:Xamarin.Forms.Label):

![Layout associabile con DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Layout associabile con un selettore di modello di dati")

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Creating a Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Collegamenti correlati

- [Demo sul layout associabile (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Creazione di un layout personalizzato](~/xamarin-forms/user-interface/layouts/custom.md)
- [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creazione di un DataTemplateSelector Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
