---
title: Servizi di posizione
description: Questa guida presenta riconoscimento del percorso in applicazioni Android e viene illustrato come ottenere la posizione dell'utente tramite l'API del servizio percorso Android, nonché il provider di posizione fusibili disponibile con l'API di servizi di posizione di Google.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/22/2018
ms.openlocfilehash: b509f6892b27afa053a6ee913826d913d7ad54a8
ms.sourcegitcommit: 4f646dc5c51db975b2936169547d625c78a22b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2018
---
# <a name="location-services"></a>Servizi di posizione

_Questa guida presenta riconoscimento del percorso in applicazioni Android e viene illustrato come ottenere la posizione dell'utente tramite l'API del servizio percorso Android, nonché il provider di posizione fusibili disponibile con l'API di servizi di posizione di Google._

## <a name="location-services-overview"></a>Panoramica di servizi di posizione

Android fornisce l'accesso a varie tecnologie di posizione come posizione della cella tower, Wi-Fi e GPS. I dettagli di ogni tecnologia di posizione vengono ricavati tramite *provider percorso*, consentendo alle applicazioni di ottenere i percorsi nello stesso modo indipendentemente dal provider utilizzato. Questa guida illustra il provider di posizione fusibili, una parte di Google Play Services, che in modo intelligente determina il modo migliore per ottenere la posizione dei dispositivi in base alle quali i provider sono disponibili e come viene usata la periferica. API del servizio di percorso di Android e viene illustrato come comunicare con il percorso di sistema del servizio utilizzando un `LocationManager`. La seconda parte della Guida viene esaminato l'API di servizi di posizione Android tramite il `LocationManager`.
 
Come una regola empirica generale, le applicazioni preferibile utilizzare il provider di posizione fusibili, eseguire il fallback il precedente percorso servizio API Android solo quando necessario.

## <a name="location-fundamentals"></a>Nozioni fondamentali sulla posizione

In Android, non ha importanza quale API scelto per l'utilizzo di dati sulla località, diversi concetti rimangono invariati. Questa sezione presenta i provider di posizione e le autorizzazioni relative al percorso.

### <a name="location-providers"></a>Provider di posizione

Diverse tecnologie vengono utilizzate internamente per individuare la posizione dell'utente. L'hardware utilizzato dipende dal tipo di *provider posizione* selezionato per il processo di raccolta dei dati. Android utilizza tre provider di percorso:

-   **Provider GPS** &ndash; GPS fornisce la posizione più accurata, utilizza una maggiore potenza di e funziona in modo ottimale in ambienti esterni. Questo provider Usa una combinazione di GPS e GPS assistito ([aGPS](http://en.wikipedia.org/wiki/Assisted_GPS)), che restituisce dati GPS raccolti da towers cellulare.

-   **Provider di rete** &ndash; fornisce una combinazione di dati, Wi-Fi e cellulare, inclusi dati aGPS raccolti da towers cella. Utilizza il Provider GPS energia minore, ma restituisce dati sulla località di precisione diverse.

-   **Provider passivo** &ndash; un'opzione "piggyback" utilizzo di provider richiesto da altre applicazioni o servizi per generare dati sulla località in un'applicazione. Questo è meno affidabile ma l'opzione di risparmio di energia ideale per applicazioni che non richiedono aggiornamenti percorso costante di lavoro.

Provider di posizione non sono sempre disponibili. Ad esempio, potrebbe essere opportuno utilizzare GPS per l'applicazione, ma GPS potrebbe essere disabilitata nelle impostazioni o il dispositivo potrebbe non disporre GPS affatto. Se un provider specifico non è disponibile, la scelta di tale provider può restituire `null`.

### <a name="location-permissions"></a>Autorizzazioni del percorso

Un'applicazione con riconoscimento del percorso deve accedere sensori di un dispositivo hardware per la ricezione di dati del cellulare, Wi-Fi e GPS. L'accesso viene controllato tramite le autorizzazioni appropriate nel manifesto Android dell'applicazione.
Sono disponibili due autorizzazioni &ndash; in base a requisiti dell'applicazione e la scelta dell'API, è possibile consentire a uno:

-   `ACCESS_FINE_LOCATION` &ndash; Consente un accesso all'applicazione per GPS.
    Richiesto per il *Provider GPS* e *Provider passivo* opzioni (*Provider passivo necessaria l'autorizzazione per accedere ai dati GPS raccolti da un'altra applicazione o servizio*). Autorizzazione facoltativa per il *Provider rete*.

-   `ACCESS_COARSE_LOCATION` &ndash; Consente a un'applicazione l'accesso al percorso di telefoni cellulari o Wi-Fi. Necessario per *Provider rete* se `ACCESS_FINE_LOCATION` non è impostata.

Per le app destinate API versione 21 (Android 5.0 simbolo) o versioni successive, è possibile abilitare `ACCESS_FINE_LOCATION` ed ancora eseguiti in dispositivi che non dispongono di hardware GPS. Se l'app richiede hardware GPS, è necessario aggiungere in modo esplicito un `android.hardware.location.gps` `uses-feature` elemento per il file manifesto Android. Per ulteriori informazioni, vedere il Android [utilizza funzionalità](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) riferimento all'elemento.

Per impostare le autorizzazioni, espandere il **proprietà** cartella la **soluzione riempimento** fare doppio clic su **AndroidManifest.xml**. Le autorizzazioni saranno elencate sotto **autorizzazioni obbligatorie**:

[![Schermata delle impostazioni Android manifesto autorizzazioni necessarie](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

Impostazione di queste autorizzazioni indica che l'applicazione necessita di autorizzazioni per accedere ai provider di percorso per l'utente Android. I dispositivi che eseguire livello API 22 (Android 5.1) o inferiore utente verrà chiesto di concedere queste autorizzazioni ogni volta che viene installata l'app. Nei dispositivi che eseguono API livello 23 (Android 6.0) o versioni successive, l'app deve eseguire un controllo di autorizzazione in fase di esecuzione prima di effettuare una richiesta del provider di posizione. 

> [!NOTE]
>Nota: L'impostazione `ACCESS_FINE_LOCATION` implica l'accesso a entrambi dati sulla località grossolana e di fine. È necessario non è necessario impostare entrambe le autorizzazioni, solo il *minimo* autorizzazione app richiesto per il funzionamento.

Questo frammento è riportato un esempio di come verificare che un'applicazione disponga delle autorizzazioni per il `ACCESS_FINE_LOCATION` autorizzazione:

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

Le app devono essere tollerante per quanto riguarda lo scenario in cui l'utente non concedere l'autorizzazione (o ha negato l'autorizzazione) e disporre di un modo per gestire normalmente tale situazione. Vedere il [Guida alle autorizzazioni](~/android/app-fundamentals/permissions.md) per ulteriori dettagli sull'implementazione in fase di esecuzione autorizzazione controlla in xamarin.


## <a name="using-the-fused-location-provider"></a>Utilizzo del provider di percorso fusibili

Il provider di percorso fusibili è il modo migliore per le applicazioni Android a ricevere gli aggiornamenti di posizione dal dispositivo perché selezionerà in modo efficiente il provider del percorso in fase di esecuzione per fornire le informazioni sul percorso migliore in modo efficiente a batteria. Ad esempio, un utente loro esterni Ottiene il percorso migliore durante la lettura con GPS. Se l'utente scorre quindi interni, in cui GPS funziona in modo inadeguato (se affatto), il provider di posizione fusibili può passare automaticamente a Wi-Fi, che funziona in modo ottimale in ambienti chiusi.
 
Il provider di percorso fusibili API fornisce un'ampia gamma di altri strumenti di sviluppo di applicazioni con riconoscimento del percorso, tra cui geofencing e il monitoraggio dell'attività. In questa sezione si imposterà lo stato attivo le nozioni di base di configurazione di `LocationClient`, stabilire i provider e l'acquisizione della posizione dell'utente.

Il provider di posizione fusibili fa parte di [Google Play Services](http://developer.android.com/google/play-services/index.html).
Il pacchetto di Google Play Services deve essere installato e configurato correttamente nell'applicazione per l'API del provider fusibili percorso per il lavoro e il dispositivo deve avere l'APK Google Play Services installato.

Prima di un xamarin applicazione può utilizzare il provider di posizione fusibili, è necessario aggiungere il **Xamarin.GooglePlayServices.Maps** pacchetto al progetto. Inoltre, i seguenti `using` istruzioni devono essere aggiunti ad alcun file di origine che fanno riferimento le classi descritte di seguito:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Verifica se è installato Google Play Services

Si verifica un'eccezione di runtime, quindi verrà xamarin arresto anomalo del sistema se si tenta di utilizzare il provider di posizione fusibili Google Play Services non è installato (o non aggiornato).  Se non è installato Google Play Services, quindi l'applicazione deve eseguire il fallback al servizio di percorso di Android in precedenza. Se Google Play Services non è aggiornato, l'app può visualizzare un messaggio all'utente che richiede di aggiornare la versione installata di Google Play Services.

Questo frammento è riportato un esempio di come un'attività Android a livello di codice controllare se Google Play Services è installato:

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

Per interagire con il provider di posizione fusibili, un'applicazione di xamarin deve avere un'istanza di `FusedLocationProviderClient`. Questa classe espone i metodi necessari per sottoscrivere gli aggiornamenti di percorso e per recuperare l'ultima posizione nota del dispositivo.

Il `OnCreate` metodo di un'attività è un luogo adatto dove ottenere un riferimento di `FusedLocationProviderClient`, come illustrato nel frammento di codice seguente:

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

Il `FusedLocationProviderClient.GetLastLocationAsync()` metodo fornisce un modo semplice e non di blocco per un'applicazione di xamarin per ottenere rapidamente l'ultimo percorso noto del dispositivo con l'overhead della codifica minima.

Questo frammento di codice viene illustrato come utilizzare il `GetLastLocationAsync` metodo per recuperare il percorso del dispositivo:

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

### <a name="subscribing-to-location-updates"></a>Aggiorna la sottoscrizione in posizione

Un'applicazione di xamarin inoltre possibile sottoscrivere gli aggiornamenti di percorso dal provider di percorso fusibili utilizzando il `FusedLocationProviderClient.RequestLocationUpdatesAsync` (metodo), come illustrato nel frammento di codice seguente:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
Questo metodo accetta due parametri:

-   **`Android.Gms.Location.LocationRequest`** &ndash; Oggetto `LocationRequest` oggetto è come passa i parametri di un'applicazione di xamarin sul funzionamento di provider di posizione fusibili. Il `LocationRequest` contiene informazioni tali come invia richieste frequenti devono essere rese o il livello di importanza deve essere un aggiornamento di posizione accurata. Ad esempio, una richiesta di posizione importante causerà il dispositivo da utilizzare il GPS e conseguenza maggiore potenza, per determinare la posizione. Frammento di codice seguente viene illustrato come creare un `LocationRequest` per un percorso con precisione elevata, verifica circa ogni cinque minuti per un aggiornamento di percorso (ma non avvenga due minuti tra le richieste). Il provider di percorso fusibili utilizzerà un `LocationRequest` come guida per il provider di percorso da utilizzare durante il tentativo di determinare la posizione del dispositivo:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; Per ricevere gli aggiornamenti di percorso, un'applicazione di xamarin deve creare una sottoclasse di `LocationProvider` classe astratta. Questa classe esposti due metodi che può essere richiamata dal provider di percorso fusibili per aggiornare l'app con le informazioni sul percorso. In questo argomento verrà descritte in dettaglio più avanti.

Per notificare a un'applicazione di xamarin di un aggiornamento del percorso, il provider di posizione fusibili richiamerà il `LocationCallBack.OnLocationResult(LocationResult result)`. Il `Android.Gms.Location.LocationResult` parametro conterrà le informazioni sul percorso di aggiornamento.

Quando il provider di posizione fusibili rileva una modifica la disponibilità dei dati del percorso, chiama il `LocationProvider.OnLocationAvaibility(LocationAvailability
locationAvailability)` metodo. Se il `LocationAvailability.IsLocationAvailable` restituisce proprietà `true`, quindi si può presupporre che i risultati dispositivo segnalati da `OnLocationResult` sono accurata e come richiesto dal aggiornati il `LocationRequest`. Se `IsLocationAvailable` è false, nessun risultato percorso verrà restituito da `OnLocationResult`.

Questo frammento di codice è un esempio di implementazione del `LocationCallback` oggetto:

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

## <a name="using-the-android-location-service-api"></a>Tramite l'API del servizio di percorso di Android

Il servizio di percorso di Android è un'API precedente per l'utilizzo di informazioni sul percorso in Android. I dati sulla località vengono raccolti dai sensori di hardware e raccolti da un servizio di sistema, è possibile accedere nell'applicazione con un `LocationManager` classe e un `ILocationListener`.

Il servizio di percorso è più adatto per le applicazioni che devono essere eseguito su dispositivi che non dispongono di Google Play Services.

Il servizio di percorso è un tipo speciale di [servizio](http://developer.android.com/guide/components/services.html) gestiti dal sistema. Un servizio di sistema interagisce con l'hardware del dispositivo ed è sempre in esecuzione. Per sfruttare gli aggiornamenti di percorso dell'applicazione, si verrà sottoscrivere gli aggiornamenti di posizione dal servizio di individuazione sistema utilizzando un `LocationManager` e `RequestLocationUpdates` chiamare.

Per ottenere la posizione dell'utente utilizzando il servizio di percorso Android prevede diversi passaggi:

1.  Ottenere un riferimento di `LocationManager` servizio.
2.  Implementare il `ILocationListener` interfaccia e gestire gli eventi quando cambia il percorso.
3.  Utilizzare il `LocationManager` agli aggiornamenti di percorso di richiesta per un provider specificato. Il `ILocationListener` nel passaggio precedente verrà utilizzato per ricevere i callback dal `LocationManager`.
4.  Arresta gli aggiornamenti di percorso quando l'applicazione non è più appropriato a ricevere gli aggiornamenti.

### <a name="location-manager"></a>Gestione percorsi

È possibile accedere al servizio di percorso di sistema con un'istanza del `LocationManager` classe. `LocationManager` è una classe speciale che consente di interagire con il percorso di sistema del servizio e chiamare i metodi. Un'applicazione può ottenere un riferimento di `LocationManager` chiamando `GetSystemService` e passando un tipo di servizio, come illustrato di seguito:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` è un ottimo strumento per ottenere un riferimento di `LocationManager`.
È consigliabile mantenere il `LocationManager` come una variabile di classe, in modo che è possibile chiamarla in vari punti del ciclo di vita di attività.

### <a name="request-location-updates-from-the-locationmanager"></a>Aggiornamenti di percorso di richiesta dal LocationManager

Una volta che l'applicazione contiene un riferimento al `LocationManager`, è necessario indicare il `LocationManager` il tipo di informazioni di percorso che sono necessari e la frequenza con cui sono necessario aggiornare tali informazioni. Eseguire questa operazione chiamando `RequestionLocationUpdates` sul `LocationManager` oggetto e passando ad alcuni criteri per gli aggiornamenti e un callback che riceverà gli aggiornamenti di percorso. Questo callback è un tipo che deve implementare il `ILocationListener` interfaccia (descritta in dettaglio più avanti in questa Guida).

Il `RequestionLocationUpdates` metodo indica la posizione del servizio di sistema che si desidera iniziare a ricevere gli aggiornamenti di percorso dell'applicazione. Questo metodo consente di specificare il provider, nonché le soglie di tempo e distanza per controllare la frequenza di aggiornamento. Ad esempio, il metodo seguente sotto la posizione delle richieste aggiornata dal provider di posizione GPS ogni 2000 millisecondi, e solo quando più di 1 metro di modificare il percorso:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Un'applicazione deve richiedere solo con la frequenza necessaria per l'applicazione di eseguire anche gli aggiornamenti di percorso. Questo consente di mantenere la durata della batteria e crea un'esperienza migliore per l'utente.

### <a name="responding-to-updates-from-the-locationmanager"></a>Risponde agli aggiornamenti di LocationManager

Una volta che un'applicazione ha richiesto gli aggiornamenti dal `LocationManager`, possa ricevere informazioni dal servizio mediante l'implementazione di [ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/) interfaccia. Questa interfaccia fornisce quattro metodi per l'ascolto per il servizio di percorso e il provider di posizione, `OnLocationChanged`. Il sistema chiamerà `OnLocationChanged` quando cambia la posizione dell'utente sufficienti per essere considerata una modifica della posizione in base ai criteri impostati per la richiesta di aggiornamenti di posizione. 

Il codice seguente illustra i metodi di `ILocationListener` interfaccia:

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

### <a name="unsubscribing-to-locationmanager-updates"></a>Annullamento della sottoscrizione per gli aggiornamenti LocationManager

Per risparmiare risorse di sistema, un'applicazione deve annullare la sottoscrizione agli aggiornamenti di percorso più breve possibile. Il `RemoveUpdates` metodo indica il `LocationManager` per arrestare l'invio degli aggiornamenti all'applicazione.  Ad esempio, un'attività può chiamare `RemoveUpdates` nel `OnPause` metodo in modo che è possibile risparmiare energia se un'applicazione non richiedono percorso aggiornamenti durante l'attività non è presente sullo schermo:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Se l'applicazione deve ottenere gli aggiornamenti di percorso in background, sarà possibile creare un servizio personalizzato che sottoscrive il servizio di percorso di sistema. Consultare la [Backgrounding con servizi Android](~/android/app-fundamentals/services/index.md) Guida per ulteriori informazioni.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinare il provider di percorso migliore per la LocationManager

L'applicazione precedente imposta GPS come provider di posizione. Tuttavia, GPS non siano disponibili in tutti i casi, ad esempio se il dispositivo è in ambienti chiusi o non dispone di un ricevitore GPS. In questo caso, il risultato è un `null` restituito per il Provider.

Per ottenere l'app quando GPS non è disponibile, utilizzare il `GetBestProvider` metodo per richiedere il migliore fornitore percorso disponibile (il dispositivo è supportato e abilitato utente) all'avvio dell'applicazione. Anziché il passaggio di un provider specifico, è possibile indicare `GetBestProvider` i requisiti per il provider, ad esempio accuratezza e power - con un [ `Criteria` oggetto](https://developer.xamarin.com/api/type/Android.Locations.Criteria/). `GetBestProvider` Restituisce il provider migliore per i criteri specificati.

Il codice seguente viene illustrato come ottenere il miglior provider e utilizzarlo per la richiesta di aggiornamenti di posizione:

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
>  Se l'utente abbia disabilitato tutti i provider di percorso, `GetBestProvider` restituirà `null`. Per verificare il funzionamento del codice in un dispositivo reale, assicurarsi di abilitare GPS, Wi-Fi e reti cellulare in **le impostazioni di Google > percorso > modalità** come illustrato in questo screenshot:

[![Schermata di modalità di localizzazione delle impostazioni in un telefono Android](location-images/location-02.png)](location-images/location-02.png#lightbox)

La schermata riportata di seguito viene illustrato il percorso dell'applicazione in esecuzione utilizzando `GetBestProvider`:

[![App GetBestProvider visualizzazione latitudine, longitudine e provider](location-images/location-03.png)](location-images/location-03.png#lightbox)

Tenere presente che `GetBestProvider` non modifica il provider in modo dinamico. Ma determina il miglior provider una volta durante il ciclo di vita di attività. Se lo stato del provider viene modificata dopo che è stata impostata, l'applicazione richiede codice aggiuntivo nel `ILocationListener` metodi &ndash; `OnProviderEnabled`, `OnProviderDisabled`, e `OnStatusChanged` &ndash; per gestire tutte le possibilità correlate per il opzione di provider.

## <a name="summary"></a>Riepilogo

Questa Guida trattati come ottenere la posizione dell'utente tramite il servizio di percorso di Android e provider di percorso fusibili dall'API dei servizi di posizione Google.


## <a name="related-links"></a>Collegamenti correlati

- [Posizione (esempio)](https://developer.xamarin.com/samples/Location/)
- [FusedLocationProvider (esempio)](https://developer.xamarin.com/samples/FusedLocationProvider/)
- [Google Play Services](http://developer.android.com/google/play-services/index.html)
- [Classe criteri](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [Classe LocationManager](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [Classe LocationListener](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient API](http://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](http://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
