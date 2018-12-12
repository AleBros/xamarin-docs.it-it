---
title: Visualizzazione di raccolta di xamarin. Forms
description: La visualizzazione di raccolta è una visualizzazione flessibile ed efficiente per presentare elenchi di dati usando le specifiche di un layout diverso.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2018
ms.openlocfilehash: 46ab8169b31624e3862cf14f477bcd6cf8c8f3a3
ms.sourcegitcommit: 408b78dd6eded4696469e316af7922a5991f2211
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53246308"
---
# <a name="xamarinforms-collectionview"></a>Visualizzazione di raccolta di xamarin. Forms

![Anteprima](~/media/shared/preview.png)

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

`CollectionView` è una vista per presentare elenchi di dati usando le specifiche di un layout diverso. L'obiettivo è fornire una più flessibile ed efficace alternativa a `ListView`. Mentre il `CollectionView` e `ListView` sono simili, esistono alcune differenze significative:

- `CollectionView` è presente alcun concetto di celle. Al contrario, i modelli di dati vengono usati per definire l'aspetto di ogni elemento di dati nell'elenco.
- `CollectionView` Consente di ridurre la superficie dell'API `ListView`. Molte proprietà e gli eventi da `ListView` non sono presenti nel `CollectionView`.
- `CollectionView` dispone di un modello di layout flessibile che consente di essere visualizzato orizzontalmente o verticalmente, in un elenco o una griglia dati.

A `CollectionView` utilizzato dall'impostazione relativa `ItemsSource` a qualsiasi insieme che implementa `IEnumerable`e la relativa `ItemTemplate` proprietà da un `DataTemplate` che definisce ogni aspetto dell'elemento di elenco. Inoltre, relativi `ItemsLayout` proprietà deve essere impostata su un `ItemsLayout` (classe), per definire la specifica del layout per l'elenco.

> [!IMPORTANT]
> Il `CollectionView` è attualmente un'anteprima e non dispone di molte delle relative funzionalità pianificato. Inoltre, l'API modificheranno l'implementazione è stata completata.

`CollectionView` è disponibile in xamarin. Forms. 4.0-pre1. Tuttavia, è attualmente in fase sperimentale e può essere usato solo aggiungendo la riga seguente di codice per il `AppDelegate` classe in iOS o alle `MainActivity` classe in Android, prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

## <a name="populating-a-collectionview-with-data"></a>Popolamento di una visualizzazione di raccolta dati

Oggetto `CollectionView` viene popolata con i dati tramite l'impostazione relativa `ItemSource` a qualsiasi insieme che implementa `IEnumerable`. È possibile aggiungere elementi in XAML inizializzando il `ItemsSource` proprietà da una matrice di elementi:

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
> Si noti che il `x:Array` elemento richiede un `Type` attributo che indica il tipo degli elementi nella matrice.

Inoltre, un `CollectionView` possono essere popolati con dati utilizzando un'associazione dati per l'associazione relativo `ItemsSource` proprietà a un `IEnumerable` raccolta. In XAML questo si ottiene con la `Binding` estensione di markup:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

In questo esempio, il `ItemsSource` esegue l'associazione dati di proprietà per il `Monkeys` proprietà del modello di visualizzazione connessa, che restituisce un `IList<Monkey>` raccolta. Nell'esempio di codice riportato di seguito viene illustrato il `Monkey` (classe), che contiene quattro proprietà:

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

## <a name="providing-a-view-for-the-empty-state"></a>Fornire una visualizzazione per lo stato vuoto

Quando il `CollectionView` non dispone di tutti i dati da visualizzare, può essere visualizzato un messaggio adatto all'utente. Questa operazione viene eseguita impostando il `CollectionView.EmptyView` proprietà da un' `object` che verrà visualizzato quando la raccolta specificata dal `ItemSource` proprietà è vuota:

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}">
    <CollectionView.EmptyView>
        <Label Text="No items to display" />
    </CollectionView.EmptyView>
</CollectionView>
```

Il `EmptyView` può essere impostata su un `string`, un'associazione o una vista e pertanto può includere contenuti interattivi, se necessario.

Inoltre, il `EmptyViewTemplate` può essere impostata su una `DataTemplate` che verrà utilizzato per formattare il `EmptyView`.

## <a name="defining-list-item-appearance"></a>La definizione dell'aspetto degli elementi elenco

L'aspetto dei dati per ogni elemento nel `CollectionView` può essere definita impostando la `CollectionView.ItemTemplate` proprietà su un `DataTemplate`:

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

In questo esempio, il `DataTemplate` contiene un `Grid` con un `Image`e due `Label` istanze, che tutti i binding alle proprietà del `Monkey` oggetto.

Per altre informazioni sui modelli di dati, vedere [modelli di Data](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="specifying-a-layout"></a>Specificare un layout

Per impostazione predefinita, un `CollectionView` visualizzerà i relativi elementi in un elenco verticale. Tuttavia, le seguenti specifiche di layout dei consente di:

- Elenco verticale: un elenco a colonna singola che si espande verticalmente man mano che vengono aggiunti nuovi elementi.
- Elenco orizzontale: un elenco di singola riga che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi.
- Griglia verticale – una griglia a più colonne che si espande verticalmente man mano che vengono aggiunti nuovi elementi.
- Griglia orizzontale – una griglia con più righe che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi.

Questi layout possono essere specificati impostando il `CollectionView.ItemsLayout` proprietà per un `ItemsLayout` (classe), con la `ListItemsLayout` classe che fornisce un layout, elenco e il `GridItemsLayout` classe che fornisce un layout di griglia.

Il `ItemsLayout` classe definisce i `Orientation` proprietà di tipo `ItemsLayoutOrientation`. Il `ItemsLayoutOrientation` enumerazione definisce `Vertical` e `Horizontal` valori del membro.

Il `ListItemsLayout` classe eredita dal `ItemsLayout` classe e definisce static `VerticalList` e `HorizontalList` membri. Questi membri sono utilizzabile per creare elenchi orizzontali o verticali, rispettivamente. In alternativa, un `ListItemsLayout` istanza può essere creata, specificando un `ItemsLayoutOrientation` membro di enumerazione come argomento.

Il `GridItemsLayout` classe eredita dal `ItemsLayout` classe e definisce una `Span` proprietà, di tipo `int`, che rappresenta il numero di colonne o righe da visualizzare nella griglia. Il valore predefinito di `Span` proprietà è 1 e il relativo valore deve sempre essere maggiore o uguale a 1.

### <a name="vertical-list"></a>Elenco verticale

Per impostazione predefinita, un `CollectionView` visualizzerà i relativi elementi in un layout di elenco verticale. Pertanto, non è necessario impostare il `ItemsLayout` proprietà da utilizzare per questo layout:

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

In alternativa, questa può essere eseguita anche impostando il `ItemsLayout` proprietà a un'istanza del `ListItemsLayout` classe, specificando la `Vertical` `ItemsLayoutOrientation` membro di enumerazione come argomento:

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

Ciò comporta un elenco a colonna singola, verticalmente aumenta man mano che vengono aggiunti nuovi elementi:

[![Layout verticale elenco CollectionView](collectionview-images/vertical-list.png "layout di visualizzazione di raccolta elenco verticale")](collectionview-images/vertical-list-large.png#lightbox)

### <a name="horizontal-list"></a>Elenco orizzontale

Oggetto `CollectionView` può visualizzare i relativi elementi in un elenco orizzontale mediante l'impostazione relativa `ItemsLayout` proprietà statica `ListItemsLayout.HorizontalList` membro:

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

In alternativa, questa può essere eseguita anche impostando il `ItemsLayout` proprietà a un'istanza del `ListItemsLayout` classe, specificando la `Horizontal` `ItemsLayoutOrientation` membro di enumerazione come argomento:

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

Ciò comporta in un elenco di singola riga, che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi:

[![Layout orizzontale elenco CollectionView](collectionview-images/horizontal-list.png "layout di visualizzazione di raccolta elenco orizzontale")](collectionview-images/horizontal-list-large.png#lightbox)

### <a name="vertical-grid"></a>Griglia verticale

Oggetto `CollectionView` possono visualizzare i relativi elementi in una griglia verticale impostando relativo `ItemsLayout` proprietà a un `GridItemsLayout` istanza la cui proprietà `Orientation` viene impostata su `Vertical`:

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

Per impostazione predefinita, un parametro vertical `GridItemsLayout` visualizzerà gli elementi in una singola colonna. Tuttavia, questo esempio viene impostato il `GridItemsLayout.Span` proprietà su 2. Ciò comporta una griglia di due colonne, in senso verticale aumenta man mano che vengono aggiunti nuovi elementi:

[![Layout di griglia verticali CollectionView](collectionview-images/vertical-grid.png "CollectionView layout di griglia verticali")](collectionview-images/vertical-grid-large.png#lightbox)

### <a name="horizontal-grid"></a>Griglia orizzontale

Un `CollectionView` possono visualizzare i relativi elementi in una griglia orizzontale mediante l'impostazione relativa `ItemsLayout` proprietà a un `GridItemsLayout` istanza la cui proprietà `Orientation` è impostata su `Horizontal`:

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

Per impostazione predefinita, un controllo orizzontale `GridItemsLayout` visualizzerà gli elementi in una singola riga. Tuttavia, questo esempio viene impostato il `GridItemsLayout.Span` proprietà a 4. Ciò comporta una griglia di quattro righe, che si espande orizzontalmente man mano che vengono aggiunti nuovi elementi:

[![Layout di griglia orizzontale CollectionView](collectionview-images/horizontal-grid.png "CollectionView layout di griglia orizzontale")](collectionview-images/horizontal-grid-large.png#lightbox)

## <a name="scrolling"></a>Lo scorrimento

`CollectionView` può scorrere gli elementi richiesti nella visualizzazione con il `ScrollTo` (metodo). Questo metodo scorre l'elemento in corrispondenza dell'indice specificato all'interno della visualizzazione:

```csharp
_collectionView.ScrollTo(12);
```

In alternativa, un overload di questo metodo è utilizzabile per scorrere l'elemento specificato all'interno della visualizzazione:

```csharp
var viewModel = BindingContext as MonkeysViewModel;
var monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
_collectionView.ScrollTo(monkey);
```

Entrambi gli overload consentono argomenti aggiuntivi specificare che indicano il gruppo di cui che l'elemento si trova, la posizione esatta dell'elemento dopo lo scorrimento è completato (avvio), allineare al centro, fine e se si desidera animare lo scorrimento.

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di raccolta (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
