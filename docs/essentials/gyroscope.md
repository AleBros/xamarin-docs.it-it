---
title: 'Xamarin.Essentials: Giroscopio'
description: La classe giroscopio in Xamarin.Essentials consente di monitorare il sensore giroscopio del dispositivo, che misura la rotazione intorno ai tre assi principali del dispositivo.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5a085ac5bd18e660339443ea33fe552dd86259cb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802297"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: Giroscopio

La classe **Gyroscope** consente di monitorare il sensore del giroscopio del dispositivo, ovvero la rotazione intorno ai tre assi primari del dispositivo.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-gyroscope"></a>Uso di Gyroscope

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Gyroscope opera chiamando i metodi `Start` e `Stop` per attivare l'ascolto dei cambi del giroscopio. Le eventuali modifiche vengono inviate tramite l'evento `ReadingChanged` in rad/s. Utilizzo di esempio:

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(object sender, GyroscopeChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Angular Velocity X, Y, and Z reported in rad/s
        Console.WriteLine($"Reading: X: {data.AngularVelocity.X}, Y: {data.AngularVelocity.Y}, Z: {data.AngularVelocity.Z}");
    }

    public void ToggleGyroscope()
    {
        try
        {
            if (Gyroscope.IsMonitoring)
              Gyroscope.Stop();
            else
              Gyroscope.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Codice sorgente di Gyroscope](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Gyroscope)
- [Documentazione dell'API Gyroscope](xref:Xamarin.Essentials.Gyroscope)
