---
title: Compasso Xamarin.Essentials
description: La classe compasso consente di monitorare intestazione magnetico settentrionale del dispositivo.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 2f720b4d4861c4969ad9726c7eef876c53217c2b
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-compass"></a>Compasso Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **compasso** classe consente di monitorare intestazione magnetico settentrionale del dispositivo.

## <a name="using-compass"></a>Utilizzando compasso

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità compasso funziona chiamando il `Start` e `Stop` metodi per attendere le modifiche a bussola. Tutte le modifiche vengono inviate attraverso la `ReadingChanged` evento. Ecco un esempio:

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(CompassChangedEventArgs e)
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
            // Some other exception has occured
        }
    }
}
```

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensore velocità](xref:Xamarin.Essentials.SensorSpeed)

- **Più veloce** -ottenere i dati del sensore più velocemente possibile (necessariamente restituire sul thread dell'interfaccia utente).
- **Gioco** – frequenza adatto per i giochi (necessariamente restituire sul thread dell'interfaccia utente).
- **Normale** – frequenza predefinita adatta per le modifiche di orientamento dello schermo.
- **Interfaccia utente** – frequenza adatto per l'interfaccia utente generali.

## <a name="platform-implementation-specifics"></a>Informazioni specifiche sull'implementazione della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android non fornisce un'API per recuperare l'intestazione bussola. Si utilizza l'accelerometro e magnetometro per calcolare il titolo magnetico settentrionale, che è consigliabile da Google. 

In rari casi, è forse visualizzare risultati incoerenti poiché i sensori di dovranno essere calibrati, che implica lo spostamento del dispositivo in un movimento figura 8. Il modo migliore di procedere si tratta per aprire le mappe di Google, toccare il punto per la località e selezionare **calibrare compasso**.

Tenere presente che l'esecuzione più sensori da app nello stesso momento può regolare la velocità del sensore.

--------------

## <a name="api"></a>API

- [Codice sorgente bussola](https://github.com/xamarin/Essentials/tree/master/Essentials/Compass)
- [Documentazione dell'API bussola](xref:Xamarin.Essentials.Compass)
