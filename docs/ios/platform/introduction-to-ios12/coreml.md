---
title: Core ML 2 in Novell. iOS
description: Questo documento descrive gli aggiornamenti di core ML disponibili nell'ambito di iOS 12. In particolare, analizza i miglioramenti delle prestazioni associati alla nuova API di stima di batch.
ms.prod: xamarin
ms.assetid: 408E752C-2C78-4B20-8B43-A6B89B7E6D1B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/15/2018
ms.openlocfilehash: 4fc72e855101f110310a46145c577b272a647ac3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645712"
---
# <a name="core-ml-2-in-xamarinios"></a>Core ML 2 in Novell. iOS

Core ML è una tecnologia di machine learning disponibile in iOS, macOS, tvOS e watchos. Consente alle app di eseguire stime basate sui modelli di machine learning.

In iOS 12, Core ML include un'API di elaborazione batch. Questa API rende più efficiente Core ML e fornisce miglioramenti delle prestazioni negli scenari in cui viene usato un modello per creare una sequenza di stime.

## <a name="sample-app-marshabitatcoremltimer"></a>App di esempio: MarsHabitatCoreMLTimer

Per illustrare le stime in batch con core ML, vedere l'app di esempio [MarsHabitatCoreMLTimer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer) . Questo esempio usa un modello di core ML con training per stimare il costo della creazione di un habitat su Marte, in base a diversi input: numero di pannelli solari, numero di serre e numero di acri.

I frammenti di codice di questo documento provengono da questo esempio.

## <a name="generate-sample-data"></a>Generare dati di esempio

In `ViewController`il metodo dell'app di `ViewDidLoad` esempio chiama `LoadMLModel`, che carica il modello di core ml incluso:

```csharp
void LoadMLModel()
{
    var assetPath = NSBundle.MainBundle.GetUrlForResource("CoreMLModel/MarsHabitatPricer", "mlmodelc");
    model = MLModel.Create(assetPath, out NSError mlErr);
}
```

Quindi, l'app di esempio crea `MarsHabitatPricerInput` oggetti 100.000 da usare come input per le stime di core ml sequenziali. Ogni campione generato ha un valore casuale impostato per il numero di pannelli solari, il numero di serre e il numero di acri:

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

Toccare uno dei tre pulsanti dell'app esegue due sequenze di stime: una usando un `for` ciclo e l'altra usando il nuovo metodo batch `GetPredictions` introdotto in iOS 12:

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

La `for` versione del ciclo del test esegue l'iterazione in base al numero di input specificato, [`GetPrediction`](xref:CoreML.MLModel.GetPrediction*) chiamando per ogni e ignorando il risultato. Il metodo impiega il tempo necessario per eseguire le stime:

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

La versione batch del test crea un `MLArrayBatchProvider` oggetto dalla matrice di input (poiché si tratta di un parametro di input obbligatorio per il `GetPredictions` metodo), crea un'[`MLPredictionOptions`](xref:CoreML.MLPredictionOptions)
oggetto che impedisce che i calcoli di stima vengano limitati alla CPU e utilizza l' `GetPredictions` API per recuperare le stime, eliminando nuovamente il risultato:

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

Sia per il simulatore che `GetPredictions` per il dispositivo, termina più rapidamente rispetto alle stime di core ml basate su loop.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio-MarsHabitatCoreMLTimer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer)
- [Novità di core ML, parte 1 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/708/)
- [Novità di core ML, parte 2 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/709/)
- [Introduzione a core ML in Novell. iOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/coreml)
- [Core ML (Apple)](https://developer.apple.com/documentation/coreml?language=objc)
- [Uso dei modelli di base ML](https://developer.apple.com/machine-learning/build-run-models/)
