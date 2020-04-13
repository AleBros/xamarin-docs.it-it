---
title: Utilizzo dell'API di Google Maps nell'applicazione
description: Come implementare le funzionalità dell'API v2 di Google Maps nell'applicazione Xamarin.Android.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: adcfb1457742d343f87a602885566107cf327e2d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027146"
---
# <a name="using-the-google-maps-api-in-your-application"></a>Utilizzo dell'API di Google Maps nell'applicazione

L'utilizzo dell'applicazione Mappe è ideale, ma a volte si desidera includere le mappe direttamente nell'applicazione. Oltre all'applicazione mappe integrata, Google offre anche [un'API di mappatura nativa per Android.](https://developers.google.com/maps/documentation/android-sdk/intro)
L'API Mappe è adatta per i casi in cui si desidera mantenere un maggiore controllo sull'esperienza di mapping. Le operazioni possibili con l'API Mappe includono:Things that are possible with the Maps API include:

- Modifica a livello di codice del punto di vista della mappa.
- Aggiunta e personalizzazione di marcatori.
- Annotazione di una mappa con sovrapposizioni.

A differenza dell'ormai deprecata GOOGLE Maps Android API v1, Google Maps Android API v2 fa parte di [Google Play Services.](https://developers.google.com/android/guides/overview)
Un'app Xamarin.Android deve soddisfare alcuni prerequisiti obbligatori prima che sia possibile utilizzare l'API Android di Google Maps.

## <a name="google-maps-api-prerequisites"></a>Prerequisiti dell'API di Google Maps

Prima di poter utilizzare l'API Mappe, è necessario eseguire diversi passaggi, tra cui:Several steps need to be taken before you can use the Maps API, including:

- [Ottenere una chiave API di MapsObtain a Maps API key](#obtain-maps-key)
- [Installare l'SDK di Google Play Services](#install-gps-sdk)
- [Installare il pacchetto Xamarin.GooglePlayServices.Maps da NuGet](#install-gpsmaps-nuget)
- [Specificare le autorizzazioni necessarie](#declare-permissions)
- [Facoltativamente, creare un emulatore con le API di GoogleOptionally, Create an emulator with the Google APIs](#create-emulator-with-google-api)

### <a name="obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />Ottenere una chiave API di Google Maps

Il primo passaggio consiste nell'ottenere una chiave API di Google Maps (si noti che non è possibile riutilizzare una chiave API dall'API legacy di Google Maps v1). Per informazioni su come ottenere e utilizzare la chiave API con Xamarin.Android, vedere [Ottenere una chiave API di Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

### <a name="install-the-google-play-services-sdk"></a><a name="install-gps-sdk" />Installare l'SDK di Google Play Services

Google Play Services è una tecnologia di Google che consente alle applicazioni Android di sfruttare varie funzionalità di Google come Google, la fatturazione in-app e Mappe. Queste funzioni sono accessibili sui dispositivi Android come servizi in background, che sono contenuti nel [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Le applicazioni Android interagiscono con Google Play Services tramite la libreria client di Google Play Services. Questa libreria contiene le interfacce e le classi per i singoli servizi, ad esempio Mappe.This library contains the interfaces and classes for the individual services such as Maps. Il diagramma seguente mostra la relazione tra un'applicazione Android e Google Play Services:

![Diagramma che illustra l'aggiornamento dell'APK di Google Play Store](maps-api-images/play-services-diagram.png)

L'API di Android Maps viene fornita come parte di Google Play Services.
Prima che un'applicazione Xamarin.Android possa utilizzare l'API Maps, è necessario installare Google Play Services SDK utilizzando [Android SDK Manager.](~/android/get-started/installation/android-sdk.md) La schermata seguente mostra dove in Android SDK Manager è possibile trovare il client dei servizi Google Play:

![Google Play Services viene visualizzato in Extra in Android SDK Manager](maps-api-images/image01.png)

> [!NOTE]
> Google Play Services APK è un prodotto concesso in licenza che potrebbe non essere presente su tutti i dispositivi. Se non è installato, Google Maps non funzionerà sul dispositivo.

### <a name="install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" />Installare il pacchetto Xamarin.GooglePlayServices.Maps da NuGet

Il [pacchetto Xamarin.GooglePlayServices.Maps](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps) contiene i binding Xamarin.Android per l'API Google Play Services Maps.
Per aggiungere il pacchetto Google Play Services Map, fare clic con il pulsante destro del mouse sulla cartella **Riferimenti** del progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet...**:

![Esplora soluzioni con la voce di menu di scelta rapida Gestisci pacchetti NuGet in Riferimenti](maps-api-images/image02.png)

Verrà aperto **Gestione pacchetti NuGet**. Fai clic su **Sfoglia** e inserisci **Xamarin Google Play Services Maps** nel campo di ricerca. Selezionare **Xamarin.GooglePlayServices.Maps** e fare clic su **Installa**. Se il pacchetto è stato installato in precedenza, fare clic su **Aggiorna**.):

[![NuGet Package Manager con il pacchetto Xamarin.GooglePlayServices.Maps selezionato](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Si noti che sono installati anche i seguenti pacchetti di dipendenze:

- **Xamarin.GooglePlayServices.Base**
- **Xamarin.GooglePlayServices.Basement**
- **Xamarin.GooglePlayServices.Tasks**

### <a name="specify-the-required-permissions"></a><a name="declare-permissions" />Specificare le autorizzazioni necessarie

Le app devono identificare i requisiti hardware e di autorizzazione per poter utilizzare l'API di Google Maps.  Alcune autorizzazioni vengono concesse automaticamente da Google Play Services SDK e non è necessario che uno sviluppatore le aggiuva esplicitamente a **AndroidManfest.XML:**

- **Accesso allo stato** &ndash; della rete L'API Mappe deve essere in grado di verificare se è possibile scaricare i riquadri della mappa.

- **L'accesso** &ndash; a Internet è necessario per scaricare i riquadri della mappa e comunicare con Google Play Server per l'accesso alle API.

Le autorizzazioni e le funzionalità seguenti devono essere specificate in **AndroidManifest.XML** per l'API Android di Google Maps:

- **OpenGL ES v2** &ndash; L'applicazione deve dichiarare il requisito per OpenGL ES v2.

- **Chiave** &ndash; API di Google Maps La chiave API viene utilizzata per confermare che l'applicazione è registrata e autorizzata a utilizzare Google Play Services. Per informazioni dettagliate su questa chiave, vedi [Ottenere una chiave API di Google Maps.](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)

- **Richiedere il client** &ndash; Apache legacy App client di destinazione Android 9.0 (livello API 28) o superiore deve specificare che il client Apache HTTP legacy è una libreria facoltativa da utilizzare.

- **Accesso ai servizi** &ndash; basati su Google Web L'applicazione necessita delle autorizzazioni per accedere ai servizi Web di Google che supportano l'API Android Maps.

- **Autorizzazioni per le notifiche** &ndash; dei Servizi Google Play L'applicazione deve essere autorizzata a ricevere notifiche remote da Google Play Services.

- **Accesso ai provider** &ndash; di posizione Si tratta di autorizzazioni facoltative.
   Permetteranno alla `GoogleMap` classe di visualizzare la posizione del dispositivo sulla mappa.

Inoltre, Android 9 ha rimosso la libreria client HTTP Apache dal percorso di classe di avvio e pertanto non è disponibile per le applicazioni destinate all'API 28 o successiva. La riga seguente deve `application` essere aggiunta al nodo del file **AndroidManifest.xml** per continuare a usare il client HTTP Apache nelle applicazioni destinate all'API 28 o versione successiva:

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> Le versioni molto vecchie di Google Play `WRITE_EXTERNAL_STORAGE` SDK richiedevano un'app per richiedere l'autorizzazione. Questo requisito non è più necessario con i recenti binding Xamarin per Google Play Services.

Il frammento di codice seguente è un esempio delle impostazioni che devono essere aggiunte a **AndroidManifest.XML:**

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

Oltre a richiedere le autorizzazioni **AndroidManifest.XML,** un'app deve `ACCESS_COARSE_LOCATION` anche `ACCESS_FINE_LOCATION` eseguire controlli delle autorizzazioni di runtime per le autorizzazioni e . Vedere la Guida [alle autorizzazioni di Xamarin.Android](~/android/app-fundamentals/permissions.md) per ulteriori informazioni sull'esecuzione di controlli delle autorizzazioni in fase di esecuzione.

### <a name="create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />Creare un emulatore con le API di Google

Nel caso in cui un dispositivo Android fisico con servizi Google Play non sia installato, è possibile creare un'immagine dell'emulatore per lo sviluppo. Per ulteriori informazioni, vedere [Gestione periferiche](~/android/get-started/installation/android-emulator/device-manager.md).

## <a name="the-googlemap-class"></a>La classe GoogleMap

Una volta soddisfatti i prerequisiti, è il momento di iniziare a sviluppare l'applicazione e utilizzare l'API di Android Maps. La classe [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) è l'API principale che un'applicazione Xamarin.Android utilizzerà per visualizzare e interagire con Google Maps per Android. Questa classe ha le seguenti responsabilità:

- Interagire con i servizi Google Play per autorizzare l'applicazione con il servizio web Google.

- Download, memorizzazione nella cache e visualizzazione dei riquadri della mappa.

- Visualizzazione di controlli dell'interfaccia utente, ad esempio panoramica e zoom per l'utente.

- Disegnare marcatori e forme geometriche sulle mappe.

L'oggetto `GoogleMap` viene aggiunto a un'attività in uno dei due modi seguenti:The is added to an Activity in one of two ways:

- **MapFragment** - Il [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) è un frammento `GoogleMap` specializzato che funge da host per l'oggetto. Il `MapFragment` richiede Android API livello 12 o superiore.
   Le versioni precedenti di Android possono utilizzare il [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment).  Questa guida si concentrerà sull'utilizzo della `MapFragment` classe.

- **MapView** - il [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) è una sottoclasse di visualizzazione `GoogleMap` specializzata, che può fungere da host per un oggetto. Gli utenti di questa classe devono inoltrare `MapView` tutti i metodi del ciclo di vita Activity alla classe.

Ognuno di questi `Map` contenitori espone una `GoogleMap`proprietà che restituisce un'istanza di . Preferenza deve essere data per il [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) classe in quanto è un'API più semplice che riduce la quantità di codice boilerplate che uno sviluppatore deve implementare manualmente.

### <a name="adding-a-mapfragment-to-an-activity"></a>Aggiunta di un MapFragment a un'attivitàAdding a MapFragment to an Activity

La schermata seguente è `MapFragment`un esempio di un semplice:

[![Screenshot di un dispositivo che visualizza un frammento di Google Map](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Analogamente ad altre classi Fragment, `MapFragment` esistono due modi per aggiungere un oggetto a un'attività:Similar to other Fragment classes, there are two ways to add a to an Activity:

- **In modo dichiarativo:** l'oggetto `MapFragment` può essere aggiunto tramite il file di layout XML per l'attività. Il frammento XML seguente mostra un `fragment` esempio di come usare l'elemento:The following XML snippet shows an example of how to use the element:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

- **A livello di codice:** è possibile creare un'istanza a livello di codice utilizzando il metodo e quindi aggiunto a un Activity.Programmatically - The `MapFragment` can be programmatically instantiated using the [`MapFragment.NewInstance`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) method and then added to an Activity. Questo frammento di codice `MapFragment` mostra il modo più semplice per creare un'istanza di un oggetto e aggiungerlo a un'attività:This snippet shows the simplest way to instantiate a object and add to an Activity:

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    È possibile configurare `MapFragment` l'oggetto [`GoogleMapOptions`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) passando `NewInstance`un oggetto a . Questo argomento è illustrato nella sezione [Proprietà di GoogleMap](#googlemap_object) che appare più avanti in questa guida.

Il `MapFragment.GetMapAsync` metodo viene utilizzato [`GoogleMap`](#googlemap_object) per inizializzare l'oggetto ospitato dal frammento e ottenere `MapFragment`un riferimento all'oggetto mappa ospitato dall'oggetto . Questo metodo accetta un `IOnMapReadyCallback` oggetto che implementa l'interfaccia.

Questa interfaccia dispone di `IMapReadyCallback.OnMapReady(MapFragment map)` un singolo metodo, che verrà richiamato quando `GoogleMap` è possibile che l'app interagisca con l'oggetto. Il frammento di codice seguente mostra `MapFragment` come `IOnMapReadyCallback` un'attività Android può inizializzare un e implementare l'interfaccia:The following code snippet shows how an Android Activity can initialize a and implement the interface:

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

### <a name="map-types"></a>Tipi di mappa

Esistono cinque diversi tipi di mappe disponibili dall'API di Google Maps:

- **Normale** - Questo è il tipo di mappa predefinito. Mostra strade e importanti caratteristiche naturali insieme ad alcuni punti artificiali di interesse (come edifici e ponti).

- **Satellite** - Questa mappa mostra la fotografia satellitare.

- **Ibrido** - Questa mappa mostra la fotografia satellitare e le mappe stradali.

- **Terreno** - Questo mostra principalmente caratteristiche topografiche con alcune strade.

- **Nessuno:** questa mappa non carica alcun riquadro, viene eseguito il rendering come una griglia vuota.

L'immagine seguente mostra tre dei diversi tipi di mappe, da sinistra a destra (normale, ibrido, terreno):

[![Schermate di esempio di tre mappe: Normale, Ibrido e Terreno](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

La `GoogleMap.MapType` proprietà viene utilizzata per impostare o modificare il tipo di mappa visualizzata. Il frammento di codice seguente viene illustrato come visualizzare una mappa satellite.

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```

### <a name="googlemap-properties"></a><a name="googlemap_object" />Proprietà di GoogleMap

`GoogleMap`definisce diverse proprietà che possono controllare la funzionalità e l'aspetto della mappa. Un modo per configurare `GoogleMap` lo stato iniziale di un `MapFragment`oggetto consiste nel passare un oggetto [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) durante la creazione di un oggetto . Il frammento di codice seguente `GoogleMapOptions` è un `MapFragment`esempio di utilizzo di un oggetto durante la creazione di un:

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

L'altro modo `GoogleMap` per configurare un consiste nel modificare le proprietà nell'oggetto [UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) dell'oggetto mappa. L'esempio di codice seguente `GoogleMap` viene illustrato come configurare un per visualizzare i controlli di zoom e una bussola:The next code sample shows how to configure a to display the zoom controls and a compass:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>Interazione con GoogleMap

L'API Mappe Android fornisce API che consentono a un'attività di modificare il punto di vista, aggiungere marcatori, inserire sovrapposizioni personalizzate o disegnare forme geometriche. Questa sezione verrà illustrato come eseguire alcune di queste attività in Xamarin.Android.This section will discuss how to accomplish some of these tasks in Xamarin.Android.

### <a name="changing-the-viewpoint"></a>Modifica del punto di vista

Le mappe sono modellate come un piano piatto sullo schermo, basato sulla proiezione Mercatore. La vista mappa è quella di una *telecamera* che guarda verso il basso su questo piano. La posizione della fotocamera può essere controllata modificando la posizione, lo zoom, l'inclinazione e il cuscinetto. La classe [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) viene utilizzata per spostare la posizione della fotocamera. `CameraUpdate`non viene creata direttamente un'istanza degli oggetti, ma l'API Maps fornisce la classe [CameraUpdateFactory.](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory)

Una `CameraUpdate` volta creato, un oggetto viene passato come parametro ai metodi [GoogleMap.MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) o [GoogleMap.AnimateCamera.](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) Il `MoveCamera` metodo aggiorna immediatamente la `AnimateCamera` mappa mentre il metodo fornisce una transizione animata e uniforme.

Questo frammento di codice è un `CameraUpdateFactory` semplice `CameraUpdate` esempio di come usare l'oggetto per creare un che incrementerà il livello di zoom della mappa di un livello di zoom:This code snippet is a simple example of how to use the to create a that will increment the zoom level of the map by one zoom level:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

L'API Mappe fornisce una [CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) che aggregherà tutti i valori possibili per la posizione della fotocamera. Un'istanza di questa classe può essere fornita al metodo [CameraUpdateFactory.NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) che restituirà un `CameraUpdate` oggetto. L'API Mappe include anche la classe [CameraPosition.Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) che fornisce un'API fluente per la creazione di `CameraPosition` oggetti.
Il frammento di codice seguente `CameraUpdate` mostra `CameraPosition` un esempio di creazione di `GoogleMap`un da a e di utilizzo che per modificare la posizione della fotocamera in un:The following code snippet shows an example of creating a from a and using that to change the camera position on a:

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

Nel frammento di codice precedente, una posizione specifica sulla mappa è rappresentata dalla classe [LatLng.](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) Il livello di zoom è impostato su 18, che è una misura arbitraria dello zoom utilizzato da Google Maps. Il cuscinetto è la misurazione della bussola in senso orario da Nord. La proprietà Tilt controlla l'angolo di visualizzazione e specifica un angolo di 25 gradi dalla verticale. La schermata `GoogleMap` seguente mostra il codice dopo l'esecuzione del codice precedente:The following screenshot shows the after executing the preceding code:

[![Esempio Google Map che mostra una posizione specificata con un angolo di visualizzazione inclinato](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)

### <a name="drawing-on-the-map"></a>Disegno sulla mappa

L'API di Android Maps fornisce API per disegnare i seguenti elementi su una mappa:

- **Marcatori:** si tratta di icone speciali che vengono utilizzate per identificare una singola posizione su una mappa.

- **Sovrapposizioni** - Si tratta di un'immagine che può essere utilizzata per identificare una raccolta di posizioni o aree sulla mappa.

- **Linee, poligoni e cerchi:** si tratta di API che consentono alle attività di aggiungere forme a una mappa.

#### <a name="markers"></a>Indicatori

L'API Mappe fornisce una classe [Marker](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) che incapsula tutti i dati relativi a una singola posizione in una mappa. Per impostazione predefinita, la classe Marker utilizza un'icona standard fornita da Google Maps. È possibile personalizzare l'aspetto di un marcatore e rispondere ai clic dell'utente.

##### <a name="adding-a-marker"></a>Aggiunta di un marcatore

Per aggiungere un marcatore a una mappa, è necessario creare un nuovo `GoogleMap` [oggetto MarkerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) e quindi chiamare il metodo [AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) su un'istanza. Questo metodo restituirà un [Marker](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) oggetto.

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

Il titolo del marcatore verrà visualizzato in una *finestra di informazioni* quando l'utente tocca il marcatore. La schermata seguente mostra l'aspetto di questo marcatore:The following screenshot shows what this marker looks like:

[![Esempio Google Map con un marcatore e una finestra di informazioni per Vimy Ridge](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)

##### <a name="customizing-a-marker"></a>Personalizzazione di un marcatore

È possibile personalizzare l'icona utilizzata dal marcatore chiamando il `MarkerOptions.InvokeIcon` metodo quando si aggiunge il marcatore alla mappa.
Questo metodo accetta un [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) oggetto contenente i dati necessari per eseguire il rendering dell'icona. La classe [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory) fornisce alcuni metodi di `BitmapDescriptor`supporto per semplificare la creazione di un oggetto . Nell'elenco seguente vengono introdotti alcuni di questi metodi:

- `DefaultMarker(float colour)`&ndash; Utilizza il marcatore predefinito di Google Maps, ma cambia il colore.

- `FromAsset(string assetName)`&ndash; Utilizzate un'icona personalizzata del file specificato nella cartella Assets.

- `FromBitmap(Bitmap image)`&ndash; Utilizzare la bitmap specificata come icona.

- `FromFile(string fileName)`&ndash; Creare l'icona personalizzata dal file nel percorso specificato.

- `FromResource(int resourceId)`&ndash; Creare un'icona personalizzata dalla risorsa specificata.

Il frammento di codice seguente mostra un esempio di creazione di un marcatore predefinito di colore ciano:The following code snippet shows an example of creating a cian coloured default marker:

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

*Le finestre di informazioni* sono finestre speciali che popup visualizzano informazioni all'utente quando toccano un marcatore specifico. Per impostazione predefinita, nella finestra delle informazioni verrà visualizzato il contenuto del titolo del marcatore. Se il titolo non è stato assegnato, non verrà visualizzata alcuna finestra di informazioni. È possibile visualizzare una sola finestra di informazioni alla volta.

È possibile personalizzare la finestra delle informazioni implementando l'interfaccia [GoogleMap.IInfoWindowAdapter.](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) Esistono due metodi importanti su questa interfaccia:There are two important methods on this interface:

- `public View GetInfoWindow(Marker marker)`&ndash; Questo metodo viene chiamato per ottenere una finestra di informazioni personalizzata per un marcatore. Se restituisce `null` , verrà utilizzato il rendering predefinito della finestra. Se questo metodo restituisce un View, tale visualizzazione verrà inserita all'interno della cornice della finestra di informazioni.

- `public View GetInfoContents(Marker marker)`&ndash; Questo metodo verrà chiamato solo se `null` GetInfoWindow restituisce . Questo metodo può `null` restituire un valore se deve essere utilizzato il rendering predefinito del contenuto della finestra informazioni. In caso contrario, questo metodo deve restituire un View con il contenuto della finestra di informazioni.

Una finestra di informazioni non è una visualizzazione dal vivo, invece Android convertirà la visualizzazione in una bitmap statica e la visualizzerà nell'immagine. Ciò significa che una finestra di informazioni non può rispondere a eventi tocco o gesti, né si aggiornerà automaticamente. Per aggiornare una finestra di informazioni, è necessario chiamare il [metodo GoogleMap.ShowInfoWindow.To](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) update an info window, it is necessary to call the GoogleMap.ShowInfoWindow method.

L'immagine seguente mostra alcuni esempi di alcune finestre di informazioni personalizzate. L'immagine a sinistra ha il suo contenuto personalizzato, mentre l'immagine a destra ha la sua finestra e il contenuto personalizzato con angoli arrotondati:

![Esempio di finestre dei marcatori per Melbourne, incluse l'icona e la popolazione. La finestra di destra ha angoli arrotondati.](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

A differenza dei marcatori, che identificano una posizione specifica su una mappa, un [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) è un'immagine utilizzata per identificare una raccolta di posizioni o un'area sulla mappa.

##### <a name="adding-a-groundoverlay"></a>Aggiunta di un GroundOverlay

L'aggiunta di una sovrapposizione di terreno a una mappa è simile all'aggiunta di un marcatore a una mappa. Innanzitutto, viene creato un oggetto [GroundOverlayOptions.](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) Questo oggetto viene quindi passato [`GoogleMap.AddGroundOverlay`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) come parametro al `GroundOverlay` metodo , che restituirà un oggetto. Questo frammento di codice è un esempio di aggiunta di una sovrapposizione di terra a una mappa:This code snippet is an example of adding a ground overlay to a map:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

La schermata seguente mostra questa sovrapposizione su una mappa:The following screenshot shows this overlay on a map:

[![Mappa di esempio con un'immagine sovrapposta di un orso polare](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)

#### <a name="lines-circles-and-polygons"></a>Linee, cerchi e poligoni

Esistono tre semplici tipi di figure geometriche che possono essere aggiunte a una mappa:

- **Polilinea** - Si tratta di una serie di segmenti di linea collegati. Può contrassegnare un tracciato su una mappa o creare una forma geometrica.

- **Cerchio** - Questo disegna un cerchio sulla mappa.

- **Poligono** - Si tratta di una forma chiusa per contrassegnare le aree su una mappa.

##### <a name="polylines"></a>Polilinee

Una [polilinea](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) è un `LatLng` elenco di oggetti consecutivi che specificano i vertici di ogni segmento di linea. Una polilinea viene creata `PolylineOptions` creando innanzitutto un oggetto e aggiungendovi i punti. L'oggetto `PolylineOption` viene quindi `GoogleMap` passato a `AddPolyline` un oggetto chiamando il metodo .

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

I cerchi vengono creati creando innanzitutto un'istanza di un oggetto [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) che specificherà il centro e il raggio del cerchio in metri. Il cerchio viene disegnato sulla mappa chiamando [GoogleMap.AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions)).
Il frammento di codice seguente mostra come disegnare un cerchio:The following code snippet shows how to draw a circle:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```

##### <a name="polygons"></a>Poligoni

`Polygon`s sono `Polyline`simili a s, tuttavia non sono aperti ended. `Polygon`s sono un anello chiuso e hanno il loro interno riempito.
`Polygon`s vengono creati esattamente nello `Polyline`stesso modo di un , ad eccezione del metodo [GoogleMap.AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) richiamato.

A `Polyline`differenza `Polygon` di un oggetto , a è autochiusura. Il poligono verrà chiuso `AddPolygon` con il metodo disegnando una linea che collega il primo e l'ultimo punto. Il frammento di codice seguente creerà un rettangolo solido `Polyline` sulla stessa area del frammento di codice precedente nell'esempio.

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

Esistono tre tipi di interazioni che un utente può avere con una mappa:

- **Clic marcatore** - L'utente fa clic su un marcatore.

- **Marker Drag** - L'utente ha cliccato a lungo su un mparger

- **Info Finestra Clic** - L'utente ha fatto clic su una finestra di informazioni.

Ognuno di questi eventi sarà discusso in modo più dettagliato di seguito.

### <a name="marker-click-events"></a>Eventi clic marcatore

L'evento `MarkerClicked` viene generato quando l'utente tocca un marcatore. Questo evento accetta `GoogleMap.MarkerClickEventArgs` un oggetto come parametro. Questa classe contiene due proprietà:This class contains two properties:

- `GoogleMap.MarkerClickEventArgs.Handled`&ndash; Questa proprietà deve `true` essere impostata su per indicare che il gestore eventi ha utilizzato l'evento. Se è impostato `false` su, si verificherà il comportamento predefinito oltre al comportamento personalizzato del gestore eventi.

- `Marker`&ndash; Questa proprietà è un riferimento al `MarkerClick` marcatore che ha generato l'evento.

Questo frammento di codice `MarkerClick` mostra un esempio di un che cambierà la posizione della fotocamera in una nuova posizione sulla mappa:This code snippet shows an example of a that will change the camera position to a new location on the map:

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

### <a name="marker-drag-events"></a>Eventi di trascinamento dei marcatori

Questo evento viene generato quando l'utente desidera trascinare il marcatore. Per impostazione predefinita, i marcatori non sono trascinabili. Un marcatore può essere impostato come `Marker.Draggable` trascinabile impostando la proprietà su `true` o richiamando il `MarkerOptions.Draggable` metodo con `true` come parametro.

Per trascinare il marcatore, l'utente deve prima fare clic a lungo sul marcatore e quindi il dito deve rimanere sulla mappa. Quando il dito dell'utente viene trascinato sullo schermo, il marcatore si sposterà. Quando il dito dell'utente solleva lo schermo, il marcatore rimarrà in posizione.

Nell'elenco seguente vengono descritti i vari eventi che verranno generati per un marcatore trascinabile:

- `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)`&ndash; Questo evento viene generato quando l'utente trascina per la prima volta il marcatore.

- `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)`&ndash; Questo evento viene generato durante il trascinamento del marcatore.

- `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)`&ndash; Questo evento viene generato quando l'utente ha terminato di trascinare il marcatore.

Ognuno `EventArgs` di essi contiene `P0` una singola proprietà `Marker` denominata che è un riferimento all'oggetto trascinato.

### <a name="info-window-click-events"></a>Eventi click della finestra Info

È possibile visualizzare una sola finestra informazioni alla volta. Quando l'utente fa clic su una finestra di informazioni `InfoWindowClick` in una mappa, l'oggetto mappa genererà un evento. Il frammento di codice seguente mostra come collegare un gestore all'evento:The following code snippet shows how to wire up a handler to the event:

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

Tenere presente che una `View` finestra di informazioni è una finestra statica che viene eseguito il rendering come un'immagine sulla mappa. Tutti i widget, ad esempio pulsanti, caselle di controllo o visualizzazioni di testo inserite all'interno della finestra delle informazioni, saranno inerti e non potranno rispondere a nessuno dei relativi eventi utente integrali.

## <a name="related-links"></a>Collegamenti correlati

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play Services](https://developers.google.com/android/guides/overview)
- [API Android di Google Maps v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [Google Play Services APK](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Ottenere una chiave API di Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element)
