---
title: Selezione di Novell. Forms CollectionView
description: Per impostazione predefinita, la selezione di CollectionView è disabilitata. Tuttavia, è possibile abilitare una selezione singola o multipla.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: a4cc237ef738edeccf66f1a91a010e4831c1c72f
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292161"
---
# <a name="xamarinforms-collectionview-selection"></a>Selezione di Novell. Forms CollectionView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce le proprietà seguenti che controllano la selezione degli elementi:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), di tipo [`SelectionMode`](xref:Xamarin.Forms.SelectionMode), la modalità di selezione.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), di tipo `object`, l'elemento selezionato nell'elenco. Questa proprietà ha una modalità di associazione predefinita di `TwoWay`e ha un valore `null` se non è selezionato alcun elemento.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), di tipo `IList<object>`, gli elementi selezionati nell'elenco. Questa proprietà ha una modalità di associazione predefinita di `OneWay`e ha un valore `null` se non è selezionato alcun elemento.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), di tipo `ICommand`, che viene eseguito quando viene modificato l'elemento selezionato.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), di tipo `object`, che corrisponde al parametro passato al `SelectionChangedCommand`.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che tali proprietà possono essere destinazioni di data binding.

Per impostazione predefinita, [`CollectionView`](xref:Xamarin.Forms.CollectionView) selezione è disabilitata. Tuttavia, questo comportamento può essere modificato impostando il valore della proprietà [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) su uno dei membri dell'enumerazione [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) :

- `None`: indica che non è possibile selezionare gli elementi. Si tratta del valore predefinito.
- `Single`: indica che è possibile selezionare un singolo elemento, con l'elemento selezionato evidenziato.
- `Multiple`: indica che è possibile selezionare più elementi con gli elementi selezionati evidenziati.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce un evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) generato quando la proprietà [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) viene modificata, a causa dell'utente che seleziona un elemento dall'elenco o quando un'applicazione imposta la proprietà. Inoltre, questo evento viene generato quando cambia la proprietà [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) . L'oggetto [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) che accompagna l'evento `SelectionChanged` dispone di due proprietà, entrambe di tipo `IReadOnlyList<object>`:

- `PreviousSelection`: l'elenco di elementi selezionati prima della modifica della selezione.
- `CurrentSelection`: l'elenco di elementi selezionati, dopo la modifica della selezione.

Inoltre, [`CollectionView`](xref:Xamarin.Forms.CollectionView) dispone di un metodo `UpdateSelectedItems` che aggiorna la proprietà [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) con un elenco di elementi selezionati, mentre genera solo una singola notifica di modifica.

## <a name="single-selection"></a>Selezione singola

Quando la proprietà [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) è impostata su `Single`, è possibile selezionare un singolo elemento nel [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Quando si seleziona un elemento, la proprietà [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) verrà impostata sul valore dell'elemento selezionato. Quando questa proprietà viene modificata, l' [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) viene eseguita (con il valore della [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) passata al `ICommand`) e viene generato l'evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) .

Nell'esempio di codice XAML riportato di seguito viene illustrato un [`CollectionView`](xref:Xamarin.Forms.CollectionView) in grado di rispondere alla selezione di un singolo elemento:

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

In questo esempio di codice, il gestore dell'evento `OnCollectionViewSelectionChanged` viene eseguito quando viene generato l'evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) , con il gestore eventi che recupera l'elemento selezionato in precedenza e l'elemento selezionato corrente:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> L'evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) può essere generato da modifiche che si verificano in seguito alla modifica della proprietà [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) .

Gli screenshot seguenti mostrano la selezione di un singolo elemento in un [`CollectionView`](xref:Xamarin.Forms.CollectionView):

[![Screenshot di un elenco di ritrovi verticali con selezione singola, in iOS e Android](selection-images/single-selection.png "Elenco verticale di CollectionView con selezione singola")](selection-images/single-selection-large.png#lightbox "Elenco verticale di CollectionView con selezione singola")

## <a name="multiple-selection"></a>Selezione multipla

Quando la proprietà [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) è impostata su `Multiple`, è possibile selezionare più elementi nel [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Quando si selezionano elementi, la proprietà [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) verrà impostata sugli elementi selezionati. Quando questa proprietà viene modificata, l' [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) viene eseguita (con il valore della [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) passata al `ICommand`) e viene generato l'evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) .

Nell'esempio di codice XAML riportato di seguito viene illustrato un [`CollectionView`](xref:Xamarin.Forms.CollectionView) in grado di rispondere a più selezioni di elementi:

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

In questo esempio di codice, il gestore dell'evento `OnCollectionViewSelectionChanged` viene eseguito quando viene generato l'evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) , con il gestore eventi che recupera gli elementi selezionati in precedenza e gli elementi selezionati correnti:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> L'evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) può essere generato da modifiche che si verificano in seguito alla modifica della proprietà [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) .

Gli screenshot seguenti mostrano la selezione di più elementi in un [`CollectionView`](xref:Xamarin.Forms.CollectionView):

[![Screenshot di un elenco di ritrovi verticali con selezione multipla, in iOS e Android](selection-images/multiple-selection.png "Elenco verticale di CollectionView con selezione multipla")](selection-images/multiple-selection-large.png#lightbox "Elenco verticale di CollectionView con selezione multipla")

## <a name="single-pre-selection"></a>Selezione singola

Quando la proprietà [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) è impostata su `Single`, è possibile pre-selezionare un singolo elemento nel [`CollectionView`](xref:Xamarin.Forms.CollectionView) impostando la proprietà [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) sull'elemento. Nell'esempio di codice XAML seguente viene illustrato un `CollectionView` che consente di preselezionare un singolo elemento:

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
> Per la proprietà [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) è presente una modalità di associazione predefinita di `TwoWay`.

I dati della proprietà [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) vengono associati alla proprietà `SelectedMonkey` del modello di visualizzazione connesso, che è di tipo `Monkey`. Per impostazione predefinita, viene utilizzata un'associazione `TwoWay` in modo che, se l'utente modifica l'elemento selezionato, il valore della proprietà `SelectedMonkey` verrà impostato sull'oggetto `Monkey` selezionato. La proprietà `SelectedMonkey` è definita nella classe `MonkeysViewModel` e viene impostata sul quarto elemento della raccolta `Monkeys`:

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

Pertanto, quando viene visualizzata la [`CollectionView`](xref:Xamarin.Forms.CollectionView) , il quarto elemento dell'elenco è preselezionato:

[![Screenshot di un elenco di ritrovi verticali con una singola pre-selezione, in iOS e Android](selection-images/single-pre-selection.png "Elenco verticale di CollectionView con selezione preliminare singola")](selection-images/single-pre-selection-large.png#lightbox "Elenco verticale di CollectionView con selezione preliminare singola")

## <a name="multiple-pre-selection"></a>Pre-selezione multipla

Quando la proprietà [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) è impostata su `Multiple`, è possibile pre-selezionare più elementi nel [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Nell'esempio di codice XAML riportato di seguito viene illustrato un `CollectionView` che consentirà la pre-selezione di più elementi:

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
> Per la proprietà [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) è presente una modalità di associazione predefinita di `OneWay`.

I dati della proprietà [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) vengono associati alla proprietà `SelectedMonkeys` del modello di visualizzazione connesso, che è di tipo `ObservableCollection<object>`. La proprietà `SelectedMonkeys` è definita nella classe `MonkeysViewModel` e viene impostata sul secondo, quarto e quinto elemento della raccolta `Monkeys`:

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

Pertanto, quando viene visualizzata la [`CollectionView`](xref:Xamarin.Forms.CollectionView) , il secondo, il quarto e il quinto elemento dell'elenco sono pre-selezionati:

[![Screenshot di un elenco di ritrovi verticali con più selezioni preliminari in iOS e Android](selection-images/multiple-pre-selection.png "Elenco verticale di CollectionView con più pre-selezione")](selection-images/multiple-pre-selection-large.png#lightbox "Elenco verticale di CollectionView con più pre-selezione")

## <a name="clear-selections"></a>Cancella selezioni

Le proprietà [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) e [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) possono essere cancellate impostando o gli oggetti a cui vengono associati, per `null`.

## <a name="change-selected-item-color"></a>Modifica colore elemento selezionato

[`CollectionView`](xref:Xamarin.Forms.CollectionView) dispone di un [`VisualState`](xref:Xamarin.Forms.VisualState) `Selected` che può essere utilizzato per avviare una modifica visiva all'elemento selezionato nel `CollectionView`. Un caso d'uso comune per questa `VisualState` consiste nel modificare il colore di sfondo dell'elemento selezionato, come illustrato nell'esempio di codice XAML seguente:

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
> Il [`Style`](xref:Xamarin.Forms.Style) che contiene la `VisualState` di `Selected` deve avere un valore della proprietà [`TargetType`](xref:Xamarin.Forms.Style.TargetType) che è il tipo dell'elemento radice del [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), che è impostato come valore della proprietà `ItemTemplate`.

In questo esempio il valore della proprietà [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) è impostato su `Grid` perché l'elemento radice del [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) è un [`Grid`](xref:Xamarin.Forms.Grid). Il `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) specifica che, quando viene selezionato un elemento del [`CollectionView`](xref:Xamarin.Forms.CollectionView) , il [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) dell'elemento verrà impostato su `LightSkyBlue`:

[![Screenshot di un elenco di ritrovi verticali con un colore di selezione singola personalizzato, in iOS e Android](selection-images/single-selection-color.png "Elenco verticale di CollectionView con un colore di selezione singola personalizzato")](selection-images/single-selection-color-large.png#lightbox "Elenco verticale di CollectionView con un colore di selezione singola personalizzato")

Per ulteriori informazioni sugli stati visivi, vedere [Novell. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Disabilita selezione

per impostazione predefinita, [`CollectionView`](xref:Xamarin.Forms.CollectionView) selezione è disabilitata. Tuttavia, se la selezione di un `CollectionView` è abilitata, è possibile disabilitarla impostando la proprietà [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) su `None`:

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

Quando la proprietà [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) è impostata su `None`, non è possibile selezionare gli elementi del [`CollectionView`](xref:Xamarin.Forms.CollectionView) , la proprietà [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) resterà `null`e l'evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) non verrà generato.

> [!NOTE]
> Quando viene selezionato un elemento e la proprietà [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) viene modificata da `Single` a `None`, la proprietà [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) verrà impostata su `null` e l'evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) verrà generato con una proprietà `CurrentSelection` vuota.

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Gestore dello stato di visualizzazione di Novell. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
