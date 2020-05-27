---
title: 'Xamarin.Essentials: Map'
description: La classe Map in Xamarin.Essentials consente a un'applicazione di aprire l'applicazione per le mappe installata su una località o un indicatore di posizione specifici.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 05/26/2020
ms.custom: video
ms.openlocfilehash: 16f4a69e2d9216bb2e03fbcb663403d198b42c98
ms.sourcegitcommit: 6a6cbb62bcf149f9515ee8868679a8a07ce17956
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83862716"
---
# <a name="xamarinessentials-map"></a>Xamarin.Essentials: Map

La classe **Map** consente a un'applicazione di aprire l'applicazione per le mappe installata su una località o un indicatore di posizione specifici.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-map"></a>Uso di Map

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Map opera chiamando il metodo `OpenAsync` con `Location` o `Placemark` per l'apertura con le opzioni facoltative `MapLaunchOptions`.

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        try
        {
            await Map.OpenAsync(location, options);
        }
        catch (Exception ex)
        {
            // No map application available to open
        }
    }
}
```

In caso di apertura con `Placemark`, sono necessarie le informazioni seguenti:

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var placemark = new Placemark
            {
                CountryName = "United States",
                AdminArea = "WA",
                Thoroughfare = "Microsoft Building 25",
                Locality = "Redmond"
            };
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        try
        {
            await Map.OpenAsync(placemark, options);
        }
        catch (Exception ex)
        {
            // No map application available to open or placemark can not be located
        }
    }
}
```

## <a name="extension-methods"></a>Metodi di estensione

Se è già disponibile un riferimento a `Location` oppure `Placemark`, è possibile usare il metodo di estensione predefinito `OpenMapAsync` con le opzioni facoltative `MapLaunchOptions`:

```csharp
public class MapTest
{
    public async Task OpenPlacemarkOnMap(Placemark placemark)
    {
        try
        {
            await placemark.OpenMapAsync();
        }
        catch (Exception ex)
        {
            // No map application available to open
        }
    }
}
```

## <a name="directions-mode"></a>Modalità indicazioni

Se si chiama `OpenMapAsync` senza `MapLaunchOptions`, la mappa verrà avviata sulla posizione specificata. Facoltativamente, è possibile calcolare un percorso dalla posizione corrente del dispositivo. Questa operazione viene eseguita impostando `NavigationMode` in `MapLaunchOptions`:

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { NavigationMode = NavigationMode.Driving };

        await Map.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="android"></a>[Android](#tab/android)

- `NavigationMode` supporta Bicycling, Driving e Walking.

# <a name="ios"></a>[iOS](#tab/ios)

- `NavigationMode` supporta Driving, Transit e Walking.

# <a name="uwp"></a>[UWP](#tab/uwp)

- `NavigationMode` supporta Driving, Transit e Walking.

--------------

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="android"></a>[Android](#tab/android)

Android usa lo schema URI `geo:` per avviare l'applicazione per le mappe nel dispositivo. Ciò potrebbe richiedere all'utente di effettuare una selezione da un'app esistente che supporta questo schema URI.  Xamarin.Essentials è stato testato con Google Maps, che supporta questo schema.

# <a name="ios"></a>[iOS](#tab/ios)

Nessun dettaglio di implementazione specifico della piattaforma.

# <a name="uwp"></a>[UWP](#tab/uwp)

Nessun dettaglio di implementazione specifico della piattaforma.

--------------

## <a name="api"></a>API

- [Codice sorgente di Map](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Map)
- [Documentazione dell'API Map](xref:Xamarin.Essentials.Map)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Maps-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
