---
title: 'Xamarin.Essentials: Magnetometro'
description: La classe magnetometro in Xamarin.Essentials consente di monitorare il sensore magnetometro del dispositivo, che indica l'orientamento del dispositivo rispetto al campo magnetico della terra.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b01bc1fd9c65186952635c5f472b1ac6beb0c9bd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802281"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials: Magnetometro

La classe **Magnetometer** consente di monitorare il magnetometro del dispositivo, che indica l'orientamento del dispositivo rispetto al campo magnetico della Terra.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-magnetometer"></a>Uso di Magnetometer

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Magnetometer opera chiamando i metodi `Start` e `Stop` per attivare l'ascolto dei cambi del magnetometro. Le eventuali modifiche vengono inviate tramite l'evento `ReadingChanged`. Utilizzo di esempio:

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometer_ReadingChanged(object sender, MagnetometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process MagneticField X, Y, and Z
        Console.WriteLine($"Reading: X: {data.MagneticField.X}, Y: {data.MagneticField.Y}, Z: {data.MagneticField.Z}");
    }

    public void ToggleMagnetometer()
    {
        try
        {
            if (Magnetometer.IsMonitoring)
              Magnetometer.Stop();
            else
              Magnetometer.Start(speed);
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

Tutti i dati vengono restituiti in μT (microtesla).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Codice sorgente di Magnetometer](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Magnetometer)
- [Documentazione dell'API Magnetometer](xref:Xamarin.Essentials.Magnetometer)
