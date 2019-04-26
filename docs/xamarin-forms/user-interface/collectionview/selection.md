---
title: Impostare la modalità di selezione di xamarin. Forms CollectionView
description: Per impostazione predefinita, selezione di visualizzazione di raccolta è disabilitato. Tuttavia, può essere abilitata selezione singola e multiple.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/18/2019
ms.openlocfilehash: 441afb9348a85de61d35574bb9121c7de713a897
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61367780"
---
# <a name="set-collectionview-selection-mode"></a>Impostare la modalità di selezione di visualizzazione di raccolta

![Anteprima](~/media/shared/preview.png)

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> Il `CollectionView` è attualmente un'anteprima e non dispone di alcune delle proprie funzionalità pianificato. Inoltre, l'API può cambiare quando viene completata l'implementazione.

`CollectionView` definisce le proprietà seguenti che controllano la selezione di elementi:

- `SelectionMode`, di tipo `SelectionMode`, la modalità di selezione.
- `SelectedItem`, di tipo `object`, l'elemento selezionato nell'elenco. Questa proprietà ha un `null` valore quando è selezionato alcun elemento.
- `SelectionChangedCommand`, di tipo `ICommand`, che viene eseguito quando viene modificato l'elemento selezionato.
- `SelectionChangedCommandParameter`, di tipo `object`, ovvero il parametro passato al `SelectionChangedCommand`.

Tutte queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che le proprietà possono essere destinazioni di associazioni dati.

Per impostazione predefinita, `CollectionView` la selezione è disabilitata. Tuttavia, questo comportamento può essere modificato impostando il `SelectionMode` valore della proprietà da uno del `SelectionMode` membri dell'enumerazione:

- `None` : indica che non è possibile selezionare elementi. Rappresenta il valore predefinito.
- `Single` : indica che si possa selezionare un singolo elemento, con l'elemento selezionato viene evidenziato.
- `Multiple` : indica che è possibile selezionare più elementi, con gli elementi selezionati in corso evidenziati.

`CollectionView` definisce un `SelectionChanged` evento generato quando il `SelectedItem` le modifiche alle proprietà, a causa di selezione di un elemento dall'elenco, oppure quando un'applicazione imposta la proprietà utente. Il `SelectionChangedEventArgs` che accompagna il `SelectionChanged` evento ha due proprietà, entrambi di tipo `IReadOnlyList<object>`:

- `PreviousSelection` : l'elenco di elementi che sono stati selezionati, prima della modifica della selezione.
- `CurrentSelection` : l'elenco degli elementi selezionati, dopo la modifica della selezione.

## <a name="single-selection"></a>Selezione singola

Quando la `SelectionMode` è impostata su `Single`, un singolo elemento nel `CollectionView` selezionabile. Quando viene selezionato un elemento, il `SelectedItem` proprietà verrà impostata sul valore dell'elemento selezionato. Quando questa proprietà viene modificata, il `SelectionChangedCommand` viene eseguita (con il valore della `SelectionChangedCommandParameter` passati al `ICommand`) e il `SelectionChanged` viene generato l'evento.

L'esempio XAML seguente viene illustrato un `CollectionView` che può rispondere a elemento singolo selezione:

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

In questo esempio di codice, il `OnCollectionViewSelectionChanged` gestore eventi viene eseguito quando il `SelectionChanged` viene generato l'evento, con il gestore dell'evento recuperare l'elemento selezionato in precedenza e l'elemento attualmente selezionato:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (previousSelectedItems.FirstOrDefault() as Monkey)?.Name;
    string current = (currentSelectedItems.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> Il `SelectionChanged` evento può essere attivato da modifiche che si verificano in seguito alla modifica di `SelectionMode` proprietà.

Le schermate seguenti illustrano la selezione di elementi singolo in un `CollectionView`:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con selezione singola, in iOS e Android](selection-images/single-selection.png "CollectionView elenco verticale con selezione singola")](selection-images/single-selection-large.png#lightbox "CollectionView elenco verticale con singola selezione")

## <a name="pre-selection"></a>Pre-selezione

Quando il `SelectionMode` è impostata su `Single`, un singolo elemento nel `CollectionView` possono essere preselezionata impostando il `SelectedItem` proprietà all'elemento. L'esempio XAML seguente viene illustrato un `CollectionView` che preseleziona un singolo elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey, Mode=TwoWay}">
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
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey", BindingMode.TwoWay);
```

Il `SelectedItem` esegue l'associazione dati di proprietà per il `SelectedMonkey` proprietà del modello di visualizzazione connessa, che è di tipo `Monkey`. Oggetto `TwoWay` viene utilizzata l'associazione in modo che se l'utente modifica l'elemento selezionato, il valore della `SelectedMonkey` verrà impostata al `Monkey` oggetto. Il `SelectedMonkey` proprietà definita nel `MonkeysViewModel` classe e viene impostato per l'elemento della quarto il `Monkeys` raccolta:

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

Pertanto, quando il `CollectionView` viene visualizzata, è già selezionato il quarto elemento dell'elenco:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con selezione singola non definitiva, in iOS e Android](selection-images/single-pre-selection.png "CollectionView elenco verticale con selezione singola non definitiva")](selection-images/single-pre-selection-large.png#lightbox "elenco verticale di visualizzazione di raccolta con singola pre-selezione")

## <a name="change-selected-item-color"></a>Modificare il colore di elemento selezionato

`CollectionView` è un `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) che può essere utilizzato per avviare una modifica all'elemento selezionato nel `CollectionView`. Caso d'uso di un comune per questo `VisualState` consiste nel modificare il colore di sfondo dell'elemento selezionato, che è illustrata nell'esempio XAML seguente:

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

In questo esempio, il [ `Style.TargetType` ](xref:Xamarin.Forms.Style.TargetType) valore della proprietà è impostato su `Grid` perché l'elemento radice del `ItemTemplate` è un [ `Grid` ](xref:Xamarin.Forms.Grid). Il `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) specifica che quando un elemento la `CollectionView` è selezionata, il [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) dell'elemento verrà impostato su `LightSkyBlue`:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con un colore personalizzato selezione singola, in iOS e Android](selection-images/single-selection-color.png "CollectionView elenco verticale con un colore personalizzato selezione singola") ] (selection-images/single-selection-color-large.png#lightbox " Visualizzazione di raccolta elenco verticale con un colore personalizzato selezione singola")

Per altre informazioni sugli stati visual, vedere [xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Disabilitare la selezione

`CollectionView` selezione è disabilitata per impostazione predefinita. Tuttavia, se un `CollectionView` è abilitata, selezione, può essere disabilitata impostando la `SelectionMode` proprietà `None`:

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

Quando la `SelectionMode` è impostata su `None`, gli elementi nel `CollectionView` non può essere selezionato, il `SelectedItem` proprietà rimarrà `null`e il `SelectionChanged` evento non verrà generato.

> [!NOTE]
> Quando è stato selezionato un elemento e il `SelectionMode` proprietà viene modificata da `Single` al `None`, il `SelectedItem` verrà impostata su `null` e la `SelectionChanged` evento verrà generato con un oggetto vuoto `CurrentSelection` proprietà.

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di raccolta (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
