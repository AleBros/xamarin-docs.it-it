---
title: Informazioni visualizzate Xamarin.Essentials dispositivo
description: La classe DeviceDisplay fornisce informazioni sulle metriche di schermata del dispositivo all'applicazione sono in esecuzione in.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 570fb6cf3eadfbbc7e189f875498d3cea0bc1514
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-device-display-information"></a>Informazioni visualizzate Xamarin.Essentials dispositivo

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **DeviceDisplay** classe fornisce informazioni sulle metriche dello schermo del dispositivo è in esecuzione l'applicazione.

## <a name="using-devicedisplay"></a>Utilizzo DeviceDisplay

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>Metriche dello schermo

Oltre alle informazioni di base del dispositivo il **DeviceDisplay** classe contiene informazioni sulla schermata e l'orientamento del dispositivo.

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

Il **DeviceDisplay** classe inoltre espone e l'evento che può essere sottoscritti che generi un evento ogni volta che qualsiasi schermata le modifiche alla metrica:

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanaged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChanagedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="api"></a>API

- [Codice sorgente DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Essentials/DeviceDisplay)
- [Documentazione dell'API DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
