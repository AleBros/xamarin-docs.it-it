---
title: Layout di Novell. Forms CollectionView
description: Per impostazione predefinita, un oggetto CollectionView Visualizza gli elementi in un elenco verticale. È tuttavia possibile specificare gli elenchi e le griglie verticali e orizzontali.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2019
ms.openlocfilehash: 5fb92882f443007e5b3dd693f54e582757db1905
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739018"
---
# <a name="xamarinforms-collectionview-layout"></a>Layout di Novell. Forms CollectionView

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce le proprietà seguenti che controllano il layout:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), di tipo [`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout), specifica il layout da utilizzare.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy), di tipo [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy), specifica la strategia di misura dell'elemento da utilizzare.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

Per impostazione predefinita, [`CollectionView`](xref:Xamarin.Forms.CollectionView) un oggetto visualizzerà gli elementi in un elenco verticale. È tuttavia possibile usare uno dei seguenti layout:

- Elenco verticale: un elenco a colonna singola che cresce verticalmente Man mano che vengono aggiunti nuovi elementi.
- Elenco orizzontale: un elenco a riga singola che aumenta orizzontalmente Man mano che vengono aggiunti nuovi elementi.
- Griglia verticale: griglia a più colonne che aumenta verticalmente Man mano che vengono aggiunti nuovi elementi.
- Griglia orizzontale: griglia a più righe che aumenta orizzontalmente Man mano che vengono aggiunti nuovi elementi.

Questi layout possono essere specificati impostando la [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà sulla classe che deriva [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) dalla classe. Questa classe definisce le proprietà seguenti:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), di tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), specifica la direzione in cui l' [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto viene espanso durante l'aggiunta di elementi.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), di tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), specifica il modo in cui i punti di allineamento sono allineati con gli elementi.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), di tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), specifica il comportamento dei punti di blocco durante lo scorrimento.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati. Per ulteriori informazioni sui punti di aggancio, vedere la pagina relativa ai [punti](scrolling.md#snap-points) di blocco nella Guida allo [scorrimento di Novell. Forms](scrolling.md) .

L' [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) enumerazione definisce i membri seguenti:

- `Vertical`indica che l' [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto verrà espanso verticalmente quando vengono aggiunti elementi.
- `Horizontal`indica che l' [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto si espande orizzontalmente quando vengono aggiunti elementi.

La [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) classe eredita [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) dalla classe e definisce una `ItemSpacing` proprietà di tipo `double`, che rappresenta lo spazio vuoto intorno a ogni elemento. Il valore predefinito di questa proprietà è 0 e il relativo valore deve essere sempre maggiore o uguale a 0. La `ListItemsLayout` classe definisce anche membri `Vertical` statici `Horizontal` e. Questi membri possono essere utilizzati rispettivamente per creare elenchi verticali o orizzontali. In alternativa, è `ListItemsLayout` possibile creare un oggetto, specificando [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) un membro di enumerazione come argomento.

La [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) classe eredita [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) dalla classe e definisce le proprietà seguenti:

- `VerticalItemSpacing`, di tipo `double`, che rappresenta lo spazio vuoto verticale intorno a ogni elemento. Il valore predefinito di questa proprietà è 0 e il relativo valore deve essere sempre maggiore o uguale a 0.
- `HorizontalItemSpacing`, di tipo `double`, che rappresenta lo spazio vuoto orizzontale intorno a ogni elemento. Il valore predefinito di questa proprietà è 0 e il relativo valore deve essere sempre maggiore o uguale a 0.
- `Span`, di tipo `int`, che rappresenta il numero di colonne o righe da visualizzare nella griglia. Il valore predefinito di questa proprietà è 1 e il valore deve essere sempre maggiore o uguale a 1.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)USA i motori di layout nativi per eseguire il layout.

## <a name="vertical-list"></a>Elenco verticale

Per impostazione predefinita [`CollectionView`](xref:Xamarin.Forms.CollectionView) , visualizzerà gli elementi in un layout di elenco verticale. Pertanto, non è necessario impostare la [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà per utilizzare questo layout:

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

Tuttavia, per completezza, è [`CollectionView`](xref:Xamarin.Forms.CollectionView) possibile impostare un oggetto per visualizzare i relativi elementi in un elenco verticale impostando la `VerticalList` [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà su:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

In alternativa, questa operazione può essere eseguita anche impostando [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) la proprietà su un oggetto [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) della classe, specificando `Vertical` il [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro di enumerazione come argomento:

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

In questo modo si ottiene un elenco a colonna singola, che aumenta verticalmente Man mano che vengono aggiunti nuovi elementi:

[ ![Screenshot di un layout di elenco verticale di CollectionView, nel](layout-images/vertical-list.png "layout di elenco verticale") di iOS e Android CollectionView] (layout-images/vertical-list-large.png#lightbox "Layout elenco verticale di CollectionView")

## <a name="horizontal-list"></a>Elenco orizzontale

[`CollectionView`](xref:Xamarin.Forms.CollectionView)consente di visualizzare gli elementi in un elenco orizzontale impostando [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) la proprietà `HorizontalList`su:

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

In alternativa, questa operazione può essere eseguita anche impostando [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) la proprietà su [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) un oggetto, specificando il `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro di enumerazione come argomento:

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

In questo modo si ottiene un elenco a riga singola, che aumenta orizzontalmente Man mano che vengono aggiunti nuovi elementi:

[ ![Screenshot di un layout di elenco orizzontale di CollectionView, nel layout dell'elenco orizzontale per iOS e Android](layout-images/horizontal-list.png "CollectionView") ] (layout-images/horizontal-list-large.png#lightbox "Layout elenco orizzontale CollectionView")

## <a name="vertical-grid"></a>Griglia verticale

[`CollectionView`](xref:Xamarin.Forms.CollectionView)consente di visualizzare gli elementi in una griglia verticale impostando [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) la relativa proprietà [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) su un [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) oggetto la cui proprietà `Vertical`è impostata su:

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

Per impostazione predefinita, in [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) un oggetto verticale gli elementi vengono visualizzati in una singola colonna. Tuttavia, in questo esempio la `GridItemsLayout.Span` proprietà viene impostata su 2. In questo modo si ottiene una griglia a due colonne, che aumenta verticalmente Man mano che vengono aggiunti nuovi elementi:

[ ![Screenshot di un layout di griglia verticale di CollectionView, nel](layout-images/vertical-grid.png "layout di griglia verticale") per iOS e Android CollectionView] (layout-images/vertical-grid-large.png#lightbox "Layout griglia verticale di CollectionView")

## <a name="horizontal-grid"></a>Griglia orizzontale

[`CollectionView`](xref:Xamarin.Forms.CollectionView)consente di visualizzare gli elementi in una griglia orizzontale impostando [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) la proprietà su [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) un oggetto[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) la cui proprietà è `Horizontal`impostata su:

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

Per impostazione predefinita, in [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) un orizzontale gli elementi vengono visualizzati in una singola riga. Tuttavia, in questo esempio la `GridItemsLayout.Span` proprietà viene impostata su 4. In questo modo si ottiene una griglia a quattro righe, che aumenta orizzontalmente Man mano che vengono aggiunti nuovi elementi:

[ ![Screenshot di un layout di griglia orizzontale di CollectionView, nel](layout-images/horizontal-grid.png "layout di griglia orizzontale") per iOS e Android CollectionView] (layout-images/horizontal-grid-large.png#lightbox "Layout griglia orizzontale CollectionView")

## <a name="item-spacing"></a>Spaziatura elementi

Per impostazione predefinita, ogni elemento in [`CollectionView`](xref:Xamarin.Forms.CollectionView) un oggetto non contiene alcuno spazio vuoto. Questo comportamento può essere modificato impostando le proprietà nel layout degli elementi utilizzato da `CollectionView`.

`double` `ListItemsLayout.ItemSpacing` [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) Quando a [`CollectionView`](xref:Xamarin.Forms.CollectionView) imposta la [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà su un oggetto, la proprietà può essere impostata su un valore che rappresenta lo spazio vuoto intorno a ogni elemento:

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
> La `ListItemsLayout.ItemSpacing` proprietà dispone di un set di callback di convalida, che assicura che il valore della proprietà sia sempre maggiore o uguale a 0.

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

Questo codice genera un elenco a colonna singola verticale con una spaziatura di 20 intorno a ogni elemento:

[ ![Screenshot di un CollectionView con spaziatura degli elementi, nella spaziatura degli elementi di iOS e Android](layout-images/vertical-list-spacing.png "CollectionView") ] (layout-images/vertical-list-spacing-large.png#lightbox "Spaziatura elemento CollectionView")

[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) `GridItemsLayout.VerticalItemSpacing` `GridItemsLayout.HorizontalItemSpacing` `double` Quando a [`CollectionView`](xref:Xamarin.Forms.CollectionView) imposta la [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) proprietà su un oggetto, le proprietà e possono essere impostate su valori che rappresentano lo spazio vuoto verticalmente e orizzontalmente intorno a ogni elemento:

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
> Per `GridItemsLayout.VerticalItemSpacing` le `GridItemsLayout.HorizontalItemSpacing` proprietà e sono impostati callback di convalida che assicurano che i valori delle proprietà siano sempre maggiori o uguali a 0.

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

Questo codice restituisce una griglia verticale a due colonne, con una spaziatura verticale di 20 intorno a ogni elemento e una spaziatura orizzontale di 30 intorno a ogni elemento:

[ ![Screenshot di un CollectionView con spaziatura degli elementi, nella spaziatura degli elementi di iOS e Android](layout-images/vertical-grid-spacing.png "CollectionView") ] (layout-images/vertical-grid-spacing-large.png#lightbox "Spaziatura elemento CollectionView")

## <a name="item-sizing"></a>Ridimensionamento elementi

Per impostazione predefinita, ogni elemento in [`CollectionView`](xref:Xamarin.Forms.CollectionView) un oggetto viene misurato e dimensionato singolarmente, purché gli [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) elementi dell'interfaccia utente in non specifichino dimensioni fisse. Questo comportamento, che può essere modificato, viene specificato dal valore [`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) della proprietà. Il valore di questa proprietà può essere impostato su uno [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) dei membri dell'enumerazione:

- `MeasureAllItems`: ogni elemento viene misurato singolarmente. Rappresenta il valore predefinito.
- `MeasureFirstItem`-viene misurato solo il primo elemento, con tutti gli elementi successivi a cui vengono assegnate le stesse dimensioni del primo elemento.

> [!IMPORTANT]
> La `MeasureFirstItem` strategia di ridimensionamento comporta un miglioramento delle prestazioni quando viene utilizzata in situazioni in cui la dimensione dell'elemento deve essere uniforme in tutti gli elementi.

Nell'esempio di codice seguente viene illustrata l'impostazione della [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) proprietà:

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

## <a name="dynamic-resizing-of-items"></a>Ridimensionamento dinamico di elementi

Gli elementi in [`CollectionView`](xref:Xamarin.Forms.CollectionView) un oggetto possono essere ridimensionati dinamicamente in fase di esecuzione modificando le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)proprietà correlate al layout degli elementi all'interno di. Nell'esempio di codice seguente, ad esempio, [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) vengono [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) modificate le proprietà [`Image`](xref:Xamarin.Forms.Image) e di un oggetto:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

Il `OnImageTapped` gestore eventi viene eseguito in risposta a un [`Image`](xref:Xamarin.Forms.Image) oggetto che viene toccato e modifica le dimensioni dell'immagine in modo che sia più semplice da visualizzare:

[ ![Screenshot di un CollectionView con ridimensionamento di elementi dinamici, in iOS e Android](layout-images/runtime-resizing.png "CollectionView dimensioni degli elementi dinamici") ] (layout-images/runtime-resizing-large.png#lightbox "Ridimensionamento di elementi dinamici di CollectionView")

## <a name="right-to-left-layout"></a>Layout da destra a sinistra

[`CollectionView`](xref:Xamarin.Forms.CollectionView)può impostare il layout del contenuto in una direzione di flusso da destra a sinistra impostando la [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)relativa [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà su. Tuttavia, la `FlowDirection` proprietà deve essere impostata idealmente in una pagina o in un layout radice, che causa la risposta alla direzione del flusso di tutti gli elementi all'interno della pagina o del layout radice:

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

Il valore [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) predefinito per un elemento con un elemento [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)padre è. `FlowDirection` [`StackLayout`](xref:Xamarin.Forms.StackLayout) `FlowDirection` Eredita quindi il valore della proprietà da, che [`ContentPage`](xref:Xamarin.Forms.ContentPage)a sua volta eredita il valore della proprietà da. [`CollectionView`](xref:Xamarin.Forms.CollectionView) In questo modo si ottiene il layout da destra a sinistra illustrato nelle schermate seguenti:

[ ![Screenshot di un layout di elenco verticale da destra a sinistra per gli elenchi di controllo di stato, in iOS e Android,]con(layout-images/vertical-list-rtl.png "layout di elenco verticale da destra a sinistra") ] (layout-images/vertical-list-rtl-large.png#lightbox "Layout elenco verticale da destra a sinistra di CollectionView")

Per altre informazioni sulla direzione del flusso, vedere [localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Novell. Forms (scorrimento)](scrolling.md)
