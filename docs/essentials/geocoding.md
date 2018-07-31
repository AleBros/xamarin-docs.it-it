---
title: 'Xamarin.Essentials: la Geocodifica'
description: La classe la Geocodifica in Xamarin.Essentials fornisce le API per la geocodifica entrambi un placemark un coordinate posizionali e invertire una placemark geocode coordinate.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a4d6e4d9b32e665893d82693a3c858630b63d372
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353675"
---
# <a name="xamarinessentials-geocoding"></a>Xamarin.Essentials: la Geocodifica

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Geocodifica** classe fornisce le API per la geocodifica un placemark un coordinate posizionali e invertire una placemark geocode coordinate.

## <a name="getting-started"></a>Introduzione

Per l'accesso di **Geocodifica** è necessaria la funzionalità le seguenti impostazioni specifiche della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Una chiave API di Bing mappe è necessario usare la funzionalità di geocodifica. Iscriversi per ottenere una [Bing Maps](https://www.bingmapsportal.com/) account. Sotto **il mio account > i codici** creare una nuova chiave e immettere le informazioni di base al tipo di applicazione (che deve essere **pubblica App di Windows (UWP, 8.x e versioni precedenti)** per le app UWP).

Sin dall'inizio nel ciclo di vita dell'applicazione prima di chiamare qualsiasi **Geocodifica** metodi impostano la chiave API:

```csharp
Geocoding.MapKey = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>Usando la Geocodifica

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Getting [posizione](xref:Xamarin.Essentials.Location) coordinate per un indirizzo:

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

Altitudine non è sempre disponibile. Se non è disponibile, il `Altitude` proprietà potrebbe essere `null` o il valore potrebbe essere zero. Se è disponibile l'altitudine, il valore è in metri sopra livello del mare.

Getting [placemarks](xref:Xamarin.Essentials.Placemark) per un set esistente di coordinate:

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

## <a name="distance-between-two-locations"></a>Distanza tra due posizioni

Il [ `Location` ](xref:Xamarin.Essentials.Location) e [ `LocationExtensions` ](xref:Xamarin.Essentials.LocationExtensions) classi definiscono i metodi per calcolare la distanza tra due posizioni. Vedere l'articolo [ **Xamarin.Essentials: Georilevazione** ](geolocation.md#calculate-distance) per un esempio.

## <a name="api"></a>API

- [Codice sorgente di geocodifica](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Geocoding)
- [Documentazione delle API di geocodifica](xref:Xamarin.Essentials.Geocoding)
