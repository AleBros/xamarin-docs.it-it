---
title: Riepilogo dei capitoli 28. Posizione e mappe
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: Riepilogo dei capitoli 28. Posizione e mappe'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 0abd7e6cb5b8b9650a3dc324338587ff59a80a19
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331456"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Riepilogo dei capitoli 28. Posizione e mappe

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

Xamarin. Forms supporta un [ `Map` ](xref:Xamarin.Forms.Maps.Map) elemento che deriva da `View`. A causa dei requisiti della piattaforma speciali coinvolti nell'uso di mappe, vengono implementate in un assembly separato, **verifica**e prevedono uno spazio dei nomi diversi: `Xamarin.Forms.Maps`.

## <a name="the-geographic-coordinate-system"></a>Il sistema di coordinate geografico

Un sistema di coordinate geografico identifica le posizioni in un oggetto sferico (o quasi sferica), ad esempio la terra. Una coordinata è costituito da entrambi una *latitudine* e *longitudine* espressa in angoli.

Un grande-cerchio chiamato il `equator` trova a metà tra due poli attraverso il quale si estende a livello concettuale dell'asse della terra.

### <a name="parallels-and-latitude"></a>Parallels e la latitudine

Angolo espresso Nord o sud dell'equatore dall'area dei segni di terra linee della latitudine uguale detta *parallels*. Queste vanno da 0 gradi all'equatore a 90 gradi a nord e Sud poli. Per convenzione, latitudini a nord dell'equatore sono i valori positivi e quelli congiunge equatore sono valori negativi.

### <a name="longitude-and-meridians"></a>Indicata con longitudine e meridiani

Metà dei cerchi great dal polo nord al polo sud sono noti anche come righe di longitudine uguale *meridiani*. Questi sono relativi alla meridiano principale nell'ora di Greenwich, Inghilterra. Per convenzione, longitudini est meridiano principale sono i valori positivi da 0 gradi a 180 gradi e longitudini a meridiano principale sono i valori negativi da 0 gradi a &ndash;180 gradi.

### <a name="the-equirectangular-projection"></a>La proiezione equirettangolare

Qualsiasi mappa della terra piatta introduce le distorsioni. Se tutte le righe di latitudine e longitudine sono rette e se le differenze uguali in latitudine e longitudine angoli corrispondono a equidistanti sulla mappa, il risultato è un' *proiezione equirettangolare*. Questa mappa distorce aree più vicini ai poli perché vengono adattate orizzontalmente.

### <a name="the-mercator-projection"></a>La proiezione Mercator

I più diffusi *proiezione Mercator* tenta di compensare l'allungamento orizzontale estendendo anche verticalmente queste aree. Ciò comporta una mappa in cui aree vicino poli sono presenti più ampio di quanto sono in realtà, ma qualsiasi rete locale piuttosto strettamente conforme con l'area effettivo.

### <a name="map-services-and-tiles"></a>Mapping dei servizi e i riquadri

Mapping dei servizi usano una variazione della proiezione Mercator chiamata `Web Mercator`. I servizi di mappe recapitare le tessere mappa di bit a un client in base a livello di zoom e percorso.

## <a name="getting-the-users-location"></a>Recupero della posizione dell'utente

Xamarin. Forms `Map` classi non includono una funzionalità per ottenere la posizione geografica dell'utente, ma ciò è spesso opportuno quando si utilizzano le mappe, quindi, un servizio di dipendenza deve gestire lo.

> [!NOTE]
> Le applicazioni xamarin. Forms possono invece usare la [ `Geolocation` ](~/essentials/geolocation.md) classe incluso in Xamarin.Essentials.

### <a name="the-location-tracker-api"></a>L'API di rilevamento delle località

Il [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) soluzione contiene codice per un rilevatore della posizione di API. Il [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) struttura incapsula una latitudine e longitudine. Il [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interfaccia definisce due metodi per avviare e sospendere lo strumento di rilevamento del percorso e un evento quando è disponibile una nuova posizione.

#### <a name="the-ios-location-manager"></a>La gestione di percorsi di iOS

L'implementazione di iOS del `ILocationTracker` è un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) classe che consente di usare di iOS [ `CLLocationManager` ](xref:CoreLocation.CLLocationManager).

#### <a name="the-android-location-manager"></a>Gestione percorsi di Android

L'implementazione di Android `ILocationTracker` è un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) classe che utilizza l'Android [ `LocationManager` ](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/) classe.

#### <a name="the-uwp-geo-locator"></a>L'indicatore di posizione geografica UWP

L'implementazione (Universal Windows Platform) di `ILocationTracker` è un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) classe che usa la piattaforma UWP [ `Geolocator` ](/uwp/api/Windows.Devices.Geolocation.Geolocator).

### <a name="display-the-phones-location"></a>Visualizzare la posizione del telefono

Il [ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) esempio Usa il rilevamento delle località per visualizzare la posizione del telefono, in formato testo sia su una mappa equirettangolare.

### <a name="the-required-overhead"></a>Il sovraccarico richiesto

È necessario per un certo overhead **WhereAmI** usare lo strumento di rilevamento del percorso. Prima, tutti i progetti nel **WhereAmI** soluzioni devono includere riferimenti ai progetti corrispondenti **Xamarin.FormsBook.Platform**e ogni **WhereAmI** progetto è necessario chiamare il `Toolkit.Init` (metodo).

Un overhead aggiuntivo specifico della piattaforma, sotto forma delle autorizzazioni del percorso, è necessario.

#### <a name="location-permission-for-ios"></a>Autorizzazione di posizione per iOS

Per iOS, il **Info. plist** file deve includere gli elementi che contengono il testo di una domanda che chiede all'utente per consentire di recupero della posizione dell'utente.

#### <a name="location-permissions-for-android"></a>Autorizzazioni del percorso per Android

Le applicazioni Android che ottengono la posizione dell'utente devono avere un'autorizzazione ACCESS_FILE_LOCATION nel file androidmanifest. Xml.

#### <a name="location-permissions-for-the-uwp"></a>Autorizzazioni del percorso per la piattaforma UWP

Un'applicazione (Universal Windows Platform) deve avere un `location` contrassegnato come funzionalità del dispositivo nel file package. appxmanifest.

## <a name="working-with-xamarinformsmaps"></a>Utilizzo di verifica

Diversi requisiti coinvolti nell'uso di `Map` classe.

### <a name="the-nuget-package"></a>Il pacchetto NuGet

Il **verifica** libreria NuGet deve essere aggiunto alla soluzione dell'applicazione. Il numero di versione deve essere lo stesso come il **xamarin. Forms** pacchetti attualmente installati.

### <a name="initializing-the-maps-package"></a>L'inizializzazione del pacchetto di mappe

I progetti dell'applicazione devono chiamare il `Xamarin.FormsMaps.Init` dopo aver apportato una chiamata al metodo `Xamarin.Forms.Forms.Init`.

### <a name="enabling-map-services"></a>Abilitazione mapping dei servizi

Poiché il `Map` può ottenere la posizione dell'utente, l'applicazione deve ottenere l'autorizzazione per l'utente, come descritto in precedenza in questo capitolo:

#### <a name="enabling-ios-maps"></a>Esegue il mapping di attivazione di iOS

Un'applicazione iOS usando `Map` richiede due righe nel file Info. plist.

#### <a name="enabling-android-maps"></a>Abilitazione di Android viene eseguito il mapping

Una chiave di autorizzazione è necessaria per l'uso di servizi di Google Map. Questa chiave viene inserita nel **androidmanifest. XML** file. Inoltre, il **androidmanifest. XML** richiede file `manifest` tag coinvolti nell'ottenere la posizione dell'utente.

#### <a name="enabling-uwp-maps"></a>Esegue il mapping di abilitazione della piattaforma UWP

Un'applicazione (Universal Windows Platform) richiede una chiave di autorizzazione per l'uso di Bing mappe. Questa chiave viene passata come argomento per il `Xamarin.FormsMaps.Init` (metodo). L'applicazione deve anche essere abilitata per servizi di posizione.

### <a name="the-unadorned-map"></a>La mappa non decorata

Il [ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) esempio è costituito un [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) file e [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) file code-behind consente lo spostamento a vari programmi di dimostrazione.

Il [BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) file Mostra come visualizzare i [ `Map` ](xref:Xamarin.Forms.Maps.Map) visualizzazione. Per impostazione predefinita vengono visualizzate le città di Roma, ma la mappa può essere modificata dall'utente.

Per disabilitare lo scorrimento orizzontale e verticale, impostare il [ `HasScrollEnabled` ](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled) proprietà `false`. Per disabilitare lo zoom avanti, impostare [ `HasZoomEnabled` ](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled) a `false`. Queste proprietà potrebbero non funzionare in tutte le piattaforme.

### <a name="streets-and-terrain"></a>Streets and terreno

È possibile visualizzare diversi tipi di mappe impostando il `Map` proprietà [ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType) di tipo [ `MapType` ](xref:Xamarin.Forms.Maps.MapType), un'enumerazione con tre membri:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), il valore predefinito
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

Il [MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) file Mostra come usare un pulsante di opzione per selezionare il tipo di mappa. Si usa il [ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) library e una classe di base il [MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) file.

### <a name="map-coordinates"></a>Coordinate della mappa

Un programma può ottenere l'area corrente che il `Map` viene visualizzato tramite il [ `VisibleRegion` ](xref:Xamarin.Forms.Maps.Map.VisibleRegion) proprietà. Questa proprietà è *non* supportata da una proprietà associabile, e non esiste un meccanismo di notifica per indicare quando è stato modificato, in modo che un programma che si desidera monitorare la proprietà consigliabile utilizzare un timer a tale scopo.

`VisibleRegion` JE typu [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan), una classe con quattro proprietà di sola lettura:

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center) di tipo [`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees) di tipo `double`, che indica l'altezza dell'area della mappa visualizzata
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees) di tipo `double`, che indica la larghezza dell'area della mappa visualizzata
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius) typu [ `Distance` ](xref:Xamarin.Forms.Maps.Distance), che indica la dimensione dell'area circolare del più grande visibile sulla mappa

`Position` e `Distance` sono entrambi strutture. `Position` definisce due proprietà di sola lettura impostata tramite il [ `Position` costruttore](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance` Consente di specificare una distanza di unità indipendenti dal eseguendo la conversione tra unità metriche che anglosassoni. Oggetto `Distance` valore può essere creato in diversi modi:

- [`Distance` costruttore](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double)) con una distanza in metri
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double)) metodo statico
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double)) metodo statico
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double)) metodo statico

Il valore è disponibile da tre proprietà:

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters) di tipo `double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers) di tipo `double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles) di tipo `double`

Il [MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) file contiene numerosi `Label` elementi per la visualizzazione di `MapSpan` informazioni. Il [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) file code-behind Usa un timer per mantenere le informazioni aggiornate durante la gestione della mappa.

### <a name="position-extensions"></a>Estensioni di posizione

Una nuova libreria per questo libro denominato [ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contiene tipi specifici della mappa ma indipendente dalla piattaforma. Il [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe dispone di una `ToString` metodo per la `Position`e un metodo per calcolare la distanza tra due `Position` valori.

### <a name="setting-an-initial-location"></a>L'impostazione di una posizione iniziale

È possibile chiamare il [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)) metodo `Map` a livello di codice impostare un livello di posizione e lo zoom sulla mappa. L'argomento è di tipo `MapSpan`. È possibile creare un `MapSpan` utilizzando uno dei modi seguenti:

- [`MapSpan` costruttore](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double)) con un `Position`e l'intervallo di latitudine e longitudine
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance)) con un `Position` e radius

È anche possibile creare una nuova `MapSpan` da una esistente usando i metodi [ `ClampLatitude` ](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double)) oppure [ `WithZoom` ](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double)).

Il [WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) file e [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) file code-behind viene illustrato come utilizzare il `MoveToRegion` per visualizzare la stato del Wyoming (metodo).

In alternativa, è possibile usare la [ `Map` costruttore](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan)) con un `MapSpan` oggetto per inizializzare la posizione della mappa. Il [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) file Mostra come eseguire questa operazione interamente in XAML per visualizzare sede centrale di Xamarin a San Francisco.

### <a name="dynamic-zooming"></a>Lo zoom dinamico

È possibile usare un `Slider` per ingrandire in modo dinamico una mappa. Il [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) file e [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) file code-behind Mostra come modificare il raggio di una mappa di base di `Slider` valore.

Il [LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) file e [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) file code-behind mostrano un approccio alternativo che funziona meglio in Android, ma nessuno dei due approcci funziona perfettamente in di Windows piattaforme.

### <a name="the-phones-location"></a>Posizione del telefono

Il [ `IsShowingUser` ](xref:Xamarin.Forms.Maps.Map.IsShowingUser) proprietà della `Map` leggermente differente in ciascuna piattaforma come il [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) file illustra:

- In iOS, un punto blu indica la posizione del telefono, ma è necessario passare manualmente non esiste
- In Android, viene visualizzata un'icona che, quando il push si sposta la mappa alla posizione del telefono
- La piattaforma UWP è simile ai dispositivi iOS, ma in alcuni casi automaticamente consente di passare al percorso

Il **MapDemos** progetto tenta di simulare l'approccio Android con la prima definizione di un pulsante basata sull'icona in base il [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) file e [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) file code-behind.

Il [GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) file e [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) file code-behind usare questo pulsante per passare al percorso del telefono.

### <a name="pins-and-science-museums"></a>I pin e musei science

Infine, il `Map` classe definisce un [ `Pins` ](xref:Xamarin.Forms.Maps.Map.Pins) vlastnosti typu `IList<Pin>`. Il [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) classe definisce quattro proprietà:

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label) di tipo `string`, una proprietà obbligatoria
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address) di tipo `string`, un indirizzo leggibile facoltativo
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) di tipo `Position`, che indica dove il pin viene visualizzato sulla mappa
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) typu [ `PinType` ](xref:Xamarin.Forms.Maps.PinType), un'enumerazione, che non viene utilizzata

Il **MapDemos** progetto contiene il file [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), che elenca musei science negli Stati Uniti, e [ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) e [ `Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) classi per la deserializzazione di questi dati.

Il [ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) file e [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) pin di visualizzazione di file code-behind per questi musei science nella mappa. Quando l'utente tocca un pin, viene visualizzato l'indirizzo e un sito Web per il museo.

### <a name="the-distance-between-two-points"></a>La distanza tra due punti

Il [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe contiene una [ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) metodo con un calcolo semplificato della distanza tra due aree geografiche.

Viene utilizzato per il [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) file e [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) file code-behind per visualizzare anche la distanza per museo dalla posizione dell'utente:

[![Tripla screenshot della pagina locale di musei](images/ch28fg28-small.png "distanza in una posizione")](images/ch28fg28-large.png#lightbox "distanza in un percorso")

Il programma viene inoltre illustrato come limitare in modo dinamico il numero di pin in base alla posizione della mappa.

## <a name="geocoding-and-back-again"></a>Geocodifica e uscire

Il [ **verifica** ](xref:Xamarin.Forms.Maps) assembly contiene anche una [ `Geocoder` ](xref:Xamarin.Forms.Maps.Geocoder) classe con un [ `GetPositionsForAddressAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String)) metodo che esegue la conversione un indirizzo di testo in zero o più posizioni geografiche e un altro metodo [ `GetAddressesForPositionAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position)) che converte in altra direzione.

Il [GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) file e [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) file code-behind illustrare questa funzionalità.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 28 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Capitolo 28 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Mappa di xamarin. Forms](~/xamarin-forms/user-interface/map.md)
