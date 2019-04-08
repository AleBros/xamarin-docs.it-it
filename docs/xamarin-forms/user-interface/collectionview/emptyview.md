---
title: Visualizzare un EmptyView quando i dati non sono disponibile
description: Nella visualizzazione di raccolta, è possibile specificare una vista vuota che fornisce feedback all'utente quando non sono disponibili per la visualizzazione dati. La vista vuota può essere una stringa, una vista o più visualizzazioni.
ms.prod: xamarin
ms.assetid: 6CEBCFE6-5577-4F68-9709-431062609153
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/19/2019
ms.openlocfilehash: a430387bba83887045e5687c99d9295d4be373e4
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329976"
---
# <a name="display-an-emptyview-when-data-is-unavailable"></a>Visualizzare un EmptyView quando i dati non sono disponibile

![Anteprima](~/media/shared/preview.png)

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> Il `CollectionView` è attualmente un'anteprima e non dispone di alcune delle proprie funzionalità pianificato. Inoltre, l'API può cambiare quando viene completata l'implementazione.

`CollectionView` definisce le proprietà seguenti che possono essere utilizzate per fornire commenti e suggerimenti utente quando non sono presenti dati da visualizzare:

- `EmptyView`, di tipo `object`, la stringa, associazione o vista che verrà visualizzato quando il `ItemsSource` proprietà è `null`, o quando la raccolta specificata dal `ItemsSource` proprietà è `null` o vuoto. Il valore predefinito è `null`.
- `EmptyViewTemplate`, di tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), il modello da utilizzare per formattare l'oggetto specificato `EmptyView`. Il valore predefinito è `null`.

Queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che le proprietà possono essere destinazioni di associazioni dati.

Gli scenari di utilizzo principali per l'impostazione di `EmptyView` proprietà vengono visualizzati commenti degli utenti quando un'operazione di filtraggio su un `CollectionView` non produce alcuna dati e la visualizzazione dei commenti degli utenti durante il recupero dei dati da un servizio web.

> [!NOTE]
> Il `EmptyView` può essere impostata su una vista che include contenuti interattivi, se necessario.

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="display-a-string-when-data-is-unavailable"></a>Visualizzare una stringa quando i dati non sono disponibili

Il `EmptyView` proprietà può essere impostata su una stringa, in cui è visualizzato quando il `ItemsSource` proprietà è `null`, o quando la raccolta specificata per il `ItemsSource` proprietà è `null` o vuoto. il XAML seguente viene illustrato un esempio di questo scenario:

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}"
                EmptyView="No items to display" />
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    EmptyView = "No items to display"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "EmptyMonkeys");
```

Il risultato è che, perché i dati associati insieme `null`, imposta la stringa come il `EmptyView` viene visualizzato il valore di proprietà:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con una vista vuota di testo, in iOS e Android](emptyview-images/null-itemssource.png "CollectionView elenco verticale con la visualizzazione di testo vuota")](emptyview-images/null-itemssource-large.png#lightbox "CollectionView elenco verticale con testo vuoto visualizzazione")

## <a name="display-views-when-data-is-unavailable"></a>Visualizzare le visualizzazioni quando i dati non sono disponibili

Il `EmptyView` può essere impostata su una vista, che sarà visualizzato quando il `ItemsSource` proprietà è `null`, o quando la raccolta specificata per il `ItemsSource` proprietà è `null` o vuoto. Può trattarsi di una singola visualizzazione o una vista contenente più visualizzazioni figlio. L'esempio XAML seguente viene illustrato il `EmptyView` proprietà è impostata su una visualizzazione contenente più visualizzazioni figlio:

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

Il codice C# equivalente è:

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

Quando il [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) esegue il `FilterCommand`, la raccolta visualizzata per il `CollectionView` viene filtrato per il termine di ricerca archiviate nel [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) proprietà. Se l'operazione di filtro restituisce dati non sono disponibili i [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) imposta come il `EmptyView` viene visualizzato il valore di proprietà:

[![Schermata di elenco verticale CollectionView con vista vuota personalizzata, su iOS e Android](emptyview-images/filter-multiple-views.png "CollectionView elenco verticale con vista vuota personalizzata")](emptyview-images/filter-multiple-views-large.png#lightbox "CollectionView elenco verticale con personalizzato vista vuota")

## <a name="display-a-templated-custom-type-when-data-is-unavailable"></a>Visualizzare un tipo personalizzato basato su modelli quando i dati non sono disponibili

Il `EmptyView` può essere impostata su un tipo personalizzato, il cui modello è visualizzato quando il `ItemsSource` proprietà è `null`, o quando la raccolta specificata per il `ItemsSource` proprietà è `null` o vuoto. Il `EmptyViewTemplate` può essere impostata su una [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) che definisce l'aspetto del `EmptyView`. il XAML seguente viene illustrato un esempio di questo scenario:

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

Il codice C# equivalente è:

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

Il `FilterData` tipo definisce un `Filter` proprietà e un corrispondente [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty):

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

Il `EmptyView` è impostata su una `FilterData` oggetto e il `Filter` esegue l'associazione dati di proprietà per il [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) proprietà. Quando il [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) esegue il `FilterCommand`, la raccolta visualizzata per il `CollectionView` viene filtrato per il termine di ricerca archiviate nel `Filter` proprietà. Se l'operazione di filtro restituisce dati non sono disponibili i [ `Label` ](xref:Xamarin.Forms.Label) definito nel [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), che viene impostato come il `EmptyViewTemplate` valore della proprietà, viene visualizzato:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con un modello di visualizzazione vuota, in iOS e Android](emptyview-images/emptyviewtemplate.png "CollectionView elenco verticale con modello di vista vuota")](emptyview-images/emptyviewtemplate-large.png#lightbox "CollectionView elenco verticale con modello di vista vuota")

> [!NOTE]
> La visualizzazione di un tipo personalizzato basato su modelli quando i dati non sono disponibili, il `EmptyViewTemplate` può essere impostata su una visualizzazione contenente più visualizzazioni figlio.

## <a name="choose-an-emptyview-at-runtime"></a>Scegliere un EmptyView in fase di esecuzione

Le viste che verranno visualizzate come un `EmptyView` quando i dati non sono disponibili, può essere definita come [ `ContentView` ](xref:Xamarin.Forms.ContentView) gli oggetti in un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Il `EmptyView` può quindi essere impostata su uno specifico `ContentView`, in base a una logica di business, in fase di esecuzione. Nell'esempio XAML seguente mostra un esempio di questo scenario:

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

Questo XAML definisce due [ `ContentView` ](xref:Xamarin.Forms.ContentView) oggetti nel livello di pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), con la [ `Switch` ](xref:Xamarin.Forms.Switch) oggetto controllo cui `ContentView` oggetto verrà impostato come il `EmptyView` valore della proprietà. Quando il [ `Switch` ](xref:Xamarin.Forms.Switch) viene attivato/disattivato, il `OnEmptyViewSwitchToggled` gestore eventi viene eseguito il `ToggleEmptyView` metodo:

```csharp
void ToggleEmptyView(bool isToggled)
{
    collectionView.EmptyView = isToggled ? Resources["BasicEmptyView"] : Resources["AdvancedEmptyView"];
}
```

Il `ToggleEmptyView` metodo imposta la `EmptyView` proprietà del `collectionView` oggetto in uno dei due [ `ContentView` ](xref:Xamarin.Forms.ContentView) gli oggetti archiviati nel [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), basato sul valore della [ `Switch.IsToggled` ](xref:Xamarin.Forms.Switch.IsToggled) proprietà. Quando il [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) esegue il `FilterCommand`, la raccolta visualizzata per il `CollectionView` viene filtrato per il termine di ricerca archiviate nel [ `SearchBar.Text` ](xref:Xamarin.Forms.SearchBar.Text) proprietà. Se l'operazione di filtro restituisce dati non sono disponibili i `ContentView` oggetto impostato come il `EmptyView` proprietà viene visualizzata:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con visualizzazioni vuote invertite, in iOS e Android](emptyview-images/swap.png "CollectionView elenco verticale con le visualizzazioni vuote invertite")](emptyview-images/swap-large.png#lightbox "CollectionView elenco verticale con scambiare le visualizzazioni vuote")

Per altre informazioni sui dizionari risorse, vedere [dizionari risorse xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di raccolta (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Modelli di dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Dizionari risorse xamarin. Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
