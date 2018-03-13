---
title: Riepilogo del capitolo 28. Percorso e mappe
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 0968270f657d3195855a752da311b4855283e971
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Riepilogo del capitolo 28. Percorso e mappe

Xamarin. Forms supporta un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) elemento che deriva da `View`. A causa dei requisiti della piattaforma speciali relativi all'utilizzo di mappe, vengono implementati in un assembly separato, **Xamarin.Forms.Maps**e implicano un spazio dei nomi diverso: `Xamarin.Forms.Maps`.

## <a name="the-geographic-coordinate-system"></a>Il sistema di coordinate geografico

Un sistema di coordinate geografico identifica le posizioni in un oggetto sferico (o quasi sferica) come la terra. Una coordinata è costituito un *latitudine* e *longitudine* espresso angoli.

Chiamata di un cerchio eccellente il `equator` si trova a metà tra due poli tramite cui si estende a livello concettuale dell'asse della terra.

### <a name="parallels-and-latitude"></a>Parallels e latitudine

Angolo espresso Nord o sud dell'equatore dall'area dei segni di terra linee della latitudine uguale noto come *parallels*. Questi sono compresi tra 0 gradi all'equatore e 90 gradi settentrionale e meridionale poli. Per convenzione, Latitude equatore a Nord sono valori positivi e quelli dall'equatore sono valori negativi.

### <a name="longitude-and-meridians"></a>Longitudine e meridiani

Metà dei cerchi eccellente dal polo nord al polo sud sono righe di longitudine uguale, noto anche come *i meridiani*. Questi sono relativi del primo meridiano Greenwich, in Inghilterra. Per convenzione, longitudine est del meridiano sono valori positivi da 0 a 180 gradi e longitudine a ovest del primo meridiano sono valori negativi da 0 gradi per & #x 2013; 180 gradi.

### <a name="the-equirectangular-projection"></a>La proiezione equirettangolare

Qualsiasi mappa della terra piatta introduce distorsioni. Se tutte le righe di latitudine e longitudine sono semplici e se le differenze uguale in latitudine e longitudine angoli corrispondono a equidistanti sulla mappa, il risultato è un *proiezione equirettangolare*. Questa mappa distorsione di aree ai poli perché vengono adattate orizzontalmente.

### <a name="the-mercator-projection"></a>La proiezione Mercator

Il diffuso *proiezione Mercator* tenta compensare l'adattamento orizzontale estendendo anche queste aree in senso verticale. Il risultato di una mappa in cui le aree quasi poli appaiono molto maggiori sono effettivamente, ma qualsiasi rete locale sia conforme piuttosto strettamente con l'area effettivo.

### <a name="map-services-and-tiles"></a>Servizi di mappa e riquadri

Servizi di mappa utilizzano una variante della proiezione Mercator chiamata `Web Mercator`. I servizi di mappa recapitare le tessere mappa di bit a un client in base al livello di zoom e percorso.

## <a name="getting-the-users-location"></a>Ottenere la posizione dell'utente

Di xamarin. Forms `Map` classi non includono una funzionalità per ottenere la posizione geografica dell'utente, ma questo è spesso utile quando si usano le mappe, pertanto, un servizio di dipendenza debba di gestirlo.

### <a name="the-location-tracker-api"></a>L'API di rilevamento di percorso

Il [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) soluzione contiene codice per un rilevatore della posizione di API. Il [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) struttura incapsula latitudine e longitudine. Il [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interfaccia definisce i due metodi per avviare e sospendere l'individuazione del percorso e un evento quando è disponibile una nuova posizione.

#### <a name="the-ios-location-manager"></a>Gestione percorsi iOS

L'implementazione di iOS di `ILocationTracker` è un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) classe che consente l'utilizzo di iOS [ `CLLocationManager` ](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/).

#### <a name="the-android-location-manager"></a>Gestione percorsi Android

L'implementazione di Android `ILocationTracker` è un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) classe che utilizza il Android [ `LocationManager` ](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/) classe.

#### <a name="the-windows-runtime-geo-locator"></a>L'indicatore di posizione geografica di Windows Runtime

L'implementazione di Windows Runtime di `ILocationTracker` è un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) classe che utilizza la piattaforma UWP [ `Geolocator` ](https://msdn.microsoft.com/library/windows/apps/br225534).

### <a name="display-the-phones-location"></a>Visualizzare la posizione del telefono

Il [ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) esempio si utilizza l'individuazione del percorso per visualizzare il percorso del telefono, testo e su una mappa equirettangolare.

### <a name="the-required-overhead"></a>L'overhead richiesto

È necessario per un certo overhead **WhereAmI** per utilizzare la posizione di arresto. Innanzitutto, tutti i progetti nel **WhereAmI** soluzione deve disporre di riferimenti ai progetti corrispondenti in **Xamarin.FormsBook.Platform**e ogni **WhereAmI** progetto è necessario chiamare il `Toolkit.Init` metodo.

Un overhead aggiuntivo di specifica della piattaforma, sotto forma di autorizzazioni di percorso, è necessario.

#### <a name="location-permission-for-ios"></a>Autorizzazione di posizione per iOS

Per iOS, il **Info. plist** file deve includere elementi che contengono il testo di una domanda che chiede all'utente per consentire di ottenere la posizione dell'utente.

#### <a name="location-permissions-for-android"></a>Autorizzazioni del percorso per Android

Applicazioni Android tramite cui ottenere la posizione dell'utente devono avere un'autorizzazione ACCESS_FILE_LOCATION nel file AndroidManifest.xml.

#### <a name="location-permissions-for-the-windows-runtime"></a>Autorizzazioni del percorso per il Runtime di Windows

Un'applicazione di Windows o Windows Phone deve avere un `location` contrassegnato come funzionalità di dispositivo nel file package. appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Utilizzo di Xamarin.Forms.Maps

Coinvolti diversi requisiti utilizzando il `Map` classe.

### <a name="the-nuget-package"></a>Il pacchetto NuGet

Il **Xamarin.Forms.Maps** NuGet libreria deve essere aggiunto alla soluzione dell'applicazione. Il numero di versione deve essere lo stesso come il **xamarin. Forms** pacchetto attualmente installato.

### <a name="initializing-the-maps-package"></a>L'inizializzazione del pacchetto di mappe

I progetti di applicazione devono chiamare il `Xamarin.FormsMaps.Init` metodo dopo una chiamata a `Xamarin.Forms.Forms.Init`.

### <a name="enabling-map-services"></a>Abilitazione di servizi di mappa

Poiché il `Map` può ottenere la posizione dell'utente, l'applicazione deve ottenere l'autorizzazione per l'utente nel modo descritto in precedenza in questo capitolo:

#### <a name="enabling-ios-maps"></a>Esegue il mapping di attivazione di iOS

Un'applicazione iOS utilizzando `Map` richiede due righe nel file Info. plist.

#### <a name="enabling-android-maps"></a>Abilitare Android viene eseguito il mapping

Una chiave di autorizzazione è necessaria per usare i servizi Google mappa. Questa chiave viene inserita nella **AndroidManifest.xml** file. Inoltre, il **AndroidManifest.xml** file richiede `manifest` tag eseguite per ottenere la posizione dell'utente.

#### <a name="enabling-windows-runtime-maps"></a>Esegue il mapping di attivazione di Windows Runtime

Un'applicazione di Windows Runtime richiede una chiave di autorizzazione per l'utilizzo di Bing Maps. Questa chiave viene passata come argomento per il `Xamarin.FormsMaps.Init` metodo. L'applicazione deve inoltre essere abilitata per servizi di posizione.

### <a name="the-unadorned-map"></a>La mappa non decorata

Il [ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) esempio è costituito un [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) file e [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) file code-behind che consente lo spostamento di vari programmi di dimostrazione.

Il [BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) file viene illustrato come visualizzare il [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) visualizzazione. Per impostazione predefinita visualizza le città di Roma, ma la mappa può essere modificata dall'utente.

Per disabilitare lo scorrimento orizzontale e verticale, impostare il [ `HasScrollEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasScrollEnabled/) proprietà `false`. Per disabilitare lo zoom, impostare [ `HasZoomEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasZoomEnabled/) a `false`. Queste proprietà potrebbero non funzionare in tutte le piattaforme.

### <a name="streets-and-terrain"></a>Strade e terreno

È possibile visualizzare diversi tipi di mappe impostando il `Map` proprietà [ `MapType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.MapType/) di tipo [ `MapType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapType/), un'enumerazione con tre membri:

- [`Street`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Street/), il valore predefinito
- [`Satellite`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Satellite/)
- [`Hybrid`](https://developer.xamarin.com/api/field/Xamarin.Forms.Maps.MapType.Hybrid/)

Il [MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) file viene illustrato come utilizzare un pulsante di opzione per selezionare il tipo di mappa. Si avvalgono del [ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria e una classe in base al [MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) file.

### <a name="map-coordinates"></a>Coordinate della mappa

Un programma può ottenere l'area corrente il `Map` è visualizzato tramite il [ `VisibleRegion` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.VisibleRegion/) proprietà. Questa proprietà è *non* supportato da una proprietà associabile, e non prevede alcun meccanismo di notifica per indicare quando è stato modificato, in modo da un programma che si desidera controllare la proprietà consigliabile utilizzare un timer a tale scopo.

`VisibleRegion` è di tipo [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/), una classe con quattro proprietà di sola lettura:

- [`Center`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Center/) di tipo [`Position`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/)
- [`LatitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LatitudeDegrees/) di tipo `double`, che indica l'altezza dell'area della mappa visualizzata
- [`LongitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LongitudeDegrees/) di tipo `double`, che indica la larghezza dell'area della mappa visualizzata
- [`Radius`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Radius/) di tipo [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/), che indica la dimensione dell'area circolare più grande visibile sulla mappa

`Position` e `Distance` sono entrambe le strutture. `Position` definisce due proprietà di sola lettura impostata tramite la [ `Position` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Position.Position/p/System.Double/System.Double/):

- [`Latitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Latitude/)
- [`Longitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Longitude/)

`Distance` deve fornire una distanza di unità indipendente tramite la conversione tra unità metriche e in lingua inglese. Oggetto `Distance` valore può essere creato in diversi modi:

- [`Distance` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Distance.Distance/p/System.Double/) con una distanza in metri
- [`Distance.FromMeters`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMeters/p/System.Double/) metodo statico
- [`Distance.FromKilometers`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromKilometers/p/System.Double/) metodo statico
- [`Distance.FromMiles`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMiles/p/System.Double/) metodo statico

Il valore è disponibile da tre proprietà:

- [`Meters`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Meters/) di tipo `double`
- [`Kilometers`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Kilometers/) di tipo `double`
- [`Miles`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Miles/) di tipo `double`

Il [MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) file contiene numerosi `Label` elementi per la visualizzazione di `MapSpan` informazioni. Il [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) file code-behind utilizza un timer per mantenere le informazioni aggiornate, mentre l'utente modifica la mappa.

### <a name="position-extensions"></a>Estensioni di posizione

Una nuova libreria per questo libro denominato [ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contiene i tipi specifici di mappa ma indipendente dalla piattaforma. Il [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe dispone di un `ToString` metodo per `Position`e un metodo per calcolare la distanza tra due `Position` valori.

### <a name="setting-an-initial-location"></a>L'impostazione di una posizione iniziale

È possibile chiamare il [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion/p/Xamarin.Forms.Maps.MapSpan/) metodo `Map` per impostare un livello di zoom e posizione sulla mappa. L'argomento è di tipo `MapSpan`. È possibile creare un `MapSpan` utilizzando uno dei seguenti:

- [`MapSpan` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.MapSpan.MapSpan/p/Xamarin.Forms.Maps.Position/System.Double/System.Double/) con un `Position`e l'intervallo di latitudine e longitudine
- [`MapSpan.FromCenterAndRadius`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius/p/Xamarin.Forms.Maps.Position/Xamarin.Forms.Maps.Distance/) con un `Position` e radius

È anche possibile creare un nuovo `MapSpan` da uno esistente utilizzando i metodi [ `ClampLatitude` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.ClampLatitude/p/System.Double/System.Double/) o [ `WithZoom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.WithZoom/p/System.Double/).

Il [WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) file e [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) file code-behind di seguito viene illustrato come utilizzare il `MoveToRegion` metodo per visualizzare la stato del Wyoming.

In alternativa, è possibile utilizzare il [ `Map` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Map.Map/p/Xamarin.Forms.Maps.MapSpan/) con un `MapSpan` oggetto per inizializzare il percorso della mappa. Il [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) file viene illustrato come eseguire questa operazione interamente in XAML per visualizzare la sede centrale di Xamarin a San Francisco.

### <a name="dynamic-zooming"></a>Lo zoom dinamico

È possibile utilizzare un `Slider` per ingrandire in modo dinamico una mappa. Il [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) file e [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) viene illustrato come modificare il raggio di una mappa basata su file code-behind di `Slider` valore.

Il [LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) file e [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) file code-behind Mostra un approccio alternativo che funziona meglio su Android, ma nessuno dei due approcci funziona bene in Windows piattaforme.

### <a name="the-phones-location"></a>Percorso del telefono

Il [ `IsShowingUser` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.IsShowingUser/) proprietà di `Map` leggermente funziona in modo diverso nelle tre piattaforme come il [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) file illustra:

- In iOS, un punto blu indica la posizione del telefono, ma è necessario passare manualmente sono
- In Android, viene visualizzata un'icona che, se inserito passa la mappa il percorso del telefono
- La piattaforma UWP è simile a iOS, ma in alcuni casi automaticamente consente di passare al percorso

Il **MapDemos** progetto tenta di simulare l'approccio Android tramite la prima definizione di un pulsante di icona in base il [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) file e [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) file code-behind.

Il [GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) file e [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) file code-behind usare questo pulsante per passare al percorso del telefono.

### <a name="pins-and-science-museums"></a>I pin e musei scienza

Infine, il `Map` classe definisce un [ `Pins` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.Pins/) proprietà di tipo `IList<Pin>`. Il [ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/) classe definisce quattro proprietà:

- [`Label`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Label/) di tipo `string`, una proprietà obbligatoria
- [`Address`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Address/) di tipo `string`, un indirizzo leggibile facoltativo
- [`Position`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Position/) di tipo `Position`, che indica dove il pin viene visualizzato sulla mappa
- [`Type`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Type/) di tipo [ `PinType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.PinType/), un'enumerazione, che non viene utilizzata

Il **MapDemos** progetto contiene il file [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), in cui sono elencate musei scienza negli Stati Uniti, e [ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) e [ `Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) classi per la deserializzazione di questi dati.

Il [ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) file e [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) pin di visualizzazione di file di codice per questi musei scienza nella mappa. Quando l'utente tocca un pin, viene visualizzato l'indirizzo e un sito Web per museo.

### <a name="the-distance-between-two-points"></a>La distanza tra due punti

Il [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe contiene un [ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) metodo con un calcolo della distanza tra due posizioni geografiche semplificato.

Viene utilizzato per il [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) file e [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) file code-behind per visualizzare anche la distanza di museo dalla posizione dell'utente:

[![Schermata triplo della pagina musei locale](images/ch28fg28-small.png "distanza a una posizione")](images/ch28fg28-large.png#lightbox "distanza a una posizione")

Il programma viene inoltre illustrato come limitare in modo dinamico il numero di pin in base alla posizione della mappa.

## <a name="geocoding-and-back-again"></a>Geocodifica e quindi di nuovo

Il [ **Xamarin.Forms.Maps** ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) assembly contiene anche un [ `Geocoder` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Geocoder/) classe con un [ `GetPositionsForAddressAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync/p/System.String/) per la conversione un indirizzo di testo in zero o più possibili posizioni geografiche, mentre un altro metodo [ `GetAddressesForPositionAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync/p/Xamarin.Forms.Maps.Position/) che converte in altra direzione.

Il [GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) file e [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) file code-behind illustrare questa funzionalità.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 28 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Esempi di capitolo 28](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Controllo mappa](~/xamarin-forms/user-interface/map.md)
