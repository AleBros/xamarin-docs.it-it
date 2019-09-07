---
title: 'Xamarin.Essentials: Informazioni sullo schermo del dispositivo'
description: Questo documento descrive la classe DeviceDisplay in Xamarin.Essentials, che fornisce le metriche dello schermo per il dispositivo cui viene eseguita l'applicazione.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 77af173bc3297ac9ccdef22dccbeab054895f772
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756897"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Informazioni sullo schermo del dispositivo

La classe **DeviceDisplay** fornisce informazioni sulle metriche dello schermo del dispositivo in cui è in esecuzione l'applicazione e può richiedere di evitare la sospensione dello schermo quando l'applicazione è in esecuzione.

## <a name="get-started"></a>Attività iniziali

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Uso di DeviceDisplay

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="main-display-info"></a>Informazioni sullo schermo principale

Oltre alle informazioni di base sul dispositivo, la classe **DeviceDisplay** contiene informazioni sullo schermo e l'orientamento del dispositivo.

```csharp
// Get Metrics
var mainDisplayInfo = DeviceDisplay.MainDisplayInfo;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = mainDisplayInfo.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = mainDisplayInfo.Rotation;

// Width (in pixels)
var width = mainDisplayInfo.Width;

// Height (in pixels)
var height = mainDisplayInfo.Height;

// Screen density
var density = mainDisplayInfo.Density;
```

La classe **DeviceDisplay** espone anche un evento che è possibile sottoscrivere, attivato quando le metriche dello schermo vengono modificate:

```csharp
public class DisplayInfoTest
{
    public DisplayInfoTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.MainDisplayInfoChanged += OnMainDisplayInfoChanged;
    }

    void OnMainDisplayInfoChanged(object sender, DisplayInfoChangedEventArgs  e)
    {
        // Process changes
        var displayInfo = e.DisplayInfo;
    }
}
```

La classe **DeviceDisplay** espone una proprietà `bool` denominata `KeepScreenOn` che può essere impostata per evitare lo spegnimento o il blocco dello schermo del dispositivo.

```csharp
public class KeepScreenOnTest
{
    public void ToggleScreenLock()
    {
        DeviceDisplay.KeepScreenOn = !DeviceDisplay.KeepScreenOn;
    }
}
```

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

Nessuna differenza.

# <a name="iostabios"></a>[iOS](#tab/ios)

- L'accesso a `DeviceDisplay` deve essere eseguito nel thread dell'interfaccia utente. In caso contrario, verrà generata un'eccezione. È possibile usare il metodo [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) per eseguire tale codice nel thread dell'interfaccia utente.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nessuna differenza.

--------------

## <a name="api"></a>API

- [Codice sorgente di DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentazione dell'API DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
