---
title: Novell. Forms-dati CollectionView
description: Un oggetto CollectionView viene popolato con i dati impostando la relativa proprietà ItemsSource su una raccolta che implementa IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/29/2020
ms.openlocfilehash: 1ae290b3fd0e9773d880b29aa9e38ff8b736b82c
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82516823"
---
# <a name="xamarinforms-collectionview-data"></a>Novell. Forms-dati CollectionView

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)include le proprietà seguenti che definiscono i dati da visualizzare e il relativo aspetto:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), di tipo `IEnumerable`, specifica la raccolta di elementi da visualizzare e il cui valore predefinito è `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), specifica il modello da applicare a ogni elemento nella raccolta di elementi da visualizzare.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce una `ItemsUpdatingScrollMode` proprietà che rappresenta il comportamento di scorrimento di `CollectionView` quando vengono aggiunti nuovi elementi. Per altre informazioni su questa proprietà, vedere [controllare la posizione di scorrimento quando vengono aggiunti nuovi elementi](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CollectionView`](xref:Xamarin.Forms.CollectionView)supporta la virtualizzazione dei dati incrementali quando l'utente scorre. Per altre informazioni, vedere [caricare dati in modo incrementale](#load-data-incrementally).

## <a name="populate-a-collectionview-with-data"></a>Popola un oggetto CollectionView con i dati

Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto viene popolato con i dati [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) impostando la relativa proprietà su `IEnumerable`qualsiasi raccolta che implementa. È possibile aggiungere elementi in XAML inizializzando la `ItemsSource` proprietà da una matrice di stringhe:

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
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)genererà un'eccezione se [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) viene aggiornata dal thread dell'interfaccia utente.

Per impostazione predefinita [`CollectionView`](xref:Xamarin.Forms.CollectionView) , Visualizza gli elementi in un elenco verticale, come illustrato nelle schermate seguenti:

[![Screenshot di CollectionView contenente gli elementi di testo in iOS e Android](populate-data-images/text.png "Elementi di testo in un oggetto CollectionView")](populate-data-images/text-large.png#lightbox "Elementi di testo in un oggetto CollectionView")

> [!IMPORTANT]
> Se [`CollectionView`](xref:Xamarin.Forms.CollectionView) è necessario aggiornare quando gli elementi vengono aggiunti, rimossi o modificati nell'insieme sottostante, la raccolta sottostante deve essere una `IEnumerable` raccolta che invia notifiche di modifica della proprietà, ad esempio. `ObservableCollection`

Per informazioni su come modificare il [`CollectionView`](xref:Xamarin.Forms.CollectionView) layout, vedere [Novell. Forms CollectionView layout](layout.md). Per informazioni su come definire l'aspetto di ogni elemento in `CollectionView`, vedere definire l' [aspetto dell'elemento](#define-item-appearance).

### <a name="data-binding"></a>Associazione dati

[`CollectionView`](xref:Xamarin.Forms.CollectionView)può essere popolato con i dati usando data binding per associare [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) la relativa proprietà `IEnumerable` a una raccolta. In XAML, questa operazione viene eseguita con `Binding` l'estensione di markup:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

In questo esempio, i [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) dati della proprietà vengono associati alla `Monkeys` proprietà dell'elemento ViewModel connesso.

> [!NOTE]
> Le associazioni compilate possono essere abilitate per migliorare le prestazioni di data binding nelle applicazioni Novell. Forms. Per altre informazioni, vedere [Binding compilati](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definisci aspetto elemento

L'aspetto di ogni elemento in [`CollectionView`](xref:Xamarin.Forms.CollectionView) può essere definito impostando la [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) proprietà su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

Gli elementi specificati in [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definiscono l'aspetto di ogni elemento nell'elenco. Nell'esempio, il layout all'interno `DataTemplate` di è gestito da [`Grid`](xref:Xamarin.Forms.Grid)un oggetto. Contiene `Grid` un [`Image`](xref:Xamarin.Forms.Image) oggetto e due [`Label`](xref:Xamarin.Forms.Label) oggetti, che tutti sono associati alle proprietà della `Monkey` classe:

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

È [`CollectionView`](xref:Xamarin.Forms.CollectionView) possibile scegliere l'aspetto di ogni elemento in in fase di esecuzione, in base al valore dell'elemento, impostando la [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) proprietà [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) su un oggetto:

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

La [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) proprietà è impostata su un `MonkeyDataTemplateSelector` oggetto. Nell'esempio seguente viene illustrata la `MonkeyDataTemplateSelector` classe:

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

La `MonkeyDataTemplateSelector` classe definisce `AmericanMonkey` le `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà e impostate su modelli di dati diversi. L' `OnSelectTemplate` override restituisce il `AmericanMonkey` modello, che Visualizza il nome e la posizione della scimmia in verde acqua, quando il nome della scimmia contiene "America". Quando il nome della scimmia non contiene "America", `OnSelectTemplate` l'override restituisce `OtherMonkey` il modello, che Visualizza il nome e la posizione della scimmia in argento:

[![Screenshot della selezione del modello di elemento di runtime di CollectionView, in iOS e Android](populate-data-images/datatemplateselector.png "Selezione del modello di elemento di runtime in un CollectionView")](populate-data-images/datatemplateselector-large.png#lightbox "Selezione del modello di elemento di runtime in un CollectionView")

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Create a Novell. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Quando si [`CollectionView`](xref:Xamarin.Forms.CollectionView)USA, non impostare mai l'elemento radice [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) degli oggetti su `ViewCell`un oggetto. Questo comporterà la generazione di un'eccezione `CollectionView` perché non ha un concetto di celle.

## <a name="context-menus"></a>Menu di scelta rapida

[`CollectionView`](xref:Xamarin.Forms.CollectionView)supporta i `SwipeView`menu di scelta rapida per gli elementi di dati tramite, che consente di visualizzare il menu di scelta rapida con un gesto di scorrimento. È `SwipeView` un controllo contenitore che esegue il wrapping di un elemento di contenuto e fornisce voci di menu di scelta rapida per l'elemento di contenuto. Pertanto, i menu di scelta rapida vengono `CollectionView` implementati per `SwipeView` un oggetto creando un oggetto che `SwipeView` definisce il contenuto che esegue il wrapping e le voci del menu di scelta rapida rivelate dal gesto di scorrimento. Questa operazione viene eseguita impostando `SwipeView` come visualizzazione radice nell'oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che definisce l'aspetto di ogni elemento di dati in: `CollectionView`

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

In questo esempio, il `SwipeView` contenuto è un [`Grid`](xref:Xamarin.Forms.Grid) oggetto che definisce l'aspetto di ogni elemento in [`CollectionView`](xref:Xamarin.Forms.CollectionView). Gli elementi swipe vengono usati per eseguire azioni sul `SwipeView` contenuto e vengono rivelati quando il controllo viene sottoposto a scorrimento dal lato sinistro:

[![Screenshot delle voci del menu di scelta rapida CollectionView in iOS e Android](populate-data-images/swipeview.png "CollectionView con voci di menu di scelta rapida SwipeView")](populate-data-images/swipeview-large.png#lightbox "CollectionView con voci di menu di scelta rapida SwipeView")

`SwipeView`supporta quattro direzioni di scorrimento diverse, con la direzione di scorrimento definita dalla raccolta direzionale `SwipeItems` a cui vengono `SwipeItems` aggiunti gli oggetti. Per impostazione predefinita, un elemento swipe viene eseguito quando viene toccato dall'utente. Inoltre, dopo l'esecuzione di un elemento swipe gli elementi swipe sono nascosti e il `SwipeView` contenuto viene nuovamente visualizzato. Tuttavia, è possibile modificare questi comportamenti.

Per ulteriori informazioni sul `SwipeView` controllo, vedere [Novell. Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md).

## <a name="pull-to-refresh"></a>Aggiornamento tramite trascinamento verso il basso

[`CollectionView`](xref:Xamarin.Forms.CollectionView)supporta pull per aggiornare la funzionalità tramite `RefreshView`, che consente la visualizzazione dei dati da aggiornare tramite il pull di un elenco di elementi. `RefreshView` È un controllo contenitore che fornisce il pull per aggiornare la funzionalità al relativo elemento figlio, purché l'elemento figlio supporti il contenuto scorrevole. Pertanto, il pull per l'aggiornamento viene implementato `CollectionView` per un oggetto impostando il valore come `RefreshView`figlio di un oggetto:

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

Quando l'utente avvia un aggiornamento, viene eseguito `ICommand` il definito dalla `Command` proprietà, che dovrebbe aggiornare gli elementi visualizzati. Quando si verifica l'aggiornamento, viene visualizzata una visualizzazione di aggiornamento, che è costituita da un cerchio di avanzamento animato:

[![Screenshot del pull-to-refresh di CollectionView, in iOS e Android](populate-data-images/pull-to-refresh.png "Pull-to-refresh di CollectionView")](populate-data-images/pull-to-refresh-large.png#lightbox "Pull-to-refresh di CollectionView")

Il valore della `RefreshView.IsRefreshing` proprietà indica lo stato corrente dell'oggetto `RefreshView`. Quando un aggiornamento viene attivato dall'utente, questa proprietà passerà automaticamente a `true`. Una volta completato l'aggiornamento, è necessario reimpostare la proprietà `false`su.

Per ulteriori informazioni su `RefreshView`, vedere [Novell. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Caricare i dati in modo incrementale

[`CollectionView`](xref:Xamarin.Forms.CollectionView)supporta la virtualizzazione dei dati incrementali quando l'utente scorre. Questo consente scenari come il caricamento asincrono di una pagina di dati da un servizio Web, quando l'utente scorre. Inoltre, il punto in cui vengono caricati più dati è configurabile, in modo che gli utenti non visualizzino spazio vuoto o siano interrotti dallo scorrimento.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce le proprietà seguenti per controllare il caricamento incrementale dei dati:

- `RemainingItemsThreshold`, di tipo `int`, la soglia di elementi non ancora visibili nell'elenco in cui verrà generato `RemainingItemsThresholdReached` l'evento.
- `RemainingItemsThresholdReachedCommand`, di tipo `ICommand`, che viene eseguito quando `RemainingItemsThreshold` viene raggiunto.
- `RemainingItemsThresholdReachedCommandParameter`, di tipo `object`, ovvero il parametro passato a `RemainingItemsThresholdReachedCommand`.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce inoltre un `RemainingItemsThresholdReached` evento che viene generato quando viene `CollectionView` eseguito lo scorrimento a un numero `RemainingItemsThreshold` molto lungo di elementi che non sono stati visualizzati. Questo evento può essere gestito per caricare altri elementi. Inoltre, quando viene generato `RemainingItemsThresholdReached` l'evento, viene eseguito `RemainingItemsThresholdReachedCommand` l'oggetto, consentendo il caricamento incrementale dei dati in un elemento ViewModel.

Il valore predefinito della `RemainingItemsThreshold` proprietà è-1, che indica che l' `RemainingItemsThresholdReached` evento non verrà mai attivato. Quando il valore della proprietà è 0, `RemainingItemsThresholdReached` l'evento viene generato quando [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) viene visualizzato l'elemento finale in. Per i valori maggiori di 0, `RemainingItemsThresholdReached` l'evento verrà generato quando contiene `ItemsSource` il numero di elementi non ancora sottoposto a scorrimento.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)convalida la `RemainingItemsThreshold` proprietà in modo che il relativo valore sia sempre maggiore o uguale a-1.

Nell'esempio di codice XAML seguente [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene illustrato un che carica i dati in modo incrementale:

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

In questo esempio di codice, `RemainingItemsThresholdReached` l'evento viene generato quando sono presenti 5 elementi non ancora sottoposte a scorrimento e in risposta `OnCollectionViewRemainingItemsThresholdReached` viene eseguito il gestore eventi:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> I dati possono anche essere caricati in modo incrementale `RemainingItemsThresholdReachedCommand` associando `ICommand` a un'implementazione nell'elemento ViewModel.

## <a name="related-links"></a>Link correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Novell. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Novell. Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md)
- [Associazione di dati di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creare un DataTemplateSelector Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
