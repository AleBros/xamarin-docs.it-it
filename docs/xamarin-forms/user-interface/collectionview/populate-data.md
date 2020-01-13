---
title: Xamarin.Forms-dati CollectionView
description: Un oggetto CollectionView viene popolato con i dati impostando la relativa proprietà ItemsSource su una raccolta che implementa IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 9442f7878d9290946fabb7bfc5dee77a828228c7
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488173"
---
# <a name="xamarinforms-collectionview-data"></a>Xamarin.Forms-dati CollectionView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) include le proprietà seguenti che definiscono i dati da visualizzare e il relativo aspetto:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), di tipo `IEnumerable`, specifica la raccolta di elementi da visualizzare e ha un valore predefinito di `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), specifica il modello da applicare a ogni elemento nella raccolta di elementi da visualizzare.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce una proprietà `ItemsUpdatingScrollMode` che rappresenta il comportamento di scorrimento del `CollectionView` quando vengono aggiunti nuovi elementi. Per altre informazioni su questa proprietà, vedere [controllare la posizione di scorrimento quando vengono aggiunti nuovi elementi](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) inoltre possibile caricare i dati in modo incrementale mentre l'utente scorre. Per altre informazioni, vedere [caricare dati in modo incrementale](#load-data-incrementally).

## <a name="populate-a-collectionview-with-data"></a>Popola un oggetto CollectionView con i dati

Una [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene popolata con i dati impostando la relativa proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) su qualsiasi raccolta che implementi `IEnumerable`. È possibile aggiungere elementi in XAML inizializzando la proprietà `ItemsSource` da una matrice di stringhe:

```xaml
<CollectionView>
  <CollectionView.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </CollectionView.ItemsSource>
</CollectionView>
```

> [!NOTE]
> Si noti che l'elemento `x:Array` richiede un attributo `Type` che indica il tipo degli elementi nella matrice.

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.ItemsSource = new string[]
{
    "Baboon",
    "Capuchin Monkey",
    "Blue Monkey",
    "Squirrel Monkey",
    "Golden Lion Tamarin",
    "Howler Monkey",
    "Japanese Macaque"
};
```

> [!WARNING]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) genererà un'eccezione se il relativo [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) viene aggiornato dal thread dell'interfaccia utente.

Per impostazione predefinita, [`CollectionView`](xref:Xamarin.Forms.CollectionView) Visualizza gli elementi in un elenco verticale, come illustrato nelle schermate seguenti:

[![Screenshot di CollectionView contenente gli elementi di testo in iOS e Android](populate-data-images/text.png "Elementi di testo in un oggetto CollectionView")](populate-data-images/text-large.png#lightbox "Elementi di testo in un oggetto CollectionView")

> [!IMPORTANT]
> Se il [`CollectionView`](xref:Xamarin.Forms.CollectionView) è necessario per l'aggiornamento quando gli elementi vengono aggiunti, rimossi o modificati nella raccolta sottostante, la raccolta sottostante deve essere una raccolta di `IEnumerable` che invia le notifiche di modifica delle proprietà, ad esempio `ObservableCollection`.

Per informazioni su come modificare il layout di [`CollectionView`](xref:Xamarin.Forms.CollectionView) , vedere [Xamarin.Forms CollectionView layout](layout.md). Per informazioni su come definire l'aspetto di ogni elemento nell'`CollectionView`, vedere definire l' [aspetto dell'elemento](#define-item-appearance).

### <a name="data-binding"></a>Associazione dati

[`CollectionView`](xref:Xamarin.Forms.CollectionView) possono essere popolati con i dati utilizzando data binding per associare la relativa proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) a una raccolta `IEnumerable`. In XAML, questa operazione viene eseguita con l'estensione di markup `Binding`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

In questo esempio, i dati della proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) vengono associati alla proprietà `Monkeys` dell'elemento ViewModel connesso.

> [!NOTE]
> Le associazioni compilate possono essere abilitate per migliorare le prestazioni di data binding nelle applicazioni Xamarin.Forms. Per altre informazioni, vedere [Binding compilati](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definisci aspetto elemento

È possibile definire l'aspetto di ogni elemento nell' [`CollectionView`](xref:Xamarin.Forms.CollectionView) impostando la proprietà [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    Grid grid = new Grid { Padding = 10 };
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });

    Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 60, WidthRequest = 60 };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Label locationLabel = new Label { FontAttributes = FontAttributes.Italic, VerticalOptions = LayoutOptions.End };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Grid.SetRowSpan(image, 2);

    grid.Children.Add(image);
    grid.Children.Add(nameLabel, 1, 0);
    grid.Children.Add(locationLabel, 1, 1);

    return grid;
});
```

Gli elementi specificati nel [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definiscono l'aspetto di ogni elemento nell'elenco. Nell'esempio, il layout all'interno del `DataTemplate` è gestito da una [`Grid`](xref:Xamarin.Forms.Grid). Il `Grid` contiene un oggetto [`Image`](xref:Xamarin.Forms.Image) e due oggetti [`Label`](xref:Xamarin.Forms.Label) , che tutti vengono associati alle proprietà della classe `Monkey`:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

Gli screenshot seguenti mostrano il risultato della creazione di un modello per ogni elemento nell'elenco:

[![Screenshot di CollectionView in cui ogni elemento è basato su modelli, in iOS e Android](populate-data-images/datatemplate.png "Elementi basati su modelli in un oggetto CollectionView")](populate-data-images/datatemplate-large.png#lightbox "Elementi basati su modelli in un oggetto CollectionView")

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Scegli aspetto elemento in fase di esecuzione

È possibile scegliere l'aspetto di ogni elemento nell' [`CollectionView`](xref:Xamarin.Forms.CollectionView) in fase di esecuzione, in base al valore dell'elemento, impostando la proprietà [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) su un oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AmericanMonkeyTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="OtherMonkeyTemplate">
            ...
        </DataTemplate>

        <controls:MonkeyDataTemplateSelector x:Key="MonkeySelector"
                                             AmericanMonkey="{StaticResource AmericanMonkeyTemplate}"
                                             OtherMonkey="{StaticResource OtherMonkeyTemplate}" />
    </ContentPage.Resources>

    <CollectionView ItemsSource="{Binding Monkeys}"
                    ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

La proprietà [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) è impostata su un oggetto `MonkeyDataTemplateSelector`. Nell'esempio seguente viene illustrata la classe `MonkeyDataTemplateSelector`:

```csharp
public class MonkeyDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate AmericanMonkey { get; set; }
    public DataTemplate OtherMonkey { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Monkey)item).Location.Contains("America") ? AmericanMonkey : OtherMonkey;
    }
}
```

La classe `MonkeyDataTemplateSelector` definisce `AmericanMonkey` e `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà impostate su modelli di dati diversi. L'override del `OnSelectTemplate` restituisce il modello di `AmericanMonkey`, che Visualizza il nome e la posizione della scimmia in verde acqua, quando il nome della scimmia contiene "America". Quando il nome della scimmia non contiene "America", l'override del `OnSelectTemplate` restituisce il modello di `OtherMonkey`, che Visualizza il nome e la posizione della scimmia in argento:

[![Screenshot della selezione del modello di elemento di runtime di CollectionView, in iOS e Android](populate-data-images/datatemplateselector.png "Selezione del modello di elemento di runtime in un CollectionView")](populate-data-images/datatemplateselector-large.png#lightbox "Selezione del modello di elemento di runtime in un CollectionView")

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Create a Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Quando si usa [`CollectionView`](xref:Xamarin.Forms.CollectionView), non impostare mai l'elemento radice degli oggetti [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) su un `ViewCell`. Questa operazione comporterà la generazione di un'eccezione in quanto `CollectionView` non ha alcun concetto di celle.

## <a name="context-menus"></a>Menu di scelta rapida

[`CollectionView`](xref:Xamarin.Forms.CollectionView) supporta i menu di scelta rapida per gli elementi di dati tramite l'`SwipeView`, che consente di visualizzare il menu di scelta rapida con un gesto di scorrimento rapido. Il `SwipeView` è un controllo contenitore che esegue il wrapping di un elemento di contenuto e fornisce voci di menu di scelta rapida per l'elemento di contenuto. Pertanto, i menu di scelta rapida vengono implementati per un `CollectionView` creando una `SwipeView` che definisce il contenuto di cui il `SwipeView` esegue il wrapping e le voci del menu di scelta rapida rivelate dal gesto di scorrimento. Questa operazione viene eseguita impostando il `SwipeView` come visualizzazione radice nell' [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che definisce l'aspetto di ogni elemento di dati nella `CollectionView`:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <SwipeView>
                <SwipeView.LeftItems>
                    <SwipeItems>
                        <SwipeItem Text="Favorite"
                                   IconImageSource="favorite.png"
                                   BackgroundColor="LightGreen"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.FavoriteCommand}"
                                   CommandParameter="{Binding}" />
                        <SwipeItem Text="Delete"
                                   IconImageSource="delete.png"
                                   BackgroundColor="LightPink"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.DeleteCommand}"
                                   CommandParameter="{Binding}" />
                    </SwipeItems>
                </SwipeView.LeftItems>
                <Grid BackgroundColor="White"
                      Padding="10">
                    <!-- Define item appearance -->
                </Grid>
            </SwipeView>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    // Define item appearance
    Grid grid = new Grid { Padding = 10, BackgroundColor = Color.White };
    // ...

    SwipeView swipeView = new SwipeView();
    SwipeItem favoriteSwipeItem = new SwipeItem
    {
        Text = "Favorite",
        IconImageSource = "favorite.png",
        BackgroundColor = Color.LightGreen
    };
    favoriteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.FavoriteCommand", source: collectionView));
    favoriteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    SwipeItem deleteSwipeItem = new SwipeItem
    {
        Text = "Delete",
        IconImageSource = "delete.png",
        BackgroundColor = Color.LightPink
    };
    deleteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.DeleteCommand", source: collectionView));
    deleteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    swipeView.LeftItems = new SwipeItems { favoriteSwipeItem, deleteSwipeItem };
    swipeView.Content = grid;    
    return swipeView;
});
```

In questo esempio, il contenuto del `SwipeView` è un [`Grid`](xref:Xamarin.Forms.Grid) che definisce l'aspetto di ogni elemento nella [`CollectionView`](xref:Xamarin.Forms.CollectionView). Gli elementi swipe vengono usati per eseguire azioni sul contenuto `SwipeView` e vengono rivelati quando il controllo viene sottoposto a scorrimento dal lato sinistro:

[![Screenshot delle voci del menu di scelta rapida CollectionView in iOS e Android](populate-data-images/swipeview.png "CollectionView con voci di menu di scelta rapida SwipeView")](populate-data-images/swipeview-large.png#lightbox "CollectionView con voci di menu di scelta rapida SwipeView")

`SwipeView` supporta quattro direzioni di scorrimento diverse, con la direzione di scorrimento definita dalla raccolta di `SwipeItems` direzionali a cui vengono aggiunti gli oggetti di `SwipeItems`. Per impostazione predefinita, un elemento swipe viene eseguito quando viene toccato dall'utente. Inoltre, dopo l'esecuzione di un elemento swipe gli elementi swipe sono nascosti e il contenuto del `SwipeView` viene nuovamente visualizzato. Tuttavia, è possibile modificare questi comportamenti.

Per ulteriori informazioni sul controllo `SwipeView`, vedere [Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md).

## <a name="pull-to-refresh"></a>Aggiornamento tramite trascinamento verso il basso

[`CollectionView`](xref:Xamarin.Forms.CollectionView) supporta il pull per aggiornare la funzionalità tramite il `RefreshView`, che consente di visualizzare i dati da aggiornare effettuando il pull di un elenco di elementi. Il `RefreshView` è un controllo contenitore che fornisce il pull per aggiornare la funzionalità al relativo elemento figlio, purché l'elemento figlio supporti il contenuto scorrevole. Pertanto, il pull per l'aggiornamento viene implementato per un `CollectionView` impostando il valore come figlio di un `RefreshView`:

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CollectionView ItemsSource="{Binding Animals}">
        ...
    </CollectionView>
</RefreshView>
```

Il codice C# equivalente è il seguente:

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = collectionView;
// ...
```

Quando l'utente avvia un aggiornamento, viene eseguito il `ICommand` definito dalla proprietà `Command`, che dovrebbe aggiornare gli elementi visualizzati. Quando si verifica l'aggiornamento, viene visualizzata una visualizzazione di aggiornamento, che è costituita da un cerchio di avanzamento animato:

[![Screenshot del pull-to-refresh di CollectionView, in iOS e Android](populate-data-images/pull-to-refresh.png "Pull-to-refresh di CollectionView")](populate-data-images/pull-to-refresh-large.png#lightbox "Pull-to-refresh di CollectionView")

Il valore della proprietà `RefreshView.IsRefreshing` indica lo stato corrente del `RefreshView`. Quando un aggiornamento viene attivato dall'utente, questa proprietà passerà automaticamente a `true`. Una volta completato l'aggiornamento, è necessario reimpostare la proprietà su `false`.

Per ulteriori informazioni su `RefreshView`, vedere [Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Caricare i dati in modo incrementale

[`CollectionView`](xref:Xamarin.Forms.CollectionView) supporta il caricamento incrementale dei dati quando gli utenti scorrono gli elementi. Questo consente scenari come il caricamento asincrono di una pagina di dati da un servizio Web, quando l'utente scorre. Inoltre, il punto in cui vengono caricati più dati è configurabile, in modo che gli utenti non visualizzino spazio vuoto o siano interrotti dallo scorrimento.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce le proprietà seguenti per controllare il caricamento incrementale dei dati:

- `RemainingItemsThreshold`, di tipo `int`, la soglia di elementi non ancora visibili nell'elenco in cui verrà generato l'evento di `RemainingItemsThresholdReached`.
- `RemainingItemsThresholdReachedCommand`, di tipo `ICommand`, che viene eseguito quando viene raggiunto il `RemainingItemsThreshold`.
- `RemainingItemsThresholdReachedCommandParameter`, di tipo `object`, ovvero il parametro passato a `RemainingItemsThresholdReachedCommand`.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce anche un evento di `RemainingItemsThresholdReached` che viene generato quando viene eseguito lo scorrimento della `CollectionView` a un numero molto lungo che `RemainingItemsThreshold` elementi non sono stati visualizzati. Questo evento può essere gestito per caricare altri elementi. Inoltre, quando viene generato l'evento `RemainingItemsThresholdReached`, viene eseguito il `RemainingItemsThresholdReachedCommand`, consentendo il caricamento incrementale dei dati in un elemento ViewModel.

Il valore predefinito della proprietà `RemainingItemsThreshold` è-1, che indica che l'evento `RemainingItemsThresholdReached` non verrà mai generato. Quando il valore della proprietà è 0, l'evento `RemainingItemsThresholdReached` viene generato quando viene visualizzato l'elemento finale nel [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) . Per i valori maggiori di 0, l'evento `RemainingItemsThresholdReached` viene generato quando il `ItemsSource` contiene il numero di elementi non ancora a scorrimento.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) convalida la proprietà `RemainingItemsThreshold` in modo che il relativo valore sia sempre maggiore o uguale a-1.

Nell'esempio di codice XAML seguente viene illustrato un [`CollectionView`](xref:Xamarin.Forms.CollectionView) che carica i dati in modo incrementale:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                RemainingItemsThreshold="5"
                RemainingItemsThresholdReached="OnCollectionViewRemainingItemsThresholdReached">
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    RemainingItemsThreshold = 5
};
collectionView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

In questo esempio di codice, l'evento `RemainingItemsThresholdReached` viene generato quando sono presenti 5 elementi non ancora sottoposte a scorrimento e in risposta viene eseguito il gestore dell'evento `OnCollectionViewRemainingItemsThresholdReached`:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> I dati possono anche essere caricati in modo incrementale associando il `RemainingItemsThresholdReachedCommand` a un'implementazione di `ICommand` nell'elemento ViewModel.

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md)
- [Data Binding Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creare un DataTemplateSelector Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
