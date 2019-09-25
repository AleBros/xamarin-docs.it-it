---
title: Novell. Forms (scorrimento)
description: Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Inoltre, CollectionView definisce due metodi ScrollTo, che a livello di codice scorrono gli elementi nella visualizzazione.
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2019
ms.openlocfilehash: 303266f44664f7f57aeaf36869a3a06c8eb91870
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "69888644"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Novell. Forms (scorrimento)

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce due [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) metodi, che scorrono gli elementi nella visualizzazione. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione, mentre l'altro scorre l'elemento specificato nella visualizzazione. Entrambi gli overload hanno argomenti aggiuntivi che possono essere specificati per indicare la posizione esatta dell'elemento dopo il completamento dello scorrimento e l'eventuale animazione dello scorrimento.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce un [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento che viene generato quando viene richiamato [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) uno dei metodi. L' [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) oggetto che accompagna l' `ScrollToRequested` evento dispone di molte proprietà, tra `IsAnimated`cui `Index`, `Item`, e `ScrollToPosition`. Queste proprietà vengono impostate dagli argomenti specificati nelle `ScrollTo` chiamate al metodo.

Inoltre, [`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce un `Scrolled` evento generato per indicare che si è verificato lo scorrimento. Il `ItemsViewScrolledEventArgs` numero di proprietà dell'oggetto `Scrolled` che accompagna l'evento è elevato. Per ulteriori informazioni, vedere [rilevamento dello scorrimento](#detect-scrolling).

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce inoltre una `ItemsUpdatingScrollMode` proprietà che rappresenta il comportamento `CollectionView` di scorrimento di quando vengono aggiunti nuovi elementi. Per altre informazioni su questa proprietà, vedere [controllare la posizione di scorrimento quando vengono aggiunti nuovi elementi](#control-scroll-position-when-new-items-are-added).

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Questa funzionalità è nota come blocco, perché gli elementi si bloccano alla posizione quando lo scorrimento viene interrotto. Per altre informazioni, vedere [punti di aggancio](#snap-points).

[`CollectionView`](xref:Xamarin.Forms.CollectionView)consente inoltre di caricare i dati in modo incrementale quando l'utente scorre. Per altre informazioni, vedere [caricare dati in modo incrementale](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Rileva scorrimento

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce un `Scrolled` evento generato per indicare che si è verificato lo scorrimento. Nell'esempio di codice XAML riportato `CollectionView` di seguito viene illustrato un oggetto che `Scrolled` imposta un gestore eventi per l'evento:

```xaml
<CollectionView Scrolled="OnCollectionViewScrolled">
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.Scrolled += OnCollectionViewScrolled;
```

In questo esempio di codice, `OnCollectionViewScrolled` il gestore eventi viene eseguito quando `Scrolled` viene generato l'evento:

```csharp
void OnCollectionViewScrolled(object sender, ItemsViewScrolledEventArgs e)
{
    Debug.WriteLine("HorizontalDelta: " + e.HorizontalDelta);
    Debug.WriteLine("VerticalDelta: " + e.VerticalDelta);
    Debug.WriteLine("HorizontalOffset: " + e.HorizontalOffset);
    Debug.WriteLine("VerticalOffset: " + e.VerticalOffset);
    Debug.WriteLine("FirstVisibleItemIndex: " + e.FirstVisibleItemIndex);
    Debug.WriteLine("CenterItemIndex: " + e.CenterItemIndex);
    Debug.WriteLine("LastVisibleItemIndex: " + e.LastVisibleItemIndex);
}
```

Il `OnCollectionViewScrolled` gestore eventi restituisce i valori `ItemsViewScrolledEventArgs` dell'oggetto che accompagna l'evento.

> [!IMPORTANT]
> L' `Scrolled` evento viene generato per gli scorrimenti avviati dall'utente e per gli scorrimenti a livello di codice.

## <a name="scroll-an-item-at-an-index-into-view"></a>Scorrere un elemento in corrispondenza di un indice nella visualizzazione

Il primo [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) overload del metodo scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione. Dato un [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto denominato `collectionView`, nell'esempio seguente viene illustrato come scorrere l'elemento in corrispondenza dell'indice 12 nella visualizzazione:

```csharp
collectionView.ScrollTo(12);
```

> [!NOTE]
> L' [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento viene generato quando viene [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) richiamato il metodo.

## <a name="scroll-an-item-into-view"></a>Scorrere un elemento nella visualizzazione

Il secondo [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) overload del metodo scorre l'elemento specificato nella visualizzazione. Dato un [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto denominato `collectionView`, nell'esempio seguente viene illustrato come scorrere l'elemento specificato nella visualizzazione:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

> [!NOTE]
> L' [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento viene generato quando viene [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) richiamato il metodo.

## <a name="scroll-bar-visibility"></a>Visibilità della barra di scorrimento

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce `HorizontalScrollBarVisibility` le `VerticalScrollBarVisibility` proprietà e, supportate da proprietà associabili. Queste proprietà ottengono o impostano un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valore di enumerazione che rappresenta quando la barra di scorrimento orizzontale o verticale è visibile. L'enumerazione `ScrollBarVisibility` definisce i membri seguenti:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)indica il comportamento predefinito della barra di scorrimento per la piattaforma e è il valore predefinito per `HorizontalScrollBarVisibility` le `VerticalScrollBarVisibility` proprietà e.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)indica che le barre di scorrimento saranno visibili, anche quando il contenuto si adatterà alla visualizzazione.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)indica che le barre di scorrimento non saranno visibili, anche se il contenuto non rientra nella visualizzazione.

## <a name="control-scroll-position"></a>Posizione di scorrimento del controllo

Quando si scorre un elemento nella visualizzazione, è possibile specificare la posizione esatta dell'elemento dopo il completamento dello scorrimento con l' `position` argomento [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) dei metodi. Questo argomento accetta un [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) membro di enumerazione.

### <a name="makevisible"></a>MakeVisible

Il [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato fino a quando non è visibile nella visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Questo codice di esempio genera lo scorrimento minimo necessario per scorrere l'elemento nella visualizzazione:

[![Screenshot di un elenco di ritrovi verticali con un elemento visualizzato in visualizzazione, in iOS e Android](scrolling-images/scrolltoposition-makevisible.png "Elenco verticale di CollectionView con elemento a scorrimento")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "Elenco verticale di CollectionView con elemento a scorrimento")

> [!NOTE]
> Il [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membro viene usato per impostazione predefinita, se `position` l'argomento non viene specificato quando si `ScrollTo` chiama il metodo.

### <a name="start"></a>Inizia

Il [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato all'inizio della visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Questo esempio di codice comporta lo scorrimento dell'elemento all'inizio della visualizzazione:

[![Screenshot di un elenco di ritrovi verticali con un elemento visualizzato in visualizzazione, in iOS e Android](scrolling-images/scrolltoposition-start.png "Elenco verticale di CollectionView con elemento a scorrimento")](scrolling-images/scrolltoposition-start-large.png#lightbox "Elenco verticale di CollectionView con elemento a scorrimento")

### <a name="center"></a>Center

Il [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato al centro della visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Questo esempio di codice comporta lo scorrimento dell'elemento al centro della visualizzazione:

[![Screenshot di un elenco di ritrovi verticali con un elemento visualizzato in visualizzazione, in iOS e Android](scrolling-images/scrolltoposition-center.png "Elenco verticale di CollectionView con elemento a scorrimento")](scrolling-images/scrolltoposition-center-large.png#lightbox "Elenco verticale di CollectionView con elemento a scorrimento")

### <a name="end"></a>Fine

Il [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato alla fine della visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Questo esempio di codice comporta lo scorrimento dell'elemento alla fine della visualizzazione:

[![Screenshot di un elenco di ritrovi verticali con un elemento visualizzato in visualizzazione, in iOS e Android](scrolling-images/scrolltoposition-end.png "Elenco verticale di CollectionView con elemento a scorrimento")](scrolling-images/scrolltoposition-end-large.png#lightbox "Elenco verticale di CollectionView con elemento a scorrimento")

## <a name="disable-scroll-animation"></a>Disabilita animazione Scroll

Quando si scorre un elemento nella visualizzazione, viene visualizzata un'animazione di scorrimento. Questa animazione può tuttavia essere disabilitata impostando l' `animate` argomento `ScrollTo` del metodo su `false`:

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position-when-new-items-are-added"></a>Posizione di scorrimento del controllo quando vengono aggiunti nuovi elementi

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce una `ItemsUpdatingScrollMode` proprietà, supportata da una proprietà associabile. Questa proprietà Ottiene o imposta un `ItemsUpdatingScrollMode` valore di enumerazione che rappresenta il comportamento `CollectionView` di scorrimento di quando vengono aggiunti nuovi elementi. L'enumerazione `ItemsUpdatingScrollMode` definisce i membri seguenti:

- `KeepItemsInView`regola l'offset di scorrimento per tenere visualizzato il primo elemento visibile quando vengono aggiunti nuovi elementi.
- `KeepScrollOffset`mantiene l'offset di scorrimento relativo all'inizio dell'elenco quando vengono aggiunti nuovi elementi.
- `KeepLastItemInView`regola l'offset di scorrimento per tenere visibile l'ultimo elemento quando vengono aggiunti nuovi elementi.

Il valore predefinito della `ItemsUpdatingScrollMode` proprietà è. `KeepItemsInView` Pertanto, quando vengono aggiunti nuovi elementi a un [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto, il primo elemento visibile nell'elenco rimarrà visualizzato. Per assicurarsi che gli elementi appena aggiunti siano sempre visibili nella parte inferiore dell'elenco, la `ItemsUpdatingScrollMode` proprietà deve essere impostata su `KeepLastItemInView`:

```xaml
<CollectionView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="snap-points"></a>Punti di aggancio

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Questa funzionalità è nota come blocco, perché gli elementi si bloccano alla posizione quando lo scorrimento viene interrotto ed è controllato dalle proprietà [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) seguenti della classe:

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), di tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), specifica il comportamento dei punti di blocco durante lo scorrimento.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), di tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), specifica il modo in cui i punti di allineamento sono allineati con gli elementi.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> Quando si verifica il blocco, si verificherà nella direzione che produce il minor numero di movimento.

### <a name="snap-points-type"></a>Tipo di punti di aggancio

L' [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) enumerazione definisce i membri seguenti:

- `None`indica che lo scorrimento non si blocca sugli elementi.
- `Mandatory`indica che il contenuto viene sempre agganciato al punto di aggancio più vicino alla posizione in cui lo scorrimento viene naturalmente interrotto, lungo la direzione di inerzia.
- `MandatorySingle`indica lo stesso comportamento di `Mandatory`, ma scorre un solo elemento alla volta.

Per impostazione predefinita, [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) la proprietà è impostata `SnapPointsType.None`su, che garantisce che lo scorrimento non riagganci gli elementi, come illustrato nelle schermate seguenti:

[![Screenshot di un elenco di ritrovi verticali senza punti di aggancio, in iOS e Android](scrolling-images/snappoints-none.png "Elenco verticale di CollectionView senza punti di aggancio")](scrolling-images/snappoints-none-large.png#lightbox "Elenco verticale di CollectionView senza punti di aggancio")

### <a name="snap-points-alignment"></a>Allineamento punti di aggancio

L' [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) enumerazione definisce `Start`i `Center`membri, `End` e.

> [!IMPORTANT]
> Il valore della [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) proprietà viene rispettato solo quando la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) proprietà è impostata su `Mandatory`, o `MandatorySingle`.

#### <a name="start"></a>Inizia

Il `SnapPointsAlignment.Start` membro indica che i punti di allineamento sono allineati al bordo di elementi iniziali.

Per impostazione predefinita, [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) la proprietà è impostata `SnapPointsAlignment.Start`su. Per completezza, tuttavia, nell'esempio di codice XAML riportato di seguito viene illustrato come impostare questo membro di enumerazione:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Start">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Quando un utente scorre il dito per avviare uno scorrimento, l'elemento superiore verrà allineato alla parte superiore della visualizzazione:

[![Screenshot di un elenco verticale di CollectionView con punti di aggancio iniziali, in iOS e Android](scrolling-images/snappoints-start.png "Elenco verticale di CollectionView con punti di aggancio iniziali")](scrolling-images/snappoints-start-large.png#lightbox "Elenco verticale di CollectionView con punti di aggancio iniziali")

#### <a name="center"></a>Center

Il `SnapPointsAlignment.Center` membro indica che i punti di allineamento sono allineati al centro di elementi. Nell'esempio di codice XAML riportato di seguito viene illustrato come impostare questo membro di enumerazione:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Center">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Quando un utente scorre il dito per avviare uno scorrimento, l'elemento superiore verrà allineato al centro nella parte superiore della visualizzazione:

[![Screenshot di un elenco di ritrovi verticali con punti di allineamento al centro, in iOS e Android](scrolling-images/snappoints-center.png "Elenco verticale di CollectionView con punti di allineamento al centro")](scrolling-images/snappoints-center-large.png#lightbox "Elenco verticale di CollectionView con punti di allineamento al centro")

#### <a name="end"></a>Fine

Il `SnapPointsAlignment.End` membro indica che i punti di allineamento sono allineati al bordo finale degli elementi. Nell'esempio di codice XAML riportato di seguito viene illustrato come impostare questo membro di enumerazione:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="End">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Quando un utente scorre il dito per avviare uno scorrimento, l'elemento inferiore verrà allineato alla parte inferiore della visualizzazione:

[![Screenshot di un elenco di ritrovi verticali con punti di aggancio finali, in iOS e Android](scrolling-images/snappoints-end.png "Elenco verticale di CollectionView con punti di aggancio finali")](scrolling-images/snappoints-end-large.png#lightbox "Elenco verticale di CollectionView con punti di aggancio finali")

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
