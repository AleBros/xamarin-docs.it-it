---
title: Pin mappa di Novell. Forms
description: Questo articolo illustra come creare pin in una mappa Novell. Forms.
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/23/2019
ms.openlocfilehash: 76535f9c31a9dc138e132a3e582b986daf89bdb0
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697339"
---
# <a name="xamarinforms-map-pins"></a>Pin mappa di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Il controllo `Maps` Novell. Forms consente di contrassegnare le posizioni con `Pin` oggetti. Un `Pin` è un marcatore di mappa che consente di aprire una finestra informazioni quando si fa clic o si toccano.

La classe `Pin` presenta le proprietà seguenti:

- `Type` è un valore enum `PinType`: Generic, Place, SavedPin o SearchResult.
- `Position` è un'istanza di `Position` che contiene la latitudine e la longitudine del PIN.
- `Label` è un `string` che viene in genere visualizzato come titolo del PIN.
- `Address` è un `string` che verrà visualizzato nella finestra informazioni. Può trattarsi di qualsiasi contenuto `string` e non solo di un indirizzo.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che l'`Pin` può essere la destinazione di associazioni dati. Per altre informazioni, vedere [creare pin con data binding](#create-pins-with-data-binding).

## <a name="create-map-pins"></a>Crea PIN mappa

È possibile creare un'istanza di `Pin` nel codice e aggiungerla a una mappa:

```csharp
Pin pin1 = new Pin
{
    Type = PinType.Place,
    Position = new Position(47.6368678, -122.137305),
    Label = "Example Pin 1",
    Address = "Example custom details..."
};
map.Pins.Add(pin1);
```

> [!NOTE]
> Il valore `PinType` influiscono sulla modalità di rendering dei pin a seconda della piattaforma. Per personalizzare l'aspetto di un PIN, è necessario creare un renderer personalizzato. Per altre informazioni, vedere [personalizzazione di un PIN della mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

## <a name="create-pins-with-data-binding"></a>Creazione di pin con data binding

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) espone le proprietà seguenti:

- `ItemsSource`: specifica la raccolta di elementi `IEnumerable` da visualizzare.
- `ItemTemplate`: specifica il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) da applicare a ogni elemento nella raccolta di elementi visualizzati.
- `ItemTemplateSelector`: specifica il [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) che verrà usato per scegliere un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) per un elemento in fase di esecuzione.

> [!NOTE]
> La proprietà `ItemTemplate` ha la precedenza quando vengono impostate entrambe le proprietà `ItemTemplate` e `ItemTemplateSelector`.

Un [`Map`](xref:Xamarin.Forms.Maps.Map) può essere popolato con i dati utilizzando data binding per associare la relativa proprietà `ItemsSource` a una raccolta `IEnumerable`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  MoveToLastRegionOnLayoutChange="false"
                  ItemsSource="{Binding Locations}">
            <maps:Map.ItemTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </maps:Map.ItemTemplate>
        </maps:Map>
        ...
    </Grid>
</ContentPage>
```

I dati della proprietà `ItemsSource` vengono associati alla proprietà `Locations` del modello di visualizzazione connesso, che restituisce un `ObservableCollection` di `Location` oggetti, che è un tipo personalizzato. Ogni oggetto `Location` definisce proprietà `Address` e `Description`, di tipo `string` e una proprietà `Position`, di tipo [`Position`](xref:Xamarin.Forms.Maps.Position).

L'aspetto di ogni elemento nella raccolta di `IEnumerable` viene definito impostando la proprietà `ItemTemplate` su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) contenente un oggetto [`Pin`](xref:Xamarin.Forms.Maps.Pin) a cui i dati vengono associati alle proprietà appropriate.

Gli screenshot seguenti mostrano un [`Map`](xref:Xamarin.Forms.Maps.Map) la visualizzazione di una raccolta di [`Pin`](xref:Xamarin.Forms.Maps.Pin) tramite Data Binding:

[![Screenshot della mappa con i pin associati ai dati, in iOS e Android](map-images/pins-itemssource.png "Mappa con pin associati a dati")](map-images/pins-itemssource-large.png#lightbox "Mappa con pin associati a dati")

### <a name="choose-item-appearance-at-runtime"></a>Scegli aspetto elemento in fase di esecuzione

È possibile scegliere l'aspetto di ogni elemento nella raccolta `IEnumerable` in fase di esecuzione, in base al valore dell'elemento, impostando la proprietà `ItemTemplateSelector` su un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:WorkingWithMaps"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <ContentPage.Resources>
        <local:MapItemTemplateSelector x:Key="MapItemTemplateSelector">
            <local:MapItemTemplateSelector.DefaultTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </local:MapItemTemplateSelector.DefaultTemplate>
            <local:MapItemTemplateSelector.XamarinTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="Xamarin!" />
                </DataTemplate>
            </local:MapItemTemplateSelector.XamarinTemplate>    
        </local:MapItemTemplateSelector>
    </ContentPage.Resources>

    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}"
                  ItemTemplateSelector="{StaticResource MapItemTemplateSelector}" />
        ...
    </Grid>
</ContentPage>
```

Nell'esempio seguente viene illustrata la classe `MapItemTemplateSelector`:

```csharp
public class MapItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Location)item).Address.Contains("San Francisco") ? XamarinTemplate : DefaultTemplate;
    }
}
```

La classe `MapItemTemplateSelector` definisce `DefaultTemplate` e `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà impostate su modelli di dati diversi. Il metodo `OnSelectTemplate` restituisce il `XamarinTemplate`, che Visualizza "Novell" come etichetta quando viene toccato un `Pin`, quando l'elemento ha un indirizzo che contiene "San Francisco". Quando l'elemento non dispone di un indirizzo che contiene "San Francisco", il metodo `OnSelectTemplate` restituisce l'`DefaultTemplate`.

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Creating a Novell. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="pin-events"></a>Blocca eventi

La classe `Pin` fornisce due eventi:

- `MarkerClicked` viene generato quando si fa clic o si tocca il PIN.
- `InfoWindowClicked` viene generato quando si fa clic o si tocca la finestra informazioni.

I gestori di eventi possono essere associati a un pin nel codice:

```csharp
public class PinEvents: ContentPage
{
    public PinEvents()
    {
        // ...

        pin1.MarkerClicked += OnMarkerClickedAsync;
        pin1.InfoWindowClicked += OnInfoWindowClickedAsync;
    }

    async void OnMarkerClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
    }

    async void OnInfoWindowClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
    }
}
```

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Renderer personalizzato mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Creazione di un DataTemplateSelector Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
