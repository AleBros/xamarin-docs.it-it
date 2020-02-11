---
title: Core ML 2 in Xamarin.iOS
description: Questo documento descrive gli aggiornamenti di core ML disponibili nell'ambito di iOS 12. In particolare, analizza i miglioramenti delle prestazioni associati alla nuova API di stima di batch.
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/15/2018
ms.openlocfilehash: 6245873385caa23e37d5499daa822fa0b699ac1e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032030"
---
# <a name="core-ml-2-in-xamarinios"></a>Core ML 2 in Xamarin.iOS

Core ML è una tecnologia di machine learning disponibile in iOS, macOS, tvOS e watchos. Consente alle app di eseguire stime basate sui modelli di machine learning.

In iOS 12, Core ML include un'API di elaborazione batch. Questa API rende più efficiente Core ML e fornisce miglioramenti delle prestazioni negli scenari in cui viene usato un modello per creare una sequenza di stime.

## <a name="sample-app-marshabitatcoremltimer"></a>App di esempio: MarsHabitatCoreMLTimer

Per illustrare le stime in batch con core ML, vedere l'app di esempio [MarsHabitatCoreMLTimer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer) . Questo esempio usa un modello di core ML con training per stimare il costo della creazione di un habitat su Marte, in base a diversi input: numero di pannelli solari, numero di serre e numero di acri.

I frammenti di codice di questo documento provengono da questo esempio.

## <a name="generate-sample-data"></a>Generare dati di esempio

In `ViewController`, il metodo di `ViewDidLoad` dell'app di esempio chiama `LoadMLModel`, che carica il modello di core ML incluso:

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

Quindi, l'app di esempio crea 100.000 oggetti `MarsHabitatPricerInput` da usare come input per le stime di core ML sequenziali. Ogni campione generato ha un valore casuale impostato per il numero di pannelli solari, il numero di serre e il numero di acri:

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

Se si tocca uno dei tre pulsanti dell'app, vengono eseguite due sequenze di stime: una usando un ciclo `for` e l'altra usando il nuovo metodo batch `GetPredictions` introdotto in iOS 12:

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

La versione del ciclo `for` del test esegue l'iterazione in base al numero specificato di input, chiamando [`GetPrediction`](xref:CoreML.MLModel.GetPrediction*) per ogni e ignorando il risultato. Il metodo impiega il tempo necessario per eseguire le stime:

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

## <a name="getpredictions-new-batch-api"></a>Getpredictions (nuova API batch)

La versione batch del test crea un oggetto `MLArrayBatchProvider` dalla matrice di input (poiché si tratta di un parametro di input obbligatorio per il metodo `GetPredictions`), crea un [`MLPredictionOptions`](xref:CoreML.MLPredictionOptions)
oggetto che impedisce che i calcoli di stima vengano limitati alla CPU e utilizza l'API `GetPredictions` per recuperare le stime, eliminando nuovamente il risultato:

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

Sia per il simulatore sia per il dispositivo, `GetPredictions` termina più rapidamente rispetto alle stime di core ML basate su loop.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio-MarsHabitatCoreMLTimer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer)
- [Novità di core ML, parte 1 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/708/)
- [Novità di core ML, parte 2 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/709/)
- [Introduzione a core ML in Xamarin.iOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/coreml)
- [Core ML (Apple)](https://developer.apple.com/documentation/coreml?language=objc)
- [Uso dei modelli di base ML](https://developer.apple.com/machine-learning/build-run-models/)
