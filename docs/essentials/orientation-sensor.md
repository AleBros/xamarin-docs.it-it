---
title: 'Xamarin.Essentials: OrientationSensor'
description: La classe OrientationSensor consente di monitorare l'orientamento di un dispositivo in uno spazio tridimensionale.
ms.assetid: F3091D93-E779-41BA-8696-23D296F2F6F5
author: charlespetzold
ms.author: chape
ms.date: 05/21/2018
ms.openlocfilehash: c7bbc849e5fa0b901f5b54e77d548b28bc2a72c6
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080504"
---
# <a name="xamarinessentials-orientationsensor"></a>Xamarin.Essentials: OrientationSensor

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **OrientationSensor** classe consente di monitorare l'orientamento di un dispositivo in tre spazio dimensionale.

> [!NOTE]
> Questa classe è per determinare l'orientamento di un dispositivo nello spazio 3D. Se è necessario determinare se il dispositivo del video visualizzato è in modalità verticale o orizzontale, utilizzare il `Orientation` proprietà del `ScreenMetrics` oggetto disponibile dal [ `DeviceDisplay` ](device-display.md) classe.

## <a name="using-orientationsensor"></a>Utilizzo OrientationSensor

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Il `OrientationSensor` attivato chiamando la `Start` metodo per monitorare le modifiche per l'orientamento del dispositivo e disabilitato chiamando il `Stop` (metodo). Tutte le modifiche vengono inviate attraverso la `ReadingChanged` evento. Ecco un esempio di utilizzo:

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

`OrientationSensor` letture sono riferite sotto forma di un [ `Quaternion` ](xref:System.Numerics.Quaternion) che descrive l'orientamento del dispositivo in base alle due sistemi di coordinate 3D:

Il dispositivo (in genere un telefono o tablet) dispone di un sistema di coordinate 3D con gli assi seguenti:

- Il segno positivo X punti asse a destra dello schermo in modalità verticale.
- L'asse Y positivo punta all'inizio del dispositivo in modalità verticale.
- L'asse Z positivo punta fuori schermo.

Il sistema di coordinate 3D della terra presenta gli assi seguenti:

- Il segno positivo asse X è tangente alla superficie della terra e punta est.
- L'asse Y è inoltre tangente alla superficie della terra e punti Nord.
- L'asse Z positivo è perpendicolare alla superficie della terra e punti di backup.

Il `Quaternion` descrive la rotazione del dispositivo sistema di coordinate relativo al sistema di coordinate della terra.

Oggetto `Quaternion` valore è strettamente correlato alla rotazione attorno a un asse. Se un asse di rotazione vettore normalizzato (un<sub>x</sub>, un<sub>y</sub>, un<sub>z</sub>), senza che sia l'angolo di rotazione Θ, quindi la (X, Y, Z, W) sono componenti del quaternione:

(un<sub>x</sub>·sin(Θ/2), un<sub>y</sub>·sin(Θ/2), una<sub>z</sub>·sin(Θ/2), cos(Θ/2))

Si tratta di sistemi di coordinate di destro, in modo con il cursore dell'icona della mano destra puntato nella direzione dell'asse di rotazione positiva, la curva delle dita indicano la direzione di rotazione per gli angoli positivo.

Esempi:

* Quando il dispositivo si trova flat in una tabella con la schermata rivolto verso l'alto, con la parte superiore del dispositivo (in modalità verticale) che puntano a nord, sono allineati i due sistemi di coordinate. Il `Quaternion` valore rappresenta il quaternione identità (0, 0, 0, 1). È possibile analizzare tutte le rotazioni relazione questa posizione.

* Quando il dispositivo si trova flat in una tabella con la schermata rivolto verso l'alto e la parte superiore del dispositivo (in modalità verticale) che punta a ovest, il `Quaternion` valore è (0, 0, 0,707, 0,707). Il dispositivo è stato ruotato di 90 gradi intorno all'asse Z della terra.

* Quando il dispositivo viene mantenuto in verticale in modo che la parte superiore (in modalità verticale) punta verso il sky e la parte posteriore del dispositivo è rivolto Nord, il dispositivo è stato ruotato di 90 gradi intorno all'asse X. Il `Quaternion` valore è (0,707, 0, 0, 0,707).

* Se il dispositivo non è posizionato in modo che il bordo sinistro è in una tabella e la parte superiore Nord, il dispositivo è stato ruotato &ndash;90 gradi intorno all'asse Y (o 90 gradi intorno all'asse Y negativo). Il `Quaternion` valore è (0,-0.707, 0, 0,707).

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensore velocità](xref:Xamarin.Essentials.SensorSpeed)

- **Più veloce** -ottenere i dati del sensore più velocemente possibile (necessariamente restituire sul thread dell'interfaccia utente).
- **Gioco** – frequenza adatto per i giochi (necessariamente restituire sul thread dell'interfaccia utente).
- **Normale** – frequenza predefinita adatta per le modifiche di orientamento dello schermo.
- **Interfaccia utente** – frequenza adatto per l'interfaccia utente generali.

Se il gestore dell'evento non è garantito che eseguite sul thread dell'interfaccia utente e se il gestore dell'evento è necessario accedere agli elementi dell'interfaccia utente, usare il [ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) metodo per eseguire il codice nel thread UI.

## <a name="api"></a>API

- [Codice sorgente OrientationSensor](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/OrientationSensor)
- [Documentazione dell'API OrientationSensor](xref:Xamarin.Essentials.OrientationSensor)
