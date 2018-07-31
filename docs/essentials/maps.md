---
title: Mappe Xamarin.Essentials
description: La classe esegue il mapping in Xamarin.Essentials consente a un'applicazione aprire l'applicazione di mappe installate in un percorso specifico o placemark.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 445e2da84e9a9aaf1ce4d836af11cfba963b8cbb
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353945"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials: mappe

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **viene eseguito il mapping** classe consente a un'applicazione aprire l'applicazione di mappe installate in un percorso specifico o placemark.

## <a name="using-maps"></a>Utilizzo di mappe

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di mappe funziona chiamando il `OpenAsync` metodo con il `Location` o `Placemark` per aprire con facoltativo `MapsLaunchOptions`.

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

Quando si apre con un `Placemark` sono necessarie le informazioni seguenti:

* `CountryName`
* `AdminArea`
* `Thoroughfare`
* `Locality`

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

Se si dispone già di un riferimento a un `Location` oppure `Placemark` è possibile usare il metodo di estensione incorporati `OpenMapsAsync` facoltativamente `MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `MapDirectionsMode` non è supportata e non ha alcun effetto.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `MapDirectionsMode` è possibile per impostare la modalità di direzione predefinita quando viene aperto l'applicazione esegue il mapping.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

* `MapDirectionsMode` non è supportata e non ha alcun effetto.

--------------

## <a name="platform-implementation-specifics"></a>Funzionalità specifiche di implementazione della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android Usa la `geo:` schema Uri per avviare l'applicazione esegue il mapping nel dispositivo. Ciò potrebbe richiedere all'utente di selezionare da un'app esistente che supporta questo schema Uri.  Xamarin.Essentials viene testato con Google Maps che supporta questo schema.

# <a name="iostabios"></a>[iOS](#tab/ios)

Nessun dettaglio di implementazione specifica della piattaforma.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Nessun dettaglio di implementazione specifica della piattaforma.

--------------

## <a name="api"></a>API

- [Mappe di codice sorgente](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [Documentazione di mappe di API](xref:Xamarin.Essentials.Maps)
