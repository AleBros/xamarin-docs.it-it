---
title: Layout di visualizzazione di raccolta di xamarin. Forms
description: Per impostazione predefinita, visualizzazione di una raccolta verranno visualizzati gli elementi contenuti in un elenco verticale. Tuttavia, è possibile specificare grids ed elenchi orizzontali e verticali.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2019
ms.openlocfilehash: 786cea04718022847bba2ecffed8f377dd49bd8b
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512792"
---
# <a name="xamarinforms-collectionview-layout"></a>Layout di visualizzazione di raccolta di xamarin. Forms

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce le proprietà seguenti che controllano il layout:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), di tipo [ `IItemsLayout` ](xref:Xamarin.Forms.IItemsLayout), specifica il layout da usare.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy), di tipo [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemSizingStrategy), specifica la strategia di misure di elemento da utilizzare.

Queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che le proprietà possono essere destinazioni di associazioni dati.

Per impostazione predefinita, un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) visualizzerà i relativi elementi in un elenco verticale. Tuttavia, uno qualsiasi dei layout seguenti consente di:

- Elenco verticale: un elenco a colonna singola che si espande verticalmente man mano che vengono aggiunti nuovi elementi.
- Elenco orizzontale: un elenco di singola riga che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi.
- Griglia verticale – una griglia a più colonne che si espande verticalmente man mano che vengono aggiunti nuovi elementi.
- Griglia orizzontale – una griglia con più righe che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi.

Questi layout possono essere specificati impostando il [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà alla classe che deriva dal [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) classe. Questa classe definisce le proprietà seguenti:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), di tipo [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation), specifica la direzione in cui il [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) consente di espandere man mano che vengono aggiunti gli elementi.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), di tipo [ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment), specifica l'allineamento con gli elementi di punti di bloccaggio.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), di tipo [ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType), specifica il comportamento dei punti di bloccaggio quando lo scorrimento.

Queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che le proprietà possono essere destinazioni di associazioni dati. Per altre informazioni sui punti di bloccaggio, vedere [punti di allineamento](scrolling.md#snap-points) nel [lo scorrimento di xamarin. Forms CollectionView](scrolling.md) Guida.

Il [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) enumerazione definisce i membri seguenti:

- `Vertical` indica che il [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) espanderà verticalmente man mano che vengono aggiunti gli elementi.
- `Horizontal` indica che il [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) espanderà orizzontalmente man mano che vengono aggiunti gli elementi.

Il [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) classe eredita dal [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) classe e definisce un `ItemSpacing` proprietà, di tipo `double`, che rappresenta lo spazio vuoto attorno a ogni elemento. Il valore predefinito di questa proprietà è 0 e il relativo valore deve sempre essere maggiore o uguale a 0. Il `ListItemsLayout` classe definisce anche statici `Vertical` e `Horizontal` membri. Questi membri sono utilizzabile per creare elenchi orizzontali o verticali, rispettivamente. In alternativa, un `ListItemsLayout` oggetto può essere creato, specificando un' [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) membro di enumerazione come argomento.

Il [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) classe eredita dal [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout) di classi e definisce le proprietà seguenti:

- `VerticalItemSpacing`, di tipo `double`, che rappresenta lo spazio vuoto verticale intorno a ogni elemento. Il valore predefinito di questa proprietà è 0 e il relativo valore deve sempre essere maggiore o uguale a 0.
- `HorizontalItemSpacing`, di tipo `double`, che rappresenta lo spazio vuoto orizzontale intorno a ogni elemento. Il valore predefinito di questa proprietà è 0 e il relativo valore deve sempre essere maggiore o uguale a 0.
- `Span`, di tipo `int`, che rappresenta il numero di colonne o righe da visualizzare nella griglia. Il valore predefinito di questa proprietà è 1 e il relativo valore deve sempre essere maggiore o uguale a 1.

Queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) Usa i motori di layout nativa per eseguire il layout.

## <a name="vertical-list"></a>Elenco verticale

Per impostazione predefinita [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) visualizzerà i relativi elementi in un layout di elenco verticale. Pertanto, non è necessario impostare il [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà da utilizzare per questo layout:

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

Tuttavia, per motivi di completezza, un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) può essere impostato per visualizzare i relativi elementi in un elenco verticale impostando relativo [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà `VerticalList`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

In alternativa, questa può essere eseguita anche impostando il [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà a un oggetto del [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) classe, specificando il `Vertical` [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) membro di enumerazione come argomento:

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
    ItemsLayout = ListItemsLayout.Vertical
};
```

Ciò comporta un elenco a colonna singola, verticalmente aumenta man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di visualizzazione di raccolta elenco verticale, in iOS e Android](layout-images/vertical-list.png "layout verticale elenco CollectionView")](layout-images/vertical-list-large.png#lightbox "layout di visualizzazione di raccolta elenco verticale")

## <a name="horizontal-list"></a>Elenco orizzontale

[`CollectionView`](xref:Xamarin.Forms.CollectionView) può visualizzare i relativi elementi in un elenco orizzontale mediante l'impostazione relativa [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà `HorizontalList`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="HorizontalList">
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

In alternativa, questa può essere eseguita anche impostando il [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà a un [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) dell'oggetto, che specifica il `Horizontal` [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation) membro dell'enumerazione come argomento:

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
    ItemsLayout = ListItemsLayout.Horizontal
};
```

Ciò comporta in un elenco di singola riga, che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di visualizzazione di raccolta elenco orizzontale, in iOS e Android](layout-images/horizontal-list.png "layout orizzontale elenco CollectionView")](layout-images/horizontal-list-large.png#lightbox "layout di visualizzazione di raccolta elenco orizzontale")

## <a name="vertical-grid"></a>Griglia verticale

[`CollectionView`](xref:Xamarin.Forms.CollectionView) può visualizzare i relativi elementi in una griglia verticale impostando relativi [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà a un [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) il cui [ `Orientation` ](xref:Xamarin.Forms.ItemsLayout.Orientation) è impostata su `Vertical`:

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

Per impostazione predefinita, un parametro vertical [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) visualizzerà gli elementi in una singola colonna. Tuttavia, questo esempio viene impostato il `GridItemsLayout.Span` proprietà su 2. Ciò comporta una griglia di due colonne, in senso verticale aumenta man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di griglia verticali CollectionView, su iOS e Android](layout-images/vertical-grid.png "layout verticale griglia CollectionView")](layout-images/vertical-grid-large.png#lightbox "CollectionView layout di griglia verticali")

## <a name="horizontal-grid"></a>Griglia orizzontale

[`CollectionView`](xref:Xamarin.Forms.CollectionView) può visualizzare i relativi elementi in una griglia orizzontale mediante l'impostazione relativa [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà a un [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) il cui[ `Orientation` ](xref:Xamarin.Forms.ItemsLayout.Orientation) è impostata su `Horizontal`:

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

Per impostazione predefinita, un controllo orizzontale [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) visualizzerà gli elementi in una singola riga. Tuttavia, questo esempio viene impostato il `GridItemsLayout.Span` proprietà a 4. Ciò comporta una griglia di quattro righe, che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di griglia orizzontale CollectionView, su iOS e Android](layout-images/horizontal-grid.png "layout di griglia orizzontale CollectionView")](layout-images/horizontal-grid-large.png#lightbox "CollectionView layout di griglia orizzontale")

## <a name="item-spacing"></a>Spaziatura elementi

Per impostazione predefinita, ogni elemento un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) non dispone di qualsiasi spazio vuoto circostante. Questo comportamento può essere modificato impostando le proprietà del layout di elementi usato dal `CollectionView`.

Quando un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) imposta relativo [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà su un [ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout) oggetto, il `ListItemsLayout.ItemSpacing` proprietà può essere impostata su un `double` valore che rappresenta lo spazio vuoto attorno a ogni elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout ItemSpacing="20">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> Il `ListItemsLayout.ItemSpacing` proprietà dispone di un set di callback di convalida, che assicura che il valore della proprietà è sempre maggiore di o uguale a 0.

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Questo codice genera un elenco verticale singola colonna, contenente una spaziatura 20 intorno a ogni elemento:

[![Screenshot della visualizzazione di una raccolta con elemento spaziatura, in iOS e Android](layout-images/vertical-list-spacing.png "CollectionView elemento spaziatura")](layout-images/vertical-list-spacing-large.png#lightbox "spaziatura di elemento di visualizzazione di raccolta")

Quando un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) imposta relativo [ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà su un [ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout) oggetto, il `GridItemsLayout.VerticalItemSpacing` e `GridItemsLayout.HorizontalItemSpacing` le proprietà possono essere Impostare su `double` valori che rappresentano lo spazio vuoto orizzontalmente e verticalmente intorno a ogni elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2"
                        VerticalItemSpacing="20"
                        HorizontalItemSpacing="30" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> Il `GridItemsLayout.VerticalItemSpacing` e `GridItemsLayout.HorizontalItemSpacing` proprietà impostato i callback di convalida, che garantiscono che i valori delle proprietà siano sempre maggiore di o uguale a 0.

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
    {
        VerticalItemSpacing = 20,
        HorizontalItemSpacing = 30
    }
};
```

Questo codice genera una griglia di due colonne verticale, che presenta una spaziatura verticale 20 intorno a ogni elemento e una spaziatura orizzontale pari a 30 intorno a ogni elemento:

[![Screenshot della visualizzazione di una raccolta con elemento spaziatura, in iOS e Android](layout-images/vertical-grid-spacing.png "CollectionView elemento spaziatura")](layout-images/vertical-grid-spacing-large.png#lightbox "spaziatura di elemento di visualizzazione di raccolta")

## <a name="item-sizing"></a>Ridimensionamento di elementi

Per impostazione predefinita, ogni elemento un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) singolarmente misurato e dimensioni, a condizione che gli elementi dell'interfaccia utente nel [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) non specifica dimensioni fisse. Questo comportamento, che può essere modificato, viene specificato per il [ `CollectionView.ItemSizingStrategy` ](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) valore della proprietà. Questo valore di proprietà può essere impostato su uno dei [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemSizingStrategy) membri dell'enumerazione:

- `MeasureAllItems` : ogni elemento viene misurato singolarmente. Rappresenta il valore predefinito.
- `MeasureFirstItem` -viene misurato solo il primo elemento, con tutti gli elementi successivi conferimento delle stesse dimensioni del primo elemento.

> [!IMPORTANT]
> Il `MeasureFirstItem` strategia di ridimensionamento comporterà un miglioramento delle prestazioni quando utilizzata in situazioni in cui la dimensione dell'elemento deve essere uniforme tra tutti gli elementi.

Esempio di codice riportato di seguito viene illustrata l'impostazione di [ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) proprietà:

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

## <a name="dynamic-resizing-of-items"></a>Il ridimensionamento dinamico di elementi

Gli elementi in un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) può essere ridimensionato in modo dinamico in fase di esecuzione modificando proprietà degli elementi all'interno di layout correlate le [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Ad esempio, il codice seguente esempio modifica il [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) e [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) le proprietà di un [ `Image` ](xref:Xamarin.Forms.Image) oggetto:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

Il `OnImageTapped` gestore eventi viene eseguito in risposta a un [ `Image` ](xref:Xamarin.Forms.Image) oggetti vengano utilizzate e modificare le dimensioni dell'immagine in modo che più facilmente, viene visualizzata:

[![Screenshot della visualizzazione di una raccolta con elemento dinamico determinazione della dimensione, in iOS e Android](layout-images/runtime-resizing.png "CollectionView elemento dinamico ridimensionamento")](layout-images/runtime-resizing-large.png#lightbox "CollectionView elemento dinamico ridimensionamento")

## <a name="right-to-left-layout"></a>Layout da destra a sinistra

[`CollectionView`](xref:Xamarin.Forms.CollectionView) possibile modificare il layout relativo contenuto in una direzione di flusso da destra a sinistra mediante l'impostazione relativa [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà [ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft). Tuttavia, il `FlowDirection` proprietà idealmente deve essere impostata su un layout di pagina o radice, che fa sì che tutti gli elementi all'interno della pagina o il layout di primo livello, per rispondere alla direzione di flusso:

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

Il valore predefinito [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) per un elemento con un elemento padre è [ `MatchParent` ](xref:Xamarin.Forms.FlowDirection.MatchParent). Pertanto, il [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) eredita il `FlowDirection` valore della proprietà dal [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), che a sua volta eredita la `FlowDirection` valore della proprietà dal [ `ContentPage`](xref:Xamarin.Forms.ContentPage). Ciò comporta il layout destra-sinistra illustrato negli screenshot seguenti:

[![Screenshot di un layout di visualizzazione di raccolta elenco verticale di destra a sinistra, in iOS e Android](layout-images/vertical-list-rtl.png "layout di visualizzazione di raccolta elenco verticale di destra a sinistra")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView verticale di destra a sinistra layout dell'elenco")

Per altre informazioni sulla direzione del flusso, vedere [localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di raccolta (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin. Forms CollectionView lo scorrimento](scrolling.md)
