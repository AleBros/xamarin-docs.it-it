---
title: Selezione di xamarin. Forms CollectionView
description: Per impostazione predefinita, selezione di visualizzazione di raccolta è disabilitato. Tuttavia, può essere abilitata selezione singola e multiple.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: dc01cf6bea9fe614cbfb53dcc4417ffb0e602c6f
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512752"
---
# <a name="xamarinforms-collectionview-selection"></a>Selezione di xamarin. Forms CollectionView

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce le proprietà seguenti che controllano la selezione di elementi:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), di tipo [ `SelectionMode` ](xref:Xamarin.Forms.SelectionMode), la modalità di selezione.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), di tipo `object`, l'elemento selezionato nell'elenco. Questa proprietà è una modalità di associazione predefinita `TwoWay`e ha un `null` valore quando è selezionato alcun elemento.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), di tipo `IList<object>`, gli elementi selezionati nell'elenco. Questa proprietà è una modalità di associazione predefinita `OneWay`e ha un `null` valore quando non sono selezionati elementi.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), di tipo `ICommand`, che viene eseguito quando viene modificato l'elemento selezionato.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), di tipo `object`, ovvero il parametro passato al `SelectionChangedCommand`.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che tali proprietà possono essere destinazioni di data binding.

Per impostazione predefinita [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) la selezione è disabilitata. Tuttavia, questo comportamento può essere modificato impostando il [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) valore della proprietà da uno del [ `SelectionMode` ](xref:Xamarin.Forms.SelectionMode) membri dell'enumerazione:

- `None` : indica che non è possibile selezionare elementi. Rappresenta il valore predefinito.
- `Single` : indica che si possa selezionare un singolo elemento, con l'elemento selezionato viene evidenziato.
- `Multiple` : indica che è possibile selezionare più elementi, con gli elementi selezionati in corso evidenziati.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce un [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento generato quando il [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) le modifiche alle proprietà, a causa di selezione di un elemento dall'elenco, oppure quando un'applicazione imposta la proprietà utente. Inoltre, questo evento viene generato quando il [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) le modifiche alle proprietà. Il [ `SelectionChangedEventArgs` ](xref:Xamarin.Forms.SelectionChangedEventArgs) oggetti che accompagna il `SelectionChanged` evento ha due proprietà, entrambi di tipo `IReadOnlyList<object>`:

- `PreviousSelection` : l'elenco di elementi che sono stati selezionati, prima della modifica della selezione.
- `CurrentSelection` : l'elenco degli elementi selezionati, dopo la modifica della selezione.

## <a name="single-selection"></a>Selezione singola

Quando la [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) è impostata su `Single`, un singolo elemento nel [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) selezionabile. Quando viene selezionato un elemento, il [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà verrà impostata sul valore dell'elemento selezionato. Quando questa proprietà viene modificata, il [ `SelectionChangedCommand` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) viene eseguita (con il valore della [ `SelectionChangedCommandParameter` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) passati al `ICommand`) e il [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) viene generato l'evento.

L'esempio XAML seguente viene illustrato un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) che può rispondere a elemento singolo selezione:

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

In questo esempio di codice, il `OnCollectionViewSelectionChanged` gestore eventi viene eseguito quando il [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) viene generato l'evento, con il gestore dell'evento recuperare l'elemento selezionato in precedenza e l'elemento attualmente selezionato:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> Il [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento può essere attivato da modifiche che si verificano come risultato della modifica il [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà.

Le schermate seguenti illustrano la selezione di elementi singolo in un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView):

[![Screenshot di un elenco verticale di visualizzazione di raccolta con selezione singola, in iOS e Android](selection-images/single-selection.png "CollectionView elenco verticale con selezione singola")](selection-images/single-selection-large.png#lightbox "CollectionView elenco verticale con singola selezione")

## <a name="multiple-selection"></a>Selezione multipla

Quando il [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) è impostata su `Multiple`, più elementi all'interno di [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) selezionabile. Quando sono selezionati elementi, il [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) verrà impostata per gli elementi selezionati. Quando questa proprietà viene modificata, il [ `SelectionChangedCommand` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) viene eseguita (con il valore della [ `SelectionChangedCommandParameter` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) passati al `ICommand`) e il [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) viene generato l'evento.

L'esempio XAML seguente viene illustrato un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) che può rispondere alla selezione di elementi più:

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

In questo esempio di codice, il `OnCollectionViewSelectionChanged` gestore eventi viene eseguito quando il [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) viene generato l'evento, con il gestore dell'evento recuperare gli elementi selezionati in precedenza e gli elementi selezionati correnti:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> Il [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento può essere attivato da modifiche che si verificano come risultato della modifica il [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà.

Le schermate seguenti illustrano la selezione di più elementi in un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView):

[![Screenshot di un elenco verticale di visualizzazione di raccolta con selezione multipla, in iOS e Android](selection-images/multiple-selection.png "CollectionView elenco verticale con selezione multipla")](selection-images/multiple-selection-large.png#lightbox "CollectionView elenco verticale con selezione multipla")

## <a name="single-pre-selection"></a>Pre-selezione di singola

Quando il [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) è impostata su `Single`, un singolo elemento nel [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) possono essere pre-selezionato impostando il [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà dell'elemento. L'esempio XAML seguente viene illustrato un `CollectionView` che preseleziona un singolo elemento:

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
> Il [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà dispone di una modalità di associazione predefinito di `TwoWay`.

Il [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) esegue l'associazione dati di proprietà per il `SelectedMonkey` proprietà del modello di visualizzazione connessa, che è di tipo `Monkey`. Per impostazione predefinita, un `TwoWay` viene utilizzata l'associazione in modo che se l'utente modifica l'elemento selezionato, il valore della `SelectedMonkey` verrà impostata al `Monkey` oggetto. Il `SelectedMonkey` proprietà definita nel `MonkeysViewModel` classe e viene impostato per l'elemento della quarto il `Monkeys` raccolta:

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

Pertanto, quando la [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) viene visualizzata, è già selezionato il quarto elemento dell'elenco:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con selezione singola non definitiva, in iOS e Android](selection-images/single-pre-selection.png "CollectionView elenco verticale con selezione singola non definitiva")](selection-images/single-pre-selection-large.png#lightbox "elenco verticale di visualizzazione di raccolta con singola pre-selezione")

## <a name="multiple-pre-selection"></a>Pre-selezione multipla

Quando il [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) è impostata su `Multiple`, più elementi all'interno di [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) possibile pre-selezionata. L'esempio XAML seguente viene illustrato un `CollectionView` che consentirà di pre-selezionare più elementi:

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
> Il [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) proprietà dispone di una modalità di associazione predefinito di `OneWay`.

Il [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) esegue l'associazione dati di proprietà per il `SelectedMonkeys` proprietà del modello di visualizzazione connessa, che è di tipo `ObservableCollection<object>`. Il `SelectedMonkeys` proprietà definita nel `MonkeysViewModel` classe e viene impostato per il secondo, quarto e quinto gli elementi nel `Monkeys` raccolta:

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

Pertanto, quando la [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) viene visualizzato, il secondo, la quarta e quinto elementi nell'elenco sono già selezionati:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con selezione multipla non definitiva, in iOS e Android](selection-images/multiple-pre-selection.png "CollectionView elenco verticale con selezione multipla non definitiva")](selection-images/multiple-pre-selection-large.png#lightbox "CollectionView verticale elenco con preselezione più")

## <a name="clearing-selections"></a>Cancellare le selezioni

Il [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) e [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) proprietà possono essere cancellate impostando o gli oggetti siano associati a, a `null`.

## <a name="change-selected-item-color"></a>Modificare il colore di elemento selezionato

[`CollectionView`](xref:Xamarin.Forms.CollectionView) è un `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) che può essere utilizzato per avviare una modifica all'elemento selezionato nel `CollectionView`. Caso d'uso di un comune per questo `VisualState` consiste nel modificare il colore di sfondo dell'elemento selezionato, che è illustrata nell'esempio XAML seguente:

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
> Il [ `Style` ](xref:Xamarin.Forms.Style) che contiene il `Selected` `VisualState` deve avere un [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) valore della proprietà che è il tipo dell'elemento radice del [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), che è impostato come il `ItemTemplate` valore della proprietà.

In questo esempio, il [ `Style.TargetType` ](xref:Xamarin.Forms.Style.TargetType) valore della proprietà è impostato su `Grid` perché l'elemento radice del [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate) è una [ `Grid` ](xref:Xamarin.Forms.Grid). Il `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) specifica che quando un elemento la [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) è selezionata, il [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) dell'elemento verrà impostato su `LightSkyBlue`:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con un colore personalizzato selezione singola, in iOS e Android](selection-images/single-selection-color.png "CollectionView elenco verticale con un colore personalizzato selezione singola") ] (selection-images/single-selection-color-large.png#lightbox " Visualizzazione di raccolta elenco verticale con un colore personalizzato selezione singola")

Per altre informazioni sugli stati visual, vedere [xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Disabilitare la selezione

[`CollectionView`](xref:Xamarin.Forms.CollectionView) selezione è disabilitata per impostazione predefinita. Tuttavia, se un `CollectionView` è abilitata, selezione, può essere disabilitata impostando la [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà `None`:

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

Quando la [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) è impostata su `None`, gli elementi nel [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) non può essere selezionato, il [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) proprietà verrà rimangono `null`e il [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento non verrà generato.

> [!NOTE]
> Quando è stato selezionato un elemento e il [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) proprietà viene modificata da `Single` a `None`, la [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) verrà impostata su `null` e [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento verrà generato con un oggetto vuoto `CurrentSelection` proprietà.

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di raccolta (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
