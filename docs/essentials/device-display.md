---
title: 'Xamarin.Essentials: Device Display Information'
description: Questo documento descrive la classe DeviceDisplay in Xamarin.Essentials, che fornisce le metriche dello schermo per il dispositivo cui viene eseguita l'applicazione.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ebe97cf7fbb78bff17196110e835bd35ff76b826
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674886"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Device Display Information

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **DeviceDisplay** fornisce informazioni sulle metriche dello schermo del dispositivo in cui viene eseguita l'applicazione.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Uso di DeviceDisplay

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>Metriche dello schermo

Oltre alle informazioni di base sul dispositivo, la classe **DeviceDisplay** contiene informazioni sullo schermo e l'orientamento del dispositivo.

```csharp
// Get Metrics
var metrics = DeviceDisplay.ScreenMetrics;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = metrics.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = metrics.Rotation;

// Width (in pixels)
var width = metrics.Width;

// Height (in pixels)
var height = metrics.Height;

// Screen density
var density = metrics.Density;
```

La classe **DeviceDisplay** espone anche un evento che è possibile sottoscrivere, attivato quando le metriche dello schermo vengono modificate:

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChangedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

Nessuna differenza.

# <a name="iostabios"></a>[iOS](#tab/ios)

* L'accesso a `ScreenMetrics` deve essere eseguito nel thread dell'interfaccia utente. In caso contrario, verrà generata un'eccezione. È possibile usare il metodo [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) per eseguire tale codice nel thread dell'interfaccia utente.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nessuna differenza.

--------------


## <a name="api"></a>API

- [Codice sorgente di DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentazione dell'API DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
