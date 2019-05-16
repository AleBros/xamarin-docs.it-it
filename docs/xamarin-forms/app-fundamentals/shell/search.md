---
title: Ricerca nella shell Xamarin.Forms
description: Le applicazioni shell Xamarin.Forms possono usare la funzionalità di ricerca integrata fornita da una casella di ricerca che è possibile aggiungere nella parte superiore di ogni pagina.
ms.prod: xamarin
ms.assetid: F8F9471D-6771-4D23-96C0-2B79473A06D4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: acaa847b61443eff480e2b39e388f5df9de06e42
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65054421"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/Xaminals/)

La shell Xamarin.Forms include una funzionalità di ricerca integrata fornita dalla classe `SearchHandler`. La funzionalità di ricerca può essere aggiunta a una pagina impostando la proprietà associata `Shell.SearchHandler` a un oggetto `SearchHandler` sottoclassato. Come conseguenza, viene aggiunta una casella di ricerca nella parte superiore della pagina:

[![Screenshot di un oggetto SearchHandler della shell, in iOS e Android](search-images/searchhandler.png "Oggetto SearchHandler della shell")](search-images/searchhandler-large.png#lightbox "Oggetto SearchHandler della shell")

Quando viene immessa una query nella casella di ricerca, l'area dei suggerimenti per la ricerca può essere popolata con dati:

[![Screenshot dei risultati della ricerca in un oggetto SearchHandler della shell, in iOS e Android](search-images/search-suggestions.png "Risultati della ricerca in un oggetto SearchHandler della shell")](search-images/search-suggestions-large.png#lightbox "Risultati della ricerca in un oggetto SearchHandler della shell")

Quando viene quindi selezionato un risultato della ricerca, l'applicazione può rispondere in modo appropriato, ad esempio passando a un'altra pagina.

## <a name="searchhandler-class"></a>Classe SearchHandler

La classe `SearchHandler` definisce le proprietà seguenti che ne controllano l'aspetto e il comportamento:

- `ClearIcon`, di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), ovvero l'icona visualizzata per cancellare il contenuto della casella di ricerca.
- `ClearIconHelpText`, di tipo `string`, ovvero il testo della guida accessibile per l'icona di cancellazione.
- `ClearIconName`, di tipo `string`, ovvero il nome dell'icona di cancellazione da usare con le utilità per la lettura dello schermo.
- `ClearPlaceholderCommand`, di tipo `ICommand`. Questo oggetto viene eseguito quando si tocca `ClearPlaceholderIcon`.
- `ClearPlaceholderCommandParameter`, di tipo `object`, ovvero il parametro passato a `ClearPlaceholderCommand`.
- `ClearPlaceholderEnabled`, di tipo `bool`, che determina se è possibile eseguire `ClearPlaceholderCommand`. Il valore predefinito è `true`.
- `ClearPlaceholderHelpText`, di tipo `string`, ovvero il testo della guida accessibile per l'icona di cancellazione del segnaposto.
- `ClearPlaceholderIcon`, di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), ovvero l'icona di cancellazione del segnaposto visualizzata quando la casella di ricerca è vuota.
- `ClearPlaceholderName`, di tipo `string`, ovvero il nome dell'icona di cancellazione del segnaposto da usare con le utilità per la lettura dello schermo.
- `Command`, di tipo `ICommand`. Questo oggetto viene eseguito quando la query di ricerca viene confermata.
- `CommandParameter`, di tipo `object`, ovvero il parametro passato a `Command`.
- `DisplayMemberName`, di tipo `string`, che rappresenta il nome o il percorso della proprietà visualizzata per ogni elemento di dati nella raccolta `ItemsSource`.
- `IsSearchEnabled`, di tipo `bool`, che rappresenta lo stato abilitato della casella di ricerca. Il valore predefinito è `true`.
- `ItemsSource`, di tipo `IEnumerable`, che specifica la raccolta di elementi da visualizzare nell'area dei suggerimenti e ha un valore predefinito `null`.
- `ItemTemplate`, di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), che specifica il modello da applicare a ogni elemento nella raccolta di elementi da visualizzare nell'area dei suggerimenti.
- `Placeholder`, di tipo `string`, ovvero il testo da visualizzare quando la casella di ricerca è vuota.
- `Query`, di tipo `string`, ovvero il testo immesso dall'utente nella casella di ricerca.
- `QueryIcon`, di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), ovvero l'icona usata per indicare all'utente che la ricerca è disponibile.
- `QueryIconHelpText`, di tipo `string`, ovvero il testo della guida accessibile per l'icona di query.
- `QueryIconName`, di tipo `string`, ovvero il nome dell'icona di query da usare con le utilità per la lettura dello schermo.
- `SearchBoxVisibility`, di tipo `SearchBoxVisibility`, che indica la visibilità della casella di ricerca. Per impostazione predefinita, la casella di ricerca è visibile e completamente espansa.
- `SelectedItem`, di tipo `object`, ovvero l'elemento selezionato nei risultati della ricerca. Questa proprietà è di sola lettura e ha un valore predefinito `null`.
- `ShowsResults`, di tipo `bool`, che indica se sono previsti risultati della ricerca nell'area dei suggerimenti, quando viene immesso testo. Il valore predefinito è `false`.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che tali proprietà possono essere destinazioni di data binding.

La classe `SearchHandler` fornisce inoltre i metodi sottoponibili a override seguenti:

- `OnClearPlaceholderClicked`, chiamato ogni volta che si tocca `ClearPlaceholderIcon`.
- `OnItemSelected`, chiamato ogni volta che un risultato della ricerca viene selezionato dall'utente.
- `OnQueryChanged`, chiamato quando la proprietà `Query` cambia.
- `OnQueryConfirmed`, chiamato ogni volta che l'utente preme INVIO o conferma le query nella casella di ricerca.

Quando un utente immette una query nella casella di ricerca, la proprietà `Query` viene aggiornata e a ogni aggiornamento il metodo `OnQueryChanged` viene eseguito. Questo metodo può essere usato per aggiornare l'area dei suggerimenti visualizzata sotto la casella di ricerca. Quando un utente seleziona un risultato nell'area dei suggerimenti, il metodo `OnItemSelected` viene eseguito.

## <a name="create-a-searchhandler"></a>Creare un oggetto SearchHandler

La funzionalità di ricerca può essere aggiunta a un'applicazione shell creando sottoclassi della classe `SearchHandler` ed eseguendo l'override dei metodi `OnQueryChanged` e `OnItemSelected`:

```csharp
public class MonkeySearchHandler : SearchHandler
{
    protected override void OnQueryChanged(string oldValue, string newValue)
    {
        base.OnQueryChanged(oldValue, newValue);

        if (string.IsNullOrWhiteSpace(newValue))
        {
            ItemsSource = null;
        }
        else
        {
            ItemsSource = MonkeyData.Monkeys
                .Where(monkey => monkey.Name.ToLower().Contains(newValue.ToLower()))
                .ToList<Animal>();
        }
    }

    protected override async void OnItemSelected(object item)
    {
        base.OnItemSelected(item);

        // Note: strings will be URL encoded for navigation (e.g. "Blue Monkey" becomes "Blue%20Monkey"). Therefore, decode at the receiver.
        await (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"monkeydetails?name={((Animal)item).Name}");
    }
}
```

L'override di `OnQueryChanged` ha due argomenti: `oldValue`, che contiene la query di ricerca precedente, e `newValue`, che contiene la query di ricerca corrente. L'area dei suggerimenti per la ricerca può essere aggiornata impostando la proprietà `SearchHandler.ItemsSource` su una raccolta `IEnumerable` contenente elementi che corrispondono alla query di ricerca corrente.

Quando un risultato della ricerca viene selezionato dall'utente, viene eseguito l'override di `OnItemSelected` e la proprietà `SelectedItem` viene impostata. In questo esempio, il metodo consente di passare a un'altra pagina che visualizza i dati sull'oggetto `Animal` selezionato. Per altre informazioni sulla navigazione, vedere [Navigazione nella shell Xamarin.Forms](navigation.md).

> [!NOTE]
> È possibile impostare proprietà aggiuntive di `SearchHandler` per controllare l'aspetto della casella di ricerca.

## <a name="consume-a-searchhandler"></a>Utilizzare un oggetto SearchHandler

L'oggetto `SearchHandler` sottoclassato può essere utilizzato impostando la proprietà associata `Shell.SearchHandler` su un oggetto del tipo sottoclassato:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true"
                                      DisplayMemberName="Name" />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name"
});
```

Il metodo `MonkeySearchHandler.OnQueryChanged` restituisce un oggetto `List` contenente oggetti `Animal`. La proprietà `DisplayMemberName` è impostata sulla proprietà `Name` di ogni oggetto `Animal`, quindi i dati visualizzati nell'area dei suggerimenti corrispondono al nome di ogni animale.

La proprietà `ShowsResults` è impostata su `true`, quindi quando l'utente immette una query di ricerca vengono visualizzati i suggerimenti per la ricerca:

[![Screenshot dei risultati della ricerca in un oggetto SearchHandler della shell, in iOS e Android](search-images/search-results.png "Risultati della ricerca in un oggetto SearchHandler della shell")](search-images/search-results-large.png#lightbox "Risultati della ricerca in un oggetto SearchHandler della shell")

Quando la query di ricerca cambia, l'area dei suggerimenti per la ricerca viene aggiornata:

[![Screenshot dei risultati della ricerca in un oggetto SearchHandler della shell, in iOS e Android](search-images/search-results-change.png "Risultati della ricerca in un oggetto SearchHandler della shell")](search-images/search-results-change-large.png#lightbox "Risultati della ricerca in un oggetto SearchHandler della shell")

Quando viene selezionato un risultato della ricerca, si passa a `MonkeyDetailPage` e vengono visualizzati i dati sulla scimmia selezionata:

[![Screenshot dei dettagli relativi alla scimmia, in iOS e Android](search-images/detailpage.png "Dettagli relativi alla scimmia")](search-images/detailpage-large.png#lightbox "Dettagli relativi alla scimmia")

## <a name="define-search-results-item-appearance"></a>Definire l'aspetto degli elementi dei risultati della ricerca

Oltre a visualizzare dati `string` nei risultati della ricerca, è possibile definire l'aspetto di ogni elemento dei risultati della ricerca impostando la proprietà `SearchHandler.ItemTemplate` su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">    
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler Placeholder="Enter search term"
                                      ShowsResults="true">
            <controls:MonkeySearchHandler.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="0.15*" />
                            <ColumnDefinition Width="0.85*" />
                        </Grid.ColumnDefinitions>
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="40"
                               WidthRequest="40" />
                        <Label Grid.Column="1"
                               Text="{Binding Name}"
                               FontAttributes="Bold" />
                    </Grid>
                </DataTemplate>
            </controls:MonkeySearchHandler.ItemTemplate>
       </controls:MonkeySearchHandler>
    </Shell.SearchHandler>
    ...
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
Shell.SetSearchHandler(this, new MonkeySearchHandler
{
    Placeholder = "Enter search term",
    ShowsResults = true,
    DisplayMemberName = "Name",
    ItemTemplate = new DataTemplate(() =>
    {
        Grid grid = new Grid { Padding = 10 };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.15, GridUnitType.Star) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(0.85, GridUnitType.Star) });

        Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 40, WidthRequest = 40 };
        image.SetBinding(Image.SourceProperty, "ImageUrl");
        Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        nameLabel.SetBinding(Label.TextProperty, "Name");

        grid.Children.Add(image);
        grid.Children.Add(nameLabel, 1, 0);
        return grid;
    })
});
```

Gli elementi specificati in [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definiscono l'aspetto di ogni elemento nell'area dei suggerimenti. In questo esempio il layout all'interno di `DataTemplate` è gestito da un oggetto [`Grid`](xref:Xamarin.Forms.Grid). `Grid` contiene un oggetto [`Image`](xref:Xamarin.Forms.Image) e un oggetto [`Label`](xref:Xamarin.Forms.Label), entrambi associati alle proprietà di ogni oggetto `Monkey`.

Gli screenshot seguenti mostrano il risultato dell'applicazione di un modello per ogni elemento nell'area dei suggerimenti:

[![Screenshot dei risultati della ricerca basati su un modello in un oggetto SearchHandler della shell, in iOS e Android](search-images/search-results-template.png "Risultati della ricerca basati su un modello in un oggetto SearchHandler della shell")](search-images/search-results-template-large.png#lightbox "Risultati della ricerca basati su un modello in un oggetto SearchHandler della shell")

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="searchbox-visibility"></a>Visibilità di SearchBox

Quando una casella di ricerca viene aggiunta nella parte superiore di una pagina, per impostazione predefinita è visibile e completamente espansa. Tuttavia, questo comportamento può essere modificato impostando la proprietà `SearchHandler.SearchBoxVisibility` su uno dei membri dell'enumerazione `SearchBoxVisibility`:

- `Hidden`: la casella di ricerca non è visibile o accessibile.
- `Collapsible`: la casella di ricerca è nascosta fino a quando l'utente non esegue un'azione per visualizzarla.
- `Expanded`: la casella di ricerca è visibile e completamente espansa.

L'esempio seguente illustra come nascondere la casella di ricerca:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:MonkeySearchHandler SearchBoxVisibility="Hidden"
                                      ... />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/Xaminals/)
- [Navigazione nella shell Xamarin.Forms](navigation.md)
