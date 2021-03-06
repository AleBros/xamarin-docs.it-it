---
title: 'Xamarin.Essentials: Sensore di orientamento'
description: La classe OrientationSensor consente di monitorare l'orientamento di un dispositivo in uno spazio tridimensionale.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 5423872da7966bc7e4bb88e278d76b709f114158
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70120708"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: Sensore di orientamento

La classe **OrientationSensor** consente di monitorare l'orientamento di un dispositivo in uno spazio tridimensionale.

> [!NOTE]
> Questa classe è progettata per determinare l'orientamento di un dispositivo nello spazio 3D. Se è necessario determinare se lo schermo video del dispositivo è `Orientation` in `ScreenMetrics` modalità verticale o orizzontale, utilizzare la proprietà dell'oggetto disponibile dalla [`DeviceDisplay`](device-display.md) classe.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-orientationsensor"></a>Uso di OrientationSensor

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

`OrientationSensor` viene abilitato tramite la chiamata del metodo `Start` per monitorare le variazioni dell'orientamento del dispositivo e disabilitato chiamando il metodo `Stop`. Le eventuali modifiche vengono inviate tramite l'evento `ReadingChanged`. Ecco un esempio di utilizzo:

```csharp

public class OrientationSensorTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public OrientationSensorTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        OrientationSensor.ReadingChanged += OrientationSensor_ReadingChanged;
    }

    void OrientationSensor_ReadingChanged(object sender, OrientationSensorChangedEventArgs e)
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

`OrientationSensor`le letture sono riportate [`Quaternion`](xref:System.Numerics.Quaternion) sotto forma di un che descrive l'orientamento del dispositivo in base a due sistemi di coordinate 3D:

Il dispositivo (in genere un telefono o tablet) usa un sistema di coordinate 3D con gli assi seguenti:

- L'asse X positivo punta a destra dello schermo in modalità verticale.
- L'asse Y positivo punta alla parte superiore del dispositivo in modalità verticale.
- L'asse Z punta all'esterno dello schermo.

Il sistema di coordinate 3D della terra usa gli assi seguenti:

- L'asse X positivo è tangenziale rispetto alla superficie della terra e punta a est.
- Anche l'asse Y positivo è tangenziale rispetto alla superficie della terra e punta a nord.
- L'asse Z positivo è perpendicolare alla superficie della terra e punta verso l'alto.

`Quaternion` descrive la rotazione del sistema di coordinate del dispositivo rispetto al sistema di coordinate della terra.

Un valore `Quaternion` è strettamente correlato alla rotazione attorno a un asse. Se un asse di rotazione è il vettore normalizzato (a<sub>x</sub>, a<sub>y</sub>, a<sub>z</sub>), e l'angolo di rotazione è Θ, i componenti (X, Y, Z, W) del quaternione sono:

(a<sub>x</sub>·sin(Θ/2), a<sub>y</sub>·sin(Θ/2), a<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Questi sono i sistemi di coordinate di destra, quindi con il pollice della mano destra puntato nella direzione positiva dell'asse di rotazione, la curva delle dita indica la direzione di rotazione per gli angoli positivi.

Esempi:

- Quando il dispositivo è appoggiato su un piano con lo schermo rivolto verso l'alto, con la parte superiore del dispositivo (in modalità verticale) orientata a nord, i due sistemi di coordinate sono allineati. Il valore `Quaternion` rappresenta il quaternione identità (0, 0, 0, 1). Tutte le rotazioni possono essere analizzate rispetto a questa posizione.

- Quando il dispositivo è appoggiato su un piano con lo schermo rivolto verso l'alto e la parte superiore del dispositivo (in modalità verticale) è orientata a ovest, il valore di `Quaternion` è (0, 0, 0.707, 0.707). Il dispositivo è stato ruotato di 90 gradi attorno all'asse Z della terra.

- Quando il dispositivo viene mantenuto in verticale in modo che la parte superiore (in modalità verticale) punti verso il cielo e la parte posteriore del dispositivo sia rivolta verso nord, il dispositivo è stato ruotato di 90 gradi attorno all'asse X. Il valore di `Quaternion` è (0.707, 0, 0, 0.707).

- Se il dispositivo è posizionato in modo che il bordo sinistro sia appoggiato su un piano e la parte superiore sia rivolta a nord, il dispositivo è stato ruotato di &ndash;90 gradi attorno all'asse Y (o 90 gradi attorno all'asse Y negativo). Il valore di `Quaternion` è (0, -0.707, 0, 0.707).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Codice sorgente di OrientationSensor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Documentazione dell'API OrientationSensor](xref:Xamarin.Essentials.OrientationSensor)
