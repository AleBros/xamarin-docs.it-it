---
title: Selezione di Novell. Forms CollectionView
description: Per impostazione predefinita, la selezione di CollectionView è disabilitata. Tuttavia, è possibile abilitare una selezione singola o multipla.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: f1a3e8bb8959588e64339f70268370440f356be9
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738965"
---
# <a name="xamarinforms-collectionview-selection"></a>Selezione di Novell. Forms CollectionView

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce le proprietà seguenti che controllano la selezione degli elementi:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), di tipo [`SelectionMode`](xref:Xamarin.Forms.SelectionMode), la modalità di selezione.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), di tipo `object`, l'elemento selezionato nell'elenco. Questa proprietà ha una modalità di `TwoWay`associazione predefinita e ha un `null` valore se non è selezionato alcun elemento.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), di tipo `IList<object>`, gli elementi selezionati nell'elenco. Questa proprietà ha una modalità di `OneWay`associazione predefinita e ha un `null` valore se non è selezionato alcun elemento.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), di tipo `ICommand`, che viene eseguito quando viene modificato l'elemento selezionato.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), di tipo `object`, che è il parametro passato `SelectionChangedCommand`a.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che tali proprietà possono essere destinazioni di data binding.

Per impostazione predefinita [`CollectionView`](xref:Xamarin.Forms.CollectionView) , la selezione è disabilitata. Tuttavia, questo comportamento può essere modificato impostando il [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) valore della proprietà su uno [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) dei membri dell'enumerazione:

- `None`: indica che non è possibile selezionare gli elementi. Rappresenta il valore predefinito.
- `Single`: indica che è possibile selezionare un singolo elemento, con l'elemento selezionato evidenziato.
- `Multiple`: indica che è possibile selezionare più elementi con gli elementi selezionati evidenziati.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce un [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento che viene generato quando la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà viene modificata, a causa dell'utente che seleziona un elemento dall'elenco o quando un'applicazione imposta la proprietà. Inoltre, questo evento viene generato quando la [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) proprietà viene modificata. L' [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) oggetto che accompagna l' `SelectionChanged` evento è costituito da due proprietà, entrambe `IReadOnlyList<object>`di tipo:

- `PreviousSelection`: elenco di elementi selezionati prima della modifica della selezione.
- `CurrentSelection`: elenco di elementi selezionati, dopo la modifica della selezione.

## <a name="single-selection"></a>Selezione singola

Quando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà è impostata su `Single`, è [`CollectionView`](xref:Xamarin.Forms.CollectionView) possibile selezionare un singolo elemento in. Quando si seleziona un elemento, la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà verrà impostata sul valore dell'elemento selezionato. Quando questa proprietà [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) viene modificata, viene eseguito (con il valore [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) di passato a `ICommand`) e viene generato l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento.

Nell'esempio di codice XAML riportato [`CollectionView`](xref:Xamarin.Forms.CollectionView) di seguito viene illustrato un oggetto che può rispondere alla selezione di un singolo elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

In questo esempio di codice, `OnCollectionViewSelectionChanged` il gestore eventi viene eseguito quando [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) viene generato l'evento, con il gestore eventi che recupera l'elemento selezionato in precedenza e l'elemento selezionato corrente:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> L' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento può essere generato da modifiche che si verificano in seguito alla modifica [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) della proprietà.

Gli screenshot seguenti mostrano la selezione di un singolo elemento [`CollectionView`](xref:Xamarin.Forms.CollectionView)in un:

[ ![Screenshot di un elenco di riepilogo verticale con selezione singola, in iOS e Android](selection-images/single-selection.png "CollectionView elenco verticale con selezione singola") ] (selection-images/single-selection-large.png#lightbox "Elenco verticale di CollectionView con selezione singola")

## <a name="multiple-selection"></a>Selezione multipla

Quando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà è impostata su `Multiple`, è possibile selezionare più [`CollectionView`](xref:Xamarin.Forms.CollectionView) elementi nell'oggetto. Quando si selezionano elementi, [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) la proprietà viene impostata sugli elementi selezionati. Quando questa proprietà [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) viene modificata, viene eseguito (con il valore [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) di passato a `ICommand`) e viene generato l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento.

Nell'esempio di codice XAML riportato [`CollectionView`](xref:Xamarin.Forms.CollectionView) di seguito viene illustrato un che può rispondere a più selezioni di elementi:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

In questo esempio di codice, `OnCollectionViewSelectionChanged` il gestore eventi viene eseguito quando [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) viene generato l'evento, con il gestore eventi che recupera gli elementi selezionati in precedenza e gli elementi selezionati correnti:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> L' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento può essere generato da modifiche che si verificano in seguito alla modifica [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) della proprietà.

Gli screenshot seguenti mostrano la selezione di più elementi in [`CollectionView`](xref:Xamarin.Forms.CollectionView)un:

[ ![Screenshot di un elenco di ritrovi verticali con selezione multipla, in iOS e Android](selection-images/multiple-selection.png "CollectionView elenco verticale con selezione multipla") ] (selection-images/multiple-selection-large.png#lightbox "Elenco verticale di CollectionView con selezione multipla")

## <a name="single-pre-selection"></a>Selezione singola

Quando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà è impostata su `Single`, è [`CollectionView`](xref:Xamarin.Forms.CollectionView) possibile pre-selezionare un singolo elemento in impostando la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà sull'elemento. Nell'esempio di codice XAML seguente `CollectionView` viene illustrato un oggetto che precede la selezione di un singolo elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey}">
    ...
</CollectionView>
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey");
```

> [!NOTE]
> La [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà dispone di una modalità di `TwoWay`associazione predefinita.

Il [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) esegue l'associazione dati di proprietà per il `SelectedMonkey` proprietà del modello di visualizzazione connessa, che è di tipo `Monkey`. Per impostazione predefinita, `TwoWay` viene utilizzata un'associazione in modo che, se l'utente modifica l'elemento selezionato, il `SelectedMonkey` valore della proprietà verrà impostato sull'oggetto `Monkey` selezionato. La `SelectedMonkey` proprietà è definita `MonkeysViewModel` nella classe e viene impostata sul quarto elemento della `Monkeys` raccolta:

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    Monkey selectedMonkey;
    public Monkey SelectedMonkey
    {
        get
        {
            return selectedMonkey;
        }
        set
        {
            if (selectedMonkey != value)
            {
                selectedMonkey = value;
            }
        }
    }

    public MonkeysViewModel()
    {
        ...
        selectedMonkey = Monkeys.Skip(3).FirstOrDefault();
    }
    ...
}
```

Pertanto, quando [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene visualizzato, il quarto elemento dell'elenco è preselezionato:

[ ![Screenshot di un elenco di ritrovi verticali con selezione singola, in iOS e Android](selection-images/single-pre-selection.png "CollectionView elenco verticale con selezione singola") ] (selection-images/single-pre-selection-large.png#lightbox "Elenco verticale di CollectionView con selezione preliminare singola")

## <a name="multiple-pre-selection"></a>Pre-selezione multipla

Quando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà è impostata su `Multiple`, è possibile pre- [`CollectionView`](xref:Xamarin.Forms.CollectionView) selezionare più elementi nell'oggetto. Nell'esempio di codice XAML riportato `CollectionView` di seguito viene illustrato un oggetto che consente la selezione preliminare di più elementi:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectedItems="{Binding SelectedMonkeys}">
    ...
</CollectionView>
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemsProperty, "SelectedMonkeys");
```

> [!NOTE]
> La [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) proprietà dispone di una modalità di `OneWay`associazione predefinita.

Il [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) esegue l'associazione dati di proprietà per il `SelectedMonkeys` proprietà del modello di visualizzazione connessa, che è di tipo `ObservableCollection<object>`. La `SelectedMonkeys` proprietà è definita `MonkeysViewModel` nella classe e viene impostata sul secondo, il quarto `Monkeys` e il quinto elemento della raccolta:

```csharp
namespace CollectionViewDemos.ViewModels
{
    public class MonkeysViewModel : INotifyPropertyChanged
    {
        ...
        ObservableCollection<object> selectedMonkeys;
        public ObservableCollection<object> SelectedMonkeys
        {
            get
            {
                return selectedMonkeys;
            }
            set
            {
                if (selectedMonkeys != value)
                {
                    selectedMonkeys = value;
                }
            }
        }

        public MonkeysViewModel()
        {
            ...
            SelectedMonkeys = new ObservableCollection<object>()
            {
                Monkeys[1], Monkeys[3], Monkeys[4]
            };
        }
        ...
    }
}
```

Pertanto, quando viene [`CollectionView`](xref:Xamarin.Forms.CollectionView) visualizzato, il secondo, il quarto e il quinto elemento dell'elenco sono pre-selezionati:

[ ![Screenshot di un elenco di riepilogo verticale con più selezioni preliminari, in iOS e Android](selection-images/multiple-pre-selection.png "CollectionView elenco verticale con più pre-selezione") ] (selection-images/multiple-pre-selection-large.png#lightbox "Elenco verticale di CollectionView con più pre-selezione")

## <a name="clearing-selections"></a>Cancellazione delle selezioni

Le [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) e possono essere cancellate impostando o gli oggetti a `null`cui vengono associati.

## <a name="change-selected-item-color"></a>Modifica colore elemento selezionato

[`CollectionView`](xref:Xamarin.Forms.CollectionView)dispone di `Selected` un oggetto [`VisualState`](xref:Xamarin.Forms.VisualState) che può essere utilizzato per avviare una modifica visiva all'elemento selezionato in `CollectionView`. Un caso `VisualState` d'uso comune è quello di modificare il colore di sfondo dell'elemento selezionato, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="Grid">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal" />
                        <VisualState x:Name="Selected">
                            <VisualState.Setters>
                                <Setter Property="BackgroundColor"
                                        Value="LightSkyBlue" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}"
                        SelectionMode="Single">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        ...
                    </Grid>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

> [!IMPORTANT]
> L' [`Style`](xref:Xamarin.Forms.Style) oggetto che `Selected` [`TargetType`](xref:Xamarin.Forms.Style.TargetType) [contienedeveavere`DataTemplate`](xref:Xamarin.Forms.DataTemplate)un valore della proprietà che è il tipo dell'elemento radice di, che è impostato come valore della `ItemTemplate` proprietà. `VisualState`

In questo esempio, il [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) valore della proprietà è impostato `Grid` su perché [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) l'elemento radice di è un [`Grid`](xref:Xamarin.Forms.Grid)oggetto. `Selected` [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) Specifica che, `LightSkyBlue`quando viene selezionato un elemento in, l'oggetto dell'elemento verrà impostato su: [`CollectionView`](xref:Xamarin.Forms.CollectionView) [`VisualState`](xref:Xamarin.Forms.VisualState)

[ ![Screenshot di un elenco di ritrovi verticali con un colore di selezione singola personalizzato, in iOS e Android](selection-images/single-selection-color.png "CollectionView elenco verticale con un colore di selezione singola personalizzato") ] (selection-images/single-selection-color-large.png#lightbox "Elenco verticale di CollectionView con un colore di selezione singola personalizzato")

Per ulteriori informazioni sugli stati visivi, vedere [Novell. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Disabilita selezione

[`CollectionView`](xref:Xamarin.Forms.CollectionView)per impostazione predefinita, la selezione è disabilitata. Tuttavia, se una `CollectionView` selezione è abilitata, può essere disabilitata impostando [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) la proprietà `None`su:

```xaml
<CollectionView ...
                SelectionMode="None" />
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

Quando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà è impostata su `None`, gli elementi in [`CollectionView`](xref:Xamarin.Forms.CollectionView) `null`non possono essere selezionati, [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) la proprietà rimarrà e l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento non verrà generato.

> [!NOTE]
> Quando viene selezionato [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) un elemento e la proprietà viene modificata da `Single` a `None`, la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà viene impostata su `null` e l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento viene generato con una proprietà vuota `CurrentSelection` . .

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Gestore dello stato di visualizzazione di Novell. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
