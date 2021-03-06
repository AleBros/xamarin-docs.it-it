---
title: 'Xamarin.Essentials: Compass'
description: Questo documento descrive la classe Compass in Xamarin.Essentials, che consente di monitorare la direzione del nord magnetico del dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 55dd10bff21b7d082b225277d0100232d5efd4f3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "61356877"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: Compass

La classe **Compass** consente di monitorare la direzione del nord magnetico del dispositivo.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-compass"></a>Uso di Compass

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Compass opera chiamando i metodi `Start` e `Stop` per attivare l'ascolto dei cambi della bussola. Le eventuali modifiche vengono inviate tramite l'evento `ReadingChanged`. Esempio:

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(object sender, CompassChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: {data.HeadingMagneticNorth} degrees");
        // Process Heading Magnetic North
    }

    public void ToggleCompass()
    {
        try
        {
            if (Compass.IsMonitoring)
              Compass.Stop();
            else
              Compass.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Some other exception has occurred
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="android"></a>[Android](#tab/android)

Android non fornisce un'API per recuperare la direzione della bussola. Vengono utilizzati l'accelerometro e il magnetometro per calcolare la direzione del nord magnetico, come consigliato da Google.

In rari casi, si potrebbero osservare risultati non coerenti perché i sensori devono essere calibrati e a questo scopo è necessario muovere il dispositivo disegnando una forma a 8. Il modo migliore per eseguire questa operazione consiste nell'aprire Google Maps, toccare il punto corrispondente alla propria posizione e selezionare **Calibrate compass** (Calibra bussola).

Tenere presente che l'esecuzione simultanea di più sensori dalla propria app può modificare la velocità del sensore.

## <a name="low-pass-filter"></a>Filtro passa basso

A causa di come vengono aggiornati e calcolati i valori della bussola in Android, potrebbe essere necessario uniformare i valori. Si può applicare un _filtro passa basso_ che calcola la media dei valori di seno e coseno degli angoli e può essere attivato usando l'overload del metodo `Start` che accetta il parametro `bool applyLowPassFilter`:

```csharp
Compass.Start(SensorSpeed.UI, applyLowPassFilter: true);
```

Il parametro viene applicato solo alla piattaforma Android e ignorato nei sistemi iOS e UWP.  Per altre informazioni, vedere [qui](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860).

--------------

## <a name="api"></a>API

- [Codice sorgente di Compass](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentazione dell'API Compass](xref:Xamarin.Essentials.Compass)
