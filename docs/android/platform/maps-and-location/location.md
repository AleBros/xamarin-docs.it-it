---
title: Servizi di posizione in Android
description: Questa guida introduce la conoscenza della posizione nelle applicazioni Android e illustra come ottenere la posizione dell'utente usando l'API del servizio di localizzazione Android, nonché il provider di percorsi con fuso disponibile con Google API Servizi in base alla località.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/22/2018
ms.openlocfilehash: b44bb52dc69aae1d3d058a1eae7c3be13ec5dc53
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643345"
---
# <a name="location-services-on-android"></a>Servizi di posizione in Android

_Questa guida introduce la conoscenza della posizione nelle applicazioni Android e illustra come ottenere la posizione dell'utente usando l'API del servizio di localizzazione Android, nonché il provider di percorsi con fuso disponibile con Google API Servizi in base alla località._

Android consente di accedere a diverse tecnologie di localizzazione, ad esempio la posizione della Torre della cella, Wi-Fi e GPS. I dettagli di ogni tecnologia di posizione vengono estratti attraverso i *provider di località*, consentendo alle applicazioni di ottenere posizioni nello stesso modo, indipendentemente dal provider utilizzato. Questa guida presenta il provider di posizioni fuse, una parte del Google Play Services, che determina in modo intelligente il modo migliore per ottenere la posizione dei dispositivi in base ai provider disponibili e alla modalità di utilizzo del dispositivo. API del servizio di localizzazione Android e Mostra come comunicare con il servizio di percorso di `LocationManager`sistema usando un. La seconda parte della guida esamina la API Servizi in base alla località Android usando `LocationManager`.

Come regola empirica generale, le applicazioni preferiscono usare il provider di percorsi con fusibile, eseguendo il fallback della precedente API del servizio di localizzazione Android solo quando necessario.

## <a name="location-fundamentals"></a>Nozioni fondamentali sulla posizione

In Android, indipendentemente dall'API scelta per l'uso dei dati sulla posizione, diversi concetti rimangono invariati. In questa sezione vengono presentati i provider di percorsi e le autorizzazioni relative alla località.

### <a name="location-providers"></a>Provider di località

Diverse tecnologie vengono utilizzate internamente per individuare la posizione dell'utente. L'hardware utilizzato dipende dal tipo di provider di *località* selezionato per il processo di raccolta dei dati. Android usa tre provider di località:

-   **Provider GPS** &ndash; Il GPS offre la posizione più accurata, usa la potenza massima e funziona meglio all'esterno. Questo provider usa una combinazione di GPS e GPS assistito ([AGPS](https://en.wikipedia.org/wiki/Assisted_GPS)), che restituisce i dati GPS raccolti dalle torrette cellulari.

-   **Provider di rete** &ndash; Fornisce una combinazione di dati WiFi e cellulari, inclusi i dati AGPS raccolti dalle torrette delle celle. Usa meno energia rispetto al provider GPS, ma restituisce i dati di posizione con una precisione variabile.

-   **Provider passivo** &ndash; Opzione "Piggyback" che usa i provider richiesti da altre applicazioni o servizi per generare dati sulla posizione in un'applicazione. Si tratta di un'opzione meno affidabile ma con risparmio di energia ideale per le applicazioni che non richiedono aggiornamenti costanti del percorso per funzionare.

I provider di località non sono sempre disponibili. Ad esempio, potrebbe essere necessario usare il GPS per l'applicazione, ma il GPS potrebbe essere disattivato nelle impostazioni oppure il dispositivo potrebbe non avere alcun GPS. Se un provider specifico non è disponibile, la scelta del provider potrebbe `null`restituire.

### <a name="location-permissions"></a>Autorizzazioni per il percorso

Un'applicazione in grado di riconoscere la posizione deve accedere ai sensori hardware di un dispositivo per ricevere dati GPS, Wi-Fi e cellulari. L'accesso viene controllato tramite le autorizzazioni appropriate nel manifesto Android dell'applicazione.
Sono disponibili &ndash; due autorizzazioni, a seconda dei requisiti dell'applicazione e della scelta dell'API, che sarà necessario consentire:

-   `ACCESS_FINE_LOCATION`&ndash; Consente a un'applicazione di accedere a GPS.
    Obbligatorio per le opzioni provider *GPS* e *provider passivo* (il*provider passivo necessita dell'autorizzazione per accedere ai dati GPS raccolti da un'altra applicazione o servizio*). Autorizzazione facoltativa per il *provider di rete*.

-   `ACCESS_COARSE_LOCATION`&ndash; Consente a un'applicazione di accedere al percorso cellulare e Wi-Fi. Obbligatorio per il provider di `ACCESS_FINE_LOCATION` *rete* se non è impostato.

Per le app destinate all'API versione 21 (Android 5,0 Lollipop) o versioni successive, `ACCESS_FINE_LOCATION` è possibile abilitare ed eseguire comunque i dispositivi che non dispongono di hardware GPS. Se l'app richiede hardware GPS, è necessario aggiungere in modo esplicito un `android.hardware.location.gps` `uses-feature` elemento al manifesto Android. Per altre informazioni, vedere la Guida di riferimento per gli elementi [uso](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) di Android.

Per impostare le autorizzazioni, espandere la cartella **Proprietà** nel **riquadro della soluzione** e fare doppio clic su **file AndroidManifest. XML**. Le autorizzazioni verranno elencate in **autorizzazioni obbligatorie**:

[![Screenshot delle impostazioni delle autorizzazioni necessarie per il manifesto Android](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

L'impostazione di una di queste autorizzazioni indica a Android che l'applicazione necessita dell'autorizzazione dell'utente per accedere ai provider di percorsi. I dispositivi che eseguono API level 22 (Android 5,1) o versioni precedenti chiederanno all'utente di concedere queste autorizzazioni ogni volta che l'app viene installata. Nei dispositivi che eseguono il livello API 23 (Android 6,0) o versione successiva, l'app deve eseguire un controllo delle autorizzazioni in fase di esecuzione prima di effettuare una richiesta del provider di posizione. 

> [!NOTE]
>Nota: L' `ACCESS_FINE_LOCATION` impostazione implica l'accesso ai dati della posizione grossolana e fine. Non è mai necessario impostare entrambe le autorizzazioni, ma solo l'autorizzazione *minima* necessaria per il funzionamento dell'app.

Questo frammento di codice è un esempio di come verificare che un'app disponga dell' `ACCESS_FINE_LOCATION` autorizzazione per l'autorizzazione:

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

Le app devono essere a tolleranza dello scenario in cui l'utente non concede l'autorizzazione (o ha revocato l'autorizzazione) ed è in grado di gestire la situazione in modo normale. Per ulteriori informazioni sull'implementazione dei controlli delle autorizzazioni di runtime in Novell. Android, vedere la [Guida alle autorizzazioni](~/android/app-fundamentals/permissions.md) .


## <a name="using-the-fused-location-provider"></a>Uso del provider di percorsi con fusibile

Il provider di percorsi con fusibile è il modo preferito per le applicazioni Android per la ricezione degli aggiornamenti del percorso dal dispositivo, in quanto consente di selezionare in modo efficiente il provider di località in fase di esecuzione per fornire le informazioni sulla posizione ottimale in modo efficiente per la batteria. Ad esempio, un utente che cammina intorno all'esterno ottiene la posizione migliore per la lettura con GPS. Se l'utente passa a un punto di ingresso, in cui il GPS funziona in modo non corretto (se disponibile), il provider del percorso con fusibile può passare automaticamente al Wi-Fi, che funziona in modo ottimale.
 
L'API del provider di percorsi con fusibile fornisce un'ampia gamma di altri strumenti per potenziare le applicazioni in grado di riconoscere la posizione, tra cui il monitoraggio delle attività e geoschermate In questa sezione verranno illustrate le nozioni di base per la configurazione `LocationClient`di, la definizione dei provider e la posizione dell'utente.

Il provider del percorso fuso fa parte di [Google Play Services](https://developer.android.com/google/play-services/index.html).
Il pacchetto di Google Play Services deve essere installato e configurato correttamente nell'applicazione affinché l'API del provider di percorsi con fusibile funzioni e il dispositivo disponga del Google Play Services APK installato.

Prima che un'applicazione Novell. Android possa usare il provider di percorsi con fusibile, deve aggiungere il pacchetto **Novell. GooglePlayServices. Maps** al progetto. Inoltre, è necessario aggiungere `using` le istruzioni seguenti a tutti i file di origine che fanno riferimento alle classi descritte di seguito:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Verifica dell'installazione di Google Play Services

Un Novell. Android si arresterà in modo anomalo se tenta di usare il provider di percorsi con fusibile quando Google Play Services non è installato (o non aggiornato), si verificherà un'eccezione di Runtime.  Se Google Play Services non è installato, l'applicazione deve eseguire il fallback al servizio di localizzazione Android illustrato in precedenza. Se Google Play Services non è aggiornato, l'app potrebbe visualizzare un messaggio all'utente che richiede di aggiornare la versione installata di Google Play Services.

Questo frammento di codice è un esempio di come un'attività Android può verificare a livello di codice se Google Play Services è installato:

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

Per interagire con il provider di percorsi con fusibile, un'applicazione Novell. Android deve avere un'istanza `FusedLocationProviderClient`di. Questa classe espone i metodi necessari per sottoscrivere gli aggiornamenti della posizione e per recuperare l'ultima posizione nota del dispositivo.

Il `OnCreate` metodo di un'attività è un punto adatto per ottenere un riferimento `FusedLocationProviderClient`a, come illustrato nel frammento di codice seguente:

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

### <a name="getting-the-last-known-location"></a>Recupero dell'ultima posizione nota

Il `FusedLocationProviderClient.GetLastLocationAsync()` metodo fornisce un modo semplice e non bloccante affinché un'applicazione Novell. Android ottenga rapidamente l'ultima posizione nota del dispositivo con un sovraccarico di codifica minimo.

Questo frammento di codice Mostra come `GetLastLocationAsync` usare il metodo per recuperare il percorso del dispositivo:

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

### <a name="subscribing-to-location-updates"></a>Sottoscrizione degli aggiornamenti della posizione

Un'applicazione Novell. Android può anche sottoscrivere gli aggiornamenti del percorso dal provider di percorsi con `FusedLocationProviderClient.RequestLocationUpdatesAsync` fusibile usando il metodo, come illustrato nel frammento di codice seguente:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```

Questo metodo accetta due parametri:

-   **`Android.Gms.Location.LocationRequest`** &ndash; Un`LocationRequest` oggetto è il modo in cui un'applicazione Novell. Android passa i parametri sulla modalità di funzionamento del provider di percorsi con fusibile. In `LocationRequest` sono contenute informazioni quali il modo in cui devono essere effettuate le richieste frequenti o l'importanza di un aggiornamento del percorso preciso. Una richiesta di posizione importante, ad esempio, fa sì che il dispositivo usi il GPS e, di conseguenza, più potenza, quando viene determinato il percorso. Questo frammento di codice Mostra come creare `LocationRequest` un oggetto per una posizione con accuratezza elevata, verificando circa ogni cinque minuti per un aggiornamento della località (ma non prima di due minuti tra le richieste). Il provider di percorsi con fusibile utilizzerà un `LocationRequest` come materiale sussidiario per il provider di percorsi da usare quando si tenta di determinare la posizione del dispositivo:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```

-   **`Android.Gms.Location.LocationCallback`** Per ricevere gli aggiornamenti degli indirizzi, un'applicazione Novell. Android deve sottoclassare la `LocationProvider` classe astratta. &ndash; Questa classe espone due metodi che potrebbero essere richiamati dal provider di percorsi con fusibile per aggiornare l'app con le informazioni sulla posizione. Questo argomento verrà illustrato in dettaglio di seguito.

Per notificare a un'applicazione Novell. Android un percorso di aggiornamento, il provider del percorso con `LocationCallBack.OnLocationResult(LocationResult result)`fusibile richiama. Il `Android.Gms.Location.LocationResult` parametro conterrà le informazioni sul percorso di aggiornamento.

Quando il provider del percorso con fusibile rileva una modifica nella disponibilità dei dati del percorso, chiamerà il `LocationProvider.OnLocationAvailability(LocationAvailability
locationAvailability)` metodo. Se la `LocationAvailability.IsLocationAvailable` proprietà restituisce `true`, è possibile presumere che i risultati della posizione del dispositivo restituiti da `OnLocationResult` siano accurati e aggiornati in base alle esigenze di `LocationRequest`. Se `IsLocationAvailable` è false, nessun risultato del percorso verrà restituito da `OnLocationResult`.

Questo frammento di codice è un'implementazione di `LocationCallback` esempio dell'oggetto:

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

## <a name="using-the-android-location-service-api"></a>Uso dell'API del servizio di localizzazione Android

Il servizio di localizzazione Android è un'API precedente per l'uso delle informazioni sulla posizione in Android. I dati relativi alla posizione vengono raccolti dai sensori hardware e raccolti da un servizio di sistema, a cui si accede `LocationManager` nell'applicazione con `ILocationListener`una classe e un oggetto.

Il servizio location è più adatto per le applicazioni che devono essere eseguite su dispositivi in cui non è installato Google Play Services.

Il servizio location è un tipo speciale di [servizio](https://developer.android.com/guide/components/services.html) gestito dal sistema. Un servizio di sistema interagisce con l'hardware del dispositivo ed è sempre in esecuzione. Per accedere agli aggiornamenti della posizione nell'applicazione, si sottoscriveranno gli aggiornamenti del percorso dal servizio di individuazione del `LocationManager` percorso del `RequestLocationUpdates` sistema usando un e una chiamata a.

Per ottenere la posizione dell'utente usando Android Location Service sono necessari diversi passaggi:

1.  Ottenere un riferimento al `LocationManager` servizio.
2.  Implementare l' `ILocationListener` interfaccia e gestire gli eventi quando il percorso viene modificato.
3.  Utilizzare per `LocationManager` richiedere gli aggiornamenti della posizione per un provider specificato. Il `ILocationListener` dal passaggio precedente verrà usato per ricevere i callback `LocationManager`da.
4.  Arrestare gli aggiornamenti della posizione quando l'applicazione non è più appropriata per la ricezione degli aggiornamenti.

### <a name="location-manager"></a>Gestione percorso

È possibile accedere al servizio percorso di sistema con un'istanza della `LocationManager` classe. `LocationManager`è una classe speciale che consente di interagire con il servizio di individuazione del sistema e di chiamare i metodi su di essa. Un'applicazione può ottenere un riferimento a `LocationManager` `GetSystemService` chiamando e passando un tipo di servizio, come mostrato di seguito:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate`è una posizione ideale per ottenere un riferimento a `LocationManager`.
È consigliabile mantenerla `LocationManager` come variabile di classe, in modo che sia possibile chiamarla in diversi punti del ciclo di vita dell'attività.

### <a name="request-location-updates-from-the-locationmanager"></a>Aggiornamenti della posizione della richiesta da LocationManager

Quando l'applicazione dispone di un riferimento a `LocationManager`, deve indicare il tipo di `LocationManager` informazioni sulla località richieste e la frequenza di aggiornamento delle informazioni. A tale scopo, `RequestLocationUpdates` chiamare `LocationManager` sull'oggetto e passare alcuni criteri per gli aggiornamenti e un callback che riceverà gli aggiornamenti del percorso. Questo callback è un tipo che deve implementare l' `ILocationListener` interfaccia, descritta in modo più dettagliato più avanti in questa guida.

Il `RequestLocationUpdates` metodo indica al servizio percorso di sistema che l'applicazione desidera avviare la ricezione degli aggiornamenti del percorso. Questo metodo consente di specificare il provider, nonché le soglie di tempo e distanza per controllare la frequenza di aggiornamento. Il metodo seguente, ad esempio, richiede gli aggiornamenti della posizione dal provider di posizione GPS ogni 2000 millisecondi e solo quando la località viene modificata più di un metro:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Un'applicazione deve richiedere gli aggiornamenti della posizione solo con la frequenza necessaria affinché l'applicazione possa essere eseguita correttamente. Questo consente di mantenere la durata della batteria e di ottenere un'esperienza migliore per l'utente.

### <a name="responding-to-updates-from-the-locationmanager"></a>Risposta agli aggiornamenti dalla LocationManager

Una volta che un'applicazione ha richiesto gli `LocationManager`aggiornamenti da, può ricevere informazioni dal servizio implementando l' [`ILocationListener`](xref:Android.Locations.ILocationListener) interfaccia. Questa interfaccia fornisce quattro metodi per l'ascolto del servizio location e del provider di posizione `OnLocationChanged`,. Il sistema `OnLocationChanged` chiamerà quando la posizione dell'utente cambia abbastanza per essere qualificata come una modifica della posizione in base ai criteri impostati durante la richiesta degli aggiornamenti del percorso. 

Il codice seguente mostra i metodi nell' `ILocationListener` interfaccia:

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

Per conservare le risorse di sistema, un'applicazione deve annullare la sottoscrizione agli aggiornamenti del percorso appena possibile. Il `RemoveUpdates` metodo`LocationManager` indica a di arrestare l'invio di aggiornamenti all'applicazione.  Ad esempio, un'attività può chiamare `RemoveUpdates` nel metodo in modo che sia possibile conservare la `OnPause` potenza se un'applicazione non necessita di aggiornamenti della posizione mentre l'attività non è visualizzata sullo schermo:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Se l'applicazione deve ottenere gli aggiornamenti della posizione in background, è opportuno creare un servizio personalizzato che sottoscrive il servizio di individuazione del sistema. Per ulteriori informazioni, vedere la Guida in [background con servizi Android](~/android/app-fundamentals/services/index.md) .

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinazione del provider di percorsi migliore per LocationManager

L'applicazione precedente imposta GPS come provider di posizione. Tuttavia, il GPS potrebbe non essere disponibile in tutti i casi, ad esempio se il dispositivo è indoor o non ha un ricevitore GPS. In tal caso, il risultato è un `null` valore restituito per il provider.

Per fare in modo che l'app funzioni quando il GPS non è disponibile, `GetBestProvider` usare il metodo per richiedere il provider di percorsi migliore disponibile (supportato dal dispositivo e abilitato dall'utente) all'avvio dell'applicazione. Anziché passare un provider specifico, è possibile indicare `GetBestProvider` i requisiti per il provider, ad esempio l'accuratezza e l'alimentazione con un [ `Criteria` oggetto](xref:Android.Locations.Criteria). `GetBestProvider`Restituisce il provider migliore per i criteri specificati.

Il codice seguente illustra come ottenere il migliore provider disponibile e usarlo quando si richiedono aggiornamenti della posizione:

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
>  Se l'utente ha disabilitato tutti i provider `GetBestProvider` di percorsi `null`, restituirà. Per vedere come funziona questo codice in un dispositivo reale, assicurarsi di abilitare le reti GPS, Wi-Fi e cellulari in **Impostazioni Google > percorso >** come illustrato in questa schermata:

[![Schermata Impostazioni modalità posizione in un telefono Android](location-images/location-02.png)](location-images/location-02.png#lightbox)

La schermata seguente illustra l'applicazione location in esecuzione `GetBestProvider`con:

[![App GetBestProvider che visualizza latitudine, Longitudine e provider](location-images/location-03.png)](location-images/location-03.png#lightbox)

Tenere presente che `GetBestProvider` non modifica il provider in modo dinamico. Determina invece il migliore provider disponibile una volta durante il ciclo di vita dell'attività. Se lo stato del provider cambia dopo che è stato impostato, l'applicazione richiederà codice aggiuntivo nei `ILocationListener` metodi &ndash; `OnProviderEnabled`, `OnProviderDisabled`e `OnStatusChanged` &ndash; per gestire tutte le possibilità correlate al opzione del provider.

## <a name="summary"></a>Riepilogo

Questa guida ha spiegato come ottenere la posizione dell'utente usando sia il servizio di localizzazione Android sia il provider di località con fusibile da Google API Servizi in base alla località.

## <a name="related-links"></a>Collegamenti correlati

- [Località (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/location)
- [FusedLocationProvider (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fusedlocationprovider)
- [Google Play Services](https://developer.android.com/google/play-services/index.html)
- [Classe criteri](xref:Android.Locations.Criteria)
- [Classe LocationManager](xref:Android.Locations.LocationManager)
- [Classe LocationListener](xref:Android.Locations.ILocationListener)
- [API LocationClient](https://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [API LocationListener](https://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [API LocationRequest](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
