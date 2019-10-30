---
title: Uso dell'API Google Maps nell'applicazione
description: Come implementare le funzionalità di Google Maps API v2 nell'applicazione Novell. Android.
ms.prod: xamarin
ms.assetid: C0589878-2D04-180E-A5B9-BB41D5AF6E02
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: adcfb1457742d343f87a602885566107cf327e2d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027146"
---
# <a name="using-the-google-maps-api-in-your-application"></a>Uso dell'API Google Maps nell'applicazione

L'uso dell'applicazione Maps è molto utile, ma a volte si desidera includere le mappe direttamente nell'applicazione. Oltre all'applicazione Maps predefinita, Google offre anche un' [API di mapping nativo per Android](https://developers.google.com/maps/documentation/android-sdk/intro).
L'API Maps è adatta ai casi in cui si desidera mantenere un maggiore controllo sull'esperienza di mapping. Gli elementi possibili con l'API Maps includono:

- Modifica a livello di codice del punto di vista della mappa.
- Aggiunta e personalizzazione dei marcatori.
- Annotazione di una mappa con sovrimpressioni.

Diversamente dall'API di Google Maps Android V1, Google Maps Android API v2 fa parte di [Google Play Services](https://developers.google.com/android/guides/overview).
Prima di poter usare l'API Google Maps Android, un'app Novell. Android deve soddisfare alcuni prerequisiti obbligatori.

## <a name="google-maps-api-prerequisites"></a>Prerequisiti per l'API di Google Maps

Prima di poter usare l'API Maps, è necessario eseguire diversi passaggi, tra cui:

- [Ottenere una chiave API Maps](#obtain-maps-key)
- [Installare l'SDK di Google Play Services](#install-gps-sdk)
- [Installare il pacchetto Novell. GooglePlayServices. Maps da NuGet](#install-gpsmaps-nuget)
- [Specificare le autorizzazioni necessarie](#declare-permissions)
- [Facoltativamente, creare un emulatore con le API Google](#create-emulator-with-google-api)

### <a name="a-nameobtain-maps-key-obtain-a-google-maps-api-key"></a><a name="obtain-maps-key" />ottenere una chiave API di Google Maps

Il primo passaggio consiste nell'ottenere una chiave API di Google Maps (si noti che non è possibile riutilizzare una chiave API dall'API di Google Maps V1 legacy). Per informazioni su come ottenere e usare la chiave API con Novell. Android, vedere [ottenere una chiave API di Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

### <a name="a-nameinstall-gps-sdk--install-the-google-play-services-sdk"></a><a name="install-gps-sdk" /> installare SDK Google Play Services

Google Play Services è una tecnologia di Google che consente alle applicazioni Android di sfruttare le varie funzionalità di Google, ad esempio Google +, Fatturazione in-app e Maps. Queste funzionalità sono accessibili nei dispositivi Android come servizi in background, che sono contenuti in [Google Play Services apk](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en).

Le applicazioni Android interagiscono con Google Play Services tramite la libreria client Google Play Services. Questa libreria contiene le interfacce e le classi per i singoli servizi, ad esempio maps. Il diagramma seguente mostra la relazione tra un'applicazione Android e Google Play Services:

![Diagramma che illustra la Google Play Store aggiornamento dell'APK Google Play Services](maps-api-images/play-services-diagram.png)

L'API Maps Android viene fornita come parte di Google Play Services.
Prima che un'applicazione Novell. Android possa usare l'API Maps, è necessario installare Google Play Services SDK usando [gestione Android SDK](~/android/get-started/installation/android-sdk.md). Lo screenshot seguente illustra il punto in cui è possibile trovare il client di Google Play Services in Android SDK Manager:

![Google Play Services viene visualizzato in extra in gestione Android SDK](maps-api-images/image01.png)

> [!NOTE]
> Il Google Play APK Services è un prodotto concesso in licenza che potrebbe non essere presente in tutti i dispositivi. Se non è installato, Google Maps non funzionerà sul dispositivo.

### <a name="a-nameinstall-gpsmaps-nuget--install-the-xamaringoogleplayservicesmaps-package-from-nuget"></a><a name="install-gpsmaps-nuget" /> installare il pacchetto Novell. GooglePlayServices. Maps da NuGet

Il [pacchetto Novell. GooglePlayServices. Maps](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Maps) contiene le associazioni Novell. Android per l'API Google Play Services maps.
Per aggiungere il pacchetto della mappa Google Play Services, fare clic con il pulsante destro del mouse sulla cartella **riferimenti** del progetto nel Esplora soluzioni e scegliere **Gestisci pacchetti NuGet...** :

![Esplora soluzioni visualizzazione della voce di menu di scelta rapida Gestisci pacchetti NuGet in riferimenti](maps-api-images/image02.png)

Verrà aperto **Gestione pacchetti NuGet**. Fare clic su **Sfoglia** e immettere **Novell Google Play Services Maps** nel campo di ricerca. Selezionare **Novell. GooglePlayServices. Maps** e fare clic su **Installa**. (Se il pacchetto è stato installato in precedenza, fare clic su **Aggiorna**):

[![gestione pacchetti NuGet con il pacchetto Novell. GooglePlayServices. Maps selezionato](maps-api-images/image03-sml.png)](maps-api-images/image03.png#lightbox)

Si noti che vengono installati anche i pacchetti di dipendenza seguenti:

- **Novell. GooglePlayServices. base**
- **Novell. GooglePlayServices. scantinato**
- **Novell. GooglePlayServices. Tasks**

### <a name="a-namedeclare-permissions--specify-the-required-permissions"></a><a name="declare-permissions" /> specificare le autorizzazioni necessarie

Per poter usare l'API di Google Maps, le app devono identificare i requisiti hardware e di autorizzazione.  Alcune autorizzazioni vengono concesse automaticamente da Google Play Services SDK e non è necessario che uno sviluppatore le aggiunga in modo esplicito a **AndroidManfest. XML**:

- **Accesso allo stato della rete** &ndash; l'API Maps deve essere in grado di controllare se è possibile scaricare i riquadri della mappa.

- Accesso a **internet** &ndash; accesso a Internet è necessario per scaricare i riquadri della mappa e comunicare con i server Google Play per l'accesso all'API.

È necessario specificare le autorizzazioni e le funzionalità seguenti in **file AndroidManifest. XML** per l'API Android di Google Maps:

- **OpenGL ES v2** &ndash; l'applicazione deve dichiarare il requisito per OpenGL ES V2.

- **Chiave API di Google Maps** &ndash; la chiave API viene usata per verificare che l'applicazione sia registrata e autorizzata a usare Google Play Services. Per informazioni dettagliate su questa chiave, vedere [ottenere una chiave API di Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md) .

- **Richiedere il client HTTP Apache legacy** &ndash; app destinate a Android 9,0 (livello API 28) o superiore devono specificare che il client HTTP Apache legacy è una libreria facoltativa da usare.

- **Accesso ai servizi basati su Google web** &ndash; l'applicazione deve disporre delle autorizzazioni per accedere ai servizi Web di Google che eseguono l'API Maps Android.

- Le **autorizzazioni per le notifiche Google Play Services** &ndash; all'applicazione devono essere concesse le autorizzazioni per la ricezione di notifiche remote da Google Play Services.

- **Accesso ai provider di posizione** &ndash; si tratta di autorizzazioni facoltative.
   Consentiranno alla classe `GoogleMap` di visualizzare la posizione del dispositivo sulla mappa.

Android 9 ha inoltre rimosso la libreria client Apache HTTP dal bootclasspath, quindi non è disponibile per le applicazioni destinate all'API 28 o versioni successive. È necessario aggiungere la riga seguente al nodo `application` del file **file AndroidManifest. XML** per continuare a usare il client HTTP Apache nelle applicazioni destinate all'API 28 o versioni successive:

```xml
<application ...>
   ...
   <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

> [!NOTE]
> Nelle versioni molto vecchie di Google Play SDK è necessaria un'app per richiedere l'autorizzazione `WRITE_EXTERNAL_STORAGE`. Questo requisito non è più necessario con le associazioni Novell recenti per Google Play Services.

Il frammento di codice seguente è un esempio delle impostazioni che devono essere aggiunte a **file AndroidManifest. XML**:

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

Oltre a richiedere le autorizzazioni **file AndroidManifest. XML**, un'app deve anche eseguire controlli di autorizzazione Runtime per le autorizzazioni `ACCESS_COARSE_LOCATION` e `ACCESS_FINE_LOCATION`. Per ulteriori informazioni sull'esecuzione dei controlli delle autorizzazioni in fase di esecuzione, vedere la Guida alle [autorizzazioni di Novell. Android](~/android/app-fundamentals/permissions.md) .

### <a name="a-namecreate-emulator-with-google-api-create-an-emulator-with-google-apis"></a><a name="create-emulator-with-google-api" />creare un emulatore con Google APIs

Nel caso in cui non sia installato un dispositivo Android fisico con Google Play Services, è possibile creare un'immagine dell'emulatore per lo sviluppo. Per ulteriori informazioni, vedere la [Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).

## <a name="the-googlemap-class"></a>Classe GoogleMap

Una volta soddisfatti i prerequisiti, è possibile iniziare a sviluppare l'applicazione e usare l'API Maps Android. La classe [GoogleMap](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) è l'API principale che verrà usata da un'applicazione Novell. Android per visualizzare e interagire con Google Maps per Android. Questa classe ha le responsabilità seguenti:

- Interazione con i servizi di Google Play per autorizzare l'applicazione con il servizio Web Google.

- Download, memorizzazione nella cache e visualizzazione dei riquadri della mappa.

- Visualizzazione di controlli dell'interfaccia utente, ad esempio Pan e zoom all'utente.

- Disegno di marcatori e forme geometriche nelle mappe.

Il `GoogleMap` viene aggiunto a un'attività in uno dei due modi seguenti:

- **MapFragment** : [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) è un frammento specializzato che funge da host per l'oggetto `GoogleMap`. Il `MapFragment` richiede il livello di API Android 12 o versione successiva.
   Le versioni precedenti di Android possono usare [SupportMapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/SupportMapFragment).  Questa guida si concentra sull'uso della classe `MapFragment`.

- **MapView** : [MapView](https://developers.google.com/android/reference/com/google/android/gms/maps/MapView) è una sottoclasse di visualizzazione specializzata che può fungere da host per un oggetto `GoogleMap`. Gli utenti di questa classe devono trasmettere tutti i metodi del ciclo di vita delle attività alla classe `MapView`.

Ognuno di questi contenitori espone una proprietà di `Map` che restituisce un'istanza di `GoogleMap`. È necessario assegnare una preferenza alla classe [MapFragment](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment) , poiché si tratta di un'API più semplice che riduce il codice standard della quantità che uno sviluppatore deve implementare manualmente.

### <a name="adding-a-mapfragment-to-an-activity"></a>Aggiunta di un MapFragment a un'attività

Lo screenshot seguente è un esempio di `MapFragment`semplice:

[![screenshot di un dispositivo che visualizza un frammento di Google Map](maps-api-images/image05-sml.png)](maps-api-images/image05.png#lightbox)

Analogamente ad altre classi di frammenti, è possibile aggiungere una `MapFragment` a un'attività in due modi:

- In **modo dichiarativo** : è possibile aggiungere la `MapFragment` tramite il file di layout XML per l'attività. Il frammento di codice XML seguente mostra un esempio di come usare l'elemento `fragment`:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <fragment xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/map"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              class="com.google.android.gms.maps.MapFragment" />
    ```

- **A livello di codice** : è possibile creare un'istanza del `MapFragment` a livello di codice usando il metodo [`MapFragment.NewInstance`](https://developers.google.com/android/reference/com/google/android/gms/maps/MapFragment.html#newInstance()) e quindi aggiungerla a un'attività. Questo frammento di codice mostra il modo più semplice per creare un'istanza di un oggetto `MapFragment` e aggiungerlo a un'attività:

    ```csharp
        var mapFrag = MapFragment.NewInstance();
        activity.FragmentManager.BeginTransaction()
                                .Add(Resource.Id.map_container, mapFrag, "map_fragment")
                                .Commit();

    ```

    È possibile configurare l'oggetto `MapFragment` passando un oggetto [`GoogleMapOptions`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) a `NewInstance`. Questa procedura viene descritta nella sezione [proprietà di GoogleMap](#googlemap_object) che viene visualizzata più avanti in questa guida.

Il metodo `MapFragment.GetMapAsync` viene usato per inizializzare la [`GoogleMap`](#googlemap_object) ospitata dal frammento e ottenere un riferimento all'oggetto map ospitato dal `MapFragment`. Questo metodo accetta un oggetto che implementa l'interfaccia `IOnMapReadyCallback`.

Questa interfaccia ha un solo metodo, `IMapReadyCallback.OnMapReady(MapFragment map)` che verrà richiamato quando è possibile che l'app interagisca con l'oggetto `GoogleMap`. Il frammento di codice seguente mostra come un'attività Android può inizializzare una `MapFragment` e implementare l'interfaccia `IOnMapReadyCallback`:

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

Dall'API Google Maps sono disponibili cinque diversi tipi di mappe:

- **Normale** : tipo di mappa predefinito. Mostra le strade e le funzionalità naturali importanti insieme ad alcuni punti di interesse artificiali, ad esempio edifici e Bridge.

- **Satellite** : questa mappa mostra la fotografia satellite.

- **Ibrido** : questa mappa mostra la fotografia satellite e le mappe stradali.

- **Terrain** : Mostra principalmente le funzionalità topografiche con alcune strade.

- **None** : questa mappa non carica i riquadri, ne viene eseguito il rendering come griglia vuota.

L'immagine seguente mostra tre tipi diversi di mappe, da sinistra a destra (normale, ibrido, terreno):

[![tre schermate di esempio della mappa: normale, ibrido e terreno](maps-api-images/map-types-sml.png)](maps-api-images/map-types.png#lightbox)

La proprietà `GoogleMap.MapType` viene utilizzata per impostare o modificare il tipo di mappa visualizzato. Il frammento di codice seguente mostra come visualizzare una mappa satellite.

```csharp
public void OnMapReady(GoogleMap map)
{
    map.MapType = GoogleMap.MapTypeHybrid;
}
```

### <a name="a-namegooglemap_object-googlemap-properties"></a>Proprietà di <a name="googlemap_object" />GoogleMap

`GoogleMap` definisce diverse proprietà che consentono di controllare la funzionalità e l'aspetto della mappa. Un modo per configurare lo stato iniziale di un `GoogleMap` consiste nel passare un oggetto [GoogleMapOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMapOptions) durante la creazione di un `MapFragment`. Il frammento di codice seguente è un esempio di utilizzo di un oggetto `GoogleMapOptions` durante la creazione di un `MapFragment`:

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

L'altro modo per configurare un `GoogleMap` consiste nel modificare le proprietà di [UiSettings](https://developers.google.com/android/reference/com/google/android/gms/maps/UiSettings) dell'oggetto map. Nell'esempio di codice seguente viene illustrato come configurare un `GoogleMap` per visualizzare i controlli zoom e una bussola:

```csharp
public void OnMapReady(GoogleMap map)
{
    map.UiSettings.ZoomControlsEnabled = true;
    map.UiSettings.CompassEnabled = true;
}
```

## <a name="interacting-with-the-googlemap"></a>Interazione con GoogleMap

L'API Maps Android fornisce le API che consentono a un'attività di modificare il punto di vista, aggiungere marcatori, inserire sovrapposizioni personalizzate o tracciare forme geometriche. In questa sezione viene illustrato come eseguire alcune di queste attività in Novell. Android.

### <a name="changing-the-viewpoint"></a>Modifica del punto di vista

Le mappe sono modellate come un piano Flat sullo schermo, sulla base della proiezione Mercator. La vista mappa è quella di una *fotocamera* che guarda direttamente il piano. La posizione della fotocamera può essere controllata cambiando la posizione, lo zoom, l'inclinazione e il cuscinetto. La classe [CameraUpdate](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdate) viene usata per spostare la posizione della fotocamera. non viene creata un'istanza diretta degli oggetti `CameraUpdate`, ma l'API Maps fornisce la classe [CameraUpdateFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/CameraUpdateFactory) .

Una volta creato un oggetto `CameraUpdate`, viene passato come parametro ai metodi [googlemap. MoveCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#moveCamera(com.google.android.gms.maps.CameraUpdate)) o [googlemap. AnimateCamera](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap#animateCamera(com.google.android.gms.maps.CameraUpdate)) . Il metodo `MoveCamera` aggiorna immediatamente la mappa mentre il metodo `AnimateCamera` fornisce una transizione animata e uniforme.

Questo frammento di codice è un semplice esempio di utilizzo del `CameraUpdateFactory` per creare un `CameraUpdate` che incrementerà il livello di zoom della mappa di un livello di zoom:

```csharp
MapFragment mapFrag = (MapFragment) FragmentManager.FindFragmentById(Resource.Id.my_mapfragment_container);
mapFrag.GetMapAsync(this);
...

public void OnMapReady(GoogleMap map)
{   
    map.MoveCamera(CameraUpdateFactory.ZoomIn());
}
```

L'API Maps fornisce un [CameraPosition](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.html) che aggrega tutti i valori possibili per la posizione della fotocamera. Un'istanza di questa classe può essere fornita al metodo [CameraUpdateFactory. NewCameraPosition](https://developers.google.com/maps/documentation/android/reference/com/google/android/gms/maps/CameraUpdateFactory#newCameraPosition%28com.google.android.gms.maps.model.CameraPosition%29) che restituirà un oggetto `CameraUpdate`. L'API Maps include anche la classe [CameraPosition. Builder](https://developer.android.com/reference/com/google/android/gms/maps/model/CameraPosition.Builder.html) che fornisce un'API Fluent per la creazione di oggetti `CameraPosition`.
Il frammento di codice seguente mostra un esempio di creazione di un `CameraUpdate` da un `CameraPosition` e di utilizzo di tale per modificare la posizione della fotocamera in un `GoogleMap`:

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

Nel frammento di codice precedente, una posizione specifica sulla mappa è rappresentata dalla classe [LatLng](https://developers.google.com/android/reference/com/google/android/gms/maps/model/LatLng) . Il livello di zoom è impostato su 18, ovvero una misura arbitraria dello zoom utilizzata da Google Maps. Il cuscinetto è la misura della bussola in senso orario dal Nord. La proprietà Tilt controlla l'angolo di visualizzazione e specifica un angolo di 25 gradi dall'oggetto verticale. Lo screenshot seguente mostra il `GoogleMap` dopo l'esecuzione del codice precedente:

[![esempio Google Map che mostra una posizione specificata con angolo di visualizzazione inclinato](maps-api-images/image06-sml.png)](maps-api-images/image06.png#lightbox)

### <a name="drawing-on-the-map"></a>Disegno sulla mappa

L'API Maps di Android fornisce le API per disegnare gli elementi seguenti su una mappa:

- **Marcatori** : si tratta di icone speciali che consentono di identificare un'unica posizione su una mappa.

- **Sovrapposizioni** : si tratta di un'immagine che può essere usata per identificare una raccolta di posizioni o area sulla mappa.

- **Linee, poligoni e cerchi** : si tratta di API che consentono alle attività di aggiungere forme a una mappa.

#### <a name="markers"></a>Markers

L'API Maps fornisce una classe [marker](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) che incapsula tutti i dati relativi a una singola posizione su una mappa. Per impostazione predefinita, la classe marcatore usa un'icona standard fornita da Google Maps. È possibile personalizzare l'aspetto di un marcatore e rispondere ai clic dell'utente.

##### <a name="adding-a-marker"></a>Aggiunta di un marcatore

Per aggiungere un marcatore a una mappa, è necessario creare un nuovo oggetto [markerOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/MarkerOptions) e quindi chiamare il metodo [AddMarker](https://developer.android.com/reference/com/google/android/gms/maps/GoogleMap.html#addMarker%28com.google.android.gms.maps.model.MarkerOptions%29) su un'istanza di `GoogleMap`. Questo metodo restituirà un oggetto [marcatore](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker) .

```csharp
public void OnMapReady(GoogleMap map)
{
    MarkerOptions markerOpt1 = new MarkerOptions();
    markerOpt1.SetPosition(new LatLng(50.379444, 2.773611));
    markerOpt1.SetTitle("Vimy Ridge");

    map.AddMarker(markerOpt1);
}
```

Il titolo del marcatore verrà visualizzato in una *finestra informazioni* quando l'utente tocca il marcatore. Lo screenshot seguente mostra l'aspetto del marcatore:

[![esempio Google Map con un marcatore e una finestra informazioni per Vimy Ridge](maps-api-images/image07-sml.png)](maps-api-images/image07.png#lightbox)

##### <a name="customizing-a-marker"></a>Personalizzazione di un marcatore

È possibile personalizzare l'icona utilizzata dal marcatore chiamando il metodo `MarkerOptions.InvokeIcon` quando si aggiunge il marcatore alla mappa.
Questo metodo accetta un oggetto [BitmapDescriptor](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptor) che contiene i dati necessari per eseguire il rendering dell'icona. La classe [BitmapDescriptorFactory](https://developers.google.com/android/reference/com/google/android/gms/maps/model/BitmapDescriptorFactory) fornisce alcuni metodi helper per semplificare la creazione di un `BitmapDescriptor`. Nell'elenco seguente vengono introdotti alcuni di questi metodi:

- `DefaultMarker(float colour)` &ndash; usare il marcatore di Google Maps predefinito, ma modificare il colore.

- `FromAsset(string assetName)` &ndash; usare un'icona personalizzata dal file specificato nella cartella assets.

- `FromBitmap(Bitmap image)` &ndash; utilizzare la bitmap specificata come icona.

- `FromFile(string fileName)` &ndash; creare l'icona personalizzata dal file nel percorso specificato.

- `FromResource(int resourceId)` &ndash; creare un'icona personalizzata dalla risorsa specificata.

Il frammento di codice seguente mostra un esempio di creazione di un marcatore predefinito ciano:

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

#### <a name="info-windows"></a>Finestre informazioni

Le *finestre di informazioni* sono finestre speciali che popup per visualizzare informazioni all'utente quando toccano un marcatore specifico. Per impostazione predefinita, nella finestra informazioni viene visualizzato il contenuto del titolo del marcatore. Se il titolo non è stato assegnato, non verrà visualizzata alcuna finestra informazioni. È possibile visualizzare una sola finestra informazioni alla volta.

È possibile personalizzare la finestra informazioni implementando l'interfaccia [googlemap. IInfoWindowAdapter](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.InfoWindowAdapter) . Questa interfaccia presenta due metodi importanti:

- `public View GetInfoWindow(Marker marker)` &ndash; questo metodo viene chiamato per ottenere una finestra di informazioni personalizzata per un marcatore. Se restituisce `null`, verrà usato il rendering predefinito della finestra. Se questo metodo restituisce una visualizzazione, la visualizzazione verrà inserita all'interno della cornice della finestra informazioni.

- `public View GetInfoContents(Marker marker)` &ndash; il metodo verrà chiamato solo se GetInfoWindow restituisce `null`. Questo metodo può restituire un valore `null` se il rendering predefinito del contenuto della finestra informazioni deve essere utilizzato. In caso contrario, questo metodo deve restituire una visualizzazione con il contenuto della finestra informazioni.

Una finestra informativa non è una visualizzazione in tempo reale, ma Android convertirà la visualizzazione in una bitmap statica e la visualizzerà nell'immagine. Ciò significa che una finestra informativa non può rispondere a eventi o movimenti di tocco, né verrà aggiornata automaticamente. Per aggiornare una finestra informazioni, è necessario chiamare il metodo [googlemap. ShowInfoWindow](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Marker.html#showInfoWindow()) .

Nell'immagine seguente vengono illustrati alcuni esempi di finestre di informazioni personalizzate. Il contenuto dell'immagine a sinistra è personalizzato, mentre l'immagine a destra presenta la finestra e il contenuto personalizzati con angoli arrotondati:

![Finestre marcatore di esempio per Melbourne, incluse l'icona e la popolazione. La finestra a destra presenta angoli arrotondati.](maps-api-images/marker-infowindows.png)

#### <a name="groundoverlays"></a>GroundOverlays

A differenza dei marcatori, che identificano una posizione specifica su una mappa, un [GroundOverlay](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlay) è un'immagine usata per identificare una raccolta di posizioni o un'area sulla mappa.

##### <a name="adding-a-groundoverlay"></a>Aggiunta di un GroundOverlay

L'aggiunta di una sovrapposizione di base a una mappa è simile all'aggiunta di un marcatore a una mappa. Viene innanzitutto creato un oggetto [GroundOverlayOptions](https://developers.google.com/android/reference/com/google/android/gms/maps/model/GroundOverlayOptions) . Questo oggetto viene quindi passato come parametro al metodo [`GoogleMap.AddGroundOverlay`](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addGroundOverlay(com.google.android.gms.maps.model.GroundOverlayOptions)) , che restituirà un oggetto `GroundOverlay`. Questo frammento di codice è un esempio di aggiunta di una sovrapposizione di base a una mappa:

```csharp
BitmapDescriptor image = BitmapDescriptorFactory.FromResource(Resource.Drawable.polarbear);
GroundOverlayOptions groundOverlayOptions = new GroundOverlayOptions()
    .Position(position, 150, 200)
    .InvokeImage(image);
GroundOverlay myOverlay = googleMap.AddGroundOverlay(groundOverlayOptions);
```

La schermata seguente Mostra questa sovrapposizione su una mappa:

[![mappa di esempio con un'immagine sovrapposta di un orso polare](maps-api-images/image09-sml.png)](maps-api-images/image09.png#lightbox)

#### <a name="lines-circles-and-polygons"></a>Linee, cerchi e poligoni

Sono disponibili tre tipi semplici di cifre geometriche che è possibile aggiungere a una mappa:

- **Polilinea** : si tratta di una serie di segmenti di linea collegati. Può contrassegnare un percorso su una mappa o creare una forma geometrica.

- **Circle** : verrà disegnato un cerchio sulla mappa.

- **Polygon** : forma chiusa per contrassegnare le aree su una mappa.

##### <a name="polylines"></a>Polilinee

Una [polilinea](https://developers.google.com/android/reference/com/google/android/gms/maps/model/Polyline) è un elenco di oggetti `LatLng` consecutivi che specificano i vertici di ogni segmento di linea. Viene creata una polilinea creando innanzitutto un oggetto `PolylineOptions` e aggiungendovi i punti. L'oggetto `PolylineOption` viene quindi passato a un oggetto `GoogleMap` chiamando il metodo `AddPolyline`.

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

I cerchi vengono creati creando prima un'istanza di un oggetto [CircleOption](https://developers.google.com/android/reference/com/google/android/gms/maps/model/CircleOptions) che specifica il centro e il raggio del cerchio in metri. Il cerchio viene disegnato sulla mappa chiamando [googlemap. AddCircle](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addCircle(com.google.android.gms.maps.model.CircleOptions)).
Il frammento di codice seguente mostra come creare un cerchio:

```csharp
CircleOptions circleOptions = new CircleOptions ();
circleOptions.InvokeCenter (new LatLng(37.4, -122.1));
circleOptions.InvokeRadius (1000);

googleMap.AddCircle (circleOptions);
```

##### <a name="polygons"></a>Poligoni

`Polygon`s sono simili a `Polyline`s, ma non sono terminati. `Polygon`s sono un ciclo chiuso e sono riempiti internamente.
`Polygon`vengono creati esattamente allo stesso modo di un `Polyline`, ad eccezione del metodo [googlemap. AddPolygon](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap.html#addPolygon(com.google.android.gms.maps.model.PolygonOptions)) richiamato.

A differenza di un `Polyline`, un `Polygon` è a chiusura automatica. Il poligono verrà chiuso dal metodo `AddPolygon` disegnando una linea che connette il primo e l'ultimo punto. Il frammento di codice seguente creerà un rettangolo continuo sulla stessa area del frammento di codice precedente nell'esempio `Polyline`.

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

- **Marcatore clic** : l'utente fa clic su un marcatore.

- **Trascinare il marcatore** : l'utente ha fatto clic con il pulsante destro del mouse su un mparger

- **Finestra informazioni fare clic** : l'utente ha fatto clic su una finestra informazioni.

Ognuno di questi eventi verrà discusso in dettaglio di seguito.

### <a name="marker-click-events"></a>Eventi clic marcatore

L'evento `MarkerClicked` viene generato quando l'utente tocca un marcatore. Questo evento accetta un oggetto `GoogleMap.MarkerClickEventArgs` come parametro. Questa classe contiene due proprietà:

- `GoogleMap.MarkerClickEventArgs.Handled` &ndash; questa proprietà deve essere impostata su `true` per indicare che l'evento è stato utilizzato dal gestore eventi. Se questa opzione è impostata su `false`, si verificherà il comportamento predefinito, oltre al comportamento personalizzato del gestore eventi.

- `Marker` &ndash; questa proprietà è un riferimento al marcatore che ha generato l'evento di `MarkerClick`.

Questo frammento di codice mostra un esempio di `MarkerClick` che cambierà la posizione della fotocamera in una nuova posizione sulla mappa:

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

Questo evento viene generato quando l'utente desidera trascinare il marcatore. Per impostazione predefinita, i marcatori non sono trascinabili. È possibile impostare un marcatore come trascinabile impostando la proprietà `Marker.Draggable` su `true` oppure richiamando il metodo `MarkerOptions.Draggable` con `true` come parametro.

Per trascinare il marcatore, l'utente deve prima fare clic con il pulsante destro del mouse sul marcatore, quindi il dito deve rimanere sulla mappa. Quando il dito dell'utente viene trascinato sullo schermo, il marcatore si sposta. Quando il dito dell'utente si solleva dallo schermo, il marcatore rimarrà sul posto.

Nell'elenco seguente vengono descritti i vari eventi che verranno generati per un marcatore trascinabile:

- `GoogleMap.MarkerDragStart(object sender, GoogleMap.MarkerDragStartEventArgs e)` &ndash; questo evento viene generato quando l'utente trascina per la prima volta il marcatore.

- `GoogleMap.MarkerDrag(object sender, GoogleMap.MarkerDragEventArgs e)` &ndash; questo evento viene generato quando il marcatore viene trascinato.

- `GoogleMap.MarkerDragEnd(object sender, GoogleMap.MarkerDragEndEventArgs e)` &ndash; questo evento viene generato al termine del trascinamento del marcatore da parte dell'utente.

Ogni `EventArgs` contiene una singola proprietà denominata `P0` che è un riferimento all'oggetto `Marker` trascinato.

### <a name="info-window-click-events"></a>Finestra informazioni-fare clic su eventi

È possibile visualizzare una sola finestra informazioni alla volta. Quando l'utente fa clic su una finestra di informazioni in una mappa, l'oggetto Map genera un evento `InfoWindowClick`. Il frammento di codice seguente mostra come collegare un gestore all'evento:

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

Tenere presente che una finestra informazioni è un `View` statico di cui viene eseguito il rendering come immagine sulla mappa. Qualsiasi widget, ad esempio pulsanti, caselle di controllo o visualizzazioni di testo posizionati all'interno della finestra informazioni, sarà inerte e non potrà rispondere ad alcun evento utente integrale.

## <a name="related-links"></a>Collegamenti correlati

- [SimpleMapDemo](https://github.com/xamarin/monodroid-samples/tree/master/MapsAndLocationDemo_v3/SimpleMapDemo)
- [Google Play Services](https://developers.google.com/android/guides/overview)
- [API Android di Google Maps v2](https://developers.google.com/maps/documentation/android-sdk/intro)
- [APK Google Play Services](https://play.google.com/store/apps/details?id=com.google.android.gms&hl=en)
- [Ottenere una chiave API di Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Usa-libreria](https://developer.android.com/guide/topics/manifest/uses-library-element)
- [usi-funzionalità](https://developer.android.com/guide/topics/manifest/uses-feature-element)
