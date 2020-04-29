---
title: Pin mappa di Novell. Forms
description: Questo articolo illustra come creare pin in una mappa Novell. Forms.
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2019
ms.openlocfilehash: 3df78a7c8eaf12306ade182f134f8d294d203af5
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517591"
---
# <a name="xamarinforms-map-pins"></a>Pin mappa di Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Il controllo Novell. [`Map`](xref:Xamarin.Forms.Maps.Map) Forms consente di contrassegnare le [`Pin`](xref:Xamarin.Forms.Maps.Pin) posizioni con gli oggetti. Un `Pin` è un marcatore di mappa che apre una finestra informazioni quando viene toccato:

[![Screenshot di un PIN della mappa e della relativa finestra informazioni, in iOS e Android](pins-images/pin-and-information-window.png "Pin mappa con finestra informazioni")](pins-images/pin-and-information-window-large.png#lightbox "Pin mappa con finestra informazioni")

Quando un [`Pin`](xref:Xamarin.Forms.Maps.Pin) oggetto viene aggiunto alla [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) raccolta, viene eseguito il rendering del pin sulla mappa.

La [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe presenta le proprietà seguenti:

- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address), di tipo `string`, che in genere rappresenta l'indirizzo per il percorso del PIN. Tuttavia, può trattarsi di `string` qualsiasi contenuto, non solo di un indirizzo.
- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label), di tipo `string`, che in genere rappresenta il titolo del PIN.
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position), di tipo [`Position`](xref:Xamarin.Forms.Maps.Position), che rappresenta la latitudine e la longitudine del PIN.
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type), di tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType), che rappresenta il tipo di pin.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che `Pin` può essere la destinazione di associazioni dati. Per ulteriori informazioni sugli oggetti `Pin` Data Binding, vedere la pagina relativa alla [visualizzazione di una raccolta di pin](#display-a-pin-collection).

Inoltre, la [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe definisce `MarkerClicked` gli eventi `InfoWindowClicked` e. L' `MarkerClicked` evento viene generato quando viene toccato un PIN e l' `InfoWindowClicked` evento viene generato quando la finestra informazioni viene toccata. L' `PinClickedEventArgs` oggetto che accompagna entrambi gli eventi ha una singola `HideInfoWindow` proprietà di tipo `bool`.

## <a name="display-a-pin"></a>Visualizzare un pin

È [`Pin`](xref:Xamarin.Forms.Maps.Pin) possibile aggiungere un oggetto a [`Map`](xref:Xamarin.Forms.Maps.Map) un oggetto in XAML:

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

Questo codice XAML crea [`Map`](xref:Xamarin.Forms.Maps.Map) un oggetto che mostra l'area specificata dall' [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) oggetto. L' `MapSpan` oggetto viene centrato sulla latitudine e la Longitudine rappresentate da [`Position`](xref:Xamarin.Forms.Maps.Position) un oggetto, che estende 0,01 di latitudine e longitudine. Un [`Pin`](xref:Xamarin.Forms.Maps.Pin) oggetto viene aggiunto alla [`Map.Pins`](xref:Xamarin.Forms.Maps.Pin) raccolta e disegnato in `Map` corrispondenza della posizione specificata dalla relativa [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) proprietà. Per informazioni sullo struct [`Position`](xref:Xamarin.Forms.Maps.Position) , vedere [posizione e distanza della mappa](position-distance.md). Per informazioni sul passaggio di argomenti in XAML a oggetti che non dispongono di costruttori predefiniti, vedere [passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md).

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
> Se non si imposta [`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label) la proprietà, viene generata `ArgumentException` un'eccezione quando l' [`Pin`](xref:Xamarin.Forms.Maps.Pin) oggetto viene aggiunto a [`Map`](xref:Xamarin.Forms.Maps.Map)un oggetto.

Questo esempio di codice comporta il rendering di un singolo pin su una mappa:

[![Screenshot di un pin mappa, in iOS e Android](pins-images/pin-only.png "Pin mappa")](pins-images/pin-only-large.png#lightbox "Pin mappa")

## <a name="interact-with-a-pin"></a>Interagire con un pin

Per impostazione predefinita, quando [`Pin`](xref:Xamarin.Forms.Maps.Pin) si tocca la finestra di informazioni visualizzata:

[![Screenshot di un PIN della mappa e della relativa finestra informazioni, in iOS e Android](pins-images/pin-and-information-window.png "Pin mappa con finestra informazioni")](pins-images/pin-and-information-window-large.png#lightbox "Pin mappa con finestra informazioni")

Se si tocca altrove sulla mappa, la finestra informazioni verrà chiusa.

La [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe definisce un `MarkerClicked` evento, che viene generato quando viene `Pin` toccato un oggetto. Non è necessario gestire questo evento per visualizzare la finestra informazioni. Questo evento deve essere invece gestito quando è necessario ricevere una notifica che indica che è stato toccato un PIN specifico.

La [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe definisce anche un `InfoWindowClicked` evento che viene generato quando una finestra informazioni viene toccata. Questo evento deve essere gestito quando è necessario ricevere una notifica per informare che è stata toccata una finestra di informazioni specifica.

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

L' `PinClickedEventArgs` oggetto che accompagna entrambi gli eventi ha una singola `HideInfoWindow` proprietà di tipo `bool`. Quando questa proprietà è impostata su `true` all'interno di un gestore eventi, la finestra informazioni sarà nascosta.

## <a name="pin-types"></a>Tipi di pin

[`Pin`](xref:Xamarin.Forms.Maps.Pin)gli oggetti includono [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) una proprietà, di [`PinType`](xref:Xamarin.Forms.Maps.PinType)tipo, che rappresenta il tipo di pin. L'enumerazione `PinType` definisce i membri seguenti:

- `Generic`, rappresenta un pin generico.
- `Place`, rappresenta un PIN per una posizione.
- `SavedPin`, rappresenta un PIN per un percorso salvato.
- `SearchResult`, rappresenta un PIN per un risultato della ricerca.

Tuttavia, l' [`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type) impostazione della proprietà su [`PinType`](xref:Xamarin.Forms.Maps.PinType) qualsiasi membro non modifica l'aspetto del pin sottoposto a rendering. Al contrario, è necessario creare un renderer personalizzato per personalizzare l'aspetto del PIN. Per altre informazioni, vedere [personalizzazione di un PIN della mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md).

## <a name="display-a-pin-collection"></a>Visualizzare una raccolta di segnaposto

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe definisce le proprietà seguenti:

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), di tipo `IEnumerable`, che specifica la raccolta di `IEnumerable` elementi da visualizzare.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), che specifica l' [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto da applicare a ogni elemento nella raccolta di elementi visualizzati.
- `ItemTemplateSelector`, di tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector), che specifica l' [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) oggetto che verrà usato per scegliere un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto per un elemento in fase di esecuzione.

> [!IMPORTANT]
> La [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) proprietà ha la precedenza quando vengono `ItemTemplate` impostate `ItemTemplateSelector` entrambe le proprietà e.

Un [`Map`](xref:Xamarin.Forms.Maps.Map) oggetto può essere popolato con i pin usando data binding per [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) associare la relativa `IEnumerable` proprietà a una raccolta:

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

I [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) dati della proprietà vengono associati alla `Locations` proprietà dell'elemento ViewModel connesso, che restituisce un `ObservableCollection` oggetto `Location` di oggetti, che è un tipo personalizzato. Ogni `Location` oggetto definisce `Address` le `Description` proprietà e, di `string`tipo e una `Position` proprietà di tipo [`Position`](xref:Xamarin.Forms.Maps.Position).

L'aspetto `IEnumerable` di ogni elemento della raccolta viene definito impostando la [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) proprietà su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che contiene un [`Pin`](xref:Xamarin.Forms.Maps.Pin) oggetto che i dati vengono associati alle proprietà appropriate.

Gli screenshot seguenti mostrano una [`Map`](xref:Xamarin.Forms.Maps.Map) visualizzazione di una [`Pin`](xref:Xamarin.Forms.Maps.Pin) raccolta usando Data Binding:

[![Screenshot della mappa con i pin associati ai dati, in iOS e Android](pins-images/pins-itemsource.png "Mappa con pin associati a dati")](pins-images/pins-itemsource-large.png#lightbox "Mappa con pin associati a dati")

### <a name="choose-item-appearance-at-runtime"></a>Scegli aspetto elemento in fase di esecuzione

È possibile scegliere l'aspetto di ogni `IEnumerable` elemento della raccolta in fase di esecuzione, in base al valore dell'elemento, impostando la `ItemTemplateSelector` proprietà [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)su:

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
                    <!-- Change the property values, or the properties that are bound to. -->
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

Nell'esempio seguente viene illustrata la `MapItemTemplateSelector` classe:

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

La `MapItemTemplateSelector` classe definisce `DefaultTemplate` le `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) proprietà e impostate su modelli di dati diversi. Il `OnSelectTemplate` metodo restituisce `XamarinTemplate`, che Visualizza "Novell" come etichetta quando viene toccato un `Pin` oggetto, quando l'elemento dispone di un indirizzo che contiene "San Francisco". Quando l'elemento non dispone di un indirizzo che contiene "San Francisco", `OnSelectTemplate` il metodo restituisce `DefaultTemplate`.

> [!NOTE]
> Un caso di utilizzo per questa funzionalità è l'associazione di proprietà di [`Pin`](xref:Xamarin.Forms.Maps.Pin) oggetti sottoclass a proprietà diverse, in base `Pin` al sottotipo.

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Creating a Novell. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Link correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Renderer personalizzato mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
- [Creazione di un DataTemplateSelector Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
