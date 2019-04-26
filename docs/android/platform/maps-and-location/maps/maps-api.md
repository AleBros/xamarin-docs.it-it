---
title: Usando le mappe Google API nell'applicazione
description: Come implementare le funzionalità di Google Maps API v2 nell'applicazione xamarin. Android.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 1889154a12a701fb4ce57ef8644699dd978f768e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61186294"
---
# <a name="using-the-google-maps-api-in-your-application"></a>Usando l'API di Google Maps nell'applicazione

Utilizzando l'applicazione esegue il mapping è molto utile, ma talvolta si desidera includere esegue il mapping direttamente nell'applicazione. Oltre a predefinito viene eseguito il mapping dell'applicazione, Google offre anche un [mapping API nativo per Android](https://developers.google.com/maps/documentation/android-sdk/intro).
L'API di mappe è appropriata nei casi in cui si desidera esercitare un maggiore controllo dell'esperienza di mapping delle. Operazioni possibili con l'API di Maps includono:

-  A livello di codice modifica punto di vista della mappa.
-  L'aggiunta e personalizzazione dei marcatori.
-  Annotazione di una mappa con le sovrimpressioni.

Diversamente da v1 a Google Maps Android API attualmente deprecata, Google Maps Android API v2 è parte della [Google Play Services](https://developers.google.com/android/guides/overview).
Un'app xamarin. Android deve soddisfare alcuni prerequisiti obbligatori prima che sia possibile usare l'API Android di Google Maps.


## <a name="google-maps-api-prerequisites"></a>Prerequisiti dell'API di Google Maps

Diversi passaggi necessari da intraprendere prima di poter usare l'API di mappe, tra cui:

-  [Ottenere una chiave API di mappe](#obtain-maps-key)
-  [Installare il SDK di servizi di Google Play](#install-gps-sdk)
-  [Installare il pacchetto Xamarin.GooglePlayServices.Maps da NuGet](#install-gpsmaps-nuget)
-  [Specificare le autorizzazioni necessarie](#declare-permissions)
-  [Facoltativamente, creare un emulatore con Google APIs](#create-emulator-with-google-api)


### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />Ottenere una chiave API di Google Maps

Il primo passo è ottenere una chiave API Google Maps (si noti che non è possibile riutilizzare una chiave API dell'API di v1 legacy di Google Maps). Per informazioni su come ottenere e usare la chiave API con xamarin. Android, vedere [come ottenere un Google Maps chiave API](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).


### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" /> Installare il SDK di servizi di Google Play

Google Play Services è una tecnologia da Google che consente alle applicazioni Android di sfruttare i vantaggi delle varie funzionalità di Google, ad esempio Google +, di fatturazione In-App e mappe. Queste funzionalità sono accessibili nei dispositivi Android come servizi in background, che sono contenuti nel [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Le applicazioni Android interagiscono con Google Play Services tramite la libreria client di Google Play Services. Questa libreria contiene le interfacce e classi per i singoli servizi, ad esempio mappe. Il diagramma seguente mostra la relazione tra un'applicazione Android e Google Play Services:

![Diagramma che illustra la Store di Google Play l'aggiornamento di Google Play Services APK](maps-api-images/play-services-diagram.png)

API Maps Android viene fornito come parte di Google Play Services.
Prima di un'applicazione xamarin. Android può usare l'API di mappe, Google Play Services SDK deve essere installato mediante il [Android SDK Manager](~/android/get-started/installation/android-sdk.md). Lo screenshot seguente mostra dove in Android SDK Manager è reperibile il client di servizi di Google Play:

![Google Play Services viene visualizzato sotto i dati aggiuntivi in Android SDK Manager](maps-api-images/image01.png)

> [!NOTE]
> I servizi Google Play APK è un prodotto concesso in licenza che non può essere presente in tutti i dispositivi. Se non è installato, quindi Google Maps non funzionerà sul dispositivo.

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" /> Installare il pacchetto Xamarin.GooglePlayServices.Maps da NuGet

Il [Xamarin.GooglePlayServices.Maps pacchetto](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps) contiene le associazioni di xamarin. Android per API di Google Play Services esegue il mapping.
Per aggiungere il pacchetto di Google Play Services mappa, fare doppio clic il **riferimenti** cartella del progetto in Esplora soluzioni e fare clic su **Gestisci pacchetti NuGet...** :

![Esplora soluzioni Mostra Gestisci pacchetti NuGet menu di scelta rapida in riferimenti](maps-api-images/image02.png)

Verrà visualizzata la **Gestisci pacchetti NuGet**. Fare clic su **esplorare** e immettere **Xamarin Google Play Services Maps** nel campo di ricerca. Selezionare **Xamarin.GooglePlayServices.Maps** e fare clic su **installare**. (Se questo pacchetto è stato installato in precedenza, fare clic su **Update**.):

[![Gestione pacchetti NuGet con pacchetto Xamarin.GooglePlayServices.Maps selezionato](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Si noti che vengono installati anche i pacchetti di dipendenza seguente:

-   **Xamarin.GooglePlayServices.Base**
-   **Xamarin.GooglePlayServices.Basement**
-   **Xamarin.GooglePlayServices.Tasks**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" /> Specificare le autorizzazioni necessarie

Le app devono identificare i requisiti hardware e l'autorizzazione per usare l'API di Google Maps.  Alcune autorizzazioni vengono concesse automaticamente da Google Play Services SDK e non è necessario aggiungerli in modo esplicito a uno sviluppatore **AndroidManfest.XML**:

-  **Accesso allo stato di rete** &ndash; l'API mappe deve essere in grado di controllare se è possibile scaricare i riquadri mappa.

-  **L'accesso a Internet** &ndash; l'accesso a Internet è necessario scaricare le tessere mappa e comunicare con i server Google Play per l'accesso all'API.

Le autorizzazioni seguenti e le funzionalità devono essere specificate nel **androidmanifest. XML** per l'API Android di Google Maps:

-  **OpenGL ES v2** &ndash; l'applicazione deve dichiarare il requisito per la versione v2 OpenGL ES.

-  **Chiave API di Google Maps** &ndash; la chiave API viene usata per confermare che l'applicazione viene registrato e autorizzato a usare Google Play Services. Visualizzare [come ottenere una chiave API di Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) per informazioni dettagliate su questa chiave.

- **Richiedere il client HTTP Apache legacy** &ndash; le app destinate a 9.0 Android (livello API 28) o versioni successive deve specificare che il client HTTP Apache legacy è una libreria facoltativa da utilizzare.

-  **Accesso ai servizi basati sul Web di Google** &ndash; l'applicazione necessita delle autorizzazioni per accedere ai servizi web di Google che supportano l'API di mappe di Android.

-  **Le autorizzazioni per Google Play Services notifiche** &ndash; l'applicazione deve essere concessa l'autorizzazione per ricevere le notifiche remote da Google Play Services.

-  **Accesso ai provider di posizione** &ndash; queste sono autorizzazioni facoltative.
   Consentirà di `GoogleMap` classe per visualizzare la posizione del dispositivo sulla mappa.

Inoltre, 9 Android ha rimosso la libreria client HTTP Apache dal bootclasspath e pertanto non è disponibile per le applicazioni che usano API 28 o superiore. La riga seguente deve essere aggiunto per il `application` nodo del **androidmanifest. XML** file per continuare a usare il client HTTP Apache in applicazioni destinate a API 28 o superiore:

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> Versioni molto obsolete di Google Play SDK necessari un'app per richiedere il `WRITE_EXTERNAL_STORAGE` l'autorizzazione. Questo requisito non è più necessario con i binding Xamarin per recenti per Google Play Services.

Il frammento seguente è riportato un esempio delle impostazioni che devono essere aggiunti a **androidmanifest. XML**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionName="4.5" package="com.xamarin.docs.android.mapsandlocationdemo2" android:versionCode="6">
    <uses-sdk android:minSdkVersion="23" android:targetSdkVersion="28" />

    <!-- Google Maps for Android v2 requires OpenGL ES v2 -->
    <uses-feature android:glEsVersion="0x00020000" android:required="true" />

    <!-- Necessary for apps that target Android 9.0 or higher -->
    <uses-library android:name="org.apache.http.legacy" android:required="false" />


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
        <!-- Necessary for apps that target Android 9.0 or higher -->
        <uses-library android:name="org.apache.http.legacy" android:required="false" />
    </application>
</manifest>
```

Oltre a richiedere le autorizzazioni **androidmanifest. XML**, un'app deve anche eseguire controlli delle autorizzazioni di runtime per il `ACCESS_COARSE_LOCATION` e il `ACCESS_FINE_LOCATION` autorizzazioni. Vedere le [le autorizzazioni di xamarin. Android](~/android/app-fundamentals/permissions.md) Guida per altre informazioni sull'esecuzione di controlli delle autorizzazioni in fase di esecuzione.


### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />Creare un emulatore con API Google

Nel caso in cui un dispositivo Android fisico con Google Play services non è installato, è possibile creare un'immagine dell'emulatore per lo sviluppo. Per altre informazioni vedere la [Gestione dispositivi](~/android/get-started/installation/android-emulator/device-manager.md).


## <a name="the-googlemap-class"></a>La classe GoogleMap

Dopo che i prerequisiti vengono soddisfatti, è possibile iniziare a sviluppare l'applicazione e usare l'API di mappe di Android. Il [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) classe è l'API principale che un'applicazione xamarin. Android userà per visualizzare e interagire con un Google Maps per Android. Questa classe ha le responsabilità seguenti:

-  L'interazione con i servizi Google Play per autorizzare l'applicazione con il servizio web di Google.

-  Download, la memorizzazione nella cache e visualizzare i riquadri mappa.

-  La visualizzazione controlli dell'interfaccia utente, ad esempio, panoramica e zoom all'utente.

-  Marcatori di disegno e forme geometriche nelle mappe.

Il `GoogleMap` viene aggiunto a un'attività in uno dei due modi:

-  **MapFragment** - il [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) è riportato un frammento specializzato che funge da host per il `GoogleMap` oggetto. Il `MapFragment` richiede il livello API Android 12 o superiore.
   Le versioni precedenti di Android è possono usare la [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment).  Questa guida è incentrata sull'uso di `MapFragment` classe.

-  **MapView** - il [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) è una sottoclasse specializzata visualizzazione, che può fungere da host per un `GoogleMap` oggetto. Gli utenti di questa classe devono inoltrare tutti i metodi del ciclo di vita di attività per il `MapView` classe.

Ognuno di questi contenitori espone una `Map` proprietà che restituisce un'istanza di `GoogleMap`. Dovrebbe dare la preferenza per la [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) classe perché è un'API più semplice che consente di ridurre il codice boilerplate quantità che uno sviluppatore deve implementare manualmente.

### <a name="adding-a-mapfragment-to-an-activity"></a>Aggiunta di un MapFragment a un'attività

Lo screenshot seguente è riportato un esempio di una semplice `MapFragment`:

[![Screenshot di un dispositivo di visualizzazione di un frammento di mapping di Google](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Analogamente alle altre classi di frammenti, esistono due modi per aggiungere un `MapFragment` a un'attività:

-   **In modo dichiarativo** - il `MapFragment` possono essere aggiunti tramite il file di layout XML per l'attività. Il frammento XML seguente viene illustrato un esempio di come usare il `fragment` elemento:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

-   **A livello di programmazione** - il `MapFragment` può essere implementato a livello di codice con il [ `MapFragment.NewInstance` ](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) (metodo) e quindi aggiunto a un'attività. Questo frammento di codice viene illustrato il modo più semplice per creare un'istanza di un `MapFragment` dell'oggetto e aggiungere a un'attività:

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    È possibile configurare il `MapFragment` passando un [ `GoogleMapOptions` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) oggetto `NewInstance`. Questo argomento viene discusso nella sezione [GoogleMap proprietà](#googlemap_object) che viene visualizzato in un secondo momento in questa Guida.

Il `MapFragment.GetMapAsync` metodo viene utilizzato per inizializzare il [ `GoogleMap` ](#googlemap_object) che viene ospitato dal frammento e ottenere un riferimento all'oggetto map ospitata dal `MapFragment`. Questo metodo accetta un oggetto che implementa il `IOnMapReadyCallback` interfaccia.

Questa interfaccia ha un solo metodo, `IMapReadyCallback.OnMapReady(MapFragment map)` che verrà richiamato quando è possibile che l'app interagire con il `GoogleMap` oggetto. Il frammento di codice seguente viene illustrato come è possibile inizializzare un'attività Android una `MapFragment` e implementare il `IOnMapReadyCallback` interfaccia:
```csharp
public class MapWithMarkersActivity : AppCompatActivity, IOnMapReadyCallback
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.MapLayout);

        var mapFragment = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.map);
        mapFragment.GetMapAsync(this);

        // remainder of code omitted
    }

    public void OnMapReady(GoogleMap map)
    {
        // Do something with the map, i.e. add markers, move to a specific location, etc.
    }
}
```

### <a name="map-types"></a>Eseguire il mapping di tipi

Esistono cinque tipi diversi di mappe disponibili dall'API di Google viene eseguito il mapping:

-  **Normale** -si tratta del tipo di mappa predefinito. Visualizza le strade e importante funzionalità naturale con alcuni punti fittizi di interesse (ad esempio i bridge di edifici e).

-  **Satellite** -questa mappa Mostra fotografia satellite.

-  **Hybrid** : questa mappa Mostra fotografia satellite e road esegue il mapping.

-  **Terreno** -Mostra principalmente le funzionalità topografici con alcune strade.

-  **Nessuno** -questa mappa non verrà caricato tutte le sezioni, ne viene eseguito il rendering come una griglia vuota.


L'immagine seguente mostra tre dei diversi tipi di mappe, da sinistra a destra (normale, ibrido, terreno):

[![Screenshot di esempio di tre mappa: Normale, ibride e terreno](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

Il `GoogleMap.MapType` proprietà viene utilizzata per impostare o modificare il tipo di mappa viene visualizzato. Il frammento di codice seguente viene illustrato come visualizzare una mappa di satellite.

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```


### <a name="a-namegooglemapobject-googlemap-properties"></a><a name="googlemap_object" />Proprietà GoogleMap

`GoogleMap` definisce diverse proprietà che è possibile controllare le funzionalità e l'aspetto della mappa. Uno dei modi per configurare lo stato iniziale di una `GoogleMap` consiste nel passare un [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) dell'oggetto durante la creazione di un `MapFragment`. Il frammento di codice seguente è un esempio dell'uso di un `GoogleMapOptions` dell'oggetto durante la creazione di un `MapFragment`:

```csharp
GoogleMapOptions mapOptions = new GoogleMapOptions()
    .InvokeMapType(GoogleMap.MapTypeSatellite)
    .InvokeZoomControlsEnabled(false)
    .InvokeCompassEnabled(true);

FragmentTransaction fragTx = FragmentManager.BeginTransaction();
mapFragment = MapFragment.NewInstance(mapOptions);
fragTx.Add(Resource.Id.map, mapFragment, "map");
fragTx.Commit();
```

L'altro modo per configurare un `GoogleMap` consiste nella modifica delle proprietà nel [UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) dell'oggetto map. Nell'esempio di codice successivo viene illustrato come configurare un `GoogleMap` per visualizzare i controlli zoom e una bussola:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>L'interazione con il GoogleMap

API Maps Android fornisce API che permettono un'attività modificare il punto di vista, aggiungere marcatori, posizionare le sovrimpressioni personalizzate o disegnare forme geometriche. Questa sezione illustra come eseguire alcune di queste attività in xamarin. Android.

### <a name="changing-the-viewpoint"></a>La modifica del punto di vista

Le mappe sono modellate come piano flat sullo schermo, in base la proiezione Mercator. La vista mappa è quello di un *fotocamera* dall'aspetto professionale direttamente verso il basso su questo piano. È possibile controllare la posizione della camera modificando il percorso, lo zoom, inclinazione e rilevante. Il [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) classe viene utilizzata per spostare la posizione della fotocamera. `CameraUpdate` gli oggetti non vengono creati direttamente, l'API di mappe fornisce invece la [CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory) classe.

Una volta una `CameraUpdate` oggetto è stato creato, viene passato come parametro a uno il [GoogleMap.MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) oppure [GoogleMap.AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) metodi. Il `MoveCamera` metodo aggiorna la mappa immediatamente durante il `AnimateCamera` metodo fornisce una transizione senza problemi e animata.

Questo frammento di codice è un semplice esempio di come usare il `CameraUpdateFactory` per creare un `CameraUpdate` che incrementa il livello di zoom della mappa da una percentuale di zoom:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

L'API di mappe fornisce una [CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) che aggrega tutti i valori possibili per la posizione della camera. Un'istanza di questa classe può essere fornita per il [CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) metodo che restituirà un `CameraUpdate` oggetto. L'API di Maps include anche il [CameraPosition.Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) classe che fornisce un'API intuitiva per la creazione di `CameraPosition` oggetti.
Il frammento di codice seguente viene illustrato un esempio di creazione di un `CameraUpdate` da un `CameraPosition` e l'uso per modificare la posizione della fotocamera su un `GoogleMap`:

```csharp
public void OnMapReady(GoogleMap map)
{
    LatLng location = new LatLng(50.897778, 3.013333);

    CameraPosition.Builder builder = CameraPosition.InvokeBuilder();
    builder.Target(location);
    builder.Zoom(18);
    builder.Bearing(155);
    builder.Tilt(65);

    CameraPosition cameraPosition = builder.Build();

    CameraUpdate cameraUpdate = CameraUpdateFactory.NewCameraPosition(cameraPosition);

    map.MoveCamera(cameraUpdate);
}
```

Nel frammento di codice precedente, una posizione sulla mappa specifica è rappresentata dal [LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) classe. Il livello di zoom è impostato a 18, che è una misura arbitraria di zoom utilizzata da Google Maps. L'impatto è la misurazione della bussola in senso orario da Nord. La proprietà inclinazione controlla l'angolo di visualizzazione e specifica l'angolo di 25 gradi da verticale. La schermata seguente mostra il `GoogleMap` dopo l'esecuzione di codice precedente:

[![Angolo di visualizzazione della mappa Google di esempio che mostra un percorso specificato con un inclinato](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)


### <a name="drawing-on-the-map"></a>Disegno sulla mappa

API Maps Android offre API di disegno degli elementi seguenti in una mappa:

-  **Marcatori** -si tratta di icone speciali che vengono usate per identificare una singola posizione su una mappa.

-  **Le sovrimpressioni** -si tratta di un'immagine che può essere utilizzata per identificare una raccolta di posizioni o area sulla mappa.

-  **Linee, poligoni e cerchi** -si tratta di API che consentono le attività aggiungere forme a una mappa.


#### <a name="markers"></a>Markers

L'API di mappe fornisce una [marcatore](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) classe che incapsula tutti i dati su un'unica posizione su una mappa. Per impostazione predefinita il marcatore di classe viene utilizzata un'icona standard fornita da Google Maps. È possibile personalizzare l'aspetto di un marcatore e per rispondere all'interazione dell'utente.


##### <a name="adding-a-marker"></a>Aggiunta di un marcatore

Per aggiungere un marcatore a una mappa, è necessario creare una nuova [MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) dell'oggetto e quindi chiamare il [AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) metodo su un `GoogleMap` istanza. Questo metodo restituisce un [marcatore](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) oggetto.

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

Verrà visualizzato il titolo del marcatore un' *finestra info* quando l'utente tocca il marcatore. Lo screenshot seguente mostra come appare questo marcatore:

[![Esempio Google Map con un indicatore e una finestra delle informazioni per Vimy Ridge](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)


##### <a name="customizing-a-marker"></a>Personalizzazione di un marcatore

È possibile personalizzare l'icona utilizzata dall'indicatore di chiamando il `MarkerOptions.InvokeIcon` metodo quando si aggiunge il marcatore della mappa.
Questo metodo accetta un [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) oggetto contenente i dati necessari per eseguire il rendering l'icona. Il [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory) classe offre alcuni metodi helper per semplificare la creazione di un `BitmapDescriptor`. Nell'elenco seguente introduce alcuni di questi metodi:

-   `DefaultMarker(float colour)` &ndash; Usare l'indicatore di Google Maps predefinito, ma modificare il colore.

-   `FromAsset(string assetName)` &ndash; Usare un'icona personalizzata dal file specificato nella cartella asset.

-   `FromBitmap(Bitmap image)` &ndash; Usare la mappa di bit specificato come icona.

-   `FromFile(string fileName)` &ndash; Creare l'icona personalizzata dal file nel percorso specificato.

-   `FromResource(int resourceId)` &ndash; Creare un'icona personalizzata dalla risorsa specificata.

Il frammento di codice seguente mostra un esempio di creazione di un marcatore predefinito colorato ciano:

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    var bmDescriptor = BitmapDescriptorFactory.DefaultMarker (BitmapDescriptorFactory.HueCyan);
    markerOpt1.InvokeIcon(bmDescriptor);

    map.AddMarker(markerOpt1);
}
```

#### <a name="info-windows"></a>Finestre di informazioni

*Windows Info* sono finestre speciale tale popup per visualizzare le informazioni all'utente quando si tocca un marcatore specifico. Per impostazione predefinita la finestra delle informazioni verrà visualizzato il contenuto del titolo del marcatore. Se non è stato assegnato il titolo, non verrà visualizzata alcuna finestra di informazioni. Solo una finestra delle informazioni potrebbe essere visualizzata alla volta.

È possibile personalizzare la finestra di informazioni mediante l'implementazione di [GoogleMap.IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) interfaccia. Esistono due metodi importanti su questa interfaccia:

-  `public View GetInfoWindow(Marker marker)` &ndash; Questo metodo viene chiamato per ottenere una finestra di informazioni personalizzate per un marcatore. Se il valore restituito `null` , verrà usato il rendering di finestra predefinito. Se questo metodo restituisce una visualizzazione, quindi verrà inserita quella visualizzazione all'interno della cornice della finestra informazioni.

-  `public View GetInfoContents(Marker marker)` &ndash; Questo metodo verrà chiamato solo se restituisce GetInfoWindow `null` . Questo metodo può restituire un `null` valore se il rendering predefinito del contenuto della finestra informazioni deve essere utilizzato. In caso contrario, questo metodo deve restituire una visualizzazione con il contenuto della finestra di informazioni.

Una finestra info non è una visualizzazione in tempo reale - invece Android verrà convertire la visualizzazione in una bitmap statica e di visualizzarla nell'immagine. Ciò significa che una finestra di informazioni non può rispondere a eventi di tocco o movimenti, né aggiornerà automaticamente se stesso. Per aggiornare una finestra di informazioni, è necessario chiamare il [GoogleMap.ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) (metodo).

L'immagine seguente mostra alcuni esempi di alcune finestre di informazioni personalizzate. L'immagine a sinistra presenta il contenuto personalizzato, mentre l'immagine a destra è la finestra e il contenuto personalizzato con angoli arrotondati:

![Finestre di marcatore di esempio per Melbourne, tra cui icona e il popolamento. Finestra di destra ha gli angoli arrotondati.](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

A differenza dei marcatori, che identificano una posizione specifica in una mappa, un [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) è un'immagine che viene usata per identificare una raccolta di posizioni o un'area sulla mappa.

##### <a name="adding-a-groundoverlay"></a>Aggiunta di un GroundOverlay

L'aggiunta di una sovrimpressione zero a una mappa è analoga all'aggiunta di un marcatore a una mappa. Prima di tutto una [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) creazione dell'oggetto. Questo oggetto viene quindi passato come parametro per il [ `GoogleMap.AddGroundOverlay` ](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) metodo, che restituirà un `GroundOverlay` oggetto. Questo frammento di codice è un esempio di aggiunta di una sovrimpressione zero a una mappa:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

Lo screenshot seguente illustra questo controllo overlay su una mappa:

[![Mappa di esempio con un'immagine sovrapposta di un bear polare](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)


#### <a name="lines-circles-and-polygons"></a>Linee, cerchi e poligoni

Esistono tre tipi semplici di figure geometriche, che possono essere aggiunto a una mappa:

-  **Polyline** -si tratta di una serie di segmenti di linea collegati. È possibile contrassegnare un percorso su una mappa o creare una forma geometrica.

-  **Cerchio** -verrà tracciato un cerchio nella mappa.

-  **Poligono** -si tratta di una forma chiusa per contrassegnare le aree su una mappa.


##### <a name="polylines"></a>Polilinee

Oggetto [polilinea](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) è un elenco di consecutivi `LatLng` gli oggetti che specificano i vertici di ogni segmento di linea. Viene creata una polilinea creando innanzitutto un `PolylineOptions` oggetto e aggiungendovi i punti. Il `PolylineOption` oggetto viene quindi passato a un `GoogleMap` chiamando il `AddPolyline` (metodo).

```csharp
PolylineOption rectOptions = new PolylineOption();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
rectOptions.Add(new LatLng(37.35, -122.0)); // close the polyline - this makes a rectangle.

googleMap.AddPolyline(rectOptions);
```

##### <a name="circles"></a>Cerchi

Cerchi vengono creati creando prima una [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) oggetto cui specificherà il centro e il raggio del cerchio in metri. Il cerchio viene disegnato sulla mappa chiamando [GoogleMap.AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions)).
Il frammento di codice seguente illustra come disegnare un cerchio:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```


##### <a name="polygons"></a>Poligoni

`Polygon`sono simili a `Polyline`s, ma non sono aperti è terminata. `Polygon`s sono un ciclo chiuso e presentano relativi interno compilato.
`Polygon`vengono create nello stesso modo esattamente come un `Polyline`, tranne le [GoogleMap.AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) metodo richiamato.

A differenza di una `Polyline`, un `Polygon` è chiusura automatica. Il poligono verrà chiuso la `AddPolygon` metodo tracciando una linea che connette il primo e ultimo punto. Il frammento di codice seguente creerà un rettangolo pieno sull'area della stessa come frammento di codice precedente nel `Polyline` esempio.

```csharp
PolygonOptions rectOptions = new PolygonOptions();
rectOptions.Add(new LatLng(37.35, -122.0));
rectOptions.Add(new LatLng(37.45, -122.0));
rectOptions.Add(new LatLng(37.45, -122.2));
rectOptions.Add(new LatLng(37.35, -122.2));
// notice we don't need to close off the polygon

googleMap.AddPolygon(rectOptions);
```


## <a name="responding-to-user-events"></a>Risposta agli eventi utente

Esistono tre tipi di interazioni tra che un utente potrebbe avere con una mappa:

-  **Fare clic su marcatore** -l'utente fa clic su un marcatore.

-  **Trascinare marcatore** -l'utente ha prolungata fatto clic su un mparger

-  **Fare clic su Info finestra** -l'utente ha fatto clic su una finestra di informazioni.

Ciascuno di questi eventi verrà descritte in dettaglio più avanti.


### <a name="marker-click-events"></a>Marcatore di eventi click

Il `MarkerClicked` evento viene generato quando l'utente tocca un marcatore. Questo evento accetta un `GoogleMap.MarkerClickEventArgs` oggetto come parametro. Questa classe contiene due proprietà:

-  `GoogleMap.MarkerClickEventArgs.Handled` &ndash; Questa proprietà deve essere impostata su `true` per indicare che il gestore dell'evento ha utilizzato l'evento. Se è impostato su `false` verificherà il comportamento predefinito oltre il comportamento personalizzato del gestore dell'evento.

-  `Marker` &ndash; Questa proprietà è un riferimento per il marcatore che ha generato il `MarkerClick` evento.


Questo frammento di codice illustra un esempio di un `MarkerClick` che modifica la posizione della fotocamera in una nuova posizione sulla mappa:

```csharp
void MapOnMarkerClick(object sender, GoogleMap.MarkerClickEventArgs markerClickEventArgs)
{
    markerClickEventArgs.Handled = true;

    var marker = markerClickEventArgs.Marker;
    if (marker.Id.Equals(gotMauiMarkerId))
    {
        LatLng InMaui = new LatLng(20.72110, -156.44776);

        // Move the camera to look at Maui.
        PositionPolarBearGroundOverlay(InMaui);
        googleMap.AnimateCamera(CameraUpdateFactory.NewLatLngZoom(InMaui, 13));
        gotMauiMarkerId = null;
        polarBearMarker.Remove();
        polarBearMarker = null;
    }
    else
    {
        Toast.MakeText(this, $"You clicked on Marker ID {marker.Id}", ToastLength.Short).Show();
    }
}
```


### <a name="marker-drag-events"></a>Eventi di trascinamento marcatore

Questo evento viene generato quando l'utente desidera trascinare il marcatore. Per impostazione predefinita, i marcatori non sono trascinabili. Un marcatore può essere impostato come trascinabile impostando il `Marker.Draggable` proprietà `true` oppure richiamando il `MarkerOptions.Draggable` metodo con `true` come parametro.

Per trascinare il marcatore, l'utente deve prima di tutto prolungata fare clic sul marcatore e quindi il dito deve rimanere nella mappa. Quando il dito dell'utente viene trascinato sullo schermo, si passerà il marcatore. Quando il dito dell'utente solleva fuori dallo schermo, il marcatore verrà rimangono invariati.

L'elenco seguente descrive i vari eventi che vengono generati per un marcatore trascinabile:

-   `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; Questo evento viene generato quando l'utente trascina prima di tutto il marcatore.

-   `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; Questo evento viene generato come il marcatore viene trascinato.

-   `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; Questo evento viene generato quando l'utente ha terminato il trascinamento di marcatore.

Ognuna delle `EventArgs` contiene una singola proprietà denominata `P0` vale a dire un riferimento al `Marker` dell'oggetto da trascinare.


### <a name="info-window-click-events"></a>Eventi finestra fare clic su informazioni

Solo una finestra delle informazioni può essere visualizzata alla volta. Quando l'utente fa clic su una finestra di informazioni in una mappa, l'oggetto map genererà un `InfoWindowClick` evento. Il frammento di codice seguente viene illustrato come associare un gestore dell'evento:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.InfoWindowClick += MapOnInfoWindowClick;
}

private void MapOnInfoWindowClick (object sender, GoogleMap.InfoWindowClickEventArgs e)
{
    Marker myMarker = e.Marker;
    // Do something with marker.
}
```

È importante ricordare che una finestra di informazioni è un valore statico `View` che viene eseguito il rendering come immagine sulla mappa. Alcun widget, ad esempio pulsanti, caselle di controllo o le visualizzazioni di testo che vengono inserite all'interno della finestra info saranno inerti e non può rispondere a uno dei relativi eventi utente integrale.


## <a name="related-links"></a>Collegamenti correlati

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play Services](https://developers.google.com/android/guides/overview)
- [Esegue il mapping di Google Android API v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Come ottenere una chiave API Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [Usa-funzione](https://developer.android.com/guide/topics/manifest/uses-feature-element)
