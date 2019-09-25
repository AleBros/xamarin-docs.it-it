---
title: SearchBar Novell. Forms
description: Il Novell. Forms SearchBar è un controllo di input utente usato per avviare una ricerca. Il controllo SearchBar supporta il testo segnaposto, l'input della query, l'esecuzione e l'annullamento. Questo articolo illustra come usare un oggetto SearchBar in XAML e nel codice.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/12/2019
ms.openlocfilehash: 41bb9e082f042e7ca2933d72b4b71a4ff6c4fef4
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "69658030"
---
# <a name="xamarinforms-searchbar"></a>SearchBar Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Novell. Forms [`SearchBar`](xref:Xamarin.Forms.SearchBar) è un controllo di input utente usato per avviare una ricerca. Il `SearchBar` controllo supporta testo segnaposto, input query, esecuzione ricerca e annullamento. Lo screenshot seguente mostra una `SearchBar` query con i risultati visualizzati in `ListView`un:

[![Screenshot di Searchbar in iOS e Android](searchbar-images/device-searchbars-cropped.png "Searchbar in iOS e Android")](searchbar-images/device-searchbars.png#lightbox "SearchBar in iOS e Android")

La `SearchBar` classe definisce le proprietà seguenti:

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)è un `Color` oggetto che definisce il colore del pulsante Annulla.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes)è un `FontAttributes` valore di enumerazione che determina se `SearchBar` il tipo di carattere è grassetto, corsivo o nessuno dei due.
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily)è un `string` oggetto che determina la famiglia `SearchBar`di caratteri utilizzata da.
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize)può essere un `NamedSize` valore enum o un `double` valore che rappresenta dimensioni specifiche dei tipi di carattere nelle diverse piattaforme.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment)è un `TextAlignment` valore enum che definisce l'allineamento orizzontale del testo della query.
* [`Placeholder`](xref:Xamarin.Forms.SearchBar.Placeholder)è un `string` oggetto che definisce il testo segnaposto, ad esempio "search...".
* [`PlaceholderColor`](xref:Xamarin.Forms.SearchBar.PlaceholderColor)è un `Color` oggetto che definisce il colore del testo segnaposto.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)è un `ICommand` oggetto che consente di associare le azioni dell'utente, ad esempio le manopole o i clic, ai comandi definiti in un elemento ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)è un `object` oggetto che specifica il parametro che deve essere passato `SearchCommand`a.
* [`Text`](xref:Xamarin.Forms.SearchBar.Text)è un `string` oggetto che contiene il testo della `SearchBar`query in.
* [`TextColor`](xref:Xamarin.Forms.SearchBar.TextColor)è un `Color` oggetto che definisce il colore del testo della query.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il `SearchBar` che significa che può essere personalizzato ed essere la destinazione delle associazioni dati. La specifica delle proprietà dei `SearchBar` tipi di carattere in è coerente con la personalizzazione del testo in altri [controlli testo Novell. Forms](~/xamarin-forms/user-interface/text/index.md). Per altre informazioni, vedere [tipi di carattere in Novell. Forms](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Creare una SearchBar

È `SearchBar` possibile creare un'istanza di un oggetto in XAML. La proprietà `Placeholder` facoltativa può essere impostata in modo da definire il testo del suggerimento nella casella di input della query. Il valore predefinito per `Placeholder` è una stringa vuota. se non è impostato, non verrà visualizzato alcun segnaposto. Nell'esempio seguente viene illustrato come creare un'istanza `SearchBar` di in XAML con il `Placeholder` set di proprietà facoltativo:

```xaml
<SearchBar Placeholder="Search items..." />
```

Un `SearchBar` oggetto può essere creato anche nel codice:

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Proprietà aspetto di SearchBar

Il `SearchBar` controllo definisce molte proprietà che personalizzano l'aspetto del controllo. Nell'esempio seguente viene illustrato come creare un'istanza `SearchBar` di in XAML con più proprietà specificate:

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

È possibile specificare queste proprietà anche quando si crea `SearchBar` un oggetto nel codice:

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

La schermata seguente mostra il controllo `SearchBar` risultante:

[![Screenshot di Searchbar personalizzato in iOS e Android](searchbar-images/device-searchbars-styled-cropped.png "Searchbar personalizzato in iOS e Android")](searchbar-images/device-searchbars-styled.png#lightbox "SearchBar personalizzato in iOS e Android")

## <a name="perform-a-search-with-event-handlers"></a>Eseguire una ricerca con i gestori eventi

Una ricerca può essere eseguita usando il `SearchBar` controllo connettendo un gestore eventi a uno degli eventi seguenti:

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)viene chiamato quando l'utente fa clic sul pulsante di ricerca o preme il tasto INVIO.
* [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged)viene chiamato ogni volta che il testo nella casella della query viene modificato.

Nell'esempio seguente viene illustrato un gestore eventi associato all' `TextChanged` evento in XAML e viene utilizzato `ListView` un oggetto per visualizzare i risultati della ricerca:

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

Un gestore eventi può essere collegato anche a un `SearchBar` oggetto creato nel codice:

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

Il `TextChanged` gestore eventi nel file code-behind è lo stesso, `SearchBar` indipendentemente dal fatto che venga creato tramite XAML o codice:

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

Nell'esempio precedente viene implicata l'esistenza `DataService` di una classe `GetSearchResults` con un metodo in grado di restituire elementi che corrispondono a una query. Il `SearchBar` `Text` `ListView` valoredella`ItemsSource` proprietà del controllo viene passato al metodoeilrisultatovieneusatoperaggiornarelaproprietàdelcontrollo.`GetSearchResults` L'effetto complessivo è che i risultati della ricerca vengono visualizzati `ListView` nel controllo.

L'applicazione di esempio fornisce `DataService` un'implementazione della classe che può essere utilizzata per testare la funzionalità di ricerca.

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

Il codice XAML seguente mostra come associare un `SearchBar` oggetto al ViewModel di esempio, con `ListView` un controllo che Visualizza i risultati della ricerca:

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
                  ItemsSource="{Binding SearchResults} />
    </StackLayout>
</ContentPage>
```

In questo esempio l' `BindingContext` oggetto viene impostato su un'istanza `SearchViewModel` della classe. Associa `SearchCommand` la `SearchCommandParameter` proprietà `PerformSearch` `SearchBar` a nell'elemento ViewModel e associa la `Text` proprietà alla proprietà. `ICommand` La `ListView.ItemsSource` proprietà è associata `SearchResults` alla proprietà dell'elemento ViewModel.

Per ulteriori informazioni sull' `ICommand` interfaccia e sulle associazioni, vedere [Novell. Forms Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md) e [l'interfaccia ICommand](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Collegamenti correlati

* [Demo di SearchBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Controlli testo Novell. Forms](~/xamarin-forms/user-interface/text/index.md)
* [Tipi di carattere in Novell. Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Data binding Novell. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
