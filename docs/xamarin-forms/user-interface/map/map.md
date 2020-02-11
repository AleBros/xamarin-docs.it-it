---
title: Controllo mappa Xamarin.Forms
description: Il controllo mappa è una visualizzazione multipiattaforma per la visualizzazione e l'annotazione delle mappe. Usa il controllo mappa nativo per ogni piattaforma, offrendo un'esperienza di mapping rapida e familiare per gli utenti.
ms.prod: xamarin
ms.assetid: 22C99029-0B16-43A6-BF58-26B48C4AED38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 1cfda90360557af1160d421f18807f8b534967a8
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426230"
---
# <a name="xamarinforms-map-control"></a>Controllo mappa Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Il controllo [`Map`](xref:Xamarin.Forms.Maps.Map) è una visualizzazione multipiattaforma per la visualizzazione e l'annotazione delle mappe. Usa il controllo mappa nativo per ogni piattaforma, offrendo un'esperienza di Maps rapida e familiare per gli utenti:

[![Screenshot del controllo mappa, in iOS e Android](map-images/map-default.png "Controllo mappa")](map-images/map-default-large.png#lightbox "Controllo mappa")

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) definisce le proprietà seguenti che controllano l'aspetto e il comportamento della mappa:

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser), di tipo `bool`, indica se la mappa mostra la posizione corrente dell'utente.
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), di tipo `IEnumerable`, che specifica la raccolta di elementi `IEnumerable` da visualizzare.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), che specifica il [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) da applicare a ogni elemento nella raccolta di elementi visualizzati.
- `ItemTemplateSelector`, di tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector), che specifica il [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) che verrà usato per scegliere un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) per un elemento in fase di esecuzione.
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled), di tipo `bool`, determina se è consentito lo scorrimento della mappa.
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled), di tipo `bool`, determina se la mappa è consentita per lo zoom.
- `MapElements`, di tipo `IList<MapElement>`, rappresenta l'elenco di elementi sulla mappa, ad esempio poligoni e polilinee.
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), di tipo [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), indica lo stile di visualizzazione della mappa.
- `MoveToLastRegionOnLayoutChange`, di tipo `bool`, controlla se l'area mappa visualizzata verrà spostata dall'area corrente all'area impostata in precedenza quando si verifica una modifica del layout.
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins), di tipo `IList<Pin>`, rappresenta l'elenco di pin sulla mappa.
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion), di tipo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan), restituisce l'area attualmente visualizzata della mappa.

Queste proprietà, fatta eccezione per le proprietà `MapElements`, `Pins`e `VisibleRegion`, sono supportate da oggetti di [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che possono essere destinazioni di associazioni dati.

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) definisce anche un evento `MapClicked` generato quando viene toccata la mappa. Il `MapClickedEventArgs` oggetto che accompagna l'evento ha una singola proprietà denominata `Position`, di tipo [`Position`](xref:Xamarin.Forms.Maps.Position). Quando viene generato l'evento, la proprietà `Position` viene impostata sul percorso della mappa che è stato toccato. Per informazioni sul [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vedere [posizione e distanza della mappa](position-distance.md).

Per informazioni sulle proprietà [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)e `ItemTemplateSelector`, vedere [visualizzare una raccolta di pin](pins.md#display-a-pin-collection).

## <a name="display-a-map"></a>Visualizzare una mappa

Un [`Map`](xref:Xamarin.Forms.Maps.Map) può essere visualizzato aggiungendolo a un layout o a una pagina:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> Per fare riferimento ai controlli Xamarin.Forms. Maps è necessaria una definizione di spazio dei nomi `xmlns` aggiuntiva. Nell'esempio precedente viene fatto riferimento allo spazio dei nomi `Xamarin.Forms.Maps` tramite la parola chiave `maps`.

Il codice C# equivalente è il seguente:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Maps;

namespace WorkingWithMaps
{
    public class MapTypesPageCode : ContentPage
    {
        public MapTypesPageCode()
        {
            Map map = new Map();
            Content = map;
        }
    }
}
```

In questo esempio viene chiamato il costruttore predefinito [`Map`](xref:Xamarin.Forms.Maps.Map) , che centra la mappa a Roma:

[![Screenshot del controllo mappa con il percorso predefinito, in iOS e Android](map-images/map-default.png "Controllo mappa con percorso predefinito")](map-images/map-default-large.png#lightbox "Controllo mappa con percorso predefinito")

In alternativa, è possibile passare un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argomento a un costruttore [`Map`](xref:Xamarin.Forms.Maps.Map) per impostare il punto centrale e il livello di zoom della mappa quando viene caricato. Per altre informazioni, vedere [visualizzare un percorso specifico su una mappa](#display-a-specific-location-on-a-map).

## <a name="map-types"></a>Tipi di mappa

La proprietà [`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType) può essere impostata su un membro dell'enumerazione [`MapType`](xref:Xamarin.Forms.Maps.MapType) per definire lo stile di visualizzazione della mappa. L'enumerazione `MapType` definisce i membri seguenti:

- `Street` specifica che verrà visualizzata una mappa stradale.
- `Satellite` specifica che verrà visualizzata una mappa contenente immagini satellite.
- `Hybrid` specifica che verrà visualizzata una mappa che combina la strada e i dati satellite.

Per impostazione predefinita, un [`Map`](xref:Xamarin.Forms.Maps.Map) visualizzerà una mappa stradale se la proprietà [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) non è definita. In alternativa, è possibile impostare la proprietà `MapType` su uno dei membri dell'enumerazione [`MapType`](xref:Xamarin.Forms.Maps.MapType) :

```xaml
<maps:Map MapType="Satellite" />
```

Il codice C# equivalente è il seguente:

```csharp
Map map = new Map
{
    MapType = MapType.Satellite
};
```

Gli screenshot seguenti mostrano un [`Map`](xref:Xamarin.Forms.Maps.Map) quando la proprietà [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) è impostata su `Street`:

[![Screenshot del controllo mappa con il tipo di mappa stradale, in iOS e Android](map-images/maptype-street.png "Controllo mappa con la via MapType")](map-images/maptype-street-large.png#lightbox "Map control with the street map type")

Gli screenshot seguenti mostrano un [`Map`](xref:Xamarin.Forms.Maps.Map) quando la proprietà [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) è impostata su `Satellite`:

[![Screenshot del controllo mappa con il tipo di mappa satellite, in iOS e Android](map-images/maptype-satellite.png "Controllo mappa con il MapType satellite")](map-images/maptype-satellite-large.png#lightbox "Map control with the satellite map type")

Gli screenshot seguenti mostrano un [`Map`](xref:Xamarin.Forms.Maps.Map) quando la proprietà [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) è impostata su `Hybrid`:

[![Screenshot del controllo mappa con il tipo di mappa ibrida, in iOS e Android](map-images/maptype-hybrid.png "Controllo mappa con MapType ibrido")](map-images/maptype-hybrid-large.png#lightbox "Map control with the hybrid map type")

## <a name="display-a-specific-location-on-a-map"></a>Visualizzare una posizione specifica in una mappa

L'area di una mappa da visualizzare quando è caricata una mappa può essere impostata passando un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argomento al costruttore [`Map`](xref:Xamarin.Forms.Maps.Map) :

```xaml
<maps:Map>
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
</maps:Map>
```

Il codice C# equivalente è il seguente:

```csharp
Position position = new Position(36.9628066, -122.0194722);
MapSpan mapSpan = new MapSpan(position, 0.01, 0.01);
Map map = new Map(mapSpan);
```

In questo esempio viene creato un oggetto [`Map`](xref:Xamarin.Forms.Maps.Map) che mostra l'area specificata dall'oggetto [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) . L'oggetto `MapSpan` viene centrato sulla latitudine e la Longitudine rappresentate da un oggetto [`Position`](xref:Xamarin.Forms.Maps.Position) e si estende a 0,01 latitudine e 0,01 gradi di longitudine. Per informazioni sul [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vedere [posizione e distanza della mappa](position-distance.md). Per informazioni sul passaggio di argomenti in XAML, vedere [passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md).

Il risultato è che, quando la mappa viene visualizzata, viene centrata in una posizione specifica e si estende su un numero specifico di gradi di latitudine e Longitudine:

[![Screenshot del controllo mappa con il percorso specificato, in iOS e Android](map-images/map-region.png "Controllo mappa con un percorso specificato")](map-images/map-region-large.png#lightbox "Controllo mappa con un percorso specificato")

## <a name="create-a-mapspan-object"></a>Creare un oggetto MapSpan

Sono disponibili diversi approcci per la creazione di oggetti [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) . Un approccio comune è fornire gli argomenti obbligatori al costruttore `MapSpan`. Si tratta di una latitudine e una Longitudine rappresentate da un oggetto [`Position`](xref:Xamarin.Forms.Maps.Position) e `double` valori che rappresentano i gradi di latitudine e Longitudine estesi dal `MapSpan`. Per informazioni sul [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vedere [posizione e distanza della mappa](position-distance.md).

In alternativa, esistono tre metodi nella classe [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) che restituiscono nuovi oggetti `MapSpan`:

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*) restituisce una `MapSpan` con lo stesso `LongitudeDegrees` dell'istanza della classe del metodo e un raggio definito dal relativo `north` e `south` argomenti.
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*) restituisce un `MapSpan` definito dagli argomenti [`Position`](xref:Xamarin.Forms.Maps.Position) e [`Distance`](xref:Xamarin.Forms.Maps.Distance) .
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) restituisce una `MapSpan` con lo stesso centro dell'istanza della classe del metodo, ma con un raggio moltiplicato per il relativo argomento `double`.

Per informazioni sul [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct, vedere [posizione e distanza della mappa](position-distance.md).

Una volta creato un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) , è possibile accedere alle proprietà seguenti per recuperare i dati:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center), che rappresenta il [`Position`](xref:Xamarin.Forms.Maps.Position) nel centro geografico della `MapSpan`.
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees), che rappresenta i gradi di latitudine estesi dall'`MapSpan`.
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees), che rappresenta i gradi di longitudine estesi dall'`MapSpan`.
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius), che rappresenta il raggio di `MapSpan`.

## <a name="move-the-map"></a>Spostare la mappa

È possibile chiamare il metodo [`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) per modificare la posizione e il livello di zoom di una mappa. Questo metodo accetta un argomento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) che definisce l'area della mappa da visualizzare e il relativo livello di zoom.

Nel codice riportato di seguito viene illustrato un esempio di trasferimento dell'area visualizzata su una mappa:

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>Zoom della mappa

Il livello di zoom di un [`Map`](xref:Xamarin.Forms.Maps.Map) può essere modificato senza modificarne la posizione. Questa operazione può essere eseguita usando l'interfaccia utente della mappa o a livello chiamando il metodo [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) con un argomento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) che usa il percorso corrente come argomento [`Position`](xref:Xamarin.Forms.Maps.Position) :

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

In questo esempio, il metodo [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) viene chiamato con un argomento [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) che specifica la posizione corrente della mappa, tramite la proprietà [`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) e il livello di zoom come gradi di latitudine e longitudine. Il risultato complessivo è che il livello di zoom della mappa è stato modificato, ma il suo percorso non è. Un approccio alternativo per implementare lo zoom su una mappa consiste nell'usare il metodo [`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) per controllare il fattore di zoom.

> [!IMPORTANT]
> Per eseguire lo zoom di una mappa, sia tramite l'interfaccia utente della mappa che a livello, è necessario che la proprietà [`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) sia `true`. Per altre informazioni su questa proprietà, vedere [disabilitare lo zoom](#disable-zoom).

## <a name="customize-map-behavior"></a>Personalizzare il comportamento della mappa

Il comportamento di un [`Map`](xref:Xamarin.Forms.Maps.Map) può essere personalizzato impostando alcune proprietà e gestendo l'evento `MapClicked`.

> [!NOTE]
> È possibile ottenere un ulteriore comportamento della mappa customizatin creando un renderer personalizzato della mappa. Per altre informazioni, vedere [personalizzazione di un mapping di Xamarin.Forms](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

### <a name="disable-scroll"></a>Disabilita scorrimento

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) definisce una proprietà [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) di tipo `bool`. Per impostazione predefinita, questa proprietà è `true`, che indica che è consentito lo scorrimento della mappa. Quando questa proprietà è impostata su `false`, la mappa non scorrerà. Nell'esempio seguente viene illustrata l'impostazione di questa proprietà:

```xaml
<maps:Map HasScrollEnabled="false" />
```

Il codice C# equivalente è il seguente:

```csharp
Map map = new Map
{
    HasScrollEnabled = false
};
```

### <a name="disable-zoom"></a>Disabilita zoom

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) definisce una proprietà [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) di tipo `bool`. Per impostazione predefinita, questa proprietà è `true`, che indica che è possibile eseguire lo zoom sulla mappa. Quando questa proprietà è impostata su `false`, non è possibile ingrandire la mappa. Nell'esempio seguente viene illustrata l'impostazione di questa proprietà:

```xaml
<maps:Map HasZoomEnabled="false" />
```

Il codice C# equivalente è il seguente:

```csharp
Map map = new Map
{
    HasZoomEnabled = false
};
```

### <a name="show-the-users-location"></a>Mostra la località dell'utente

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) definisce una proprietà [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) di tipo `bool`. Per impostazione predefinita, questa proprietà è `false`, a indicare che la mappa non Mostra la posizione corrente dell'utente. Quando questa proprietà è impostata su `true`, la mappa mostra la posizione corrente dell'utente. Nell'esempio seguente viene illustrata l'impostazione di questa proprietà:

```xaml
<maps:Map IsShowingUser="true" />
```

Il codice C# equivalente è il seguente:

```csharp
Map map = new Map
{
    IsShowingUser = true
};
```

> [!IMPORTANT]
> In iOS, Android e il piattaforma UWP (Universal Windows Platform) l'accesso alla posizione dell'utente richiede l'autorizzazione per la posizione per l'applicazione. Per ulteriori informazioni, vedere [configurazione della piattaforma](setup.md#platform-configuration).

### <a name="maintain-map-region-on-layout-change"></a>Mantieni area mappa durante la modifica del layout

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) definisce una proprietà `MoveToLastRegionOnLayoutChange` di tipo `bool`. Per impostazione predefinita, questa proprietà è `true`, che indica che l'area mappa visualizzata passerà dall'area corrente all'area impostata in precedenza quando si verifica una modifica del layout, ad esempio la rotazione del dispositivo. Quando questa proprietà è impostata su `false`, l'area mappa visualizzata rimarrà centrata quando si verifica una modifica del layout. Nell'esempio seguente viene illustrata l'impostazione di questa proprietà:

```xaml
<maps:Map MoveToLastRegionOnLayoutChange="false" />
```

Il codice C# equivalente è il seguente:

```csharp
Map map = new Map
{
    MoveToLastRegionOnLayoutChange = false
};
```

### <a name="map-clicks"></a>Clic mappa

La classe [`Map`](xref:Xamarin.Forms.Maps.Map) definisce un evento `MapClicked` generato quando viene toccata la mappa. Il `MapClickedEventArgs` oggetto che accompagna l'evento ha una singola proprietà denominata `Position`, di tipo [`Position`](xref:Xamarin.Forms.Maps.Position). Quando viene generato l'evento, la proprietà `Position` viene impostata sul percorso della mappa che è stato toccato. Per informazioni sul [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vedere [posizione e distanza della mappa](position-distance.md).

Nell'esempio di codice riportato di seguito viene illustrato un gestore eventi per l'evento `MapClicked`:

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

In questo esempio, il gestore dell'evento `OnMapClicked` restituisce la latitudine e la longitudine che rappresenta la posizione della mappa toccata. Il gestore eventi può essere registrato con l'evento `MapClicked` come indicato di seguito:

```xaml
<maps:Map MapClicked="OnMapClicked" />
```

Il codice C# equivalente è il seguente:

```csharp
Map map = new Map();
map.MapClicked += OnMapClicked;
```

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Posizione e distanza della mappa](position-distance.md)
- [Personalizzazione di un mapping di Xamarin.Forms](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
