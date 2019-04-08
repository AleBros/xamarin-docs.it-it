---
title: Specificare il Layout di visualizzazione raccolta xamarin. Forms
description: Per impostazione predefinita, visualizzazione di una raccolta verranno visualizzati gli elementi contenuti in un elenco verticale. Tuttavia, è possibile specificare grids ed elenchi orizzontali e verticali.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
ms.openlocfilehash: 8ed365ed41ac31c66d41f1a32a7a16929cdc6770
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329941"
---
# <a name="specify-xamarinforms-collectionview-layout"></a>Specificare il Layout di visualizzazione raccolta xamarin. Forms

![Anteprima](~/media/shared/preview.png)

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> Il `CollectionView` è attualmente un'anteprima e non dispone di alcune delle proprie funzionalità pianificato. Inoltre, l'API può cambiare quando viene completata l'implementazione.

`CollectionView` definisce le proprietà seguenti che controllano il layout:

- `ItemsLayout`, di tipo `IItemsLayout`, specifica il layout da usare.
- `ItemSizingStrategy`, di tipo `ItemSizingStrategy`, specifica la strategia di misure di elemento da utilizzare.

Queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che le proprietà possono essere destinazioni di associazioni dati.

Per impostazione predefinita, un `CollectionView` visualizzerà i relativi elementi in un elenco verticale. Tuttavia, uno qualsiasi dei layout seguenti consente di:

- Elenco verticale: un elenco a colonna singola che si espande verticalmente man mano che vengono aggiunti nuovi elementi.
- Elenco orizzontale: un elenco di singola riga che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi.
- Griglia verticale – una griglia a più colonne che si espande verticalmente man mano che vengono aggiunti nuovi elementi.
- Griglia orizzontale – una griglia con più righe che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi.

Questi layout possono essere specificati impostando il `ItemsLayout` proprietà alla classe che deriva dal `ItemsLayout` classe. Questa classe definisce le proprietà seguenti:

- `Orientation`, di tipo `ItemsLayoutOrientation`, specifica la direzione in cui il `CollectionView` si espande in base gli elementi vengono aggiunti.
- `SnapPointsAlignment`, di tipo `SnapPointsAlignment`, specifica l'allineamento con gli elementi di punti di bloccaggio.
- `SnapPointsType`, di tipo `SnapPointsType`, specifica il comportamento dei punti di bloccaggio quando lo scorrimento.

Queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che le proprietà possono essere destinazioni di associazioni dati. Per altre informazioni sui punti di bloccaggio, vedere [punti di allineamento](scrolling.md#snap-points) nel [scorrere un elemento all'interno della visualizzazione](scrolling.md) Guida.

Il `ItemsLayoutOrientation` enumerazione definisce i membri seguenti:

- `Vertical` indica che il `CollectionView` espanderà verticalmente man mano che vengono aggiunti gli elementi.
- `Horizontal` indica che il `CollectionView` espanderà orizzontalmente man mano che vengono aggiunti gli elementi.

Il `ListItemsLayout` classe eredita dal `ItemsLayout` classe e definisce static `VerticalList` e `HorizontalList` membri. Questi membri sono utilizzabile per creare elenchi orizzontali o verticali, rispettivamente. In alternativa, un `ListItemsLayout` oggetto può essere creato, specificando un `ItemsLayoutOrientation` membro di enumerazione come argomento.

Il `GridItemsLayout` classe eredita dal `ItemsLayout` classe e definisce una `Span` proprietà, di tipo `int`, che rappresenta il numero di colonne o righe da visualizzare nella griglia. Il valore predefinito di `Span` proprietà è 1 e il relativo valore deve sempre essere maggiore o uguale a 1.

> [!NOTE]
> `CollectionView` Usa i motori di layout nativa per eseguire il layout.

## <a name="vertical-list"></a>Elenco verticale

Per impostazione predefinita, `CollectionView` visualizzerà i relativi elementi in un layout di elenco verticale. Pertanto, non è necessario impostare il `ItemsLayout` proprietà da utilizzare per questo layout:

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
</CollectionView>
```

Tuttavia, per motivi di completezza, un `CollectionView` può essere impostato su visualizzare i relativi elementi in un elenco verticale tramite l'impostazione relativa `ItemsLayout` alla proprietà statica `ListItemsLayout.VerticalList` membro:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.VerticalList}">
    ...
</CollectionView>
```

In alternativa, questa può essere eseguita anche impostando il `ItemsLayout` proprietà a un oggetto del `ListItemsLayout` classe, specificando la `Vertical` `ItemsLayoutOrientation` membro di enumerazione come argomento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.VerticalList
};
```

Ciò comporta un elenco a colonna singola, verticalmente aumenta man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di visualizzazione di raccolta elenco verticale, in iOS e Android](layout-images/vertical-list.png "layout verticale elenco CollectionView")](layout-images/vertical-list-large.png#lightbox "layout di visualizzazione di raccolta elenco verticale")

## <a name="horizontal-list"></a>Elenco orizzontale

`CollectionView` può visualizzare i relativi elementi in un elenco orizzontale mediante l'impostazione relativa `ItemsLayout` proprietà statica `ListItemsLayout.HorizontalList` membro:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.HorizontalList}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

In alternativa, questa può essere eseguita anche impostando il `ItemsLayout` proprietà per un `ListItemsLayout` dell'oggetto, che specifica il `Horizontal` `ItemsLayoutOrientation` membro di enumerazione come argomento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Horizontal</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.HorizontalList
};
```

Ciò comporta in un elenco di singola riga, che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di visualizzazione di raccolta elenco orizzontale, in iOS e Android](layout-images/horizontal-list.png "layout orizzontale elenco CollectionView")](layout-images/horizontal-list-large.png#lightbox "layout di visualizzazione di raccolta elenco orizzontale")

## <a name="vertical-grid"></a>Griglia verticale

`CollectionView` può visualizzare i relativi elementi in una griglia verticale impostando relativi `ItemsLayout` proprietà per un `GridItemsLayout` il cui `Orientation` è impostata su `Vertical`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="80" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

Per impostazione predefinita, un parametro vertical `GridItemsLayout` visualizzerà gli elementi in una singola colonna. Tuttavia, questo esempio viene impostato il `GridItemsLayout.Span` proprietà su 2. Ciò comporta una griglia di due colonne, in senso verticale aumenta man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di griglia verticali CollectionView, su iOS e Android](layout-images/vertical-grid.png "layout verticale griglia CollectionView")](layout-images/vertical-grid-large.png#lightbox "CollectionView layout di griglia verticali")

## <a name="horizontal-grid"></a>Griglia orizzontale

`CollectionView` può visualizzare i relativi elementi in una griglia orizzontale mediante l'impostazione relativa `ItemsLayout` proprietà per un `GridItemsLayout` il cui `Orientation` è impostata su `Horizontal`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

Per impostazione predefinita, un controllo orizzontale `GridItemsLayout` visualizzerà gli elementi in una singola riga. Tuttavia, questo esempio viene impostato il `GridItemsLayout.Span` proprietà a 4. Ciò comporta una griglia di quattro righe, che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di griglia orizzontale CollectionView, su iOS e Android](layout-images/horizontal-grid.png "layout di griglia orizzontale CollectionView")](layout-images/horizontal-grid-large.png#lightbox "CollectionView layout di griglia orizzontale")

## <a name="right-to-left-layout"></a>Layout da destra a sinistra

`CollectionView` possibile modificare il layout relativo contenuto in una direzione di flusso da destra a sinistra mediante l'impostazione relativa [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft). Tuttavia, il `FlowDirection` proprietà idealmente deve essere impostata su un layout di pagina o radice, che fa sì che tutti gli elementi all'interno della pagina o il layout di primo livello, per rispondere alla direzione di flusso:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.VerticalListFlowDirectionPage"
             Title="Vertical list (RTL FlowDirection)"
             FlowDirection="RightToLeft">
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}">
            ...
        </CollectionView>
    </StackLayout>
</ContentPage>
```

Il valore predefinito [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) per un elemento con un elemento padre è [ `MatchParent` ](xref:Xamarin.Forms.FlowDirection.MatchParent). Pertanto, il `CollectionView` eredita le `FlowDirection` valore della proprietà dal [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), che a sua volta eredita il `FlowDirection` valore della proprietà dal [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) . Ciò comporta il layout destra-sinistra illustrato negli screenshot seguenti:

[![Screenshot di un layout di visualizzazione di raccolta elenco verticale di destra a sinistra, in iOS e Android](layout-images/vertical-list-rtl.png "layout di visualizzazione di raccolta elenco verticale di destra a sinistra")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView verticale di destra a sinistra layout dell'elenco")

Per altre informazioni sulla direzione del flusso, vedere [localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="item-sizing"></a>Ridimensionamento di elementi

Per impostazione predefinita, ogni elemento un `CollectionView` singolarmente misurato e dimensioni, a condizione che gli elementi dell'interfaccia utente nel [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) non specifica dimensioni fisse. Questo comportamento, che può essere modificato, specificato da di `CollectionView.ItemSizingStrategy` valore della proprietà. Questo valore di proprietà può essere impostato su uno del `ItemSizingStrategy` membri dell'enumerazione:

- `MeasureAllItems` : ogni elemento viene misurato singolarmente. Rappresenta il valore predefinito.
- `MeasureFirstItem` -viene misurato solo il primo elemento, con tutti gli elementi successivi conferimento delle stesse dimensioni del primo elemento.

> [!IMPORTANT]
> Il `MeasureFirstItem` strategia di ridimensionamento deve essere usato in situazioni in cui è destinata a essere uniforme tra tutti gli elementi, la dimensione dell'elemento, comporterà un miglioramento delle prestazioni.

Esempio di codice riportato di seguito viene illustrata l'impostazione di `ItemSizingStrategy` proprietà:

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di raccolta (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Scorrere un elemento all'interno della visualizzazione](scrolling.md)
