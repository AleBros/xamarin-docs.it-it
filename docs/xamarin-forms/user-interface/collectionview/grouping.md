---
title: Raggruppamento Novell. Forms di CollectionView
description: CollectionView può visualizzare i dati raggruppati correttamente impostando la relativa proprietà Grouping su true.
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 8fd37999428c2813bbf96de3bcbd6ebd1fe0879d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69894028"
---
# <a name="xamarinforms-collectionview-grouping"></a>Raggruppamento Novell. Forms di CollectionView

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos)

I set di dati di grandi dimensioni possono spesso diventare difficoltosi quando vengono presentati in un elenco a scorrimento continuo. In questo scenario l'organizzazione dei dati in gruppi può migliorare l'esperienza utente semplificando l'esplorazione dei dati.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)supporta la visualizzazione di dati raggruppati e definisce le proprietà seguenti che controllano la modalità di presentazione:

- `IsGrouped`, di tipo `bool`, indica se i dati sottostanti devono essere visualizzati in gruppi. Il valore predefinito di questa proprietà è `false`.
- `GroupHeaderTemplate`, di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), il modello da utilizzare per l'intestazione di ogni gruppo.
- `GroupFooterTemplate`, di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), il modello da usare per il piè di pagina di ogni gruppo.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

> [!IMPORTANT]
> Il raggruppamento dei dati [`CollectionView`](xref:Xamarin.Forms.CollectionView) con è attualmente supportato solo in iOS.

## <a name="group-data"></a>Raggruppa dati

Prima di poter visualizzare i dati, è necessario raggrupparli. Questa operazione può essere eseguita creando un elenco di gruppi, in cui ogni gruppo è un elenco di elementi. L'elenco dei gruppi deve essere una `IEnumerable<T>` raccolta, dove `T` definisce due tipi di dati:

- Nome del gruppo.
- `IEnumerable` Raccolta che definisce gli elementi appartenenti al gruppo.

Il processo di raggruppamento dei dati, pertanto, consiste nel:

- Creare un tipo che modella un singolo elemento.
- Creare un tipo che modella un singolo gruppo di elementi.
- Creare una `IEnumerable<T>` raccolta, dove `T` è il tipo che modella un singolo gruppo di elementi. Questa raccolta è quindi una raccolta di gruppi che archivia i dati raggruppati.
- Aggiungere i dati alla `IEnumerable<T>` raccolta.

### <a name="example"></a>Esempio

Quando si raggruppano i dati, il primo passaggio consiste nel creare un tipo che modella un singolo elemento. Nell'esempio seguente viene illustrata la `Animal` classe dell'applicazione di esempio:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

La `Animal` classe modella un singolo elemento. È quindi possibile creare un tipo che modella un gruppo di elementi. Nell'esempio seguente viene illustrata la `AnimalGroup` classe dell'applicazione di esempio:

```csharp
public class AnimalGroup : List<Animal>
{
    public string Name { get; private set; }

    public AnimalGroup(string name, List<Animal> animals) : base(animals)
    {
        Name = name;
    }
}
```

La `AnimalGroup` classe eredita `List<T>` dalla classe e aggiunge una `Name` proprietà che rappresenta il nome del gruppo.

È `IEnumerable<T>` quindi possibile creare una raccolta di gruppi:

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

Questo codice definisce una raccolta denominata `Animals`, in cui ogni elemento della raccolta è un `AnimalGroup` oggetto. Ogni `AnimalGroup` oggetto è costituito da un nome `List<Animal>` e da una raccolta `Animal` che definisce gli oggetti nel gruppo.

I `Animals` dati raggruppati possono quindi essere aggiunti alla raccolta:

```csharp
Animals.Add(new AnimalGroup("Bears", new List<Animal>
{
    new Animal
    {
        Name = "American Black Bear",
        Location = "North America",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/0/08/01_Schwarzbär.jpg"
    },
    new Animal
    {
        Name = "Asian Black Bear",
        Location = "Asia",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/b/b7/Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG/180px-Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG"
    },
    // ...
}));

Animals.Add(new AnimalGroup("Monkeys", new List<Animal>
{
    new Animal
    {
        Name = "Baboon",
        Location = "Africa & Asia",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
    },
    new Animal
    {
        Name = "Capuchin Monkey",
        Location = "Central & South America",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
    },
    new Animal
    {
        Name = "Blue Monkey",
        Location = "Central and East Africa",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
    },
    // ...
}));
```

Questo codice crea due gruppi nella `Animals` raccolta. Il primo `AnimalGroup` è denominato `Bears`e contiene una `List<Animal>` raccolta di dettagli dell'orso. Il secondo `AnimalGroup` è denominato `Monkeys`e contiene una `List<Animal>` raccolta di dettagli scimmia.

## <a name="display-grouped-data"></a>Visualizza dati raggruppati

[`CollectionView`](xref:Xamarin.Forms.CollectionView)visualizzerà i dati raggruppati, a condizione che i dati siano stati raggruppati correttamente, impostando `IsGrouped` la `true`proprietà su:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                ...
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

Il codice C# equivalente è:

```csharp
CollectionView collectionView = new CollectionView
{
    IsGrouped = true
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
// ...
```

L'aspetto di ogni elemento in [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene definito impostando la [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) proprietà su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Per altre informazioni, vedere [definire l'aspetto dell'elemento](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance).

> [!NOTE]
> Per impostazione predefinita [`CollectionView`](xref:Xamarin.Forms.CollectionView) , visualizzerà il nome del gruppo nell'intestazione e nel piè di pagina del gruppo. Questo comportamento può essere modificato personalizzando l'intestazione di gruppo e il piè di pagina del gruppo.

## <a name="customize-the-group-header"></a>Personalizzare l'intestazione del gruppo

L'aspetto di ogni intestazione di gruppo può essere personalizzato impostando `CollectionView.GroupHeaderTemplate` la proprietà [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)su:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupHeaderTemplate>
        <DataTemplate>
            <Label Text="{Binding Name}"
                   BackgroundColor="LightGray"
                   FontSize="Large"
                   FontAttributes="Bold" />
        </DataTemplate>
    </CollectionView.GroupHeaderTemplate>
</CollectionView>
```

In questo esempio, ogni intestazione di gruppo è impostata su [`Label`](xref:Xamarin.Forms.Label) un oggetto che Visualizza il nome del gruppo e con altre proprietà di aspetto impostate.

## <a name="customize-the-group-footer"></a>Personalizzare il piè di pagina del gruppo

È possibile personalizzare l'aspetto di ogni piè di pagina del gruppo impostando la `CollectionView.GroupFooterTemplate` proprietà [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)su:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupFooterTemplate>
        <DataTemplate>
            <Label Text="{Binding Count, StringFormat='Total animals: {0:D}'}"
                   Margin="0,0,0,10" />
        </DataTemplate>
    </CollectionView.GroupFooterTemplate>
</CollectionView>
```

In questo esempio, ogni piè di pagina del gruppo è impostato [`Label`](xref:Xamarin.Forms.Label) su un oggetto che Visualizza il numero di elementi nel gruppo.

## <a name="empty-groups"></a>Gruppi vuoti

Quando un [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto Visualizza i dati raggruppati, verranno visualizzati tutti i gruppi vuoti. Tali gruppi verranno visualizzati con un'intestazione e un piè di pagina di gruppo, a indicare che il gruppo è vuoto.

> [!NOTE]
> In iOS 10 e versioni precedenti le intestazioni di gruppo e i piè di pagina per i gruppi vuoti possono essere tutti visualizzati `CollectionView`nella parte superiore del.

## <a name="group-without-templates"></a>Gruppo senza modelli

[`CollectionView`](xref:Xamarin.Forms.CollectionView)consente di visualizzare i dati raggruppati correttamente senza [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) impostare la proprietà [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)su un oggetto:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

In questo scenario è possibile visualizzare dati significativi eseguendo l'override del `ToString` metodo nel tipo che modella un singolo elemento e il tipo che modella un singolo gruppo di elementi.

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Modelli di dati Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
