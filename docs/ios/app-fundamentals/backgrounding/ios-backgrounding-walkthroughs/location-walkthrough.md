---
title: Procedura dettagliata-percorso in background in Novell. iOS
description: Questo documento fornisce una procedura dettagliata su come usare le informazioni sul percorso in un'applicazione Novell. iOS in background. Viene descritto il programma di installazione, l'interfaccia utente e gli Stati dell'applicazione necessari.
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: e9cfdc32f5e679216781abda1cf718a1c1a186ae
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620709"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>Procedura dettagliata-percorso in background in Novell. iOS

In questo esempio verrà compilata un'applicazione location iOS che stampa le informazioni relative alla posizione corrente: Latitudine, Longitudine e altri parametri sullo schermo. Questa applicazione dimostrerà come eseguire correttamente gli aggiornamenti del percorso mentre l'applicazione è attiva o in background.

Questa procedura dettagliata illustra alcuni concetti chiave in background, inclusa la registrazione di un'app come applicazione in background necessaria, la sospensione degli aggiornamenti dell'interfaccia utente quando l'app è in background e `WillEnterBackground` l' `WillEnterForeground` uso dei metodi e `AppDelegate` .

## <a name="application-set-up"></a>Configurazione dell'applicazione


1. Per prima cosa, creare una nuova **App > iOS > applicazione visualizzazioneC#singola ()** . Chiamarla e assicurarsi che sia stato selezionato iPad e iPhone.

1. Un'applicazione location è qualificata come applicazione in background necessaria in iOS. Registrare l'applicazione come applicazione location modificando il file **info. plist** per il progetto.

    In Esplora soluzioni fare doppio clic sul file **info. plist** per aprirlo e scorrere fino alla fine dell'elenco. Inserire un segno di spunta per le caselle di controllo **Abilita modalità sfondo** e **aggiornamenti percorso** .

    In Visual Studio per Mac sarà simile alla seguente:

    [![](location-walkthrough-images/image7.png "Inserire un segno di spunta per le caselle di controllo Abilita modalità sfondo e aggiornamenti percorso")](location-walkthrough-images/image7.png#lightbox)

    In Visual Studio il file **info. plist** deve essere aggiornato manualmente aggiungendo la coppia chiave/valore seguente:

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. Ora che l'applicazione è registrata, può ottenere i dati di posizione dal dispositivo. In iOS, la `CLLocationManager` classe viene usata per accedere alle informazioni sulla posizione e può generare eventi che forniscono aggiornamenti della posizione.

1. Nel codice, creare una nuova classe denominata `LocationManager` che fornisce un'unica posizione per varie schermate e codice per sottoscrivere gli aggiornamenti del percorso. Nella classe, creare un'istanza `CLLocationManager` del denominato `LocMgr`: `LocationManager`

    ```csharp
    public class LocationManager
    {
        protected CLLocationManager locMgr;

        public LocationManager () {
            this.locMgr = new CLLocationManager();
            this.locMgr.PausesLocationUpdatesAutomatically = false;

            // iOS 8 has additional permissions requirements
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                locMgr.RequestAlwaysAuthorization (); // works in background
                //locMgr.RequestWhenInUseAuthorization (); // only in foreground
            }

            if (UIDevice.CurrentDevice.CheckSystemVersion (9, 0)) {
                locMgr.AllowsBackgroundLocationUpdates = true;
            }
        }

        public CLLocationManager LocMgr {
            get { return this.locMgr; }
        }
    }
    ```

    Il codice precedente imposta alcune proprietà e autorizzazioni per la classe [CLLocationManager](xref:CoreLocation.CLLocationManager) :

    - `PausesLocationUpdatesAutomatically`: Valore booleano che può essere impostato a seconda che il sistema sia autorizzato a sospendere gli aggiornamenti del percorso. In alcuni dispositivi il valore predefinito `true`è, che può causare il blocco del dispositivo per ottenere gli aggiornamenti della posizione in background dopo circa 15 minuti.
    - `RequestAlwaysAuthorization`-È necessario passare questo metodo per concedere all'utente dell'app l'opzione per consentire l'accesso al percorso in background. `RequestWhenInUseAuthorization`può anche essere passato se si vuole dare all'utente l'opzione per consentire l'accesso alla posizione solo quando l'app è in primo piano.
    - `AllowsBackgroundLocationUpdates`: Proprietà booleana, introdotta in iOS 9, che può essere impostata per consentire a un'app di ricevere gli aggiornamenti del percorso quando vengono sospesi.

    > [!IMPORTANT]
    > iOS 8 (e versioni successive) richiede anche una voce nel file **info. plist** per visualizzare l'utente come parte della richiesta di autorizzazione.

1. Aggiungere una chiave `NSLocationAlwaysUsageDescription` o `NSLocationWhenInUseUsageDescription` una stringa che verrà visualizzata all'utente nell'avviso che richiede l'accesso ai dati della località.

1. iOS 9 richiede che quando si `AllowsBackgroundLocationUpdates` usa il file **info. plist** includa la `UIBackgroundModes` chiave `location`con il valore. Se è stato completato il passaggio 2 di questa procedura dettagliata, questo dovrebbe essere già presente nel file INFO. plist.


1. All'interno `LocationManager` della classe, creare un metodo `StartLocationUpdates` denominato con il codice seguente. Questo codice Mostra come iniziare a ricevere gli aggiornamenti del percorso dal `CLLocationManager`:

    ```csharp
    if (CLLocationManager.LocationServicesEnabled) {
        //set the desired accuracy, in meters
        LocMgr.DesiredAccuracy = 1;
        LocMgr.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) =>
        {
            // fire our custom Location Updated event
            LocationUpdated (this, new LocationUpdatedEventArgs (e.Locations [e.Locations.Length - 1]));
        };
        LocMgr.StartUpdatingLocation();
    }
    ```

    In questo metodo si verificano molti aspetti importanti. Prima di tutto, viene eseguito un controllo per verificare se l'applicazione ha accesso ai dati del percorso nel dispositivo. Per verificarlo, chiamare `LocationServicesEnabled` `CLLocationManager`su. Questo metodo restituirà **false** se l'utente ha negato l'accesso all'applicazione alle informazioni sulla posizione.

1. Successivamente, indicare alla gestione della posizione la frequenza di aggiornamento. `CLLocationManager`in sono disponibili molte opzioni per filtrare e configurare i dati del percorso, inclusa la frequenza degli aggiornamenti. In questo esempio, impostare `DesiredAccuracy` per aggiornare ogni volta che il percorso viene modificato da un contatore. Per ulteriori informazioni sulla configurazione della frequenza di aggiornamento del percorso e di altre preferenze, vedere il [riferimento alla classe CLLocationManager](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) nella documentazione di Apple.

1. Infine, chiamare `StartUpdatingLocation` `CLLocationManager` sull'istanza. Ciò indica al gestore della posizione di ottenere una correzione iniziale nella posizione corrente e di iniziare a inviare gli aggiornamenti

Fino ad ora, il gestore della posizione è stato creato, configurato con i tipi di dati che si desidera ricevere e ha determinato la posizione iniziale. A questo punto il codice deve eseguire il rendering dei dati del percorso nell'interfaccia utente. È possibile eseguire questa operazione con un evento personalizzato che accetta `CLLocation` come argomento:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

Il passaggio successivo consiste nel sottoscrivere gli aggiornamenti del percorso `CLLocationManager`da e generare l'evento `LocationUpdated` personalizzato quando diventano disponibili nuovi dati della posizione, passando la posizione come argomento. A tale scopo, creare una nuova classe **LocationUpdateEventArgs.cs**. Questo codice è accessibile all'interno dell'applicazione principale e restituisce il percorso del dispositivo quando viene generato l'evento:

```csharp
public class LocationUpdatedEventArgs : EventArgs
{
    CLLocation location;

    public LocationUpdatedEventArgs(CLLocation location)
    {
       this.location = location;
    }

    public CLLocation Location
    {
       get { return location; }
    }
}
```

## <a name="user-interface"></a>Interfaccia utente

1. Usare la finestra di progettazione iOS per compilare la schermata che consente di visualizzare le informazioni sulla posizione. Fare doppio clic sul file **Main. Storyboard** per iniziare.

    Nello storyboard trascinare diverse etichette sullo schermo per fungere da segnaposto per le informazioni sulla posizione. In questo esempio sono presenti etichette per la latitudine, la longitudine, l'altitudine, il corso e la velocità.

    Il layout sarà simile al seguente:

    ![](location-walkthrough-images/image8.png "Un layout dell'interfaccia utente di esempio in iOS designer")

1. Nella riquadro della soluzione fare doppio clic sul `ViewController.cs` file e modificarlo per creare una nuova istanza di LocationManager e chiamare `StartLocationUpdates`su di essa.
  Modificare il codice in modo che sia simile al seguente:

    ```csharp
    #region Computed Properties
    public static bool UserInterfaceIdiomIsPhone {
        get { return UIDevice.CurrentDevice.UserInterfaceIdiom == UIUserInterfaceIdiom.Phone; }
    }

    public static LocationManager Manager { get; set;}
    #endregion

    #region Constructors
    public ViewController (IntPtr handle) : base (handle)
    {
    // As soon as the app is done launching, begin generating location updates in the location manager
        Manager = new LocationManager();
        Manager.StartLocationUpdates();
    }

    #endregion
    ```

    Verranno avviati gli aggiornamenti della posizione all'avvio dell'applicazione, anche se non verrà visualizzato alcun dato.

1. Ora che sono stati ricevuti gli aggiornamenti della posizione, aggiornare la schermata con le informazioni sulla posizione. Il metodo seguente ottiene la posizione dall' `LocationUpdated` evento e la Mostra nell'interfaccia utente:

    ```csharp
    #region Public Methods
    public void HandleLocationChanged (object sender, LocationUpdatedEventArgs e)
    {
        // Handle foreground updates
        CLLocation location = e.Location;

        LblAltitude.Text = location.Altitude + " meters";
        LblLongitude.Text = location.Coordinate.Longitude.ToString ();
        LblLatitude.Text = location.Coordinate.Latitude.ToString ();
        LblCourse.Text = location.Course.ToString ();
        LblSpeed.Text = location.Speed.ToString ();

        Console.WriteLine ("foreground updated");
    }
    #endregion
    ```

È comunque necessario sottoscrivere l' `LocationUpdated` evento nella AppDelegate e chiamare il nuovo metodo per aggiornare l'interfaccia utente. Aggiungere il codice `ViewDidLoad,` seguente subito dopo la `StartLocationUpdates` chiamata:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


A questo punto, quando viene eseguita l'applicazione, il risultato sarà simile al seguente:

[![](location-walkthrough-images/image5.png "Esecuzione di un'app di esempio")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>Gestione degli Stati attivi e di sfondo

1. L'applicazione sta inserendo gli aggiornamenti della posizione mentre è in primo piano e attivo. Per dimostrare cosa accade quando l'app entra in background, eseguire l' `AppDelegate` override dei metodi che tengono traccia delle modifiche dello stato dell'applicazione in modo che l'applicazione scriva nella console quando esegue la transizione tra il primo piano e lo sfondo:

    ```csharp
    public override void DidEnterBackground (UIApplication application)
    {
        Console.WriteLine ("App entering background state.");
    }

    public override void WillEnterForeground (UIApplication application)
    {
        Console.WriteLine ("App will enter foreground");
    }
    ```

    Aggiungere il codice seguente in `LocationManager` per stampare continuamente i dati del percorso aggiornati nell'output dell'applicazione, per verificare che le informazioni sul percorso siano ancora disponibili in background:

    ```csharp
    public class LocationManager
    {
        public LocationManager ()
        {
        ...
        LocationUpdated += PrintLocation;
        }
        ...

        //This will keep going in the background and the foreground
        public void PrintLocation (object sender, LocationUpdatedEventArgs e) {
        CLLocation location = e.Location;
        Console.WriteLine ("Altitude: " + location.Altitude + " meters");
        Console.WriteLine ("Longitude: " + location.Coordinate.Longitude);
        Console.WriteLine ("Latitude: " + location.Coordinate.Latitude);
        Console.WriteLine ("Course: " + location.Course);
        Console.WriteLine ("Speed: " + location.Speed);
        }
    }
    ```

1. Si è verificato un problema rimanente con il codice: se si tenta di aggiornare l'interfaccia utente quando l'app è in background, iOS lo termina. Quando l'app passa in background, il codice deve annullare la sottoscrizione agli aggiornamenti del percorso e arrestare l'aggiornamento dell'interfaccia utente.

    iOS fornisce notifiche degli Stati Uniti quando l'app sta per passare a uno stato di applicazione diverso. In questo caso, è possibile sottoscrivere la `ObserveDidEnterBackground` notifica.

    Il frammento di codice seguente illustra come usare una notifica per consentire alla visualizzazione di stabilire quando arrestare gli aggiornamenti dell'interfaccia utente. Questo passaggio verrà inserito `ViewDidLoad`in:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    Quando l'app è in esecuzione, l'output sarà simile al seguente:

    ![](location-walkthrough-images/image6.png "Esempio di output della posizione nella console")

1. L'applicazione stampa gli aggiornamenti della posizione sullo schermo quando opera in primo piano e continua a stampare i dati nella finestra di output dell'applicazione mentre funziona in background.

Rimane solo un problema in attesa: lo schermo avvia gli aggiornamenti dell'interfaccia utente quando l'app viene caricata per la prima volta, ma non ha modo di sapere quando l'app è stata nuovamente immessa in primo piano. Se l'applicazione in background viene riportata in primo piano, gli aggiornamenti dell'interfaccia utente non vengono ripresi.

Per risolvere il problema, nidificare una chiamata per avviare gli aggiornamenti dell'interfaccia utente all'interno di un'altra notifica, che verrà attivata quando l'applicazione passa allo stato attivo:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

Ora l'interfaccia utente inizierà ad aggiornare quando l'applicazione viene avviata per la prima volta e riprende l'aggiornamento ogni volta che l'app torna in primo piano.

In questa procedura dettagliata è stata compilata un'applicazione iOS in grado di riconoscere in background, che stampa i dati relativi alla posizione sia sullo schermo che sulla finestra di output dell'applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Località (parte 4) (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/location)
- [Informazioni di riferimento sul Framework di posizione principale](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
