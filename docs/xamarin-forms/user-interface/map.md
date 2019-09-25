---
title: Mappa di xamarin. Forms
description: Questo articolo illustra come usare la classe Map di xamarin. Forms per usare la mappa API native in ogni piattaforma per fornire che già esegue il mapping di esperienza degli utenti.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 242673efb38931eb678432a28f24db0ad9b8cb7d
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "70228226"
---
# <a name="xamarinforms-map"></a>Mappa di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

_Xamarin. Forms Usa la mappa nativa le API in ciascuna piattaforma._

Verifica Usa la mappa nativa le API in ciascuna piattaforma. Ciò offre un'esperienza di mapping veloce e familiare per gli utenti, ma significa che alcuni passaggi di configurazione sono necessari per rispettare le esigenze dell'API per ogni piattaforma.
Una volta configurato, il `Map` controllo funziona esattamente come qualsiasi altro elemento di xamarin. Forms nel codice comune.

Il controllo mappa è stata invece utilizzato la [MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps) esempio mostrata di seguito.

 [![Mappe nell'esempio mobileCRM](map-images/maps-zoom-sml.png "Esempio di controllo mappa")](map-images/maps-zoom.png#lightbox "Esempio di controllo mappa")

La funzionalità mappa può essere migliorata ulteriormente mediante la creazione di un [eseguire il mapping di renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>Inizializzazione mappa

Quando si aggiunge mappe a un'applicazione xamarin. Forms **verifica** è un pacchetto NuGet separato che è necessario aggiungere a ogni progetto nella soluzione.
In Android, anche questo presenta una dipendenza su GooglePlayServices (un altro NuGet) che viene scaricata automaticamente quando si aggiunge una verifica.

Dopo aver installato il pacchetto NuGet, è necessario un codice di inizializzazione in ogni progetto di applicazione *dopo aver* il `Xamarin.Forms.Forms.Init` chiamata al metodo. Per iOS usare il codice seguente:

```csharp
Xamarin.FormsMaps.Init();
```

In Android è necessario passare gli stessi parametri `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Per la Universal Windows Platform (UWP) usare il codice seguente:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Aggiungere questa chiamata nei file seguenti per ogni piattaforma:

- **iOS** -AppDelegate.cs nel file, il `FinishedLaunching` (metodo).
- **Android** -MainActivity.cs nel file, il `OnCreate` (metodo).
- **Piattaforma UWP** -nel file MainPage.xaml.cs di `MainPage` costruttore.

Una volta aggiunto il pacchetto NuGet e il metodo di inizializzazione chiamato all'interno di `Xamarin.Forms.Maps` ogni applicazione, le API possono essere usate nel progetto di libreria .NET standard comune o nel codice del progetto condiviso.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configurazione della piattaforma

In alcune piattaforme sono necessari passaggi di configurazione aggiuntiva prima che la mappa verrà visualizzata.

### <a name="ios"></a>iOS

Per accedere ai servizi di posizione su iOS, è necessario impostare le chiavi seguenti in **Info. plist**:

- iOS 11
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) -per l'uso di servizi di posizione quando l'app è in uso
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) -per l'uso di servizi location in qualsiasi momento
- iOS 10 e versioni precedenti
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) -per l'uso di servizi di posizione quando l'app è in uso
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) -per l'uso di servizi location in qualsiasi momento    

Per supportare iOS 11 e versioni precedenti, è possibile includere tutte le tre chiavi: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription`, e `NSLocationAlwaysUsageDescription`.

La rappresentazione XML per queste chiavi nel **Info. plist** è illustrato di seguito. È necessario aggiornare il `string` valori in modo da riflettere come l'applicazione usa le informazioni sul percorso:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

Il **Info. plist** è inoltre possibile aggiungere le voci nella **origine** visualizzazione mentre si modifica il **Info. plist** file:

![File Info. plist per iOS 8](map-images/ios8-map-permissions.png "voci Info. plist necessarie iOS 8")

### <a name="android"></a>Android

Usare la [API Google Maps v2](https://developers.google.com/maps/documentation/android/) in Android è necessario generare una chiave API e aggiungerlo al progetto Android.
Seguire le istruzioni nel documento Xamarin nella [come ottenere una chiave di Google Maps API v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Dopo aver seguito le istruzioni, incollare la chiave API nel **Properties/AndroidManifest.xml** file (HTML e trova o aggiornare l'elemento seguente):

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

Senza una chiave API valida, il controllo Maps viene visualizzato come una casella grigia in Android.

> [!NOTE]
> Si noti che, affinché il pacchetto APK l'accesso a Google Maps, è necessario includere le impronte digitali SHA-1 e creare un pacchetto nomi per ogni archivio chiavi (debug e rilascio) che usano per firmare l'APK. Ad esempio, se si usa un computer per il debug e un altro computer per la generazione del file APK di rilascio, è necessario includere l'impronta digitale certificato SHA-1 dall'archivio di chiavi di debug del computer prima e l'impronta digitale certificato SHA-1 dalla versione dell'archivio chiavi di il secondo computer. Ricordare anche di modificare le credenziali della chiave, se l'app **nome del pacchetto** le modifiche. Visualizzare [come ottenere una chiave di Google Maps API v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Sarà anche necessario abilitare le autorizzazioni appropriate facendo clic sul progetto Android e selezionando **Opzioni > compilazione > applicazione Android** e perfezione quanto segue:

- `AccessCoarseLocation`
- `AccessFineLocation`
- `AccessLocationExtraCommands`
- `AccessMockLocation`
- `AccessNetworkState`
- `AccessWifiState`
- `Internet`

Alcuni di questi vengono visualizzati nella schermata seguente:

![Autorizzazioni necessarie per Android](map-images/android-map-permissions.png "autorizzazioni necessarie per Android")

Gli ultimi due sono necessari perché le applicazioni richiedono una connessione di rete per scaricare i dati della mappa. Leggere informazioni su Android [autorizzazioni](https://developer.android.com/reference/android/Manifest.permission.html) per altre informazioni.

Android 9 ha inoltre rimosso la libreria client Apache HTTP dal bootclasspath, quindi non è disponibile per le applicazioni destinate all'API 28 o versioni successive. È necessario aggiungere la riga seguente al `application` nodo del file **file AndroidManifest. XML** per continuare a usare il client HTTP Apache nelle applicazioni destinate all'API 28 o versioni successive:

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Per usare le mappe in Universal Windows Platform è necessario generare un token di autorizzazione. Per altre informazioni, vedere [richiedere una chiave di autenticazione mappe](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) su MSDN.

Il token di autenticazione deve quindi essere specificato nel `FormsMaps.Init("AUTHORIZATION_TOKEN")` chiamata al metodo, per autenticare l'app con Bing Maps.

<a name="Using_Maps" />

## <a name="map-configuration"></a>Configurazione della mappa

Vedere le [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) nell'esempio MobileCRM per un esempio di come è possibile utilizzare il controllo mappa nel codice. Un semplice `MapPage` classe potrebbe essere simile a questo - si noti che un nuovo `MapSpan` viene creato per posizionare la visualizzazione della mappa:

```csharp
public class MapPage : ContentPage {
    public MapPage() {
        var map = new Map(
            MapSpan.FromCenterAndRadius(
                    new Position(37,-122), Distance.FromMiles(0.3))) {
                IsShowingUser = true,
                HeightRequest = 100,
                WidthRequest = 960,
                VerticalOptions = LayoutOptions.FillAndExpand
            };
        var stack = new StackLayout { Spacing = 0 };
        stack.Children.Add(map);
        Content = stack;
    }
}
```

### <a name="map-type"></a>Tipo di mappa

Contenuto della mappa può anche essere modificato impostando la `MapType` proprietà, per visualizzare una mappa strada regolare (l'impostazione predefinita), immagini satellitari o una combinazione di entrambi.

```csharp
map.MapType = MapType.Street;
```

Valido `MapType` i valori sono:

- `Hybrid`
- `Satellite`
- `Street` (predefinito)

### <a name="map-region-and-mapspan"></a>Area mappa e MapSpan

Come illustrato nel frammento di codice precedente, fornendo un `MapSpan` istanza a un costruttore della mappa viene impostata la visualizzazione iniziale (punto al centro e zoom a livello) della mappa quando viene caricato. Esistono due modi per creare un nuovo `MapSpan` istanza:

- **MapSpan.FromCenterAndRadius()** -un metodo statico per creare un intervallo da un `Position` e specificando un `Distance` .
- **New () MapSpan** -costruttore che utilizza un `Position` e i gradi di latitudine e longitudine da visualizzare.

Il `MoveToRegion` metodo nella classe map può essere utilizzato quindi per modificare il livello di posizione o zoom della mappa. Per modificare il livello di zoom della mappa senza alterare la posizione, creare una nuova `MapSpan` usando il percorso corrente dal `VisibleRegion.Center` proprietà del controllo mappa. Un `Slider` oggetto può essere utilizzato per controllare lo zoom della mappa come questo, tuttavia non è possibile aggiornare il valore del dispositivo di scorrimento direttamente nel controllo mappa:

```csharp
Slider slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) =>
{
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

[![Mappe con zoom](map-images/maps-zoom-sml.png "Zoom controllo mappa")](map-images/maps-zoom.png#lightbox "Zoom controllo mappa")

Inoltre, la [`Map`](xref:Xamarin.Forms.Maps.Map) classe dispone di una `MoveToLastRegionOnLayoutChange` proprietà di tipo `bool`, supportata da una proprietà associabile. Per impostazione predefinita, questa `true`proprietà è, che indica che l'area mappa visualizzata verrà spostata dall'area corrente all'area impostata in precedenza quando si verifica una modifica del layout, ad esempio la rotazione del dispositivo. Quando questa proprietà è impostata su `false`, l'area mappa visualizzata rimarrà centrata quando si verifica una modifica del layout. Nell'esempio seguente viene illustrata l'impostazione di questa proprietà:

```csharp
map.MoveToLastRegionOnLayoutChange = false;
```

### <a name="map-pins"></a>Pin mappa

Percorsi possono essere contrassegnati nella mappa insieme `Pin` oggetti.

```csharp
var position = new Position(37,-122); // Latitude, Longitude
var pin = new Pin {
            Type = PinType.Place,
            Position = position,
            Label = "custom pin",
            Address = "custom detail info"
        };
map.Pins.Add(pin);
```

`PinType`può essere impostato su uno dei valori seguenti, che può influire sul modo in cui viene eseguito il rendering del PIN (a seconda della piattaforma):

- Generico
- Sul posto
- SavedPin
- SearchResult

### <a name="map-clicks"></a>Clic mappa

`Map`definisce un `MapClicked` evento che viene generato quando si tocca la mappa. L' `MapClickedEventArgs` oggetto che accompagna l' `MapClicked` evento ha una singola proprietà denominata `Position`, di tipo `Position`. Quando viene generato l'evento, il valore della `Position` proprietà viene impostato sulla posizione della mappa che è stata toccata.

Nell'esempio di codice seguente viene illustrato un gestore eventi `MapClicked` per l'evento:

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

In questo esempio, il `OnMapClicked` gestore eventi restituisce la latitudine e la longitudine che rappresenta la posizione della mappa toccata.

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>Creare una mappa in XAML

È anche possibile creare mappe in XAML, come illustrato nell'esempio seguente:

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map x:Name="MyMap"
                  Clicked="OnMapClicked"
                  WidthRequest="320"
                  HeightRequest="200"                  
                  IsShowingUser="true"
                  MapType="Hybrid" />
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> Per fare `xmlns` riferimento ai controlli Novell. Forms. Maps è necessaria una definizione dello spazio dei nomi aggiuntiva.

E possono essere impostati nel codice utilizzando `Map`il riferimento denominato per: `Pins` `MapRegion`

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="populate-a-map-with-data-using-data-binding"></a>Popolare una mappa con i dati usando data binding

La [`Map`](xref:Xamarin.Forms.Maps.Map) classe espone inoltre le proprietà seguenti:

- `ItemsSource`: specifica la raccolta di `IEnumerable` elementi da visualizzare.
- `ItemTemplate`: specifica l' [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto da applicare a ogni elemento nella raccolta di elementi visualizzati.
- `ItemTemplateSelector`: specifica l' [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) oggetto che verrà usato per scegliere un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto per un elemento in fase di esecuzione.

> [!NOTE]
> La `ItemTemplate` proprietà ha la precedenza quando vengono `ItemTemplate` impostate `ItemTemplateSelector` entrambe le proprietà e.

Un [`Map`](xref:Xamarin.Forms.Maps.Map) oggetto può essere popolato con i dati usando data binding per `ItemsSource` associare la relativa `IEnumerable` proprietà a una raccolta:

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

I `ItemsSource` dati della proprietà vengono associati `Locations` alla proprietà del modello di visualizzazione connesso, che restituisce un `ObservableCollection` oggetto `Location` di oggetti, che è un tipo personalizzato. Ogni `Location` oggetto definisce `Address` le `Description` proprietà e, di `string`tipo e una `Position` proprietà di tipo [`Position`](xref:Xamarin.Forms.Maps.Position).

L' `IEnumerable` aspetto di ogni elemento della raccolta viene definito impostando la `ItemTemplate` proprietà su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetto che contiene un [`Pin`](xref:Xamarin.Forms.Maps.Pin) oggetto che i dati vengono associati alle proprietà appropriate.

Gli screenshot seguenti mostrano una [`Map`](xref:Xamarin.Forms.Maps.Map) visualizzazione di una [`Pin`](xref:Xamarin.Forms.Maps.Pin) raccolta usando Data Binding:

[![Screenshot della mappa con i pin associati ai dati, in iOS e Android](map-images/pins-itemssource.png "Mappa con pin associati a dati")](map-images/pins-itemssource-large.png#lightbox "Mappa con pin associati a dati")

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

La `MapItemTemplateSelector` classe definisce `DefaultTemplate` le `XamarinTemplate` proprietà [e`DataTemplate`](xref:Xamarin.Forms.DataTemplate) impostate su modelli di dati diversi. Il `OnSelectTemplate` metodo `Pin` restituisce, che Visualizza "Novell" come etichetta quando viene toccato un oggetto, quando l'elemento dispone di un indirizzo che contiene "San Francisco". `XamarinTemplate` Quando l'elemento non dispone di un indirizzo che contiene "San Francisco", `OnSelectTemplate` il metodo `DefaultTemplate`restituisce.

Per ulteriori informazioni sui selettori di modelli di dati, vedere [Creating a Novell. Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Collegamenti correlati

- [MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Eseguire il mapping di Renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Creazione di un DataTemplateSelector Novell. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
- [API Maps](xref:Xamarin.Forms.Maps)
