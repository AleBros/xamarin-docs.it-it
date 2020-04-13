---
title: Riassunto del capitolo 28. Posizione e mappe
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 28. Posizione e mappe'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 5dcd84536cc6d80deb753fc6fe57f9090f6b2dad
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "72697082"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Riassunto del capitolo 28. Posizione e mappe

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui Xamarin.Forms si è discostata dal materiale presentato nel libro.

Xamarin.Forms supporta [`Map`](xref:Xamarin.Forms.Maps.Map) un elemento che `View`deriva da . A causa degli speciali requisiti della piattaforma coinvolti nell'utilizzo delle mappe, vengono implementati in un `Xamarin.Forms.Maps`assembly separato, **Xamarin.Forms.Maps**, e implicano uno spazio dei nomi diverso: .

## <a name="the-geographic-coordinate-system"></a>Il sistema di coordinate geografiche

Un sistema di coordinate geografiche identifica le posizioni su un oggetto sferico (o quasi sferico) come la Terra. Una coordinata è costituita da una *latitudine* e *longitudine* espresse in angoli.

Un grande cerchio `equator` chiamato è a metà strada tra i due poli attraverso i quali si estende concettualmente l'asse terrestre.

### <a name="parallels-and-latitude"></a>Paralleli e latitudine

Un angolo misurato a nord o a sud dell'equatore dal centro della Terra indica linee di uguale latitudine note come *paralleli.* Questi vanno da 0 gradi all'equatore a 90 gradi ai poli nord e sud. Per convenzione, le latitudini a nord dell'equatore sono valori positivi e quelli a sud dell'equatore sono valori negativi.

### <a name="longitude-and-meridians"></a>Longitudine e meridiani

Le metà di grandi cerchi dal polo nord al polo sud sono linee di uguale longitudine, note anche come *meridiani*. Questi sono relativi al Prime Meridian di Greenwich, Inghilterra. Per convenzione, le longinedini ad est del Primo Meridiano sono valori positivi da 0 gradi a 180 gradi, e le longinedini ad ovest del Prime Meridian sono valori negativi da 0 gradi a &ndash;180 gradi.

### <a name="the-equirectangular-projection"></a>La proiezione equirettangolare

Qualsiasi mappa piatta della Terra introduce distorsioni. Se tutte le linee di latitudine e longitudine sono dritte, e se le differenze uguali negli angoli di latitudine e longitudine corrispondono a distanze uguali sulla mappa, il risultato è una *proiezione equirettangolare*. Questa mappa distorce le aree più vicine ai poli perché sono allungate orizzontalmente.

### <a name="the-mercator-projection"></a>La proiezione Mercatore

La popolare *proiezione Mercatore* tenta di compensare l'allungamento orizzontale estendendo anche queste aree verticalmente. Questo si traduce in una mappa in cui le aree vicino ai poli appaiono molto più grandi di quelle reali, ma qualsiasi area locale si conforma abbastanza strettamente con l'area effettiva.

### <a name="map-services-and-tiles"></a>Servizi e riquadri delle mappe

I servizi di mappe utilizzano `Web Mercator`una variante della proiezione Mercatore chiamata . I servizi mappa forniscono riquadri bitmap a un client in base alla posizione e al livello di zoom.

## <a name="getting-the-users-location"></a>Ottenere la posizione dell'utente

Le classi Xamarin.Forms `Map` non includono una funzionalità per ottenere la posizione geografica dell'utente, ma questo è spesso auspicabile quando si lavora con le mappe, pertanto un servizio di dipendenza deve gestirlo.

> [!NOTE]
> Le applicazioni Xamarin.Forms [`Geolocation`](~/essentials/geolocation.md) possono invece utilizzare la classe inclusa in Xamarin.Essentials.

### <a name="the-location-tracker-api"></a>L'API di rilevamento della posizione

La soluzione [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) contiene il codice per un'API di rilevamento della posizione. La [`GeographicLocation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) struttura racchiude una latitudine e longitudine. L'interfaccia [`ILocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) definisce due metodi per avviare e sospendere il rilevamento della posizione e un evento quando è disponibile una nuova posizione.

#### <a name="the-ios-location-manager"></a>Il gestore di posizione iOS

L'implementazione iOS di `ILocationTracker` è una [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) classe [`CLLocationManager`](xref:CoreLocation.CLLocationManager)che utilizza iOS .

#### <a name="the-android-location-manager"></a>Il gestore di posizione Android

L'implementazione `ILocationTracker` di [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) Android di è [`LocationManager`](xref:Android.Locations.LocationManager) una classe che utilizza la classe Android.

#### <a name="the-uwp-geo-locator"></a>Il localizzatore geografico UWP

L'implementazione della `ILocationTracker` piattaforma [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) Windows universale di è [`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator)una classe che utilizza la piattaforma UWP .

### <a name="display-the-phones-location"></a>Visualizzare la posizione del telefono

L'esempio [**WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) utilizza il localizzatore di posizione per visualizzare la posizione del telefono, sia nel testo che su una mappa equirettangolare.

### <a name="the-required-overhead"></a>L'overhead richiesto

Alcuni overhead è necessario per **WhereAmI** per utilizzare il localizzatore di posizione. In primo luogo, tutti i progetti nella soluzione **WhereAmI** devono avere riferimenti ai progetti corrispondenti in **Xamarin.FormsBook.Platform**e ogni progetto **WhereAmI** deve chiamare il `Toolkit.Init` metodo .

È necessario un sovraccarico aggiuntivo specifico della piattaforma, sotto forma di autorizzazioni di posizione.

#### <a name="location-permission-for-ios"></a>Autorizzazione alla posizione per iOSLocation permission for iOS

Per iOS, il file **info.plist** deve includere elementi contenenti il testo di una domanda che chiede all'utente di consentire l'ottenimento della posizione dell'utente.

#### <a name="location-permissions-for-android"></a>Autorizzazioni di posizione per Android

Le applicazioni Android che ottengono la posizione dell'utente devono disporre di un'autorizzazione ACCESS_FILE_LOCATION nel file AndroidManifest.xml.Android applications that obtain the user's location must have an ACCESS_FILE_LOCATION permission in the AndroidManifest.xml file.

#### <a name="location-permissions-for-the-uwp"></a>Autorizzazioni per la posizione per la piattaforma UWP

Un'applicazione piattaforma Windows `location` universale deve avere una funzionalità del dispositivo contrassegnata nel file Package.appxmanifest.A Universal Windows Platform application must have a device capability marked in the Package.appxmanifest file.

## <a name="working-with-xamarinformsmaps"></a>Utilizzo di Xamarin.Forms.Maps

Diversi requisiti sono coinvolti `Map` nell'utilizzo della classe.

### <a name="the-nuget-package"></a>Il pacchetto NuGet

La libreria **Xamarin.Forms.Maps** NuGet deve essere aggiunta alla soluzione dell'applicazione. Il numero di versione deve essere lo stesso del pacchetto **Xamarin.Forms** attualmente installato.

### <a name="initializing-the-maps-package"></a>Inizializzazione del pacchetto Mappe

I progetti di `Xamarin.FormsMaps.Init` applicazione devono chiamare `Xamarin.Forms.Forms.Init`il metodo dopo aver effettuato una chiamata a .

### <a name="enabling-map-services"></a>Abilitazione dei servizi di mappe

Poiché `Map` è possibile ottenere la posizione dell'utente, l'applicazione deve ottenere l'autorizzazione per l'utente nel modo descritto in precedenza in questo capitolo:

#### <a name="enabling-ios-maps"></a>Abilitazione delle mappe iOS

Un'applicazione iOS che usa `Map` richiede due righe nel file info.plist.

#### <a name="enabling-android-maps"></a>Abilitazione delle mappe Android

Per l'utilizzo dei servizi Google Map è necessaria una chiave di autorizzazione. Questa chiave viene inserita nel file **AndroidManifest.xml.This** key is inserted in the AndroidManifest.xml file. Inoltre, il file **AndroidManifest.xml** richiede `manifest` i tag coinvolti nell'ottenere la posizione dell'utente.

#### <a name="enabling-uwp-maps"></a>Abilitazione delle mappe UWP

Un'applicazione Universal Windows Platform richiede una chiave di autorizzazione per l'utilizzo di Bing Maps. Questa chiave viene passata `Xamarin.FormsMaps.Init` come argomento al metodo. L'applicazione deve essere abilitata anche per i servizi di posizione.

### <a name="the-unadorned-map"></a>La mappa disadorna

L'esempio [**MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) è costituito da un file [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) e [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) file code-behind che consente di passare a vari programmi dimostrativi.

Il file [BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) mostra [`Map`](xref:Xamarin.Forms.Maps.Map) come visualizzare la visualizzazione. Per impostazione predefinita visualizza la città di Roma, ma la mappa può essere manipolata dall'utente.

Per disabilitare lo scorrimento orizzontale e verticale, impostare la [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) proprietà su `false`. Per disattivare lo [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) `false`zoom, impostare su . Queste proprietà potrebbero non funzionare su tutte le piattaforme.

### <a name="streets-and-terrain"></a>Strade e terreni

È possibile visualizzare diversi tipi `Map` di [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) mappe [`MapType`](xref:Xamarin.Forms.Maps.MapType)impostando la proprietà di tipo , un'enumerazione con tre membri:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), l'impostazione predefinita
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

Il [mapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) file viene illustrato come utilizzare un pulsante di opzione per selezionare il tipo di mappa. Utilizza la [`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) classe nella libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) e una classe basata sul file [MapTypeRadioButton.xaml.](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml)

### <a name="map-coordinates"></a>Coordinate della mappa

Un programma può ottenere l'area corrente che l'oggetto `Map` visualizza tramite la [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) proprietà . Questa proprietà *non* è supportata da una proprietà associabile e non esiste alcun meccanismo di notifica per indicare quando è stata modificata, pertanto un programma che desidera monitorare la proprietà deve probabilmente utilizzare un timer a tale scopo.

`VisibleRegion`è di [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)tipo , una classe con quattro proprietà di sola lettura:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)di tipo[`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)di `double`tipo , indicando l'altezza dell'area visualizzata della mappa
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)di `double`tipo , che indica la larghezza dell'area visualizzata della mappa
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)di [`Distance`](xref:Xamarin.Forms.Maps.Distance)tipo , indicando le dimensioni dell'area circolare più grande visibile sulla mappa

`Position`e `Distance` sono entrambe le strutture. `Position`definisce due proprietà di sola lettura impostate tramite il [ `Position` costruttore](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance`è destinato a fornire una distanza indipendente dall'unità convertendo tra unità metriche e inglesi. Un `Distance` valore può essere creato in diversi modi:A value can be created in several ways:

- costruttore con una distanza in metri [ `Distance` ](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double))
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double))metodo statico
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double))metodo statico
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double))metodo statico

Il valore è disponibile da tre proprietà:The value is available from three properties:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)di tipo`double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)di tipo`double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)di tipo`double`

Il [mapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) file `Label` contiene diversi `MapSpan` elementi per la visualizzazione delle informazioni. Il [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) file code-behind utilizza un timer per mantenere aggiornate le informazioni mentre l'utente modifica la mappa.

### <a name="position-extensions"></a>Estensioni di posizione

Una nuova libreria per questo libro [**denominata Xamarin.FormsBook.Toolkit.Maps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contiene tipi specifici della mappa ma indipendenti dalla piattaforma. La [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe `ToString` dispone `Position`di un metodo per e `Position` un metodo per calcolare la distanza tra due valori.

### <a name="setting-an-initial-location"></a>Impostazione di una posizione iniziale

È possibile [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)) chiamare `Map` il metodo di per impostare a livello di codice una posizione e il livello di zoom sulla mappa. L'argomento è `MapSpan`di tipo . È possibile `MapSpan` creare un oggetto utilizzando una delle seguenti opzioni:

- costruttore `Position`con un intervallo di latitudine e longitudine e latitudine [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double))
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance))con `Position` un e il raggio

È anche possibile creare un `MapSpan` nuovo da uno [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) esistente [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double))utilizzando i metodi o .

Il file [WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) e [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) file code-behind illustra come utilizzare il `MoveToRegion` metodo per visualizzare lo stato del Wyoming.

In alternativa, è possibile `MapSpan` utilizzare il [ `Map` costruttore](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan)) con un oggetto per inizializzare la posizione della mappa. Il file [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) mostra come eseguire questa operazione interamente in XAML per visualizzare la sede centrale di Xamarin a San Francisco.

### <a name="dynamic-zooming"></a>Ingrandimento dinamico

È possibile `Slider` utilizzare un oggetto per eseguire lo zoom dinamico di una mappa. Il file [Radius-oomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) e [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) file code-behind mostrano `Slider` come modificare il raggio di una mappa in base al valore.

Il file [Longitude'oomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) e [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) file code-behind mostrano un approccio alternativo che funziona meglio su Android, ma nessuno dei due approcci funziona bene sulle piattaforme Windows.

### <a name="the-phones-location"></a>La posizione del telefono

La [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) proprietà `Map` di funziona in modo leggermente diverso in ogni piattaforma come il showLocationPage.xaml file dimostra:The property of works a littledifferently differently on each platform as the [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) file demonstrates:

- Su iOS, un punto blu indica la posizione del telefono, ma è necessario navigare manualmente
- Su Android, viene visualizzata un'icona che quando viene spinta sposta la mappa nella posizione del telefono
- La piattaforma UWP è simile a iOS, ma a volte passa automaticamente alla posizione

Il progetto **MapDemos** tenta di simulare l'approccio Android definendo innanzitutto un pulsante basato su icona basato sul file [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) e [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) file code-behind.

Il file [GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) e [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) file code-behind utilizzano questo pulsante per passare alla posizione del telefono.

### <a name="pins-and-science-museums"></a>Pin e musei della scienza

Infine, `Map` la classe [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins) definisce `IList<Pin>`una proprietà di tipo . La [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe definisce quattro proprietà:The class defines four properties:

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)di `string`tipo , una proprietà obbligatoria
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)di `string`tipo , un indirizzo opzionale leggibile dall'utente
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)di `Position`tipo , indicando dove viene visualizzato il perno sulla mappa
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)di [`PinType`](xref:Xamarin.Forms.Maps.PinType)tipo , un'enumerazione, che non viene utilizzata

Il progetto **MapDemos** contiene il file [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), che [`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) elenca [`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) i musei scientifici negli Stati Uniti e le classi per la deserializzazione di questi dati.

Il file [ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) e [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) pin di visualizzazione dei file code-behind per questi musei scientifici nella mappa. Quando l'utente tocca un pin, visualizza l'indirizzo e un sito web per il museo.

### <a name="the-distance-between-two-points"></a>La distanza tra due punti

La [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe [`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) contiene un metodo con un calcolo semplificato della distanza tra due posizioni geografiche.

Viene usato nel file [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) e [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) file code-behind per visualizzare anche la distanza dal museo dalla posizione dell'utente:

[![Triplo screenshot della pagina Musei locali](images/ch28fg28-small.png "Distanza da una posizione")](images/ch28fg28-large.png#lightbox "Distanza da una posizione")

Il programma mostra anche come limitare dinamicamente il numero di pin in base alla posizione della mappa.

## <a name="geocoding-and-back-again"></a>Geocodifica e viceversa

L'assembly [**Xamarin.Forms.Maps**](xref:Xamarin.Forms.Maps) [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) contiene inoltre [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) una classe con un metodo che converte un [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position)) indirizzo di testo in zero o più posizioni geografiche possibili e un altro metodo che esegue la conversione nella direzione opposta.

Il file [GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) e [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) file code-behind illustrano questa funzionalità.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 28 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Capitolo 28 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Mappa Xamarin.Forms](~/xamarin-forms/user-interface/map/index.md)
