---
title: Pin mappa di Novell. Forms
description: Questo articolo illustra come creare pin in una mappa Novell. Forms.
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2019
ms.openlocfilehash: 930d2dcc701f88e2a350ec1011405bb18b86de6e
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425552"
---
# <a name="xamarinforms-map-pins"></a>Pin mappa di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Il controllo [`Map`](xref:Xamarin.Forms.Maps.Map) Novell. Forms consente di contrassegnare le posizioni con [`Pin`](xref:Xamarin.Forms.Maps.Pin) oggetti. Un `Pin` è un marcatore di mappa che apre una finestra informazioni quando viene toccato:

[![Screenshot di un PIN della mappa e della relativa finestra informazioni, in iOS e Android](pins-images/pin-and-information-window.png "Pin mappa con finestra informazioni")](pins-images/pin-and-information-window-large.png#lightbox "Pin mappa con finestra informazioni")

Quando un oggetto [`Pin`](xref:Xamarin.Forms.Maps.Pin) viene aggiunto alla raccolta [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) , il rendering del PIN viene eseguito sulla mappa.

La classe [`Pin`](xref:Xamarin.Forms.Maps.Pin) presenta le proprietà seguenti:

- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address), di tipo `string`, che rappresenta in genere l'indirizzo della posizione del PIN. Tuttavia, può trattarsi di qualsiasi contenuto `string` e non solo di un indirizzo.
- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label), di tipo `string`, che in genere rappresenta il titolo del PIN.
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position), di tipo [`Position`](xref:Xamarin.Forms.Maps.Position), che rappresenta la latitudine e la longitudine del PIN.
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type), di tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType), che rappresenta il tipo di pin.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ovvero una `Pin` può essere la destinazione di associazioni dati. Per ulteriori informazioni sugli oggetti data binding `Pin`, vedere [visualizzare una raccolta di pin](#display-a-pin-collection).

Inoltre, la classe [`Pin`](xref:Xamarin.Forms.Maps.Pin) definisce gli eventi `MarkerClicked` e `InfoWindowClicked`. L'evento `MarkerClicked` viene generato quando viene toccato un PIN e viene generato l'evento `InfoWindowClicked` quando viene toccata la finestra informazioni. L'oggetto `PinClickedEventArgs` che accompagna entrambi gli eventi dispone di una singola proprietà `HideInfoWindow`, di tipo `bool`.

## <a name="display-a-pin"></a>Visualizzare un pin

È possibile aggiungere una [`Pin`](xref:Xamarin.Forms.Maps.Pin) a un [`Map`](xref:Xamarin.Forms.Maps.Map) in XAML:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map x:Name="map"
               IsShowingUser="True"
               MoveToLastRegionOnLayoutChange="False">
         <x:Arguments>
             <maps:MapSpan>
                 <x:Arguments>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>36.9628066</x:Double>
                             <x:Double>-122.0194722</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     <x:Double>0.01</x:Double>
                     <x:Double>0.01</x:Double>
                 </x:Arguments>
             </maps:MapSpan>
         </x:Arguments>
         <maps:Map.Pins>
             <maps:Pin Label="Santa Cruz"
                       Address="The city with a boardwalk"
                       Type="Place">
                 <maps:Pin.Position>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>36.9628066</x:Double>
                             <x:Double>-122.0194722</x:Double>
                         </x:Arguments>
                     </maps:Position>
                 </maps:Pin.Position>
             </maps:Pin>
         </maps:Map.Pins>
     </maps:Map>
</ContentPage>
```

Questo codice XAML crea un oggetto [`Map`](xref:Xamarin.Forms.Maps.Map) che mostra l'area specificata dall'oggetto [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) . L'oggetto `MapSpan` viene centrato sulla latitudine e la Longitudine rappresentate da un oggetto [`Position`](xref:Xamarin.Forms.Maps.Position) , che estende 0,01 di latitudine e longitudine. Un oggetto [`Pin`](xref:Xamarin.Forms.Maps.Pin) viene aggiunto alla raccolta [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) e disegnato sulla `Map` nel percorso specificato dalla relativa proprietà [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) . Per informazioni sul [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vedere [posizione e distanza della mappa](position-distance.md). Per informazioni sul passaggio di argomenti in XAML a oggetti che non dispongono di costruttori predefiniti, vedere [passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md).

Il codice C# equivalente è il seguente:

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
Pin pin = new Pin
{
  Label = "Santa Cruz",
  Address = "The city with a boardwalk",
  Type = PinType.Place,
  Position = new Position(36.9628066, -122.0194722)
};
map.Pins.Add(pin);
```

> [!WARNING]
> Se non si imposta la proprietà [`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label) , viene generata un'`ArgumentException` quando il [`Pin`](xref:Xamarin.Forms.Maps.Pin) viene aggiunto a una [`Map`](xref:Xamarin.Forms.Maps.Map).

Questo esempio di codice comporta il rendering di un singolo pin su una mappa:

[![Screenshot di un pin mappa, in iOS e Android](pins-images/pin-only.png "Pin mappa")](pins-images/pin-only-large.png#lightbox "Pin mappa")

## <a name="interact-with-a-pin"></a>Interagire con un pin

Per impostazione predefinita, quando viene toccata una [`Pin`](xref:Xamarin.Forms.Maps.Pin) viene visualizzata la finestra delle informazioni:

[![Screenshot di un PIN della mappa e della relativa finestra informazioni, in iOS e Android](pins-images/pin-and-information-window.png "Pin mappa con finestra informazioni")](pins-images/pin-and-information-window-large.png#lightbox "Pin mappa con finestra informazioni")

Se si tocca altrove sulla mappa, la finestra informazioni verrà chiusa.

La classe [`Pin`](xref:Xamarin.Forms.Maps.Pin) definisce un evento `MarkerClicked`, che viene generato quando viene toccato un `Pin`. Non è necessario gestire questo evento per visualizzare la finestra informazioni. Questo evento deve essere invece gestito quando è necessario ricevere una notifica che indica che è stato toccato un PIN specifico.

La classe [`Pin`](xref:Xamarin.Forms.Maps.Pin) definisce anche un evento `InfoWindowClicked` generato quando viene toccata una finestra informativa. Questo evento deve essere gestito quando è necessario ricevere una notifica per informare che è stata toccata una finestra di informazioni specifica.

Il codice seguente illustra un esempio di gestione di questi eventi:

```csharp
using Xamarin.Forms.Maps;
// ...
Pin boardwalkPin = new Pin
{
    Position = new Position(36.9641949, -122.0177232),
    Label = "Boardwalk",
    Address = "Santa Cruz",
    Type = PinType.Place
};
boardwalkPin.MarkerClicked += async (s, args) =>
{
    args.HideInfoWindow = true;
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
};

Pin wharfPin = new Pin
{
    Position = new Position(36.9571571, -122.0173544),
    Label = "Wharf",
    Address = "Santa Cruz",
    Type = PinType.Place
};
wharfPin.InfoWindowClicked += async (s, args) =>
{
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
};
```

L'oggetto `PinClickedEventArgs` che accompagna entrambi gli eventi dispone di una singola proprietà `HideInfoWindow`, di tipo `bool`. Quando questa proprietà è impostata su `true` all'interno di un gestore eventi, la finestra informazioni sarà nascosta.

## <a name="pin-types"></a>Tipi di pin

[`Pin`](xref:Xamarin.Forms.Maps.Pin) oggetti includono una proprietà [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) , di tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType), che rappresenta il tipo di pin. L'enumerazione `PinType` definisce i membri seguenti:

- `Generic`, rappresenta un pin generico.
- `Place`, rappresenta un PIN per una posizione.
- `SavedPin`, rappresenta un PIN per un percorso salvato.
- `SearchResult`, rappresenta un PIN per un risultato della ricerca.

Tuttavia, l'impostazione della proprietà [`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type) su qualsiasi membro [`PinType`](xref:Xamarin.Forms.Maps.PinType) non modifica l'aspetto del pin sottoposto a rendering. Al contrario, è necessario creare un renderer personalizzato per personalizzare l'aspetto del PIN. Per altre informazioni, vedere [personalizzazione di un PIN della mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

## <a name="display-a-pin-collection"></a>Visualizzare una raccolta di pin

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) definisce le proprietà seguenti:

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), di tipo `IEnumerable`, che specifica la raccolta di elementi `IEnumerable` da visualizzare.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), che specifica il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) da applicare a ogni elemento nella raccolta di elementi visualizzati.
- `ItemTemplateSelector`, di tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector), che specifica il [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) che verrà usato per scegliere un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) per un elemento in fase di esecuzione.

> [!IMPORTANT]
> La proprietà [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) ha la precedenza quando vengono impostate entrambe le proprietà `ItemTemplate` e `ItemTemplateSelector`.

Un [`Map`](xref:Xamarin.Forms.Maps.Map) può essere popolato con i pin utilizzando data binding per associare la relativa proprietà [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) a una raccolta `IEnumerable`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
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

I dati della proprietà [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) vengono associati alla proprietà `Locations` dell'elemento ViewModel connesso, che restituisce un `ObservableCollection` di `Location` oggetti, che è un tipo personalizzato. Ogni oggetto `Location` definisce proprietà `Address` e `Description`, di tipo `string` e una proprietà `Position`, di tipo [`Position`](xref:Xamarin.Forms.Maps.Position).

L'aspetto di ogni elemento nella raccolta di `IEnumerable` viene definito impostando la proprietà [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) contenente un oggetto [`Pin`](xref:Xamarin.Forms.Maps.Pin) a cui i dati vengono associati alle proprietà appropriate.

Gli screenshot seguenti mostrano un [`Map`](xref:Xamarin.Forms.Maps.Map) la visualizzazione di una raccolta di [`Pin`](xref:Xamarin.Forms.Maps.Pin) tramite Data Binding:

[![Screenshot della mappa con i pin associati ai dati, in iOS e Android](pins-images/pins-itemsource.png "Mappa con pin associati a dati")](pins-images/pins-itemsource-large.png#lightbox "Mappa con pin associati a dati")

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

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Renderer personalizzato mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
- [Creazione di un DataTemplateSelector Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
