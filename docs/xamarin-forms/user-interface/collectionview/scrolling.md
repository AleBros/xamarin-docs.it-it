---
title: Scorrere un elemento all'interno della visualizzazione
description: Quando un tessere magnetiche utente per avviare un'operazione di scorrimento, la posizione finale dello scorrimento può essere controllata in modo che gli elementi vengono visualizzati completamente. Inoltre, visualizzazione di raccolta definisce due metodi ScrollTo, che a livello di programmazione scorrere gli elementi all'interno della visualizzazione.
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/19/2019
ms.openlocfilehash: da7f379076b8e193deddc99e9004f051ba006cbb
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329949"
---
# <a name="scroll-an-item-into-view"></a>Scorrere un elemento all'interno della visualizzazione

![Anteprima](~/media/shared/preview.png)

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> Il `CollectionView` è attualmente un'anteprima e non dispone di alcune delle proprie funzionalità pianificato. Inoltre, l'API può cambiare quando viene completata l'implementazione.

`CollectionView` vengono definiti due `ScrollTo` metodi, scorrere gli elementi all'interno della visualizzazione. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato all'interno della visualizzazione, mentre l'altro scorre l'elemento specificato all'interno della visualizzazione. Entrambi gli overload sono argomenti aggiuntivi che possono essere specificati per indicare la posizione esatta dell'elemento dopo lo scorrimento è stata completata e se si desidera animare lo scorrimento.

`CollectionView` definisce un `ScrollToRequested` evento generato quando uno del `ScrollTo` metodi viene richiamato. Il `ScrollToRequestedEventArgs` che accompagna il `ScrollToRequested` evento dispone di molte proprietà, tra cui `IsAnimated`, `Index`, `Item`, e `ScrollToPosition`. Queste proprietà vengono impostate in base agli argomenti specificati nel `ScrollTo` chiamate al metodo.

Quando un tessere magnetiche utente per avviare un'operazione di scorrimento, la posizione finale dello scorrimento può essere controllata in modo che gli elementi vengono visualizzati completamente. Questa funzionalità è noto come blocco, perché gli elementi di allineamento per posizionare durante lo scorrimento viene arrestata. Per altre informazioni, vedere [punti di allineamento](#snap-points).

## <a name="scroll-an-item-at-an-index-into-view"></a>Scorrere un elemento in corrispondenza di un indice all'interno della visualizzazione

Il primo `ScrollTo` overload del metodo scorre l'elemento in corrispondenza dell'indice specificato all'interno della visualizzazione. Dato un `CollectionView` oggetto denominato `collectionView`, nell'esempio seguente viene illustrato come scorrere l'elemento in corrispondenza dell'indice 12 nella visualizzazione:

```csharp
collectionView.ScrollTo(12);
```

## <a name="scroll-an-item-into-view"></a>Scorrere un elemento all'interno della visualizzazione

Il secondo `ScrollTo` overload del metodo scorre l'elemento specificato all'interno della visualizzazione. Dato un `CollectionView` oggetto denominato `collectionView`, nell'esempio seguente viene illustrato come scorrere l'elemento specificato all'interno della visualizzazione:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

## <a name="control-scroll-position"></a>Posizione di scorrimento di controllo

Durante lo scorrimento di un elemento in visualizzazione, è possibile specificare la posizione esatta dell'elemento dopo lo scorrimento è stata completata con il `position` argomento del `ScrollTo` metodi. Questo argomento accetta un [ `ScrollToPosition` ](xref:Xamarin.Forms.ScrollToPosition) membro di enumerazione.

### <a name="makevisible"></a>MakeVisible

Il [ `ScrollToPosition.MakeVisible` ](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve scorrere fino a quando non è visibile nella visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Questo codice di esempio genera lo scorrimento minimo necessario per scorrere l'elemento all'interno della visualizzazione:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con un elemento di cui è stato eseguito lo scorrimento nella visualizzazione, in iOS e Android](scrolling-images/scrolltoposition-makevisible.png "CollectionView elenco verticale con elementi a scorrimento")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "CollectionView elenco verticale con elemento di scorrimento")

> [!NOTE]
> Il [ `ScrollToPosition.MakeVisible` ](xref:Xamarin.Forms.ScrollToPosition) membro viene utilizzato per impostazione predefinita, se il `position` argomento non viene specificato quando si chiama il `ScrollTo` (metodo).

### <a name="start"></a>Inizia

Il [ `ScrollToPosition.Start` ](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato all'inizio della visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Questo codice di esempio genera l'elemento sottoposto a scorrimento all'inizio della visualizzazione:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con un elemento di cui è stato eseguito lo scorrimento nella visualizzazione, in iOS e Android](scrolling-images/scrolltoposition-start.png "CollectionView elenco verticale con elementi a scorrimento")](scrolling-images/scrolltoposition-start-large.png#lightbox "CollectionView elenco verticale con elemento di scorrimento")

### <a name="center"></a>Center

Il [ `ScrollToPosition.Center` ](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato al centro della visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Questo codice di esempio genera l'elemento sottoposto a scorrimento al centro della visualizzazione:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con un elemento di cui è stato eseguito lo scorrimento nella visualizzazione, in iOS e Android](scrolling-images/scrolltoposition-center.png "CollectionView elenco verticale con elementi a scorrimento")](scrolling-images/scrolltoposition-center-large.png#lightbox "CollectionView elenco verticale con elemento di scorrimento")

### <a name="end"></a>Fine

Il [ `ScrollToPosition.End` ](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato alla fine della visualizzazione:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Questo codice di esempio genera l'elemento sottoposto a scorrimento alla fine della visualizzazione:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con un elemento di cui è stato eseguito lo scorrimento nella visualizzazione, in iOS e Android](scrolling-images/scrolltoposition-end.png "CollectionView elenco verticale con elementi a scorrimento")](scrolling-images/scrolltoposition-end-large.png#lightbox "CollectionView elenco verticale con elemento di scorrimento")

## <a name="disable-scroll-animation"></a>Disabilitare l'animazione di scorrimento

Un'animazione dello scorrimento viene visualizzata quando lo scorrimento di un elemento nella visualizzazione. Tuttavia, questa animazione può essere disabilitata impostando la `animate` argomento del `ScrollTo` metodo `false`:

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="snap-points"></a>Punti di allineamento

Quando un tessere magnetiche utente per avviare un'operazione di scorrimento, la posizione finale dello scorrimento può essere controllata in modo che gli elementi vengono visualizzati completamente. Questa funzionalità è nota come blocco, perché gli elementi di allineamento per posizionare quando lo scorrimento sarà arrestato e viene controllato dalle proprietà seguenti dal `ItemsLayout` classe:

- `SnapPointsType`, di tipo `SnapPointsType`, specifica il comportamento dei punti di bloccaggio quando lo scorrimento.
- `SnapPointsAlignment`, di tipo `SnapPointsAlignment`, specifica l'allineamento con gli elementi di punti di bloccaggio.

Queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> Quando lo snapping si verifica, si verificano nella direzione che produce la quantità minima di movimento.

### <a name="snap-points-type"></a>Tipo di punti di allineamento

Il `SnapPointsType` enumerazione definisce i membri seguenti:

- `None` indica che lo scorrimento non si blocca in elementi.
- `Mandatory` indica che il contenuto consente di agganciare il più vicino snap fare sempre riferimento per lo scorrimento sarebbe naturalmente arresto, lungo la direzione dell'inerzia.
- `MandatorySingle` indica lo stesso comportamento `Mandatory`, ma solo scorre un elemento alla volta.

Per impostazione predefinita, il `SnapPointsType` è impostata su `SnapPointsType.None`, che assicura che lo scorrimento non blocca gli elementi, come illustrato negli screenshot seguenti:

[![Screenshot di un elenco verticale di visualizzazione di raccolta senza punti di bloccaggio, su iOS e Android](scrolling-images/snappoints-none.png "elenco verticale di visualizzazione di raccolta senza punti di bloccaggio")](scrolling-images/snappoints-none-large.png#lightbox "elenco verticale di visualizzazione di raccolta senza snap punti")

### <a name="snap-points-alignment"></a>Allineamento di punti di allineamento

Il `SnapPointsAlignment` enumerazione definisce `Start`, `Center`, e `End` membri.

> [!IMPORTANT]
> Il valore della `SnapPointsAlignment` proprietà è solo rispettato quando il `SnapPointsType` è impostata su `Mandatory`, o `MandatorySingle`.

#### <a name="start"></a>Inizia

Il `SnapPointsAlignment.Start` membro indica che i punti di bloccaggio siano allineati con il bordo iniziale di elementi.

Per impostazione predefinita, la proprietà `SnapPointsAlignment` è impostata su `SnapPointsAlignment.Start`. Tuttavia, per motivi di completezza, nell'esempio XAML seguente viene illustrato come impostare questo membro di enumerazione:

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

Il codice c# equivalente è:

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

Quando un tessere magnetiche utente per avviare un'operazione di scorrimento, il primo elemento sarà allineato alla parte superiore della visualizzazione:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con i punti di bloccaggio start, su iOS e Android](scrolling-images/snappoints-start.png "CollectionView elenco verticale con punti di bloccaggio inizio")](scrolling-images/snappoints-start-large.png#lightbox "CollectionView elenco verticale con inizio i punti di allineamento")

#### <a name="center"></a>Center

Il `SnapPointsAlignment.Center` membro indica che i punti di bloccaggio siano allineati con il centro di elementi. Nell'esempio XAML seguente viene illustrato come impostare il membro di enumerazione:

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

Il codice c# equivalente è:

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

Quando un tessere magnetiche utente per avviare un'operazione di scorrimento, il primo elemento è allineato alla parte superiore della visualizzazione al centro:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con i punti di bloccaggio center, in iOS e Android](scrolling-images/snappoints-center.png "CollectionView elenco verticale con punti di bloccaggio center")](scrolling-images/snappoints-center-large.png#lightbox "CollectionView elenco verticale con punti di bloccaggio Center")

#### <a name="end"></a>Fine

Il `SnapPointsAlignment.End` membro indica che i punti di bloccaggio siano allineati con il margine finale di elementi. Nell'esempio XAML seguente viene illustrato come impostare il membro di enumerazione:

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

Il codice c# equivalente è:

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

Quando un tessere magnetiche utente per avviare un'operazione di scorrimento, l'elemento nella parte inferiore sarà allineata alla parte inferiore della visualizzazione:

[![Screenshot di un elenco verticale di visualizzazione di raccolta con i punti di bloccaggio finali, nei sistemi iOS e Android](scrolling-images/snappoints-end.png "CollectionView elenco verticale con punti di bloccaggio finali")](scrolling-images/snappoints-end-large.png#lightbox "CollectionView elenco verticale con lo snap di fine punti")

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di raccolta (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
