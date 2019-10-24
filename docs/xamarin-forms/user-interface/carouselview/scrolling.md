---
title: Scorrimento CarouselView Novell. Forms
description: Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Inoltre, CarouselView definisce due metodi ScrollTo, che a livello di codice scorrono gli elementi nella visualizzazione.
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: dc72dc7549a697c7231045601851ba4108f29e1b
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697584"
---
# <a name="xamarinforms-carouselview-scrolling"></a>Scorrimento CarouselView Novell. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce le seguenti proprietà correlate allo scorrimento:

- `HorizontalScrollBarVisibility`, di tipo `ScrollBarVisibility`, che specifica quando la barra di scorrimento orizzontale è visibile.
- `IsDragging`, di tipo `bool`, che indica se la `CarouselView` è a scorrimento. Si tratta di una proprietà di sola lettura il cui valore predefinito è `false`.
- `IsScrollAnimated`, di tipo `bool`, che specifica se si verificherà un'animazione durante lo scorrimento della `CarouselView`. Il valore predefinito è `true`.
- `ItemsUpdatingScrollMode`, di tipo `ItemsUpdatingScrollMode`, che rappresenta il comportamento di scorrimento del `CarouselView` quando vengono aggiunti nuovi elementi.
- `VerticalScrollBarVisibility`, di tipo `ScrollBarVisibility`, che specifica quando la barra di scorrimento verticale è visibile.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che possono essere destinazioni di associazioni dati.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce inoltre due metodi [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , che scorrono gli elementi nella visualizzazione. Uno degli overload scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione, mentre l'altro scorre l'elemento specificato nella visualizzazione. Entrambi gli overload hanno argomenti aggiuntivi che possono essere specificati per indicare la posizione esatta dell'elemento dopo il completamento dello scorrimento e l'eventuale animazione dello scorrimento.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce un evento [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) generato quando viene richiamato uno dei metodi di [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) . Il [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) oggetto che accompagna l'evento `ScrollToRequested` dispone di molte proprietà, tra cui `IsAnimated`, `Index`, `Item` e `ScrollToPosition`. Queste proprietà vengono impostate dagli argomenti specificati nelle chiamate al metodo `ScrollTo`.

Inoltre, [`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce un evento `Scrolled` generato per indicare che si è verificato lo scorrimento. Il `ItemsViewScrolledEventArgs` oggetto che accompagna l'evento `Scrolled` dispone di molte proprietà. Per ulteriori informazioni, vedere [rilevamento dello scorrimento](#detect-scrolling).

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Questa funzionalità è nota come blocco, perché gli elementi si bloccano alla posizione quando lo scorrimento viene interrotto. Per altre informazioni, vedere [punti di aggancio](#snap-points).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) inoltre possibile caricare i dati in modo incrementale mentre l'utente scorre. Per altre informazioni, vedere [caricare dati in modo incrementale](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Rileva scorrimento

È possibile esaminare la proprietà `IsDragging` per determinare se il [`CarouselView`](xref:Xamarin.Forms.CarouselView) sta attualmente scorrendo gli elementi.

Inoltre, [`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce un evento `Scrolled` che viene generato per indicare che si è verificato lo scorrimento. Questo evento deve essere utilizzato quando sono necessari dati sullo scorrimento.

Nell'esempio di codice XAML riportato di seguito viene illustrato un `CarouselView` che imposta un gestore eventi per l'evento `Scrolled`:

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

In questo esempio di codice, il gestore dell'evento `OnCarouselViewScrolled` viene eseguito quando viene generato l'evento `Scrolled`:

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

In questo esempio, il gestore dell'evento `OnCarouselViewScrolled` restituisce i valori dell'oggetto `ItemsViewScrolledEventArgs` che accompagna l'evento.

> [!IMPORTANT]
> L'evento `Scrolled` viene generato per gli scorrimenti avviati dall'utente e per gli scorrimenti a livello di codice.

## <a name="scroll-an-item-at-an-index-into-view"></a>Scorrere un elemento in corrispondenza di un indice nella visualizzazione

Il primo overload del metodo [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) scorre l'elemento in corrispondenza dell'indice specificato nella visualizzazione. Dato un oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) denominato `carouselView`, nell'esempio seguente viene illustrato come scorrere l'elemento in corrispondenza dell'indice 6 nella visualizzazione:

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> L'evento [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) viene generato quando viene richiamato il metodo [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) .

## <a name="scroll-an-item-into-view"></a>Scorrere un elemento nella visualizzazione

Il secondo overload del metodo [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) scorre l'elemento specificato nella visualizzazione. Dato un oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) denominato `carouselView`, nell'esempio seguente viene illustrato come scorrere l'elemento della scimmia proboscide nella visualizzazione:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> L'evento [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) viene generato quando viene richiamato il metodo [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) .

## <a name="disable-scroll-animation"></a>Disabilita animazione Scroll

Quando si sposta tra gli elementi di un [`CarouselView`](xref:Xamarin.Forms.CarouselView), viene visualizzata un'animazione di scorrimento. Questa animazione si verifica sia per gli scorrimenti avviati dall'utente sia per gli scorrimenti a livello di codice. Se si imposta la proprietà `IsScrollAnimated` su `false`, l'animazione viene disabilitata per entrambe le categorie di scorrimento.

In alternativa, è possibile impostare l'argomento `animate` del metodo `ScrollTo` su `false` per disabilitare l'animazione di scorrimento negli scorrimenti a livello di codice:

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>Posizione di scorrimento del controllo

Quando si scorre un elemento nella visualizzazione, è possibile specificare la posizione esatta dell'elemento dopo il completamento dello scorrimento con l'argomento `position` dei metodi di [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) . Questo argomento accetta un membro di enumerazione [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) .

### <a name="makevisible"></a>MakeVisible

Il membro [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) indica che l'elemento deve essere spostato fino a quando non è visibile nella visualizzazione:

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Questo codice di esempio genera lo scorrimento minimo necessario per scorrere l'elemento nella visualizzazione.

> [!NOTE]
> Il membro [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) viene usato per impostazione predefinita, se non viene specificato l'argomento `position` quando si chiama il metodo di `ScrollTo`.

### <a name="start"></a>Inizio

Il membro [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) indica che l'elemento deve essere spostato all'inizio della visualizzazione:

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Questo esempio di codice comporta lo scorrimento dell'elemento all'inizio della visualizzazione.

### <a name="center"></a>Center

Il membro [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) indica che l'elemento deve essere spostato al centro della visualizzazione:

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Questo esempio di codice comporta lo scorrimento dell'elemento al centro della visualizzazione.

### <a name="end"></a>Fine

Il membro [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) indica che l'elemento deve essere spostato alla fine della visualizzazione:

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Questo esempio di codice comporta lo scorrimento dell'elemento alla fine della visualizzazione.

## <a name="control-scroll-position-when-new-items-are-added"></a>Posizione di scorrimento del controllo quando vengono aggiunti nuovi elementi

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce una proprietà `ItemsUpdatingScrollMode`, supportata da una proprietà associabile. Questa proprietà Ottiene o imposta un valore di enumerazione `ItemsUpdatingScrollMode` che rappresenta il comportamento di scorrimento della `CarouselView` quando vengono aggiunti nuovi elementi. L'enumerazione `ItemsUpdatingScrollMode` definisce i membri seguenti:

- `KeepItemsInView` regola l'offset di scorrimento per tenere visualizzato il primo elemento visibile quando vengono aggiunti nuovi elementi.
- `KeepScrollOffset` gestisce l'offset di scorrimento relativo all'inizio dell'elenco quando vengono aggiunti nuovi elementi.
- `KeepLastItemInView` regola l'offset di scorrimento per rendere visibile l'ultimo elemento quando vengono aggiunti nuovi elementi.

Il valore predefinito della proprietà `ItemsUpdatingScrollMode` è `KeepItemsInView`. Pertanto, quando vengono aggiunti nuovi elementi a una [`CarouselView`](xref:Xamarin.Forms.CarouselView) il primo elemento visibile nell'elenco rimarrà visualizzato. Per assicurarsi che gli elementi appena aggiunti siano sempre visibili nella parte inferiore dell'elenco, è necessario impostare la proprietà `ItemsUpdatingScrollMode` su `KeepLastItemInView`:

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

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce `HorizontalScrollBarVisibility` e `VerticalScrollBarVisibility` proprietà, supportate da proprietà associabili. Queste proprietà ottengono o impostano un valore di enumerazione [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) che rappresenta quando la barra di scorrimento orizzontale o verticale è visibile. L'enumerazione `ScrollBarVisibility` definisce i membri seguenti:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) indica il comportamento predefinito della barra di scorrimento per la piattaforma ed è il valore predefinito per le proprietà `HorizontalScrollBarVisibility` e `VerticalScrollBarVisibility`.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) indica che le barre di scorrimento saranno visibili, anche quando il contenuto si adatterà alla visualizzazione.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) indica che le barre di scorrimento non saranno visibili, anche se il contenuto non rientra nella visualizzazione.

## <a name="snap-points"></a>Punti di aggancio

Quando un utente scorre il dito per avviare uno scorrimento, è possibile controllare la posizione finale dello scorrimento in modo che gli elementi vengano visualizzati completamente. Questa funzionalità è nota come blocco, perché gli elementi si bloccano alla posizione quando lo scorrimento viene interrotto ed è controllato dalle proprietà seguenti della classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) :

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), di tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), specifica il comportamento dei punti di blocco durante lo scorrimento.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), di tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), specifica il modo in cui i punti di allineamento sono allineati con gli elementi.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> Quando si verifica il blocco, si verificherà nella direzione che produce il minor numero di movimento.

### <a name="snap-points-type"></a>Tipo di punti di aggancio

L'enumerazione [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) definisce i membri seguenti:

- `None` indica che lo scorrimento non si blocca sugli elementi.
- `Mandatory` indica che il contenuto viene sempre agganciato al punto di blocco più vicino alla posizione in cui lo scorrimento viene naturalmente interrotto, lungo la direzione di inerzia.
- `MandatorySingle` indica lo stesso comportamento `Mandatory`, ma scorre un solo elemento alla volta.

Per impostazione predefinita, in una [`CarouselView`](xref:Xamarin.Forms.CarouselView)la proprietà [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) è impostata su `SnapPointsType.MandatorySingle`, in modo da garantire che lo scorrimento scorra un solo elemento alla volta.

### <a name="snap-points-alignment"></a>Allineamento punti di aggancio

L'enumerazione [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) definisce i membri `Start`, `Center` e `End`.

> [!IMPORTANT]
> Il valore della proprietà [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) viene rispettato solo quando la proprietà [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) è impostata su `Mandatory` o `MandatorySingle`.

#### <a name="start"></a>Inizio

Il membro `SnapPointsAlignment.Start` indica che i punti di allineamento sono allineati al bordo di elementi iniziali. Nell'esempio di codice XAML riportato di seguito viene illustrato come impostare questo membro di enumerazione:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              NumberOfSideItems="1">
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

Quando un utente scorre un dito per avviare uno scorrimento in una [`CarouselView`](xref:Xamarin.Forms.CarouselView)a scorrimento orizzontale, l'elemento a sinistra verrà allineato a sinistra della visualizzazione.

#### <a name="center"></a>Center

Il membro `SnapPointsAlignment.Center` indica che i punti di allineamento sono allineati al centro di elementi.

Per impostazione predefinita, in un [`CarouselView`](xref:Xamarin.Forms.CarouselView)la proprietà [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) è impostata su `Center`. Per completezza, tuttavia, nell'esempio di codice XAML riportato di seguito viene illustrato come impostare questo membro di enumerazione:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              NumberOfSideItems="1">
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

Quando un utente scorre un dito per avviare uno scorrimento in una [`CarouselView`](xref:Xamarin.Forms.CarouselView)a scorrimento orizzontale, l'elemento centrale verrà allineato al centro della visualizzazione.

#### <a name="end"></a>Fine

Il membro `SnapPointsAlignment.End` indica che i punti di allineamento sono allineati al bordo finale degli elementi. Nell'esempio di codice XAML riportato di seguito viene illustrato come impostare questo membro di enumerazione:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              NumberOfSideItems="1">
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

Quando un utente scorre un dito per avviare uno scorrimento in una [`CarouselView`](xref:Xamarin.Forms.CarouselView)a scorrimento orizzontale, l'elemento a destra verrà allineato a destra della visualizzazione.

## <a name="related-links"></a>Collegamenti correlati

- [CarouselView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
