---
title: Servizi di localizzazione su Android
description: Questa guida introduce la consapevolezza della posizione nelle applicazioni Android e illustra come ottenere la posizione dell'utente utilizzando l'API del servizio di posizione Android, nonché il provider di posizione fuso disponibile con l'API dei servizi di localizzazione di Google.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 0fc74ae2307ffd14f8c52515c93993a51455997a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "80805957"
---
# <a name="location-services-on-android"></a>Servizi di localizzazione su Android

_Questa guida introduce la consapevolezza della posizione nelle applicazioni Android e illustra come ottenere la posizione dell'utente utilizzando l'API del servizio di posizione Android, nonché il provider di posizione fuso disponibile con l'API dei servizi di localizzazione di Google._

Android fornisce l'accesso a varie tecnologie di localizzazione come la posizione della torre cellulare, Wi-Fi e GPS. I dettagli di ogni tecnologia di posizione vengono astratti tramite provider di *posizione,* consentendo alle applicazioni di ottenere le posizioni nello stesso modo indipendentemente dal provider utilizzato. Questa guida introduce il provider di posizione fuso, una parte di Google Play Services, che determina in modo intelligente il modo migliore per ottenere la posizione dei dispositivi in base a quali provider sono disponibili e come il dispositivo viene utilizzato. API del servizio di posizione Android e mostra `LocationManager`come comunicare con il servizio di posizione del sistema utilizzando un file . La seconda parte della guida esplora l'API `LocationManager`dei servizi di posizione Android utilizzando il file .

Come regola generale, le applicazioni dovrebbero preferire usare il provider di posizione fuso, esegue il fallback dell'API del servizio di posizione Android precedente solo quando necessario.

## <a name="location-fundamentals"></a>Nozioni fondamentali sulla posizione

In Android, indipendentemente dall'API scelta per l'utilizzo dei dati di posizione, diversi concetti rimangono invariati. In questa sezione vengono presentati i provider di posizione e le autorizzazioni relative alla posizione.

### <a name="location-providers"></a>Fornitori di posizione

Diverse tecnologie vengono utilizzate internamente per individuare la posizione dell'utente. L'hardware utilizzato dipende dal tipo di provider di *posizione* selezionato per il lavoro di raccolta dei dati. Android utilizza tre provider di posizione:

- **GPS Provider** &ndash; GPS fornisce la posizione più accurata, utilizza la maggior parte della potenza, e funziona meglio all'aperto. Questo fornitore utilizza una combinazione di GPS e GPS assistito ([aGPS](https://en.wikipedia.org/wiki/Assisted_GPS)), che restituisce i dati GPS raccolti dalle torri cellulari.

- **Provider di** &ndash; rete Fornisce una combinazione di dati WiFi e cellulari, inclusi i dati aGPS raccolti dalle torri cellulari. Utilizza meno energia rispetto al provider GPS, ma restituisce i dati di posizione di precisione variabile.

- **Provider** &ndash; passivo Un'opzione "piggyback" che utilizza provider richiesti da altre applicazioni o servizi per generare dati sulla posizione in un'applicazione. Si tratta di un'opzione meno affidabile ma di risparmio energetico ideale per le applicazioni che non richiedono aggiornamenti costanti della posizione per funzionare.

I provider di posizione non sono sempre disponibili. Ad esempio, potremmo voler utilizzare il GPS per la nostra applicazione, ma il GPS potrebbe essere disattivato in Impostazioni, o il dispositivo potrebbe non avere affatto il GPS. Se un provider specifico non è disponibile, la scelta di tale provider potrebbe restituire `null`.

### <a name="location-permissions"></a>Autorizzazioni per la posizione

Un'applicazione in grado di riconoscere la posizione deve accedere ai sensori hardware di un dispositivo per ricevere dati GPS, Wi-Fi e cellulari. L'accesso viene controllato tramite le autorizzazioni appropriate nel manifesto Android dell'applicazione.
Sono disponibili due &ndash; autorizzazioni a seconda dei requisiti dell'applicazione e dell'API scelta, è consigliabile consentirne una:There are two permissions available depending on your application's requirements and your choice of API, you will want to allow one:

- `ACCESS_FINE_LOCATION`&ndash; Consente a un'applicazione di accedere al GPS.
    Obbligatorio per le opzioni *Provider GPS* e *Provider passivo* *(il Provider passivo deve accedere ai dati GPS raccolti da un'altra applicazione o servizio*). Autorizzazione facoltativa per il *provider di rete*.

- `ACCESS_COARSE_LOCATION`&ndash; Consente a un'applicazione di accedere alla posizione cellulare e Wi-Fi. Obbligatorio per il `ACCESS_FINE_LOCATION` provider di *rete* se non è impostato.

Per le app destinate all'API versione 21 (Android 5.0 Lollipop) o superiore, è possibile abilitare `ACCESS_FINE_LOCATION` ed eseguire ancora su dispositivi che non dispongono di hardware GPS. Se l'app richiede hardware GPS, `android.hardware.location.gps` `uses-feature` devi aggiungere in modo esplicito un elemento al manifesto Android. Per altre informazioni, vedere il riferimento [all'elemento uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) di Android.For more information, see the Android uses-feature element reference.

Per impostare le autorizzazioni, espandere la cartella **Proprietà** nel **riquadro della soluzione** e fare doppio clic su **AndroidManifest.xml**. Le autorizzazioni verranno elencate in **Autorizzazioni necessarie**:

[![Screenshot delle impostazioni delle autorizzazioni necessarie per il manifesto Android](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

L'impostazione di una di queste autorizzazioni indica Android che l'applicazione richiede l'autorizzazione dell'utente per accedere ai provider di posizione. I dispositivi che eseguono il livello API 22 (Android 5.1) o inferiore chiederanno all'utente di concedere queste autorizzazioni ogni volta che l'app viene installata. Nei dispositivi che eseguono il livello API 23 (Android 6.0) o versione successiva, l'app deve eseguire un controllo delle autorizzazioni di runtime prima di effettuare una richiesta del provider di posizione. 

> [!NOTE]
>Nota: `ACCESS_FINE_LOCATION` l'impostazione implica l'accesso ai dati di posizione sia grossolani che fini. Non devi mai impostare entrambe le autorizzazioni, ma solo l'autorizzazione *minima* richiesta dall'app per funzionare.

Questo frammento è un esempio di come verificare che un'app disponga dell'autorizzazione:This snippet is an example of how to check that an app has permission for the `ACCESS_FINE_LOCATION` permission:

```csharp
 if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.AccessFineLocation) == Permission.Granted)
{
    StartRequestingLocationUpdates();
    isRequestingLocationUpdates = true;
}
else
{
    // The app does not have permission ACCESS_FINE_LOCATION 
}
```

Le app devono essere tolleranti rispetto allo scenario in cui l'utente non concederà l'autorizzazione (o ha revocato l'autorizzazione) e devono disporre di un modo per gestire correttamente tale situazione. Vedere la [guida alle autorizzazioni](~/android/app-fundamentals/permissions.md) per ulteriori dettagli sull'implementazione dei controlli delle autorizzazioni in fase di esecuzione in Xamarin.Android.See the Permissions guide for more details on implementing run-time permission checks in Xamarin.Android.

## <a name="using-the-fused-location-provider"></a>Utilizzo del provider di posizione fuso

Il provider di posizione fuso è il modo preferito per le applicazioni Android per ricevere gli aggiornamenti della posizione dal dispositivo perché selezionerà in modo efficiente il provider di posizione durante la fase di esecuzione per fornire le migliori informazioni sulla posizione in modo efficiente dalla batteria. Ad esempio, un utente che cammina all'aperto ottiene la migliore lettura della posizione con il GPS. Se l'utente poi cammina in casa, dove il GPS funziona male (se non del tutto), il provider di posizione fuso può passare automaticamente al WiFi, che funziona meglio in casa.

L'API del provider di posizione fusa offre una varietà di altri strumenti per consentire alle applicazioni in grado di riconoscere la posizione, tra cui la geofencing e il monitoraggio delle attività. In questa sezione, ci concentreremo sulle nozioni `LocationClient`di base per la configurazione di , la creazione di provider e l'ottenimento della posizione dell'utente.

Il provider di posizione fuso fa parte di [Google Play Services](https://developer.android.com/google/play-services/index.html).
Affinché l'API del provider di posizione fuso funzioni, il pacchetto Google Play Services deve essere installato e nel dispositivo deve essere installato l'APK di Google Play Services.

Prima che un'applicazione Xamarin.Android possa utilizzare il provider di posizione fuso, deve aggiungere il pacchetto **Xamarin.GooglePlayServices.Location** al progetto. Inoltre, le `using` istruzioni seguenti devono essere aggiunte a tutti i file di origine che fanno riferimento alle classi descritte di seguito:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Verifica dell'installazione di Google Play Services

Un Xamarin.Android si arresta in modo anomalo se tenta di utilizzare il provider di posizione fuso quando Google Play Services non è installato (o non è aggiornato) si verificherebbe un'eccezione di runtime.  Se Google Play Services non è installato, l'applicazione deve eseguire il rollback al servizio di localizzazione Android descritto in precedenza. Se Google Play Services non è aggiornato, l'app potrebbe visualizzare un messaggio all'utente che chiede di aggiornare la versione installata di Google Play Services.

Questo frammento è un esempio di come un'attività Android può controllare a livello di codice se Google Play Services è installato:

```csharp
bool IsGooglePlayServicesInstalled()
{
    var queryResult = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
    if (queryResult == ConnectionResult.Success)
    {
        Log.Info("MainActivity", "Google Play Services is installed on this device.");
        return true;
    }

    if (GoogleApiAvailability.Instance.IsUserResolvableError(queryResult))
    {
        // Check if there is a way the user can resolve the issue
        var errorString = GoogleApiAvailability.Instance.GetErrorString(queryResult);
        Log.Error("MainActivity", "There is a problem with Google Play Services on this device: {0} - {1}",
                  queryResult, errorString);

        // Alternately, display the error to the user.
    }

    return false;
}
```

### <a name="fusedlocationproviderclient"></a>FusedLocationProviderClient

Per interagire con il provider di posizione fuso, un'applicazione Xamarin.Android deve avere un'istanza di `FusedLocationProviderClient`. Questa classe espone i metodi necessari per sottoscrivere gli aggiornamenti della posizione e recuperare l'ultima posizione nota del dispositivo.

Il `OnCreate` metodo di un'attività è una posizione `FusedLocationProviderClient`adatta per ottenere un riferimento a , come illustrato nel frammento di codice seguente:

```csharp
public class MainActivity: AppCompatActivity
{
    FusedLocationProviderClient fusedLocationProviderClient;

    protected override void OnCreate(Bundle bundle) 
    {
        fusedLocationProviderClient = LocationServices.GetFusedLocationProviderClient(this);
    }
}
```

### <a name="getting-the-last-known-location"></a>Ottenere l'ultima posizione nota

Il `FusedLocationProviderClient.GetLastLocationAsync()` metodo fornisce un modo semplice e non bloccante per un'applicazione Xamarin.Android per ottenere rapidamente l'ultima posizione nota del dispositivo con un overhead di codifica minimo.

Questo frammento di `GetLastLocationAsync` codice viene illustrato come utilizzare il metodo per recuperare la posizione del dispositivo:This snippet shows how to use the method to retrieve the location of the device:

```csharp
async Task GetLastLocationFromDevice()
{
    // This method assumes that the necessary run-time permission checks have succeeded.
    getLastLocationButton.SetText(Resource.String.getting_last_location);
    Android.Locations.Location location = await fusedLocationProviderClient.GetLastLocationAsync();

    if (location == null)
    {
        // Seldom happens, but should code that handles this scenario
    }
    else
    {
        // Do something with the location 
        Log.Debug("Sample", "The latitude is " + location.Latitude);
    }
}
```

### <a name="subscribing-to-location-updates"></a>Sottoscrizione di aggiornamenti della posizione

Un'applicazione Xamarin.Android può anche sottoscrivere gli aggiornamenti `FusedLocationProviderClient.RequestLocationUpdatesAsync` della posizione dal provider di posizione fuso utilizzando il metodo , come illustrato nel frammento di codice seguente:A Xamarin.Android application can also subscribe to location updates from the fused location provider using the method, as shown in this code snippet:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

Questo metodo accetta due parametri:

- **`Android.Gms.Location.LocationRequest`**&ndash; Un `LocationRequest` oggetto è il modo in cui un'applicazione Xamarin.Android passa i parametri sul funzionamento del provider di posizione fuso. Contiene `LocationRequest` informazioni quali la frequenza con cui devono essere effettuate le richieste o l'importanza di un aggiornamento accurato della posizione. Ad esempio, una richiesta di posizione importante causerà il dispositivo di utilizzare il GPS, e di conseguenza più potenza, quando si determina la posizione. Questo frammento di codice `LocationRequest` mostra come creare un oggetto per una posizione con precisione elevata, verificando approssimativamente ogni cinque minuti l'aggiornamento della posizione (ma non prima di due minuti tra le richieste). Il provider di posizione `LocationRequest` fuso utilizzerà un as guida per quale provider di posizione da utilizzare quando si tenta di determinare la posizione del dispositivo:The fused location provider will use a as guidance for which location provider to use when trying to determine the device location:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

- **`Android.Gms.Location.LocationCallback`**&ndash; Per ricevere gli aggiornamenti dell'indirizzo, un'applicazione Xamarin.Android deve creare una sottoclasse della `LocationProvider` classe astratta. Questa classe ha esposto due metodi che potrebbero essere richiamati dal provider di posizione fuso per aggiornare l'app con informazioni sulla posizione. Questo sarà discusso in modo più dettagliato qui sotto.

Per notificare a un'applicazione Xamarin.Android un aggiornamento della `LocationCallBack.OnLocationResult(LocationResult result)`posizione, il provider di posizione fuso richiamerà il file . Il `Android.Gms.Location.LocationResult` parametro conterrà le informazioni sulla posizione di aggiornamento.

Quando il provider di posizione fuso rileva una modifica nella disponibilità `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)` dei dati di posizione, chiamerà il metodo . Se `LocationAvailability.IsLocationAvailable` la `true`proprietà restituisce , si può presumere `OnLocationResult` che i risultati della posizione del `LocationRequest`dispositivo riportati da siano accurati e aggiornati come richiesto dall'oggetto . Se `IsLocationAvailable` è false, non verrà restituito `OnLocationResult`alcun risultato di posizione da .

Questo frammento di codice `LocationCallback` è un'implementazione di esempio dell'oggetto:This code snippet is a sample implementation of the object:

```csharp
public class FusedLocationProviderCallback : LocationCallback
{
    readonly MainActivity activity;

    public FusedLocationProviderCallback(MainActivity activity)
    {
        this.activity = activity;
    }

    public override void OnLocationAvailability(LocationAvailability locationAvailability)
    {
        Log.Debug("FusedLocationProviderSample", "IsLocationAvailable: {0}",locationAvailability.IsLocationAvailable);
    }

    public override void OnLocationResult(LocationResult result)
    {
        if (result.Locations.Any())
        {
            var location = result.Locations.First();
            Log.Debug("Sample", "The latitude is :" + location.Latitude);
        }
        else
        {
            // No locations to work with.
        }
    }
}
```

## <a name="using-the-android-location-service-api"></a>Utilizzo dell'API del servizio di posizione AndroidUsing the Android Location Service API

Il servizio di posizione Android è un'API meno recente per l'uso delle informazioni sulla posizione in Android.The Android Location Service is an older API for using location information on Android. I dati sulla posizione vengono raccolti dai sensori hardware e raccolti da `LocationManager` un `ILocationListener`servizio di sistema, a cui si accede nell'applicazione con una classe e un file .

Il servizio di localizzazione è più adatto per le applicazioni che devono essere eseguite su dispositivi in cui non è installato Google Play Services.

Il servizio di localizzazione è un tipo speciale di [servizio](https://developer.android.com/guide/components/services.html) gestito dal sistema. Un servizio di sistema interagisce con l'hardware del dispositivo ed è sempre in esecuzione. Per attingere agli aggiornamenti della posizione nell'applicazione, sottoscriveremo gli aggiornamenti della posizione dal servizio di localizzazione del sistema utilizzando una `LocationManager` e una `RequestLocationUpdates` chiamata.

Per ottenere la posizione dell'utente utilizzando il servizio di posizione Android prevede diversi passaggi:

1. Ottenere un riferimento `LocationManager` al servizio.
2. Implementare `ILocationListener` l'interfaccia e gestire gli eventi quando la posizione cambia.
3. Utilizzare `LocationManager` l'oggetto per richiedere gli aggiornamenti della posizione per un provider specificato. Il `ILocationListener` passaggio precedente verrà utilizzato per ricevere `LocationManager`i callback dall'oggetto .
4. Interrompere gli aggiornamenti della posizione quando l'applicazione non è più appropriata per ricevere gli aggiornamenti.

### <a name="location-manager"></a>Gestore della posizione

È possibile accedere al servizio di `LocationManager` posizione del sistema con un'istanza della classe. `LocationManager`è una classe speciale che consente di interagire con il servizio di posizione del sistema e chiamare i metodi su di esso. Un'applicazione può ottenere `LocationManager` un `GetSystemService` riferimento a chiamando e passando un tipo di servizio, come illustrato di seguito:An application can get a reference to to the by calling and passing in a Service type, as shown below:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate`è un buon posto per `LocationManager`ottenere un riferimento al .
È consigliabile mantenere la `LocationManager` variabile di classe come variabile di classe, in modo che sia possibile chiamarla in vari punti del ciclo di vita dell'attività.

### <a name="request-location-updates-from-the-locationmanager"></a>Richiedere aggiornamenti della posizione da LocationManagerRequest location updates from the LocationManager

Una volta che l'applicazione ha un riferimento al `LocationManager`, ha bisogno di dire il `LocationManager` tipo di informazioni sulla posizione che sono necessari, e la frequenza con cui tali informazioni devono essere aggiornate. Eseguire questa `RequestLocationUpdates` operazione `LocationManager` chiamando sull'oggetto e passando alcuni criteri per gli aggiornamenti e un callback che riceverà gli aggiornamenti della posizione. Questo callback è un tipo `ILocationListener` che deve implementare l'interfaccia (descritto più dettagliatamente più avanti in questa guida).

Il `RequestLocationUpdates` metodo indica al servizio percorsi di sistema che l'applicazione desidera iniziare a ricevere gli aggiornamenti del percorso. Questo metodo consente di specificare il provider, nonché le soglie di tempo e distanza per controllare la frequenza di aggiornamento. Ad esempio, il metodo riportato di seguito richiede gli aggiornamenti della posizione dal provider di posizione GPS ogni 2000 millisecondi e solo quando la posizione cambia più di 1 metro:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Un'applicazione deve richiedere gli aggiornamenti della posizione solo con la frequenza necessaria per l'esecuzione corretta dell'applicazione. Questo mantiene la durata della batteria e crea una migliore esperienza per l'utente.

### <a name="responding-to-updates-from-the-locationmanager"></a>Risposta agli aggiornamenti da LocationManager

Una volta che un'applicazione ha richiesto gli aggiornamenti `LocationManager`da [`ILocationListener`](xref:Android.Locations.ILocationListener) , può ricevere informazioni dal Servizio implementando l'interfaccia. Questa interfaccia fornisce quattro metodi per l'ascolto `OnLocationChanged`del servizio di posizione e del provider di posizione, . Il sistema `OnLocationChanged` chiamerà quando la posizione dell'utente cambia abbastanza da qualificarsi come una posizione cambia in base ai criteri impostati quando si richiedono aggiornamenti di posizione. 

Nel codice seguente vengono `ILocationListener` illustrati i metodi nell'interfaccia:The following code shows the methods in the interface:

```csharp
public class MainActivity : AppCompatActivity, ILocationListener
{
    TextView latitude;
    TextView longitude;
    
    public void OnLocationChanged (Location location)
    {
        // called when the location has been updated.
    }
    
    public OnProviderDisabled(string locationProvider)
    {
        // called when the user disables the provider
    }
    
    public OnProviderEnabled(string locationProvider)
    {
        // called when the user enables the provider
    }
    
    public OnStatusChanged(string locationProvider, Availability status, Bundle extras)
    {
        // called when the status of the provider changes (there are a variety of reasons for this)
    }
}
```

### <a name="unsubscribing-to-locationmanager-updates"></a>Annullamento della sottoscrizione agli aggiornamenti di LocationManager

Per risparmiare le risorse di sistema, un'applicazione deve annullare la sottoscrizione agli aggiornamenti della posizione il prima possibile. Il `RemoveUpdates` metodo `LocationManager` indica l'oggetto per interrompere l'invio di aggiornamenti all'applicazione.  Ad esempio, un'attività `RemoveUpdates` può `OnPause` chiamare il metodo in modo che siamo in grado di risparmiare energia se un'applicazione non necessita di aggiornamenti della posizione mentre la relativa attività non è sullo schermo:As an example, an Activity may call in the method so that we are able to conserve power if an application doesn't need location updates while its Activity is not on the screen:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Se l'applicazione deve ottenere gli aggiornamenti della posizione in background, è consigliabile creare un servizio personalizzato che sottoscrive il servizio di posizione del sistema. Per ulteriori informazioni, fare riferimento alla guida [Backgrounding with Android Services.](~/android/app-fundamentals/services/index.md)

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinazione del provider di posizione migliore per LocationManager

L'applicazione precedente imposta GPS come provider di posizione. Tuttavia, il GPS potrebbe non essere disponibile in tutti i casi, ad esempio se il dispositivo è in casa o non dispone di un ricevitore GPS. In questo caso, il risultato è un `null` ritorno per il Provider.

Per far funzionare l'app quando il GPS `GetBestProvider` non è disponibile, puoi usare il metodo per richiedere il miglior provider di posizione disponibile (supportato dal dispositivo e abilitato per l'utente) all'avvio dell'applicazione. Anziché passare un provider specifico, `GetBestProvider` è possibile comunicare i requisiti per il provider, ad esempio accuratezza e potenza, con un [ `Criteria` oggetto](xref:Android.Locations.Criteria). `GetBestProvider`restituisce il miglior provider per i Criteri specificati.

Il codice seguente mostra come ottenere il miglior provider disponibile e usarlo quando si richiedono aggiornamenti della posizione:The following code shows how to get the best available provider and use it when requesting location updates:

```csharp
Criteria locationCriteria = new Criteria();   
locationCriteria.Accuracy = Accuracy.Coarse;
locationCriteria.PowerRequirement = Power.Medium;

locationProvider = locationManager.GetBestProvider(locationCriteria, true);

if(locationProvider != null)
{
    locationManager.RequestLocationUpdates (locationProvider, 2000, 1, this);
}
else
{
    Log.Info(tag, "No location providers available");
}
```

> [!NOTE]
> Se l'utente ha disabilitato `GetBestProvider` tutti `null`i provider di posizione, restituirà . Per vedere come funziona questo codice su un dispositivo reale, assicurati di abilitare le reti GPS, Wi-Fi e cellulari in **Impostazioni Google > Modalità > posizione,** come mostrato in questa schermata:
>
> [![Schermata Modalità di localizzazione delle impostazioni su un telefono Android](location-images/location-02.png)](location-images/location-02.png#lightbox)
>
> La schermata seguente illustra `GetBestProvider`l'applicazione di posizione in esecuzione utilizzando:
>
> [![App GetBestProvider che visualizza latitudine, longitudine e provider](location-images/location-03.png)](location-images/location-03.png#lightbox)
>
> Tenere presente `GetBestProvider` che il provider non viene modificato in modo dinamico. Piuttosto, determina il miglior provider disponibile una volta durante il ciclo di vita dell'attività. Se lo stato del provider cambia dopo che è stato `ILocationListener` &ndash; `OnProviderEnabled`impostato, l'applicazione richiederà codice aggiuntivo nei metodi , `OnProviderDisabled`, e `OnStatusChanged` &ndash; per gestire tutte le possibilità relative all'opzione del provider.

## <a name="summary"></a>Riepilogo

Questa guida riguardava l'ottenimento della posizione dell'utente utilizzando sia il servizio di localizzazione Android che il provider di posizione fuso dall'API dei servizi di localizzazione di Google.

## <a name="related-links"></a>Collegamenti correlati

- [Posizione (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/location)
- [FusedLocationProvider (esempio)FusedLocationProvider (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play Services](https://developer.android.com/google/play-services/index.html)
- [Classe Criteri](xref:Android.Locations.Criteria)
- [Classe LocationManager](xref:Android.Locations.LocationManager)
- [Classe LocationListener](xref:Android.Locations.ILocationListener)
- [LocationClient API](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
