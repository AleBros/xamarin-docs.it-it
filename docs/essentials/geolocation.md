---
title: Xamarin.Essentials Georilevazione
description: La classe Georilevazione fornisce le API per recuperare le coordinate di georilevazione corrente del dispositivo.
ms.assetid: 8F66092C-13F0-4FEE-8AA5-901D5F79B357
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ead498113f432e766fbd77ae2f01bc67c2273b60
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-geocoding"></a>Geocodifica Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Georilevazione** classe fornisce le API per recuperare le coordinate di georilevazione corrente del dispositivo.

## <a name="getting-started"></a>Introduzione

Per l'accesso di **Georilevazione** è necessaria la funzionalità le seguenti impostazioni specifiche della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Le autorizzazioni distante e la posizione di Fine sono necessari e devono essere configurate nel progetto Android. Inoltre, se l'app è destinata Android 5.0 (livello API 21) o versioni successive, è necessario dichiarare che l'app Usa le funzionalità hardware nel file manifesto. Questo può essere aggiunto nei modi seguenti:

Aprire la **AssemblyInfo.cs** file sotto il **proprietà** cartella e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessCoarseLocation)]
[assembly: UsesPermission(Android.Manifest.Permission.AccessFineLocation)]
[assembly: UsesFeature("android.hardware.location", Required = false)]
[assembly: UsesFeature("android.hardware.location.gps", Required = false)]
[assembly: UsesFeature("android.hardware.location.network", Required = false)]
```

O aggiornare il manifesto Android:

Aprire la **androidmanifest. XML** file sotto il **proprietà** cartella e aggiungere il codice seguente all'interno del **manifesto** nodo.

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-feature android:name="android.hardware.location" android:required="false" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
<uses-feature android:name="android.hardware.location.network" android:required="false" />
```

Oppure fare clic con il pulsante destro sul progetto di piattaforma Android e aprire le proprietà del progetto. Sotto **manifesto Android** trovare la **delle autorizzazioni necessarie:** area e controllare il **ACCESS_COARSE_LOCATION** e **ACCESS_FINE_LOCATION**le autorizzazioni. Verrà automaticamente aggiornato il **Androidmanifest** file.

# <a name="iostabios"></a>[iOS](#tab/ios)

L'app è necessario disporre di chiavi **Info. plist** per NSLocationWhenInUseUsageDescription per accedere alla posizione del dispositivo.

Aprire l'editor plist e aggiungere la **Privacy - percorso quando In uso descrizione dell'utilizzo** proprietà e inserire un valore da visualizzare all'utente.

O modificare il file manualmente e aggiungere quanto segue:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs access location when open.</string>
```

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

È necessario impostare il `Location` dell'autorizzazione per l'applicazione. Questa operazione può essere eseguita da aprire la **package. appxmanifest** selecing e il **funzionalità** scheda e il controllo **percorso**.

-----

## <a name="using-geolocation"></a>Utilizzo di Georilevazione

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

L'API Geoloation anche richiederà all'utente le autorizzazioni quando necessario.

È possibile ottenere l'ultimo valore noto [posizione](xref:Xamarin.Essentials.Location) del dispositivo chiamando il `GetLastKnownLocationAsync` metodo. Ciò è spesso più veloce quindi eseguire una query completa, ma può essere meno accurata.

```csharp
try
{
    var location = await Geolocation.GetLastKnownLocationAsync();

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
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

Per eseguire una query del dispositivo corrente [posizione](xref:Xamarin.Essentials.Location) coordinate il `GetLocationAsync` può essere utilizzato. È consigliabile passare in una procedura completa `GeolocationRequest` e `CancellationToken` poiché potrebbe richiedere tempo per ottenere la posizione del dispositivo.

```csharp
try
{
    var request = new GeolocationRequest(GeolocationAccuracy.Medium);
    var location = await Geolocation.GetLocationAsync(request);

    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
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

## <a name="geolocation-accuracy"></a>Accuratezza GeoLocation

Nella tabella seguente vengono illustrati l'accuratezza per ogni piattaforma

### <a name="lowest"></a>Più bassa

| Piattaforma | Distanza (in metri) |
| --- | --- |
| Android | 500 |
| iOS | 3000 |
| UWP | 1000 - 5000 |

### <a name="low"></a>Bassa

| Piattaforma | Distanza (in metri) |
| --- | --- |
| Android | 500 |
| iOS | 1000 |
| UWP | 300 - 3000 |

### <a name="medium-default"></a>Media (predefinita)

| Piattaforma | Distanza (in metri) |
| --- | --- |
| Android | 100 - 500 |
| iOS | 100 |
| UWP | 30-500 |

### <a name="high"></a>High

| Piattaforma | Distanza (in metri) |
| --- | --- |
| Android | 0 - 100 |
| iOS | 10 |
| UWP | < = 10 |

### <a name="best"></a>Migliore

| Piattaforma | Distanza (in metri) |
| --- | --- |
| Android | 0 - 100 |
| iOS | ~0 |
| UWP | < = 10 |

## <a name="api"></a>API

- [Codice sorgente di Georilevazione](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geolocation)
- [Documentazione dell'API di Georilevazione](xref:Xamarin.Essentials.Geolocation)
