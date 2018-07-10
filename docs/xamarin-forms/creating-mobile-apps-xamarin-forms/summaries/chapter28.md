---
title: Riepilogo dei capitoli 28. Posizione e mappe
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo dei capitoli 28. Posizione e mappe'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: c10a3c1a0ed2755734fe351df39caadc88dd61c4
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935122"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>Riepilogo dei capitoli 28. Posizione e mappe

Xamarin. Forms supporta un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) elemento che deriva da `View`. A causa dei requisiti della piattaforma speciali coinvolti nell'uso di mappe, vengono implementate in un assembly separato, **verifica**e prevedono uno spazio dei nomi diversi: `Xamarin.Forms.Maps`.

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

### <a name="the-location-tracker-api"></a>L'API di rilevamento delle località

Il [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) soluzione contiene codice per un rilevatore della posizione di API. Il [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs) struttura incapsula una latitudine e longitudine. Il [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs) interfaccia definisce due metodi per avviare e sospendere lo strumento di rilevamento del percorso e un evento quando è disponibile una nuova posizione.

#### <a name="the-ios-location-manager"></a>La gestione di percorsi di iOS

L'implementazione di iOS del `ILocationTracker` è un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs) classe che consente di usare di iOS [ `CLLocationManager` ](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/).

#### <a name="the-android-location-manager"></a>Gestione percorsi di Android

L'implementazione di Android `ILocationTracker` è un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs) classe che utilizza l'Android [ `LocationManager` ](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/) classe.

#### <a name="the-windows-runtime-geo-locator"></a>L'indicatore di posizione geografica di Windows Runtime

L'implementazione di Windows Runtime del `ILocationTracker` è un [ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs) classe che usa la piattaforma UWP [ `Geolocator` ](https://msdn.microsoft.com/library/windows/apps/br225534).

### <a name="display-the-phones-location"></a>Visualizzare la posizione del telefono

Il [ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI) esempio Usa il rilevamento delle località per visualizzare la posizione del telefono, in formato testo sia su una mappa equirettangolare.

### <a name="the-required-overhead"></a>Il sovraccarico richiesto

È necessario per un certo overhead **WhereAmI** usare lo strumento di rilevamento del percorso. Prima, tutti i progetti nel **WhereAmI** soluzioni devono includere riferimenti ai progetti corrispondenti **Xamarin.FormsBook.Platform**e ogni **WhereAmI** progetto è necessario chiamare il `Toolkit.Init` (metodo).

Un overhead aggiuntivo specifico della piattaforma, sotto forma delle autorizzazioni del percorso, è necessario.

#### <a name="location-permission-for-ios"></a>Autorizzazione di posizione per iOS

Per iOS, il **Info. plist** file deve includere gli elementi che contengono il testo di una domanda che chiede all'utente per consentire di recupero della posizione dell'utente.

#### <a name="location-permissions-for-android"></a>Autorizzazioni del percorso per Android

Le applicazioni Android che ottengono la posizione dell'utente devono avere un'autorizzazione ACCESS_FILE_LOCATION nel file androidmanifest. Xml.

#### <a name="location-permissions-for-the-windows-runtime"></a>Autorizzazioni del percorso per il Runtime di Windows

Un'applicazione Windows o Windows Phone deve avere un `location` contrassegnato come funzionalità del dispositivo nel file package. appxmanifest.

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

#### <a name="enabling-windows-runtime-maps"></a>Abilitazione di Windows Runtime viene eseguito il mapping

Un'applicazione di Windows Runtime richiede una chiave di autorizzazione per l'uso di Bing mappe. Questa chiave viene passata come argomento per il `Xamarin.FormsMaps.Init` (metodo). L'applicazione deve anche essere abilitata per servizi di posizione.

### <a name="the-unadorned-map"></a>La mappa non decorata

Il [ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos) esempio è costituito un [MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml) file e [MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs) file code-behind consente lo spostamento a vari programmi di dimostrazione.

Il [BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml) file Mostra come visualizzare i [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) visualizzazione. Per impostazione predefinita vengono visualizzate le città di Roma, ma la mappa può essere modificata dall'utente.

Per disabilitare lo scorrimento orizzontale e verticale, impostare il [ `HasScrollEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasScrollEnabled/) proprietà `false`. Per disabilitare lo zoom avanti, impostare [ `HasZoomEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.HasZoomEnabled/) a `false`. Queste proprietà potrebbero non funzionare in tutte le piattaforme.

### <a name="streets-and-terrain"></a>Streets and terreno

È possibile visualizzare diversi tipi di mappe impostando il `Map` proprietà [ `MapType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.MapType/) di tipo [ `MapType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapType/), un'enumerazione con tre membri:

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street), il valore predefinito
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

Il [MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml) file Mostra come usare un pulsante di opzione per selezionare il tipo di mappa. Si usa il [ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) library e una classe di base il [MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml) file.

### <a name="map-coordinates"></a>Coordinate della mappa

Un programma può ottenere l'area corrente che il `Map` viene visualizzato tramite il [ `VisibleRegion` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.VisibleRegion/) proprietà. Questa proprietà è *non* supportata da una proprietà associabile, e non esiste un meccanismo di notifica per indicare quando è stato modificato, in modo che un programma che si desidera monitorare la proprietà consigliabile utilizzare un timer a tale scopo.

`VisibleRegion` JE typu [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/), una classe con quattro proprietà di sola lettura:

- [`Center`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Center/) di tipo [`Position`](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/)
- [`LatitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LatitudeDegrees/) di tipo `double`, che indica l'altezza dell'area della mappa visualizzata
- [`LongitudeDegrees`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.LongitudeDegrees/) di tipo `double`, che indica la larghezza dell'area della mappa visualizzata
- [`Radius`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.MapSpan.Radius/) typu [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/), che indica la dimensione dell'area circolare del più grande visibile sulla mappa

`Position` e `Distance` sono entrambi strutture. `Position` definisce due proprietà di sola lettura impostata tramite il [ `Position` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Position.Position/p/System.Double/System.Double/):

- [`Latitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Latitude/)
- [`Longitude`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Position.Longitude/)

`Distance` Consente di specificare una distanza di unità indipendenti dal eseguendo la conversione tra unità metriche che anglosassoni. Oggetto `Distance` valore può essere creato in diversi modi:

- [`Distance` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Distance.Distance/p/System.Double/) con una distanza in metri
- [`Distance.FromMeters`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMeters/p/System.Double/) metodo statico
- [`Distance.FromKilometers`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromKilometers/p/System.Double/) metodo statico
- [`Distance.FromMiles`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Distance.FromMiles/p/System.Double/) metodo statico

Il valore è disponibile da tre proprietà:

- [`Meters`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Meters/) di tipo `double`
- [`Kilometers`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Kilometers/) di tipo `double`
- [`Miles`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Distance.Miles/) di tipo `double`

Il [MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml) file contiene numerosi `Label` elementi per la visualizzazione di `MapSpan` informazioni. Il [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs) file code-behind Usa un timer per mantenere le informazioni aggiornate durante la gestione della mappa.

### <a name="position-extensions"></a>Estensioni di posizione

Una nuova libreria per questo libro denominato [ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) contiene tipi specifici della mappa ma indipendente dalla piattaforma. Il [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe dispone di una `ToString` metodo per la `Position`e un metodo per calcolare la distanza tra due `Position` valori.

### <a name="setting-an-initial-location"></a>L'impostazione di una posizione iniziale

È possibile chiamare il [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion/p/Xamarin.Forms.Maps.MapSpan/) metodo `Map` a livello di codice impostare un livello di posizione e lo zoom sulla mappa. L'argomento è di tipo `MapSpan`. È possibile creare un `MapSpan` utilizzando uno dei modi seguenti:

- [`MapSpan` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.MapSpan.MapSpan/p/Xamarin.Forms.Maps.Position/System.Double/System.Double/) con un `Position`e l'intervallo di latitudine e longitudine
- [`MapSpan.FromCenterAndRadius`](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius/p/Xamarin.Forms.Maps.Position/Xamarin.Forms.Maps.Distance/) con un `Position` e radius

È anche possibile creare una nuova `MapSpan` da una esistente usando i metodi [ `ClampLatitude` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.ClampLatitude/p/System.Double/System.Double/) oppure [ `WithZoom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.MapSpan.WithZoom/p/System.Double/).

Il [WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml) file e [WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs) file code-behind viene illustrato come utilizzare il `MoveToRegion` per visualizzare la stato del Wyoming (metodo).

In alternativa, è possibile usare la [ `Map` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Maps.Map.Map/p/Xamarin.Forms.Maps.MapSpan/) con un `MapSpan` oggetto per inizializzare la posizione della mappa. Il [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml) file Mostra come eseguire questa operazione interamente in XAML per visualizzare sede centrale di Xamarin a San Francisco.

### <a name="dynamic-zooming"></a>Lo zoom dinamico

È possibile usare un `Slider` per ingrandire in modo dinamico una mappa. Il [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml) file e [RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs) file code-behind Mostra come modificare il raggio di una mappa di base di `Slider` valore.

Il [LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml) file e [LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs) file code-behind mostrano un approccio alternativo che funziona meglio in Android, ma nessuno dei due approcci funziona perfettamente in di Windows piattaforme.

### <a name="the-phones-location"></a>Posizione del telefono

Il [ `IsShowingUser` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.IsShowingUser/) proprietà della `Map` leggermente differente sulle tre piattaforme come le [ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml) file illustra:

- In iOS, un punto blu indica la posizione del telefono, ma è necessario passare manualmente non esiste
- In Android, viene visualizzata un'icona che, quando il push si sposta la mappa alla posizione del telefono
- La piattaforma UWP è simile ai dispositivi iOS, ma in alcuni casi automaticamente consente di passare al percorso

Il **MapDemos** progetto tenta di simulare l'approccio Android con la prima definizione di un pulsante basata sull'icona in base il [MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml) file e [MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs) file code-behind.

Il [GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml) file e [GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs) file code-behind usare questo pulsante per passare al percorso del telefono.

### <a name="pins-and-science-museums"></a>I pin e musei science

Infine, il `Map` classe definisce un [ `Pins` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Map.Pins/) vlastnosti typu `IList<Pin>`. Il [ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/) classe definisce quattro proprietà:

- [`Label`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Label/) di tipo `string`, una proprietà obbligatoria
- [`Address`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Address/) di tipo `string`, un indirizzo leggibile facoltativo
- [`Position`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Position/) di tipo `Position`, che indica dove il pin viene visualizzato sulla mappa
- [`Type`](https://developer.xamarin.com/api/property/Xamarin.Forms.Maps.Pin.Type/) typu [ `PinType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.PinType/), un'enumerazione, che non viene utilizzata

Il **MapDemos** progetto contiene il file [ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml), che elenca musei science negli Stati Uniti, e [ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs) e [ `Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs) classi per la deserializzazione di questi dati.

Il [ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml) file e [ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs) pin di visualizzazione di file code-behind per questi musei science nella mappa. Quando l'utente tocca un pin, viene visualizzato l'indirizzo e un sito Web per il museo.

### <a name="the-distance-between-two-points"></a>La distanza tra due punti

Il [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs) classe contiene una [ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88) metodo con un calcolo semplificato della distanza tra due aree geografiche.

Viene utilizzato per il [LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml) file e [LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs) file code-behind per visualizzare anche la distanza per museo dalla posizione dell'utente:

[![Tripla screenshot della pagina locale di musei](images/ch28fg28-small.png "distanza in una posizione")](images/ch28fg28-large.png#lightbox "distanza in un percorso")

Il programma viene inoltre illustrato come limitare in modo dinamico il numero di pin in base alla posizione della mappa.

## <a name="geocoding-and-back-again"></a>Geocodifica e uscire

Il [ **verifica** ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) assembly contiene anche una [ `Geocoder` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Geocoder/) classe con un [ `GetPositionsForAddressAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync/p/System.String/) metodo che esegue la conversione un indirizzo di testo in zero o più posizioni geografiche e un altro metodo [ `GetAddressesForPositionAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync/p/Xamarin.Forms.Maps.Position/) che converte in altra direzione.

Il [GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml) file e [GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs) file code-behind illustrare questa funzionalità.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 28 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [Capitolo 28 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Controllo mappa](~/xamarin-forms/user-interface/map.md)
