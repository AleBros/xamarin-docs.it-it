---
title: SearchBar di Xamarin.Forms
description: Il Novell. Forms SearchBar è un controllo di input utente usato per avviare una ricerca. Il controllo SearchBar supporta il testo segnaposto, l'input della query, l'esecuzione e l'annullamento. Questo articolo illustra come usare un oggetto SearchBar in XAML e nel codice.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/04/2019
ms.openlocfilehash: 9162e89768aefe761111a02b80932231a6fe759f
ms.sourcegitcommit: da15fb3b593a3e01ced9f8a1df572348d01d42ea
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844192"
---
# <a name="xamarinforms-searchbar"></a>SearchBar di Xamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Novell. Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) è un controllo di input utente usato per avviare una ricerca. Il `SearchBar` controllo supporta testo segnaposto, input query, esecuzione ricerca e annullamento. Lo screenshot seguente mostra una `SearchBar` query con i risultati visualizzati in un `ListView` :

[![Screenshot di SearchBar in iOS e Android](searchbar-images/device-searchbars-cropped.png "SearchBar in iOS e Android")](searchbar-images/device-searchbars.png#lightbox "SearchBar in iOS e Android")

La `SearchBar` classe definisce le proprietà seguenti:

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)è un oggetto `Color` che definisce il colore del pulsante Annulla.
* `CharacterSpacing`, di tipo `double` , indica la spaziatura tra i caratteri del `SearchBar` testo.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)è un `FontAttributes` valore di enumerazione che determina se il `SearchBar` tipo di carattere è grassetto, corsivo o nessuno dei due.
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)è un oggetto `string` che determina la famiglia di caratteri utilizzata da `SearchBar` .
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)può essere un `NamedSize` valore enum o un `double` valore che rappresenta dimensioni specifiche dei tipi di carattere nelle diverse piattaforme.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)è un `TextAlignment` valore enum che definisce l'allineamento orizzontale del testo della query.
* `VerticalTextAlignment`è un `TextAlignment` valore enum che definisce l'allineamento verticale del testo della query.
* [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)è un oggetto `string` che definisce il testo segnaposto, ad esempio "search...".
* [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)è un oggetto `Color` che definisce il colore del testo segnaposto.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)è un oggetto `ICommand` che consente di associare le azioni dell'utente, ad esempio le manopole o i clic, ai comandi definiti in un elemento ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)è un oggetto `object` che specifica il parametro che deve essere passato a `SearchCommand` .
* [`Text`](xref:Xamarin.Forms.InputView.Text)è un oggetto `string` che contiene il testo della query in `SearchBar` .
* [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)è un oggetto `Color` che definisce il colore del testo della query.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che `SearchBar` può essere personalizzato ed essere la destinazione delle associazioni dati. La specifica delle proprietà dei tipi di carattere in `SearchBar` è coerente con la personalizzazione del testo in altri [controlli testo Novell. Forms](~/xamarin-forms/user-interface/text/index.md). Per altre informazioni, vedere [tipi di carattere in Novell. Forms](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Creare una SearchBar

`SearchBar`È possibile creare un'istanza di un oggetto in XAML. La proprietà facoltativa `Placeholder` può essere impostata in modo da definire il testo del suggerimento nella casella di input della query. Il valore predefinito per `Placeholder` è una stringa vuota. se non è impostato, non verrà visualizzato alcun segnaposto. Nell'esempio seguente viene illustrato come creare un'istanza di `SearchBar` in XAML con il `Placeholder` set di proprietà facoltativo:

```xaml
<SearchBar Placeholder="Search items..." />
```

Un oggetto `SearchBar` può essere creato anche nel codice:

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Proprietà aspetto di SearchBar

Il `SearchBar` controllo definisce molte proprietà che personalizzano l'aspetto del controllo. Nell'esempio seguente viene illustrato come creare un'istanza di `SearchBar` in XAML con più proprietà specificate:

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

È possibile specificare queste proprietà anche quando si crea un `SearchBar` oggetto nel codice:

```csharp
SearchBar searchBar = new SearchBar
{
    Placeholder = "Search items...",
    PlaceholderColor = Color.Orange,
    TextColor = Color.Orange,
    HorizontalTextAlignment = TextAlignment.Center,
    FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(SearchBar)),
    FontAttributes = FontAttributes.Italic
};
```

La schermata seguente mostra il `SearchBar` controllo risultante:

[![Screenshot di SearchBar personalizzato in iOS e Android](searchbar-images/device-searchbars-styled-cropped.png "SearchBar personalizzato in iOS e Android")](searchbar-images/device-searchbars-styled.png#lightbox "SearchBar personalizzato in iOS e Android")

> [!NOTE]
> In iOS la `SearchBarRenderer` classe contiene un metodo sottoponibile a override `UpdateCancelButton` . Questo metodo controlla quando viene visualizzato il pulsante Annulla ed è possibile eseguirne l'override in un renderer personalizzato. Per ulteriori informazioni sui renderer personalizzati, vedere [Novell. Forms Custom renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="perform-a-search-with-event-handlers"></a>Eseguire una ricerca con i gestori eventi

Una ricerca può essere eseguita usando il `SearchBar` controllo connettendo un gestore eventi a uno degli eventi seguenti:

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)viene chiamato quando l'utente fa clic sul pulsante di ricerca o preme il tasto INVIO.
* [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)viene chiamato ogni volta che il testo nella casella della query viene modificato.

Nell'esempio seguente viene illustrato un gestore eventi associato all' `TextChanged` evento in XAML e viene utilizzato un oggetto `ListView` per visualizzare i risultati della ricerca:

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

Un gestore eventi può essere collegato anche a un oggetto `SearchBar` creato nel codice:

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

Il `TextChanged` gestore eventi nel file code-behind è lo stesso, indipendentemente dal fatto che `SearchBar` venga creato tramite XAML o codice:

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

Nell'esempio precedente viene implicata l'esistenza di una `DataService` classe con un `GetSearchResults` metodo in grado di restituire elementi che corrispondono a una query. Il `SearchBar` valore della `Text` proprietà del controllo viene passato al `GetSearchResults` metodo e il risultato viene usato per aggiornare la `ListView` proprietà del controllo `ItemsSource` . L'effetto complessivo è che i risultati della ricerca vengono visualizzati nel `ListView` controllo.

L'applicazione di esempio fornisce un' `DataService` implementazione della classe che può essere utilizzata per testare la funzionalità di ricerca.

## <a name="perform-a-search-using-a-viewmodel"></a>Eseguire una ricerca usando un ViewModel

Una ricerca può essere eseguita senza gestori eventi associando le `SearchCommand` proprietà e `SearchCommandParameter` alle `ICommand` implementazioni. Il progetto di esempio illustra queste implementazioni usando il modello Model-View-ViewModel (MVVM). Per altre informazioni sui data binding con MVVM, vedere [data bindings with MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

Il ViewModel nell'applicazione di esempio contiene il codice seguente:

```csharp
public class SearchViewModel : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    protected virtual void NotifyPropertyChanged([CallerMemberName] string propertyName = "")
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }

    public ICommand PerformSearch => new Command<string>((string query) =>
    {
        SearchResults = DataService.GetSearchResults(query);
    });

    private List<string> searchResults = DataService.Fruits;
    public List<string> SearchResults
    {
        get
        {
            return searchResults;
        }
        set
        {
            searchResults = value;
            NotifyPropertyChanged();
        }
    }
}
```

> [!NOTE]
> Il ViewModel presuppone l'esistenza di una `DataService` classe in grado di eseguire ricerche. La `DataService` classe, inclusi i dati di esempio, è disponibile nell'applicazione di esempio.

Il codice XAML seguente mostra come associare un oggetto `SearchBar` al ViewModel di esempio, con un `ListView` controllo che Visualizza i risultati della ricerca:

```xaml
<ContentPage ...>
    <ContentPage.BindingContext>
        <viewmodels:SearchViewModel />
    </ContentPage.BindingContext>
    <StackLayout ...>
        <SearchBar x:Name="searchBar"
                   ...
                   SearchCommand="{Binding PerformSearch}"
                   SearchCommandParameter="{Binding Text, Source={x:Reference searchBar}}"/>
        <ListView x:Name="searchResults"
                  ...
                  ItemsSource="{Binding SearchResults}" />
    </StackLayout>
</ContentPage>
```

In questo esempio l'oggetto viene impostato `BindingContext` su un'istanza della `SearchViewModel` classe. Associa la `SearchCommand` proprietà a `PerformSearch` `ICommand` nell'elemento ViewModel e associa la `SearchBar` `Text` proprietà alla `SearchCommandParameter` Proprietà. La `ListView.ItemsSource` proprietà è associata alla `SearchResults` proprietà dell'elemento ViewModel.

Per ulteriori informazioni sull' `ICommand` interfaccia e sulle associazioni, vedere [Novell. forms data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md) e [l'interfaccia ICommand](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Collegamenti correlati

* [Demo di SearchBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Controlli testo Novell. Forms](~/xamarin-forms/user-interface/text/index.md)
* [Tipi di carattere in Novell. Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Data binding Novell. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
