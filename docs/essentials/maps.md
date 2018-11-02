---
title: 'Xamarin.Essentials: Mappe'
description: La classe Maps in Xamarin.Essentials consente a un'applicazione di aprire l'applicazione per le mappe installata su una località o un indicatore di posizione specifici.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: fb4cbc2fd334d574abc57a3359fa346bc6795408
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674774"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials: Mappe

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **Maps** consente a un'applicazione di aprire l'applicazione per le mappe installata su una località o un indicatore di posizione specifici.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-maps"></a>Uso di Maps

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Maps opera chiamando il metodo `OpenAsync` con `Location` o `Placemark` per l'apertura con le opzioni facoltative `MapsLaunchOptions`.

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(location, options);
    }
}
```

In caso di apertura con `Placemark`, sono necessarie le informazioni seguenti:

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapsTest
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
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>Metodi di estensione

Se è già disponibile un riferimento a `Location` oppure `Placemark`, è possibile usare il metodo di estensione predefinito `OpenMapsAsync` con le opzioni facoltative `MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="directions-mode"></a>Modalità indicazioni

Se si chiama `OpenMapsAsync` senza `MapsLaunchOptions`, la mappa verrà avviata sulla posizione specificata. Facoltativamente, è possibile calcolare un percorso dalla posizione corrente del dispositivo. Questa operazione viene eseguita impostando `MapDirectionsMode` in `MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { MapDirectionsMode = MapDirectionsMode.Driving };

        await Maps.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

- `MapDirectionsMode` supporta Bicycling, Driving e Walking.

# <a name="iostabios"></a>[iOS](#tab/ios)

- `MapDirectionsMode` supporta Driving, Transit e Walking.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- `MapDirectionsMode` supporta Driving, Transit e Walking.

--------------

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android usa lo schema URI `geo:` per avviare l'applicazione per le mappe nel dispositivo. Ciò potrebbe richiedere all'utente di effettuare una selezione da un'app esistente che supporta questo schema URI.  Xamarin.Essentials è stato testato con Google Maps, che supporta questo schema.

# <a name="iostabios"></a>[iOS](#tab/ios)

Nessun dettaglio di implementazione specifico della piattaforma.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nessun dettaglio di implementazione specifico della piattaforma.

--------------

## <a name="api"></a>API

- [Codice sorgente di Maps](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [Documentazione dell'API Maps](xref:Xamarin.Essentials.Maps)
