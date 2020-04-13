---
title: Ricerca nella shell Xamarin.Forms
description: Le applicazioni shell Xamarin.Forms possono usare la funzionalità di ricerca integrata fornita da una casella di ricerca che è possibile aggiungere nella parte superiore di ogni pagina.
ms.prod: xamarin
ms.assetid: F8F9471D-6771-4D23-96C0-2B79473A06D4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2019
ms.openlocfilehash: 9bd4fe5f1a35e2a6f36540cbee13838841b36d92
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75490064"
---
# <a name="xamarinforms-shell-search"></a>Ricerca nella shell Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La shell Xamarin.Forms include una funzionalità di ricerca integrata fornita dalla classe `SearchHandler`. La funzionalità di ricerca può essere aggiunta a una pagina impostando la proprietà associata `Shell.SearchHandler` a un oggetto `SearchHandler` sottoclassato. Come conseguenza, viene aggiunta una casella di ricerca nella parte superiore della pagina:

[![Screenshot di un SearchHandler della shell, su iOS e Android](search-images/searchhandler.png "Ricerca della shell")](search-images/searchhandler-large.png#lightbox "Ricerca della shell")

Quando viene immessa una query nella casella di ricerca, la proprietà `Query` viene aggiornata e a ogni aggiornamento viene eseguito il metodo `OnQueryChanged`. È possibile eseguire l'override di questo metodo per popolare l'area dei suggerimenti di ricerca con dati:

[![Screenshot dei risultati di una ricerca in un SearchHandler della shell, su iOS e Android](search-images/search-suggestions.png "Risultati della ricerca SearchHandler della shell")](search-images/search-suggestions-large.png#lightbox "Risultati della ricerca SearchHandler della shell")

Quando viene selezionato un risultato nell'area dei suggerimenti di ricerca, viene quindi eseguito il metodo `OnItemSelected`. È possibile eseguire l'override di questo metodo per rispondere in modo appropriato, ad esempio passando a una pagina di dettagli.

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

[![Screenshot dei risultati della ricerca in un SearchHandler della shell, su iOS e Android](search-images/search-results.png "Risultati della ricerca SearchHandler della shell")](search-images/search-results-large.png#lightbox "Risultati della ricerca SearchHandler della shell")

Quando la query di ricerca cambia, l'area dei suggerimenti per la ricerca viene aggiornata:

[![Screenshot dei risultati della ricerca in un SearchHandler della shell, su iOS e Android](search-images/search-results-change.png "Risultati della ricerca SearchHandler della shell")](search-images/search-results-change-large.png#lightbox "Risultati della ricerca SearchHandler della shell")

Quando viene selezionato un risultato della ricerca, si passa a `MonkeyDetailPage` e vengono visualizzati i dati sulla scimmia selezionata:

[![Screenshot dei dettagli della scimmia, su iOS e Android](search-images/detailpage.png "Dettagli scimmia")](search-images/detailpage-large.png#lightbox "Dettagli scimmia")

## <a name="define-search-results-item-appearance"></a>Definire l'aspetto degli elementi dei risultati della ricerca

Oltre a visualizzare `string` i dati nei risultati della ricerca, è possibile definire `SearchHandler.ItemTemplate` l'aspetto di ogni elemento dei risultati della ricerca impostando la proprietà su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

Gli elementi specificati [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) nella definizione dell'aspetto di ogni elemento nell'area dei suggerimenti. In questo esempio, `DataTemplate` il layout [`Grid`](xref:Xamarin.Forms.Grid)all'interno di è gestito da un oggetto . `Grid` L'oggetto [`Image`](xref:Xamarin.Forms.Image) contiene un [`Label`](xref:Xamarin.Forms.Label) oggetto e un oggetto `Monkey` che vengono entrambi associati alle proprietà di ogni oggetto.

Gli screenshot seguenti mostrano il risultato dell'applicazione di un modello per ogni elemento nell'area dei suggerimenti:

[![Screenshot dei risultati di ricerca basati su modelli in un SearchHandler della shell, su iOS e Android](search-images/search-results-template.png "Risultati della ricerca su modelli di Ricerca shell")](search-images/search-results-template-large.png#lightbox "Risultati della ricerca su modelli di Ricerca shell")

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="search-box-visibility"></a>Visibilità della casella di ricerca

Quando viene aggiunto un controllo `SearchHandler` nella parte superiore di una pagina, per impostazione predefinita la casella di ricerca è visibile e completamente espansa. Tuttavia, questo comportamento può essere modificato impostando la proprietà `SearchHandler.SearchBoxVisibility` su uno dei membri dell'enumerazione `SearchBoxVisibility`:

- `Hidden`: la casella di ricerca non è visibile o accessibile.
- `Collapsible`: la casella di ricerca è nascosta fino a quando l'utente non esegue un'azione per visualizzarla. Su iOS la casella di ricerca viene rivelata facendo rimbalzare verticalmente il contenuto della pagina e su Android la casella di ricerca viene rivelata toccando l'icona del punto interrogativo.
- `Expanded`: la casella di ricerca è visibile e completamente espansa. Questo è il valore predefinito per la proprietà `SearchHandler.SearchBoxVisibility`.

> [!IMPORTANT]
> In iOS, una casella di ricerca comprimibile richiede iOS 11 o versione successiva.

L'esempio seguente illustra come nascondere la casella di ricerca:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:Xaminals.Controls">
    <Shell.SearchHandler>
        <controls:AnimalSearchHandler SearchBoxVisibility="Hidden"
                                      ... />
    </Shell.SearchHandler>
    ...
</ContentPage>
```

## <a name="search-box-focus"></a>Stato attivo della casella di ricerca

Con il tocco in una casella di ricerca viene richiamata la tastiera su schermo e la casella di ricerca riceve lo stato attivo per l'input. È possibile ottenere questo comportamento anche a livello di programmazione chiamando il metodo `Focus`, che tenta di impostare lo stato attivo per l'input nella casella di ricerca e restituisce `true` in caso di esito positivo. Quando una casella di ricerca ottiene lo stato attivo, viene generato l'evento `Focus` e viene chiamato il metodo `OnFocused` che può essere sottoposto a override.

Quando lo stato attivo per l'input si trova in una casella di ricerca, se si tocca un'altra posizione sullo schermo la tastiera su schermo viene chiusa e la casella di ricerca perde lo stato attivo. È anche possibile ottenere questo comportamento a livello di programmazione chiamando il metodo `Unfocus`. Quando una casella di ricerca perde lo stato attivo, viene generato l'evento `Unfocused` e viene chiamato il metodo `OnUnfocus` che può essere sottoposto a override.

Lo stato attivo di una casella di ricerca può essere recuperato tramite la proprietà `IsFocused`, che restituisce `true` se lo stato attivo per l'input si trova in un `SearchHandler`.

## <a name="searchhandler-appearance"></a>Aspetto di SearchHandler

La classe `SearchHandler` definisce le proprietà seguenti che ne controllano l'aspetto:

- `BackgroundColor`, di tipo `Color`, corrisponde al colore di sfondo per il testo della casella di ricerca.
- `CancelButtonColor`, di tipo `Color`, corrisponde al colore del pulsante Annulla.
- `CharacterSpacing`, di `double`tipo , è la `SearchHandler` spaziatura tra i caratteri del testo.
- `FontAttributes`, di tipo `FontAttributes`, indica se il testo della casella di ricerca è in grassetto o corsivo.
- `FontFamily`, di tipo `string`, corrisponde alla famiglia di caratteri usato per il testo della casella di ricerca.
- `FontSize`, di tipo `double`, corrisponde alla dimensione del testo della casella di ricerca.
- `HorizontalTextAlignment`, di tipo `TextAlignment`, corrisponde all'allineamento orizzontale del testo della casella di ricerca.
- `PlaceholderColor`, di tipo `Color`, corrisponde al colore del testo segnaposto della casella di ricerca.
- `TextColor`, di tipo `Color`, corrisponde al colore del testo della casella di ricerca.
- `VerticalTextAlignment`, di `TextAlignment`tipo , è l'allineamento verticale del testo della casella di ricerca.

## <a name="searchhandler-keyboard"></a>Tastiera per SearchHandler

La tastiera che viene presentata quando `SearchHandler` gli utenti interagiscono `Keyboard` con un può essere impostata a livello di codice tramite la proprietà, su una delle seguenti proprietà dalla [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)– utilizzato per sms e luoghi in cui emoji sono utili.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default)– la tastiera predefinita.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)– utilizzato quando si inseriscono gli indirizzi e-mail.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric)– utilizzato quando si inseriscono i numeri.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain)– utilizzato durante l'immissione di testo, senza alcun [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) valore specificato.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)– utilizzato durante l'immissione dei numeri di telefono.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)– utilizzato durante l'immissione del testo.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)– utilizzato per l'immissione di percorsi di file & indirizzi web.

Per ottenere questo risultato, è possibile procedere come segue in XAML:

```xaml
<SearchHandler Keyboard="Email" />
```

Il codice C# equivalente è il seguente:

```csharp
SearchHandler searchHandler = new SearchHandler { Keyboard = Keyboard.Email };
```

La [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe dispone [`Create`](xref:Xamarin.Forms.Keyboard.Create*) anche di un metodo factory che può essere utilizzato per personalizzare una tastiera specificando le maiuscole, il controllo ortografico e il comportamento dei suggerimenti. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)I valori di enumerazione vengono specificati come `Keyboard` argomenti del metodo , con un personalizzato restituito. L'enumerazione `KeyboardFlags` contiene i valori seguenti:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None)– nessuna funzionalità viene aggiunta alla tastiera.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)– indica che la prima lettera della prima parola di ogni frase inserita verrà automaticamente maiuscola.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)– indica che il controllo ortografico verrà eseguito sul testo inserito.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)– indica che il completamento delle parole verrà offerto sul testo inserito.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord)– indica che la prima lettera di ogni parola verrà automaticamente maiuscola.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter)– indica che ogni carattere verrà automaticamente in maiuscolo.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone)– indica che non verrà eseguita alcuna maiuscole automatica.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)– indica che il controllo ortografico, il completamento delle parole e l'uso delle maiuscole delle frasi verranno eseguiti sul testo immesso.

L'esempio di codice XAML seguente [`Keyboard`](xref:Xamarin.Forms.Keyboard) mostra come personalizzare l'impostazione predefinita per offrire il completamento delle parole e convertire in maiuscolo ogni carattere immesso:The following XAML code example shows how to customize the default to offer word completions and capitalize every entered character:

```xaml
<SearchHandler Placeholder="Enter search terms">
    <SearchHandler.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </SearchHandler.Keyboard>
</SearchHandler>
```

Il codice C# equivalente è il seguente:

```csharp
SearchHandler searchHandler = new SearchHandler { Placeholder = "Enter search terms" };
searchHandler.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

## <a name="searchhandler-reference"></a>Informazioni di riferimento per SearchHandler

La classe `SearchHandler` definisce le proprietà seguenti che ne controllano l'aspetto e il comportamento:

- `BackgroundColor`, di tipo `Color`, corrisponde al colore di sfondo per il testo della casella di ricerca.
- `CancelButtonColor`, di tipo `Color`, corrisponde al colore del pulsante Annulla.
- `ClearIcon`, di [`ImageSource`](xref:Xamarin.Forms.ImageSource)tipo , l'icona visualizzata per cancellare il contenuto della casella di ricerca.
- `ClearIconHelpText`, di tipo `string`, ovvero il testo della guida accessibile per l'icona di cancellazione.
- `ClearIconName`, di tipo `string`, ovvero il nome dell'icona di cancellazione da usare con le utilità per la lettura dello schermo.
- `ClearPlaceholderCommand`, di tipo `ICommand`. Questo oggetto viene eseguito quando si tocca `ClearPlaceholderIcon`.
- `ClearPlaceholderCommandParameter`, di tipo `object`, ovvero il parametro passato a `ClearPlaceholderCommand`.
- `ClearPlaceholderEnabled`, di tipo `bool`, che determina se è possibile eseguire `ClearPlaceholderCommand`. Il valore predefinito è `true`.
- `ClearPlaceholderHelpText`, di tipo `string`, ovvero il testo della guida accessibile per l'icona di cancellazione del segnaposto.
- `ClearPlaceholderIcon`, di [`ImageSource`](xref:Xamarin.Forms.ImageSource)tipo , l'icona segnaposto non crittografata visualizzata quando la casella di ricerca è vuota.
- `ClearPlaceholderName`, di tipo `string`, ovvero il nome dell'icona di cancellazione del segnaposto da usare con le utilità per la lettura dello schermo.
- `Command`, di tipo `ICommand`. Questo oggetto viene eseguito quando la query di ricerca viene confermata.
- `CommandParameter`, di tipo `object`, ovvero il parametro passato a `Command`.
- `DisplayMemberName`, di tipo `string`, che rappresenta il nome o il percorso della proprietà visualizzata per ogni elemento di dati nella raccolta `ItemsSource`.
- `FontAttributes`, di tipo `FontAttributes`, indica se il testo della casella di ricerca è in grassetto o corsivo.
- `FontFamily`, di tipo `string`, corrisponde alla famiglia di caratteri usato per il testo della casella di ricerca.
- `FontSize`, di tipo `double`, corrisponde alla dimensione del testo della casella di ricerca.
- `HorizontalTextAlignment`, di tipo `TextAlignment`, corrisponde all'allineamento orizzontale del testo della casella di ricerca.
- `IsFocused`, di tipo `bool`, indica se un `SearchHandler` ha attualmente lo stato attivo per l'input.
- `IsSearchEnabled`, di tipo `bool`, che rappresenta lo stato abilitato della casella di ricerca. Il valore predefinito è `true`.
- `ItemsSource`, di tipo `IEnumerable`, che specifica la raccolta di elementi da visualizzare nell'area dei suggerimenti e ha un valore predefinito `null`.
- `ItemTemplate`di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), specifica il modello da applicare a ogni elemento della raccolta di elementi da visualizzare nell'area dei suggerimenti.
- `Keyboard`, di tipo `Keyboard`, corrisponde alla tastiera per `SearchHandler`.
- `Placeholder`, di tipo `string`, ovvero il testo da visualizzare quando la casella di ricerca è vuota.
- `PlaceholderColor`, di tipo `Color`, corrisponde al colore del testo segnaposto della casella di ricerca.
- `Query`, di tipo `string`, ovvero il testo immesso dall'utente nella casella di ricerca.
- `QueryIcon`, di [`ImageSource`](xref:Xamarin.Forms.ImageSource)tipo , l'icona utilizzata per indicare all'utente che la ricerca è disponibile.
- `QueryIconHelpText`, di tipo `string`, ovvero il testo della guida accessibile per l'icona di query.
- `QueryIconName`, di tipo `string`, ovvero il nome dell'icona di query da usare con le utilità per la lettura dello schermo.
- `SearchBoxVisibility`, di tipo `SearchBoxVisibility`, che indica la visibilità della casella di ricerca. Per impostazione predefinita, la casella di ricerca è visibile e completamente espansa.
- `SelectedItem`, di tipo `object`, ovvero l'elemento selezionato nei risultati della ricerca. Questa proprietà è di sola lettura e ha un valore predefinito `null`.
- `ShowsResults`, di tipo `bool`, che indica se sono previsti risultati della ricerca nell'area dei suggerimenti, quando viene immesso testo. Il valore predefinito è `false`.
- `TextColor`, di tipo `Color`, corrisponde al colore del testo della casella di ricerca.

Tutte queste proprietà sono [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) supportate da oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

La classe `SearchHandler` fornisce inoltre i metodi sottoponibili a override seguenti:

- `OnClearPlaceholderClicked`, chiamato ogni volta che si tocca `ClearPlaceholderIcon`.
- `OnItemSelected`, chiamato ogni volta che un risultato della ricerca viene selezionato dall'utente.
- `OnFocused`, chiamato quando un `SearchHandler` acquisisce lo stato attivo per l'input.
- `OnQueryChanged`, chiamato quando la proprietà `Query` cambia.
- `OnQueryConfirmed`, chiamato ogni volta che l'utente preme INVIO o conferma le query nella casella di ricerca.
- `OnUnfocus`, chiamato quando un `SearchHandler` perde lo stato attivo per l'input.

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Navigazione nella shell Xamarin.Forms](navigation.md)
