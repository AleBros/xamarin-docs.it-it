---
title: 'Xamarin.Essentials: Rileva scuotimento'
description: La classe Accelerometer di Xamarin.Essentials consente di rilevare uno scuotimento del dispositivo.
ms.assetid: 07513D32-120F-4F12-8757-A47802A8027B
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: d2aef2c32b3fe2815f1176779a8c28ae8d421ed1
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832588"
---
# <a name="xamarinessentials-detect-shake"></a>Xamarin.Essentials: Rileva scuotimento

La classe **[Accelerometer](accelerometer.md)** consente di monitorare il sensore dell'accelerometro del dispositivo, che indica l'accelerazione del dispositivo nello spazio tridimensionale. Inoltre, consente di registrare gli eventi quando l'utente scuote il dispositivo.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-detect-shake"></a>Uso di Rileva scuotimento

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per rilevare uno scuotimento del dispositivo è necessario usare la funzionalità Accelerometer chiamando i metodi `Start` e `Stop` per ascoltare i cambi di accelerazione e rilevare lo scuotimento. Ogni volta che viene rilevato uno scuotimento, viene generato l'evento `ShakeDetected`. È consigliabile usare `Game` o un valore superiore per `SensorSpeed`. Utilizzo di esempio:

```csharp

public class DetectShakeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Game;

    public DetectShakeTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ShakeDetected  += Accelerometer_ShakeDetected ;
    }

    void Accelerometer_ShakeDetected (object sender, EventArgs e)
    {
        // Process shake event
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

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="implementation-details"></a>Dettagli sull'implementazione

L'API di rilevamento scuotimento calcola l'accelerazione usando letture non elaborate dell'accelerometro. Usa un semplice meccanismo di inserimento in coda per rilevare se i 3/4 dei recenti eventi dell'accelerometro si sono verificati nell'ultimo mezzo secondo. L'accelerazione viene calcolata aggiungendo il quadrato delle letture X, Y e Z eseguite dall'accelerometro e confrontando i valori con una soglia specifica.

## <a name="api"></a>API

- [Codice sorgente dell'accelerometro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentazione dell'API Accelerometer](xref:Xamarin.Essentials.Accelerometer)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Detect-Shake-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
