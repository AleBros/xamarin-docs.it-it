---
title: Servizi di posizione
description: Questa guida presenta riconoscimento presenza in applicazioni Android e viene illustrato come ottenere la posizione dell'utente usando l'API del servizio percorso di Android, nonché il provider di posizione congiunta disponibile con l'API di servizi di posizione di Google.
ms.prod: xamarin
ms.assetid: 0008682B-6CEF-0C1D-3200-56ECF58F5D3C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/22/2018
ms.openlocfilehash: 2cd49441ec9ba15cd7fd2647fa74b39b32ea8acd
ms.sourcegitcommit: e64c3c10d6a36b3b031d6d4dbff7af74ab2b7f21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2018
ms.locfileid: "40251169"
---
# <a name="location-services"></a>Servizi di posizione

_Questa guida presenta riconoscimento presenza in applicazioni Android e viene illustrato come ottenere la posizione dell'utente usando l'API del servizio percorso di Android, nonché il provider di posizione congiunta disponibile con l'API di servizi di posizione di Google._

## <a name="location-services-overview"></a>Panoramica di servizi di posizione

Android fornisce l'accesso a varie tecnologie di località come posizione della cella tower, Wi-Fi e GPS. I dettagli di ogni tecnologia di indirizzo vengono ricavati attraverso *provider di posizione*, consentendo alle applicazioni di ottenere i percorsi nello stesso modo indipendentemente dal provider utilizzato. Questa guida presenta il provider di posizione congiunta, una parte di Google Play Services, che determina in modo intelligente il modo migliore per ottenere la posizione dei dispositivi basata su quali provider sono disponibili e il dispositivo in uso. API del servizio percorso di Android e viene illustrato come comunicare con il percorso di sistema del servizio usando un `LocationManager`. La seconda parte della guida illustra l'API di servizi di posizione Android usando il `LocationManager`.
 
Come regola empirica generale, le applicazioni devono preferiscono usare il provider di posizione congiunta, il precedente percorso servizio API Android solo quando è necessario eseguire il fallback.

## <a name="location-fundamentals"></a>Nozioni fondamentali sulla posizione

In Android, indipendentemente da quali API scelto per l'utilizzo di dati sulla località, alcuni concetti rimangono invariati. Questa sezione illustra i provider di posizione e le autorizzazioni relative alla posizione.

### <a name="location-providers"></a>Provider di posizione

Diverse tecnologie vengono utilizzate internamente per individuare la posizione dell'utente. L'hardware usato dipende dal tipo della *provider di posizione* selezionato per il processo di raccolta dei dati. Android Usa tre provider di posizione:

-   **Provider GPS** &ndash; GPS fornisce la posizione più accurata, Usa della massima potenza e funziona in modo ottimale in ambienti esterni. Questo provider Usa una combinazione di GPS e assistito GPS ([aGPS](http://en.wikipedia.org/wiki/Assisted_GPS)), che restituisce dati GPS raccolti da torri cellulari.

-   **Provider di rete** &ndash; fornisce una combinazione di dati Wi-Fi e cellulare, inclusi quelli raccolti dalla cella towers aGPS. Usa meno energia rispetto del Provider GPS, ma restituisce dati sulla località di precisione diverse.

-   **Provider passivo** &ndash; un'opzione "piggyback" uso di provider richiesto da altre applicazioni o servizi per generare dati sulla località in un'applicazione. Si tratta di una meno affidabili ma il risparmio di energia opzione ideale per applicazioni che non richiedono aggiornamenti del percorso costante di lavoro.

I provider del percorso non sono sempre disponibili. Ad esempio, potrebbe essere necessario usare GPS per la nostra applicazione, ma GPS potrebbe essere disattivato nelle impostazioni o i dispositivi non hanno affatto GPS. Se un provider specifico non è disponibile, la scelta di tale provider può restituire `null`.

### <a name="location-permissions"></a>Autorizzazioni del percorso

Un'applicazione con riconoscimento della posizione deve accedere ai sensori hardware del dispositivo per la ricezione di dati del cellulare, Wi-Fi e GPS. L'accesso viene controllato tramite le autorizzazioni appropriate nel manifesto Android dell'applicazione.
Sono disponibili due autorizzazioni &ndash; a seconda delle esigenze dell'applicazione e la scelta dell'API, è opportuno consentire a uno:

-   `ACCESS_FINE_LOCATION` &ndash; Consente a un'applicazione l'accesso a GPS.
    Necessari per il *Provider GPS* e *Provider passivo* opzioni (*Provider passivo richiede l'autorizzazione per accedere ai dati GPS raccolti da un'altra applicazione o servizio*). Autorizzazione facoltativa per il *Provider di rete*.

-   `ACCESS_COARSE_LOCATION` &ndash; Consente a un'applicazione l'accesso al percorso di telefoni cellulari o Wi-Fi. Obbligatorio per *Provider di rete* se `ACCESS_FINE_LOCATION` non è impostata.

Per le app che usano l'API versione 21 (Android 5.0 Lollipop) o versioni successive, è possibile abilitare `ACCESS_FINE_LOCATION` e vengono ancora eseguite su dispositivi che non è hardware GPS. Se l'app richiede hardware GPS, è necessario aggiungere in modo esplicito un' `android.hardware.location.gps` `uses-feature` elemento per il manifesto Android. Per altre informazioni, vedere l'Android [Usa funzionalità](https://developer.android.com/guide/topics/manifest/uses-feature-element.html) riferimento all'elemento.

Per impostare le autorizzazioni, espandere la **delle proprietà** cartella nel **riquadro della soluzione** e fare doppio clic su **androidmanifest. XML**. Le autorizzazioni saranno elencate sotto **autorizzazioni necessarie**:

[![Screenshot delle impostazioni di Android Manifest autorizzazioni necessarie](location-images/location-01-xs.png)](location-images/location-01-xs.png#lightbox)

L'impostazione di una di queste autorizzazioni indica Android che l'applicazione necessita dell'autorizzazione da parte dell'utente per accedere ai provider di posizione. Dispositivi che il livello API 22 (Android 5.1) di esecuzione o lower chiederà all'utente per concedere queste autorizzazioni ogni volta che l'app viene installata. Nei dispositivi che eseguono API livello 23 (Android 6.0) o versioni successive, l'app deve eseguire una verifica delle autorizzazioni in fase di esecuzione prima di effettuare una richiesta del provider di posizione. 

> [!NOTE]
>Nota: L'impostazione `ACCESS_FINE_LOCATION` implica l'accesso a entrambi dati sulla località approssimativa e accurato. Non dovrebbe essere mai necessario impostare entrambe le autorizzazioni, solo il *minimo* app richiesto per il funzionamento dell'autorizzazione.

Questo frammento di codice è riportato un esempio di come verificare che un'app disponga dell'autorizzazione per il `ACCESS_FINE_LOCATION` autorizzazione:

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

Le app devono essere a tolleranza dello scenario in cui l'utente non concederà l'autorizzazione (o ha revocato l'autorizzazione) e hanno un metodo per gestire normalmente tale situazione. Vedere le [Guida alle autorizzazioni](~/android/app-fundamentals/permissions.md) per altri dettagli sull'implementazione della fase di esecuzione autorizzazione controlla in xamarin. Android.


## <a name="using-the-fused-location-provider"></a>Tramite il provider di posizione congiunta

Il provider di posizione congiunta è il modo migliore per le applicazioni Android ricevere gli aggiornamenti di posizione dal dispositivo perché selezionerà in modo efficiente il provider di posizione durante la fase di esecuzione per fornire le informazioni sul percorso migliore in modo efficiente a batteria. Ad esempio, un utente loro vita all'aria aperta Ottiene il percorso migliore la lettura con il GPS. Se l'utente vengono presentati in ambienti chiusi, in cui GPS funziona in modo inadeguato (se affatto), il provider di posizione congiunta può passare automaticamente al Wi-Fi, che funziona in modo ottimale in ambienti chiusi.
 
L'API del provider di posizione congiunta offre un'ampia gamma di altri strumenti per migliorare la produttività applicazioni con riconoscimento della posizione, tra cui geofencing e monitoraggio dell'attività. In questa sezione verrà messa a fuoco nelle Nozioni di base di configurazione di `LocationClient`, stabilire i provider e ottenere la posizione dell'utente.

Il provider di posizione congiunta fa parte di [Google Play Services](http://developer.android.com/google/play-services/index.html).
Il pacchetto di Google Play Services deve essere installato e configurato correttamente nel funzionamento dell'applicazione per l'API del provider di posizione congiunta e il dispositivo deve avere l'APK Google Play Services installato.

Prima di xamarin. Android applicazione può usare il provider di posizione congiunta, è necessario aggiungere il **Xamarin.GooglePlayServices.Maps** pacchetto al progetto. Inoltre, i seguenti `using` istruzioni devono essere aggiunti ai file di origine che fanno riferimento le classi descritte di seguito:

```csharp
using Android.Gms.Common;
using Android.Gms.Location;
```

### <a name="checking-if-google-play-services-is-installed"></a>Verifica se è installato Google Play Services

Si verifica un'eccezione di runtime, quindi verrà xamarin. Android arresto anomalo del sistema se prova a usare il provider di posizione congiunta quando Google Play Services non è installato (o non aggiornato).  Se Google Play Services non è installato, quindi l'applicazione deve eseguire il fallback al servizio di percorso di Android illustrato in precedenza. Se Google Play Services non è aggiornato, l'app può visualizzare un messaggio all'utente chiedendogli di aggiornare la versione installata di Google Play Services.

Questo frammento di codice è riportato un esempio di come un'attività Android a livello di codice controllare se Google Play Services è installato:

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

Per interagire con il provider di posizione congiunta, un'applicazione xamarin. Android debba avere un'istanza del `FusedLocationProviderClient`. Questa classe espone i metodi necessari per sottoscrivere aggiornamenti della posizione e per recuperare l'ultimo percorso noto del dispositivo.

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

Il `FusedLocationProviderClient.GetLastLocationAsync()` metodo fornisce un modo semplice e non bloccante per un'applicazione xamarin. Android ottenere rapidamente l'ultimo percorso noto del dispositivo con overhead della codifica minima.

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

### <a name="subscribing-to-location-updates"></a>Sottoscrizione di posizione degli aggiornamenti

Un'applicazione xamarin. Android può anche sottoscrivere aggiornamenti della posizione da provider di posizione congiunta usando il `FusedLocationProviderClient.RequestLocationUpdatesAsync` metodo, come illustrato nel frammento di codice seguente:

```csharp
await fusedLocationProviderClient.RequestLocationUpdatesAsync(locationRequest, locationCallback);
```
Questo metodo accetta due parametri:

-   **`Android.Gms.Location.LocationRequest`** &ndash; Oggetto `LocationRequest` oggetto è come un'applicazione xamarin. Android passa i parametri sul funzionamento di provider di posizione congiunta. Il `LocationRequest` contiene informazioni di questo tipo come richieste frequenti devono essere resi o sull'importanza deve essere un aggiornamento percorso accurate. Ad esempio, una richiesta di posizione importante causerà il dispositivo da usare il GPS e conseguenza maggiore potenza, per determinare il percorso. Questo frammento di codice viene illustrato come creare un `LocationRequest` per una località con un'accuratezza elevata, verifica circa ogni cinque minuti per un aggiornamento percorso (ma non più di due minuti tra le richieste). Il provider di posizione congiunta utilizzerà un `LocationRequest` come materiale sussidiario per il provider di posizione da utilizzare quando si tenta per determinare la posizione del dispositivo:

    ```csharp
    LocationRequest locationRequest = new LocationRequest()
                                      .SetPriority(LocationRequest.PriorityHighAccuracy)
                                      .SetInterval(60 * 1000 * 5)
                                      .SetFastestInterval(60 * 1000 * 2);
    ```
                                          

-   **`Android.Gms.Location.LocationCallback`** &ndash; Per ricevere aggiornamenti della posizione, un'applicazione xamarin. Android deve creare una sottoclasse di `LocationProvider` classe astratta. Questa classe esposte due metodi che forse richiamato dal provider di posizione congiunta per aggiornare l'app con informazioni sulla posizione. Questo argomento verrà discusso in dettaglio più avanti.

Per notificare a un'applicazione xamarin. Android di un aggiornamento del percorso, il provider di posizione congiunta richiamerà il `LocationCallBack.OnLocationResult(LocationResult result)`. Il `Android.Gms.Location.LocationResult` parametro conterrà le informazioni sul percorso di aggiornamento.

Quando il provider di posizione congiunta rileva una modifica la disponibilità di dati sulla località, verrà chiamato il `LocationProvider.OnLocationAvaibility(LocationAvailability
locationAvailability)` (metodo). Se il `LocationAvailability.IsLocationAvailable` proprietà restituisce `true`, quindi è possibile presupporre che i risultati di posizione del dispositivo è segnalato dalla `OnLocationResult` come accurate e sempre aggiornati in base alle esigenze di `LocationRequest`. Se `IsLocationAvailable` è false, nessun risultato percorso verrà utilizzato come restituito dallo strumento `OnLocationResult`.

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

## <a name="using-the-android-location-service-api"></a>Usando l'API del servizio percorso Android

Il servizio di percorso di Android è un'API precedente per l'utilizzo di informazioni sulla posizione in Android. Dati sulla località vengono raccolti dai sensori hardware e raccolti da un servizio di sistema, che è accessibile nell'applicazione con un `LocationManager` classe e un `ILocationListener`.

Il servizio di percorso è ideale per le applicazioni che devono essere eseguito sui dispositivi che non dispongono di Google Play Services.

Il servizio di percorso è un tipo speciale di [servizio](http://developer.android.com/guide/components/services.html) gestiti dal sistema. Un servizio di sistema interagisce con l'hardware del dispositivo ed è sempre in esecuzione. Per la scelta in aggiornamenti della posizione nella nostra applicazione, verrà effettuata la sottoscrizione agli aggiornamenti della posizione dal servizio di individuazione sistema usando una `LocationManager` e un `RequestLocationUpdates` chiamare.

Per ottenere la posizione dell'utente usando il servizio di percorso di Android sono necessari diversi passaggi:

1.  Ottenere un riferimento di `LocationManager` servizio.
2.  Implementare il `ILocationListener` interfaccia e handle di eventi quando viene modificato il percorso.
3.  Usare il `LocationManager` agli aggiornamenti di percorso di richiesta per un provider specificato. Il `ILocationListener` nel passaggio precedente verrà usato per ricevere i callback dal `LocationManager`.
4.  Arresta gli aggiornamenti di percorso quando l'applicazione non è più appropriato per la ricezione degli aggiornamenti.

### <a name="location-manager"></a>Gestione percorsi

È possibile accedere al servizio di percorso del sistema con un'istanza del `LocationManager` classe. `LocationManager` è una classe speciale che consente di interagire con il percorso di sistema del servizio e chiamare metodi su di esso. Un'applicazione può ottenere un riferimento per la `LocationManager` chiamando `GetSystemService` e passando un tipo di servizio, come illustrato di seguito:

```csharp
LocationManager locationManager = (LocationManager) GetSystemService(Context.LocationService);
```

`OnCreate` è un buon punto per ottenere un riferimento al `LocationManager`.
È consigliabile mantenere il `LocationManager` come una variabile di classe, in modo da poterlo chiamare in vari punti del ciclo di vita di attività.

### <a name="request-location-updates-from-the-locationmanager"></a>Aggiornamenti di percorso di richiesta dal LocationManager

Una volta che l'applicazione contiene un riferimento al `LocationManager`, deve indicare il `LocationManager` sul tipo di informazioni di percorso che sono necessari e con quale frequenza sono necessario aggiornare tali informazioni. Eseguire questa operazione chiamando `RequestionLocationUpdates` nella `LocationManager` oggetto e passando ad alcuni criteri per gli aggiornamenti e un callback che riceverà gli aggiornamenti di percorso. Questo callback è un tipo che deve implementare il `ILocationListener` interfaccia (descritto in dettaglio più avanti in questa Guida).

Il `RequestionLocationUpdates` metodo indica il percorso di sistema del servizio che l'applicazione desidera iniziare a ricevere aggiornamenti della posizione. Questo metodo consente di specificare il provider, nonché le soglie di tempo e la distanza per controllare la frequenza di aggiornamento. Ad esempio, aggiorna il metodo seguente sotto la posizione delle richieste dal provider di posizione GPS ogni 2000 millisecondi, e solo quando il percorso viene modificato più di 1 metro:

```csharp
// For this example, this method is part of a class that implements ILocationListener, described below
locationManager.RequestLocationUpdates(LocationManager.GpsProvider, 2000, 1, this);
```

Un'applicazione dovrebbe richiedere solo con la frequenza in base alle esigenze dell'applicazione come ben funzionate aggiornamenti della posizione. Ciò consente di mantenere la durata della batteria e crea un'esperienza migliore per l'utente.

### <a name="responding-to-updates-from-the-locationmanager"></a>Rispondere agli aggiornamenti del LocationManager

Una volta che un'applicazione ha richiesto aggiornamenti dal `LocationManager`, può ricevere informazioni dal servizio implementando il [ `ILocationListener` ](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/) interfaccia. Questa interfaccia fornisce quattro metodi per l'ascolto per il percorso del servizio e il provider di posizione, `OnLocationChanged`. Il sistema chiamerà `OnLocationChanged` quando la posizione dell'utente cambia sufficiente per essere considerata una modifica del percorso in base ai criteri impostati durante la richiesta di aggiornamenti della posizione. 

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

### <a name="unsubscribing-to-locationmanager-updates"></a>Annullamento della sottoscrizione agli aggiornamenti LocationManager

Per risparmiare risorse di sistema, un'applicazione deve annullare la sottoscrizione agli aggiornamenti della posizione appena possibile. Il `RemoveUpdates` metodo indica il `LocationManager` per arrestare l'invio degli aggiornamenti all'applicazione.  Ad esempio, un'attività può chiamare `RemoveUpdates` nella `OnPause` (metodo), in modo siamo in grado di risparmiare energia se un'applicazione non è necessario che gli aggiornamenti di percorso mentre l'attività non è sullo schermo:

```csharp
protected override void OnPause ()
{
    base.OnPause ();
    locationManager.RemoveUpdates (this);
}
```

Se l'applicazione deve ottenere aggiornamenti della posizione in background, è opportuno creare un servizio personalizzato che effettua la sottoscrizione per il servizio di percorso di sistema. Vedere le [elaborazione in background in con i servizi Android](~/android/app-fundamentals/services/index.md) Guida per altre informazioni.

### <a name="determining-the-best-location-provider-for-the-locationmanager"></a>Determinare il provider di posizione migliore per il LocationManager

L'applicazione precedente imposta GPS come provider di posizione. Tuttavia, GPS potrebbe non essere disponibile in tutti i casi, ad esempio se il dispositivo è in ambienti chiusi o non ha un ricevitore GPS. In questo caso, il risultato è un `null` restituito per il Provider.

Per ottenere l'app per i casi GPS non è disponibile, si utilizza il `GetBestProvider` metodo per richiedere il provider di posizione (dispositivo supportato e abilitato utente) disponibili migliore all'avvio dell'applicazione. Anziché passare un provider specifico, è possibile indicare `GetBestProvider` i requisiti per il provider, ad esempio la precisione e power - con un [ `Criteria` oggetto](https://developer.xamarin.com/api/type/Android.Locations.Criteria/). `GetBestProvider` Restituisce il provider migliore per i criteri specificati.

Il codice seguente viene illustrato come ottenere il meglio provider disponibile e usarlo quando vengono richiesti aggiornamenti della posizione:

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
>  Se l'utente abbia disabilitato tutti i provider di posizione `GetBestProvider` restituirà `null`. Per verificare il funzionamento di questo codice in un vero e proprio dispositivo, assicurarsi di abilitare GPS, Wi-Fi e reti cellulari sotto **impostazioni di Google > percorso > modalità** come illustrato in questo screenshot:

[![Schermata modalità percorso delle impostazioni in un telefono Android](location-images/location-02.png)](location-images/location-02.png#lightbox)

La schermata riportata di seguito viene illustrato il percorso dell'applicazione in esecuzione con `GetBestProvider`:

[![App GetBestProvider visualizzazione latitudine, longitudine e provider](location-images/location-03.png)](location-images/location-03.png#lightbox)

Tenere presente che `GetBestProvider` non modifica il provider in modo dinamico. Piuttosto, determina il provider migliore disponibile una sola volta durante il ciclo di vita di attività. Se lo stato del provider viene modificata dopo che è stata impostata, l'applicazione richiede codice aggiuntivo nel `ILocationListener` metodi &ndash; `OnProviderEnabled`, `OnProviderDisabled`, e `OnStatusChanged` &ndash; per gestire tutte le possibilità correlati per il opzione di provider.

## <a name="summary"></a>Riepilogo

Questa guida sono stati illustrati come ottenere la posizione dell'utente usando il servizio di percorso di Android e il provider di posizione congiunta da Google API di servizi di posizione.


## <a name="related-links"></a>Collegamenti correlati

- [Location (esempio)](https://developer.xamarin.com/samples/Location/)
- [FusedLocationProvider (esempio)](https://developer.xamarin.com/samples/FusedLocationProvider/)
- [Google Play Services](http://developer.android.com/google/play-services/index.html)
- [Classe criteri](https://developer.xamarin.com/api/type/Android.Locations.Criteria/)
- [Classe LocationManager](https://developer.xamarin.com/api/type/Android.Locations.LocationManager/)
- [Classe LocationListener](https://developer.xamarin.com/api/type/Android.Locations.ILocationListener/)
- [LocationClient API](http://developer.android.com/reference/com/google/android/gms/location/LocationClient.html)
- [LocationListener API](http://developer.android.com/reference/com/google/android/gms/location/LocationListener.html)
- [LocationRequest API](https://developer.android.com/reference/com/google/android/gms/location/LocationRequest.html)
