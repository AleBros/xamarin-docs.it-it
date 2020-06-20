---
title: Xamarin.FormsScorrimento CarouselView
description: Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Inoltre, CarouselView definisce due metodi ScrollTo, che a livello di codice scorrono gli elementi nella visualizzazione.
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 462948905f40679e2b931d4aa0039308c64a0a8f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136496"
---
# <a name="xamarinforms-carouselview-scrolling"></a>Xamarin.FormsScorrimento CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce le seguenti proprietà correlate allo scorrimento:

- `HorizontalScrollBarVisibility`, di tipo `ScrollBarVisibility` , che specifica quando la barra di scorrimento orizzontale è visibile.
- `IsDragging`, di tipo `bool` , che indica se l'oggetto `CarouselView` è a scorrimento. Si tratta di una proprietà di sola lettura il cui valore predefinito è `false` .
- `IsScrollAnimated`, di tipo `bool` , che specifica se si verificherà un'animazione durante lo scorrimento dell'oggetto `CarouselView` . Il valore predefinito è `true`.
- `ItemsUpdatingScrollMode`, di tipo `ItemsUpdatingScrollMode` , che rappresenta il comportamento di scorrimento di `CarouselView` quando vengono aggiunti nuovi elementi.
- `VerticalScrollBarVisibility`, di tipo `ScrollBarVisibility` , che specifica quando la barra di scorrimento verticale è visibile.

Tutte queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di associazioni dati.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce inoltre due [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) metodi, che scorrono gli elementi nella visualizzazione. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione, mentre l'altro scorre l'elemento specificato nella visualizzazione. Entrambi gli overload hanno argomenti aggiuntivi che possono essere specificati per indicare la posizione esatta dell'elemento dopo il completamento dello scorrimento e l'eventuale animazione dello scorrimento.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce un [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento che viene generato quando [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) viene richiamato uno dei metodi. L' [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) oggetto che accompagna l' `ScrollToRequested` evento dispone di molte proprietà, tra cui `IsAnimated` , `Index` , `Item` e `ScrollToPosition` . Queste proprietà vengono impostate dagli argomenti specificati nelle chiamate al `ScrollTo` metodo.

Inoltre, [`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce un `Scrolled` evento generato per indicare che si è verificato lo scorrimento. Il `ItemsViewScrolledEventArgs` numero di proprietà dell'oggetto che accompagna l' `Scrolled` evento è elevato. Per ulteriori informazioni, vedere [rilevamento dello scorrimento](#detect-scrolling).

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Questa funzionalità è nota come blocco, perché gli elementi si bloccano alla posizione quando lo scorrimento viene interrotto. Per altre informazioni, vedere [punti di aggancio](#snap-points).

[`CarouselView`](xref:Xamarin.Forms.CarouselView)consente inoltre di caricare i dati in modo incrementale quando l'utente scorre. Per altre informazioni, vedere [caricare dati in modo incrementale](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Rileva scorrimento

`IsDragging`È possibile esaminare la proprietà per determinare se l'oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) sta attualmente scorrendo gli elementi.

Inoltre, [`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce un `Scrolled` evento generato per indicare che si è verificato lo scorrimento. Questo evento deve essere utilizzato quando sono necessari dati sullo scorrimento.

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto `CarouselView` che imposta un gestore eventi per l' `Scrolled` evento:

```xaml
<CarouselView Scrolled="OnCollectionViewScrolled">
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.Scrolled += OnCarouselViewScrolled;
```

In questo esempio di codice, il `OnCarouselViewScrolled` gestore eventi viene eseguito quando viene `Scrolled` generato l'evento:

```csharp
void OnCarouselViewScrolled(object sender, ItemsViewScrolledEventArgs e)
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

In questo esempio, il `OnCarouselViewScrolled` gestore eventi restituisce i valori dell' `ItemsViewScrolledEventArgs` oggetto che accompagna l'evento.

> [!IMPORTANT]
> L' `Scrolled` evento viene generato per gli scorrimenti avviati dall'utente e per gli scorrimenti a livello di codice.

## <a name="scroll-an-item-at-an-index-into-view"></a>Scorrere un elemento in corrispondenza di un indice nella visualizzazione

Il primo [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) Overload del metodo scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione. Dato un [`CarouselView`](xref:Xamarin.Forms.CarouselView) oggetto denominato `carouselView` , nell'esempio seguente viene illustrato come scorrere l'elemento in corrispondenza dell'indice 6 nella visualizzazione:

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> L' [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento viene generato quando [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) viene richiamato il metodo.

## <a name="scroll-an-item-into-view"></a>Scorrere un elemento nella visualizzazione

Il secondo [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) Overload del metodo scorre l'elemento specificato nella visualizzazione. Dato un [`CarouselView`](xref:Xamarin.Forms.CarouselView) oggetto denominato `carouselView` , l'esempio seguente mostra come scorrere l'elemento della scimmia proboscide nella visualizzazione:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> L' [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento viene generato quando [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) viene richiamato il metodo.

## <a name="disable-scroll-animation"></a>Disabilita animazione Scroll

Quando si sposta tra gli elementi di un oggetto, viene visualizzata un'animazione di scorrimento [`CarouselView`](xref:Xamarin.Forms.CarouselView) . Questa animazione si verifica sia per gli scorrimenti avviati dall'utente sia per gli scorrimenti a livello di codice. Se si imposta la `IsScrollAnimated` proprietà su `false` , l'animazione viene disabilitata per entrambe le categorie di scorrimento.

In alternativa, l' `animate` argomento del `ScrollTo` metodo può essere impostato su `false` per disabilitare l'animazione di scorrimento negli scorrimenti a livello di codice:

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>Posizione di scorrimento del controllo

Quando si scorre un elemento nella visualizzazione, è possibile specificare la posizione esatta dell'elemento dopo il completamento dello scorrimento con l' `position` argomento dei [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) metodi. Questo argomento accetta un [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) membro di enumerazione.

### <a name="makevisible"></a>MakeVisible

Il [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato fino a quando non è visibile nella visualizzazione:

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Questo codice di esempio genera lo scorrimento minimo necessario per scorrere l'elemento nella visualizzazione.

> [!NOTE]
> Il [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membro viene usato per impostazione predefinita, se l' `position` argomento non viene specificato quando si chiama il `ScrollTo` metodo.

### <a name="start"></a>Avvio

Il [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato all'inizio della visualizzazione:

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Questo esempio di codice comporta lo scorrimento dell'elemento all'inizio della visualizzazione.

### <a name="center"></a>Center

Il [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato al centro della visualizzazione:

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Questo esempio di codice comporta lo scorrimento dell'elemento al centro della visualizzazione.

### <a name="end"></a>Fine

Il [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) membro indica che l'elemento deve essere spostato alla fine della visualizzazione:

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Questo esempio di codice comporta lo scorrimento dell'elemento alla fine della visualizzazione.

## <a name="control-scroll-position-when-new-items-are-added"></a>Posizione di scorrimento del controllo quando vengono aggiunti nuovi elementi

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce una `ItemsUpdatingScrollMode` proprietà, supportata da una proprietà associabile. Questa proprietà Ottiene o imposta un `ItemsUpdatingScrollMode` valore di enumerazione che rappresenta il comportamento di scorrimento di `CarouselView` quando vengono aggiunti nuovi elementi. L'enumerazione `ItemsUpdatingScrollMode` definisce i membri seguenti:

- `KeepItemsInView`regola l'offset di scorrimento per tenere visualizzato il primo elemento visibile quando vengono aggiunti nuovi elementi.
- `KeepScrollOffset`mantiene l'offset di scorrimento relativo all'inizio dell'elenco quando vengono aggiunti nuovi elementi.
- `KeepLastItemInView`regola l'offset di scorrimento per tenere visibile l'ultimo elemento quando vengono aggiunti nuovi elementi.

Il valore predefinito della `ItemsUpdatingScrollMode` proprietà è `KeepItemsInView` . Pertanto, quando vengono aggiunti nuovi elementi a un oggetto, [`CarouselView`](xref:Xamarin.Forms.CarouselView) il primo elemento visibile nell'elenco rimarrà visualizzato. Per assicurarsi che gli elementi appena aggiunti siano sempre visibili nella parte inferiore dell'elenco, la `ItemsUpdatingScrollMode` proprietà deve essere impostata su `KeepLastItemInView` :

```xaml
<CarouselView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>Visibilità della barra di scorrimento

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce `HorizontalScrollBarVisibility` le `VerticalScrollBarVisibility` proprietà e, supportate da proprietà associabili. Queste proprietà ottengono o impostano un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valore di enumerazione che rappresenta quando la barra di scorrimento orizzontale o verticale è visibile. L'enumerazione `ScrollBarVisibility` definisce i membri seguenti:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)indica il comportamento predefinito della barra di scorrimento per la piattaforma e è il valore predefinito per `HorizontalScrollBarVisibility` le `VerticalScrollBarVisibility` proprietà e.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)indica che le barre di scorrimento saranno visibili, anche quando il contenuto si adatterà alla visualizzazione.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)indica che le barre di scorrimento non saranno visibili, anche se il contenuto non rientra nella visualizzazione.

## <a name="snap-points"></a>Punti di ancoraggio

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Questa funzionalità è nota come blocco, perché gli elementi si bloccano alla posizione quando lo scorrimento viene interrotto ed è controllato dalle proprietà seguenti della [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe:

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), di tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) , specifica il comportamento dei punti di blocco durante lo scorrimento.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), di tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) , specifica il modo in cui i punti di allineamento sono allineati con gli elementi.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> Quando si verifica il blocco, si verificherà nella direzione che produce il minor numero di movimento.

### <a name="snap-points-type"></a>Tipo di punti di aggancio

L' [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) enumerazione definisce i membri seguenti:

- `None`indica che lo scorrimento non si blocca sugli elementi.
- `Mandatory`indica che il contenuto viene sempre agganciato al punto di aggancio più vicino alla posizione in cui lo scorrimento viene naturalmente interrotto, lungo la direzione di inerzia.
- `MandatorySingle`indica lo stesso comportamento di `Mandatory` , ma scorre un solo elemento alla volta.

Per impostazione predefinita [`CarouselView`](xref:Xamarin.Forms.CarouselView) , la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) proprietà è impostata su, in modo da `SnapPointsType.MandatorySingle` garantire che lo scorrimento scorra un solo elemento alla volta.

Gli screenshot seguenti mostrano un [`CarouselView`](xref:Xamarin.Forms.CarouselView) con blocco disattivato:

[![Screenshot di un CarouselView senza punti di aggancio, in iOS e Android](scrolling-images/snappoints-none.png "CarouselView senza punti di aggancio")](scrolling-images/snappoints-none-large.png#lightbox "CarouselView senza punti di aggancio")

### <a name="snap-points-alignment"></a>Allineamento punti di aggancio

L' [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) enumerazione definisce `Start` `Center` `End` i membri, e.

> [!IMPORTANT]
> Il valore della [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) proprietà viene rispettato solo quando la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) proprietà è impostata su `Mandatory` , o `MandatorySingle` .

#### <a name="start"></a>Avvio

Il `SnapPointsAlignment.Start` membro indica che i punti di allineamento sono allineati al bordo di elementi iniziali. Nell'esempio di codice XAML riportato di seguito viene illustrato come impostare questo membro di enumerazione:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

Quando un utente scorre per avviare uno scorrimento in uno scorrimento orizzontale [`CarouselView`](xref:Xamarin.Forms.CarouselView) , l'elemento a sinistra verrà allineato a sinistra della visualizzazione:

[![Screenshot di un CarouselView con punti di aggancio iniziali, in iOS e Android](scrolling-images/snappoints-start.png "CarouselView con punti di aggancio iniziali")](scrolling-images/snappoints-start-large.png#lightbox "CarouselView con punti di aggancio iniziali")

#### <a name="center"></a>Center

Il `SnapPointsAlignment.Center` membro indica che i punti di allineamento sono allineati al centro di elementi.

Per impostazione predefinita [`CarouselView`](xref:Xamarin.Forms.CarouselView) , la [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) proprietà viene impostata su `Center` . Per completezza, tuttavia, nell'esempio di codice XAML riportato di seguito viene illustrato come impostare questo membro di enumerazione:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

Quando un utente scorre per avviare uno scorrimento in uno scorrimento orizzontale [`CarouselView`](xref:Xamarin.Forms.CarouselView) , l'elemento centrale verrà allineato al centro della visualizzazione:

[![Screenshot di un CarouselView con punti di allineamento al centro, in iOS e Android](scrolling-images/snappoints-center.png "CarouselView con punti di allineamento centrali")](scrolling-images/snappoints-center-large.png#lightbox "CarouselView con punti di allineamento centrali")

#### <a name="end"></a>Fine

Il `SnapPointsAlignment.End` membro indica che i punti di allineamento sono allineati al bordo finale degli elementi. Nell'esempio di codice XAML riportato di seguito viene illustrato come impostare questo membro di enumerazione:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

Quando un utente scorre per avviare uno scorrimento in uno scorrimento orizzontale [`CarouselView`](xref:Xamarin.Forms.CarouselView) , l'elemento a destra verrà allineato a destra della visualizzazione.

[![Screenshot di un CarouselView con punti di blocco finali, in iOS e Android](scrolling-images/snappoints-end.png "CarouselView con punti di allineamento finali")](scrolling-images/snappoints-end-large.png#lightbox "CarouselView con punti di allineamento finali")

## <a name="related-links"></a>Collegamenti correlati

- [CarouselView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
