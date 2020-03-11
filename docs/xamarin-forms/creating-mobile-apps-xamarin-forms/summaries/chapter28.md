---
title: Riepilogo dei capitoli 28. Posizione e mappe
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo dei capitoli 28. Posizione e mappe'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 5dcd84536cc6d80deb753fc6fe57f9090f6b2dad
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "72697082"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Riepilogo dei capitoli 28. Posizione e mappe

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

Novell. Forms supporta un elemento [`Map`](xref:Xamarin.Forms.Maps.Map) che deriva da `View`. A causa dei requisiti speciali della piattaforma coinvolti nell'uso delle mappe, vengono implementati in un assembly separato, **Novell. Forms. Maps**, e coinvolgono uno spazio dei nomi diverso: `Xamarin.Forms.Maps`.

## <a name="the-geographic-coordinate-system"></a>Il sistema di coordinate geografico

Un sistema di coordinate geografico identifica le posizioni in un oggetto sferico (o quasi sferica), ad esempio la terra. Una coordinata è costituita da una *Latitudine* e una *Longitudine* espresse in angoli.

Un cerchio eccezionale chiamato `equator` è A metà tra i due poli attraverso i quali l'asse della terra si estende concettualmente.

### <a name="parallels-and-latitude"></a>Parallels e la latitudine

Un angolo misurato a nord o a sud dell'equatore dal centro della terra contrassegna le linee della latitudine uguale, nota come *paralleli*. Queste vanno da 0 gradi all'equatore a 90 gradi a nord e Sud poli. Per convenzione, latitudini a nord dell'equatore sono i valori positivi e quelli congiunge equatore sono valori negativi.

### <a name="longitude-and-meridians"></a>Indicata con longitudine e meridiani

Le metà dei cerchi grandi dal Polo Nord al Polo Sud sono linee di longitudine uguale, note anche come *meridiani*. Questi sono relativi alla meridiano principale nell'ora di Greenwich, Inghilterra. Per convenzione, le Longitudine est del meridiano principale sono valori positivi compresi tra 0 e 180 gradi e la longitudine ovest del meridiano principale è un valore negativo compreso tra 0 gradi e &ndash;180 gradi.

### <a name="the-equirectangular-projection"></a>La proiezione equirettangolare

Qualsiasi mappa della terra piatta introduce le distorsioni. Se tutte le righe di latitudine e longitudine sono diritte e se le differenze uguali negli angoli di latitudine e Longitudine corrispondono a distanze uguali sulla mappa, il risultato è una *proiezione equirettangolare*. Questa mappa distorce aree più vicini ai poli perché vengono adattate orizzontalmente.

### <a name="the-mercator-projection"></a>La proiezione Mercator

La *proiezione di Mercator* più diffusa tenta di compensare l'allungamento orizzontale eseguendo anche l'allungamento verticale di queste aree. Ciò comporta una mappa in cui aree vicino poli sono presenti più ampio di quanto sono in realtà, ma qualsiasi rete locale piuttosto strettamente conforme con l'area effettivo.

### <a name="map-services-and-tiles"></a>Mapping dei servizi e i riquadri

I servizi map utilizzano una variante della proiezione Mercator denominata `Web Mercator`. I servizi di mappe recapitare le tessere mappa di bit a un client in base a livello di zoom e percorso.

## <a name="getting-the-users-location"></a>Recupero della posizione dell'utente

Le classi `Map` Novell. Forms non includono una funzionalità per ottenere la posizione geografica dell'utente, ma ciò è spesso auspicabile quando si utilizzano le mappe, quindi un servizio di dipendenza deve gestirlo.

> [!NOTE]
> Le applicazioni Novell. Forms possono invece usare la classe [`Geolocation`](~/essentials/geolocation.md) inclusa in Novell. Essentials.

### <a name="the-location-tracker-api"></a>L'API di rilevamento delle località

La soluzione [**Novell. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) contiene il codice per un'API location tracker. La struttura [`GeographicLocation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) incapsula una latitudine e una longitudine. L'interfaccia [`ILocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) definisce due metodi per avviare e sospendere il rilevamento della posizione e un evento quando è disponibile un nuovo percorso.

#### <a name="the-ios-location-manager"></a>La gestione di percorsi di iOS

L'implementazione iOS di `ILocationTracker` è una classe [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) che usa il [`CLLocationManager`](xref:CoreLocation.CLLocationManager)iOS.

#### <a name="the-android-location-manager"></a>Gestione percorsi di Android

L'implementazione Android di `ILocationTracker` è una classe [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) che usa la classe di [`LocationManager`](xref:Android.Locations.LocationManager) Android.

#### <a name="the-uwp-geo-locator"></a>L'indicatore di posizione geografica UWP

L'implementazione piattaforma UWP (Universal Windows Platform) di `ILocationTracker` è una classe [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) che utilizza il [`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator)UWP.

### <a name="display-the-phones-location"></a>Visualizzare la posizione del telefono

L'esempio [**WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) usa il percorso Tracker per visualizzare la posizione del telefono, sia nel testo che in una mappa equirettangolare.

### <a name="the-required-overhead"></a>Il sovraccarico richiesto

È necessario un sovraccarico per **WhereAmI** per l'uso di location tracker. Per prima cosa, tutti i progetti nella soluzione **WhereAmI** devono avere riferimenti ai progetti corrispondenti in **Novell. FormsBook. Platform**e ogni progetto **WhereAmI** deve chiamare il metodo `Toolkit.Init`.

Un overhead aggiuntivo specifico della piattaforma, sotto forma delle autorizzazioni del percorso, è necessario.

#### <a name="location-permission-for-ios"></a>Autorizzazione di posizione per iOS

Per iOS, il file **info. plist** deve includere elementi contenenti il testo di una domanda che chiede all'utente di ottenere la posizione dell'utente.

#### <a name="location-permissions-for-android"></a>Autorizzazioni del percorso per Android

Le applicazioni Android che ottengono la posizione dell'utente devono avere un'autorizzazione ACCESS_FILE_LOCATION nel file androidmanifest. Xml.

#### <a name="location-permissions-for-the-uwp"></a>Autorizzazioni del percorso per la piattaforma UWP

Un'applicazione piattaforma UWP (Universal Windows Platform) deve avere una funzionalità del dispositivo `location` contrassegnata nel file Package. appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Utilizzo di verifica

Per l'utilizzo della classe `Map` sono necessari diversi requisiti.

### <a name="the-nuget-package"></a>Il pacchetto NuGet

È necessario aggiungere la libreria NuGet **Novell. Forms. Maps** alla soluzione dell'applicazione. Il numero di versione deve corrispondere al pacchetto **Novell. Forms** attualmente installato.

### <a name="initializing-the-maps-package"></a>L'inizializzazione del pacchetto di mappe

I progetti di applicazione devono chiamare il metodo `Xamarin.FormsMaps.Init` dopo aver effettuato una chiamata a `Xamarin.Forms.Forms.Init`.

### <a name="enabling-map-services"></a>Abilitazione mapping dei servizi

Poiché il `Map` può ottenere la posizione dell'utente, l'applicazione deve ottenere l'autorizzazione per l'utente nel modo descritto in precedenza in questo capitolo:

#### <a name="enabling-ios-maps"></a>Esegue il mapping di attivazione di iOS

Un'applicazione iOS che usa `Map` richiede due righe nel file INFO. plist.

#### <a name="enabling-android-maps"></a>Abilitazione di Android viene eseguito il mapping

Una chiave di autorizzazione è necessaria per l'uso di servizi di Google Map. Questa chiave viene inserita nel file **file AndroidManifest. XML** . Il file **file AndroidManifest. XML** richiede inoltre `manifest` tag necessari per ottenere la posizione dell'utente.

#### <a name="enabling-uwp-maps"></a>Esegue il mapping di abilitazione della piattaforma UWP

Un'applicazione (Universal Windows Platform) richiede una chiave di autorizzazione per l'uso di Bing mappe. Questa chiave viene passata come argomento al metodo `Xamarin.FormsMaps.Init`. L'applicazione deve anche essere abilitata per servizi di posizione.

### <a name="the-unadorned-map"></a>La mappa non decorata

L'esempio [**MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) è costituito da un file [MapsDemoHomePage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) e da un file code-behind [MapsDemoHomePage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) che consente lo spostamento a vari programmi dimostrativi.

Il file [BasicMapPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) Mostra come visualizzare la visualizzazione [`Map`](xref:Xamarin.Forms.Maps.Map) . Per impostazione predefinita vengono visualizzate le città di Roma, ma la mappa può essere modificata dall'utente.

Per disabilitare lo scorrimento orizzontale e verticale, impostare la proprietà [`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) su `false`. Per disabilitare lo zoom, impostare [`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) su `false`. Queste proprietà potrebbero non funzionare in tutte le piattaforme.

### <a name="streets-and-terrain"></a>Streets and terreno

È possibile visualizzare diversi tipi di mappe impostando la proprietà `Map` [`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) di tipo [`MapType`](xref:Xamarin.Forms.Maps.MapType), un'enumerazione con tre membri:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), il valore predefinito
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

Il file [MapTypesPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) Mostra come usare un pulsante di opzione per selezionare il tipo di mappa. Usa la classe [`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) e una classe basata sul file [MapTypeRadioButton. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) .

### <a name="map-coordinates"></a>Coordinate della mappa

Un programma può ottenere l'area corrente visualizzata dalla `Map` tramite la proprietà [`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion) . Questa proprietà *non* è supportata da una proprietà associabile e non è presente alcun meccanismo di notifica per indicare quando è stato modificato, quindi un programma che desidera monitorare la proprietà dovrebbe probabilmente utilizzare un timer a tale scopo.

`VisibleRegion` è di tipo [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan), una classe con quattro proprietà di sola lettura:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center) di tipo [`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees) di tipo `double`, che indica l'altezza dell'area visualizzata della mappa.
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees) di tipo `double`, che indica la larghezza dell'area visualizzata della mappa.
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius) di tipo [`Distance`](xref:Xamarin.Forms.Maps.Distance), che indica le dimensioni dell'area circolare più grande visibile sulla mappa

`Position` e `Distance` sono entrambe strutture. `Position` definisce due proprietà di sola lettura impostate tramite il [costruttore`Position`](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance` ha lo scopo di fornire una distanza indipendente dall'unità convertendo tra le unità metriche e inglesi. È possibile creare un valore `Distance` in diversi modi:

- [costruttore`Distance`](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double)) con una distanza in metri
- Metodo statico [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double))
- Metodo statico [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double))
- Metodo statico [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double))

Il valore è disponibile da tre proprietà:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters) di tipo `double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers) di tipo `double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles) di tipo `double`

Il file [MapCoordinatesPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) contiene diversi elementi `Label` per la visualizzazione delle informazioni di `MapSpan`. Il file code-behind [MapCoordinatesPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) utilizza un timer per aggiornare le informazioni quando l'utente modifica la mappa.

### <a name="position-extensions"></a>Estensioni di posizione

Una nuova libreria per questo libro denominata [**Novell. FormsBook. Toolkit. Maps**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contiene tipi specifici della mappa ma indipendenti dalla piattaforma. La classe [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) dispone di un metodo `ToString` per `Position`e un metodo per calcolare la distanza tra due valori `Position`.

### <a name="setting-an-initial-location"></a>L'impostazione di una posizione iniziale

È possibile chiamare il metodo [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)) di `Map` per impostare a livello di codice una posizione e un livello di zoom sulla mappa. L'argomento è di tipo `MapSpan`. È possibile creare un oggetto `MapSpan` usando uno dei seguenti elementi:

- [costruttore`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double)) con `Position`e latitudine e Longitudine
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance)) con una `Position` e un raggio

È anche possibile creare un nuovo `MapSpan` da uno esistente usando i metodi [`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) o [`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)).

Il file [WyomingPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) e il file code-behind [WyomingPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) illustrano come usare il metodo `MoveToRegion` per visualizzare lo stato di Wyoming.

In alternativa, è possibile usare il [costruttore`Map`](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan)) con un oggetto `MapSpan` per inizializzare il percorso della mappa. Il file [XamarinHQPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) illustra come eseguire questa operazione interamente in XAML per visualizzare la sede di Novell a San Francisco.

### <a name="dynamic-zooming"></a>Lo zoom dinamico

È possibile usare un `Slider` per ingrandire dinamicamente una mappa. Il file [RadiusZoomPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) e il file code-behind [RadiusZoomPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) mostrano come modificare il raggio di una mappa in base al valore `Slider`.

Il file [LongitudeZoomPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) e il file code-behind [LongitudeZoomPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) mostrano un approccio alternativo che funziona meglio in Android, ma nessuno dei due approcci è adatto alle piattaforme Windows.

### <a name="the-phones-location"></a>Posizione del telefono

La proprietà [`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser) di `Map` funziona in modo leggermente diverso in ogni piattaforma come illustrato nel file [ShowLocationPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) :

- In iOS, un punto blu indica la posizione del telefono, ma è necessario passare manualmente non esiste
- In Android, viene visualizzata un'icona che, quando il push si sposta la mappa alla posizione del telefono
- La piattaforma UWP è simile ai dispositivi iOS, ma in alcuni casi automaticamente consente di passare al percorso

Il progetto **MapDemos** tenta di simulare l'approccio Android definendo innanzitutto un pulsante basato su icone in base al file [MyLocationButton. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) e al file code-behind di [MyLocationButton.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) .

Il file [GoToLocationPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) e il file code-behind [GoToLocationPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) usano questo pulsante per passare alla posizione del telefono.

### <a name="pins-and-science-museums"></a>I pin e musei science

Infine, la classe `Map` definisce una proprietà [`Pins`](xref:Xamarin.Forms.Maps.Map.Pins) di tipo `IList<Pin>`. La classe [`Pin`](xref:Xamarin.Forms.Maps.Pin) definisce quattro proprietà:

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label) di tipo `string`, una proprietà obbligatoria
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address) di tipo `string`, un indirizzo leggibile facoltativo
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) di tipo `Position`, che indica il punto in cui il PIN viene visualizzato sulla mappa
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) di tipo [`PinType`](xref:Xamarin.Forms.Maps.PinType), un'enumerazione, che non viene utilizzata

Il progetto **MapDemos** contiene il file [ScienceMuseums. XML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), che elenca i musei scientifici nell'Stati Uniti e le classi [`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) e [`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) per la deserializzazione dei dati.

Il file [ScienceMuseumsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) e il file code-behind [ScienceMuseumsPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) visualizzano i pin per questi musei della scienza nella mappa. Quando l'utente tocca un pin, viene visualizzato l'indirizzo e un sito Web per il museo.

### <a name="the-distance-between-two-points"></a>La distanza tra due punti

La classe [`PositionExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) contiene un metodo [`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) con un calcolo semplificato della distanza tra due posizioni geografiche.

Viene usato nel file [LocalMuseumsPage. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) e nel file code-behind [LocalMuseumsPage.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) per visualizzare anche la distanza al Museo dalla posizione dell'utente:

[![Schermata tripla della pagina dei musei locali](images/ch28fg28-small.png "Distanza da una località")](images/ch28fg28-large.png#lightbox "Distanza da una località")

Il programma viene inoltre illustrato come limitare in modo dinamico il numero di pin in base alla posizione della mappa.

## <a name="geocoding-and-back-again"></a>Geocodifica e uscire

L'assembly [**Novell. Forms. Maps**](xref:Xamarin.Forms.Maps) contiene anche una classe [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) con un metodo [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) che converte un indirizzo di testo in zero o più posizioni geografiche possibili e un altro metodo [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position)) che esegue la conversione nell'altra direzione.

Il file [GeocoderRoundTrip. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) e il file code-behind [GeocoderRoundTrip.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) illustrano questa funzionalità.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 28 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Capitolo 28 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Mappa di Novell. Forms](~/xamarin-forms/user-interface/map/index.md)
