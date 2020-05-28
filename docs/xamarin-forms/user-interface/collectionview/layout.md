---
title: Xamarin.FormsLayout di CollectionView
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
ms.openlocfilehash: 425eb7abc14fb941dbfc28219907d98558cbfabb
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137435"
---
# <a name="xamarinforms-collectionview-layout"></a>Xamarin.FormsLayout di CollectionView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce le proprietà seguenti che controllano il layout:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), di tipo [`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout) , specifica il layout da utilizzare.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy), di tipo [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) , specifica la strategia di misura dell'elemento da utilizzare.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

Per impostazione predefinita, un oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) visualizzerà gli elementi in un elenco verticale. È tuttavia possibile usare uno dei seguenti layout:

- Elenco verticale: un elenco a colonna singola che cresce verticalmente Man mano che vengono aggiunti nuovi elementi.
- Elenco orizzontale: un elenco a riga singola che aumenta orizzontalmente Man mano che vengono aggiunti nuovi elementi.
- Griglia verticale: griglia a più colonne che aumenta verticalmente Man mano che vengono aggiunti nuovi elementi.
- Griglia orizzontale: griglia a più righe che aumenta orizzontalmente Man mano che vengono aggiunti nuovi elementi.

Questi layout possono essere specificati impostando la [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) proprietà sulla classe che deriva dalla [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe. Questa classe definisce le proprietà seguenti:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), di tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) , specifica la direzione in cui l'oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene espanso durante l'aggiunta di elementi.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), di tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) , specifica il modo in cui i punti di allineamento sono allineati con gli elementi.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), di tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) , specifica il comportamento dei punti di blocco durante lo scorrimento.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati. Per ulteriori informazioni sui punti di aggancio, vedere la pagina relativa ai [punti](scrolling.md#snap-points) di blocco nella Guida allo [ Xamarin.Forms scorrimento di CollectionView](scrolling.md) .

L' [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) enumerazione definisce i membri seguenti:

- `Vertical`indica che l'oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) verrà espanso verticalmente quando vengono aggiunti elementi.
- `Horizontal`indica che l'oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) si espande orizzontalmente quando vengono aggiunti elementi.

La `LinearItemsLayout` classe eredita dalla [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe e definisce una `ItemSpacing` proprietà di tipo `double` , che rappresenta lo spazio vuoto intorno a ogni elemento. Il valore predefinito di questa proprietà è 0 e il relativo valore deve essere sempre maggiore o uguale a 0. La `LinearItemsLayout` classe definisce anche `Vertical` membri statici e `Horizontal` . Questi membri possono essere utilizzati rispettivamente per creare elenchi verticali o orizzontali. In alternativa, `LinearItemsLayout` è possibile creare un oggetto, specificando un [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro di enumerazione come argomento.

La [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) classe eredita dalla [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe e definisce le proprietà seguenti:

- `VerticalItemSpacing`, di tipo `double` , che rappresenta lo spazio vuoto verticale intorno a ogni elemento. Il valore predefinito di questa proprietà è 0 e il relativo valore deve essere sempre maggiore o uguale a 0.
- `HorizontalItemSpacing`, di tipo `double` , che rappresenta lo spazio vuoto orizzontale intorno a ogni elemento. Il valore predefinito di questa proprietà è 0 e il relativo valore deve essere sempre maggiore o uguale a 0.
- `Span`, di tipo `int` , che rappresenta il numero di colonne o righe da visualizzare nella griglia. Il valore predefinito di questa proprietà è 1 e il valore deve essere sempre maggiore o uguale a 1.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)USA i motori di layout nativi per eseguire il layout.

## <a name="vertical-list"></a>Elenco verticale

Per impostazione predefinita, visualizzerà [`CollectionView`](xref:Xamarin.Forms.CollectionView) gli elementi in un layout di elenco verticale. Pertanto, non è necessario impostare la [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) proprietà per utilizzare questo layout:

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

Tuttavia, per completezza, [`CollectionView`](xref:Xamarin.Forms.CollectionView) è possibile impostare un oggetto per visualizzare i relativi elementi in un elenco verticale impostando la [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) proprietà su `VerticalList` :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

In alternativa, questa operazione può essere eseguita anche impostando la [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) proprietà su un `LinearItemsLayout` oggetto, specificando il `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro di enumerazione come valore della `Orientation` proprietà:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

In questo modo si ottiene un elenco a colonna singola, che aumenta verticalmente Man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di elenco verticale di CollectionView, in iOS e Android](layout-images/vertical-list.png "Layout elenco verticale di CollectionView")](layout-images/vertical-list-large.png#lightbox "Layout elenco verticale di CollectionView")

## <a name="horizontal-list"></a>Elenco orizzontale

[`CollectionView`](xref:Xamarin.Forms.CollectionView)consente di visualizzare gli elementi in un elenco orizzontale impostando la [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) proprietà su `HorizontalList` :

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

In alternativa, questo layout può essere eseguito anche impostando la [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) proprietà su un `LinearItemsLayout` oggetto, specificando il `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro di enumerazione come `Orientation` valore della proprietà:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

In questo modo si ottiene un elenco a riga singola, che aumenta orizzontalmente Man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di elenco orizzontale di CollectionView, in iOS e Android](layout-images/horizontal-list.png "Layout elenco orizzontale CollectionView")](layout-images/horizontal-list-large.png#lightbox "Layout elenco orizzontale CollectionView")

## <a name="vertical-grid"></a>Griglia verticale

[`CollectionView`](xref:Xamarin.Forms.CollectionView)consente di visualizzare gli elementi in una griglia verticale impostando la relativa [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) proprietà su un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) oggetto la cui [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) proprietà è impostata su `Vertical` :

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

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

Per impostazione predefinita, in un oggetto verticale [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) gli elementi vengono visualizzati in una singola colonna. Tuttavia, in questo esempio la `GridItemsLayout.Span` proprietà viene impostata su 2. In questo modo si ottiene una griglia a due colonne, che aumenta verticalmente Man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di griglia verticale di CollectionView, in iOS e Android](layout-images/vertical-grid.png "Layout griglia verticale di CollectionView")](layout-images/vertical-grid-large.png#lightbox "Layout griglia verticale di CollectionView")

## <a name="horizontal-grid"></a>Griglia orizzontale

[`CollectionView`](xref:Xamarin.Forms.CollectionView)consente di visualizzare gli elementi in una griglia orizzontale impostando la [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) proprietà su un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) oggetto la cui [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) proprietà è impostata su `Horizontal` :

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

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

Per impostazione predefinita, in un orizzontale [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) gli elementi vengono visualizzati in una singola riga. Tuttavia, in questo esempio la `GridItemsLayout.Span` proprietà viene impostata su 4. In questo modo si ottiene una griglia a quattro righe, che aumenta orizzontalmente Man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout di griglia orizzontale di CollectionView, in iOS e Android](layout-images/horizontal-grid.png "Layout griglia orizzontale CollectionView")](layout-images/horizontal-grid-large.png#lightbox "Layout griglia orizzontale CollectionView")

## <a name="headers-and-footers"></a>Intestazioni e piè di pagina

[`CollectionView`](xref:Xamarin.Forms.CollectionView)può presentare un'intestazione e un piè di pagina che scorrono con gli elementi dell'elenco. L'intestazione e il piè di pagina possono essere stringhe, viste o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetti.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce le proprietà seguenti per specificare l'intestazione e il piè di pagina:

- `Header`, di tipo `object` , specifica la stringa, l'associazione o la visualizzazione che verrà visualizzata all'inizio dell'elenco.
- `HeaderTemplate`, di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , specifica l'oggetto `DataTemplate` da utilizzare per formattare `Header` .
- `Footer`, di tipo `object` , specifica la stringa, l'associazione o la visualizzazione che verrà visualizzata alla fine dell'elenco.
- `FooterTemplate`, di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , specifica l'oggetto `DataTemplate` da utilizzare per formattare `Footer` .

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

Quando un'intestazione viene aggiunta a un layout che cresce orizzontalmente, da sinistra a destra, l'intestazione viene visualizzata a sinistra dell'elenco. Analogamente, quando un piè di pagina viene aggiunto a un layout che cresce orizzontalmente, da sinistra a destra, il piè di pagina viene visualizzato a destra dell'elenco.

### <a name="display-strings-in-the-header-and-footer"></a>Visualizzare le stringhe nell'intestazione e nel piè di pagina

Le `Header` `Footer` proprietà e possono essere impostate su `string` valori, come illustrato nell'esempio seguente:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="Monkeys"
                Footer="2019">
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    Header = "Monkeys",
    Footer = "2019"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Questo codice genera gli screenshot seguenti, con l'intestazione mostrata nello screenshot iOS e il piè di pagina visualizzato nello screenshot Android:

[![Screenshot di un'intestazione e di un piè di pagina di una stringa CollectionView, in iOS e Android](layout-images/header-footer-string.png "Intestazione e piè di pagina di una stringa CollectionView")](layout-images/header-footer-string-large.png#lightbox "Intestazione e piè di pagina di una stringa CollectionView")

### <a name="display-views-in-the-header-and-footer"></a>Visualizzazione delle visualizzazioni nell'intestazione e nel piè di pagina

Le `Header` `Footer` proprietà e possono essere impostate su una visualizzazione. Può trattarsi di una vista singola o di una vista che contiene più visualizzazioni figlio. Nell'esempio seguente vengono illustrate le `Header` `Footer` proprietà e impostate su un [`StackLayout`](xref:Xamarin.Forms.StackLayout) oggetto che contiene un [`Label`](xref:Xamarin.Forms.Label) oggetto:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.Header>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Monkeys"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Header>
    <CollectionView.Footer>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Friends of Xamarin Monkey"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Footer>
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    Header = new StackLayout
    {
        Children =
        {
            new Label { Text = "Monkeys", ... }
        }
    },
    Footer = new StackLayout
    {
        Children =
        {
            new Label { Text = "Friends of Xamarin Monkey", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Questo codice genera gli screenshot seguenti, con l'intestazione mostrata nello screenshot iOS e il piè di pagina visualizzato nello screenshot Android:

[![Screenshot di un'intestazione e un piè di pagina di CollectionView usando le visualizzazioni, in iOS e Android](layout-images/header-footer-view.png "Intestazione e piè di pagina della visualizzazione CollectionView")](layout-images/header-footer-view-large.png#lightbox "Intestazione e piè di pagina della visualizzazione CollectionView")

### <a name="display-a-templated-header-and-footer"></a>Visualizzare un'intestazione e un piè di pagina basati su modelli

Le `HeaderTemplate` `FooterTemplate` proprietà e possono essere impostate su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetti utilizzati per formattare l'intestazione e il piè di pagina. In questo scenario, le `Header` `Footer` proprietà e devono essere associate all'origine corrente per i modelli da applicare, come illustrato nell'esempio seguente:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="{Binding .}"
                Footer="{Binding .}">
    <CollectionView.HeaderTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Monkeys"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.HeaderTemplate>
    <CollectionView.FooterTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Friends of Xamarin Monkey"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.FooterTemplate>
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    HeaderTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    }),
    FooterTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    })
};
collectionView.SetBinding(ItemsView.HeaderProperty, ".");
collectionView.SetBinding(ItemsView.FooterProperty, ".");
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Questo codice genera gli screenshot seguenti, con l'intestazione mostrata nello screenshot iOS e il piè di pagina visualizzato nello screenshot Android:

[![Screenshot di un'intestazione e un piè di pagina di CollectionView usando i modelli in iOS e Android](layout-images/header-footer-template.png "Intestazione e piè di pagina del modello CollectionView")](layout-images/header-footer-template-large.png#lightbox "Intestazione e piè di pagina del modello CollectionView")

## <a name="item-spacing"></a>Spaziatura elementi

Per impostazione predefinita, non è presente alcuno spazio tra gli elementi di un oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Questo comportamento può essere modificato impostando le proprietà nel layout degli elementi utilizzato da `CollectionView` .

Quando un [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto imposta la [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) proprietà su un `LinearItemsLayout` oggetto, la `LinearItemsLayout.ItemSpacing` proprietà può essere impostata su un `double` valore che rappresenta lo spazio tra gli elementi:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> La `LinearItemsLayout.ItemSpacing` proprietà dispone di un set di callback di convalida, che assicura che il valore della proprietà sia sempre maggiore o uguale a 0.

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Questo codice genera un elenco a colonna singola verticale con una spaziatura di 20 elementi tra gli elementi:

[![Screenshot di un CollectionView con spaziatura degli elementi, in iOS e Android](layout-images/vertical-list-spacing.png "Spaziatura elemento CollectionView")](layout-images/vertical-list-spacing-large.png#lightbox "Spaziatura elemento CollectionView")

Quando a [`CollectionView`](xref:Xamarin.Forms.CollectionView) imposta la [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) proprietà su un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) oggetto, le `GridItemsLayout.VerticalItemSpacing` `GridItemsLayout.HorizontalItemSpacing` proprietà e possono essere impostate su `double` valori che rappresentano lo spazio vuoto verticalmente e orizzontalmente tra gli elementi:

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
> `GridItemsLayout.VerticalItemSpacing`Per le `GridItemsLayout.HorizontalItemSpacing` proprietà e sono impostati callback di convalida che assicurano che i valori delle proprietà siano sempre maggiori o uguali a 0.

Il codice C# equivalente è il seguente:

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

Questo codice restituisce una griglia verticale a due colonne, che ha una spaziatura verticale di 20 tra gli elementi e una spaziatura orizzontale pari a 30 tra gli elementi:

[![Screenshot di un CollectionView con spaziatura degli elementi, in Android](layout-images/vertical-grid-spacing.png "Spaziatura elemento CollectionView")](layout-images/vertical-grid-spacing-large.png#lightbox "Spaziatura elemento CollectionView")

## <a name="item-sizing"></a>Ridimensionamento elementi

Per impostazione predefinita, ogni elemento in un oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene misurato e dimensionato singolarmente, purché gli elementi dell'interfaccia utente in [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) non specifichino dimensioni fisse. Questo comportamento, che può essere modificato, viene specificato dal [`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy) valore della proprietà. Il valore di questa proprietà può essere impostato su uno dei [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) membri dell'enumerazione:

- `MeasureAllItems`: ogni elemento viene misurato singolarmente. Si tratta del valore predefinito.
- `MeasureFirstItem`-viene misurato solo il primo elemento, con tutti gli elementi successivi a cui vengono assegnate le stesse dimensioni del primo elemento.

> [!IMPORTANT]
> La `MeasureFirstItem` strategia di ridimensionamento comporta un miglioramento delle prestazioni quando viene utilizzata in situazioni in cui la dimensione dell'elemento deve essere uniforme in tutti gli elementi.

Nell'esempio di codice seguente viene illustrata l'impostazione della [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy) proprietà:

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="dynamic-resizing-of-items"></a>Ridimensionamento dinamico di elementi

Gli elementi in un oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) possono essere ridimensionati dinamicamente in fase di esecuzione modificando le proprietà correlate al layout degli elementi all'interno di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Nell'esempio di codice seguente, ad esempio, vengono modificate le [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) proprietà e di un [`Image`](xref:Xamarin.Forms.Image) oggetto:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

Il `OnImageTapped` gestore eventi viene eseguito in risposta a un [`Image`](xref:Xamarin.Forms.Image) oggetto che viene toccato e modifica le dimensioni dell'immagine in modo che sia più semplice da visualizzare:

[![Screenshot di un CollectionView con ridimensionamento di elementi dinamici, in iOS e Android](layout-images/runtime-resizing.png "Ridimensionamento di elementi dinamici di CollectionView")](layout-images/runtime-resizing-large.png#lightbox "Ridimensionamento di elementi dinamici di CollectionView")

## <a name="right-to-left-layout"></a>Layout da destra a sinistra

[`CollectionView`](xref:Xamarin.Forms.CollectionView)può impostare il layout del contenuto in una direzione di flusso da destra a sinistra impostando la relativa [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà su [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) . Tuttavia, la `FlowDirection` proprietà deve essere impostata idealmente in una pagina o in un layout radice, che causa la risposta alla direzione del flusso di tutti gli elementi all'interno della pagina o del layout radice:

```xaml
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

Il valore predefinito [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) per un elemento con un elemento padre è [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) . [`CollectionView`](xref:Xamarin.Forms.CollectionView)Eredita quindi il valore della `FlowDirection` proprietà da [`StackLayout`](xref:Xamarin.Forms.StackLayout) , che a sua volta eredita il `FlowDirection` valore della proprietà da [`ContentPage`](xref:Xamarin.Forms.ContentPage) . In questo modo si ottiene il layout da destra a sinistra illustrato nelle schermate seguenti:

[![Screenshot di un layout di elenco verticale da destra a sinistra in un elenco in iOS e Android](layout-images/vertical-list-rtl.png "Layout elenco verticale da destra a sinistra di CollectionView")](layout-images/vertical-list-rtl-large.png#lightbox "Layout elenco verticale da destra a sinistra di CollectionView")

Per altre informazioni sulla direzione del flusso, vedere [localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin.FormsScorrimento di CollectionView](scrolling.md)
