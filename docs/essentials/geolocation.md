---
title: " Xamarin.Essentials : Geolocation" Description: "questo documento descrive la classe Geolocation in Xamarin.Essentials , che fornisce le API per recuperare le coordinate geografiche correnti del dispositivo".
ms. AssetID: 8F66092C-13F0-4FEE-8AA5-901D5F79B357 Author: jamesmontemagno ms. Custom: video ms. Author: Jamont ms. Date: 03/13/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials: Georilevazione

La classe **Geolocation** fornisce le API per recuperare le coordinate di georilevazione correnti del dispositivo.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

Per accedere alla funzionalità **Geolocation**, è necessaria la configurazione seguente specifica della piattaforma:

# <a name="android"></a>[Android](#tab/android)

Le autorizzazioni Coarse e Fine Location sono obbligatorie e devono essere configurate nel progetto Android. Se inoltre l'app usa come destinazione Android 5.0 (livello API 21) o versione successiva, è necessario dichiarare che l'app usa le funzionalità hardware nel file manifesto. È possibile aggiungerla nei modi seguenti:

Aprire il file **AssemblyInfo.cs** nella cartella **Proprietà** e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

Oppure aggiornare il manifesto di Android:

Aprire il file **AndroidManifest.xml** nella cartella **Proprietà** e aggiungere quanto segue all'interno del nodo **manifest**:

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

Oppure fare clic con il pulsante destro del mouse sul progetto Android e aprire le proprietà del progetto. In **Manifesto Android** trovare l'area **Autorizzazioni necessarie** e selezionare le autorizzazioni **ACCESS_COARSE_LOCATION** e **ACCESS_FINE_LOCATION**. Il file **AndroidManifest.xml** verrà aggiornato automaticamente.

[!include[](~/essentials/includes/android-permissions.md)]

# <a name="ios"></a>[iOS](#tab/ios)

Il file **Info.plist** dell'app deve contenere la chiave `NSLocationWhenInUseUsageDescription` per accedere alla posizione del dispositivo.

Aprire l'editor plist, aggiungere la proprietà **Privacy - Location When In Use Usage Description** (Privacy - Descrizione utilizzo Rileva posizione durante l'uso) e immettere un valore per visualizzare l'utente.

Oppure modificare manualmente il file, aggiungere quanto segue e aggiornare la logica:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>Fill in a reason why your app needs access to location.</string>
```

# <a name="uwp"></a>[UWP](#tab/uwp)

È necessario impostare l'autorizzazione `Location` per l'applicazione. A tale scopo, aprire **Package.appxmanifest**, selezionare la scheda **Capabilities** (Capacità) e selezionare **Location** (Posizione).

-----

## <a name="using-geolocation"></a>Uso di Geolocation

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

L'API per la georilevazione richiederà anche le autorizzazioni all'utente quando necessario.

È possibile ottenere l'ultima [posizione](xref:Xamarin.Essentials.Location) conosciuta del dispositivo chiamando il metodo `GetLastKnownLocationAsync`. Questa procedura è spesso più veloce che eseguire una query completa, ma può essere meno accurata e potrebbe restituire `null` in assenza di un percorso memorizzato nella cache.

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (FeatureNotEnabledException fneEx)
{
    // Handle not enabled on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

L'altitudine non è sempre disponibile. Se non è disponibile, la proprietà `Altitude` potrebbe essere `null` o il valore potrebbe essere pari a zero. Se l'altitudine è disponibile, il valore è espresso in metri sopra il livello del mare.

Per eseguire una query delle coordinate [geografiche](xref:Xamarin.Essentials.Location) del dispositivo corrente, si può usare `GetLocationAsync`. È preferibile passare `GeolocationRequest` e `CancellationToken` completi perché l'acquisizione della posizione del dispositivo potrebbe richiedere tempo.

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (FeatureNotEnabledException fneEx)
{
    // Handle not enabled on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

## <a name="geolocation-accuracy"></a>Accuratezza della georilevazione

La tabella seguente indica l'accuratezza per ogni piattaforma:

### <a name="lowest"></a>Più bassa

| Piattaforma | Distanza (in metri) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| Piattaforma UWP | 1000 - 5000 |

### <a name="low"></a>Basso

| Piattaforma | Distanza (in metri) |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| Piattaforma UWP | 300 - 3000 |

### <a name="medium-default"></a>Media (impostazione predefinita)

| Piattaforma | Distanza (in metri) |
| --- | --- |
| Android | 100 - 500 |
| iOS | 100 |
| Piattaforma UWP | 30 - 500 |

### <a name="high"></a>Alta

| Piattaforma | Distanza (in metri) |
| --- | --- |
| Android | 0 - 100 |
| iOS | 10 |
| Piattaforma UWP | <= 10 |

### <a name="best"></a>Ottimale

| Piattaforma | Distanza (in metri) |
| --- | --- |
| Android | 0 - 100 |
| iOS | ~0 |
| Piattaforma UWP | <= 10 |

<a name="calculate-distance"></a>

## <a name="detecting-mock-locations"></a>Rilevamento di località fittizie
Alcuni dispositivi possono restituire una località fittizia proposta dal provider o da un'applicazione che fornisce località fittizie. È possibile rilevarlo utilizzando `IsFromMockProvider` in qualsiasi [`Location`](xref:Xamarin.Essentials.Location) .

```csharp
var request = new GeolocationRequest(GeolocationAccuracy.Medium);
var location = await Geolocation.GetLocationAsync(request);

if (location != null)
{
    if(location.IsFromMockProvider)
    {
        // location is from a mock provider
    }
}
```

## <a name="distance-between-two-locations"></a>Distanza tra due località

Le [`Location`](xref:Xamarin.Essentials.Location) [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) classi e definiscono `CalculateDistance` metodi che consentono di calcolare la distanza tra due posizioni geografiche. Questa distanza calcolata non tiene in considerazione le strade o altri percorsi, ma è semplicemente la distanza più breve tra due punti sulla superficie terrestre, nota anche come _ortodromia_ o, colloquialmente, distanza "a volo d'uccello".

Ad esempio:

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

Il costruttore `Location` ha gli argomenti di latitudine e longitudine in quest'ordine. I valori di latitudine positiva sono a nord dell'equatore e i valori di longitudine positiva sono a est del Meridiano di Greenwich. Usare l'argomento finale per `CalculateDistance` per specificare miglia o chilometri. La classe `UnitConverters` definisce anche i metodi `KilometersToMiles` e `MilesToKilometers` per eseguire la conversione da un'unità di misura all'altra.

## <a name="platform-differences"></a>Differenze tra le piattaforme

L'altitudine viene calcolata in modo diverso in ogni piattaforma.

# <a name="android"></a>[Android](#tab/android)

In Android, [Altitude](https://developer.android.com/reference/android/location/Location#getAltitude()), se disponibile, viene restituito in metri sopra l'ellissoide di riferimento WGS 84. Se il percorso non ha un'altitudine, viene restituito 0,0.

# <a name="ios"></a>[iOS](#tab/ios)

In iOS, l' [altitudine](https://developer.apple.com/documentation/corelocation/cllocation/1423820-altitude) viene misurata in metri. I valori positivi indicano le Altitude sopra il livello del mare, mentre i valori negativi indicano le Altitude al di sotto del livello Sea.

# <a name="uwp"></a>[UWP](#tab/uwp)

In UWP, l'altitudine viene restituita in metri. Per ulteriori informazioni, vedere la documentazione di [AltitudeReferenceSystem](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geopoint.altitudereferencesystem#Windows_Devices_Geolocation_Geopoint_AltitudeReferenceSystem) .

-----

## <a name="api"></a>API

- [Codice sorgente di Geolocation](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [Documentazione dell'API Geolocation](xref:Xamarin.Essentials.Geolocation)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Geolocation-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
