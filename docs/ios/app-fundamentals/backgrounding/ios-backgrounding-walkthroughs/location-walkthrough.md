---
title: Procedura dettagliata - percorso di sfondo in xamarin. IOS
description: Questo documento fornisce una procedura dettagliata di come usare le informazioni sul percorso in un'applicazione di xamarin. IOS backgrounded. Viene descritto il programma di installazione necessario, interfaccia utente e gli Stati dell'applicazione.
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: aef39ef435bbbad6f643b2376832d8f8132d6a4c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784094"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>Procedura dettagliata - percorso di sfondo in xamarin. IOS

In questo esempio verrà per compilare iOS dell'applicazione di percorso che stampa informazioni il percorso corrente: latitudine, longitudine e altri parametri alla schermata. Questa applicazione verrà illustrato come eseguire correttamente gli aggiornamenti di percorso, mentre l'applicazione è attivo o Backgrounded.

Questa procedura dettagliata vengono illustrate alcune chiave backgrounding concetti, tra cui registrazione di un'app come un'applicazione in background necessarie, la sospensione di aggiornamenti dell'interfaccia utente quando l'app è backgrounded e l'utilizzo con il `WillEnterBackground` e `WillEnterForeground` `AppDelegate` metodi .

## <a name="application-set-up"></a>Configurazione di applicazione


1. Innanzitutto, creare un nuovo **iOS > App > singola applicazione di visualizzazione (c#)**. Chiamarlo _percorso_ e assicurarsi che siano stati selezionati sia iPad e iPhone.

1. Un'applicazione di percorso qualificato come un'applicazione in background necessarie in iOS. Registrare l'applicazione come applicazione percorso modificando il **Info. plist** file per il progetto.

    In Esplora soluzioni, fare doppio clic su di **Info. plist** file per aprirlo e scorrere fino alla fine dell'elenco. Inserire un segno di spunta da entrambi i **abilitare la modalità di Background** e **percorso aggiornamenti** caselle di controllo.

    In Visual Studio per Mac, avrà un aspetto simile al seguente:

    [![](location-walkthrough-images/image7.png "Inserire un segno di spunta sia la modalità di Background attiva e le caselle di controllo degli aggiornamenti di percorso")](location-walkthrough-images/image7.png#lightbox)

    In Visual Studio, **Info. plist** deve essere aggiornato manualmente aggiungendo la seguente coppia chiave/valore:

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. Ora che l'applicazione è registrata, è possibile ottenere i dati sulla località dal dispositivo. In iOS, il `CLLocationManager` classe viene utilizzata per accedere alle informazioni di percorso e può generare eventi che forniscono gli aggiornamenti di percorso.

1. Nel codice, creare una nuova classe denominata `LocationManager` che fornisce un'unica posizione per diverse schermate e codice per sottoscrivere gli aggiornamenti di percorso. Nel `LocationManager` di classi, creare un'istanza della classe di `CLLocationManager` chiamato `LocMgr`:

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

    Il codice precedente imposta il numero di proprietà e le autorizzazioni nel [CLLocationManager](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/) classe:

    - `PausesLocationUpdatesAutomatically` – Questo è un valore booleano che può essere impostato a seconda se il sistema è possibile sospendere gli aggiornamenti di percorso. Su alcuni dispositivi per impostazione predefinita `true`, che può causare problemi di recupero in background gli aggiornamenti di percorso dopo circa 15 minuti.
    - `RequestAlwaysAuthorization` -È necessario passare questo metodo per consentire all'utente di app per consentire il percorso a cui accedere in background. `RequestWhenInUseAuthorization` può essere passato anche se si desidera assegnare all'utente la possibilità di consentire il percorso a cui accedere solo quando l'applicazione è in primo piano.
    - `AllowsBackgroundLocationUpdates` – Questo è una proprietà booleana, introdotta in iOS 9 che possono essere impostati per consentire un'app di ricevere aggiornamenti di percorso quando è sospeso.

    > [!IMPORTANT]
    > iOS 8 (e versione successiva) richiede inoltre una voce nella **Info. plist** file per indicare all'utente come parte della richiesta di autorizzazione.

1. Aggiungere una chiave `NSLocationAlwaysUsageDescription` o `NSLocationWhenInUseUsageDescription` con una stringa che verrà visualizzata all'utente nell'avviso che richiede l'accesso ai dati di posizione.

1. iOS 9 richiede che, quando si utilizza `AllowsBackgroundLocationUpdates` il **Info. plist** include la chiave `UIBackgroundModes` con il valore `location`. Se è stata completata, passaggio 2 di questa procedura dettagliata, questo deve già essere stati nel file Info. plist.


1. All'interno di `LocationManager` di classi, creare un metodo denominato `StartLocationUpdates` con il codice seguente. Questo codice viene illustrato il modo per iniziare a ricevere gli aggiornamenti di percorso dal `CLLocationManager`:

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

    Esistono diversi aspetti importanti in questo metodo in corso. In primo luogo, si esegue un controllo per verificare se l'applicazione ha accesso ai dati di posizione del dispositivo. Si verifica, chiamare `LocationServicesEnabled` sul `CLLocationManager`. Questo metodo restituirà **false** se l'utente ha negato l'accesso all'applicazione per informazioni sul percorso.

1. Successivamente, indicano la frequenza con la gestione del percorso per l'aggiornamento. `CLLocationManager` fornisce numerose opzioni per il filtraggio e la configurazione dei dati di posizione, tra cui la frequenza degli aggiornamenti. In questo esempio, impostare il `DesiredAccuracy` per aggiornare ogni volta che cambia il percorso da un misuratore. Per ulteriori informazioni sulla configurazione di frequenza di aggiornamento di posizione e le altre preferenze, consultare il [riferimento alla classe CLLocationManager](http://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) nella documentazione di Apple.

1. Infine, chiamare `StartUpdatingLocation` sul `CLLocationManager` istanza. In questo modo la gestione di percorsi per ottenere una correzione iniziale alla posizione corrente e avviare l'invio degli aggiornamenti

Fino a questo punto, il percorso manager è stato creato, configurato con i tipi di dati che si desidera ricevere, e ha determinato il percorso iniziale. Ora il codice deve eseguire il rendering di dati del percorso per l'interfaccia utente. È possibile farlo con un evento personalizzato che accetta un `CLLocation` come argomento:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

Il passaggio successivo consiste nel sottoscrivere gli aggiornamenti di percorso dal `CLLocationManager`e generare personalizzata `LocationUpdated` evento quando nuovi dati di posizione diventano disponibili, passando il percorso come argomento. A tale scopo, creare una nuova classe **LocationUpdateEventArgs.cs**. Questo codice è accessibile all'interno dell'applicazione principale e restituisce il percorso del dispositivo quando viene generato l'evento:

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

1. Utilizzare la finestra di progettazione iOS per la schermata che consente di visualizzare le informazioni sul percorso di compilazione. Fare doppio clic su di **Main** file da cui iniziare.

    Nello storyboard, trascinare più etichette nella schermata di agire come segnaposto per le informazioni sul percorso. In questo esempio sono presenti etichette per latitudine, longitudine, altitudine, corsi e velocità.

    Il layout sarà simile al seguente:

    ![](location-walkthrough-images/image8.png "Un esempio di layout dell'interfaccia utente nella finestra di progettazione iOS")

1. Nel riquadro di soluzioni, fare doppio clic su di `ViewController.cs` file e modificarlo per creare una nuova istanza della LocationManager e chiamare metodo `StartLocationUpdates`su di esso.
  Modificare il codice simile al seguente:

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

    Gli aggiornamenti di percorso verrà avviato all'avvio dell'applicazione, anche se non verrà visualizzato alcun dato.

1. Ora che vengono ricevuti gli aggiornamenti di percorso, è possibile aggiornare la schermata con le informazioni sul percorso. Il metodo seguente ottiene il percorso dal nostro `LocationUpdated` evento e la visualizza nell'interfaccia utente:

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

È necessario sottoscrivere il `LocationUpdated` evento AppDelegate, e chiamare il metodo nuovo per aggiornare l'interfaccia utente. Aggiungere il codice seguente in `ViewDidLoad,` subito dopo il `StartLocationUpdates` chiamare:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


A questo punto, quando viene eseguita l'applicazione, dovrebbe essere simile al seguente:

[![](location-walkthrough-images/image5.png "Eseguire un'app di esempio")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>Gestione degli stati attivo e in Background

1. L'applicazione è l'output aggiornamenti di posizione, mentre è in primo piano e attiva. Per illustrare cosa accade quando l'app passa lo sfondo, eseguire l'override di `AppDelegate` modifiche dello stato di metodi che tengono traccia dell'applicazione in modo che l'applicazione viene scritto nella console quando esegue la transizione tra il primo piano e sfondo:

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

    Aggiungere il codice seguente nel `LocationManager` continuamente stampare posizione aggiornata sono ancora disponibili in background i dati dell'output dell'applicazione, per verificare le informazioni sul percorso:

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

1. Si verifica un problema rimanente con il codice: il tentativo di aggiornare l'interfaccia utente quando l'app è backgrounded causa iOS verrà terminerà il. Quando l'app passa in background, il codice necessario annullare la sottoscrizione da aggiornamenti di posizione e arrestare l'aggiornamento dell'interfaccia utente.

    iOS fornisce notifiche quando l'app sta per eseguire la transizione a un'altra applicazione stati. In questo caso, è possibile sottoscrivere il `ObserveDidEnterBackground` notifica.

    Frammento di codice seguente viene illustrato come utilizzare una notifica per consentire la visualizzazione di sapere quando interrompere gli aggiornamenti dell'interfaccia utente. Questo entra `ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    Quando l'app è in esecuzione, l'output avrà un aspetto simile al seguente:

    ![](location-walkthrough-images/image6.png "Esempio di percorso output nella console di")

1. L'applicazione visualizza gli aggiornamenti di posizione sullo schermo quando si opera in primo piano e continua a stampare dati alla finestra di output dell'applicazione durante il funzionamento in background.

Rimane solo uno dei problemi in sospeso: la schermata avvio aggiornamenti dell'interfaccia utente quando viene caricata l'app, ma non ha modo di sapere quando l'app nuovamente entrato in primo piano. Se l'applicazione backgrounded viene ripristinata in primo piano, non verranno effettuati aggiornamenti dell'interfaccia utente.

Per risolvere questo problema, nidificare una chiamata per avviare gli aggiornamenti dell'interfaccia utente all'interno di un'altra notifica, che verrà generato quando l'applicazione passa allo stato attivo:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

L'interfaccia utente verrà iniziare l'aggiornamento quando prima dell'avvio dell'applicazione e riprendere l'aggiornamento ogni volta che l'applicazione torna in primo piano.

In questa procedura dettagliata, è creata un'applicazione iOS ben progettate, in grado di riconoscere sfondo che visualizza i dati sulla località per la schermata sia nella finestra di output dell'applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Posizione (parte 4) (esempio)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Riferimento al Framework di percorso di base](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
