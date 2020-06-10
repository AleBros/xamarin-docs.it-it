---
title: " Xamarin.Forms controllo mappa" Descrizione: "il controllo mappa è una visualizzazione multipiattaforma per la visualizzazione e l'annotazione delle mappe. Usa il controllo mappa nativo per ogni piattaforma, offrendo un'esperienza di mapping rapida e familiare per gli utenti ".
ms. prod: Novell MS. AssetID: 22C99029-0B16-43A6-BF58-26B48C4AED38 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/29/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-map-control"></a>Xamarin.Formscontrollo mappa

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Il [`Map`](xref:Xamarin.Forms.Maps.Map) controllo è una visualizzazione multipiattaforma per la visualizzazione e l'annotazione delle mappe. Usa il controllo mappa nativo per ogni piattaforma, offrendo un'esperienza di Maps rapida e familiare per gli utenti:

[![Screenshot del controllo mappa, in iOS e Android](map-images/map-default.png "Controllo mappa")](map-images/map-default-large.png#lightbox "Controllo mappa")

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe definisce le proprietà seguenti che controllano l'aspetto e il comportamento della mappa:

- [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser), di tipo `bool` , indica se la mappa mostra la posizione corrente dell'utente.
- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource), di tipo `IEnumerable` , che specifica la raccolta di `IEnumerable` elementi da visualizzare.
- [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate), di tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , che specifica l'oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) da applicare a ogni elemento nella raccolta di elementi visualizzati.
- `ItemTemplateSelector`, di tipo [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) , che specifica l'oggetto [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) che verrà usato per scegliere un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto per un elemento in fase di esecuzione.
- [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled), di tipo `bool` , determina se è consentito lo scorrimento della mappa.
- [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled), di tipo `bool` , determina se la mappa è consentita per lo zoom.
- `MapElements`, di tipo `IList<MapElement>` , rappresenta l'elenco di elementi sulla mappa, ad esempio poligoni e polilinee.
- [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType), di tipo [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) , indica lo stile di visualizzazione della mappa.
- `MoveToLastRegionOnLayoutChange`, di tipo `bool` , controlla se l'area della mappa visualizzata viene spostata dall'area corrente all'area impostata in precedenza quando si verifica una modifica del layout.
- [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins), di tipo `IList<Pin>` , rappresenta l'elenco di pin sulla mappa.
- [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion), di tipo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) , restituisce l'area attualmente visualizzata della mappa.

Queste proprietà, ad eccezione delle `MapElements` `Pins` proprietà, e `VisibleRegion` , sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di associazioni dati.

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe definisce anche un `MapClicked` evento che viene generato quando si tocca la mappa. L' `MapClickedEventArgs` oggetto che accompagna l'evento ha una singola proprietà denominata `Position` , di tipo [`Position`](xref:Xamarin.Forms.Maps.Position) . Quando viene generato l'evento, la `Position` proprietà viene impostata sulla posizione della mappa che è stata toccata. Per informazioni sullo [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vedere [posizione e distanza della mappa](position-distance.md).

Per informazioni sulle [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource) proprietà, [`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate) e `ItemTemplateSelector` , vedere [visualizzare una raccolta di pin](pins.md#display-a-pin-collection).

## <a name="display-a-map"></a>Visualizzare una mappa

[`Map`](xref:Xamarin.Forms.Maps.Map)È possibile visualizzare un oggetto aggiungendolo a un layout o a una pagina:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <maps:Map x:Name="map" />
</ContentPage>
```

> [!NOTE]
> `xmlns`Per fare riferimento a, è necessaria una definizione dello spazio dei nomi aggiuntiva Xamarin.Forms . Esegue il mapping di controlli. Nell'esempio precedente `Xamarin.Forms.Maps` viene fatto riferimento allo spazio dei nomi tramite la `maps` parola chiave.

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

In questo esempio viene chiamato il [`Map`](xref:Xamarin.Forms.Maps.Map) costruttore predefinito, che allinea la mappa a Roma:

[![Screenshot del controllo mappa con il percorso predefinito, in iOS e Android](map-images/map-default.png "Controllo mappa con percorso predefinito")](map-images/map-default-large.png#lightbox "Controllo mappa con percorso predefinito")

In alternativa, è [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) possibile passare un argomento a un [`Map`](xref:Xamarin.Forms.Maps.Map) costruttore per impostare il punto centrale e il livello di zoom della mappa quando viene caricato. Per altre informazioni, vedere [visualizzare un percorso specifico su una mappa](#display-a-specific-location-on-a-map).

## <a name="map-types"></a>Tipi di mappa

La [`Map.MapType`](xref:Xamarin.Forms.Maps.Map.MapType) proprietà può essere impostata su un [`MapType`](xref:Xamarin.Forms.Maps.MapType) membro di enumerazione per definire lo stile di visualizzazione della mappa. L'enumerazione `MapType` definisce i membri seguenti:

- `Street`Specifica che verrà visualizzata una mappa stradale.
- `Satellite`Specifica che verrà visualizzata una mappa contenente le immagini satellite.
- `Hybrid`Specifica che verrà visualizzata una mappa che combina i dati via e satellite.

Per impostazione predefinita, un oggetto visualizzerà [`Map`](xref:Xamarin.Forms.Maps.Map) una mappa stradale se la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) proprietà non è definita. In alternativa, la `MapType` proprietà può essere impostata su uno dei [`MapType`](xref:Xamarin.Forms.Maps.MapType) membri dell'enumerazione:

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

Gli screenshot seguenti mostrano un [`Map`](xref:Xamarin.Forms.Maps.Map) quando la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) proprietà è impostata su `Street` :

[![Screenshot del controllo mappa con il tipo di mappa stradale, in iOS e Android](map-images/maptype-street.png "Controllo mappa con la via MapType")](map-images/maptype-street-large.png#lightbox "Controllo mappa con il tipo di mappa stradale")

Gli screenshot seguenti mostrano un [`Map`](xref:Xamarin.Forms.Maps.Map) quando la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) proprietà è impostata su `Satellite` :

[![Screenshot del controllo mappa con il tipo di mappa satellite, in iOS e Android](map-images/maptype-satellite.png "Controllo mappa con il MapType satellite")](map-images/maptype-satellite-large.png#lightbox "Controllo mappa con il tipo di mappa satellite")

Gli screenshot seguenti mostrano un [`Map`](xref:Xamarin.Forms.Maps.Map) quando la [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) proprietà è impostata su `Hybrid` :

[![Screenshot del controllo mappa con il tipo di mappa ibrida, in iOS e Android](map-images/maptype-hybrid.png "Controllo mappa con MapType ibrido")](map-images/maptype-hybrid-large.png#lightbox "Controllo mappa con il tipo di mappa ibrida")

## <a name="display-a-specific-location-on-a-map"></a>Visualizzare una posizione specifica in una mappa

L'area di una mappa da visualizzare quando è caricata una mappa può essere impostata passando un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argomento al [`Map`](xref:Xamarin.Forms.Maps.Map) costruttore:

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

In questo esempio viene creato un [`Map`](xref:Xamarin.Forms.Maps.Map) oggetto che mostra l'area specificata dall' [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) oggetto. L' `MapSpan` oggetto viene centrato sulla latitudine e la Longitudine rappresentate da un [`Position`](xref:Xamarin.Forms.Maps.Position) oggetto e si estende a 0,01 latitudine e 0,01 di longitudine. Per informazioni sullo [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vedere [posizione e distanza della mappa](position-distance.md). Per informazioni sul passaggio di argomenti in XAML, vedere [passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md).

Il risultato è che, quando la mappa viene visualizzata, viene centrata in una posizione specifica e si estende su un numero specifico di gradi di latitudine e Longitudine:

[![Screenshot del controllo mappa con il percorso specificato, in iOS e Android](map-images/map-region.png "Controllo mappa con un percorso specificato")](map-images/map-region-large.png#lightbox "Controllo mappa con un percorso specificato")

## <a name="create-a-mapspan-object"></a>Creare un oggetto MapSpan

Sono disponibili diversi approcci per la creazione di [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) oggetti. Un approccio comune è fornire gli argomenti obbligatori al `MapSpan` costruttore. Si tratta di una latitudine e una Longitudine rappresentate da un [`Position`](xref:Xamarin.Forms.Maps.Position) oggetto e `double` i valori che rappresentano i gradi di latitudine e Longitudine estesi da `MapSpan` . Per informazioni sullo [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vedere [posizione e distanza della mappa](position-distance.md).

In alternativa, nella classe sono disponibili tre metodi [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) che restituiscono nuovi `MapSpan` oggetti:

1. [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude*)Restituisce un oggetto `MapSpan` con lo stesso valore `LongitudeDegrees` dell'istanza della classe del metodo e un raggio definito dagli `north` `south` argomenti e.
1. [`FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius*)Restituisce un oggetto `MapSpan` definito dagli [`Position`](xref:Xamarin.Forms.Maps.Position) [`Distance`](xref:Xamarin.Forms.Maps.Distance) argomenti e.
1. [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*)Restituisce un oggetto `MapSpan` con lo stesso centro dell'istanza della classe del metodo, ma con un raggio moltiplicato per il relativo `double` argomento.

Per informazioni sullo [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct, vedere [posizione e distanza della mappa](position-distance.md).

Una volta [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) creato, è possibile accedere alle proprietà seguenti per recuperare i dati:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center), che rappresenta l'oggetto [`Position`](xref:Xamarin.Forms.Maps.Position) nel centro geografico dell'oggetto `MapSpan` .
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees), che rappresenta i gradi di latitudine estesi da `MapSpan` .
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees), che rappresenta i gradi di longitudine estesi da `MapSpan` .
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius), che rappresenta il `MapSpan` raggio.

## <a name="move-the-map"></a>Spostare la mappa

Il [`Map.MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) metodo può essere chiamato per modificare la posizione e il livello di zoom di una mappa. Questo metodo accetta un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argomento che definisce l'area della mappa da visualizzare e il livello di zoom.

Nel codice riportato di seguito viene illustrato un esempio di trasferimento dell'area visualizzata su una mappa:

```csharp
MapSpan mapSpan = MapSpan.FromCenterAndRadius(position, Distance.FromKilometers(0.444));
map.MoveToRegion(mapSpan);
```

## <a name="zoom-the-map"></a>Zoom della mappa

Il livello di zoom di un oggetto [`Map`](xref:Xamarin.Forms.Maps.Map) può essere modificato senza modificarne la posizione. Questa operazione può essere eseguita usando l'interfaccia utente della mappa o a livello chiamando il [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) metodo con un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argomento che usa il percorso corrente come [`Position`](xref:Xamarin.Forms.Maps.Position) argomento:

```csharp
double zoomLevel = 0.5;
double latlongDegrees = 360 / (Math.Pow(2, zoomLevel));
if (map.VisibleRegion != null)
{
    map.MoveToRegion(new MapSpan(map.VisibleRegion.Center, latlongDegrees, latlongDegrees));
}
```

In questo esempio, il [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) metodo viene chiamato con un [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) argomento che specifica la posizione corrente della mappa, tramite la [`Map.VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) proprietà e il livello di zoom come gradi di latitudine e longitudine. Il risultato complessivo è che il livello di zoom della mappa è stato modificato, ma il suo percorso non è. Un approccio alternativo per implementare lo zoom su una mappa consiste nell'utilizzare il [`MapSpan.WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom*) metodo per controllare il fattore di zoom.

> [!IMPORTANT]
> Per eseguire lo zoom di una mappa, sia tramite l'interfaccia utente della mappa che a livello, è necessario che la [`Map.HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) proprietà sia `true` . Per altre informazioni su questa proprietà, vedere [disabilitare lo zoom](#disable-zoom).

## <a name="customize-map-behavior"></a>Personalizzare il comportamento della mappa

Il comportamento di un oggetto [`Map`](xref:Xamarin.Forms.Maps.Map) può essere personalizzato impostando alcune proprietà e gestendo l' `MapClicked` evento.

> [!NOTE]
> Per ottenere una personalizzazione aggiuntiva del comportamento della mappa, è possibile creare un renderer personalizzato della mappa. Per ulteriori informazioni, vedere [personalizzazione di una Xamarin.Forms mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md).

### <a name="disable-scroll"></a>Disabilita scorrimento

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe definisce una [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) proprietà di tipo `bool` . Per impostazione predefinita, questa proprietà è `true` , che indica che è consentito lo scorrimento della mappa. Quando questa proprietà è impostata su `false` , la mappa non scorrerà. Nell'esempio seguente viene illustrata l'impostazione di questa proprietà:

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe definisce una [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) proprietà di tipo `bool` . Per impostazione predefinita, questa proprietà è `true` , che indica che è possibile eseguire lo zoom sulla mappa. Quando questa proprietà è impostata su `false` , non è possibile eseguire lo zoom della mappa. Nell'esempio seguente viene illustrata l'impostazione di questa proprietà:

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe definisce una [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) proprietà di tipo `bool` . Per impostazione predefinita, questa proprietà è `false` , che indica che la mappa non Mostra la posizione corrente dell'utente. Quando questa proprietà è impostata su `true` , la mappa mostra la posizione corrente dell'utente. Nell'esempio seguente viene illustrata l'impostazione di questa proprietà:

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe definisce una `MoveToLastRegionOnLayoutChange` proprietà di tipo `bool` . Per impostazione predefinita, questa proprietà è `true` , che indica che l'area mappa visualizzata verrà spostata dall'area corrente all'area impostata in precedenza quando si verifica una modifica del layout, ad esempio la rotazione del dispositivo. Quando questa proprietà è impostata su `false` , l'area mappa visualizzata rimarrà centrata quando si verifica una modifica del layout. Nell'esempio seguente viene illustrata l'impostazione di questa proprietà:

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

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe definisce un `MapClicked` evento che viene generato quando si tocca la mappa. L' `MapClickedEventArgs` oggetto che accompagna l'evento ha una singola proprietà denominata `Position` , di tipo [`Position`](xref:Xamarin.Forms.Maps.Position) . Quando viene generato l'evento, la `Position` proprietà viene impostata sulla posizione della mappa che è stata toccata. Per informazioni sullo [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vedere [posizione e distanza della mappa](position-distance.md).

Nell'esempio di codice seguente viene illustrato un gestore eventi per l' `MapClicked` evento:

```csharp
void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

In questo esempio, il `OnMapClicked` gestore eventi restituisce la latitudine e la longitudine che rappresenta la posizione della mappa toccata. Il gestore eventi può essere registrato con l' `MapClicked` evento come indicato di seguito:

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
- [Personalizzazione di una Xamarin.Forms mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map-pin.md)
- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
