---
title: " Xamarin.Forms MenuItem" Description: "la classe MenuItem viene utilizzata per creare voci di menu per menu come i menu di scelta rapida dell'elemento ListView e i menu a comparsa dell'applicazione shell".
ms. prod: Novell MS. assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5 ms. Technology: Novell-Forms Author: profexorgeek ms. Author: jusjohns ms. Date: 08/01/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-menuitem"></a>Xamarin.FormsMenuItem

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)

La Xamarin.Forms [`MenuItem`](xref:Xamarin.Forms.MenuItem) classe definisce le voci di menu per i menu come menu di `ListView` scelta rapida dell'elemento e menu a comparsa dell'applicazione shell.

Gli screenshot seguenti mostrano `MenuItem` gli oggetti in un `ListView` menu di scelta rapida in iOS e Android:

[!["MenuItems in iOS e Android"](menuitem-images/menuitem-demo-cropped.png "MenuItems in iOS e Android")](menuitem-images/menuitem-demo-full.png#lightbox "MenuItems su un'immagine completa iOS e Android")

La `MenuItem` classe definisce le proprietà seguenti:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)è un oggetto `ICommand` che consente di associare le azioni dell'utente, ad esempio le manopole o i clic, ai comandi definiti in un elemento ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)è un oggetto `object` che specifica il parametro che deve essere passato a `Command` .
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)`ImageSource`valore che definisce l'icona di visualizzazione.
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)`bool`valore che indica se l'oggetto `MenuItem` rimuove dall'elenco l'elemento dell'interfaccia utente associato.
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)`bool`valore che indica se l'oggetto risponde all'input dell'utente.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)`string`valore che specifica il testo visualizzato.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, quindi l' `MenuItem` istanza può essere la destinazione di associazioni dati.

## <a name="create-a-menuitem"></a>Creare un MenuItem

`MenuItem`gli oggetti possono essere utilizzati all'interno di un menu di scelta rapida per gli `ListView` elementi di un oggetto. Il modello più comune consiste nel creare `MenuItem` oggetti all'interno di un' `ViewCell` istanza, che viene utilizzata come `DataTemplate` oggetto per gli oggetti `ListView` `ItemTemplate` . Quando l' `ListView` oggetto viene popolato, creerà ogni elemento usando `DataTemplate` , esponendo le `MenuItem` scelte quando il menu di scelta rapida viene attivato per un elemento.

Nell'esempio seguente viene illustrata la `MenuItem` creazione di un'istanza nel contesto di un `ListView` oggetto:

```xaml
<ListView>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.ContextActions>
                    <MenuItem Text="Context Menu Option" />
                </ViewCell.ContextActions>
                <Label Text="{Binding .}" />
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Un oggetto `MenuItem` può essere creato anche nel codice:

```csharp
// A function returns a ViewCell instance that
// is used as the template for each list item
DataTemplate dataTemplate = new DataTemplate(() =>
{
    // A Label displays the list item text
    Label label = new Label();
    label.SetBinding(Label.TextProperty, ".");

    // A ViewCell serves as the DataTemplate
    ViewCell viewCell = new ViewCell
    {
        View = label
    };

    // Add a MenuItem instance to the ContextActions
    MenuItem menuItem = new MenuItem
    {
        Text = "Context Menu Option"
    };
    viewCell.ContextActions.Add(menuItem);

    // The function returns the custom ViewCell
    // to the DataTemplate constructor
    return viewCell;
});

// Finally, the dataTemplate is provided to
// the ListView object
ListView listView = new ListView
{
    ...
    ItemTemplate = dataTemplate
};
```

## <a name="define-menuitem-behavior-with-events"></a>Definire il comportamento MenuItem con gli eventi

La classe `MenuItem` espone un evento `Clicked`. Un gestore eventi può essere associato a questo evento per rispondere ai colpetti o ai clic sull' `MenuItem` istanza in XAML:

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

Un gestore eventi può essere allegato anche nel codice:

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

Negli esempi precedenti è stato fatto riferimento a un `OnItemClicked` gestore eventi. Nel codice seguente viene illustrata un'implementazione di esempio:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    // The sender is the menuItem
    MenuItem menuItem = sender as MenuItem;

    // Access the list item through the BindingContext
    var contextItem = menuItem.BindingContext;

    // Do something with the contextItem here
}
```

## <a name="define-menuitem-behavior-with-mvvm"></a>Definire il comportamento MenuItem con MVVM

La `MenuItem` classe supporta il modello MVC (Model-View-ViewModel) tramite [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) gli oggetti e l' `ICommand` interfaccia. Il codice XAML seguente mostra le `MenuItem` istanze associato ai comandi definiti in un elemento ViewModel:

```xaml
<ContentPage.BindingContext>
    <viewmodels:ListPageViewModel />
</ContentPage.BindingContext>

<StackLayout>
    <Label Text="{Binding Message}" ... />
    <ListView ItemsSource="{Binding Items}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ViewCell.ContextActions>
                        <MenuItem Text="Edit"
                                    IconImageSource="icon.png"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.EditCommand}"
                                    CommandParameter="{Binding .}"/>
                        <MenuItem Text="Delete"
                                    Command="{Binding Source={x:Reference contentPage}, Path=BindingContext.DeleteCommand}"
                                    CommandParameter="{Binding .}"/>
                    </ViewCell.ContextActions>
                    <Label Text="{Binding .}" />
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

Nell'esempio precedente, `MenuItem` vengono definiti due oggetti con le relative `Command` `CommandParameter` proprietà e associate ai comandi nell'elemento ViewModel. Il ViewModel contiene i comandi a cui viene fatto riferimento in XAML:

```csharp
public class ListPageViewModel : INotifyPropertyChanged
{
    ...

    public ICommand EditCommand => new Command<string>((string item) =>
    {
        Message = $"Edit command was called on: {item}";
    });

    public ICommand DeleteCommand => new Command<string>((string item) =>
    {
        Message = $"Delete command was called on: {item}";
    });
}
```

Nell'applicazione di esempio è inclusa una `DataService` classe utilizzata per ottenere un elenco di elementi per il popolamento degli `ListView` oggetti. Viene creata un'istanza di ViewModel con elementi della `DataService` classe e impostati come `BindingContext` nel code-behind:

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>Icone MenuItem

> [!WARNING]
> `MenuItem`gli oggetti visualizzano solo le icone in Android. In altre piattaforme verrà visualizzato solo il testo specificato dalla `Text` Proprietà.

 Le icone vengono specificate utilizzando la `IconImageSource` Proprietà. Se si specifica un'icona, il testo specificato dalla `Text` proprietà non verrà visualizzato. La schermata seguente mostra un `MenuItem` con un'icona in Android:

!["Screenshot dell'icona MenuItem in Android"](menuitem-images/menuitem-android-icon.png "Screenshot dell'icona MenuItem in Android")

Per ulteriori informazioni sull'utilizzo delle immagini in Xamarin.Forms , vedere [images Xamarin.Forms in ](~/xamarin-forms/user-interface/images.md).

## <a name="enable-or-disable-a-menuitem-at-runtime"></a>Abilitare o disabilitare un MenuItem in fase di esecuzione

Per abilitare la disabilitazione di un oggetto in fase di `MenuItem` esecuzione, associare la relativa `Command` proprietà a un' `ICommand` implementazione di e assicurarsi che un `canExecute` delegato abiliti e disattivi l'oggetto in `ICommand` base alle esigenze.

> [!IMPORTANT]
> Non associare la `IsEnabled` proprietà a un'altra proprietà quando si utilizza la `Command` proprietà per abilitare o disabilitare `MenuItem` .

Nell'esempio seguente viene illustrato un oggetto la `MenuItem` cui `Command` proprietà è associata a un oggetto `ICommand` denominato `MyCommand` :

```xaml
<MenuItem Text="My menu item"
          Command="{Binding MyCommand}" />
```

L' `ICommand` implementazione richiede un `canExecute` delegato che restituisce il valore di una `bool` proprietà per abilitare e disabilitare `MenuItem` :

```csharp
public class MyViewModel : INotifyPropertyChanged
{
    bool isMenuItemEnabled = false;
    public bool IsMenuItemEnabled
    {
        get { return isMenuItemEnabled; }
        set
        {
            isMenuItemEnabled = value;
            MyCommand.ChangeCanExecute();
        }
    }

    public Command MyCommand { get; private set; }

    public ToolbarItemViewModel()
    {
        MyCommand = new Command(() =>
        {
            // Execute logic here
        },
        () => IsToolbarItemEnabled);
    }
}
```

In questo esempio, `MenuItem` è disabilitato fino a quando non `IsMenuItemEnabled` viene impostata la proprietà. Quando si verifica questo problema, `Command.ChangeCanExecute` viene chiamato il metodo che determina la `canExecute` `MyCommand` rivalutazione del delegato.

## <a name="cross-platform-context-menu-behavior"></a>Comportamento del menu di scelta rapida multipiattaforma

È possibile accedere ai menu di scelta rapida e visualizzarli in modo diverso in ogni piattaforma.

In Android, il menu di scelta rapida viene attivato con una pressione prolungata su un elemento dell'elenco. Il menu di scelta rapida sostituisce l'area del titolo e della barra di spostamento e le `MenuItem` opzioni vengono visualizzate come pulsanti orizzontali.

!["Screenshot del menu di scelta rapida in Android"](menuitem-images/menuitem-android-icon.png "Screenshot del menu di scelta rapida in Android")

In iOS, il menu di scelta rapida viene attivato scorrendo rapidamente un elemento dell'elenco. Il menu di scelta rapida viene visualizzato nell'elemento elenco e `MenuItems` viene visualizzato come pulsanti orizzontali.

!["Screenshot del menu di scelta rapida in iOS"](menuitem-images/menuitem-ios-contextmenu.png "Screenshot del menu di scelta rapida in iOS")

In UWP, il menu di scelta rapida viene attivato facendo clic con il pulsante destro del mouse su un elemento dell'elenco. Il menu di scelta rapida viene visualizzato accanto al cursore come elenco verticale.

!["Screenshot del menu di scelta rapida in UWP"](menuitem-images/menuitem-uwp.png "Screenshot del menu di scelta rapida in UWP")

## <a name="related-links"></a>Collegamenti correlati

* [Demo MenuItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-menuitemdemos/)
* [Immagini inXamarin.Forms](~/xamarin-forms/user-interface/images.md)
