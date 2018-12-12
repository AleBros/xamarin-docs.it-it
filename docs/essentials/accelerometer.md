---
title: 'Xamarin.Essentials: Accelerometer'
description: La classe Accelerometer in Xamarin.Essentials consente di monitorare il sensore dell'accelerometro del dispositivo, che indica l'accelerazione del dispositivo nello spazio tridimensionale.
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: f443cc094c7d7be6ec782df27332bafe5d51dda3
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898797"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials: Accelerometer

La classe **Accelerometer** consente di monitorare il sensore dell'accelerometro del dispositivo, che indica l'accelerazione del dispositivo nello spazio tridimensionale.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-accelerometer"></a>Uso della classe Accelerometer

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Accelerometer opera chiamando i metodi `Start` e `Stop` per attivare l'ascolto dei cambi di accelerazione. Le eventuali modifiche vengono inviate tramite l'evento `ReadingChanged`. Utilizzo di esempio:

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

    public void ToggleAccelerometer()
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

Le letture dell'accelerometro vengono restituite in G. G è un'unità di forza gravitazionale uguale a quella esercitata dal campo gravitazionale della terra (9,81 m/s^2).

Il sistema di coordinate viene definito in relazione allo schermo del telefono con l'orientamento predefinito. Gli assi non vengono invertiti quando cambia l'orientamento dello schermo del dispositivo.

L'asse X è orizzontale e punta verso destra, l'asse Y è verticale e punta verso l'alto e l'asse Z punta verso l'esterno rispetto alla parte frontale dello schermo. In questo sistema, le coordinate dietro lo schermo hanno valori Z negativi.

Esempi:

- Quando il dispositivo è piatto su un piano e viene spinto dal lato sinistro verso destra, il valore di accelerazione x è positivo.

- Quando il dispositivo è piatto su un piano, il valore di accelerazione è +1,00 G o (+9,81 m/s^2), che corrisponde all'accelerazione del dispositivo (0 m/s^2) meno la forza di gravità (-9,81 m/s^2) normalizzata in G.

- Quando il dispositivo è piatto su un piano e viene spinto verso il cielo con un'accelerazione di A m/s^2, il valore di accelerazione è uguale a A+9,81 che corrisponde all'accelerazione del dispositivo (+A m/s^2) meno la forza di gravità (-9,81 m/s^2) normalizzata in G.

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Codice sorgente dell'accelerometro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentazione dell'API Accelerometer](xref:Xamarin.Essentials.Accelerometer)
