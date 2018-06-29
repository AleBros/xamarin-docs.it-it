---
title: 'Xamarin.Essentials: Magnetometro'
description: La classe Magnetometro in Xamarin.Essentials consente di monitorare sensore magnetometro del dispositivo, che indica l'orientamento del dispositivo rispetto al campo magnetico della terra.
ms.assetid: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2c02188b5282949559e0abc5fa1b61b6b451fc8e
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080496"
---
# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials: Magnetometro

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Magnetometro** classe consente di monitorare sensore magnetometro del dispositivo che indica l'orientamento del dispositivo rispetto al campo magnetico della terra.

## <a name="using-magnetometer"></a>Utilizzo Magnetometro

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Magnetometro funziona chiamando il `Start` e `Stop` metodi per attendere le modifiche per il magnetometro. Tutte le modifiche vengono inviate attraverso la `ReadingChanged` evento. Ecco l'esempio di utilizzo:

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometerr_ReadingChanged(MagnetometerChangedEventArgs e)
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

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensore velocità](xref:Xamarin.Essentials.SensorSpeed)

- **Più veloce** -ottenere i dati del sensore più velocemente possibile (necessariamente restituire sul thread dell'interfaccia utente).
- **Gioco** – frequenza adatto per i giochi (necessariamente restituire sul thread dell'interfaccia utente).
- **Normale** – frequenza predefinita adatta per le modifiche di orientamento dello schermo.
- **Interfaccia utente** – frequenza adatto per l'interfaccia utente generali.

Se il gestore dell'evento non è garantito che eseguite sul thread dell'interfaccia utente e se il gestore dell'evento è necessario accedere agli elementi dell'interfaccia utente, usare il [ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) metodo per eseguire il codice nel thread UI.

## <a name="api"></a>API

- [Codice sorgente magnetometro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [Documentazione magnetometro API](xref:Xamarin.Essentials.Magnetometer)
