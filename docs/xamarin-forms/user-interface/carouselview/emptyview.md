---
title: Xamarin.FormsCarouselView EmptyView
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a9f952da75e68e9ad39e0a15f57fbd0379233d7e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137397"
---
# <a name="xamarinforms-carouselview-emptyview"></a>Xamarin.FormsCarouselView EmptyView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce le proprietà seguenti che possono essere usate per fornire commenti e suggerimenti degli utenti quando non sono presenti dati da visualizzare:

- [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView), di tipo `object` , la stringa, l'associazione o la vista che verrà visualizzata quando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) proprietà è o `null` quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Il valore predefinito è `null`.
- [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , il modello da utilizzare per formattare l'oggetto specificato `EmptyView` . Il valore predefinito è `null`.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

Gli scenari di utilizzo principali per l'impostazione della [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà sono la visualizzazione di commenti e suggerimenti degli utenti quando un'operazione di filtro in un oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) non produce dati e visualizza il feedback dell'utente mentre i dati vengono recuperati da un servizio Web.

> [!NOTE]
> [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView)Se necessario, la proprietà può essere impostata su una vista che include contenuto interattivo.

Per ulteriori informazioni sui modelli di dati, vedere [ Xamarin.Forms modelli di dati](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Visualizza una stringa quando i dati non sono disponibili

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà può essere impostata su una stringa che verrà visualizzata quando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) proprietà è o `null` quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Il codice XAML seguente mostra un esempio di questo scenario:

```xaml
<CarouselView ItemsSource="{Binding EmptyMonkeys}"
              EmptyView="No items to display." />
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView
{
    EmptyView = "No items to display."
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

Il risultato è che, poiché la raccolta associata ai dati è `null` , viene visualizzata la stringa impostata come [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) valore della proprietà.

## <a name="display-views-when-data-is-unavailable"></a>Visualizzazione delle visualizzazioni quando i dati non sono disponibili

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà può essere impostata su una visualizzazione, che verrà visualizzata quando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) proprietà è o `null` quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. Può trattarsi di una vista singola o di una vista che contiene più visualizzazioni figlio. Nell'esempio di codice XAML riportato di seguito viene illustrata la `EmptyView` proprietà impostata su una vista che contiene più visualizzazioni figlio:

```xaml
<StackLayout Margin="20">
    <SearchBar SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
               Placeholder="Filter" />
    <CarouselView ItemsSource="{Binding Monkeys}">
        <CarouselView.EmptyView>
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
        </CarouselView.EmptyView>
        <CarouselView.ItemTemplate>
            ...
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

Il codice C# equivalente è il seguente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView
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
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Quando [`SearchBar`](xref:Xamarin.Forms.SearchBar) esegue l'oggetto `FilterCommand` , la raccolta visualizzata da [`CarouselView`](xref:Xamarin.Forms.CarouselView) viene filtrata in base al termine di ricerca archiviato nella [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) Proprietà. Se l'operazione di filtro non produce dati, [`StackLayout`](xref:Xamarin.Forms.StackLayout) viene visualizzato il set come [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) valore della proprietà.

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Visualizza un tipo personalizzato basato su modelli quando i dati non sono disponibili

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà può essere impostata su un tipo personalizzato, il cui modello viene visualizzato quando la [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) proprietà è o `null` quando la raccolta specificata dalla `ItemsSource` proprietà è `null` o vuota. La [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) proprietà può essere impostata su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che definisce l'aspetto dell'oggetto `EmptyView` . Il codice XAML seguente mostra un esempio di questo scenario:

```xaml
<StackLayout Margin="20">
    <SearchBar x:Name="searchBar"
               SearchCommand="{Binding FilterCommand}"
               SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
               Placeholder="Filter" />
    <CarouselView ItemsSource="{Binding Monkeys}">
        <CarouselView.EmptyView>
            <controls:FilterData Filter="{Binding Source={x:Reference searchBar}, Path=Text}" />
        </CarouselView.EmptyView>
        <CarouselView.EmptyViewTemplate>
            <DataTemplate>
                <Label Text="{Binding Filter, StringFormat='Your filter term of {0} did not match any records.'}"
                       Margin="10,25,10,10"
                       FontAttributes="Bold"
                       FontSize="18"
                       HorizontalOptions="Fill"
                       HorizontalTextAlignment="Center" />
            </DataTemplate>
        </CarouselView.EmptyViewTemplate>
        <CarouselView.ItemTemplate>
            ...
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

Il codice C# equivalente è il seguente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView
{
    EmptyView = new FilterData { Filter = searchBar.Text },
    EmptyViewTemplate = new DataTemplate(() =>
    {
        return new Label { ... };
    })
};
```

Il `FilterData` tipo definisce una `Filter` proprietà e un oggetto corrispondente [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) :

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

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà è impostata su un `FilterData` oggetto e i `Filter` dati della proprietà vengono associati alla [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) Proprietà. Quando [`SearchBar`](xref:Xamarin.Forms.SearchBar) esegue l'oggetto `FilterCommand` , la raccolta visualizzata da [`CarouselView`](xref:Xamarin.Forms.CarouselView) viene filtrata in base al termine di ricerca archiviato nella `Filter` Proprietà. Se l'operazione di filtro non produce dati, [`Label`](xref:Xamarin.Forms.Label) viene visualizzato il definito in [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , impostato come valore della [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) Proprietà.

> [!NOTE]
> Quando si visualizza un tipo personalizzato basato su modelli quando i dati non sono disponibili, la [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) proprietà può essere impostata su una vista che contiene più visualizzazioni figlio.

## <a name="choose-an-emptyview-at-runtime"></a>Scegliere un EmptyView in fase di esecuzione

Le visualizzazioni che verranno visualizzate come [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) dati quando non sono disponibili, possono essere definite come [`ContentView`](xref:Xamarin.Forms.ContentView) oggetti in un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . La `EmptyView` proprietà può quindi essere impostata su un oggetto specifico `ContentView` , in base a una logica di business, in fase di esecuzione. Nell'esempio di codice XAML riportato di seguito viene illustrato un esempio di questo scenario:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:viewmodels="clr-namespace:CarouselViewDemos.ViewModels"
             x:Class="CarouselViewDemos.Views.EmptyViewSwapPage"
             Title="EmptyView (swap)">
    <ContentPage.BindingContext>
        <viewmodels:MonkeysViewModel />
    </ContentPage.BindingContext>
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
        <SearchBar SearchCommand="{Binding FilterCommand}"
                   SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
                   Placeholder="Filter" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Toggle EmptyViews" />
            <Switch Toggled="OnEmptyViewSwitchToggled" />
        </StackLayout>
        <CarouselView x:Name="carouselView"
                      ItemsSource="{Binding Monkeys}">
            <CarouselView.ItemTemplate>
                ...
            </CarouselView.ItemTemplate>
        </CarouselView>
    </StackLayout>
</ContentPage>
```

Questo codice XAML definisce due [`ContentView`](xref:Xamarin.Forms.ContentView) oggetti a livello di pagina [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , con l' [`Switch`](xref:Xamarin.Forms.Switch) oggetto che controlla quale `ContentView` oggetto verrà impostato come [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) valore della proprietà. Quando l'oggetto [`Switch`](xref:Xamarin.Forms.Switch) viene attivato o disattivato, il `OnEmptyViewSwitchToggled` gestore dell'evento esegue il `ToggleEmptyView` Metodo:

```csharp
void ToggleEmptyView(bool isToggled)
{
    carouselView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

Il `ToggleEmptyView` metodo imposta la [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà dell' `carouselView` oggetto su uno dei due [`ContentView`](xref:Xamarin.Forms.ContentView) oggetti archiviati in [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , in base al valore della [`Switch.IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) Proprietà. Quando [`SearchBar`](xref:Xamarin.Forms.SearchBar) esegue l'oggetto `FilterCommand` , la raccolta visualizzata da [`CarouselView`](xref:Xamarin.Forms.CarouselView) viene filtrata in base al termine di ricerca archiviato nella [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) Proprietà. Se l'operazione di filtro non restituisce alcun dato, `ContentView` viene visualizzato l'oggetto impostato come `EmptyView` Proprietà.

Per altre informazioni sui dizionari risorse, vedere [ Xamarin.Forms dizionari risorse](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="choose-an-emptyviewtemplate-at-runtime"></a>Scegliere un EmptyViewTemplate in fase di esecuzione

L'aspetto di [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) può essere scelto in fase di esecuzione, in base al relativo valore, impostando la [`CarouselView.EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) proprietà su un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) oggetto:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CarouselViewDemos.Controls">
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
                   SearchCommandParameter="{Binding Source={RelativeSource Self}, Path=Text}"
                   Placeholder="Filter" />
        <CarouselView ItemsSource="{Binding Monkeys}"
                      EmptyView="{Binding Source={x:Reference searchBar}, Path=Text}"
                      EmptyViewTemplate="{StaticResource SearchSelector}">
            <CarouselView.ItemTemplate>
                ...
            </CarouselView.ItemTemplate>
        </CarouselView>
    </StackLayout>
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
SearchBar searchBar = new SearchBar { ... };
CarouselView carouselView = new CarouselView()
{
    EmptyView = searchBar.Text,
    EmptyViewTemplate = new SearchTermDataTemplateSelector { ... }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

La [`EmptyView`](xref:Xamarin.Forms.ItemsView.EmptyView) proprietà viene impostata sulla [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) proprietà e la [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) proprietà viene impostata su un `SearchTermDataTemplateSelector` oggetto.

Quando [`SearchBar`](xref:Xamarin.Forms.SearchBar) esegue l'oggetto `FilterCommand` , la raccolta visualizzata da [`CarouselView`](xref:Xamarin.Forms.CarouselView) viene filtrata in base al termine di ricerca archiviato nella [`SearchBar.Text`](xref:Xamarin.Forms.InputView.Text) Proprietà. Se l'operazione di filtro non produce dati, il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) scelto dall' `SearchTermDataTemplateSelector` oggetto viene impostato come [`EmptyViewTemplate`](xref:Xamarin.Forms.ItemsView.EmptyViewTemplate) proprietà e visualizzato.

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

La `SearchTermTemplateSelector` classe definisce `DefaultTemplate` le `OtherTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà e impostate su modelli di dati diversi. L' `OnSelectTemplate` override restituisce `DefaultTemplate` , che visualizza un messaggio all'utente, quando la query di ricerca non è uguale a "Novell". Quando la query di ricerca è uguale a "Novell", l' `OnSelectTemplate` override restituisce `OtherTemplate` , che visualizza un messaggio di base per l'utente.

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Create a Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Collegamenti correlati

- [CarouselView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.FormsModelli di dati](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Xamarin.FormsDizionari risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Creare un Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
