---
title: Xamarin.FormsSelezione di CollectionView
description: Per impostazione predefinita, la selezione di CollectionView è disabilitata. Tuttavia, è possibile abilitare una selezione singola o multipla.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 39f118d7073fc551923f891681c8c6cf6a4c5ddd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137384"
---
# <a name="xamarinforms-collectionview-selection"></a>Xamarin.FormsSelezione di CollectionView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce le proprietà seguenti che controllano la selezione degli elementi:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), di tipo [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) , la modalità di selezione.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), di tipo `object` , l'elemento selezionato nell'elenco. Questa proprietà ha una modalità di associazione predefinita `TwoWay` e ha un `null` valore se non è selezionato alcun elemento.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), di tipo `IList<object>` , gli elementi selezionati nell'elenco. Questa proprietà ha una modalità di associazione predefinita `OneWay` e ha un `null` valore se non è selezionato alcun elemento.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), di tipo `ICommand` , che viene eseguito quando viene modificato l'elemento selezionato.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), di tipo `object` , che è il parametro passato a `SelectionChangedCommand` .

Tutte queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

Per impostazione predefinita, [`CollectionView`](xref:Xamarin.Forms.CollectionView) la selezione è disabilitata. Tuttavia, questo comportamento può essere modificato impostando il [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) valore della proprietà su uno dei [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) membri dell'enumerazione:

- `None`: indica che non è possibile selezionare gli elementi. Si tratta del valore predefinito.
- `Single`: indica che è possibile selezionare un singolo elemento, con l'elemento selezionato evidenziato.
- `Multiple`: indica che è possibile selezionare più elementi con gli elementi selezionati evidenziati.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce un [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento che viene generato quando la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà viene modificata, a causa dell'utente che seleziona un elemento dall'elenco o quando un'applicazione imposta la proprietà. Inoltre, questo evento viene generato quando la proprietà viene [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) modificata. L' [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) oggetto che accompagna l' `SelectionChanged` evento è costituito da due proprietà, entrambe di tipo `IReadOnlyList<object>` :

- `PreviousSelection`: elenco di elementi selezionati prima della modifica della selezione.
- `CurrentSelection`: elenco di elementi selezionati, dopo la modifica della selezione.

Dispone inoltre di [`CollectionView`](xref:Xamarin.Forms.CollectionView) un `UpdateSelectedItems` metodo che aggiorna la [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) proprietà con un elenco di elementi selezionati, mentre genera solo una singola notifica di modifica.

## <a name="single-selection"></a>Selezione singola

Quando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà è impostata su `Single` , è possibile selezionare un singolo elemento in [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Quando si seleziona un elemento, la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà verrà impostata sul valore dell'elemento selezionato. Quando questa proprietà viene modificata, [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) viene eseguito (con il valore di [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) passato a `ICommand` ) e viene [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) generato l'evento.

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) che può rispondere alla selezione di un singolo elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

In questo esempio di codice, il `OnCollectionViewSelectionChanged` gestore eventi viene eseguito quando viene [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) generato l'evento, con il gestore eventi che recupera l'elemento selezionato in precedenza e l'elemento selezionato corrente:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> L' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento può essere generato da modifiche che si verificano in seguito alla modifica della [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) Proprietà.

Gli screenshot seguenti mostrano la selezione di un singolo elemento in un [`CollectionView`](xref:Xamarin.Forms.CollectionView) :

[![Screenshot di un elenco di ritrovi verticali con selezione singola, in iOS e Android](selection-images/single-selection.png "Elenco verticale di CollectionView con selezione singola")](selection-images/single-selection-large.png#lightbox "Elenco verticale di CollectionView con selezione singola")

## <a name="multiple-selection"></a>Selezione multipla

Quando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà è impostata su `Multiple` , è possibile selezionare più elementi nell'oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Quando si selezionano elementi, la [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) proprietà viene impostata sugli elementi selezionati. Quando questa proprietà viene modificata, [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) viene eseguito (con il valore di [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) passato a `ICommand` ) e viene [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) generato l'evento.

Nell'esempio di codice XAML riportato di seguito viene illustrato un [`CollectionView`](xref:Xamarin.Forms.CollectionView) che può rispondere a più selezioni di elementi:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

In questo esempio di codice, il `OnCollectionViewSelectionChanged` gestore eventi viene eseguito quando viene [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) generato l'evento, con il gestore eventi che recupera gli elementi selezionati in precedenza e gli elementi selezionati correnti:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> L' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento può essere generato da modifiche che si verificano in seguito alla modifica della [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) Proprietà.

Gli screenshot seguenti mostrano la selezione di più elementi in un [`CollectionView`](xref:Xamarin.Forms.CollectionView) :

[![Screenshot di un elenco di ritrovi verticali con selezione multipla, in iOS e Android](selection-images/multiple-selection.png "Elenco verticale di CollectionView con selezione multipla")](selection-images/multiple-selection-large.png#lightbox "Elenco verticale di CollectionView con selezione multipla")

## <a name="single-pre-selection"></a>Selezione singola

Quando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà è impostata su `Single` , è possibile pre-selezionare un singolo elemento in impostando [`CollectionView`](xref:Xamarin.Forms.CollectionView) la [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà sull'elemento. Nell'esempio di codice XAML seguente viene illustrato un oggetto `CollectionView` che precede la selezione di un singolo elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey}">
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey");
```

> [!NOTE]
> La [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà dispone di una modalità di associazione predefinita `TwoWay` .

I [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) dati della proprietà vengono associati alla `SelectedMonkey` proprietà del modello di visualizzazione connesso, che è di tipo `Monkey` . Per impostazione predefinita, `TwoWay` viene utilizzata un'associazione in modo che, se l'utente modifica l'elemento selezionato, il valore della `SelectedMonkey` proprietà verrà impostato sull' `Monkey` oggetto selezionato. La `SelectedMonkey` proprietà è definita nella `MonkeysViewModel` classe e viene impostata sul quarto elemento della `Monkeys` raccolta:

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

[![Screenshot di un elenco di ritrovi verticali con una singola pre-selezione, in iOS e Android](selection-images/single-pre-selection.png "Elenco verticale di CollectionView con selezione preliminare singola")](selection-images/single-pre-selection-large.png#lightbox "Elenco verticale di CollectionView con selezione preliminare singola")

## <a name="multiple-pre-selection"></a>Pre-selezione multipla

Quando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà è impostata su `Multiple` , è [`CollectionView`](xref:Xamarin.Forms.CollectionView) possibile pre-selezionare più elementi nell'oggetto. Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto `CollectionView` che consente la selezione preliminare di più elementi:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectedItems="{Binding SelectedMonkeys}">
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemsProperty, "SelectedMonkeys");
```

> [!NOTE]
> La [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) proprietà dispone di una modalità di associazione predefinita `OneWay` .

I [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) dati della proprietà vengono associati alla `SelectedMonkeys` proprietà del modello di visualizzazione connesso, che è di tipo `ObservableCollection<object>` . La `SelectedMonkeys` proprietà è definita nella `MonkeysViewModel` classe e viene impostata sul secondo, il quarto e il quinto elemento della `Monkeys` raccolta:

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

Pertanto, quando [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene visualizzato, il secondo, il quarto e il quinto elemento dell'elenco sono pre-selezionati:

[![Screenshot di un elenco di ritrovi verticali con più selezioni preliminari in iOS e Android](selection-images/multiple-pre-selection.png "Elenco verticale di CollectionView con più pre-selezione")](selection-images/multiple-pre-selection-large.png#lightbox "Elenco verticale di CollectionView con più pre-selezione")

## <a name="clear-selections"></a>Cancella selezioni

Le [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) proprietà e possono essere cancellate impostando o gli oggetti a cui vengono associati `null` .

## <a name="change-selected-item-color"></a>Modifica colore elemento selezionato

[`CollectionView`](xref:Xamarin.Forms.CollectionView)dispone di un oggetto `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) che può essere utilizzato per avviare una modifica visiva all'elemento selezionato in `CollectionView` . Un caso d'uso comune `VisualState` è quello di modificare il colore di sfondo dell'elemento selezionato, come illustrato nell'esempio di codice XAML seguente:

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
> L'oggetto [`Style`](xref:Xamarin.Forms.Style) che contiene `Selected` `VisualState` deve avere un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valore della proprietà che è il tipo dell'elemento radice di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , che è impostato come valore della `ItemTemplate` Proprietà.

In questo esempio, il [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) valore della proprietà è impostato su `Grid` perché l'elemento radice di [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) è un oggetto [`Grid`](xref:Xamarin.Forms.Grid) . `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) Specifica che, quando viene selezionato un elemento in [`CollectionView`](xref:Xamarin.Forms.CollectionView) , l'oggetto [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) dell'elemento verrà impostato su `LightSkyBlue` :

[![Screenshot di un elenco di ritrovi verticali con un colore di selezione singola personalizzato, in iOS e Android](selection-images/single-selection-color.png "Elenco verticale di CollectionView con un colore di selezione singola personalizzato")](selection-images/single-selection-color-large.png#lightbox "Elenco verticale di CollectionView con un colore di selezione singola personalizzato")

Per ulteriori informazioni sugli stati visivi, vedere [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Disabilita selezione

[`CollectionView`](xref:Xamarin.Forms.CollectionView)per impostazione predefinita, la selezione è disabilitata. Tuttavia, se una `CollectionView` selezione è abilitata, può essere disabilitata impostando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà su `None` :

```xaml
<CollectionView ...
                SelectionMode="None" />
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

Quando la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà è impostata su `None` , gli elementi in [`CollectionView`](xref:Xamarin.Forms.CollectionView) non possono essere selezionati, la proprietà rimarrà [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) `null` e l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento non verrà generato.

> [!NOTE]
> Quando viene selezionato un elemento e la [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà viene modificata da `Single` a `None` , la proprietà viene [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) impostata su `null` e l' [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento viene generato con una `CurrentSelection` proprietà vuota.

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.FormsGestione stato di visualizzazione](~/xamarin-forms/user-interface/visual-state-manager.md)
