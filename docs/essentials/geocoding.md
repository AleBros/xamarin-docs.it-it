---
title: Geocodifica Xamarin.Essentials
description: La classe Geocodifica offre API a geocode un placemark una corrispondenza delle coordinate posizionali e invertire geocode coordincates un placemark.
ms.assetid: 3ADC440C-B000-4708-A2CC-296F5160AF90
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0bfc463ca22d4137eadc35c02375fe41b0b99b8a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-geocoding"></a>Geocodifica Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Geocodifica** classe offre API a geocode un placemark una corrispondenza delle coordinate posizionali e invertire geocode coordincates un placemark.

## <a name="getting-started"></a>Introduzione

Per l'accesso di **Geocodifica** è necessaria la funzionalità le seguenti impostazioni specifiche della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Chiave dell'API di Bing mappe è obbligatorio utilizzare funcationality geocodifica. Iscriversi a una liberazione [Bing Maps](https://www.bingmapsportal.com/) account. Sotto **mio account > le chiavi** creare una nuova chiave e le informazioni in base al tipo di applicazione.

All'inizio di vita dell'applicazione prima di chiamare qualsiasi **Geocodifica** metodi impostano la chiave API:

```csharp
Geocoding.MapKey = "YOUR-KEY-HERE";
```

-----

## <a name="using-geocoding"></a>Utilizzo di Geocodifica

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Recupero [posizione](xref:Xamarin.Essentials.Location) coordinate per un indirizzo:

```csharp
try
{
    var address =  "Microsoft Building 25 Redmond WA USA";
    var locations = await Geocoding.GetLocationsAsync(address);

    var location = locations?.FirstOrDefault();
    if (location != null)
    {
        Console.WriteLine($"Latitude: {location.Latitude}, Longitude: {location.Longitude}");
    }
}
catch (FeatureNotSupportedException fnsEx)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Handle exception that may have occured in geocoding
}
```

Recupero [placemarks](xref:Xamarin.Essentials.Placemark) per un set esistente di coordinate:

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
    // Handle exception that may have occured in geocoding
}
```

## <a name="api"></a>API

- [Codice sorgente geocodifica](https://github.com/xamarin/Essentials/tree/master/Essentials/Geocoding)
- [Documentazione di geocodifica API](xref:Xamarin.Essentials.Geocoding)
