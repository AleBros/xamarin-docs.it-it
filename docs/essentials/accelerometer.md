---
title: 'Xamarin.Essentials: accelerometro'
description: La classe Accelerometer in Xamarin.Essentials consente di monitorare il sensore accelerometro del dispositivo, che indica l'accelerazione del dispositivo in uno spazio dimensionale tre.
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b5a24e214eb129b4d53b94586632791c8827447b
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353841"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials: accelerometro

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **accelerometro** classe consente di monitorare sensore accelerometro del dispositivo che indica l'accelerazione del dispositivo in uno spazio dimensionale tre.

## <a name="using-accelerometer"></a>Usando l'accelerometro

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di accelerometro funziona chiamando il `Start` e `Stop` metodi per l'ascolto delle modifiche per l'accelerazione. Eventuali modifiche vengono inviate attraverso la `ReadingChanged` evento. Ecco l'esempio di utilizzo:

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(object sender, AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Acceleration.X}, Y: {data.Acceleration.Y}, Z: {data.Acceleration.Z}");
        // Process Acceleration X, Y, and Z
    }

    public void ToggleAcceleromter()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
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

Le letture accelerometro vengano restituite nel G. G è un'unità di gravitation forzata uguale a quello esercitati dal campo gravitazionale della terra (9,81 m/s ^ 2).

Il sistema di coordinate è definito rispetto allo schermo del telefono nello stesso orientamento predefinito. Gli assi non vengono scambiati quando cambia l'orientamento dello schermo del dispositivo.

L'asse X è orizzontale e punta a destra, l'asse Y verticale e punta verso l'alto e fa riferimento all'asse Z verso l'esterno della parte anteriore dello schermo. In questo sistema, dietro la schermata hanno valori Z negativi.

Esempi:

* Quando il dispositivo si trova flat in una tabella e viene eseguito il push a sinistra verso destra, il valore di accelerazione x è positivo.

* Quando il dispositivo si trova fissi su una tabella, il valore di accelerazione è +1.00 G o (9,81 m/s + ^ 2), che corrispondono all'accelerazione del dispositivo (0, m/s ^ 2) meno la forza di gravità (-9,81 m/s ^ 2) e normalizzata come G.

* Quando il dispositivo si trova flat in una tabella e viene eseguito il push verso il cielo con un'accelerazione di m/s ^ 2, il valore di accelerazione è uguale a + 9.81 corrispondenti per l'accelerazione del dispositivo (m/s + ^ 2) meno la forza di gravità (-9,81 m/s ^ 2) e normalizzata in G.

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Codice sorgente accelerometro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentazione API accelerometro](xref:Xamarin.Essentials.Accelerometer)
