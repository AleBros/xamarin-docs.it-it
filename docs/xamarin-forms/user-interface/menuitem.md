---
title: MenuItem Novell. Forms
description: La classe MenuItem viene utilizzata per creare voci di menu per menu come i menu di scelta rapida dell'elemento ListView e i menu a comparsa dell'applicazione shell.
ms.prod: xamarin
ms.assetId: 62655C21-6053-466D-A7F4-DE2BE36538F5
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/01/2019
ms.openlocfilehash: 68560c6cc814f54bb8ba9348bc53334089c36a93
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68984473"
---
# <a name="xamarinforms-menuitem"></a>MenuItem Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-menuitem/)

La classe Novell. [`MenuItem`](xref:Xamarin.Forms.MenuItem) Forms è utilizzata per definire le voci di menu per menu `ListView` quali menu di scelta rapida dell'elemento e menu a comparsa dell'applicazione shell.

Lo screenshot seguente mostra `MenuItem` gli oggetti in `ListView` un menu di scelta rapida in iOS e Android:

[ !["MenuItems on iOS and Android"](menuitem-images/menuitem-demo-cropped.png "MenuItems in iOS e Android") ] (menuitem-images/menuitem-demo-full.png#lightbox "MenuItems in iOS e Androidfull image")

La `MenuItem` classe definisce le proprietà seguenti:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)è un `ICommand` oggetto che consente di associare le azioni dell'utente, ad esempio le manopole o i clic, ai comandi definiti in un elemento ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)è un `object` oggetto che specifica il parametro che deve essere passato `Command`a.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)`ImageSource` valore che definisce l'icona di visualizzazione.
* [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)valore che indica se l'oggetto `MenuItem` rimuove dall'elenco l'elemento dell'interfaccia utente associato. `bool`
* [`IsEnabled`](xref:Xamarin.Forms.MenuItem.IsEnabled)`bool` valore che determina se l'oggetto risponde all'input dell'utente.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)`string` valore che specifica il testo visualizzato.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, quindi l' `MenuItem` istanza può essere la destinazione di associazioni dati.

## <a name="create-a-menuitem"></a>Creare un MenuItem

`MenuItem`gli oggetti possono essere utilizzati all'interno di un menu `ListView` di scelta rapida per gli elementi di un oggetto. Il modello più comune consiste nel creare `MenuItem` oggetti all'interno `ViewCell` di un'istanza, `DataTemplate` che viene utilizzata come oggetto per `ListView`gli `ItemTemplate`oggetti. Quando l' `ListView` oggetto viene popolato `DataTemplate`, creerà ogni elemento usando, esponendo le `MenuItem` scelte quando il menu di scelta rapida viene attivato per un elemento.

Nell'esempio seguente viene `MenuItem` illustrata la creazione di un'istanza `ListView` nel contesto di un oggetto:

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

Un `MenuItem` oggetto può essere creato anche nel codice:

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

La classe `MenuItem` espone un evento `Clicked`. Un gestore eventi può essere associato a questo evento per rispondere ai colpetti o ai clic `MenuItem` sull'istanza in XAML:

```xaml
<MenuItem ...
          Clicked="OnItemClicked" />
```

Un gestore eventi può essere allegato anche nel codice:

```csharp
MenuItem item = new MenuItem { ... }
item.Clicked += OnItemClicked;
```

Negli esempi precedenti è stato `OnItemClicked` fatto riferimento a un gestore eventi. Nel codice seguente viene illustrata un'implementazione di esempio:

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

La `MenuItem` classe supporta il modello MVC (Model-View-ViewModel) tramite [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) gli oggetti e `ICommand` l'interfaccia. Il codice XAML seguente `MenuItem` Mostra le istanze associato ai comandi definiti in un elemento ViewModel:

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

Nell'esempio precedente, vengono definiti `MenuItem` due oggetti con le relative `Command` proprietà `CommandParameter` e associate ai comandi nell'elemento ViewModel. Il ViewModel contiene i comandi a cui viene fatto riferimento in XAML:

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

Nell'applicazione di esempio è `DataService` inclusa una classe utilizzata per ottenere un elenco di elementi per `ListView` il popolamento degli oggetti. Viene creata un'istanza di ViewModel con elementi della `DataService` classe e impostati `BindingContext` come nel code-behind:

```csharp
public MenuItemXamlMvvmPage()
{
    InitializeComponent();
    BindingContext = new ListPageViewModel(DataService.GetListItems());
}
```

## <a name="menuitem-icons"></a>Icone MenuItem

> [!WARNING]
> `MenuItem`gli oggetti visualizzano solo le icone in Android. In altre piattaforme verrà visualizzato solo il testo specificato dalla `Text` proprietà.

 Le icone vengono specificate utilizzando `IconImageSource` la proprietà. Se si specifica un'icona, il testo specificato dalla `Text` proprietà non verrà visualizzato. La schermata seguente mostra un `MenuItem` con un'icona in Android:

!["Screenshot dell'icona MenuItem in Android"](menuitem-images/menuitem-android-icon.png "Screenshot dell'icona MenuItem in Android")

Per altre informazioni sull'uso delle immagini in Novell. Forms, vedere [Immagini in Novell. Forms](~/xamarin-forms/user-interface/images.md).

## <a name="cross-platform-context-menu-behavior"></a>Comportamento del menu di scelta rapida multipiattaforma

È possibile accedere ai menu di scelta rapida e visualizzarli in modo diverso in ogni piattaforma.

In Android, il menu di scelta rapida viene attivato con una pressione prolungata su un elemento dell'elenco. Il menu di scelta rapida sostituisce l'area del titolo e `MenuItem` della barra di spostamento e le opzioni vengono visualizzate come pulsanti orizzontali.

!["Screenshot del menu di scelta rapida in Android"](menuitem-images/menuitem-android-icon.png "Screenshot del menu di scelta rapida in Android")

In iOS, il menu di scelta rapida viene attivato scorrendo rapidamente un elemento dell'elenco. Il menu di scelta rapida viene visualizzato nell'elemento elenco `MenuItems` e viene visualizzato come pulsanti orizzontali.

!["Screenshot del menu di scelta rapida in iOS"](menuitem-images/menuitem-ios-contextmenu.png "Screenshot del menu di scelta rapida in iOS")

In UWP, il menu di scelta rapida viene attivato facendo clic con il pulsante destro del mouse su un elemento dell'elenco. Il menu di scelta rapida viene visualizzato accanto al cursore come elenco verticale.

!["Screenshot del menu di scelta rapida in UWP"](menuitem-images/menuitem-uwp.png "Screenshot del menu di scelta rapida in UWP")

## <a name="related-links"></a>Collegamenti correlati

* [Demo MenuItem](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-menuitem/)
* [Immagini in Novell. Forms](~/xamarin-forms/user-interface/images.md)
