---
title: 'Xamarin.Essentials: Barometro'
description: La classe Barometer in Xamarin.Essentials consente di monitorare il sensore barometro del dispositivo, che misura la pressione atmosferica.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 08/16/2018
ms.openlocfilehash: 0217b89bc3f45347acecbdb3b8cdccfeed751744
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130298"
---
# <a name="xamarinessentials-barometer"></a>Xamarin.Essentials: Barometro

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **Barometer** consente di monitorare il sensore barometro del dispositivo, che misura la pressione atmosferica.

## <a name="using-barometer"></a>Uso del barometro

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Barometer funziona chiamando i metodi `Start` e `Stop` per rilevare le variazioni nelle letture della pressione effettuale dal barometro in kilopascal. Le eventuali modifiche vengono inviate tramite l'evento `ReadingChanged`. Utilizzo di esempio:

```csharp

public class BarometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public BarometerTest()
    {
        // Register for reading changes.
        Barometer.ReadingChanged += Barometer_ReadingChanged;
    }

    void Barometer_ReadingChanged(object sender, BarometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process Pressure
        Console.WriteLine($"Reading: Pressure: {data.Pressure} kilopascals");
    }

    public void ToggleBarometer()
    {
        try
        {
            if (Barometer.IsMonitoring)
              Barometer.Stop();
            else
              Barometer.Start(speed);
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

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Nessun dettaglio di implementazione specifico della piattaforma.

# <a name="iostabios"></a>[iOS](#tab/ios)

Questa API usa [CMAltimeter](https://developer.apple.com/documentation/coremotion/cmaltimeter#//apple_ref/occ/cl/CMAltimeter) per monitorare le variazioni della pressione, ovvero una funzionalità hardware aggiunta in iPhone 6 e nei dispositivi più recenti. Verrà generata una `FeatureNotSupportedException` nei dispositivi che non supportano l'altimetro.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nessun dettaglio di implementazione specifico della piattaforma.

-----

## <a name="api"></a>API

- [Codice sorgente di Barometer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Barometer)
- [Documentazione dell'API Barometer](xref:Xamarin.Essentials.Barometer)
