---
title: Popolare CollectionView xamarin. Forms con i dati
description: Visualizzazione di una raccolta viene popolata con i dati impostando la relativa proprietà ItemsSource per qualsiasi raccolta che implementa IEnumerable.
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
ms.openlocfilehash: 70b241944376782ec4c9446878ee2a19dcee2bbd
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329938"
---
# <a name="populate-xamarinforms-collectionview-with-data"></a>Popolare CollectionView xamarin. Forms con i dati

![Anteprima](~/media/shared/preview.png)

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> Il `CollectionView` è attualmente un'anteprima e non dispone di alcune delle proprie funzionalità pianificato. Inoltre, l'API può cambiare quando viene completata l'implementazione.

`CollectionView` definisce le proprietà seguenti che definiscono i dati da visualizzare e l'aspetto del controllo:

- `ItemsSource`, di tipo `IEnumerable`, specifica la raccolta di elementi da visualizzare, e ha un valore predefinito di `null`.
- `ItemTemplate`, di tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), specifica il modello da applicare a ogni elemento nella raccolta di elementi da visualizzare.

Queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che le proprietà possono essere destinazioni di associazioni dati.

## <a name="populate-a-collectionview-with-data"></a>Popolare una visualizzazione di raccolta dati

Oggetto `CollectionView` viene popolata con i dati tramite l'impostazione relativa `ItemsSource` a qualsiasi insieme che implementa `IEnumerable`. È possibile aggiungere elementi in XAML inizializzando il `ItemsSource` proprietà da una matrice di stringhe:

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

Il codice c# equivalente è:

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
> Se il `CollectionView` è necessario aggiornare gli elementi si aggiungono, rimosso o modificati nella raccolta sottostante, la raccolta sottostante deve essere un `IEnumerable` raccolta di proprietà di invia le notifiche di modifica, ad esempio `ObservableCollection`.

Per impostazione predefinita, `CollectionView` Visualizza gli elementi in un elenco verticale, come illustrato negli screenshot seguenti:

[![Screenshot della visualizzazione di raccolta che contiene gli elementi di testo, in iOS e Android](populate-data-images/text.png "elementi di testo nella visualizzazione di una raccolta")](populate-data-images/text-large.png#lightbox "gli elementi di testo nella visualizzazione di una raccolta")

Per informazioni su come modificare la `CollectionView` layout, vedere [specificare un Layout](layout.md). Per informazioni su come definire l'aspetto di ogni elemento di `CollectionView`, vedere [definiscono l'aspetto dell'elemento elenco](#define-list-item-appearance).

### <a name="data-binding"></a>Associazione dati

`CollectionView` può essere popolato con i dati utilizzando un'associazione dati per l'associazione relativa `ItemsSource` proprietà su un `IEnumerable` raccolta. In XAML, questo risultato viene ottenuto con il `Binding` estensione di markup:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

Il codice c# equivalente è:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

In questo esempio, il `ItemsSource` esegue l'associazione dati di proprietà per il `Monkeys` proprietà del modello di visualizzazione connessa.

> [!NOTE]
> Le associazioni compilate possono essere abilitate per migliorare le prestazioni di associazione dati nelle applicazioni xamarin. Forms. Per altre informazioni, vedere [associazioni compilate](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="define-item-appearance"></a>Definire l'aspetto dell'elemento

L'aspetto di ogni elemento nel `CollectionView` può essere definita impostando la `CollectionView.ItemTemplate` proprietà di un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

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

Il codice c# equivalente è:

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

Gli elementi specificati nel [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) definiscono l'aspetto di ogni elemento nell'elenco. Nell'esempio, il layout all'interno di `DataTemplate` gestita da un [ `Grid` ](xref:Xamarin.Forms.Grid). Il `Grid` contiene un [ `Image` ](xref:Xamarin.Forms.Image) oggetto e due [ `Label` ](xref:Xamarin.Forms.Label) oggetti, che tutti i binding alle proprietà del `Monkey` classe:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

Le schermate seguenti illustrano il risultato della creazione di modelli ogni elemento nell'elenco:

[![Screenshot della visualizzazione di raccolta in cui ogni elemento è basato su modelli, in iOS e Android](populate-data-images/datatemplate.png "basato su modelli elementi nella visualizzazione di una raccolta")](populate-data-images/datatemplate-large.png#lightbox "elementi basati su modelli in visualizzazione di una raccolta")

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di raccolta (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Associazione dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Modelli di dati di xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
