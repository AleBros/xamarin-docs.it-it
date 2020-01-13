---
title: SearchBar Xamarin.Forms
description: Il Xamarin.Forms SearchBar è un controllo di input utente usato per avviare una ricerca. Il controllo SearchBar supporta il testo segnaposto, l'input della query, l'esecuzione e l'annullamento. Questo articolo illustra come usare un oggetto SearchBar in XAML e nel codice.
ms.prod: xamarin
ms.assetId: F5EFEA72-CB23-4DD6-9545-D9BB755AF3CB
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/04/2019
ms.openlocfilehash: 8888f1615f250a908930cec9058a54bd6e7fedc2
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490077"
---
# <a name="xamarinforms-searchbar"></a>SearchBar Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)

Il [`SearchBar`](xref:Xamarin.Forms.SearchBar) Xamarin.Forms è un controllo di input utente usato per avviare una ricerca. Il controllo `SearchBar` supporta testo segnaposto, input query, esecuzione ricerca e annullamento. Lo screenshot seguente mostra una query di `SearchBar` con i risultati visualizzati in una `ListView`:

[![Screenshot di SearchBar in iOS e Android](searchbar-images/device-searchbars-cropped.png "SearchBar in iOS e Android")](searchbar-images/device-searchbars.png#lightbox "SearchBar in iOS e Android")

La classe `SearchBar` definisce le proprietà seguenti:

* [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) è un `Color` che definisce il colore del pulsante Annulla.
* `CharacterSpacing`, di tipo `double`, è la spaziatura tra i caratteri del testo `SearchBar`.
* [`FontAttributes`](xref:Xamarin.Forms.SearchBar.FontAttributes) è un valore `FontAttributes` enum che determina se il tipo di carattere del `SearchBar` è grassetto, corsivo o nessuno dei due.
* [`FontFamily`](xref:Xamarin.Forms.SearchBar.FontFamily) è un `string` che determina la famiglia di caratteri utilizzata dal `SearchBar`.
* [`FontSize`](xref:Xamarin.Forms.SearchBar.FontSize) può essere un valore `NamedSize` enum o un valore `double` che rappresenta dimensioni specifiche dei tipi di carattere nelle diverse piattaforme.
* [`HorizontalTextAlignment`](xref:Xamarin.Forms.SearchBar.HorizontalTextAlignment) è un valore `TextAlignment` enum che definisce l'allineamento orizzontale del testo della query.
* `VerticalTextAlignment` è un valore `TextAlignment` enum che definisce l'allineamento verticale del testo della query.
* [`Placeholder`](xref:Xamarin.Forms.SearchBar.Placeholder) è un `string` che definisce il testo segnaposto, ad esempio "search...".
* [`PlaceholderColor`](xref:Xamarin.Forms.SearchBar.PlaceholderColor) è un `Color` che definisce il colore del testo segnaposto.
* [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) è un `ICommand` che consente di associare le azioni dell'utente, ad esempio le manopole o i clic, ai comandi definiti in un ViewModel.
* [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) è un `object` che specifica il parametro che deve essere passato al `SearchCommand`.
* [`Text`](xref:Xamarin.Forms.SearchBar.Text) è un `string` contenente il testo della query nella `SearchBar`.
* [`TextColor`](xref:Xamarin.Forms.SearchBar.TextColor) è un `Color` che definisce il colore del testo della query.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che l'`SearchBar` può essere personalizzata ed essere la destinazione delle associazioni dati. La specifica delle proprietà dei tipi di carattere nel `SearchBar` è coerente con la personalizzazione del testo in altri [controlli testo Xamarin.Forms](~/xamarin-forms/user-interface/text/index.md). Per altre informazioni, vedere [tipi di carattere in Xamarin.Forms](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="create-a-searchbar"></a>Creare una SearchBar

È possibile creare un'istanza di un `SearchBar` in XAML. È possibile impostare la proprietà facoltativa `Placeholder` per definire il testo del suggerimento nella casella di input della query. Il valore predefinito per il `Placeholder` è una stringa vuota. se non è impostato, non verrà visualizzato alcun segnaposto. Nell'esempio seguente viene illustrato come creare un'istanza di un `SearchBar` in XAML con il set di proprietà `Placeholder` facoltativo:

```xaml
<SearchBar Placeholder="Search items..." />
```

Nel codice è anche possibile creare un `SearchBar`:

```csharp
SearchBar searchBar = new SearchBar{ Placeholder = "Search items..." };
```

### <a name="searchbar-appearance-properties"></a>Proprietà aspetto di SearchBar

Il controllo `SearchBar` definisce molte proprietà che personalizzano l'aspetto del controllo. Nell'esempio seguente viene illustrato come creare un'istanza di un `SearchBar` in XAML con più proprietà specificate:

```xaml
<SearchBar Placeholder="Search items..."
           CancelButtonColor="Orange"
           PlaceholderColor="Orange"
           TextColor="Orange"
           HorizontalTextAlignment="Center"
           FontSize="Medium"
           FontAttributes="Italic" />
```

È possibile specificare queste proprietà anche quando si crea un oggetto `SearchBar` nel codice:

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

Lo screenshot seguente mostra il controllo `SearchBar` risultante:

[![Screenshot di SearchBar personalizzato in iOS e Android](searchbar-images/device-searchbars-styled-cropped.png "SearchBar personalizzato in iOS e Android")](searchbar-images/device-searchbars-styled.png#lightbox "SearchBar personalizzato in iOS e Android")

> [!NOTE]
> In iOS, la classe `SearchBarRenderer` contiene un metodo di `UpdateCancelButton` sottoponibile a override. Questo metodo controlla quando viene visualizzato il pulsante Annulla ed è possibile eseguirne l'override in un renderer personalizzato. Per ulteriori informazioni sui renderer personalizzati, vedere [Xamarin.Forms Custom renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="perform-a-search-with-event-handlers"></a>Eseguire una ricerca con i gestori eventi

Una ricerca può essere eseguita usando il controllo `SearchBar` connettendo un gestore eventi a uno degli eventi seguenti:

* [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed) viene chiamato quando l'utente fa clic sul pulsante di ricerca o preme il tasto INVIO.
* [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged) viene chiamato ogni volta che il testo nella casella della query viene modificato.

Nell'esempio seguente viene illustrato un gestore eventi associato all'evento `TextChanged` in XAML e viene utilizzato un `ListView` per visualizzare i risultati della ricerca:

```xaml
<SearchBar TextChanged="OnTextChanged" />
<ListView x:Name="searchResults" >
```

Un gestore eventi può essere associato anche a un `SearchBar` creato nel codice:

```csharp
SearchBar searchBar = new SearchBar {/*...*/};
searchBar.TextChanged += OnTextChanged;
```

Il gestore dell'evento `TextChanged` nel file code-behind è lo stesso, indipendentemente dal fatto che il `SearchBar` venga creato tramite XAML o il codice:

```csharp
void OnTextChanged(object sender, EventArgs e)
{
    SearchBar searchBar = (SearchBar)sender;
    searchResults.ItemsSource = DataService.GetSearchResults(searchBar.Text);
}
```

Nell'esempio precedente viene implicata l'esistenza di una classe `DataService` con un `GetSearchResults` metodo in grado di restituire elementi che corrispondono a una query. Il valore della proprietà `Text` del controllo `SearchBar` viene passato al metodo `GetSearchResults` e il risultato viene utilizzato per aggiornare la proprietà `ItemsSource` del controllo `ListView`. L'effetto complessivo è che i risultati della ricerca vengono visualizzati nel controllo `ListView`.

L'applicazione di esempio fornisce un'implementazione della classe `DataService` che può essere usata per testare la funzionalità di ricerca.

## <a name="perform-a-search-using-a-viewmodel"></a>Eseguire una ricerca usando un ViewModel

Una ricerca può essere eseguita senza gestori eventi associando le proprietà `SearchCommand` e `SearchCommandParameter` a `ICommand` implementazioni. Il progetto di esempio illustra queste implementazioni usando il modello Model-View-ViewModel (MVVM). Per altre informazioni sui data binding con MVVM, vedere [data bindings with MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

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
> Il ViewModel presuppone l'esistenza di una classe `DataService` in grado di eseguire ricerche. La classe `DataService`, inclusi i dati di esempio, è disponibile nell'applicazione di esempio.

Il codice XAML seguente mostra come associare un `SearchBar` al ViewModel di esempio, con un controllo `ListView` che Visualizza i risultati della ricerca:

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

Questo esempio imposta il `BindingContext` come un'istanza della classe `SearchViewModel`. Associa la proprietà `SearchCommand` al `PerformSearch` `ICommand` nell'elemento ViewModel e associa la proprietà di `Text` `SearchBar` alla proprietà `SearchCommandParameter`. La proprietà `ListView.ItemsSource` è associata alla proprietà `SearchResults` dell'elemento ViewModel.

Per ulteriori informazioni sull'interfaccia `ICommand` e sulle associazioni, vedere [Xamarin.forms data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md) e [ICommand Interface](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="related-links"></a>Collegamenti correlati

* [Demo di SearchBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-searchbardemos/)
* [Controlli testo Xamarin.Forms](~/xamarin-forms/user-interface/text/index.md)
* [Tipi di carattere in Xamarin.Forms](~/xamarin-forms/user-interface/text/fonts.md)
* [Data binding Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
