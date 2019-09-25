---
title: Novell. Forms CollectionView EmptyView
description: In CollectionView è possibile specificare una vista vuota che fornisce feedback all'utente quando non sono disponibili dati per la visualizzazione. La vista vuota può essere una stringa, una vista o più visualizzazioni.
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: c6a2a53f267a7f6764ec441944193e8c5ecd9189
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "68739184"
---
# <a name="xamarinforms-collectionview-emptyview"></a>Novell. Forms CollectionView EmptyView

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce le proprietà seguenti che possono essere usate per fornire commenti e suggerimenti degli utenti quando non sono presenti dati da visualizzare:

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), di tipo `object`, la stringa, l'associazione o la vista che verrà visualizzata quando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) proprietà è `null`o quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Il valore predefinito è `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), il modello da utilizzare per formattare l'oggetto `EmptyView`specificato. Il valore predefinito è `null`.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

Gli scenari di utilizzo principali per l' [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) impostazione della proprietà sono la visualizzazione di commenti e suggerimenti degli [`CollectionView`](xref:Xamarin.Forms.CollectionView) utenti quando un'operazione di filtro in un oggetto non produce dati e visualizza il feedback dell'utente mentre i dati vengono recuperati da un servizio Web.

> [!NOTE]
> Se [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) necessario, la proprietà può essere impostata su una vista che include contenuto interattivo.

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Visualizza una stringa quando i dati non sono disponibili

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà può essere impostata su una stringa che verrà visualizzata quando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) proprietà è `null`o quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Il codice XAML seguente mostra un esempio di questo scenario:

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

Il risultato è che, poiché la raccolta associata ai dati `null`è, viene visualizzata la stringa [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) impostata come valore della proprietà:

[![Screenshot di un elenco di riepilogo verticale con una visualizzazione di testo vuota, in iOS e Android](emptyview-images/null-itemssource.png "Elenco verticale di CollectionView con visualizzazione vuota del testo")](emptyview-images/null-itemssource-large.png#lightbox "Elenco verticale di CollectionView con visualizzazione vuota del testo")

## <a name="display-views-when-data-is-unavailable"></a>Visualizzazione delle visualizzazioni quando i dati non sono disponibili

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà può essere impostata su una visualizzazione, che verrà visualizzata quando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) proprietà è `null`o quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Può trattarsi di una vista singola o di una vista che contiene più visualizzazioni figlio. Nell'esempio di codice XAML riportato `EmptyView` di seguito viene illustrata la proprietà impostata su una vista che contiene più visualizzazioni figlio:

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

Quando esegue l'oggetto `FilterCommand`, [`CollectionView`](xref:Xamarin.Forms.CollectionView) la raccolta visualizzata da viene [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) filtrata in base al termine di ricerca archiviato nella proprietà. [`SearchBar`](xref:Xamarin.Forms.SearchBar) Se l'operazione di filtro non produce dati, viene [`StackLayout`](xref:Xamarin.Forms.StackLayout) visualizzato il set [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) come valore della proprietà:

[![Screenshot di un elenco di ritrovi verticali con visualizzazione vuota personalizzata, in iOS e Android](emptyview-images/filter-multiple-views.png "Elenco verticale di CollectionView con visualizzazione vuota personalizzata")](emptyview-images/filter-multiple-views-large.png#lightbox "Elenco verticale di CollectionView con visualizzazione vuota personalizzata")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Visualizza un tipo personalizzato basato su modelli quando i dati non sono disponibili

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà può essere impostata su un tipo personalizzato, il cui modello viene visualizzato quando [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) la proprietà `null`è o quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. La [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) proprietà può essere impostata su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che definisce l'aspetto dell' `EmptyView`oggetto. Il codice XAML seguente mostra un esempio di questo scenario:

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

Il `FilterData` tipo definisce una `Filter` proprietà e un oggetto corrispondente [`BindableProperty`](xref:Xamarin.Forms.BindableProperty):

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

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà è impostata su un `FilterData` oggetto e i dati `Filter` della proprietà vengono associati alla [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) proprietà. Quando esegue l'oggetto `FilterCommand`, [`CollectionView`](xref:Xamarin.Forms.CollectionView) la raccolta visualizzata da viene `Filter` filtrata in base al termine di ricerca archiviato nella proprietà. [`SearchBar`](xref:Xamarin.Forms.SearchBar) Se l'operazione di filtro non restituisce alcun dato, [`Label`](xref:Xamarin.Forms.Label) viene visualizzato l' [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)oggetto definito in, impostato come [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) valore della proprietà:

[![Screenshot di un elenco di visualizzazione verticale di CollectionView con un modello di visualizzazione vuoto, in iOS e Android](emptyview-images/emptyviewtemplate.png "Elenco verticale di CollectionView con modello di visualizzazione vuoto")](emptyview-images/emptyviewtemplate-large.png#lightbox "Elenco verticale di CollectionView con modello di visualizzazione vuoto")

> [!NOTE]
> Quando si visualizza un tipo personalizzato basato su modelli quando i dati non [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) sono disponibili, la proprietà può essere impostata su una vista che contiene più visualizzazioni figlio.

## <a name="choose-an-emptyview-at-runtime"></a>Scegliere un EmptyView in fase di esecuzione

Le visualizzazioni che verranno visualizzate come [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) dati quando non sono disponibili, possono essere definite come [`ContentView`](xref:Xamarin.Forms.ContentView) oggetti in un oggetto. [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) La `EmptyView` proprietà può quindi essere impostata su un oggetto `ContentView`specifico, in base a una logica di business, in fase di esecuzione. Nell'esempio di codice XAML riportato di seguito viene illustrato un esempio di questo scenario:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
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

Questo codice XAML definisce [`ContentView`](xref:Xamarin.Forms.ContentView) due oggetti a livello [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)di pagina, con l' [`Switch`](xref:Xamarin.Forms.Switch) oggetto che controlla `ContentView` quale oggetto verrà impostato come valore [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) della proprietà. Quando l' [`Switch`](xref:Xamarin.Forms.Switch) oggetto viene attivato o disattivato `OnEmptyViewSwitchToggled` , il gestore dell'evento `ToggleEmptyView` esegue il metodo:

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

Il `ToggleEmptyView` metodo imposta la [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà dell' `collectionView` oggetto su [`ContentView`](xref:Xamarin.Forms.ContentView) [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) uno dei due oggetti archiviati in [,inbasealvaloredellaproprietà.`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) Quando esegue l'oggetto `FilterCommand`, [`CollectionView`](xref:Xamarin.Forms.CollectionView) la raccolta visualizzata da viene [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) filtrata in base al termine di ricerca archiviato nella proprietà. [`SearchBar`](xref:Xamarin.Forms.SearchBar) Se l'operazione `EmptyView` di filtro non restituisce alcun dato, `ContentView` viene visualizzato l'oggetto impostato come proprietà:

[![Screenshot di un elenco di ritrovi verticali con visualizzazioni vuote invertite, in iOS e Android](emptyview-images/swap.png "Elenco verticale di CollectionView con visualizzazioni vuote scambiate")](emptyview-images/swap-large.png#lightbox "Elenco verticale di CollectionView con visualizzazioni vuote scambiate")

Per altre informazioni sui dizionari risorse, vedere [dizionari risorse Novell. Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Scegliere un EmptyViewTemplate in fase di esecuzione

L'aspetto di [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) può essere scelto in fase di esecuzione, in base al relativo valore, impostando la [`CollectionView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) proprietà [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) su un oggetto:

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

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà viene impostata [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) sulla proprietà e la [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) proprietà viene impostata su un `SearchTermDataTemplateSelector` oggetto.

Quando esegue l'oggetto `FilterCommand`, [`CollectionView`](xref:Xamarin.Forms.CollectionView) la raccolta visualizzata da viene [`SearchBar.Text`](xref:Xamarin.Forms.SearchBar.Text) filtrata in base al termine di ricerca archiviato nella proprietà. [`SearchBar`](xref:Xamarin.Forms.SearchBar) Se l'operazione `SearchTermDataTemplateSelector` di filtro non produce dati, il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) scelto dall' [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) oggetto viene impostato come proprietà e visualizzato.

Nell'esempio seguente viene illustrata la `SearchTermDataTemplateSelector` classe:

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

La `SearchTermTemplateSelector` classe definisce `DefaultTemplate` le `OtherTemplate` proprietà [e`DataTemplate`](xref:Xamarin.Forms.DataTemplate) impostate su modelli di dati diversi. L' `OnSelectTemplate` override restituisce `DefaultTemplate`, che visualizza un messaggio all'utente, quando la query di ricerca non è uguale a "Novell". Quando la query di ricerca è uguale a "Novell", `OnSelectTemplate` l'override `OtherTemplate`restituisce, che visualizza un messaggio di base per l'utente:

[![Screenshot della selezione di un modello di visualizzazione vuota del runtime di CollectionView in iOS e Android](emptyview-images/datatemplateselector.png "Selezione del modello di visualizzazione vuota di runtime in un oggetto CollectionView")](emptyview-images/datatemplateselector-large.png#lightbox "Selezione del modello di visualizzazione vuota di runtime in un oggetto CollectionView")

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Create a Novell. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Modelli di dati Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Dizionari risorse Novell. Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Creare un DataTemplateSelector Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
