---
title: Core ML 2 in Xamarin.iOS
description: Questo documento descrive gli aggiornamenti da Core ML disponibili come parte di iOS è 12. In particolare, analizza i miglioramenti delle prestazioni associati con la nuova API di stima batch.
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/15/2018
ms.openlocfilehash: 50d59f0b6ff2133c5870d84a1d740547768116e0
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58869728"
---
# <a name="core-ml-2-in-xamarinios"></a>Core ML 2 in Xamarin.iOS

ML core è una tecnologia disponibile in iOS, macOS, tvOS e watchOS di apprendimento. Consente alle app di eseguire stime basate sui modelli di machine learning.

In iOS 12, ML Core include un'API di elaborazione batch. Questa API rende più efficiente Core ML e offre miglioramenti delle prestazioni negli scenari in cui viene usato un modello per creare una sequenza di stime.

## <a name="sample-app-marshabitatcoremltimer"></a>App di esempio: MarsHabitatCoreMLTimer

Per illustrare le stime in batch con Core ML, esaminare i [MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer) app di esempio. Questo esempio viene utilizzata per stimare il costo della creazione di un habitat in Mars, eseguito il training di un modello ML Core basata su vari input: numero di pannelli solari, numero di serre e numero di acri.

I frammenti di codice in questo documento provengono da questo esempio.

## <a name="generate-sample-data"></a>Generare dati di esempio

Nelle `ViewController`, l'app di esempio `ViewDidLoad` chiamate al metodo `LoadMLModel`, che carica il modello di Machine Learning Core incluso:

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

Quindi, l'app di esempio crea 100.000 `MarsHabitatPricerInput` oggetti da utilizzare come input per le stime di Machine Learning Core sequenziale. Ogni esempio generato ha un valore casuale impostato per il numero di pannelli solari, il numero di serre e il numero di acri:

```csharp
async void CreateInputs(int num)
{
    // ...
    Random r = new Random();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            double solarPanels = r.NextDouble() * MaxSolarPanels;
            double greenHouses = r.NextDouble() * MaxGreenHouses;
            double acres = r.NextDouble() * MaxAcres;
            inputs[i] = new MarsHabitatPricerInput(solarPanels, greenHouses, acres);
        }
    });
    // ...
}
```

Toccando uno qualsiasi dei tre pulsanti dell'app viene eseguita due sequenze di stime: uno che utilizza un `for` ciclo for e un altro servizio mediante il nuovo batch `GetPredictions` metodo introdotto in iOS 12:

```csharp
async void RunTest(int num)
{
    // ...
    await FetchNonBatchResults(num);
    // ...
    await FetchBatchResults(num);
    // ...
}
```

## <a name="for-loop"></a>for (ciclo)

Il `for` versione loop del test gestire scorre il numero specificato di input, chiamare [ `GetPrediction` ](xref:CoreML.MLModel.GetPrediction*) per ognuno ed eliminando il risultato. Il metodo si verifica il tempo necessario per eseguire le stime:

```csharp
async Task FetchNonBatchResults(int num)
{
    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        for (int i = 0; i < num; i++)
        {
            IMLFeatureProvider output = model.GetPrediction(inputs[i], out NSError error);
        }
    });
    stopWatch.Stop();
    nonBatchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="getpredictions-new-batch-api"></a>GetPredictions (batch nuova API)

La versione di batch di test crea un `MLArrayBatchProvider` oggetto della matrice di input (poiché si tratta di un parametro di input obbligatorio per il `GetPredictions` (metodo)), crea un [`MLPredictionOptions`](xref:CoreML.MLPredictionOptions)
oggetto che impedisce che i calcoli di stima vengano limitate alla CPU e Usa il `GetPredictions` dell'API per recuperare le stime, anche in questo caso ignorando il risultato:

```csharp
async Task FetchBatchResults(int num)
{
    var batch = new MLArrayBatchProvider(inputs.Take(num).ToArray());
    var options = new MLPredictionOptions()
    {
        UsesCpuOnly = false
    };

    Stopwatch stopWatch = Stopwatch.StartNew();
    await Task.Run(() =>
    {
        model.GetPredictions(batch, options, out NSError error);
    });
    stopWatch.Stop();
    batchMilliseconds = stopWatch.ElapsedMilliseconds;
}
```

## <a name="results"></a>Risultati

Simulatore sia sul dispositivo, `GetPredictions` termine più rapidamente le stime di Machine Learning di Core in base al ciclo.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio: MarsHabitatCoreMLTimer](https://developer.xamarin.com/samples/monotouch/iOS12/MarsHabitatCoreMLTimer)
- [Nuove funzionalità di ML Core, parte 1 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/708/)
- [Nuove funzionalità di ML Core, parte 2 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/709/)
- [Introduzione a Core ML in xamarin. IOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/coreml)
- [Core ML (Apple)](https://developer.apple.com/documentation/coreml?language=objc)
- [Utilizzo dei modelli di ML Core](https://developer.apple.com/machine-learning/build-run-models/)
