---
title: 'Xamarin.Essentials: Barometro'
description: La classe Barometer in Xamarin.Essentials consente di monitorare il sensore barometro del dispositivo, che misura la pressione atmosferica.
ms.assetid: DA4F968A-D988-41F5-8745-1BEE693660A1
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 5a496fc06732be1cf104cfbaffa8ff4b68c8f564
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "69976430"
---
# <a name="xamarinessentials-barometer"></a>Xamarin.Essentials: Barometro

La classe **Barometer** consente di monitorare il sensore barometro del dispositivo, che misura la pressione atmosferica.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-barometer"></a>Uso del barometro

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Barometer funziona chiamando i metodi `Start` e `Stop` per rilevare le variazioni nelle letture della pressione effettuale dal barometro in ettopascal. Le eventuali modifiche vengono inviate tramite l'evento `ReadingChanged`. Utilizzo di esempio:

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
        Console.WriteLine($"Reading: Pressure: {data.PressureInHectopascals} hectopascals");
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

# <a name="android"></a>[Android](#tab/android)

Nessun dettaglio di implementazione specifico della piattaforma.

# <a name="ios"></a>[iOS](#tab/ios)

Questa API usa [CMAltimeter](https://developer.apple.com/documentation/coremotion/cmaltimeter#//apple_ref/occ/cl/CMAltimeter) per monitorare le variazioni della pressione, ovvero una funzionalità hardware aggiunta in iPhone 6 e nei dispositivi più recenti. Verrà generata una `FeatureNotSupportedException` nei dispositivi che non supportano l'altimetro.

`SensorSpeed` non viene usato perché non è supportato in iOS.

# <a name="uwp"></a>[UWP](#tab/uwp)

Nessun dettaglio di implementazione specifico della piattaforma.

-----

## <a name="api"></a>API

- [Codice sorgente di Barometer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Barometer)
- [Documentazione dell'API Barometer](xref:Xamarin.Essentials.Barometer)
