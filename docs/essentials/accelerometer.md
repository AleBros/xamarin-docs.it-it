---
title: 'Xamarin.Essentials: Accelerometro'
description: La classe accelerometro in Xamarin.Essentials consente di monitorare il sensore accelerometro del dispositivo, che indica l'accelerazione del dispositivo nello spazio tridimensionale.
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f3d7b313039e66294a0095fd34a2caa6689cef2e
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802528"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials: Accelerometro

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

- [Codice sorgente dell'accelerometro](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Accelerometer)
- [Documentazione dell'API Accelerometer](xref:Xamarin.Essentials.Accelerometer)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Accelerometer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
