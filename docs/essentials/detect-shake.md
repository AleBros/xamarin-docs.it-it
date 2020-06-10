---
title: " Xamarin.Essentials : rileva Shake" Descrizione: "la classe accelerometra in Xamarin.Essentials consente di rilevare un movimento di agitazione del dispositivo".
ms. AssetID: 07513D32-120F-4F12-8757-A47802A8027B Author: jamesmontemagno ms. Author: Jamont ms. Date: 05/28/2019 ms. Custom: video No-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-detect-shake"></a>Xamarin.Essentials: Rileva Shake

La classe **[accelerometro](accelerometer.md)** consente di monitorare il sensore accelerometro del dispositivo, che indica l'accelerazione del dispositivo nello spazio tridimensionale. Inoltre, consente di registrare gli eventi quando l'utente scuote il dispositivo.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-detect-shake"></a>Uso di Rileva scuotimento

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per rilevare uno scuotimento del dispositivo è necessario usare la funzionalità Accelerometer chiamando i metodi `Start` e `Stop` per ascoltare i cambi di accelerazione e rilevare lo scuotimento. Ogni volta che viene rilevato uno scuotimento, viene generato l'evento `ShakeDetected`. È consigliabile usare `Game` o un valore superiore per `SensorSpeed`. Utilizzo di esempio:

```csharp

public class DetectShakeTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Game;

    public DetectShakeTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ShakeDetected  += Accelerometer_ShakeDetected ;
    }

    void Accelerometer_ShakeDetected (object sender, EventArgs e)
    {
        // Process shake event
    }

    public void ToggleAccelerometer()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
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

## <a name="implementation-details"></a>Dettagli dell'implementazione

L'API di rilevamento scuotimento calcola l'accelerazione usando letture non elaborate dell'accelerometro. Usa un semplice meccanismo di inserimento in coda per rilevare se i 3/4 dei recenti eventi dell'accelerometro si sono verificati nell'ultimo mezzo secondo. L'accelerazione viene calcolata aggiungendo il quadrato delle letture X, Y e Z eseguite dall'accelerometro e confrontando i valori con una soglia specifica.

## <a name="api"></a>API

- [Codice sorgente dell'accelerometro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentazione dell'API Accelerometer](xref:Xamarin.Essentials.Accelerometer)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Detect-Shake-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
