---
title: API di mappe
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: fc16178a4068b2dcf22fc19047e0ef403e83633f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30773524"
---
# <a name="maps-api"></a>API di mappe

Tramite l'applicazione esegue il mapping è molto utile, ma talvolta si desidera includere mappe direttamente nell'applicazione. Oltre a predefinito esegue il mapping dell'applicazione, Google offre inoltre un [il mapping API native per Android](https://developers.google.com/maps/documentation/android/).
L'API di mappe è appropriata nei casi in cui si desidera esercitare un maggiore controllo dell'esperienza di mapping. Operazioni possibili con l'API di mappe includono:

-  La modifica a livello di programmazione del punto di vista della mappa.
-  Aggiunta e personalizzazione di marcatori.
-  Annotazione di una mappa con sovrapposizioni.

A differenza di v1 API Android di Google Maps attualmente deprecato, API Android di Google Maps v2 fa parte di [Google Play Services](http://developer.android.com/google/play-services/index.html).
Pertanto, è necessario soddisfare alcuni prerequisiti obbligatori prima che sia possibile utilizzare l'API Android di Google Maps in un'applicazione di xamarin.


## <a name="google-maps-api-prerequisites"></a>Esegue il mapping di Google API prerequisiti

Alcuni elementi devono essere configurate prima di poter usare l'API di mappe, tra cui:

-  Installare il SDK di servizi Google Play
-  Creare un emulatore con Google APIs
-  Ottenere una chiave API di mappe
-  Specificare le autorizzazioni necessarie



### <a name="install-the-google-play-services-sdk"></a>Installare il SDK di servizi Google Play

Google Play Services è una tecnologia da Google che consente alle applicazioni Android di sfruttare i vantaggi di varie funzionalità di Google, ad esempio Google + In App di fatturazione e mappe. Queste funzionalità sono accessibili nei dispositivi Android come servizi in background che sono contenuti nel [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Le applicazioni Android interagiscono con Google Play Services tramite la libreria client di Google Play Services. Questa raccolta contiene le interfacce e classi per i singoli servizi, ad esempio mappe. Il diagramma seguente mostra la relazione tra un'applicazione Android e Google Play Services:

![Diagramma che illustra l'aggiornamento di Google Play Services APK Google Play Store](maps-api-images/play-services-diagram.png)

L'API di mappe Android viene fornito come parte di Google Play Services.
Prima di un'applicazione di xamarin è possibile utilizzare l'API di mappe, Google Play Services SDK deve essere installato e associato. Google Play Services SDK viene installato mediante Android SDK Manager. Nella schermata seguente viene illustrato dove in Android SDK Manager è reperibile il client di servizi Google Play:

![Google Play Services viene visualizzato sotto extra in Android SDK Manager](maps-api-images/image01.png)

> [!NOTE]
> I servizi Google Play APK è un prodotto con licenza che non può essere presente in tutti i dispositivi. Se non è installato, quindi esegue il mapping di Google non funzionerà nel dispositivo.


#### <a name="binding-google-play-services"></a>Associazione Google Play Services

Una volta installata, la libreria client di Google Play Services deve essere associata in una libreria di binding xamarin Java. Esistono due modi per eseguire questa operazione:

-  **Utilizzare il pacchetto NuGet mappe di Google Play Services** -si tratta dell'approccio più semplice (disponibile solo in xamarin 4.8 o versione successiva).
   Installare il [NuGet Xamarin Google Play Services mappe](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps); verrà installato anche eventuali pacchetti dipendenza Google Play Services.
   Il resto di questa guida è incentrata su questo approccio.

-  **Associare manualmente la libreria client di Google Play Services** -si tratta di un approccio più complesso ed è l'unico modo per xamarin 4.4 o xamarin 4.6 per associare il SDK dei servizi Google Play.
   Associazione manualmente la libreria client di Google Play Services esula dall'ambito di questo documento, ma sono disponibili in un esempio di come eseguire questa operazione il [mappe e posizione Demo esempio v3](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3) su Github.


#### <a name="adding-the-google-play-services-map-package"></a>Aggiunta del pacchetto di mappa di Google Play Services

Per aggiungere il pacchetto di Google Play Services mappa, fare clic destro la **riferimenti** cartella del progetto in Esplora soluzioni e fare clic su **Gestisci pacchetti NuGet...** :

![Visualizzazione di Esplora soluzioni di voce di menu Gestisci pacchetti NuGet contesto in riferimenti](maps-api-images/image02.png)

Verrà visualizzata la **Gestione pacchetti NuGet**. Fare clic su **Sfoglia** e immettere **Xamarin Google Play Services mappe** nel campo di ricerca. Selezionare **Xamarin.GooglePlayServices.Maps** e fare clic su **installare**. (Se il pacchetto è stato installato in precedenza, fare clic su **aggiornamento**.):

[![Gestione pacchetti NuGet con Xamarin.GooglePlayServices.Maps pacchetto selezionato](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Si noti che i seguenti pacchetti di dipendenza siano anche installati:

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**



### <a name="create-an-emulator-with-google-apis"></a>Creare un emulatore con le API di Google

Sebbene non consigliabile, è possibile configurare l'emulatore per supportare l'API di mappe Android. L'emulatore deve essere configurato per il 17 di livello API Google (Android 4.2.2) di destinazione o versione successiva. Nella schermata seguente, un'immagine dell'emulatore è configurata per l'API livello 19: 

![Android Emulator Manager con un AVD configurato per l'API livello 19](maps-api-images/image04.png)



### <a name="obtain-a-google-maps-api-key"></a>Ottenere una chiave API di mappe Google

Il passaggio finale consiste per ottenere una chiave API di mappe di Google (si noti che non è possibile riutilizzare una chiave API da legacy v1 Google Maps). Per informazioni su come ottenere e usare la chiave API con xamarin, vedere [ottenere A Google Maps chiave API](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
 


### <a name="specify-the-required-permissions"></a>Specificare le autorizzazioni necessarie

Specificare le seguenti autorizzazioni nel **AndroidManifest.XML** per l'API Android di Google mappe:

-  **Accesso allo stato della rete** &ndash; l'API di mappe deve essere in grado di controllare se è possibile scaricare i riquadri mappa.

-  **Accesso a Internet** &ndash; accesso a Internet è necessario scaricare i riquadri mappa e comunicare con i server Google Play per l'accesso all'API.

-  **OpenGL ES v2** &ndash; l'applicazione deve dichiarare il requisito per OpenGL ES v2.

-  **Chiave API di Google Maps** &ndash; l'API usata per confermare che l'applicazione viene registrata e autorizzato a usare Google Play Services. Vedere [ottenere una chiave API di mappe Google](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) per informazioni dettagliate su questa chiave.

-  **Scrivere in un archivio esterno** &ndash; l'API Android di Google mappe vengono memorizzati nella cache scaricato i riquadri in un archivio esterno.

-  **Accesso ai servizi basati sul Google Web** &ndash; l'applicazione necessita di autorizzazioni per accedere ai servizi web di Google che supportano l'API di mappe Android.

-  **Le autorizzazioni per Google Play Services notifiche** &ndash; l'applicazione deve essere concessa l'autorizzazione per ricevere notifiche remote da Google Play Services.

-  **Accesso ai provider di percorso** &ndash; queste sono autorizzazioni facoltative.
   Consentirà la `GoogleMap` classe per visualizzare la posizione del dispositivo nella mappa.


Nel frammento seguente è riportato un esempio delle impostazioni che devono essere aggiunti a **AndroidManifest.XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="14" android:targetSdkVersion="17" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

    <!-- We need to be able to download map tiles and access Google Play Services-->
    <uses-permission android:name="android.permission.INTERNET" />

    <!-- Allow the application to access Google web-based services. -->
    <uses-permission android:name="com.google.android.providers.gsf.permission.READ_GSERVICES" />

    <!-- Google Maps for Android v2 will cache map tiles on external storage -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

    <!-- Google Maps for Android v2 needs this permission so that it may check the connection state as it must download data -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

    <!-- Permission to receive remote notifications from Google Play Services -->
    <!-- Notice here that we have the package name of our application as a prefix on the permissions. -->
    <uses-permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" />
    <permission android:name="<PACKAGE NAME>.permission.MAPS_RECEIVE" android:protectionLevel="signature" />

    <!-- These are optional, but recommended. They will allow Maps to use the My Location provider. -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />


    <application android:label="@string/app_name">
        <!-- Put your Google Maps V2 API Key here. -->
        <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
        <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
    </application>
</manifest>
```


## <a name="the-googlemap-class"></a>La classe GoogleMap

Una volta che vengono risolti i prerequisiti, è necessario iniziare a sviluppare l'applicazione e usare l'API Android mappe. Il [GoogleMap](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap) classe è l'API principale che un'applicazione di xamarin utilizzerà per visualizzare e interagire con un mapping di Google per Android. Questa classe ha le responsabilità seguenti:

-  L'interazione con i servizi Google Play per autorizzare l'applicazione con il servizio web di Google.

-  Il download, la memorizzazione nella cache e visualizzare i riquadri mappa.

-  La visualizzazione controlli dell'interfaccia utente, ad esempio, panoramica e zoom all'utente.

-  Disegno di forme geometriche e marcatori nelle mappe.

Il `GoogleMap` viene aggiunto a un'attività in uno dei due modi:

-  **MapFragment** - [MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html) è riportato un frammento specializzato che funge da host per il `GoogleMap` oggetto. Il `MapFragment` richiede il livello API Android 12 o versione successiva.
   Le versioni precedenti di Android è possono utilizzare il [SupportMapFragment](http://developer.android.com/reference/com/google/android/gms/maps/SupportMapFragment.html).

-  **MapView** - [MapView](https://developer.xamarin.com/api/type/Android.GoogleMaps.MapView/) è una sottoclasse specializzata di visualizzazione che può fungere da host per un `GoogleMap` oggetto. Gli utenti di questa classe devono inoltrare tutti i metodi del ciclo di vita di attività per la `MapView` classe.

Ognuno di questi contenitori espongono un `Map` proprietà che restituisce un'istanza di `GoogleMap`. Preferenza dovrebbe essere concessa al [MapFragment](http://developer.android.com/reference/com/google/android/gms/maps/MapFragment.html) classe poiché è una semplice API che consente di ridurre il codice di boilerplate quantità che uno sviluppatore deve implementare manualmente.


### <a name="adding-a-mapfragment-to-an-activity"></a>Aggiunta di un MapFragment a un'attività

Nella schermata riportata di seguito è riportato un esempio di un semplice `MapFragment`:

[![Schermata di un dispositivo di visualizzazione di un frammento di mapping](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Analogamente alle altre classi di frammento, esistono due modi per aggiungere questo `MapFragment` a un'attività:

-   **In modo dichiarativo** - `MapFragment` può essere aggiunto tramite il file XML di layout per l'attività. Il frammento XML seguente viene illustrato un esempio di come utilizzare il `fragment` elemento:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **A livello di programmazione** - `MapFragment` possono essere aggiunti a livello di programmazione come descritto di seguito.

Per aggiungere a livello di codice un `MapFragment`, deve implementare l'attività di `IOnMapReadyCallback` interfaccia. Poiché l'inizializzazione di un `GoogleMap` oggetto può richiedere del tempo per completare l'API comunica con Google Play, è necessario fornire un callback di notifica dell'app quando il `GoogleMap` è pronto.

Aggiungere innanzitutto `IOnMapReadyCallback` per il `Activity` dichiarazione di classe.
Ad esempio:

```csharp
public class MapWithMarkersActivity : Activity, IOnMapReadyCallback
```

Successivamente, nel `OnCreate` metodo, aggiungere il `MapFragment` come illustrato nell'esempio di codice seguente (il `GoogleMapOptions` classe viene descritta più avanti in questa Guida):

```csharp
_mapFragment = FragmentManager.FindFragmentByTag("map") as MapFragment;
if (_mapFragment == null)
{
    GoogleMapOptions mapOptions = new GoogleMapOptions()
        .InvokeMapType(GoogleMap.MapTypeSatellite)
        .InvokeZoomControlsEnabled(false)
        .InvokeCompassEnabled(true);

    FragmentTransaction fragTx = FragmentManager.BeginTransaction();
    _mapFragment = MapFragment.NewInstance(mapOptions);
    fragTx.Add(Resource.Id.map, _mapFragment, "map");
    fragTx.Commit();
}
_mapFragment.GetMapAsync(this);
```

Oggetto `GoogleMap` deve essere acquisito tramite `GetMapAsync`, come illustrato alla fine dell'esempio di codice precedente &ndash; inizializza automaticamente il sistema esegue il mapping e la visualizzazione. (Si noti che questo metodo non usa `await` / `async` semantica &ndash; il `Async` comportamento viene implementato da Android.) Quando il `GoogleMap` oggetto è pronto, le chiamate di Android dell'app `OnMapReady` (metodo) (che è necessario implementare come parte del `IOnMapReadyCallback` interface). Ad esempio:

```csharp
public void OnMapReady (GoogleMap map)
{
    _map = map;
}
```

Nell'esempio di codice precedente, il `OnMapReady` callback Inizializza il `_map` variabile creato `GoogleMap` oggetto.

Come esempio di come utilizzare questo risultato, quando `OnResume` viene chiamato, è possibile verificare se `_map` è diverso da null. Se `_map` è impostata su un `GoogleMap` oggetto `OnResume` può chiamare i metodi per aggiungere marcatori e spostare la fotocamera in un specificato longitudine e latitudine. Per un esempio di codice completo, vedere [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo).



### <a name="map-types"></a>Eseguire il mapping di tipi

Sono disponibili cinque diversi tipi di mappe dall'API di mappe di Google:

-  **Normale** -questo è il tipo di mappa predefinito. Mostra strade e importante funzionalità naturale con alcuni punti di interesse (ad esempio edifici e Bridge) sintetiche.

-  **Satellite** -questa mappa Mostra la fotografia satellite.

-  **Ibrida** - questa mappa Mostra la fotografia satellite e road esegue il mapping.

-  **Terrain** -Mostra principalmente le funzionalità topografici con alcuni strade.

-  **Nessuna** -questa mappa non viene caricato qualsiasi riquadri, ne viene eseguito il rendering come una griglia vuota.


L'immagine seguente mostra tre dei diversi tipi delle mappe, da sinistra a destra (normale, soluzione ibrida, terrain):

[![Tre mapping schermate di esempio: normale, ibridi e terreno](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

Il `GoogleMap.MapType` proprietà viene utilizzata per impostare o modificare il tipo di mappa viene visualizzato. Frammento di codice seguente viene illustrato come visualizzare una mappa satellite.

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MapType = GoogleMap.MapTypeSatellite;
}
```


### <a name="googlemap-properties"></a>Proprietà GoogleMap

`GoogleMap` definisce diverse proprietà che è possibile controllare le funzionalità e l'aspetto della mappa. Un modo per configurare lo stato iniziale di un `GoogleMap` consiste nel passare una [GoogleMapOptions](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMapOptions.html) oggetto durante la creazione di un `MapFragment`. Frammento di codice seguente è un esempio dell'utilizzo di un `GoogleMapOptions` oggetto durante la creazione di un `MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
_mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, _mapFragment, "map");
fragTx.Commit();
```

L'altro modo per configurare un `GoogleMap` oggetto consiste nell'impostare i valori di [UiSettings](http://developer.android.com/reference/com/google/android/gms/maps/UiSettings.html) proprietà dell'oggetto mappa. Nell'esempio di codice successivo viene illustrato come configurare un `GoogleMap` per visualizzare i controlli zoom e una bussola:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.UiSettings.ZoomControlsEnabled = true;
    _map.UiSettings.CompassEnabled = true;
}
```


## <a name="interacting-with-the-map"></a>L'interazione con la mappa

L'API di mappe Android fornisce API che consentono a un'attività modificare il punto di vista, aggiungere indicatori, posizionare le sovrapposizioni personalizzate o disegnare forme geometriche. In questa sezione verrà illustrate le procedure eseguire alcune di queste attività in xamarin.

### <a name="changing-the-viewpoint"></a>Modifica del punto di vista

Le mappe sono modellate come un piano sullo schermo, in base alla proiezione Mercator. La vista mappa è quello di un *fotocamera* professionale direttamente verso il basso in questo piano. È possibile controllare la posizione della camera modificando il percorso, lo zoom, inclinazione e rilevante. Il [CameraUpdate](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdate) classe viene utilizzata per spostare la posizione della fotocamera. `CameraUpdate` gli oggetti non vengono creata un'istanza direttamente, l'API di mappe fornisce invece la [CameraUpdateFactory](http://developer.android.com/reference/com/google/android/gms/maps/CameraUpdateFactory.html) classe.

Una volta un `CameraUpdate` oggetto è stato creato, viene passato come parametro a uno di [GoogleMap.MoveCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#moveCamera(com.google.maps.CameraUpdate)) o [GoogleMap.AnimateCamera](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.html#animateCamera(com.google.maps.CameraUpdate)) metodi. Il `MoveCamera` metodo aggiorna la mappa immediatamente durante il `AnimateCamera` metodo fornisce una transizione uniforme animata.

Questo frammento di codice è un semplice esempio di come utilizzare il `CameraUpdateFactory` per creare un `CameraUpdate` che incrementa il livello di zoom della mappa da uno:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

L'API di mappe fornisce un [CameraPosition](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) che consente di aggregare tutti i valori possibili per la posizione della camera. Un'istanza di questa classe può essere fornita per il [CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition(com.google.android.gms.maps.model.CameraPosition)) metodo che restituirà un `CameraUpdate` oggetto. Include anche l'API di mappe di [CameraPosition.Builder](http://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) classe che fornisce un'API intuitiva per la creazione di `CameraPosition` oggetti.
Frammento di codice seguente viene illustrato un esempio di creazione di un `CameraUpdate` da un `CameraPosition` e utilizzarlo per modificare la posizione della fotocamera su un `GoogleMap`:

```csharp
LatLng location = new LatLng(50.897778, 3.013333);
CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
builder.Target(location);
builder.Zoom(18);
builder.Bearing(155);
builder.Tilt(65);
CameraPosition cameraPosition = builder.Build();
CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    _map.MoveCamera(cameraUpdate);
}
```

Nel frammento di codice precedente, una posizione sulla mappa specifica è rappresentata da di un [LatLng](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/LatLng) classe. Il livello di zoom è impostato a 18. Il rilevamento è la misurazione bussola in senso orario dal Nord. L'inclinazione proprietà controlla l'angolo di visualizzazione e specifica l'angolo di 25 gradi da verticale. La schermata seguente mostra il `GoogleMap` dopo l'esecuzione di codice precedente:

[![Angolo di visualizzazione mappa di Google di esempio con un percorso specificato con un inclinato](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)


### <a name="drawing-on-the-map"></a>Disegno sulla mappa

L'API di mappe Android fornisce API di disegno degli elementi seguenti in una mappa:

-  **Marcatori** -si tratta di icone speciali che vengono usate per identificare una singola posizione su una mappa.

-  **Sovrapposizioni** -si tratta di un'immagine che può essere usata per identificare una raccolta di posizioni o area sulla mappa.

-  **Linee, poligoni e cerchi** -si tratta di API che consentono di attività aggiungere forme a una mappa.


#### <a name="markers"></a>Markers

L'API di mappe fornisce un [marcatore](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker) classe che incapsula tutti i dati su una singola posizione su una mappa. Per impostazione predefinita usano un'icona standard fornita da Google Map. È possibile personalizzare l'aspetto del marcatore e rispondere alle selezioni.


##### <a name="adding-a-marker"></a>Aggiunta di un marcatore

Per aggiungere un marcatore a una mappa, è necessario creare un nuovo [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) e quindi chiamare il [AddMarker](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker(com.google.android.gms.maps.model.MarkerOptions)) metodo su un `GoogleMap` istanza. Questo metodo restituirà un [marcatore](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Marker) oggetto.

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...
if (_map != null) {
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    _map.AddMarker(marker1);
}
```

Il titolo del marcatore verrà visualizzato un *finestra info* quando l'utente tocca sul marcatore. Nella schermata seguente mostra l'indicatore di aspetto:

[![Esempio di mapping di Google con un indicatore e una finestra di informazioni di rilievo Vimy](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)


##### <a name="customizing-a-marker"></a>Personalizzazione di un marcatore

È possibile personalizzare l'icona utilizzata dall'indicatore di chiamando il `MarkerOptions.InvokeIcon` metodo quando si aggiunge il marcatore alla mappa.
Questo metodo accetta un [BitmapDescriptor](http://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptor.html) oggetto contenente i dati necessari per il rendering di icona. Il [BitmapDescriptorFactory](https://developer.android.com/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory.html) classe offre alcuni metodi helper per semplificare la creazione di un `BitmapDescriptor`. Nell'elenco seguente vengono descritti alcuni di questi metodi:

-   `DefaultMarker(float colour)` &ndash; Utilizzare il marcatore di mappe Google predefinito, ma modificare il colore.

-   `FromAsset(string assetName)` &ndash; Utilizzare un'icona personalizzata dal file specificato nella cartella risorse.

-   `FromBitmap(Bitmap image)` &ndash; Utilizzare la bitmap specificata come icona.

-   `FromFile(string fileName` &ndash; Creare l'icona personalizzata dal file nel percorso specificato.

-   `FromResource(int resourceId)` &ndash; Creare un'icona personalizzata dalla risorsa specificata.

Frammento di codice seguente viene illustrato un esempio di creazione di un marcatore predefinito colorato ciano:

```csharp
mapFrag.GetMapAsync(this);
...
if (_map != null)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");
    markerOpt1.InvokeIcon(BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan));
    _map.AddMarker(marker1);
}
```


#### <a name="info-windows"></a>Informazioni Windows

*Windows Info* sono finestre speciali che popup per visualizzare le informazioni all'utente quando vengono toccati un marcatore specifico. Per impostazione predefinita la finestra delle informazioni verrà visualizzato il contenuto del titolo del marcatore. Se non è stato assegnato il titolo, non verrà visualizzato alcun periodo di informazioni. Solo una finestra informazioni potrebbe essere visualizzata contemporaneamente.

È possibile personalizzare la finestra di informazioni mediante l'implementazione di [GoogleMap.IInfoWindowAdapter](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) interfaccia. Esistono due metodi importanti su questa interfaccia:

-  `public View GetInfoWindow(Marker marker)` &ndash; Questo metodo viene chiamato per ottenere una finestra di informazioni personalizzate per un marcatore. Se restituisce `null` , verrà utilizzato il rendering di finestra predefinito. Se questo metodo restituisce una visualizzazione, la visualizzazione verrà inserita all'interno della cornice di finestra di informazioni.

-  `public View GetInfoContents(Marker marker)` &ndash; Questo metodo verrà chiamato solo se GetInfoWindow restituisce `null` . Questo metodo può restituire un `null` valore se viene utilizzato il rendering predefinito del contenuto della finestra di informazioni. In caso contrario, questo metodo deve restituire una visualizzazione con il contenuto della finestra info.

Una finestra info non è una visualizzazione in tempo reale - invece Android convertirà la visualizzazione in una bitmap statica e che visualizzare sull'immagine. Ciò significa che una finestra info non può rispondere a eventi tocco o movimenti né aggiornerà automaticamente se stesso. Per aggiornare una finestra di informazioni, è necessario chiamare il [GoogleMap.ShowInfoWindow](http://developer.android.com/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) metodo.

L'immagine seguente mostra alcuni esempi di alcune finestre di informazioni personalizzate. L'immagine a sinistra contiene il contenuto personalizzato, mentre l'immagine a destra è la finestra e il contenuto personalizzato:

![Windows marcatore di esempio per Melbourne, tra cui icona e popolamento. Finestra di destra ha gli angoli arrotondati.](maps-api-images/marker-infowindows.png)


#### <a name="ground-overlays"></a>Sovrapposizioni di terreno

A differenza dei marcatori, che identificano una posizione specifica in una mappa, un [GroundOverlay](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlay.html) è un'immagine utilizzata per identificare una raccolta di posizioni o un'area sulla mappa.


##### <a name="adding-a-groundoverlay"></a>Aggiunta di un GroundOverlay

Aggiunta di una sovrapposizione di massa a una mappa è molto simile all'aggiunta di un marcatore a una mappa. Innanzitutto, un [GroundOverlayOptions](http://developer.android.com/reference/com/google/android/gms/maps/model/GroundOverlayOptions.html) viene creato l'oggetto. Questo oggetto viene quindi passato come parametro per il `GoogleMap.AddGroundOverlay` metodo, verrà restituito un `GroundOverlay` oggetto. Questo frammento di codice è riportato un esempio di aggiunta di una sovrapposizione di massa a una mappa:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = _map.AddGroundOverlay(groundOverlayOptions);
```

La schermata seguente mostra questa sovrapposizione su una mappa:

[![Esempio di mapping con un'immagine sovrapposta di un presentino polare](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)


#### <a name="lines-circles-and-polygons"></a>Linee, cerchi e poligoni

Esistono tre tipi semplici delle figure geometriche che possono essere aggiunto a una mappa:

-  **Polilinea** -si tratta di una serie di segmenti di linea connessa. È possibile contrassegnare un percorso su una mappa o qualsiasi forma necessari.

-  **Poligono** -si tratta di una forma chiusa per contrassegnare le aree su una mappa.

-  **Cerchio** -verrà tracciato un cerchio nella mappa.



##### <a name="polylines"></a>Polilinea

Oggetto [polilinea](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/Polyline) è un elenco di consecutivi `LatLng` gli oggetti che specificano i vertici di ogni segmento di linea. Viene creata una polilinea creando innanzitutto un `PolylineOptions` oggetto e aggiungere i punti. Il `PolylineOption` oggetto viene quindi passato a un `GoogleMap` oggetto chiamando il `AddPolyline` metodo.

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.
myMap.AddPolyline(rectOptions);
```


##### <a name="polygons"></a>Poligoni

`Polygon`sono molto simili a `Polyline`s, ma non sono aperti è terminata. `Polygon`s sono un ciclo chiuso e i relativi interno compilato.
`Polygon`vengono create in modo analogo un `Polyline`, ad eccezione di [GoogleMap.AddPolygon](http://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) metodo richiamato.

A differenza di un `Polyline`, `Polygon` è chiusura automatica. Quando `AddPolygon` viene chiamato il metodo chiude automaticamente disattivato il poligono tracciando una linea che connette il primo e ultimo punto. Frammento di codice seguente verrà creato un rettangolo pieno su come frammento di codice precedente nella stessa area di `Polyline` esempio.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon
myMap.AddPolygon(rectOptions);
```


##### <a name="circles"></a>Cerchi

Vengono creati cerchi creando prima una [CircleOption](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/model/CircleOptions) oggetto che è necessario specificare il centro e il raggio del cerchio in metri. Viene disegnato il cerchio sulla mappa chiamando [GoogleMap.AddCircle](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/GoogleMap#addCircle(com.google.android.gms.maps.model.CircleOptions)).
Frammento di codice riportato di seguito viene illustrato come disegnare un cerchio:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);
_map.AddCircle (CircleOptions);
```


## <a name="responding-to-events"></a>Risposta agli eventi

Esistono tre tipi di interazioni tra che un utente potrebbe avere con una mappa:

-  **Fare clic su marcatore** -l'utente fa clic su un marcatore.

-  **Trascinare marcatore** -l'utente ha long-fatto clic su un mparger

-  **Fare clic su informazioni finestra** -l'utente ha fatto clic su una finestra di informazioni.

Ciascuno di questi eventi verrà descritte in dettaglio più avanti.


### <a name="marker-click-events"></a>Marcatore di eventi Click

Quando l'utente fa clic su un marcatore di `MarkerClick` verrà generato l'evento e un `GoogleMap.MarkerClickEventArgs` passato. Questa classe contiene due proprietà:

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; Questa proprietà deve essere impostata su `true` per indicare che il gestore dell'evento ha utilizzato l'evento. Se è impostato su `false` verificherà il comportamento predefinito oltre il comportamento del gestore dell'evento personalizzato.

-  `P0` &ndash; Questo parametro di nome in modo non appropriato è un riferimento al marcatore che ha generato il `MarkerClick` evento.


Frammento di codice seguente viene illustrato un esempio di un `MarkerClick` che passerà la posizione della camera a una nuova posizione sulla mappa:

```csharp
private void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;
    Marker marker = markerClickEventArgs.P0;
    if (marker.Id.Equals(MyMarkerId)) // The ID of a specific marker the user clicked on.
    {
        _map.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(new LatLng(20.72110, -156.44776), 13));
    }
    else
    {
        Toast.MakeText(this, String.Format("You clicked on Marker ID {0}", marker.Id), ToastLength.Short).Show();
    }
}
```


### <a name="marker-drag-events"></a>Eventi di trascinamento marcatore

Questo evento viene generato quando l'utente desidera trascinare il marcatore. Per impostazione predefinita, i marcatori non sono draggable. Un marcatore può essere impostato come draggable impostando il `Marker.Draggable` proprietà `true` oppure richiamando il `MarkerOptions.Draggable` metodo con `true` come parametro.

Per trascinare il marcatore, l'utente deve fare clic su di esso prolungata e mantenere il dito sulla mappa. Quando si trascina il dito sullo schermo, verrà spostato l'indicatore. Quando un dito dell'utente solleva lo schermo, l'indicatore verrà rimangono invariati.

L'elenco seguente descrive i vari eventi che verranno generati per un marcatore draggable:

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; Questo evento viene generato quando l'utente trascina il marcatore.

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; Questo evento viene generato come il marcatore viene trascinato.

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; Questo evento viene generato quando il momento in cui l'utente termina trascinare il marcatore.

Ogni il `EventArgs` contiene una singola proprietà denominata `P0` che rappresenta un riferimento al `Marker` dell'oggetto viene trascinato.


### <a name="info-window-click-events"></a>Finestra Info eventi Click

Solo una finestra informazioni può essere visualizzata contemporaneamente. Quando l'utente fa clic su una finestra di informazioni in una mappa, l'oggetto mappa genererà un `InfoWindowClick` evento. Frammento di codice riportato di seguito viene illustrato come associare un gestore all'evento:

```csharp
private bool SetupMapIfNeeded()
{
    if (_map == null)
    {
        _map = _mapFragment.Map;
        if (_map != null)
        {
            _map.InfoWindowClick += MapOnInfoWindowClick;
            return true;
        }
        return false;
    }
    return true;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.P0;
    // Do something with marker.
}
```

Tenere presente che una finestra di informazioni è un valore statico `View` che viene visualizzato come un'immagine nella mappa. Qualsiasi widget, ad esempio pulsanti, caselle di controllo o visualizzazioni di testo che vengono inserite all'interno della finestra info saranno inerte e non può rispondere a uno dei relativi eventi utente integrale.



## <a name="related-links"></a>Collegamenti correlati

- [Google Play Services](http://developer.android.com/google/play-services/index.html)
- [Esegue il mapping di Google Android API v2](https://developers.google.com/maps/documentation/android/)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Ottenere una chiave API di mappe di Google](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Problema 57880: Google Play Services aggiornato ma AVD non](https://code.google.com/p/android/issues/detail?id=57880)
