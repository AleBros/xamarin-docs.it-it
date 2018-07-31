---
title: 'Xamarin.Essentials: Magnetometro'
description: La classe Magnetometro in Xamarin.Essentials consente di monitorare il sensore magnetometro del dispositivo, che indica l'orientamento del dispositivo rispetto al campo magnetico della terra.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3827b9a57ec2667a8716f5b56bfb4631b979d43a
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353789"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials: Magnetometro

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Magnetometro** classe consente di monitorare sensore magnetometro del dispositivo che indica l'orientamento del dispositivo rispetto al campo magnetico della terra.

## <a name="using-magnetometer"></a>Usando Magnetometro

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Magnetometro funziona chiamando il `Start` e `Stop` metodi per l'ascolto delle modifiche per il magnetometro. Eventuali modifiche vengono inviate attraverso la `ReadingChanged` evento. Ecco l'esempio di utilizzo:

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

Tutti i dati vengono restituiti in microteslas.

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Codice sorgente magnetometro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [Documentazione di magnetometro API](xref:Xamarin.Essentials.Magnetometer)
