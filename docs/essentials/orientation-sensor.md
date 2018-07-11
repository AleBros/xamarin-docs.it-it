---
title: 'Xamarin.Essentials: OrientationSensor'
description: La classe OrientationSensor consente di monitorare l'orientamento di un dispositivo in uno spazio tridimensionale.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: c01fa28e495eb3eceec62885060dce8f096c4086
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947390"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **OrientationSensor** classe consente di monitorare l'orientamento di un dispositivo in uno spazio dimensionale tre.

> [!NOTE]
> Questa classe è per determinare l'orientamento di un dispositivo nello spazio 3D. Se è necessario determinare se il dispositivo del video è visualizzato in modalità verticale o orizzontale, usare il `Orientation` proprietà del `ScreenMetrics` oggetto disponibile dalle [ `DeviceDisplay` ](device-display.md) classe.

## <a name="using-orientationsensor"></a>Usando OrientationSensor

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Il `OrientationSensor` è abilitata mediante la chiamata di `Start` metodo per monitorare le modifiche apportate orientamento del dispositivo e disabilitato chiamando il `Stop` (metodo). Eventuali modifiche vengono inviate attraverso la `ReadingChanged` evento. Ecco un esempio di utilizzo:

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Orientation.X}, Y: {data.Orientation.Y}, Z: {data.Orientation.Z}, W: {data.Orientation.W}");
        // Process Orientation quaternion (X, Y, Z, and W)
    }

    public void ToggleOrientationSensor()
    {
        try
        {
            if (OrientationSensor.IsMonitoring)
                OrientationSensor.Stop();
            else
                OrientationSensor.Start(speed);
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

`OrientationSensor` le letture vengono nuovamente segnalate sotto forma di una [ `Quaternion` ](xref:System.Numerics.Quaternion) che descrive l'orientamento del dispositivo in base a due sistemi di coordinate 3D:

Il dispositivo (in genere un telefono o tablet) dispone di un sistema di coordinate 3D con gli assi seguenti:

- Il valore positivo X punti degli assi a destra dello schermo in modalità verticale.
- L'asse Y positivo punta all'inizio del dispositivo in modalità verticale.
- Dalla schermata fa riferimento all'asse Z positivo.

Il sistema di coordinate 3D della terra presenta gli assi seguenti:

- Il valore positivo dell'asse X tangente la superficie della terra e punta a est.
- L'asse Y positivo è anche tangente alla superficie della terra e Nord punti.
- L'asse Z positivo è perpendicolare alla superficie della terra e punti di backup.

Il `Quaternion` descrive la rotazione del sistema di coordinate del dispositivo rispetto al sistema di coordinate della terra.

Oggetto `Quaternion` valore è strettamente correlato alla rotazione intorno a un asse. Se un asse di rotazione è il vettore normalizzato (un<sub>x</sub>, un<sub>y</sub>, una<sub>z</sub>), e l'angolo di rotazione è Θ, quindi la (X, Y, Z, W) i componenti del quaternione sono:

(un<sub>x</sub>·sin(Θ/2), un<sub>y</sub>·sin(Θ/2), una<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Questi sono i sistemi di coordinate di destra, in modo con il controllo thumb della mano destra punta nella direzione dell'asse di rotazione positiva, la curva delle dita indica la direzione di rotazione per gli angoli positivi.

Esempi:

* Quando il dispositivo si trova fissi su una tabella con la schermata rivolto verso l'alto, con la parte superiore del dispositivo (in modalità verticale) che puntano a nord, sono allineati i due sistemi di coordinate. Il `Quaternion` valore rappresenta il quaternione identità (0, 0, 0, 1). Tutte le rotazioni possono essere analizzate rispetto a questa posizione.

* Quando il dispositivo si trova fissi su una tabella con la schermata rivolto verso l'alto e la parte superiore del dispositivo (in modalità verticale) che punta a ovest, il `Quaternion` valore è (0, 0, 0,707, 0,707). Il dispositivo è stato ruotato di 90 gradi intorno all'asse Z della terra.

* Quando il dispositivo viene gestito in verticale in modo che la parte superiore (in modalità verticale) punta verso il cielo, e la parte posteriore del dispositivo sia rivolto verso nord, il dispositivo è stato ruotato di 90 gradi intorno all'asse X. Il `Quaternion` valore è (0,707, 0, 0, 0,707).

* Se il dispositivo è posizionato in modo che il bordo sinistro è in una tabella e la parte superiore Nord, il dispositivo è stato ruotato. &ndash;90 gradi intorno all'asse Y (o 90 gradi intorno all'asse Y negativo). Il `Quaternion` valore è (0,-0.707, 0, 0,707).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Codice sorgente OrientationSensor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Documentazione dell'API OrientationSensor](xref:Xamarin.Essentials.OrientationSensor)
