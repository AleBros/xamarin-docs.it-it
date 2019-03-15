---
title: 'Xamarin.Essentials: Geocodifica'
description: La classe Geocoding in Xamarin.Essentials fornisce le API sia per eseguire la codifica geografica di un indicatore di posizione in coordinate posizionali che per riconvertire le coordinate posizionali in un indicatore di posizione.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: bc08554f0060ca5de8d7ffc90ea5c65463b93f3b
ms.sourcegitcommit: 97dca3face7c4ad5555dfaca88f5b45a70ca556d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57972598"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: Geocodifica

La classe **Geocoding** fornisce le API sia per eseguire la codifica geografica di un indicatore di posizione in coordinate posizionali che per riconvertire le coordinate posizionali in un indicatore di posizione.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

Per accedere alla funzionalità **Geocoding**, è necessaria la configurazione seguente specifica della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Una chiave API di Mappe di Bing è necessaria per usare la funzionalità di codifica geografica. Iscriversi per un account [Mappe di Bing](https://www.bingmapsportal.com/) gratuito. In **Account personale > My keys** (Chiavi personali) creare una nuova chiave e immettere le informazioni in base al tipo di applicazione, che dovrebbe essere un'**app di Windows pubblica (UWP, 8.x e versioni successive)** per le app UWP.

Prima di chiamare qualsiasi metodo **Geocoding**, impostare subito la chiave API dell'applicazione (disponibile solo nella piattaforma UWP):

```csharp
Platform.MapServiceToken = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>Uso di Geocoding

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Acquisizione delle coordinate [geografiche](xref:Xamarin.Essentials.Location) per un indirizzo:

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}, Altitude: {location.Altitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

L'altitudine non è sempre disponibile. Se non è disponibile, la proprietà `Altitude` potrebbe essere `null` o il valore potrebbe essere pari a zero. Se l'altitudine è disponibile, il valore è espresso in metri sopra il livello del mare.

## <a name="using-reverse-geocoding"></a>Uso della geocodifica inversa

La geocodifica inversa è il processo di acquisizione degli [indicatori di posizione](xref:Xamarin.Essentials.Placemark) per un set esistente di coordinate:

```csharp
try
{
    var lat = 47.673988;
    var lon = -122.121513;

    var placemarks = await Geocoding.GetPlacemarksAsync(lat, lon);

    var placemark = placemarks?.FirstOrDefault();
    if (placemark != null)
    {
        var geocodeAddress =
            $"AdminArea:       {placemark.AdminArea}\n" +
            $"CountryCode:     {placemark.CountryCode}\n" +
            $"CountryName:     {placemark.CountryName}\n" +
            $"FeatureName:     {placemark.FeatureName}\n" +
            $"Locality:        {placemark.Locality}\n" +
            $"PostalCode:      {placemark.PostalCode}\n" +
            $"SubAdminArea:    {placemark.SubAdminArea}\n" +
            $"SubLocality:     {placemark.SubLocality}\n" +
            $"SubThoroughfare: {placemark.SubThoroughfare}\n" +
            $"Thoroughfare:    {placemark.Thoroughfare}\n";

        Console.WriteLine(geocodeAddress);
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occurred in geocoding
}
```

## <a name="distance-between-two-locations"></a>Distanza tra due località

Le classi [`Location`](xref:Xamarin.Essentials.Location) e [`LocationExtensions`](xref:Xamarin.Essentials.LocationExtensions) definiscono i metodi per calcolare la distanza tra due località. Per un esempio, vedere l'articolo [**Xamarin.Essentials: Georilevazione**](geolocation.md#calculate-distance).

## <a name="api"></a>API

- [Codice sorgente di Geocoding](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Documentazione dell'API Geocoding](xref:Xamarin.Essentials.Geocoding)
