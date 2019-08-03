---
title: Novell. Forms-dati CollectionView
description: Un oggetto CollectionView viene popolato con i dati impostando la relativa proprietà ItemsSource su una raccolta che implementa IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: ce745109ea2852b597de3a8a5922a171ad83e289
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738911"
---
# <a name="xamarinforms-collectionview-data"></a>Novell. Forms-dati CollectionView

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce le proprietà seguenti che definiscono i dati da visualizzare e il relativo aspetto:

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource), di tipo `IEnumerable`, specifica la raccolta di elementi da visualizzare e il cui `null`valore predefinito è.
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), specifica il modello da applicare a ogni elemento nella raccolta di elementi da visualizzare.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

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

Il codice C# equivalente è:

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

> [!IMPORTANT]
> Se è necessario aggiornare quando gli elementi vengono aggiunti, rimossi o modificati nell'insieme sottostante, la raccolta sottostante deve essere una `IEnumerable` raccolta che invia notifiche di `ObservableCollection`modifica della proprietà, ad esempio. [`CollectionView`](xref:Xamarin.Forms.CollectionView)

Per impostazione predefinita [`CollectionView`](xref:Xamarin.Forms.CollectionView) , Visualizza gli elementi in un elenco verticale, come illustrato nelle schermate seguenti:

[ ![Screenshot di CollectionView contenente elementi di testo, su elementi di testo iOS e Android](populate-data-images/text.png "in un oggetto CollectionView") ] (populate-data-images/text-large.png#lightbox "Elementi di testo in un oggetto CollectionView")

Per informazioni su come modificare il [`CollectionView`](xref:Xamarin.Forms.CollectionView) layout, vedere [specificare un layout](layout.md). Per informazioni su come definire l'aspetto di ogni elemento in `CollectionView`, vedere definire l' [aspetto dell'elemento](#define-item-appearance).

### <a name="data-binding"></a>Associazione dati

[`CollectionView`](xref:Xamarin.Forms.CollectionView)può essere popolato con i dati usando data binding per associare [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) la relativa proprietà `IEnumerable` a una raccolta. In XAML, questa operazione viene eseguita con `Binding` l'estensione di markup:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

In questo esempio, i [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) dati della proprietà vengono associati `Monkeys` alla proprietà del modello di visualizzazione connesso.

> [!NOTE]
> Le associazioni compilate possono essere abilitate per migliorare le prestazioni di data binding nelle applicazioni Novell. Forms. Per ulteriori informazioni, vedere [Binding](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)compilati.

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

Il codice C# equivalente è:

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

Gli elementi specificati in [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definiscono l'aspetto di ogni elemento nell'elenco. Nell'esempio, il layout all'interno `DataTemplate` di è gestito da [`Grid`](xref:Xamarin.Forms.Grid)un oggetto. Contiene `Grid` un [`Image`](xref:Xamarin.Forms.Image) oggetto e due [`Label`](xref:Xamarin.Forms.Label) oggetti `Monkey` , che tutti sono associati alle proprietà della classe:

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

[ ![Screenshot di CollectionView in cui ogni elemento è basato su modelli, su elementi basati su modelli iOS e Android](populate-data-images/datatemplate.png "in un CollectionView") ] (populate-data-images/datatemplate-large.png#lightbox "Elementi basati su modelli in un oggetto CollectionView")

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

Il codice C# equivalente è:

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

La `MonkeyDataTemplateSelector` classe definisce `AmericanMonkey` le `OtherMonkey` proprietà [e`DataTemplate`](xref:Xamarin.Forms.DataTemplate) impostate su modelli di dati diversi. L' `OnSelectTemplate` override restituisce il `AmericanMonkey` modello, che Visualizza il nome e la posizione della scimmia in verde acqua, quando il nome della scimmia contiene "America". Quando il nome della scimmia non contiene "America", `OnSelectTemplate` l'override restituisce `OtherMonkey` il modello, che Visualizza il nome e la posizione della scimmia in argento:

[ ![Screenshot della selezione del modello di elemento di runtime di CollectionView, sulla selezione del]modello di elemento di runtime iOS e Android(populate-data-images/datatemplateselector.png "in un CollectionView") ] (populate-data-images/datatemplateselector-large.png#lightbox "Selezione del modello di elemento di runtime in un CollectionView")

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Create a Novell. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

> [!IMPORTANT]
> Quando si [`CollectionView`](xref:Xamarin.Forms.CollectionView)USA, non impostare mai l'elemento radice [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) degli oggetti su `ViewCell`un oggetto. Questo comporterà la generazione di un'eccezione `CollectionView` perché non ha un concetto di celle.

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Data Binding Novell. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modelli di dati Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Creare un DataTemplateSelector Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
