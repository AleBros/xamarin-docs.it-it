---
title: Xamarin.Forms CollectionView EmptyView
description: In CollectionView è possibile specificare una vista vuota che fornisce feedback all'utente quando non sono disponibili dati per la visualizzazione. La vista vuota può essere una stringa, una vista o più visualizzazioni.
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 9302b9a353173b0445d1d696849b4956134e555d
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696900"
---
# <a name="xamarinforms-collectionview-emptyview"></a>Xamarin.Forms CollectionView EmptyView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce le proprietà seguenti che possono essere usate per fornire commenti e suggerimenti degli utenti quando non sono presenti dati da visualizzare:

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), di tipo `object`, la stringa, l'associazione o la vista che verrà visualizzata quando si `null` la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) o quando la raccolta specificata dalla proprietà `ItemsSource` è `null` o vuota. Il valore predefinito è `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), il modello da utilizzare per formattare la `EmptyView` specificata. Il valore predefinito è `null`.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che le proprietà possono essere destinazioni di associazioni dati.

Gli scenari di utilizzo principali per l'impostazione della proprietà [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) sono la visualizzazione di commenti e suggerimenti degli utenti quando un'operazione di filtro su un [`CollectionView`](xref:Xamarin.Forms.CollectionView) non produce dati e visualizza il feedback dell'utente durante il recupero dei dati da un servizio Web.

> [!NOTE]
> Se necessario, la proprietà [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) può essere impostata su una vista che include contenuto interattivo.

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Visualizza una stringa quando i dati non sono disponibili

La proprietà [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) può essere impostata su una stringa che verrà visualizzata quando viene `null` la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) o quando la raccolta specificata dalla proprietà `ItemsSource` è `null` o vuota. Il codice XAML seguente mostra un esempio di questo scenario:

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}"
                EmptyView="No items to display" />
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    EmptyView = "No items to display"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

Il risultato è che, poiché la raccolta associata ai dati è `null`, viene visualizzata la stringa impostata come valore della proprietà [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) :

[![Screenshot di un elenco di riepilogo verticale con una visualizzazione di testo vuota, in iOS e Android](emptyview-images/null-itemssource.png "Elenco verticale di CollectionView con visualizzazione vuota del testo")](emptyview-images/null-itemssource-large.png#lightbox "Elenco verticale di CollectionView con visualizzazione vuota del testo")

## <a name="display-views-when-data-is-unavailable"></a>Visualizzazione delle visualizzazioni quando i dati non sono disponibili

La proprietà [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) può essere impostata su una vista, che verrà visualizzata quando si `null` la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) o quando la raccolta specificata dalla proprietà `ItemsSource` è `null` o vuota. Può trattarsi di una vista singola o di una vista che contiene più visualizzazioni figlio. Nell'esempio di codice XAML riportato di seguito viene illustrata la proprietà `EmptyView` impostata su una vista che contiene più visualizzazioni figlio:

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </CollectionView.EmptyView>
    </CollectionView>
</StackLayout>
```

Il codice C# equivalente è il seguente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new StackLayout
    {
        Children =
        {
            new Label { Text = "No results matched your filter.", ... },
            new Label { Text = "Try a broader filter?", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Quando il [`SearchBar`](xref:Xamarin.Forms.SearchBar) esegue il `FilterCommand`, la raccolta visualizzata dal [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene filtrata per il termine di ricerca archiviato nella proprietà [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Se l'operazione di filtro non restituisce alcun dato, viene visualizzato il [`StackLayout`](xref:Xamarin.Forms.StackLayout) impostato come valore della proprietà [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) :

[![Screenshot di un elenco di ritrovi verticali con visualizzazione vuota personalizzata, in iOS e Android](emptyview-images/filter-multiple-views.png "Elenco verticale di CollectionView con visualizzazione vuota personalizzata")](emptyview-images/filter-multiple-views-large.png#lightbox "Elenco verticale di CollectionView con visualizzazione vuota personalizzata")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Visualizza un tipo personalizzato basato su modelli quando i dati non sono disponibili

La proprietà [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) può essere impostata su un tipo personalizzato, il cui modello viene visualizzato quando la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) è `null` o quando la raccolta specificata dalla proprietà `ItemsSource` è `null` o vuota. La proprietà [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) può essere impostata su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che definisce l'aspetto della `EmptyView`. Il codice XAML seguente mostra un esempio di questo scenario:

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
               Placeholder="Filter" />
    <CollectionView ItemsSource="{Binding Monkeys}">
        <CollectionView.ItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </CollectionView.ItemTemplate>
        <CollectionView.EmptyView>
            <views:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CollectionView.EmptyView>
        <CollectionView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CollectionView.EmptyViewTemplate>
    </CollectionView>
</StackLayout>
```

Il codice C# equivalente è il seguente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = new FilterData { Filter = searchBar.Text },
    EmptyViewTemplate = new DataTemplate(() =>
    {
        return new Label { ... };
    })
};
```

Il tipo di `FilterData` definisce una proprietà `Filter` e un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)corrispondente:

```csharp
public class FilterData : BindableObject
{
    public static readonly BindableProperty FilterProperty = BindableProperty.Create(nameof(Filter), typeof(string), typeof(FilterData), null);

    public string Filter
    {
        get { return (string)GetValue(FilterProperty); }
        set { SetValue(FilterProperty, value); }
    }
}
```

La proprietà [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) è impostata su un oggetto `FilterData` e i dati della proprietà `Filter` vengono associati alla proprietà [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Quando il [`SearchBar`](xref:Xamarin.Forms.SearchBar) esegue il `FilterCommand`, la raccolta visualizzata dal [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene filtrata per il termine di ricerca archiviato nella proprietà `Filter`. Se l'operazione di filtro non restituisce alcun dato, viene visualizzato il [`Label`](xref:Xamarin.Forms.Label) definito nel [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), impostato come valore della proprietà [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) :

[![Screenshot di un elenco di visualizzazione verticale di CollectionView con un modello di visualizzazione vuoto, in iOS e Android](emptyview-images/emptyviewtemplate.png "Elenco verticale di CollectionView con modello di visualizzazione vuoto")](emptyview-images/emptyviewtemplate-large.png#lightbox "Elenco verticale di CollectionView con modello di visualizzazione vuoto")

> [!NOTE]
> Quando si visualizza un tipo personalizzato basato su modelli quando i dati non sono disponibili, è possibile impostare la proprietà [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) su una vista che contiene più visualizzazioni figlio.

## <a name="choose-an-emptyview-at-runtime"></a>Scegliere un EmptyView in fase di esecuzione

Le visualizzazioni che verranno visualizzate come [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) quando i dati non sono disponibili possono essere definiti come [`ContentView`](xref:Xamarin.Forms.ContentView) oggetti in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). La proprietà `EmptyView` può quindi essere impostata su un `ContentView` specifico, in base a una logica di business, in fase di esecuzione. Nell'esempio di codice XAML riportato di seguito viene illustrato un esempio di questo scenario:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.Resources>
        <ContentView x:Key="BasicEmptyView">
            <StackLayout>
                <Label Text="No items to display."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
        <ContentView x:Key="AdvancedEmptyView">
            <StackLayout>
                <Label Text="No results matched your filter."
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
                <Label Text="Try a broader filter?"
                       FontAttributes="Italic"
                       FontSize="12"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </StackLayout>
        </ContentView>
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CollectionView x:Name="collectionView"
                        ItemsSource="{Binding Monkeys}">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    ...
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

Questo codice XAML definisce due oggetti [`ContentView`](xref:Xamarin.Forms.ContentView) nel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)a livello di pagina, con l'oggetto [`Switch`](xref:Xamarin.Forms.Switch) che controlla quale oggetto `ContentView` verrà impostato come valore della proprietà [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) . Quando il [`Switch`](xref:Xamarin.Forms.Switch) viene attivato o disattivato, il gestore dell'evento `OnEmptyViewSwitchToggled` esegue il metodo `ToggleEmptyView`:

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

Il metodo `ToggleEmptyView` imposta la proprietà [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) dell'oggetto `collectionView` su uno dei due oggetti [`ContentView`](xref:Xamarin.Forms.ContentView) archiviati nell' [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), in base al valore della proprietà [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) . Quando il [`SearchBar`](xref:Xamarin.Forms.SearchBar) esegue il `FilterCommand`, la raccolta visualizzata dal [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene filtrata per il termine di ricerca archiviato nella proprietà [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Se l'operazione di filtro non restituisce alcun dato, viene visualizzato il `ContentView` oggetto impostato come `EmptyView` proprietà:

[![Screenshot di un elenco di ritrovi verticali con visualizzazioni vuote invertite, in iOS e Android](emptyview-images/swap.png "Elenco verticale di CollectionView con visualizzazioni vuote scambiate")](emptyview-images/swap-large.png#lightbox "Elenco verticale di CollectionView con visualizzazioni vuote scambiate")

Per altre informazioni sui dizionari risorse, vedere [dizionari risorse Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Scegliere un EmptyViewTemplate in fase di esecuzione

È possibile scegliere l'aspetto dell' [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) in fase di esecuzione, in base al relativo valore, impostando la proprietà [`CollectionView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) su un oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AdvancedTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="BasicTemplate">
            ...
        </DataTemplate>

        <controls:SearchTermDataTemplateSelector x:Key="SearchSelector"
                                                 DefaultTemplate="{StaticResource AdvancedTemplate}"
                                                 OtherTemplate="{StaticResource BasicTemplate}" />
    </ContentPage.Resources>

    <StackLayout Margin="20">
        <SearchBar x:Name="searchBar"
                   SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={x:Reference searchBar}, Path=Text}"
                   Placeholder="Filter" />
        <CollectionView ItemsSource="{Binding Monkeys}"
                        EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                        EmptyViewTemplate="{StaticResource SearchSelector}" />
    </StackLayout>
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CollectionView collectionView = new CollectionView
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

La proprietà [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) è impostata sulla proprietà [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) e la proprietà [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) è impostata su un oggetto `SearchTermDataTemplateSelector`.

Quando il [`SearchBar`](xref:Xamarin.Forms.SearchBar) esegue il `FilterCommand`, la raccolta visualizzata dal [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene filtrata per il termine di ricerca archiviato nella proprietà [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) . Se l'operazione di filtro non produce dati, il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) scelto dall'oggetto `SearchTermDataTemplateSelector` viene impostato come proprietà [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) e visualizzato.

Nell'esempio seguente viene illustrata la classe `SearchTermDataTemplateSelector`:

```csharp
public class SearchTermDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate OtherTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        string query = (string)item;
        return query.ToLower().Equals("xamarin") ? OtherTemplate : DefaultTemplate;
    }
}
```

La classe `SearchTermTemplateSelector` definisce `DefaultTemplate` e `OtherTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà impostate su modelli di dati diversi. L'override del `OnSelectTemplate` restituisce `DefaultTemplate`, che visualizza un messaggio all'utente, quando la query di ricerca non è uguale a "Novell". Quando la query di ricerca è uguale a "Novell", l'override del `OnSelectTemplate` restituisce `OtherTemplate`, che visualizza un messaggio di base per l'utente:

[![Screenshot della selezione di un modello di visualizzazione vuota del runtime di CollectionView in iOS e Android](emptyview-images/datatemplateselector.png "Selezione del modello di visualizzazione vuota di runtime in un oggetto CollectionView")](emptyview-images/datatemplateselector-large.png#lightbox "Selezione del modello di visualizzazione vuota di runtime in un oggetto CollectionView")

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Create a Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Dizionari risorse Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Creare un DataTemplateSelector Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
