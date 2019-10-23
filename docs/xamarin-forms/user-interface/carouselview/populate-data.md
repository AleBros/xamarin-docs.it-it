---
title: Dati CarouselView di Novell. Forms
description: Un CarouselView viene popolato con i dati impostando la relativa proprietà ItemsSource su una raccolta che implementa IEnumerable.
ms.prod: xamarin
ms.assetid: 20DB2C57-CE3A-4D91-80DC-73AE361A3CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/02/2019
ms.openlocfilehash: 0ad31bc6f84ae633a9a18592a00670703db19df9
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697612"
---
# <a name="xamarinforms-carouselview-data"></a>Dati CarouselView di Novell. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) include le proprietà seguenti che definiscono i dati da visualizzare e il relativo aspetto:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), di tipo `IEnumerable`, specifica la raccolta di elementi da visualizzare e ha un valore predefinito di `null`.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), specifica il modello da applicare a ogni elemento nella raccolta di elementi da visualizzare.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce una proprietà `ItemsUpdatingScrollMode` che rappresenta il comportamento di scorrimento del `CarouselView` quando vengono aggiunti nuovi elementi. Per altre informazioni su questa proprietà, vedere [controllare la posizione di scorrimento quando vengono aggiunti nuovi elementi](scrolling.md#control-scroll-position-when-new-items-are-added).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) inoltre possibile caricare i dati in modo incrementale mentre l'utente scorre. Per altre informazioni, vedere [caricare dati in modo incrementale](#load-data-incrementally).

## <a name="populate-a-carouselview-with-data"></a>Popola un CarouselView con i dati

Una [`CarouselView`](xref:Xamarin.Forms.CarouselView) viene popolata con i dati impostando la relativa proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) su qualsiasi raccolta che implementi `IEnumerable`. È possibile aggiungere elementi in XAML inizializzando la proprietà `ItemsSource` da una matrice di stringhe:

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
> Se il [`CarouselView`](xref:Xamarin.Forms.CarouselView) è necessario per l'aggiornamento quando gli elementi vengono aggiunti, rimossi o modificati nella raccolta sottostante, la raccolta sottostante deve essere una raccolta di `IEnumerable` che invia le notifiche di modifica delle proprietà, ad esempio `ObservableCollection`.

Per impostazione predefinita, [`CarouselView`](xref:Xamarin.Forms.CarouselView) Visualizza gli elementi orizzontalmente. Gli screenshot seguenti mostrano un `CarouselView` la visualizzazione di elementi stringa diversi in iOS e Android:

[![Screenshot di CarouselView contenente gli elementi di testo in iOS e Android](populate-data-images/text.png "Elementi di testo in un CarouselView")](populate-data-images/text-large.png#lightbox "Elementi di testo in un CarouselView")

Per informazioni su come modificare l'orientamento [`CarouselView`](xref:Xamarin.Forms.CarouselView) , vedere [Novell. Forms CarouselView layout](layout.md). Per informazioni su come definire l'aspetto di ogni elemento nell'`CarouselView`, vedere definire l' [aspetto dell'elemento](#define-item-appearance).

### <a name="data-binding"></a>Associazione dati

[`CarouselView`](xref:Xamarin.Forms.CarouselView) possono essere popolati con i dati utilizzando data binding per associare la relativa proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) a una raccolta `IEnumerable`. In XAML, questa operazione viene eseguita con l'estensione di markup `Binding`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}" />
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

In questo esempio, i dati della proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) vengono associati alla proprietà `Monkeys` dell'elemento ViewModel connesso.

> [!NOTE]
> Le associazioni compilate possono essere abilitate per migliorare le prestazioni di data binding nelle applicazioni Novell. Forms. Per altre informazioni, vedere [Binding compilati](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definisci aspetto elemento

È possibile definire l'aspetto di ogni elemento nell' [`CarouselView`](xref:Xamarin.Forms.CarouselView) impostando la proprietà [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

Gli elementi specificati nel [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definiscono l'aspetto di ogni elemento nell'`CarouselView`. Nell'esempio, il layout all'interno del `DataTemplate` è gestito da un [`StackLayout`](xref:Xamarin.Forms.StackLayout)e i dati vengono visualizzati con un oggetto [`Image`](xref:Xamarin.Forms.Image) e tre oggetti di [`Label`](xref:Xamarin.Forms.Label) , che tutti vengono associati alle proprietà della classe `Monkey`:

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

È possibile scegliere l'aspetto di ogni elemento nell' [`CarouselView`](xref:Xamarin.Forms.CarouselView) in fase di esecuzione, in base al valore dell'elemento, impostando la proprietà [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) su un oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) :

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

La classe `MonkeyDataTemplateSelector` definisce `AmericanMonkey` e `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà impostate su modelli di dati diversi. L'override del `OnSelectTemplate` restituisce il modello di `AmericanMonkey` quando il nome della scimmia contiene "America". Quando il nome della scimmia non contiene "America", l'override del `OnSelectTemplate` restituisce il modello di `OtherMonkey`, che Visualizza i dati in grigio:

[![Screenshot della selezione del modello di elemento del runtime CarouselView, in iOS e Android](populate-data-images/datatemplateselector.png "Selezione del modello di elemento di runtime in un CarouselView")](populate-data-images/datatemplateselector-large.png#lightbox "Selezione del modello di elemento di runtime in un CarouselView")

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Create a Novell. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Quando si usa [`CarouselView`](xref:Xamarin.Forms.CarouselView), non impostare mai l'elemento radice degli oggetti [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) su un `ViewCell`. Questa operazione comporterà la generazione di un'eccezione in quanto `CarouselView` non ha alcun concetto di celle.

## <a name="pull-to-refresh"></a>Pull per aggiornare

[`CarouselView`](xref:Xamarin.Forms.CarouselView) supporta il pull per aggiornare la funzionalità tramite il `RefreshView`, che consente di visualizzare i dati da aggiornare effettuando il pull sugli elementi. Il `RefreshView` è un controllo contenitore che fornisce il pull per aggiornare la funzionalità al relativo elemento figlio, purché l'elemento figlio supporti il contenuto scorrevole. Pertanto, il pull per l'aggiornamento viene implementato per un `CarouselView` impostando il valore come figlio di un `RefreshView`:

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

Quando l'utente avvia un aggiornamento, viene eseguito il `ICommand` definito dalla proprietà `Command`, che dovrebbe aggiornare gli elementi visualizzati. Quando si verifica l'aggiornamento, viene visualizzata una visualizzazione di aggiornamento, che è costituita da un cerchio di avanzamento animato:

[![Screenshot di CarouselView pull-to-refresh, in iOS e Android](populate-data-images/pull-to-refresh.png "Pull-to-refresh CarouselView")](populate-data-images/pull-to-refresh-large.png#lightbox "Pull-to-refresh CarouselView")

Il valore della proprietà `RefreshView.IsRefreshing` indica lo stato corrente del `RefreshView`. Quando un aggiornamento viene attivato dall'utente, questa proprietà passerà automaticamente a `true`. Una volta completato l'aggiornamento, è necessario reimpostare la proprietà su `false`.

Per ulteriori informazioni su `RefreshView`, vedere [Novell. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md).

## <a name="load-data-incrementally"></a>Caricare i dati in modo incrementale

[`CarouselView`](xref:Xamarin.Forms.CarouselView) supporta il caricamento incrementale dei dati quando gli utenti scorrono gli elementi. Questo consente scenari come il caricamento asincrono di una pagina di dati da un servizio Web, quando l'utente scorre. Inoltre, il punto in cui vengono caricati più dati è configurabile, in modo che gli utenti non visualizzino spazio vuoto o siano interrotti dallo scorrimento.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce le proprietà seguenti per controllare il caricamento incrementale dei dati:

- `RemainingItemsThreshold`, di tipo `int`, la soglia di elementi non ancora visibili nell'elenco in cui verrà generato l'evento di `RemainingItemsThresholdReached`.
- `RemainingItemsThresholdReachedCommand`, di tipo `ICommand`, che viene eseguito quando viene raggiunto il `RemainingItemsThreshold`.
- `RemainingItemsThresholdReachedCommandParameter`, di tipo `object`, ovvero il parametro passato a `RemainingItemsThresholdReachedCommand`.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce anche un evento di `RemainingItemsThresholdReached` che viene generato quando viene eseguito lo scorrimento della `CarouselView` a un numero molto lungo che `RemainingItemsThreshold` elementi non sono stati visualizzati. Questo evento può essere gestito per caricare altri elementi. Inoltre, quando viene generato l'evento `RemainingItemsThresholdReached`, viene eseguito il `RemainingItemsThresholdReachedCommand`, consentendo il caricamento incrementale dei dati in un elemento ViewModel.

Il valore predefinito della proprietà `RemainingItemsThreshold` è-1, che indica che l'evento `RemainingItemsThresholdReached` non verrà mai generato. Quando il valore della proprietà è 0, l'evento `RemainingItemsThresholdReached` viene generato quando viene visualizzato l'elemento finale nel [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) . Per i valori maggiori di 0, l'evento `RemainingItemsThresholdReached` viene generato quando il `ItemsSource` contiene il numero di elementi non ancora a scorrimento.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) convalida la proprietà `RemainingItemsThreshold` in modo che il relativo valore sia sempre maggiore o uguale a-1.

Nell'esempio di codice XAML seguente viene illustrato un [`CarouselView`](xref:Xamarin.Forms.CarouselView) che carica i dati in modo incrementale:

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

In questo esempio di codice, l'evento `RemainingItemsThresholdReached` viene generato quando sono presenti 2 elementi non ancora sottoposte a scorrimento e in risposta viene eseguito il gestore dell'evento `OnCollectionViewRemainingItemsThresholdReached`:

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> I dati possono anche essere caricati in modo incrementale associando il `RemainingItemsThresholdReachedCommand` a un'implementazione di `ICommand` nell'elemento ViewModel.

## <a name="related-links"></a>Collegamenti correlati

- [CarouselView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Novell. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Data Binding Novell. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modelli di dati Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creare un DataTemplateSelector Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
