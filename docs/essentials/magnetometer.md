---
title: " Xamarin.Essentials : magnetometro" Description: "la classe magnetometro in Xamarin.Essentials consente di monitorare il sensore magnetometro del dispositivo, che indica l'orientamento del dispositivo rispetto al campo magnetico della terra."
ms. AssetID: 64DD0D41-03E2-40DD-9EC8-101CA0ED852B Author: jamesmontemagno ms. Author: Jamont ms. Date: 11/04/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-magnetometer"></a>Xamarin.Essentials: Magnetometro

La classe **Magnetometer** consente di monitorare il magnetometro del dispositivo, che indica l'orientamento del dispositivo rispetto al campo magnetico della Terra.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-magnetometer"></a>Uso di Magnetometer

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Magnetometer opera chiamando i metodi `Start` e `Stop` per attivare l'ascolto dei cambi del magnetometro. Le eventuali modifiche vengono inviate tramite l'evento `ReadingChanged`. Utilizzo di esempio:

```csharp

public class MagnetometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public MagnetometerTest()
    {
        // Register for reading changes.
        Magnetometer.ReadingChanged += Magnetometer_ReadingChanged;
    }

    void Magnetometer_ReadingChanged(object sender, MagnetometerChangedEventArgs e)
    {
        var data = e.Reading;
        // Process MagneticField X, Y, and Z
        Console.WriteLine($"Reading: X: {data.MagneticField.X}, Y: {data.MagneticField.Y}, Z: {data.MagneticField.Z}");
    }

    public void ToggleMagnetometer()
    {
        try
        {
            if (Magnetometer.IsMonitoring)
              Magnetometer.Stop();
            else
              Magnetometer.Start(speed);
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

Tutti i dati vengono restituiti in μT (microtesla).

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Codice sorgente di Magnetometer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Magnetometer)
- [Documentazione dell'API Magnetometer](xref:Xamarin.Essentials.Magnetometer)
