---
title: 'Xamarin.Essentials: Informazioni di visualizzazione dispositivo'
description: Questo documento descrive la classe DeviceDisplay in Xamarin.Essentials, che fornisce le metriche di schermata per il dispositivo in cui viene eseguita l'applicazione.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3060d56e14fb0d3801a96ec0fe6e24c9efda4dac
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080313"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Informazioni di visualizzazione dispositivo

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

Il **DeviceDisplay** classe espone inoltre un evento che può essere sottoscritti che viene attivato quando uno schermata le modifiche alla metrica:

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanaged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChangedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="api"></a>API

- [Codice sorgente DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentazione dell'API DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
