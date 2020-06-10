---
title: " Xamarin.Essentials : giroscopio" Description: "la classe giroscopio in Xamarin.Essentials consente di monitorare il sensore giroscopio del dispositivo, che misura la rotazione attorno ai tre assi primari del dispositivo".
ms. AssetID: DA4F968A-D988-41F5-8745-1BEE693660A1 Author: jamesmontemagno ms. Author: Jamont ms. Date: 11/04/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-gyroscope"></a>Xamarin.Essentials: Giroscopio

La classe **Gyroscope** consente di monitorare il sensore del giroscopio del dispositivo, ovvero la rotazione intorno ai tre assi primari del dispositivo.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-gyroscope"></a>Uso di Gyroscope

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Gyroscope opera chiamando i metodi `Start` e `Stop` per attivare l'ascolto dei cambi del giroscopio. Le eventuali modifiche vengono inviate tramite l'evento `ReadingChanged` in rad/s. Utilizzo di esempio:

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
        // Process Angular Velocity X, Y, and Z reported in rad/s
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

- [Codice sorgente di Gyroscope](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Gyroscope)
- [Documentazione dell'API Gyroscope](xref:Xamarin.Essentials.Gyroscope)
