---
title: " Xamarin.Essentials : Battery" Description: "questo documento descrive la classe Battery in Xamarin.Essentials , che consente di controllare le informazioni sulla batteria del dispositivo e di monitorare le modifiche".
ms. AssetID: 47EB26D8-8C62-477B-A13C-6977F74E6E43 Author: jamesmontemagno ms. Author: Jamont ms. Date: 01/22/2019 ms. Custom: video No-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: Batteria

La classe **Battery** consente di controllare le informazioni sulla batteria del dispositivo e monitorare eventuali modifiche e fornisce informazioni sullo stato di risparmio energia del dispositivo, che indica se il dispositivo è in esecuzione in modalità basso consumo. Le applicazioni dovrebbero evitare l'elaborazione in background se lo stato di risparmio energia del dispositivo è attivo.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

Per accedere alla funzionalità **Battery**, è necessaria la configurazione seguente specifica della piattaforma.

# <a name="android"></a>[Android](#tab/android)

L'autorizzazione `Battery` è obbligatoria e deve essere configurata nel progetto Android. È possibile aggiungerla nei modi seguenti:

Aprire il file **AssemblyInfo.cs** nella cartella **Proprietà** e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

OPPURE aggiornare il manifesto di Android:

Aprire il file **AndroidManifest.xml** nella cartella **Proprietà** e aggiungere quanto segue all'interno del nodo **manifesto** .

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

Oppure fare clic con il pulsante destro del mouse sul progetto Android e aprire le proprietà del progetto. In **Manifesto Android** trovare l'area **Autorizzazioni necessarie** e selezionare l'autorizzazione **Battery** (Batteria). Il file **AndroidManifest.xml** verrà aggiornato automaticamente.

# <a name="ios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwp"></a>[UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-battery"></a>Uso di Battery

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Controllare le informazioni sulla batteria correnti:

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or 1.0 when on AC or no battery.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.AC:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

Ogni volta che una delle proprietà della batteria cambia, viene attivato un evento:

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryInfoChanged += Battery_BatteryInfoChanged;
    }

    void Battery_BatteryInfoChanged(object sender, BatteryInfoChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

I dispositivi eseguiti a batteria possono essere messi in modalità di risparmio energia a basso consumo. In alcuni casi, questa modalità viene attivata automaticamente per i dispositivi, ad esempio quando la carica della batteria scende sotto il 20%. Il sistema operativo risponde alla modalità di risparmio energia riducendo le attività che tendono a scaricare la batteria. Le applicazioni possono contribuire evitando l'elaborazione in background o altre attività con un consumo elevato di energia, quando è attiva la modalità di risparmio energia.

È anche possibile ottenere lo stato corrente di risparmio energia del dispositivo usando la proprietà statica `Battery.EnergySaverStatus`:

```csharp
// Get energy saver status
var status = Battery.EnergySaverStatus;
```

Questa proprietà restituisce un membro dell'enumerazione `EnergySaverStatus`, ovvero `On`, `Off` o `Unknown`. Se la proprietà restituisce `On`, l'applicazione deve evitare l'elaborazione in background o altre attività che potrebbero usare molta energia.

L'applicazione deve anche installare un gestore eventi. La classe **Battery** espone un evento che viene attivato quando cambia lo stato di risparmio energia:

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Battery.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Se lo stato di risparmio energia diventa `On`, l'applicazione deve interrompere l'elaborazione in background. Se lo stato diventa `Unknown` o `Off`, l'applicazione può riprendere l'elaborazione in background.

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="android"></a>[Android](#tab/android)

Non esistono differenze per questa piattaforma.

# <a name="ios"></a>[iOS](#tab/ios)

- Per testare le API, deve essere usato il dispositivo.
- Verrà restituito solo `AC` o `Battery` per `PowerSource`.

# <a name="uwp"></a>[UWP](#tab/uwp)

- Verrà restituito solo `AC` o `Battery` per `PowerSource`.

-----

## <a name="api"></a>API

- [Codice sorgente di Battery](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Documentazione dell'API Battery](xref:Xamarin.Essentials.Battery)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Battery-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
