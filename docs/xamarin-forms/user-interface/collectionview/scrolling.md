---
title: Novell. Forms (scorrimento)
description: Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Inoltre, CollectionView definisce due metodi ScrollTo, che a livello di codice scorrono gli elementi nella visualizzazione.
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 89bbe402f056b875a7dadd96527364847ad470e8
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738931"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Novell. Forms (scorrimento)

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce due [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) metodi, che scorrono gli elementi nella visualizzazione. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione, mentre l'altro scorre l'elemento specificato nella visualizzazione. Entrambi gli overload hanno argomenti aggiuntivi che possono essere specificati per indicare la posizione esatta dell'elemento dopo il completamento dello scorrimento e l'eventuale animazione dello scorrimento.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce un [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento che viene generato quando viene richiamato [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) uno dei metodi. L' [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) oggetto che accompagna l' `ScrollToRequested` evento dispone di molte proprietà, tra `IsAnimated`cui `Index`, `Item`, e `ScrollToPosition`. Queste proprietà vengono impostate dagli argomenti specificati nelle `ScrollTo` chiamate al metodo.

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Questa funzionalità è nota come blocco, perché gli elementi si bloccano alla posizione quando lo scorrimento viene interrotto. Per altre informazioni, vedere [punti](#snap-points)di aggancio.

## <a name="scroll-an-item-at-an-index-into-view"></a>Scorrere un elemento in corrispondenza di un indice nella visualizzazione

Il primo [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) overload del metodo scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione. Dato un [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto denominato `collectionView`, nell'esempio seguente viene illustrato come scorrere l'elemento in corrispondenza dell'indice 12 nella visualizzazione:

```csharp
collectionView.ScrollTo(12);
```

## <a name="scroll-an-item-into-view"></a>Scorrere un elemento nella visualizzazione

Il secondo [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) overload del metodo scorre l'elemento specificato nella visualizzazione. Dato un [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto denominato `collectionView`, nell'esempio seguente viene illustrato come scorrere l'elemento specificato nella visualizzazione:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

## <a name="control-scroll-position"></a>Posizione di scorrimento del controllo

Quando si scorre un elemento nella visualizzazione, è possibile specificare la posizione esatta dell'elemento dopo il completamento dello scorrimento con l' `position` argomento [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) dei metodi. Questo argomento accetta un [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) membro di enumerazione.

### <a name="makevisible"></a>MakeVisible

Il [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato fino a quando non è visibile nella visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Questo codice di esempio genera lo scorrimento minimo necessario per scorrere l'elemento nella visualizzazione:

[ ![Screenshot di un elenco di ritrovi verticali con un elemento visualizzato, in iOS e Android](scrolling-images/scrolltoposition-makevisible.png "CollectionView elenco verticale con elemento a scorrimento") ] (scrolling-images/scrolltoposition-makevisible-large.png#lightbox "Elenco verticale di CollectionView con elemento a scorrimento")

> [!NOTE]
> Il [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membro viene usato per impostazione predefinita, se `position` l'argomento non viene specificato quando si `ScrollTo` chiama il metodo.

### <a name="start"></a>Inizia

Il [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato all'inizio della visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Questo esempio di codice comporta lo scorrimento dell'elemento all'inizio della visualizzazione:

[ ![Screenshot di un elenco di ritrovi verticali con un elemento visualizzato, in iOS e Android](scrolling-images/scrolltoposition-start.png "CollectionView elenco verticale con elemento a scorrimento") ] (scrolling-images/scrolltoposition-start-large.png#lightbox "Elenco verticale di CollectionView con elemento a scorrimento")

### <a name="center"></a>Center

Il [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato al centro della visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Questo esempio di codice comporta lo scorrimento dell'elemento al centro della visualizzazione:

[ ![Screenshot di un elenco di ritrovi verticali con un elemento visualizzato, in iOS e Android](scrolling-images/scrolltoposition-center.png "CollectionView elenco verticale con elemento a scorrimento") ] (scrolling-images/scrolltoposition-center-large.png#lightbox "Elenco verticale di CollectionView con elemento a scorrimento")

### <a name="end"></a>Fine

Il [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato alla fine della visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Questo esempio di codice comporta lo scorrimento dell'elemento alla fine della visualizzazione:

[ ![Screenshot di un elenco di ritrovi verticali con un elemento visualizzato, in iOS e Android](scrolling-images/scrolltoposition-end.png "CollectionView elenco verticale con elemento a scorrimento") ] (scrolling-images/scrolltoposition-end-large.png#lightbox "Elenco verticale di CollectionView con elemento a scorrimento")

## <a name="disable-scroll-animation"></a>Disabilita animazione Scroll

Quando si scorre un elemento nella visualizzazione, viene visualizzata un'animazione di scorrimento. Questa animazione può tuttavia essere disabilitata impostando l' `animate` argomento `ScrollTo` del metodo su `false`:

```csharp
collectionView.ScrollTo(monkey, animate: false);
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

[ ![Screenshot di un elenco di ritrovi verticali senza punti di aggancio, in iOS e Android](scrolling-images/snappoints-none.png "CollectionView elenco verticale senza punti") di aggancio] (scrolling-images/snappoints-none-large.png#lightbox "Elenco verticale di CollectionView senza punti") di aggancio

### <a name="snap-points-alignment"></a>Allineamento punti di aggancio

L' [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) enumerazione definisce `Start`i `Center`membri, `End` e.

> [!IMPORTANT]
> Il valore della [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) proprietà viene rispettato solo quando la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) proprietà è impostata su `Mandatory`, o `MandatorySingle`.

#### <a name="start"></a>Start

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

Il codice C# equivalente è:

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

[ ![Screenshot di un elenco di riepilogo verticale con punti di aggancio iniziali, in iOS e Android](scrolling-images/snappoints-start.png "CollectionView elenco verticale con punti di aggancio iniziali") ] (scrolling-images/snappoints-start-large.png#lightbox "Elenco verticale di CollectionView con punti di aggancio iniziali")

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

Il codice C# equivalente è:

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

[ ![Screenshot di un elenco verticale di CollectionView con punti di allineamento al centro, in iOS e Android](scrolling-images/snappoints-center.png "CollectionView elenco verticale con punti di allineamento al centro") ] (scrolling-images/snappoints-center-large.png#lightbox "Elenco verticale di CollectionView con punti di allineamento al centro")

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

Il codice C# equivalente è:

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

[ ![Screenshot di un elenco di ritrovi verticali con punti di aggancio finali, in iOS e Android](scrolling-images/snappoints-end.png "CollectionView elenco verticale con punti di allineamento finali") ] (scrolling-images/snappoints-end-large.png#lightbox "Elenco verticale di CollectionView con punti di aggancio finali")

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
