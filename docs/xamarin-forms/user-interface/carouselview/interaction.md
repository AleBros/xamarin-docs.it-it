---
title: Interazione CarouselView di Novell. Forms
description: È possibile accedere all'elemento attualmente visualizzato in un CarouselView tramite le proprietà CurrentItem e position.
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/11/2020
ms.openlocfilehash: 150c358346f90a513e1558dc847ad7eb6dd6e6e2
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292301"
---
# <a name="xamarinforms-carouselview-interaction"></a>Interazione CarouselView di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce le proprietà seguenti che controllano l'interazione dell'utente:

- `CurrentItem`, di tipo `object`, l'elemento corrente visualizzato. Questa proprietà ha una modalità di associazione predefinita di `TwoWay`e ha un valore `null` se non sono presenti dati da visualizzare.
- `CurrentItemChangedCommand`, di tipo `ICommand`, che viene eseguito quando viene modificato l'elemento corrente.
- `CurrentItemChangedCommandParameter`, di tipo `object`, ovvero il parametro passato a `CurrentItemChangedCommand`.
- `IsBounceEnabled`, di tipo `bool`, che specifica se il `CarouselView` rimbalzerà a un limite del contenuto. Il valore predefinito è `true`.
- `IsSwipeEnabled`, di tipo `bool`, che determina se un movimento di scorrimento rapido cambierà l'elemento visualizzato. Il valore predefinito è `true`.
- `Position`, di tipo `int`, l'indice dell'elemento corrente nella raccolta sottostante. Questa proprietà ha una modalità di associazione predefinita di `TwoWay`e ha un valore 0 quando non sono presenti dati da visualizzare.
- `PositionChangedCommand`, di tipo `ICommand`, che viene eseguito quando cambia la posizione.
- `PositionChangedCommandParameter`, di tipo `object`, ovvero il parametro passato a `PositionChangedCommand`.
- `VisibleViews`, di tipo `ObservableCollection<View>`, che è una proprietà di sola lettura che contiene gli oggetti per gli elementi attualmente visibili.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che tali proprietà possono essere destinazioni di data binding.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce un evento `CurrentItemChanged` generato quando la proprietà `CurrentItem` viene modificata, a causa dello scorrimento utente o quando un'applicazione imposta la proprietà. L'oggetto `CurrentItemChangedEventArgs` che accompagna l'evento `CurrentItemChanged` dispone di due proprietà, entrambe di tipo `object`:

- `PreviousItem`: l'elemento precedente, dopo la modifica della proprietà.
- `CurrentItem`: elemento corrente, dopo la modifica della proprietà.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce anche un evento `PositionChanged` generato quando la proprietà `Position` viene modificata, a causa dello scorrimento dell'utente o quando un'applicazione imposta la proprietà. L'oggetto `PositionChangedEventArgs` che accompagna l'evento `PositionChanged` dispone di due proprietà, entrambe di tipo `int`:

- `PreviousPosition`: posizione precedente, dopo la modifica della proprietà.
- `CurrentPosition`: posizione corrente, dopo la modifica della proprietà.

## <a name="respond-to-the-current-item-changing"></a>Rispondi alla modifica dell'elemento corrente

Quando l'elemento attualmente visualizzato viene modificato, la proprietà `CurrentItem` verrà impostata sul valore dell'elemento. Quando questa proprietà viene modificata, l'`CurrentItemChangedCommand` viene eseguita con il valore della `CurrentItemChangedCommandParameter` passata al `ICommand`. La proprietà `Position` viene quindi aggiornata e viene generato l'evento `CurrentItemChanged`.

> [!IMPORTANT]
> La proprietà `Position` viene modificata quando viene modificata la proprietà `CurrentItem`. Questo comporterà l'esecuzione del `PositionChangedCommand` e la generazione dell'evento `PositionChanged`.

### <a name="event"></a>Evento

Nell'esempio di codice XAML riportato di seguito viene illustrato un [`CarouselView`](xref:Xamarin.Forms.CarouselView) che utilizza un gestore eventi per rispondere alla modifica dell'elemento corrente:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChanged="OnCurrentItemChanged">
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.CurrentItemChanged += OnCurrentItemChanged;
```

In questo esempio, il gestore dell'evento `OnCurrentItemChanged` viene eseguito quando viene generato l'evento `CurrentItemChanged`:

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

In questo esempio, il gestore dell'evento `OnCurrentItemChanged` espone gli elementi precedenti e correnti:

[![Screenshot di un CarouselView con elementi precedenti e correnti, in iOS e Android](interaction-images/current-item-events.png "CarouselView con elementi correnti e precedenti")](interaction-images/current-item-events-large.png#lightbox "CarouselView con elementi correnti e precedenti")

### <a name="command"></a>Comando

Nell'esempio di codice XAML riportato di seguito viene illustrato un [`CarouselView`](xref:Xamarin.Forms.CarouselView) che utilizza un comando per rispondere alla modifica dell'elemento corrente:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChangedCommand="{Binding ItemChangedCommand}"
              CurrentItemChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=CurrentItem}">
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandProperty, "ItemChangedCommand");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandParameterProperty, new Binding("CurrentItem", source: RelativeBindingSource.Self));
```

In questo esempio, la proprietà `CurrentItemChangedCommand` viene associata alla proprietà `ItemChangedCommand`, passando il valore della proprietà `CurrentItem` come argomento. Il `ItemChangedCommand` può quindi rispondere alla modifica dell'elemento corrente, come richiesto:

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

In questo esempio, il `ItemChangedCommand` aggiorna gli oggetti in cui sono archiviati gli elementi precedenti e correnti.

## <a name="respond-to-the-position-changing"></a>Rispondere alla modifica della posizione

Quando l'elemento attualmente visualizzato viene modificato, la proprietà `Position` verrà impostata sull'indice dell'elemento corrente nella raccolta sottostante. Quando questa proprietà viene modificata, l'`PositionChangedCommand` viene eseguita con il valore della `PositionChangedCommandParameter` passata al `ICommand`. Viene quindi generato l'evento `PositionChanged`. Se la proprietà `Position` è stata modificata a livello di codice, verrà eseguito lo scorrimento della [`CarouselView`](xref:Xamarin.Forms.CarouselView) sull'elemento che corrisponde al valore di `Position`.

> [!NOTE]
> Se si imposta la proprietà `Position` su 0, il primo elemento della raccolta sottostante verrà visualizzato.

### <a name="event"></a>Evento

Nell'esempio di codice XAML riportato di seguito viene illustrato un [`CarouselView`](xref:Xamarin.Forms.CarouselView) che utilizza un gestore eventi per rispondere alla modifica della proprietà `Position`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"              
              PositionChanged="OnPositionChanged">
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.PositionChanged += OnPositionChanged;
```

In questo esempio, il gestore dell'evento `OnPositionChanged` viene eseguito quando viene generato l'evento `PositionChanged`:

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

In questo esempio, il gestore dell'evento `OnCurrentItemChanged` espone le posizioni precedenti e correnti:

[![Screenshot di un CarouselView con posizioni precedenti e correnti, in iOS e Android](interaction-images/current-position-events.png "CarouselView con posizioni correnti e precedenti")](interaction-images/current-position-events-large.png#lightbox "CarouselView con posizioni correnti e precedenti")

### <a name="command"></a>Comando

Nell'esempio di codice XAML riportato di seguito viene illustrato un [`CarouselView`](xref:Xamarin.Forms.CarouselView) che utilizza un comando per rispondere alla modifica della proprietà `Position`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PositionChangedCommand="{Binding PositionChangedCommand}"
              PositionChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=Position}">
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionChangedCommandProperty, "PositionChangedCommand");
carouselView.SetBinding(CarouselView.PositionChangedCommandParameterProperty, new Binding("Position", source: RelativeBindingSource.Self));
```

In questo esempio, la proprietà `PositionChangedCommand` viene associata alla proprietà `PositionChangedCommand`, passando il valore della proprietà `Position` come argomento. Il `PositionChangedCommand` può quindi rispondere alla modifica della posizione, come richiesto:

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

In questo esempio, il `PositionChangedCommand` aggiorna gli oggetti in cui sono archiviate le posizioni precedenti e correnti.

## <a name="preset-the-current-item"></a>Imposta il set di elementi corrente

L'elemento corrente in un [`CarouselView`](xref:Xamarin.Forms.CarouselView) può essere impostato a livello di codice impostando la proprietà `CurrentItem` sull'elemento. Nell'esempio di codice XAML riportato di seguito viene illustrato un `CarouselView` che consente di prescegliere l'elemento corrente:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItem="{Binding CurrentItem}">
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemProperty, "CurrentItem");
```

> [!NOTE]
> Per la proprietà `CurrentItem` è presente una modalità di associazione predefinita di `TwoWay`.

I dati della proprietà `CarouselView.CurrentItem` vengono associati alla proprietà `CurrentItem` del modello di visualizzazione connesso, che è di tipo `Monkey`. Per impostazione predefinita, viene utilizzata un'associazione `TwoWay` in modo che, se l'utente modifica l'elemento corrente, il valore della proprietà `CurrentItem` verrà impostato sull'oggetto `Monkey` corrente. La proprietà `CurrentItem` è definita nella classe `MonkeysViewModel`:

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    public Monkey CurrentItem { get; set; }

    public MonkeysViewModel()
    {
        // ...
        CurrentItem = Monkeys.Skip(3).FirstOrDefault();
        OnPropertyChanged("CurrentItem");
    }
}
```

In questo esempio, la proprietà `CurrentItem` è impostata sul quarto elemento nella raccolta `Monkeys`:

[![Screenshot di un CarouselView con elemento preimpostato, in iOS e Android](interaction-images/preset-item.png "CarouselView con elemento preimpostato")](interaction-images/preset-item-large.png#lightbox "CarouselView con elemento preimpostato")

## <a name="preset-the-position"></a>Imposta la posizione

L'elemento visualizzato [`CarouselView`](xref:Xamarin.Forms.CarouselView) può essere impostato a livello di codice impostando la proprietà `Position` sull'indice dell'elemento nella raccolta sottostante. Nell'esempio di codice XAML riportato di seguito viene illustrato un `CarouselView` che imposta l'elemento visualizzato:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              Position="{Binding Position}">
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionProperty, "Position");
```

> [!NOTE]
> Per la proprietà `Position` è presente una modalità di associazione predefinita di `TwoWay`.

I dati della proprietà `CarouselView.Position` vengono associati alla proprietà `Position` del modello di visualizzazione connesso, che è di tipo `int`. Per impostazione predefinita, viene utilizzata un'associazione `TwoWay` in modo che, se l'utente scorre il [`CarouselView`](xref:Xamarin.Forms.CarouselView), il valore della proprietà `Position` verrà impostato sull'indice dell'elemento visualizzato. La proprietà `Position` è definita nella classe `MonkeysViewModel`:

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public int Position { get; set; }

    public MonkeysViewModel()
    {
        // ...
        Position = 3;
        OnPropertyChanged("Position");
    }
}
```

In questo esempio, la proprietà `Position` è impostata sul quarto elemento nella raccolta `Monkeys`:

[![Screenshot di un CarouselView con la posizione preimpostata, in iOS e Android](interaction-images/preset-position.png "CarouselView con posizione predefinita")](interaction-images/preset-position-large.png#lightbox "CarouselView con posizione predefinita")

## <a name="define-visual-states"></a>Definire gli Stati di visualizzazione

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce quattro stati di visualizzazione:

- `CurrentItem` rappresenta lo stato di visualizzazione dell'elemento attualmente visualizzato.
- `PreviousItem` rappresenta lo stato di visualizzazione dell'elemento visualizzato in precedenza.
- `NextItem` rappresenta lo stato di visualizzazione dell'elemento successivo.
- `DefaultItem` rappresenta lo stato di visualizzazione per il resto degli elementi.

Questi stati visivi possono essere utilizzati per avviare modifiche visive agli elementi visualizzati dalla [`CarouselView`](xref:Xamarin.Forms.CarouselView).

Nell'esempio di codice XAML riportato di seguito viene illustrato come definire gli Stati di visualizzazione `CurrentItem`, `PreviousItem`, `NextItem`e `DefaultItem`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="CurrentItem">
                            <VisualState.Setters>
                                <Setter Property="Scale"
                                        Value="1.1" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="PreviousItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="NextItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="DefaultItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.25" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <!-- Item template content -->
                <Frame HasShadow="true">
                    ...
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

In questo esempio, lo stato di visualizzazione `CurrentItem` specifica che l'elemento corrente visualizzato dall' [`CarouselView`](xref:Xamarin.Forms.CarouselView) avrà la proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) modificata dal valore predefinito da 1 a 1,1. Gli Stati di visualizzazione `PreviousItem` e `NextItem` specificano che gli elementi che circondano l'elemento corrente verranno visualizzati con un valore di [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) pari a 0,5. Lo stato di visualizzazione `DefaultItem` specifica che il resto degli elementi visualizzati dall'`CarouselView` verrà visualizzato con un valore di `Opacity` pari a 0,25.

> [!NOTE]
> In alternativa, è possibile definire gli Stati di visualizzazione in un [`Style`](xref:Xamarin.Forms.Style) che dispone di un valore [`TargetType`](xref:Xamarin.Forms.Style.TargetType) proprietà che è il tipo dell'elemento radice del [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), che è impostato come valore della proprietà `ItemTemplate`.

Gli screenshot seguenti mostrano gli Stati di visualizzazione `CurrentItem`, `PreviousItem`e `NextItem`:

[![Screenshot di un CarouselView con gli Stati di visualizzazione, in iOS e Android](interaction-images/visual-states.png "Stati di visualizzazione CarouselView")](interaction-images/visual-states-large.png#lightbox "Stati di visualizzazione CarouselView")

Per ulteriori informazioni sugli stati visivi, vedere [Novell. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="clear-the-current-item"></a>Cancella l'elemento corrente

È possibile cancellare la proprietà `CurrentItem` impostando la proprietà o l'oggetto a cui viene associato, per `null`.

## <a name="disable-bounce"></a>Disabilita rimbalzo

Per impostazione predefinita, [`CarouselView`](xref:Xamarin.Forms.CarouselView) rimbalza gli elementi in base ai limiti del contenuto. Questa operazione può essere disabilitata impostando la proprietà `IsBounceEnabled` su `false`.

## <a name="disable-swipe-interaction"></a>Disabilita interazione swipe

Per impostazione predefinita, [`CarouselView`](xref:Xamarin.Forms.CarouselView) consente agli utenti di spostarsi tra gli elementi usando un gesto di scorrimento rapido. Questa interazione di scorrimento rapido può essere disabilitata impostando la proprietà `IsSwipeEnabled` su `false`.

## <a name="related-links"></a>Collegamenti correlati

- [CarouselView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Gestore dello stato di visualizzazione di Novell. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
