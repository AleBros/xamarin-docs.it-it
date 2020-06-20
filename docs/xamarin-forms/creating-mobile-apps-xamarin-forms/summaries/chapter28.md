---
title: Riepilogo del capitolo 28. Posizione e mappe
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 28. Posizione e mappe'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 301dc65c7909603e117717a993959e3c73fa2d32
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84133406"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Riepilogo del capitolo 28. Posizione e mappe

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> Le note in questa pagina indicano Xamarin.Forms le aree in cui è stato divergente rispetto al materiale presentato nel libro.

Xamarin.Formssupporta un [`Map`](xref:Xamarin.Forms.Maps.Map) elemento che deriva da `View` . A causa dei requisiti speciali della piattaforma necessari per l'utilizzo delle mappe, questi vengono implementati in un assembly separato ** Xamarin.Forms . Maps**e coinvolgono uno spazio dei nomi diverso: `Xamarin.Forms.Maps` .

## <a name="the-geographic-coordinate-system"></a>Sistema di coordinate geografico

Un sistema di coordinate geografiche identifica le posizioni su un oggetto sferico (o quasi sferico) come la terra. Una coordinata è costituita da una *Latitudine* e una *Longitudine* espresse in angoli.

Un cerchio grande chiamato `equator` è A metà tra i due poli attraverso i quali l'asse della terra si estende in modo concettuale.

### <a name="parallels-and-latitude"></a>Paralleli e latitudine

Un angolo misurato a nord o a sud dell'equatore dal centro della terra contrassegna le linee della latitudine uguale, nota come *paralleli*. Questi valori sono compresi tra 0 gradi nell'equatore e 90 gradi nei poli nord e sud. Per convenzione, le latitudini nord dell'equatore sono valori positivi e quelle meridionali dell'equatore sono valori negativi.

### <a name="longitude-and-meridians"></a>Longitudine e meridiani

Le metà dei cerchi grandi dal Polo Nord al Polo Sud sono linee di longitudine uguale, note anche come *meridiani*. Questi sono relativi al meridiano principale di Greenwich, Inghilterra. Per convenzione, la longitudine est del meridiano principale è costituita da valori positivi compresi tra 0 e 180 gradi e la longitudine ovest del meridiano principale è un valore negativo compreso tra 0 e &ndash; 180 gradi.

### <a name="the-equirectangular-projection"></a>Proiezione equirettangolare

Qualsiasi mappa piana della terra introduce distorsioni. Se tutte le righe di latitudine e longitudine sono diritte e se le differenze uguali negli angoli di latitudine e Longitudine corrispondono a distanze uguali sulla mappa, il risultato è una *proiezione equirettangolare*. Questa mappa deforma le aree più vicine ai pali perché sono estesi orizzontalmente.

### <a name="the-mercator-projection"></a>Proiezione Mercator

La *proiezione di Mercator* più diffusa tenta di compensare l'allungamento orizzontale eseguendo anche l'allungamento verticale di queste aree. In questo modo si ottiene una mappa in cui le aree vicine ai pali risultano molto più grandi di quelle effettivamente, ma tutte le aree locali sono conformi piuttosto strettamente all'area effettiva.

### <a name="map-services-and-tiles"></a>Mappare i servizi e i riquadri

I servizi map utilizzano una variante della proiezione Mercator denominata `Web Mercator` . I servizi map recapitano i riquadri bitmap a un client in base alla posizione e al livello di zoom.

## <a name="getting-the-users-location"></a>Ottenere la posizione dell'utente

Le Xamarin.Forms `Map` classi non includono una funzionalità per ottenere la posizione geografica dell'utente, ma ciò è spesso auspicabile quando si utilizzano le mappe, quindi un servizio di dipendenza deve gestirlo.

> [!NOTE]
> Xamarin.Formsle applicazioni possono invece usare la [`Geolocation`](~/essentials/geolocation.md) classe inclusa in Xamarin.Essentials .

### <a name="the-location-tracker-api"></a>API location tracker

La soluzione [**Novell. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) contiene il codice per un'API location tracker. La [`GeographicLocation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) struttura incapsula una latitudine e una longitudine. L' [`ILocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interfaccia definisce due metodi per avviare e sospendere il rilevamento della posizione e un evento quando è disponibile un nuovo percorso.

#### <a name="the-ios-location-manager"></a>Gestione percorso iOS

L'implementazione di iOS di `ILocationTracker` è una [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) classe che usa IOS [`CLLocationManager`](xref:CoreLocation.CLLocationManager) .

#### <a name="the-android-location-manager"></a>Gestione località di Android

L'implementazione di Android di `ILocationTracker` è una [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) classe che usa la classe Android [`LocationManager`](xref:Android.Locations.LocationManager) .

#### <a name="the-uwp-geo-locator"></a>Localizzatore geografico UWP

Il piattaforma UWP (Universal Windows Platform) implementazione di `ILocationTracker` è una [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) classe che usa UWP [`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator) .

### <a name="display-the-phones-location"></a>Visualizza la posizione del telefono

L'esempio [**WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) usa il percorso Tracker per visualizzare la posizione del telefono, sia nel testo che in una mappa equirettangolare.

### <a name="the-required-overhead"></a>Overhead obbligatorio

È necessario un sovraccarico per **WhereAmI** per l'uso di location tracker. Per prima cosa, tutti i progetti nella soluzione **WhereAmI** devono avere riferimenti ai progetti corrispondenti in **Novell. FormsBook. Platform**e ogni progetto **WhereAmI** deve chiamare il `Toolkit.Init` metodo.

È necessario un sovraccarico aggiuntivo specifico della piattaforma, sotto forma di autorizzazioni per il percorso.

#### <a name="location-permission-for-ios"></a>Autorizzazione location per iOS

Per iOS, il file **info. plist** deve includere elementi contenenti il testo di una domanda che chiede all'utente di ottenere la posizione dell'utente.

#### <a name="location-permissions-for-android"></a>Autorizzazioni per il percorso per Android

Le applicazioni Android che ottengono il percorso dell'utente devono disporre di un'autorizzazione ACCESS_FILE_LOCATION nel file di AndroidManifest.xml.

#### <a name="location-permissions-for-the-uwp"></a>Autorizzazioni per il percorso per UWP

Un'applicazione piattaforma UWP (Universal Windows Platform) deve avere una `location` funzionalità del dispositivo contrassegnata nel file Package. appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Utilizzo di Xamarin.Forms . Mappe

Per l'utilizzo della classe sono necessari diversi requisiti `Map` .

### <a name="the-nuget-package"></a>Pacchetto NuGet

Oggetto ** Xamarin.Forms . Maps** è necessario aggiungere la libreria NuGet alla soluzione dell'applicazione. Il numero di versione deve corrispondere al **Xamarin.Forms** pacchetto attualmente installato.

### <a name="initializing-the-maps-package"></a>Inizializzazione del pacchetto Maps

I progetti di applicazione devono chiamare il `Xamarin.FormsMaps.Init` metodo dopo aver effettuato una chiamata a `Xamarin.Forms.Forms.Init` .

### <a name="enabling-map-services"></a>Abilitazione dei servizi di mapping

Poiché `Map` può ottenere la posizione dell'utente, l'applicazione deve ottenere l'autorizzazione per l'utente nel modo descritto in precedenza in questo capitolo:

#### <a name="enabling-ios-maps"></a>Abilitazione delle mappe iOS

Un'applicazione iOS che usa `Map` richiede due righe nel file INFO. plist.

#### <a name="enabling-android-maps"></a>Abilitazione di Maps Android

Per l'uso dei servizi Google Map è necessaria una chiave di autorizzazione. Questa chiave viene inserita nel file di **AndroidManifest.xml** . Inoltre, il file di **AndroidManifest.xml** richiede i `manifest` tag necessari per ottenere la posizione dell'utente.

#### <a name="enabling-uwp-maps"></a>Abilitazione delle mappe UWP

Per un'applicazione piattaforma UWP (Universal Windows Platform) è necessaria una chiave di autorizzazione per l'utilizzo di Bing Maps. Questa chiave viene passata come argomento al `Xamarin.FormsMaps.Init` metodo. L'applicazione deve inoltre essere abilitata per i servizi di posizione.

### <a name="the-unadorned-map"></a>Mappa non decorata

L'esempio [**MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) è costituito da un file [MapsDemoHomePage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) e da un file code-behind [MapsDemoHomePage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) che consente lo spostamento a vari programmi dimostrativi.

Il file [BasicMapPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) Mostra come visualizzare la visualizzazione [`Map`](xref:Xamarin.Forms.Maps.Map) . Per impostazione predefinita, viene visualizzata la città di Roma, ma la mappa può essere modificata dall'utente.

Per disabilitare lo scorrimento orizzontale e verticale, impostare la [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) proprietà su `false` . Per disabilitare lo zoom, impostare [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) su `false` . Queste proprietà potrebbero non funzionare in tutte le piattaforme.

### <a name="streets-and-terrain"></a>Strade e terreno

È possibile visualizzare diversi tipi di mappe impostando la `Map` proprietà [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) di tipo [`MapType`](xref:Xamarin.Forms.Maps.MapType) , un'enumerazione con tre membri:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), il valore predefinito
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

Il file [MapTypesPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) Mostra come usare un pulsante di opzione per selezionare il tipo di mappa. Usa la [`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) e una classe basata sul file [MapTypeRadioButton. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) .

### <a name="map-coordinates"></a>Coordinate mappa

Un programma può ottenere l'area corrente visualizzata dall'oggetto `Map` tramite la [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) Proprietà. Questa proprietà *non* è supportata da una proprietà associabile e non è presente alcun meccanismo di notifica per indicare quando è stato modificato, quindi un programma che desidera monitorare la proprietà dovrebbe probabilmente utilizzare un timer a tale scopo.

`VisibleRegion`è di tipo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) , una classe con quattro proprietà di sola lettura:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)di tipo[`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees)di tipo `double` , che indica l'altezza dell'area visualizzata della mappa
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees)di tipo `double` , che indica la larghezza dell'area visualizzata della mappa
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius)di tipo [`Distance`](xref:Xamarin.Forms.Maps.Distance) , che indica le dimensioni dell'area circolare più grande visibile sulla mappa

`Position`e `Distance` sono entrambe strutture. `Position`definisce due proprietà di sola lettura impostate tramite il [ `Position` Costruttore](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance`ha lo scopo di fornire una distanza indipendente dall'unità eseguendo la conversione tra le unità metriche e inglesi. `Distance`È possibile creare un valore in diversi modi:

- [ `Distance` Costruttore](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double)) con una distanza in metri
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double))Metodo statico
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double))Metodo statico
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double))Metodo statico

Il valore è disponibile da tre proprietà:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)di tipo`double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)di tipo`double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)di tipo`double`

Il file [MapCoordinatesPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) contiene diversi `Label` elementi per la visualizzazione delle `MapSpan` informazioni. Il file code-behind [MapCoordinatesPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) utilizza un timer per aggiornare le informazioni quando l'utente modifica la mappa.

### <a name="position-extensions"></a>Estensioni posizione

Una nuova libreria per questo libro denominata [**Novell. FormsBook. Toolkit. Maps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contiene tipi specifici della mappa ma indipendenti dalla piattaforma. La [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe dispone di un `ToString` metodo per `Position` e un metodo per calcolare la distanza tra due `Position` valori.

### <a name="setting-an-initial-location"></a>Impostazione di un percorso iniziale

È possibile chiamare [ `MoveToRegion` ] (xrif: Xamarin.Forms . Maps. map. MoveToRegion ( Xamarin.Forms . Maps. MapSpan)) per impostare a livello di `Map` codice una posizione e un livello di zoom sulla mappa. L'argomento è di tipo `MapSpan` . È possibile creare un `MapSpan` oggetto utilizzando uno dei seguenti elementi:

- [ `MapSpan` Costruttore] (xrif: Xamarin.Forms . Maps. MapSpan .% 23ctor ( Xamarin.Forms . Maps. Position, System. Double, System. Double)) con un `Position` , e latitudine e Longitudine span
- [ `MapSpan.FromCenterAndRadius` ] (xrif: Xamarin.Forms . Maps. MapSpan. FromCenterAndRadius ( Xamarin.Forms . Maps. Position, Xamarin.Forms . Maps. distance)) con un `Position` raggio e

È anche possibile creare un nuovo `MapSpan` da uno esistente usando i metodi [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) o [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)) .

Il file [WyomingPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) e il file code-behind [WyomingPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) illustrano come usare il `MoveToRegion` metodo per visualizzare lo stato di Wyoming.

In alternativa, è possibile usare il [ `Map` Costruttore] (xrif: Xamarin.Forms . Maps. map .% 23ctor ( Xamarin.Forms . Maps. MapSpan)) con un `MapSpan` oggetto per inizializzare la posizione della mappa. Il file [XamarinHQPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) illustra come eseguire questa operazione interamente in XAML per visualizzare la sede di Novell a San Francisco.

### <a name="dynamic-zooming"></a>Zoom dinamico

È possibile utilizzare un oggetto `Slider` per ingrandire dinamicamente una mappa. Il file [RadiusZoomPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) e il file code-behind [RadiusZoomPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) mostrano come modificare il raggio di una mappa in base al `Slider` valore.

Il file [LongitudeZoomPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) e il file code-behind [LongitudeZoomPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) mostrano un approccio alternativo che funziona meglio in Android, ma nessuno dei due approcci è adatto alle piattaforme Windows.

### <a name="the-phones-location"></a>Località del telefono

La [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) proprietà di `Map` funziona in modo leggermente diverso in ogni piattaforma come illustrato nel file [ShowLocationPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) :

- In iOS, un punto blu indica la posizione del telefono, ma è necessario spostarsi manualmente
- In Android viene visualizzata un'icona quando il push sposta la mappa sulla posizione del telefono
- Il UWP è simile a iOS, ma a volte passa automaticamente al percorso

Il progetto **MapDemos** tenta di simulare l'approccio Android definendo innanzitutto un pulsante basato su icone in base al file [MyLocationButton. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) e al file code-behind di [MyLocationButton.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) .

Il file [GoToLocationPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) e il file code-behind [GoToLocationPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) usano questo pulsante per passare alla posizione del telefono.

### <a name="pins-and-science-museums"></a>Pin e musei scientifici

Infine, la `Map` classe definisce una [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins) proprietà di tipo `IList<Pin>` . La [`Pin`](xref:Xamarin.Forms.Maps.Pin) classe definisce quattro proprietà:

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label)di tipo `string` , una proprietà obbligatoria
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address)di tipo `string` , un indirizzo facoltativo leggibile dall'uomo
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position)di tipo `Position` , che indica il punto in cui il PIN viene visualizzato sulla mappa
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)di tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType) , un'enumerazione, che non viene utilizzata

Il progetto **MapDemos** contiene il file [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), che elenca i musei scientifici nell'Stati Uniti e le [`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) [`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) classi e per la deserializzazione dei dati.

Il file [ScienceMuseumsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) e il file code-behind [ScienceMuseumsPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) visualizzano i pin per questi musei della scienza nella mappa. Quando l'utente tocca un PIN, Visualizza l'indirizzo e un sito Web per il Museo.

### <a name="the-distance-between-two-points"></a>Distanza tra due punti

La [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe contiene un [`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) metodo con un calcolo semplificato della distanza tra due posizioni geografiche.

Viene usato nel file [LocalMuseumsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) e nel file code-behind [LocalMuseumsPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) per visualizzare anche la distanza al Museo dalla posizione dell'utente:

[![Schermata tripla della pagina dei musei locali](images/ch28fg28-small.png "Distanza da una località")](images/ch28fg28-large.png#lightbox "Distanza da una località")

Il programma illustra anche come limitare dinamicamente il numero di pin in base alla posizione della mappa.

## <a name="geocoding-and-back-again"></a>Geocodifica e viceversa

Oggetto [** Xamarin.Forms . **](xref:Xamarin.Forms.Maps)L'assembly Maps contiene anche una [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe con un [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) metodo che converte un indirizzo di testo in zero o più posizioni geografiche possibili e un altro metodo [ `GetAddressesForPositionAsync` ] (xrif: Xamarin.Forms . Maps. geocoder. GetAddressesForPositionAsync ( Xamarin.Forms . Maps. Position)) che esegue la conversione nell'altra direzione.

Il file [GeocoderRoundTrip. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) e il file code-behind [GeocoderRoundTrip.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) illustrano questa funzionalità.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 28 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Capitolo 28 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Xamarin.FormsMappa](~/xamarin-forms/user-interface/map/index.md)
