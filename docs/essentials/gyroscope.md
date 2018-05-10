---
title: Giroscopio Xamarin.Essentials
description: La classe Giroscopio consente di monitorare sensore Giroscopio del dispositivo che è la rotazione intorno a tre assi primari del dispositivo.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0fa6ed6a0ced97c2600a24860c4f42aee1a24161
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-gyroscope"></a>Giroscopio Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Giroscopio** classe consente di monitorare sensore Giroscopio del dispositivo che è la rotazione intorno a tre assi primari del dispositivo.

## <a name="using-gyroscope"></a>Utilizzando Giroscopio

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Giroscopio funziona chiamando il `Start` e `Stop` metodi per attendere le modifiche per il Giroscopio. Tutte le modifiche vengono inviate attraverso la `ReadingChanged` evento. Ecco l'esempio di utilizzo:

```csharp

public class GyroscopeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public GyroscopeTest()
    {
        // Register for reading changes.
        Gyroscope.ReadingChanged += Gyroscope_ReadingChanged;
    }

    void Gyroscope_ReadingChanged(GyroscopeChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Angular Velocity X, Y, and Z
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

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensore velocità](xref:Xamarin.Essentials.SensorSpeed)

- **Più veloce** -ottenere i dati del sensore più velocemente possibile (necessariamente restituire sul thread dell'interfaccia utente).
- **Gioco** – frequenza adatto per i giochi (necessariamente restituire sul thread dell'interfaccia utente).
- **Normale** – frequenza predefinita adatta per le modifiche di orientamento dello schermo.
- **Interfaccia utente** – frequenza adatto per l'interfaccia utente generali.

## <a name="api"></a>API

- [Codice sorgente Giroscopio](https://github.com/xamarin/Essentials/tree/master/Essentials/Gyroscope)
- [Documentazione Giroscopio API](xref:Xamarin.Essentials.Gyroscope)
