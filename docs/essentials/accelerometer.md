---
title: 'Xamarin.Essentials: accelerometro'
description: La classe accelerometro in Xamarin.Essentials consente di monitorare sensore accelerometro del dispositivo, che indica l'accelerazione del dispositivo in tre spazio dimensionale.
ms.assetid: 97883573-F0D9-4854-AC7C-A654814401C5
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 8229a372659e7918457a9d2f358b871e1a3f5978
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080491"
---
# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials: accelerometro

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **accelerometro** classe consente di monitorare sensore accelerometro del dispositivo che indica l'accelerazione del dispositivo in tre spazio dimensionale.

## <a name="using-accelerometer"></a>Utilizzando accelerometro

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità accelerometro funziona chiamando il `Start` e `Stop` metodi per attendere le modifiche per l'accelerazione. Tutte le modifiche vengono inviate attraverso la `ReadingChanged` evento. Ecco l'esempio di utilizzo:

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.Ui;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Acceleration.X}, Y: {data.Acceleration.Y}, Z: {data.Acceleration.Z}");
        // Process Acceleration X, Y, and Z
    }

    public void ToggleAcceleromter()
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

Letture accelerometro sono riferite in G. G è un'unità di gravitation forzata uguale a quello dei campo gravitazionale della terra (m/s 9.81 ^ 2).

Il sistema di coordinate è definito rispetto allo schermo del telefono in orientamento predefinito. Gli assi sono invertiti non modifica dell'orientamento dello schermo del dispositivo.

L'asse X è orizzontale e punti a destra, l'asse Y verticale e punta verso l'alto e all'asse Z punta verso l'esterno della parte anteriore della schermata. In questo sistema, coordinate dietro la schermata dispongono di valori Z negativo.

Esempi:

* Quando il dispositivo si trova flat in una tabella e viene inserito il lato sinistro verso destra, il valore di accelerazione x è positivo.

* Quando il dispositivo si trova flat in una tabella, il valore di accelerazione è +1.00 G o (+ m/s 9.81 ^ 2), che corrispondono ad accelerazione del dispositivo (m/s 0 ^ 2) meno la forza di gravità (-m/s 9.81 ^ 2) e normalizzati come G.

* Quando il dispositivo si trova flat in una tabella e spostato verso il sky con un'accelerazione di m/s ^ 2, il valore di accelerazione è uguale a + 9.81 che corrispondono all'accelerazione del dispositivo (+ m/s ^ 2) meno la forza di gravità (-m/s 9.81 ^ 2) e normalizzati in G. 

## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Sensore velocità](xref:Xamarin.Essentials.SensorSpeed)

- **Più veloce** -ottenere i dati del sensore più velocemente possibile (necessariamente restituire sul thread dell'interfaccia utente).
- **Gioco** – frequenza adatto per i giochi (necessariamente restituire sul thread dell'interfaccia utente).
- **Normale** – frequenza predefinita adatta per le modifiche di orientamento dello schermo.
- **Interfaccia utente** – frequenza adatto per l'interfaccia utente generali.

Se il gestore dell'evento non è garantito che eseguite sul thread dell'interfaccia utente e se il gestore dell'evento è necessario accedere agli elementi dell'interfaccia utente, usare il [ `MainThread.BeginInvokeOnMainThread` ](main-thread.md) metodo per eseguire il codice nel thread UI.

## <a name="api"></a>API

- [Codice sorgente accelerometro](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Documentazione accelerometro API](xref:Xamarin.Essentials.Accelerometer)
