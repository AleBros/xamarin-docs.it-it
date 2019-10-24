---
title: Raggruppamento Novell. Forms di CollectionView
description: CollectionView può visualizzare i dati raggruppati correttamente impostando la relativa proprietà Grouping su true.
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 3a0fe7159e6af24d58e49dea4166d012605c9985
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749799"
---
# <a name="xamarinforms-collectionview-grouping"></a>Raggruppamento Novell. Forms di CollectionView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

I set di dati di grandi dimensioni possono spesso diventare difficoltosi quando vengono presentati in un elenco a scorrimento continuo. In questo scenario l'organizzazione dei dati in gruppi può migliorare l'esperienza utente semplificando l'esplorazione dei dati.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) supporta la visualizzazione di dati raggruppati e definisce le proprietà seguenti che controllano la modalità di presentazione:

- `IsGrouped`, di tipo `bool`, indica se i dati sottostanti devono essere visualizzati in gruppi. Il valore predefinito di questa proprietà è `false`.
- `GroupHeaderTemplate`, di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), il modello da utilizzare per l'intestazione di ogni gruppo.
- `GroupFooterTemplate`, di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), il modello da utilizzare per il piè di pagina di ogni gruppo.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che le proprietà possono essere destinazioni di associazioni dati.

Gli screenshot seguenti mostrano un [`CollectionView`](xref:Xamarin.Forms.CollectionView) la visualizzazione dei dati raggruppati:

[![Screenshot di dati raggruppati in un oggetto CollectionView, in iOS e Android](grouping-images/grouped-data.png "CollectionView con dati raggruppati")](grouping-images/grouped-data-large.png#lightbox "CollectionView con dati raggruppati")

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="group-data"></a>Raggruppa dati

Prima di poter visualizzare i dati, è necessario raggrupparli. Questa operazione può essere eseguita creando un elenco di gruppi, in cui ogni gruppo è un elenco di elementi. L'elenco dei gruppi deve essere una raccolta `IEnumerable<T>`, in cui `T` definisce due tipi di dati:

- Nome del gruppo.
- Raccolta di `IEnumerable` che definisce gli elementi appartenenti al gruppo.

Il processo di raggruppamento dei dati, pertanto, consiste nel:

- Creare un tipo che modella un singolo elemento.
- Creare un tipo che modella un singolo gruppo di elementi.
- Creare una raccolta di `IEnumerable<T>`, dove `T` è il tipo che modella un singolo gruppo di elementi. Questa raccolta è quindi una raccolta di gruppi che archivia i dati raggruppati.
- Aggiungere i dati alla raccolta di `IEnumerable<T>`.

### <a name="example"></a>Esempio

Quando si raggruppano i dati, il primo passaggio consiste nel creare un tipo che modella un singolo elemento. Nell'esempio seguente viene illustrata la classe `Animal` dall'applicazione di esempio:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

La classe `Animal` modella un singolo elemento. È quindi possibile creare un tipo che modella un gruppo di elementi. Nell'esempio seguente viene illustrata la classe `AnimalGroup` dall'applicazione di esempio:

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

La classe `AnimalGroup` eredita dalla classe `List<T>` e aggiunge una proprietà `Name` che rappresenta il nome del gruppo.

È quindi possibile creare una raccolta `IEnumerable<T>` di gruppi:

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

Questo codice definisce una raccolta denominata `Animals`, in cui ogni elemento della raccolta è un oggetto `AnimalGroup`. Ogni oggetto `AnimalGroup` è costituito da un nome e da una raccolta `List<Animal>` che definisce gli oggetti `Animal` nel gruppo.

I dati raggruppati possono quindi essere aggiunti alla raccolta `Animals`:

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

Questo codice crea due gruppi nella raccolta `Animals`. Il primo `AnimalGroup` è denominato `Bears` e contiene una raccolta di `List<Animal>` di dettagli dell'orso. Il secondo `AnimalGroup` è denominato `Monkeys` e contiene una raccolta `List<Animal>` di dettagli scimmia.

## <a name="display-grouped-data"></a>Visualizza dati raggruppati

[`CollectionView`](xref:Xamarin.Forms.CollectionView) visualizzerà i dati raggruppati, a condizione che i dati siano stati raggruppati correttamente, impostando la proprietà `IsGrouped` su `true`:

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

Il codice C# equivalente è il seguente:

```csharp
CollectionView collectionView = new CollectionView
{
    IsGrouped = true
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
// ...
```

L'aspetto di ogni elemento nel [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene definito impostando la proprietà [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Per altre informazioni, vedere [definire l'aspetto dell'elemento](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance).

> [!NOTE]
> Per impostazione predefinita, [`CollectionView`](xref:Xamarin.Forms.CollectionView) visualizzerà il nome del gruppo nell'intestazione e nel piè di pagina del gruppo. Questo comportamento può essere modificato personalizzando l'intestazione di gruppo e il piè di pagina del gruppo.

## <a name="customize-the-group-header"></a>Personalizzare l'intestazione del gruppo

È possibile personalizzare l'aspetto di ogni intestazione di gruppo impostando la proprietà `CollectionView.GroupHeaderTemplate` su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

In questo esempio, ogni intestazione di gruppo è impostata su un [`Label`](xref:Xamarin.Forms.Label) che Visualizza il nome del gruppo e con altre proprietà di aspetto impostate. Gli screenshot seguenti mostrano l'intestazione di gruppo personalizzata:

[![Screenshot di un'intestazione di gruppo personalizzata in un CollectionView, in iOS e Android](grouping-images/customized-header.png "CollectionView con intestazione di gruppo personalizzata")](grouping-images/customized-header-large.png#lightbox "CollectionView con intestazione di gruppo personalizzata")

## <a name="customize-the-group-footer"></a>Personalizzare il piè di pagina del gruppo

È possibile personalizzare l'aspetto di ogni piè di pagina del gruppo impostando la proprietà `CollectionView.GroupFooterTemplate` su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

In questo esempio, ogni piè di pagina del gruppo è impostato su un [`Label`](xref:Xamarin.Forms.Label) che Visualizza il numero di elementi nel gruppo. Gli screenshot seguenti mostrano il piè di pagina di gruppo personalizzato:

[![Screenshot di un piè di pagina di gruppo personalizzato in un CollectionView, in iOS e Android](grouping-images/customized-footer.png "CollectionView con piè di pagina gruppo personalizzato")](grouping-images/customized-footer-large.png#lightbox "CollectionView con piè di pagina gruppo personalizzato")

## <a name="empty-groups"></a>Gruppi vuoti

Quando un [`CollectionView`](xref:Xamarin.Forms.CollectionView) Visualizza i dati raggruppati, verranno visualizzati tutti i gruppi vuoti. Tali gruppi verranno visualizzati con un'intestazione e un piè di pagina di gruppo, a indicare che il gruppo è vuoto. Gli screenshot seguenti mostrano un gruppo vuoto:

[![Screenshot di un gruppo vuoto in un oggetto CollectionView, in iOS e Android](grouping-images/empty-group.png "CollectionView con un gruppo vuoto")](grouping-images/empty-group-large.png#lightbox "CollectionView con un gruppo vuoto")

> [!NOTE]
> In iOS 10 e versioni precedenti le intestazioni di gruppo e i piè di pagina per i gruppi vuoti possono essere tutti visualizzati nella parte superiore del `CollectionView`.

## <a name="group-without-templates"></a>Gruppo senza modelli

[`CollectionView`](xref:Xamarin.Forms.CollectionView) possibile visualizzare i dati raggruppati correttamente senza impostare la proprietà [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

In questo scenario è possibile visualizzare dati significativi eseguendo l'override del metodo `ToString` nel tipo che modella un singolo elemento e il tipo che modella un singolo gruppo di elementi.

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Modelli di dati Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
