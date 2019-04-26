---
title: Procedura dettagliata - il percorso in Background in xamarin. IOS
description: Questo documento fornisce una procedura dettagliata di come usare le informazioni sul percorso in un'applicazione xamarin. IOS backgrounded. Descrive il programma di installazione necessario, interfaccia utente e gli Stati dell'applicazione.
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: fa8a48e165764a449af4bc5414d2e66aecea8269
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61392272"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>Procedura dettagliata - il percorso in Background in xamarin. IOS

In questo esempio si intende compilare iOS dell'applicazione di percorso che visualizza informazioni sul nostro percorso corrente: latitudine, longitudine e altri parametri sullo schermo. Questa applicazione verrà illustrato come eseguire correttamente gli aggiornamenti di percorso mentre l'applicazione è attivo o Backgrounded.

Questa procedura dettagliata illustra i principali funzionalità di elaborazione in background in concetti, tra cui la registrazione di un'app come un'applicazione in background necessari, la sospensione degli aggiornamenti dell'interfaccia utente quando l'app è supportata da un background e l'utilizzo con il `WillEnterBackground` e `WillEnterForeground` `AppDelegate` metodi .

## <a name="application-set-up"></a>Applicazione configurata


1. In primo luogo, creare una nuova **iOS > App > Single View Application (C#)**. Chiamarla _posizione_ e assicurarsi che siano stati selezionati sia iPad e iPhone.

1. Un'applicazione di posizione corrente, un'applicazione in background necessarie in iOS. Registrare l'applicazione come applicazione percorso modificando la **Info. plist** file per il progetto.

    In Esplora soluzioni fare doppio clic sui **Info. plist** file per aprirlo e scorrere fino alla fine dell'elenco. Inserire un segno di spunta da entrambe le **abilitare la modalità di Background** e il **aggiornamenti della posizione** caselle di controllo.

    In Visual Studio per Mac, avrà un aspetto simile al seguente:

    [![](location-walkthrough-images/image7.png "Inserire un segno di spunta per la modalità di Background abilitare sia le caselle di controllo di aggiornamenti della posizione")](location-walkthrough-images/image7.png#lightbox)

    In Visual Studio **Info. plist** deve essere aggiornato manualmente aggiungendo la coppia chiave/valore seguente:

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. Ora che l'applicazione è registrata, è possibile ottenere dati sulla località dal dispositivo. In iOS, il `CLLocationManager` classe viene utilizzata per accedere alle informazioni sulla posizione e può generare eventi che forniscono aggiornamenti della posizione.

1. Nel codice, creare una nuova classe denominata `LocationManager` che fornisce un'unica posizione per varie schermate e codice per sottoscrivere aggiornamenti della posizione. Nel `LocationManager` classe, creare un'istanza della classe la `CLLocationManager` chiamato `LocMgr`:

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

    Il codice precedente imposta un numero di proprietà e le autorizzazioni nel [CLLocationManager](xref:CoreLocation.CLLocationManager) classe:

    - `PausesLocationUpdatesAutomatically` – Questo è un valore booleano che può essere impostato a seconda del fatto che il sistema è consentito sospendere gli aggiornamenti di percorso. In alcuni dispositivi viene impostata automaticamente su `true`, che può causare il dispositivo interrompere la ricezione di aggiornamenti della posizione dopo circa 15 minuti in background.
    - `RequestAlwaysAuthorization` -È necessario passare questo metodo per offrire la possibilità di consentire la posizione per accedervi in background l'utente dell'app. `RequestWhenInUseAuthorization` può essere passato anche se si vuole concedere all'utente la possibilità di consentire la posizione per accedervi solo quando l'app è in primo piano.
    - `AllowsBackgroundLocationUpdates` – Questo è una proprietà booleana, introdotta in iOS 9 che può essere impostata su Consenti un'app di ricevere aggiornamenti della posizione quando sospesa.

    > [!IMPORTANT]
    > iOS 8 (e versioni successive) richiede inoltre una voce nella **Info. plist** file da visualizzare all'utente come parte della richiesta di autorizzazione.

1. Aggiungere una chiave `NSLocationAlwaysUsageDescription` o `NSLocationWhenInUseUsageDescription` con una stringa che verrà visualizzata all'utente nell'avviso che richiede l'accesso ai dati di posizione.

1. richiede iOS 9 che quando si usa `AllowsBackgroundLocationUpdates` il **Info. plist** include la chiave `UIBackgroundModes` con il valore `location`. Se sono state completate, passaggio 2 di questa procedura dettagliata, deve essere già stato nel file Info. plist.


1. All'interno di `LocationManager` classe, creare un metodo chiamato `StartLocationUpdates` con il codice seguente. Questo codice viene illustrato il modo per iniziare a ricevere aggiornamenti della posizione dal `CLLocationManager`:

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

    Esistono diversi aspetti importanti che accade in questo metodo. Prima di tutto, eseguiamo un controllo per verificare se l'applicazione ha accesso ai dati del percorso nel dispositivo. Si verifica, chiamare `LocationServicesEnabled` nella `CLLocationManager`. Questo metodo restituirà **false** se l'utente ha negato l'accesso all'applicazione per informazioni sulla posizione.

1. Successivamente, indicare quanto spesso la gestione di posizione per l'aggiornamento. `CLLocationManager` offre molte opzioni per il filtraggio e la configurazione di dati sulla località, tra cui la frequenza degli aggiornamenti. In questo esempio, impostare il `DesiredAccuracy` aggiornare ogni volta che viene modificato il percorso da un misuratore. Per altre informazioni su come configurare la frequenza di aggiornamento di località e altre preferenze, vedere la [riferimento alla classe CLLocationManager](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) nella documentazione di Apple.

1. Infine, chiamare `StartUpdatingLocation` nella `CLLocationManager` istanza. Ciò indica al gestore di percorso per ottenere una correzione rapida iniziale sulla posizione corrente e per iniziare a inviare gli aggiornamenti

Finora, il percorso gestore è stato creato, configurato con i tipi di dati si desidera ricevere, e ha determinato la posizione iniziale. A questo punto il codice deve eseguire il rendering di dati sulla località per l'interfaccia utente. È possibile farlo con un evento personalizzato che accetta un `CLLocation` come argomento:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

Il passaggio successivo consiste nel sottoscrivere aggiornamenti della posizione dal `CLLocationManager`e generare l'oggetto personalizzato `LocationUpdated` evento quando nuovi dati di posizione diventano disponibili, passando il percorso come argomento. A tale scopo, creare una nuova classe **LocationUpdateEventArgs.cs**. Questo codice è accessibile all'interno dell'applicazione principale e restituisce la posizione del dispositivo quando viene generato l'evento:

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

1. Utilizzare la finestra di progettazione di iOS per creare la schermata che visualizzerà le informazioni sulla posizione. Fare doppio clic sulla **Main. Storyboard** file da cui iniziare.

    Sullo storyboard, trascinare più etichette nella schermata per fungere da segnaposto per le informazioni sul percorso. In questo esempio sono presenti etichette per la latitudine, longitudine, altitudine, corsi e velocità.

    Il layout sarà simile al seguente:

    ![](location-walkthrough-images/image8.png "Un layout di interfaccia utente di esempio in iOS Designer")

1. Nel riquadro della soluzione, fare doppio clic il `ViewController.cs` file e modificarlo per creare una nuova istanza della LocationManager e chiamare metodo `StartLocationUpdates`su di esso.
  Modificare il codice come illustrato di seguito:

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

1. Ora che vengono ricevuti gli aggiornamenti di percorso, aggiornare la schermata con le informazioni sul percorso. Il metodo seguente ottiene il percorso dal nostro `LocationUpdated` evento e la visualizza nell'interfaccia utente:

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

Tuttavia è necessario sottoscrivere il `LocationUpdated` evento nel nostro AppDelegate e chiamare il nuovo metodo per aggiornare l'interfaccia utente. Aggiungere il codice seguente nel `ViewDidLoad,` subito dopo il `StartLocationUpdates` chiamare:

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

1. L'applicazione è l'output aggiornamenti della posizione, mentre è in primo piano e attiva. Per illustrare cosa accade quando l'app passa lo sfondo, eseguire l'override di `AppDelegate` modifiche dello stato di metodi che tengono traccia dell'applicazione in modo che l'applicazione scrive nella console quando esegue la transizione tra il primo piano e sfondo:

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

    Aggiungere il codice seguente nel `LocationManager` continuamente stampare posizione aggiornata sono ancora disponibili in background i dati per l'output dell'applicazione, per verificare le informazioni sul percorso:

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

1. Si verifica un problema rimanente con il codice: tentativo di aggiornare l'interfaccia utente quando l'app è supportata da un background verrà causa iOS verrà terminarlo. Quando l'app passa in background, il codice deve annullare la sottoscrizione agli aggiornamenti della posizione e interrompere l'aggiornamento dell'interfaccia utente.

    iOS ci fornisce notifiche quando l'app si trova sulla transizione a un'altra applicazione stati. In questo caso, è possibile sottoscrivere il `ObserveDidEnterBackground` notifica.

    Il frammento di codice seguente viene illustrato come usare una notifica per informare la visualizzazione quando si arresta gli aggiornamenti dell'interfaccia utente. Dovrà essere inserito `ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    Quando l'app è in esecuzione, l'output avrà un aspetto simile al seguente:

    ![](location-walkthrough-images/image6.png "Esempio di percorso output nella console di")

1. L'applicazione stampa aggiornamenti della posizione sullo schermo quando si opera in primo piano e continua a stampare i dati alla finestra di output dell'applicazione durante il funzionamento in background.

Rimane solo uno dei problemi in sospeso: la schermata di avvio aggiornamenti dell'interfaccia utente quando l'app viene caricata, ma non ha alcun modo di sapere quando l'app è rientrato in primo piano. Se l'applicazione backgrounded viene portata nuovamente in primo piano, non verranno effettuati aggiornamenti dell'interfaccia utente.

Per risolvere questo problema, annidare una chiamata per avviare gli aggiornamenti dell'interfaccia utente all'interno di un'altra notifica, che verrà generato quando l'applicazione passa allo stato attivo:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

Ora l'interfaccia utente inizierà l'aggiornamento quando l'applicazione prima di tutto viene avviata e riprendere l'aggiornamento ogni volta che l'app torna in primo piano.

In questa procedura dettagliata, abbiamo creato un'applicazione iOS ben progettate, in grado di riconoscere in background che consente di stampare dati sulla località per la schermata sia la finestra di output dell'applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Posizione (parte 4) (esempio)](https://developer.xamarin.com/samples/monotouch/Location/)
- [Riferimento al Framework di percorso core](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
