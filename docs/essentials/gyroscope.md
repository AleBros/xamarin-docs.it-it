---
title: 'Xamarin.Essentials: il Giroscopio'
description: La classe Giroscopio in Xamarin.Essentials consente di monitorare il sensore il Giroscopio del dispositivo, che misura rotazione intorno a assi primari tre del dispositivo.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: f1e1199ae32158889ec569eb5f7e9742f37d45d4
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353626"
---
# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: il Giroscopio

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Giroscopio** classe consente di monitorare sensore il Giroscopio del dispositivo che è la rotazione intorno a assi primari tre del dispositivo.

## <a name="using-gyroscope"></a>Usando il Giroscopio

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità il Giroscopio funziona chiamando il `Start` e `Stop` metodi per l'ascolto delle modifiche per il Giroscopio. Eventuali modifiche vengono inviate attraverso la `ReadingChanged` evento. Ecco l'esempio di utilizzo:

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

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Codice sorgente il Giroscopio](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Documentazione di Giroscopio API](xref:Xamarin.Essentials.Gyroscope)
