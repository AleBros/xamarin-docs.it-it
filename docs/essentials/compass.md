---
title: 'Xamarin.Essentials: Compass'
description: Questo documento descrive la classe bussola nella Xamarin.Essentials, che consente di monitorare intestazione Nord magnetico del dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c3fe98c384a87bdc08ce94e7537d1a6343767561
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353883"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: Compass

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Compass** classe consente di monitorare intestazione Nord magnetico del dispositivo.

## <a name="using-compass"></a>Usando Compass

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Compass funziona chiamando il `Start` e `Stop` metodi per l'ascolto delle modifiche alla bussola. Eventuali modifiche vengono inviate attraverso la `ReadingChanged` evento. Ecco un esempio:

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

## <a name="platform-implementation-specifics"></a>Funzionalità specifiche di implementazione della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android non fornisce un'API per recuperare l'intestazione della bussola. Viene usato l'accelerometro e magnetometro per calcolare il titolo Nord magnetico, che è consigliato da Google.

In rari casi, forse rilevino risultati incoerenti perché i sensori devono essere tarato, che comporta lo spostamento del dispositivo in un movimento figura 8. Il modo migliore di procedere si deve aprire Google Maps, toccare il punto per la posizione e selezionare **Calibra compass**.

Tenere presente che esegue più sensori dalla propria app allo stesso tempo possono regolare la velocità del sensore.

## <a name="low-pass-filter"></a>Filtro passa basso

A causa di un procedura alla bussola Android valori vengano aggiornati e calcolati in potrebbe essere necessario per rendere uniformi i valori. A _filtro passa basso_ possono essere applicati che calcola la media di valori seno e il coseno degli angoli e può essere attivata impostando il `ApplyLowPassFilter` proprietà il `Compass` classe:

```csharp
Compass.ApplyLowPassFilter = true;
```

Questo viene applicato solo nella piattaforma Android. È possibile leggere altre informazioni [qui](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860).

--------------

## <a name="api"></a>API

- [Codice sorgente della bussola](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Documentazione dell'API della bussola](xref:Xamarin.Essentials.Compass)
