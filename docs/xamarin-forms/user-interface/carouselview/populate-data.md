---
title: Dati CarouselView di Novell. Forms
description: Un CarouselView viene popolato con i dati impostando la relativa proprietà ItemsSource su una raccolta che implementa IEnumerable.
ms.prod: xamarin
ms.assetid: 20DB2C57-CE3A-4D91-80DC-73AE361A3CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/29/2020
ms.openlocfilehash: cdd77d333ead9b4ff4d2cf29b1e36ee2f287dd22
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517349"
---
# <a name="xamarinforms-carouselview-data"></a>Dati CarouselView di Novell. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)include le proprietà seguenti che definiscono i dati da visualizzare e il relativo aspetto:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), di tipo `IEnumerable`, specifica la raccolta di elementi da visualizzare e il cui valore predefinito è `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), specifica il modello da applicare a ogni elemento nella raccolta di elementi da visualizzare.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce una `ItemsUpdatingScrollMode` proprietà che rappresenta il comportamento di scorrimento di `CarouselView` quando vengono aggiunti nuovi elementi. Per altre informazioni su questa proprietà, vedere [controllare la posizione di scorrimento quando vengono aggiunti nuovi elementi](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CarouselView`](xref:Xamarin.Forms.CarouselView)supporta la virtualizzazione dei dati incrementali quando l'utente scorre. Per altre informazioni, vedere [caricare dati in modo incrementale](#load-data-incrementally).

## <a name="populate-a-carouselview-with-data"></a>Popola un CarouselView con i dati

Un [`CarouselView`](xref:Xamarin.Forms.CarouselView) oggetto viene popolato con i dati [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) impostando la relativa proprietà su `IEnumerable`qualsiasi raccolta che implementa. È possibile aggiungere elementi in XAML inizializzando la `ItemsSource` proprietà da una matrice di stringhe:

```xaml
<CarouselView>
    <CarouselView.ItemsSource>
        <x:Array Type="{x:Type x:String}">
            <x:String>Baboon</x:String>
            <x:String>Capuchin Monkey</x:String>
            <x:String>Blue Monkey</x:String>
            <x:String>Squirrel Monkey</x:String>
            <x:String>Golden Lion Tamarin</x:String>
            <x:String>Howler Monkey</x:String>
            <x:String>Japanese Macaque</x:String>
        </x:Array>
    </CarouselView.ItemsSource>
</CarouselView>
```

> [!NOTE]
> Si noti che l'elemento `x:Array` richiede un attributo `Type` che indica il tipo degli elementi nella matrice.

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.ItemsSource = new string[]
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

> [!IMPORTANT]
> Se [`CarouselView`](xref:Xamarin.Forms.CarouselView) è necessario aggiornare quando gli elementi vengono aggiunti, rimossi o modificati nell'insieme sottostante, la raccolta sottostante deve essere una `IEnumerable` raccolta che invia notifiche di modifica della proprietà, ad esempio. `ObservableCollection`

Per impostazione predefinita [`CarouselView`](xref:Xamarin.Forms.CarouselView) , gli elementi vengono visualizzati orizzontalmente. Gli screenshot seguenti mostrano una `CarouselView` visualizzazione di elementi stringa diversi in iOS e Android:

[![Screenshot di CarouselView contenente gli elementi di testo in iOS e Android](populate-data-images/text.png "Elementi di testo in un CarouselView")](populate-data-images/text-large.png#lightbox "Elementi di testo in un CarouselView")

Per informazioni su come modificare l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) orientamento, vedere [Novell. Forms CarouselView layout](layout.md). Per informazioni su come definire l'aspetto di ogni elemento in `CarouselView`, vedere definire l' [aspetto dell'elemento](#define-item-appearance).

### <a name="data-binding"></a>Associazione dati

[`CarouselView`](xref:Xamarin.Forms.CarouselView)può essere popolato con i dati usando data binding per associare [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) la relativa proprietà `IEnumerable` a una raccolta. In XAML, questa operazione viene eseguita con `Binding` l'estensione di markup:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}" />
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

In questo esempio, i [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) dati della proprietà vengono associati alla `Monkeys` proprietà dell'elemento ViewModel connesso.

> [!NOTE]
> Le associazioni compilate possono essere abilitate per migliorare le prestazioni di data binding nelle applicazioni Novell. Forms. Per altre informazioni, vedere [Binding compilati](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definisci aspetto elemento

L'aspetto di ogni elemento in [`CarouselView`](xref:Xamarin.Forms.CarouselView) può essere definito impostando la [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) proprietà su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

carouselView.ItemTemplate = new DataTemplate(() =>
{
    Label nameLabel = new Label { ... };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Image image = new Image { ... };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label locationLabel = new Label { ... };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Label detailsLabel = new Label { ... };
    detailsLabel.SetBinding(Label.TextProperty, "Details");

    StackLayout stackLayout = new StackLayout
    {
        Children = { nameLabel, image, locationLabel, detailsLabel }
    };

    Frame frame = new Frame { ... };
    StackLayout rootStackLayout = new StackLayout
    {
        Children = { frame }
    };

    return rootStackLayout;
});
```

Gli elementi specificati in [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definiscono l'aspetto di ogni elemento nell'oggetto. `CarouselView` Nell'esempio, il layout all'interno `DataTemplate` di è gestito da [`StackLayout`](xref:Xamarin.Forms.StackLayout)un oggetto e i dati vengono visualizzati con [`Image`](xref:Xamarin.Forms.Image) un oggetto e tre [`Label`](xref:Xamarin.Forms.Label) oggetti, che vengono tutti associati alle proprietà della `Monkey` classe:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

Gli screenshot seguenti mostrano il risultato della creazione di un modello per ogni elemento:

[![Screenshot di CarouselView in cui ogni elemento è basato su modelli, in iOS e Android](populate-data-images/datatemplate.png "Elementi basati su modelli in un CarouselView")](populate-data-images/datatemplate-large.png#lightbox "Elementi basati su modelli in un CarouselView")

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choose-item-appearance-at-runtime"></a>Scegli aspetto elemento in fase di esecuzione

È [`CarouselView`](xref:Xamarin.Forms.CarouselView) possibile scegliere l'aspetto di ogni elemento in in fase di esecuzione, in base al valore dell'elemento, impostando la [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) proprietà [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) su un oggetto:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CarouselViewDemos.Controls"
             x:Class="CarouselViewDemos.Views.HorizontalLayoutDataTemplateSelectorPage">
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

    <CarouselView ItemsSource="{Binding Monkeys}"
                  ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
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

La `MonkeyDataTemplateSelector` classe definisce `AmericanMonkey` le `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà e impostate su modelli di dati diversi. L' `OnSelectTemplate` override restituisce il `AmericanMonkey` modello quando il nome della scimmia contiene "America". Quando il nome della scimmia non contiene "America", `OnSelectTemplate` l'override restituisce `OtherMonkey` il modello che Visualizza i dati in grigio:

[![Screenshot della selezione del modello di elemento del runtime CarouselView, in iOS e Android](populate-data-images/datatemplateselector.png "Selezione del modello di elemento di runtime in un CarouselView")](populate-data-images/datatemplateselector-large.png#lightbox "Selezione del modello di elemento di runtime in un CarouselView")

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Create a Novell. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Quando si [`CarouselView`](xref:Xamarin.Forms.CarouselView)USA, non impostare mai l'elemento radice [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) degli oggetti su `ViewCell`un oggetto. Questo comporterà la generazione di un'eccezione `CarouselView` perché non ha un concetto di celle.

## <a name="display-indicators"></a>Visualizzare gli indicatori

Gli indicatori, che rappresentano il numero di elementi e la posizione corrente `CarouselView`in un oggetto, possono essere visualizzati `CarouselView`accanto a. Questa operazione può essere eseguita con `IndicatorView` il controllo:

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

In questo esempio viene eseguito `IndicatorView` il rendering di sotto `CarouselView`l'oggetto, con un indicatore per ogni elemento `CarouselView`in. `IndicatorView` Viene popolato con i dati impostando `CarouselView.IndicatorView` la proprietà sull' `IndicatorView` oggetto. Ogni indicatore è un cerchio grigio chiaro, mentre l'indicatore che rappresenta l'elemento corrente nel `CarouselView` è grigio scuro:

[![Screenshot di CarouselView e IndicatorView, su iOS e Android](populate-data-images/indicators.png "Cerchi IndicatorView")](populate-data-images/indicators-large.png#lightbox "Cerchi IndicatorView")

> [!IMPORTANT]
> L'impostazione `CarouselView.IndicatorView` della proprietà comporta l' `IndicatorView.Position` associazione della proprietà alla `CarouselView.Position` proprietà e l' `IndicatorView.ItemsSource` associazione della proprietà alla `CarouselView.ItemsSource` proprietà.

Per ulteriori informazioni sugli indicatori, vedere [Novell. Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md).

## <a name="context-menus"></a>Menu di scelta rapida

[`CarouselView`](xref:Xamarin.Forms.CarouselView)supporta i `SwipeView`menu di scelta rapida per gli elementi di dati tramite, che consente di visualizzare il menu di scelta rapida con un gesto di scorrimento. È `SwipeView` un controllo contenitore che esegue il wrapping di un elemento di contenuto e fornisce voci di menu di scelta rapida per l'elemento di contenuto. Pertanto, i menu di scelta rapida vengono `CarouselView` implementati per `SwipeView` un oggetto creando un oggetto che `SwipeView` definisce il contenuto che esegue il wrapping e le voci del menu di scelta rapida rivelate dal gesto di scorrimento. Questa operazione viene eseguita aggiungendo un `SwipeView` oggetto a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che definisce l'aspetto di ogni elemento di dati in `CarouselView`:

```xaml
<CarouselView x:Name="carouselView"
              ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                    <Frame HasShadow="True"
                           BorderColor="DarkGray"
                           CornerRadius="5"
                           Margin="20"
                           HeightRequest="300"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand">
                        <SwipeView>
                            <SwipeView.TopItems>
                                <SwipeItems>
                                    <SwipeItem Text="Favorite"
                                               IconImageSource="favorite.png"
                                               BackgroundColor="LightGreen"
                                               Command="{Binding Source={x:Reference carouselView}, Path=BindingContext.FavoriteCommand}"
                                               CommandParameter="{Binding}" />
                                </SwipeItems>
                            </SwipeView.TopItems>
                            <SwipeView.BottomItems>
                                <SwipeItems>
                                    <SwipeItem Text="Delete"
                                               IconImageSource="delete.png"
                                               BackgroundColor="LightPink"
                                               Command="{Binding Source={x:Reference carouselView}, Path=BindingContext.DeleteCommand}"
                                               CommandParameter="{Binding}" />
                                </SwipeItems>
                            </SwipeView.BottomItems>
                            <StackLayout>
                                <!-- Define item appearance -->
                            </StackLayout>
                        </SwipeView>
                    </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

carouselView.ItemTemplate = new DataTemplate(() =>
{
    StackLayout stackLayout = new StackLayout();
    Frame frame = new Frame { ... };

    SwipeView swipeView = new SwipeView();
    SwipeItem favoriteSwipeItem = new SwipeItem
    {
        Text = "Favorite",
        IconImageSource = "favorite.png",
        BackgroundColor = Color.LightGreen
    };
    favoriteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.FavoriteCommand", source: carouselView));
    favoriteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    SwipeItem deleteSwipeItem = new SwipeItem
    {
        Text = "Delete",
        IconImageSource = "delete.png",
        BackgroundColor = Color.LightPink
    };
    deleteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.DeleteCommand", source: carouselView));
    deleteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    swipeView.TopItems = new SwipeItems { favoriteSwipeItem };
    swipeView.BottomItems = new SwipeItems { deleteSwipeItem };

    StackLayout swipeViewStackLayout = new StackLayout { ... };
    swipeView.Content = swipeViewStackLayout;
    frame.Content = swipeView;
    stackLayout.Children.Add(frame);

    return stackLayout;
});
```

In questo esempio, il `SwipeView` contenuto è un [`StackLayout`](xref:Xamarin.Forms.StackLayout) oggetto che definisce l'aspetto di ogni elemento racchiuso da un [`Frame`](xref:Xamarin.Forms.Frame) oggetto in [`CarouselView`](xref:Xamarin.Forms.CarouselView). Gli elementi swipe vengono usati per eseguire azioni sul `SwipeView` contenuto e vengono rivelati quando il controllo viene sottoposto a scorrimento dalla parte superiore e dalla parte inferiore:

[![Screenshot della voce di menu di scelta rapida CarouselView Bottom, in iOS e Android](populate-data-images/swipeview-bottom.png "CarouselView con voce del menu di scelta rapida SwipeView inferiore")](populate-data-images/swipeview-bottom-large.png#lightbox "CarouselView con voce del menu di scelta rapida SwipeView inferiore")
[![screenshot della voce di menu Top CarouselView in iOS e Android](populate-data-images/swipeview-top.png "CarouselView con la voce del menu di scelta rapida Top SwipeView")](populate-data-images/swipeview-top-large.png#lightbox "CarouselView con la voce del menu di scelta rapida Top SwipeView")

`SwipeView`supporta quattro direzioni di scorrimento diverse, con la direzione di scorrimento definita dalla raccolta direzionale `SwipeItems` a cui vengono `SwipeItems` aggiunti gli oggetti. Per impostazione predefinita, un elemento swipe viene eseguito quando viene toccato dall'utente. Inoltre, dopo l'esecuzione di un elemento swipe gli elementi swipe sono nascosti e il `SwipeView` contenuto viene nuovamente visualizzato. Tuttavia, è possibile modificare questi comportamenti.

Per ulteriori informazioni sul `SwipeView` controllo, vedere [Novell. Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md).

## <a name="pull-to-refresh"></a>Aggiornamento tramite trascinamento verso il basso

[`CarouselView`](xref:Xamarin.Forms.CarouselView)supporta pull per aggiornare la funzionalità tramite `RefreshView`, che consente la visualizzazione dei dati da aggiornare effettuando il pull sugli elementi. `RefreshView` È un controllo contenitore che fornisce il pull per aggiornare la funzionalità al relativo elemento figlio, purché l'elemento figlio supporti il contenuto scorrevole. Pertanto, il pull per l'aggiornamento viene implementato `CarouselView` per un oggetto impostando il valore come `RefreshView`figlio di un oggetto:

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CarouselView ItemsSource="{Binding Animals}">
        ...
    </CarouselView>
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

CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = carouselView;
// ...
```

Quando l'utente avvia un aggiornamento, viene eseguito `ICommand` il definito dalla `Command` proprietà, che dovrebbe aggiornare gli elementi visualizzati. Quando si verifica l'aggiornamento, viene visualizzata una visualizzazione di aggiornamento, che è costituita da un cerchio di avanzamento animato:

[![Screenshot di CarouselView pull-to-refresh, in iOS e Android](populate-data-images/pull-to-refresh.png "Pull-to-refresh CarouselView")](populate-data-images/pull-to-refresh-large.png#lightbox "Pull-to-refresh CarouselView")

Il valore della `RefreshView.IsRefreshing` proprietà indica lo stato corrente dell'oggetto `RefreshView`. Quando un aggiornamento viene attivato dall'utente, questa proprietà passerà automaticamente a `true`. Una volta completato l'aggiornamento, è necessario reimpostare la proprietà `false`su.

Per ulteriori informazioni su `RefreshView`, vedere [Novell. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Caricare i dati in modo incrementale

[`CarouselView`](xref:Xamarin.Forms.CarouselView)supporta la virtualizzazione dei dati incrementali quando l'utente scorre. Questo consente scenari come il caricamento asincrono di una pagina di dati da un servizio Web, quando l'utente scorre. Inoltre, il punto in cui vengono caricati più dati è configurabile, in modo che gli utenti non visualizzino spazio vuoto o siano interrotti dallo scorrimento.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce le proprietà seguenti per controllare il caricamento incrementale dei dati:

- `RemainingItemsThreshold`, di tipo `int`, la soglia di elementi non ancora visibili nell'elenco in cui verrà generato `RemainingItemsThresholdReached` l'evento.
- `RemainingItemsThresholdReachedCommand`, di tipo `ICommand`, che viene eseguito quando `RemainingItemsThreshold` viene raggiunto.
- `RemainingItemsThresholdReachedCommandParameter`, di tipo `object`, ovvero il parametro passato a `RemainingItemsThresholdReachedCommand`.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce inoltre un `RemainingItemsThresholdReached` evento che viene generato quando viene `CarouselView` eseguito lo scorrimento a un numero `RemainingItemsThreshold` molto lungo di elementi che non sono stati visualizzati. Questo evento può essere gestito per caricare altri elementi. Inoltre, quando viene generato `RemainingItemsThresholdReached` l'evento, viene eseguito `RemainingItemsThresholdReachedCommand` l'oggetto, consentendo il caricamento incrementale dei dati in un elemento ViewModel.

Il valore predefinito della `RemainingItemsThreshold` proprietà è-1, che indica che l' `RemainingItemsThresholdReached` evento non verrà mai attivato. Quando il valore della proprietà è 0, `RemainingItemsThresholdReached` l'evento viene generato quando [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) viene visualizzato l'elemento finale in. Per i valori maggiori di 0, `RemainingItemsThresholdReached` l'evento verrà generato quando contiene `ItemsSource` il numero di elementi non ancora sottoposto a scorrimento.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)convalida la `RemainingItemsThreshold` proprietà in modo che il relativo valore sia sempre maggiore o uguale a-1.

Nell'esempio di codice XAML seguente [`CarouselView`](xref:Xamarin.Forms.CarouselView) viene illustrato un che carica i dati in modo incrementale:

```xaml
<CarouselView ItemsSource="{Binding Animals}"
              RemainingItemsThreshold="2"
              RemainingItemsThresholdReached="OnCarouselViewRemainingItemsThresholdReached"
              RemainingItemsThresholdReachedCommand="{Binding LoadMoreDataCommand}">
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView
{
    RemainingItemsThreshold = 2
};
carouselView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

In questo esempio di codice, `RemainingItemsThresholdReached` l'evento viene generato quando sono presenti 2 elementi non ancora sottoposto a scorrimento e in risposta viene `OnCollectionViewRemainingItemsThresholdReached` eseguito il gestore eventi:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> I dati possono anche essere caricati in modo incrementale `RemainingItemsThresholdReachedCommand` associando `ICommand` a un'implementazione nell'elemento ViewModel.

## <a name="related-links"></a>Link correlati

- [CarouselView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Novell. Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md)
- [Novell. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Associazione di dati di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creare un DataTemplateSelector Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
