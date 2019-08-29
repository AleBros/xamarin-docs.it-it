---
title: Introduzione a CoreML in Novell. iOS
description: Questo documento descrive CoreML, che consente l'apprendimento automatico in iOS. Questo documento illustra come iniziare a usare CoreML e come usarlo con il Framework di visione.
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: c2747b24c47e826e0d832faa36ddb93d8aa06f37
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121525"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>Introduzione a CoreML in Novell. iOS

CoreML offre l'apprendimento automatico per iOS: le app possono sfruttare i modelli di apprendimento automatico con training per eseguire tutti i tipi di attività, dalla risoluzione dei problemi al riconoscimento delle immagini.

In questa introduzione vengono trattati gli elementi seguenti:

- [Introduzione con CoreML](#coreml)
- [Uso di CoreML con il Framework di visione](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Introduzione con CoreML

Questi passaggi descrivono come aggiungere CoreML a un progetto iOS. Per un esempio pratico, vedere l'esempio di [Mars habitat pricer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/) .

![Schermata di esempio del predittore del prezzo di Mars habitat](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1. Aggiungere il modello CoreML al progetto

Aggiungere un modello CoreML (un file con estensione **mlmodel** ) alla directory Resources del progetto. 

Nelle proprietà del file del modello, l' **azione di compilazione** è impostata su **CoreMLModel**. Ciò significa che verrà compilato in un file con **estensione mlmodelc** durante la compilazione dell'applicazione.

### <a name="2-load-the-model"></a>2. Caricare il modello

Caricare il modello utilizzando il `MLModel.Create` metodo statico:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. Impostare i parametri

I parametri del modello vengono passati all'interno e all'esterno utilizzando una `IMLFeatureProvider`classe contenitore che implementa.

Le classi del provider di funzionalità si comportano `MLFeatureValue`come un dizionario di stringa e s, dove ogni valore della funzionalità può essere una stringa o un numero semplice, una matrice o dati o un buffer di pixel che contiene un'immagine.

Di seguito è riportato il codice per un provider di funzionalità a valore singolo:

```csharp
public class MyInput : NSObject, IMLFeatureProvider
{
  public double MyParam { get; set; }
  public NSSet<NSString> FeatureNames => new NSSet<NSString>(new NSString("myParam"));
  public MLFeatureValue GetFeatureValue(string featureName)
  {
    if (featureName == "myParam")
      return MLFeatureValue.FromDouble(MyParam);
    return MLFeatureValue.FromDouble(0); // default value
  }
```

Utilizzando classi come questa, i parametri di input possono essere forniti in modo comprensibile da CoreML. I nomi delle funzionalità, ad `myParam` esempio nell'esempio di codice, devono corrispondere a quanto previsto dal modello.

### <a name="4-run-the-model"></a>4. Eseguire il modello

Per usare il modello è necessario creare un'istanza del provider di funzionalità e impostare i parametri, quindi `GetPrediction` chiamare il metodo:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. Estrarre i risultati

Il risultato `outFeatures` della stima è anche un'istanza di `IMLFeatureProvider`. è possibile accedere ai valori di `GetFeatureValue` output usando con il `theResult`nome di ogni parametro di output, ad esempio, come nell'esempio seguente:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>Uso di CoreML con il Framework di visione

CoreML può essere usato anche in combinazione con il Framework di visione per eseguire operazioni sull'immagine, ad esempio il riconoscimento delle forme, l'identificazione degli oggetti e altre attività.

I passaggi seguenti descrivono come CoreML e visione vengono usati insieme nell' [esempio CoreMLVision](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision). L'esempio combina il [riconoscimento](~/ios/platform/introduction-to-ios11/vision.md#rectangles) dei rettangoli dal framework di visione con il modello _MNINSTClassifier_ CoreML per identificare una cifra scritta a mano in una foto.

![Riconoscimento immagini di numero 3](coreml-images/vision3.png) ![Riconoscimento immagini di numero 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. Creare un modello CoreML per la visione artificiale

Il modello CoreML _MNISTClassifier_ viene caricato e quindi sottoposto a `VNCoreMLModel` wrapper in un che rende il modello disponibile per le attività visive. Questo codice crea anche due richieste di visione: innanzitutto per trovare rettangoli in un'immagine e quindi per elaborare un rettangolo con il modello CoreML:

```csharp
// Load the ML model
var bundle = NSBundle.MainBundle;
var assetPath = bundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
NSError mlErr, vnErr;
var mlModel = MLModel.Create(assetPath, out mlErr);
var model = VNCoreMLModel.FromMLModel(mlModel, out vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(model, HandleClassification);
```

La classe deve comunque implementare i `HandleRectangles` metodi e `HandleClassification` per le richieste di visione, illustrati nei passaggi 3 e 4 di seguito.

### <a name="2-start-the-vision-processing"></a>2. Avviare l'elaborazione della visione

Il codice seguente avvia l'elaborazione della richiesta. Nell'esempio **CoreMLVision** questo codice viene eseguito dopo che l'utente ha selezionato un'immagine:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Questo gestore passa `ciImage` al Framework `VNDetectRectanglesRequest` di visione creato nel passaggio 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Gestire i risultati dell'elaborazione della visione

Una volta completato il rilevamento del rettangolo, viene eseguito il `HandleRectangles` metodo, che ritaglia l'immagine per estrarre il primo rettangolo, converte l'immagine del rettangolo in scala di grigi e la passa al modello CoreML per la classificazione.

Il `request` parametro passato a questo metodo contiene i dettagli della richiesta di visione e, usando il `GetResults<VNRectangleObservation>()` metodo, restituisce un elenco di rettangoli trovati nell'immagine. Il primo rettangolo `observations[0]` viene estratto e passato al modello CoreML:

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] {ClassificationRequest}, out NSError err);
  });
}
```

L' `ClassificationRequest` oggetto è stato inizializzato nel passaggio 1 `HandleClassification` per utilizzare il metodo definito nel passaggio successivo.

### <a name="4-handle-the-coreml"></a>4. Gestire CoreML

Il `request` parametro passato a questo metodo contiene i dettagli della richiesta CoreML e, usando il `GetResults<VNClassificationObservation>()` metodo, restituisce un elenco di risultati possibili ordinati in base alla confidenza (primo livello di confidenza):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  // ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```

## <a name="samples"></a>Esempi

Esistono tre esempi di CoreML da provare:

- L' [esempio Mars habitat Price](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/) predittor dispone di semplici output e input numerici.

- L' [esempio vision & CoreML](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision) accetta un parametro image e usa il Framework di visione per identificare le aree quadre nell'immagine, che vengono passate a un modello CoreML che riconosce le cifre singole.

- Infine, l' [esempio CoreML Image Recognition](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlimagerecognition) USA CoreML per identificare le funzionalità di una foto. Per impostazione predefinita, usa il modello **SqueezeNet** più piccolo (5 MB), ma è stato scritto in modo che sia possibile scaricare e incorporare il modello **VGG16** più grande (553MB). Per ulteriori informazioni, vedere il [file Leggimi dell'esempio](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md).

## <a name="related-links"></a>Collegamenti correlati

- [Machine Learning (Apple)](https://developer.apple.com/machine-learning/)
- [Esempio di CoreML (Mars habitat) (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-marshabitatcoremltimer/)
- [CoreML e visione (riconoscimento numerico) (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlvision)
- [Riconoscimento immagine CoreML (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlimagerecognition)
- [CoreML con Azure Visione personalizzata (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-coremlazuremodel)
- [Introduzione a CoreML (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/703/)
