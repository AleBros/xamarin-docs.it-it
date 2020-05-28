---
title: Xamarin.FormsInterazione CarouselView
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 57c501c0f789ce448d8381cbbccb46666cf06305
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137410"
---
# <a name="xamarinforms-carouselview-interaction"></a>Xamarin.FormsInterazione CarouselView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce le proprietà seguenti che controllano l'interazione dell'utente:

- `CurrentItem`, di tipo `object` , l'elemento corrente da visualizzare. Questa proprietà ha una modalità di associazione predefinita `TwoWay` e ha un `null` valore quando non sono presenti dati da visualizzare.
- `CurrentItemChangedCommand`, di tipo `ICommand` , che viene eseguito quando viene modificato l'elemento corrente.
- `CurrentItemChangedCommandParameter`, di tipo `object`, ovvero il parametro passato a `CurrentItemChangedCommand`.
- `IsBounceEnabled`, di tipo `bool` , che specifica se l'oggetto `CarouselView` rimbalzerà a un limite del contenuto. Il valore predefinito è `true`.
- `IsSwipeEnabled`, di tipo `bool` , che determina se un movimento di scorrimento rapido cambierà l'elemento visualizzato. Il valore predefinito è `true`.
- `Position`, di tipo `int` , l'indice dell'elemento corrente nella raccolta sottostante. Questa proprietà ha una modalità di associazione predefinita `TwoWay` e ha un valore 0 quando non sono presenti dati da visualizzare.
- `PositionChangedCommand`, di tipo `ICommand` , che viene eseguito quando cambia la posizione.
- `PositionChangedCommandParameter`, di tipo `object`, ovvero il parametro passato a `PositionChangedCommand`.
- `VisibleViews`, di tipo `ObservableCollection<View>` , che è una proprietà di sola lettura che contiene gli oggetti per gli elementi attualmente visibili.

Tutte queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce un `CurrentItemChanged` evento generato quando la `CurrentItem` proprietà viene modificata, a causa dello scorrimento utente o quando un'applicazione imposta la proprietà. L' `CurrentItemChangedEventArgs` oggetto che accompagna l' `CurrentItemChanged` evento è costituito da due proprietà, entrambe di tipo `object` :

- `PreviousItem`: elemento precedente, dopo la modifica della proprietà.
- `CurrentItem`: elemento corrente, dopo la modifica della proprietà.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce anche un `PositionChanged` evento generato quando la `Position` proprietà viene modificata, a causa dello scorrimento utente o quando un'applicazione imposta la proprietà. L' `PositionChangedEventArgs` oggetto che accompagna l' `PositionChanged` evento è costituito da due proprietà, entrambe di tipo `int` :

- `PreviousPosition`: posizione precedente, dopo la modifica della proprietà.
- `CurrentPosition`: posizione corrente, dopo la modifica della proprietà.

## <a name="respond-to-the-current-item-changing"></a>Rispondi alla modifica dell'elemento corrente

Quando l'elemento attualmente visualizzato viene modificato, la `CurrentItem` proprietà viene impostata sul valore dell'elemento. Quando questa proprietà viene modificata, `CurrentItemChangedCommand` viene eseguita con il valore dell'oggetto `CurrentItemChangedCommandParameter` passato a `ICommand` . La `Position` proprietà viene quindi aggiornata e l' `CurrentItemChanged` evento viene generato.

> [!IMPORTANT]
> La `Position` proprietà viene modificata quando la `CurrentItem` proprietà viene modificata. Questo comporterà l' `PositionChangedCommand` esecuzione di e la `PositionChanged` generazione dell'evento.

### <a name="event"></a>Evento

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) che utilizza un gestore eventi per rispondere alla modifica dell'elemento corrente:

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

In questo esempio, il `OnCurrentItemChanged` gestore eventi viene eseguito quando viene `CurrentItemChanged` generato l'evento:

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

In questo esempio, il `OnCurrentItemChanged` gestore eventi espone gli elementi precedenti e correnti:

[![Screenshot di un CarouselView con elementi precedenti e correnti, in iOS e Android](interaction-images/current-item-events.png "CarouselView con elementi correnti e precedenti")](interaction-images/current-item-events-large.png#lightbox "CarouselView con elementi correnti e precedenti")

### <a name="command"></a>Comando

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) che utilizza un comando per rispondere alla modifica dell'elemento corrente:

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

In questo esempio la `CurrentItemChangedCommand` proprietà viene associata alla `ItemChangedCommand` proprietà, passando il valore della `CurrentItem` proprietà come argomento. `ItemChangedCommand`Può quindi rispondere alla modifica dell'elemento corrente, come richiesto:

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

In questo esempio, gli `ItemChangedCommand` oggetti di Aggiorna che archiviano gli elementi precedenti e correnti.

## <a name="respond-to-the-position-changing"></a>Rispondere alla modifica della posizione

Quando l'elemento attualmente visualizzato viene modificato, la `Position` proprietà viene impostata sull'indice dell'elemento corrente nella raccolta sottostante. Quando questa proprietà viene modificata, `PositionChangedCommand` viene eseguita con il valore dell'oggetto `PositionChangedCommandParameter` passato a `ICommand` . L' `PositionChanged` evento viene quindi attivato. Se la `Position` proprietà è stata modificata a livello di codice, verrà eseguito lo [`CarouselView`](xref:Xamarin.Forms.CarouselView) scorrimento dell'elemento che corrisponde al `Position` valore.

> [!NOTE]
> Se si imposta la `Position` proprietà su 0, il primo elemento della raccolta sottostante verrà visualizzato.

### <a name="event"></a>Evento

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) che utilizza un gestore eventi per rispondere alla `Position` modifica della proprietà:

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

In questo esempio, il `OnPositionChanged` gestore eventi viene eseguito quando viene `PositionChanged` generato l'evento:

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

In questo esempio, il `OnCurrentItemChanged` gestore eventi espone le posizioni precedenti e correnti:

[![Screenshot di un CarouselView con posizioni precedenti e correnti, in iOS e Android](interaction-images/current-position-events.png "CarouselView con posizioni correnti e precedenti")](interaction-images/current-position-events-large.png#lightbox "CarouselView con posizioni correnti e precedenti")

### <a name="command"></a>Comando

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) che utilizza un comando per rispondere alla `Position` modifica della proprietà:

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

In questo esempio la `PositionChangedCommand` proprietà viene associata alla `PositionChangedCommand` proprietà, passando il valore della `Position` proprietà come argomento. `PositionChangedCommand`Può quindi rispondere alla modifica della posizione, come richiesto:

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

In questo esempio vengono `PositionChangedCommand` aggiornati gli oggetti in cui sono archiviate le posizioni precedenti e correnti.

## <a name="preset-the-current-item"></a>Imposta il set di elementi corrente

L'elemento corrente in un oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) può essere impostato a livello di codice impostando la `CurrentItem` proprietà sull'elemento. Nell'esempio di codice XAML riportato di seguito viene illustrato un `CarouselView` oggetto che precede la scelta dell'elemento corrente:

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
> La `CurrentItem` proprietà dispone di una modalità di associazione predefinita `TwoWay` .

I `CarouselView.CurrentItem` dati della proprietà vengono associati alla `CurrentItem` proprietà del modello di visualizzazione connesso, che è di tipo `Monkey` . Per impostazione predefinita, `TwoWay` viene utilizzata un'associazione in modo che, se l'utente modifica l'elemento corrente, il valore della `CurrentItem` proprietà verrà impostato sull' `Monkey` oggetto corrente. La `CurrentItem` proprietà è definita nella `MonkeysViewModel` classe:

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

In questo esempio la `CurrentItem` proprietà viene impostata sul quarto elemento della `Monkeys` raccolta:

[![Screenshot di un CarouselView con elemento preimpostato, in iOS e Android](interaction-images/preset-item.png "CarouselView con elemento preimpostato")](interaction-images/preset-item-large.png#lightbox "CarouselView con elemento preimpostato")

## <a name="preset-the-position"></a>Imposta la posizione

L'elemento visualizzato [`CarouselView`](xref:Xamarin.Forms.CarouselView) può essere impostato a livello di codice impostando la `Position` proprietà sull'indice dell'elemento nella raccolta sottostante. Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto `CarouselView` che imposta l'elemento visualizzato:

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
> La `Position` proprietà dispone di una modalità di associazione predefinita `TwoWay` .

I `CarouselView.Position` dati della proprietà vengono associati alla `Position` proprietà del modello di visualizzazione connesso, che è di tipo `int` . Per impostazione predefinita, `TwoWay` viene utilizzata un'associazione in modo che, se l'utente scorre l'oggetto [`CarouselView`](xref:Xamarin.Forms.CarouselView) , il valore della `Position` proprietà verrà impostato sull'indice dell'elemento visualizzato. La `Position` proprietà è definita nella `MonkeysViewModel` classe:

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

In questo esempio la `Position` proprietà viene impostata sul quarto elemento della `Monkeys` raccolta:

[![Screenshot di un CarouselView con la posizione preimpostata, in iOS e Android](interaction-images/preset-position.png "CarouselView con posizione predefinita")](interaction-images/preset-position-large.png#lightbox "CarouselView con posizione predefinita")

## <a name="define-visual-states"></a>Definire gli Stati di visualizzazione

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce quattro stati di visualizzazione:

- `CurrentItem`rappresenta lo stato di visualizzazione per l'elemento attualmente visualizzato.
- `PreviousItem`rappresenta lo stato di visualizzazione dell'elemento visualizzato in precedenza.
- `NextItem`rappresenta lo stato di visualizzazione dell'elemento successivo.
- `DefaultItem`rappresenta lo stato di visualizzazione per il resto degli elementi.

Questi stati visivi possono essere utilizzati per avviare modifiche visive agli elementi visualizzati da [`CarouselView`](xref:Xamarin.Forms.CarouselView) .

Nell'esempio di codice XAML riportato di seguito viene illustrato come definire gli `CurrentItem` `PreviousItem` Stati di visualizzazione,, `NextItem` e `DefaultItem` :

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

In questo esempio, lo `CurrentItem` stato di visualizzazione specifica che l'elemento corrente visualizzato da [`CarouselView`](xref:Xamarin.Forms.CarouselView) avrà la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà modificata rispetto al valore predefinito da 1 a 1,1. Gli `PreviousItem` `NextItem` Stati di visualizzazione e specificano che gli elementi che circondano l'elemento corrente verranno visualizzati con un [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) valore di 0,5. Lo `DefaultItem` stato di visualizzazione specifica che il resto degli elementi visualizzati dall'oggetto `CarouselView` verrà visualizzato con un `Opacity` valore di 0,25.

> [!NOTE]
> In alternativa, è possibile definire gli Stati di visualizzazione in un oggetto [`Style`](xref:Xamarin.Forms.Style) con un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valore della proprietà che è il tipo dell'elemento radice di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , che è impostato come `ItemTemplate` valore della proprietà.

Gli screenshot seguenti mostrano gli `CurrentItem` Stati di `PreviousItem` visualizzazione, e `NextItem` :

[![Screenshot di un CarouselView con gli Stati di visualizzazione, in iOS e Android](interaction-images/visual-states.png "Stati di visualizzazione CarouselView")](interaction-images/visual-states-large.png#lightbox "Stati di visualizzazione CarouselView")

Per ulteriori informazioni sugli stati visivi, vedere [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="clear-the-current-item"></a>Cancella l'elemento corrente

La `CurrentItem` proprietà può essere cancellata impostando la proprietà o l'oggetto a cui è associata `null` .

## <a name="disable-bounce"></a>Disabilita rimbalzo

Per impostazione predefinita, [`CarouselView`](xref:Xamarin.Forms.CarouselView) rimbalza gli elementi in base ai limiti del contenuto. Questa operazione può essere disabilitata impostando la `IsBounceEnabled` proprietà su `false` .

## <a name="disable-swipe-interaction"></a>Disabilita interazione swipe

Per impostazione predefinita, [`CarouselView`](xref:Xamarin.Forms.CarouselView) consente agli utenti di spostarsi tra gli elementi usando un gesto di scorrimento rapido. Questa interazione di scorrimento rapido può essere disabilitata impostando la `IsSwipeEnabled` proprietà su `false` .

## <a name="related-links"></a>Collegamenti correlati

- [CarouselView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.FormsGestione stato di visualizzazione](~/xamarin-forms/user-interface/visual-state-manager.md)
