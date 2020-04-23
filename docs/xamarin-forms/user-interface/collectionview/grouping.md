---
title: Raggruppamento di Raccolta Diamarin.FormsXamarin.Forms CollectionView Grouping
description: CollectionView può visualizzare correttamente i dati raggruppati impostando ne proprietà IsGrouped su true.
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 8360123b01f36bde084b4dc315109e6bdaef2207
ms.sourcegitcommit: 99aa05bd9b5e3f66d134066b860f41b54fa2d850
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103282"
---
# <a name="xamarinforms-collectionview-grouping"></a>Raggruppamento di Raccolta Diamarin.FormsXamarin.Forms CollectionView Grouping

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

I set di dati di grandi dimensioni possono spesso diventare ingombranti quando vengono presentati in un elenco a scorrimento continuo. In questo scenario, l'organizzazione dei dati in gruppi può migliorare l'esperienza utente semplificando l'esplorazione dei dati.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)supporta la visualizzazione di dati raggruppati e definisce le seguenti proprietà che controllano come verranno presentati:

- `IsGrouped`di tipo `bool`, indica se i dati sottostanti devono essere visualizzati in gruppi. Il valore predefinito di questa proprietà è `false`.
- `GroupHeaderTemplate`, di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)tipo , il modello da utilizzare per l'intestazione di ogni gruppo.
- `GroupFooterTemplate`, di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)tipo , il modello da utilizzare per il piè di pagina di ciascun gruppo.

Queste proprietà sono [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) supportate da oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

Le schermate seguenti [`CollectionView`](xref:Xamarin.Forms.CollectionView) mostrano una visualizzazione dei dati raggruppati:The following screenshots show a displaying grouped data:

[![Screenshot di un gruppo di dati in un controllo CollectionView, iOS e Android](grouping-images/grouped-data.png "CollectionView con dati raggruppati")](grouping-images/grouped-data-large.png#lightbox "CollectionView con dati raggruppati")

Per altre informazioni sui modelli di dati, vedere [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="group-data"></a>Raggruppare i dati

I dati devono essere raggruppati prima di poter essere visualizzati. Questa operazione può essere eseguita creando un elenco di gruppi, in cui ogni gruppo è un elenco di elementi. L'elenco dei gruppi `IEnumerable<T>` deve `T` essere una raccolta, in cui definisce due elementi di dati:The list of groups should be an collection, where defines two pieces of data:

- Un nome di gruppo.
- Raccolta `IEnumerable` che definisce gli elementi appartenenti al gruppo.

Il processo per il raggruppamento dei dati, pertanto, è quello di:

- Creare un tipo che modella un singolo elemento.
- Creare un tipo che modella un singolo gruppo di elementi.
- Creare `IEnumerable<T>` una raccolta, dove `T` è il tipo che modella un singolo gruppo di elementi. Questa raccolta è quindi una raccolta di gruppi, che archivia i dati raggruppati.
- Aggiungere dati `IEnumerable<T>` alla raccolta.

### <a name="example"></a>Esempio

Quando si raggruppano i dati, il primo passaggio consiste nel creare un tipo che modella un singolo elemento. Nell'esempio seguente `Animal` viene illustrata la classe dell'applicazione di esempio:The following example shows the class from the sample application:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

La `Animal` classe modella un singolo elemento. È quindi possibile creare un tipo che modella un gruppo di elementi. Nell'esempio seguente `AnimalGroup` viene illustrata la classe dell'applicazione di esempio:The following example shows the class from the sample application:

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

La `AnimalGroup` classe eredita `List<T>` dalla classe `Name` e aggiunge una proprietà che rappresenta il nome del gruppo.

È `IEnumerable<T>` quindi possibile creare una raccolta di gruppi:An collection of groups can then be created:

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

Questo codice definisce `Animals`un insieme denominato , in `AnimalGroup` cui ogni elemento dell'insieme è un oggetto . Ogni `AnimalGroup` oggetto comprende un nome `List<Animal>` e una `Animal` raccolta che definisce gli oggetti nel gruppo.

I dati raggruppati possono `Animals` quindi essere aggiunti alla raccolta:Grouped data can then be added to the collection:

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
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
    },
    new Animal
    {
        Name = "Capuchin Monkey",
        Location = "Central & South America",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
    },
    new Animal
    {
        Name = "Blue Monkey",
        Location = "Central and East Africa",
        Details = "Details about the monkey go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
    },
    // ...
}));
```

Questo codice crea due `Animals` gruppi nella raccolta. Il `AnimalGroup` primo `Bears`è denominato `List<Animal>` , e contiene una raccolta di dettagli sull'orso. Il `AnimalGroup` secondo `Monkeys`è denominato `List<Animal>` , e contiene una raccolta di dettagli della scimmia.

## <a name="display-grouped-data"></a>Visualizzare dati raggruppati

[`CollectionView`](xref:Xamarin.Forms.CollectionView)visualizzerà i dati raggruppati, a condizione che i `IsGrouped` dati `true`siano stati raggruppati correttamente, impostando la proprietà su :

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

L'aspetto di ogni [`CollectionView`](xref:Xamarin.Forms.CollectionView) elemento in [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) viene definito [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)impostando la proprietà su un oggetto . Per ulteriori informazioni, consultate [Definire l'aspetto degli elementi.](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance)

> [!NOTE]
> Per impostazione predefinita, [`CollectionView`](xref:Xamarin.Forms.CollectionView) il nome del gruppo verrà visualizzato nell'intestazione e nel piè di pagina del gruppo. Questo comportamento può essere modificato personalizzando l'intestazione di gruppo e il piè di pagina di gruppo.

## <a name="customize-the-group-header"></a>Personalizzare l'intestazione del gruppo

L'aspetto di ogni intestazione di `CollectionView.GroupHeaderTemplate` gruppo può [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)essere personalizzato impostando la proprietà su :

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

In questo esempio, ogni intestazione [`Label`](xref:Xamarin.Forms.Label) di gruppo è impostata su un che visualizza il nome del gruppo e che dispone di altre proprietà di aspetto impostate. Le schermate seguenti mostrano l'intestazione di gruppo personalizzata:The following screenshots show the customized group header:

[![Screenshot di un'intestazione di gruppo personalizzata in un oggetto CollectionView, in iOS e Android](grouping-images/customized-header.png "CollectionView con intestazione di gruppo personalizzata")](grouping-images/customized-header-large.png#lightbox "CollectionView con intestazione di gruppo personalizzata")

## <a name="customize-the-group-footer"></a>Personalizzare il piè di pagina di gruppo

L'aspetto di ogni piè di pagina `CollectionView.GroupFooterTemplate` di [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)gruppo può essere personalizzato impostando la proprietà su :

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

In questo esempio, ogni piè [`Label`](xref:Xamarin.Forms.Label) di pagina di gruppo è impostato su un che visualizza il numero di elementi nel gruppo. Le schermate seguenti mostrano il piè di pagina di gruppo personalizzato:

[![Screenshot di un piè di pagina di gruppo personalizzato in un controllo CollectionView, in iOS e Android](grouping-images/customized-footer.png "Visualizzazione Raccolta con piè di pagina di gruppo personalizzato")](grouping-images/customized-footer-large.png#lightbox "Visualizzazione Raccolta con piè di pagina di gruppo personalizzato")

## <a name="empty-groups"></a>Gruppi vuoti

Quando [`CollectionView`](xref:Xamarin.Forms.CollectionView) un oggetto visualizza dati raggruppati, visualizza tutti i gruppi vuoti. Tali gruppi verranno visualizzati con un'intestazione di gruppo e piè di pagina, che indica che il gruppo è vuoto. Le schermate seguenti mostrano un gruppo vuoto:The following screenshots show an empty group:

[![Screenshot di un gruppo vuoto in un controllo CollectionView, iOS e Android](grouping-images/empty-group.png "CollectionView con un gruppo vuoto")](grouping-images/empty-group-large.png#lightbox "CollectionView con un gruppo vuoto")

> [!NOTE]
> In iOS 10 e inferiori, le intestazioni di gruppo e i piè `CollectionView`di pagina per i gruppi vuoti possono essere tutti visualizzati nella parte superiore del file .

## <a name="group-without-templates"></a>Gruppo senza modelli

[`CollectionView`](xref:Xamarin.Forms.CollectionView)visualizzare correttamente i dati [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) raggruppati [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)senza impostare la proprietà su:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

In questo scenario, è possibile visualizzare dati `ToString` significativi eseguendo l'override del metodo nel tipo che modella un singolo elemento e del tipo che modella un singolo gruppo di elementi.

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)CollectionView (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Modelli di dati Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
