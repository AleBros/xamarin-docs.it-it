---
title: 'Xamarin.Essentials: Georilevazione'
description: Questo documento descrive la classe di Georilevazione in Xamarin.Essentials, che fornisce le API per recuperare le coordinate di georilevazione corrente del dispositivo.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0aeb2ed96e6c21def69eb2e6f305b26e2e478825
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353854"
---
# <a name="xamarinessentials-geolocation"></a>Xamarin.Essentials: Georilevazione

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Georilevazione** classe fornisce le API per recuperare le coordinate di georilevazione corrente del dispositivo.

## <a name="getting-started"></a>Introduzione

Per l'accesso di **Georilevazione** funzionalità, è necessaria la configurazione seguente specifica della piattaforma:

# <a name="androidtabandroid"></a>[Android](#tab/android)

Bassa e il percorso corretto le autorizzazioni sono necessarie e devono essere configurate nel progetto Android. Inoltre, se l'app è destinata Android 5.0 (livello API 21) o versioni successive, è necessario dichiarare che l'app Usa le funzionalità hardware nel file manifesto. Ciò è possibile aggiungere nei modi seguenti:

Aprire il **AssemblyInfo.cs** file sotto il **proprietà** cartella e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

In alternativa, aggiornare il manifesto Android:

Aprire il **androidmanifest. XML** file sotto il **proprietà** cartella e aggiungere il codice seguente all'interno del **manifesto** nodo:

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

O destro del mouse sul progetto Android e aprire le proprietà del progetto. Sotto **manifesto Android** trovare il **autorizzazioni necessarie:** area e selezionare il **ACCESS_COARSE_LOCATION** e **ACCESS_FINE_LOCATION**le autorizzazioni. Si aggiornerà automaticamente il **androidmanifest. XML** file.

# <a name="iostabios"></a>[iOS](#tab/ios)

L'app **Info. plist** deve contenere il `NSLocationWhenInUseUsageDescription` chiave per accedere alla posizione del dispositivo.

Aprire l'editor plist e aggiungere il **Privacy - quando In uso descrizione utilizzo posizione** proprietà e il riempimento in un valore da visualizzare all'utente.

O modificare il file manualmente e aggiungere quanto segue:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

È necessario impostare il `Location` l'autorizzazione per l'applicazione. Questa operazione può essere eseguita aprendo il **package. appxmanifest** e selezionando le **funzionalità** scheda e il controllo **percorso**.

-----

## <a name="using-geolocation"></a>Uso di Georilevazione

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

L'API Geoloation anche richiederà all'utente le autorizzazioni quando necessario.

È possibile ottenere l'ultimo valore noto [ubicazione](xref:Xamarin.Essentials.Location) del dispositivo tramite la chiamata di `GetLastKnownLocationAsync` (metodo). Ciò è spesso più veloce effettuando quindi una query completa, ma può essere meno accurata.

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
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

Altitudine non è sempre disponibile. Se non è disponibile, il `Altitude` proprietà potrebbe essere `null` o il valore potrebbe essere zero. Se è disponibile l'altitudine, il valore è in metri sopra livello del mare. 

Per eseguire una query del dispositivo corrente [ubicazione](xref:Xamarin.Essentials.Location) coordinate, il `GetLocationAsync` può essere utilizzato. È consigliabile passare un completamente `GeolocationRequest` e `CancellationToken` poiché potrebbe richiedere alcuni minuti per ottenere il percorso del dispositivo.

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
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to get location
}
```

## <a name="geolocation-accuracy"></a>Accuratezza di Georilevazione

Nella tabella seguente vengono indicati l'accuratezza per ogni piattaforma:

### <a name="lowest"></a>Più bassa

| Piattaforma | Distanza (espressa in metri) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000: 5000 |

### <a name="low"></a>Bassa

| Piattaforma | Distanza (espressa in metri) |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| UWP | 300: 3000 |

### <a name="medium-default"></a>Media (predefinita)

| Piattaforma | Distanza (espressa in metri) |
| --- | --- |
| Android | 100 - 500 |
| iOS | 100 |
| UWP | 30-500 |

### <a name="high"></a>High

| Piattaforma | Distanza (espressa in metri) |
| --- | --- |
| Android | 0: 100 |
| iOS | 10 |
| UWP | < = 10 |

### <a name="best"></a>Migliore

| Piattaforma | Distanza (espressa in metri) |
| --- | --- |
| Android | 0: 100 |
| iOS | ~0 |
| UWP | < = 10 |

<a name="calculate-distance" />

## <a name="distance-between-two-locations"></a>Distanza tra due posizioni

Il [ `Location` ](xref:Xamarin.Essentials.Location) e [ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions) classi definiscono `CalculateDistance` metodi che consentono di calcolare la distanza tra due aree geografiche. Questa calcolata strade o altri percorsi non preso in considerazione, distanza e viene semplicemente la distanza più breve tra i due punti lungo la superficie della terra, noto anche come il _distanza ortodromica_ o il prelievo, il distanza "come file linea d'aria."

Di seguito è riportato un esempio:

```csharp
Location boston = new Location(42.358056, -71.063611);
Location sanFrancisco = new Location(37.783333, -122.416667);
double miles = Location.CalculateDistance(boston, sanFrancisco, DistanceUnits.Miles);
```

Il `Location` costruttore ha argomenti di latitudine e longitudine in quell'ordine. I valori di latitudine positivo sono a nord dell'equatore e sono i valori della longitudine positivo est meridiano principale. Usare l'argomento finale `CalculateDistance` specificare chilometri o miglia. Il `Location` classe definisce inoltre `KilometersToMiles` e `MilesToKilometers` metodi per la conversione tra le due unità.

## <a name="api"></a>API

- [Codice sorgente di Georilevazione](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [Documentazione dell'API di Georilevazione](xref:Xamarin.Essentials.Geolocation)
