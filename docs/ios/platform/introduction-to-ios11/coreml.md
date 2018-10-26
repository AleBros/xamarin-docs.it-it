---
title: Introduzione a CoreML in xamarin. IOS
description: Questo documento descrive CoreML, che consente di machine learning in iOS. Questo documento descrive come iniziare a usare CoreML e come usarlo con il framework di visione artificiale.
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: 3a00a7256cace9cbcff3478d866646d48cfdc50b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120073"
---
# <a name="introduction-to-coreml-in-xamarinios"></a>Introduzione a CoreML in xamarin. IOS

CoreML offre machine learning per iOS: le app possono sfruttare i vantaggi dei modelli di apprendimento automatico sottoposto a training per eseguire tutti i tipi di attività, dalla risoluzione dei problemi al riconoscimento di immagini.

In questa introduzione illustra le attività seguenti:

- [Guida introduttiva a CoreML](#coreml)
- [Uso di CoreML con il framework di visione artificiale](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Guida introduttiva a CoreML

Questi passaggi descrivono come aggiungere CoreML a un progetto iOS. Vedere le [esempio Mars Habitat Pricer](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) per un esempio pratico.

![Screenshot di esempio Predittore dei prezzi Habitat MARS](coreml-images/marspricer-heading.png)

### <a name="1-add-the-coreml-model-to-the-project"></a>1. Aggiungere il modello CoreML al progetto

Aggiungere un modello CoreML (un file con il **.mlmodel** estensione) per il **risorse** directory del progetto. 

Nelle proprietà del file del modello relativa **azione di compilazione** è impostata su **CoreMLModel**. Ciò significa che verrà compilato in un **.mlmodelc** file quando viene compilata l'applicazione.

### <a name="2-load-the-model"></a>2. Caricare il modello

Caricare il modello usando il `MLModel.Create` metodo statico:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.Create(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. Impostare i parametri

Parametri del modello vengono passati in ingresso e in uscita usando una classe contenitore che implementa `IMLFeatureProvider`.

Classi di provider di funzionalità si comportano come un dizionario di stringhe e `MLFeatureValue`s, dove ogni valore di funzionalità potrebbe essere una semplice stringa o numero, una matrice o dati o un buffer di pixel che contiene un'immagine.

Seguito è riportato il codice per un provider di funzionalità a valore singolo:

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

Usare le classi simile al seguente, è possono specificare i parametri di input in modo comprensibile da CoreML. I nomi delle funzionalità (ad esempio `myParam` nell'esempio di codice) deve corrispondere a ciò che prevede che il modello.

### <a name="4-run-the-model"></a>4. Eseguire il modello

Usando il modello richiede che il provider di funzionalità da cui creare istanze e i parametri impostati, quindi che il `GetPrediction` chiamare il metodo:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. Estrarre i risultati

Il risultato di stima `outFeatures` è anche un'istanza di `IMLFeatureProvider`; output i valori sono accessibili tramite `GetFeatureValue` con il nome di ogni parametro di output (ad esempio `theResult`), come in questo esempio:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>Uso di CoreML con il Framework di visione artificiale

CoreML anche utilizzabile in combinazione con il framework di visione artificiale per eseguire operazioni sull'immagine, ad esempio il riconoscimento delle forme, identificazione di oggetti e altre attività.

I passaggi seguenti descrivono come Vision e CoreML vengono usati insieme nel [CoreMLVision esempio](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/). Combina il codice di esempio il [riconoscimento rettangoli](~/ios/platform/introduction-to-ios11/vision.md#rectangles) dal framework di visione artificiale con i _MNINSTClassifier_ modelli CoreML per identificare una cifra in una fotografia scritto a mano.

![Riconoscimento di immagini di numero 3](coreml-images/vision3.png) ![Riconoscimento di immagini del numero 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. Creare un modello di visione artificiale CoreML

I modelli CoreML _MNISTClassifier_ viene caricato e quindi eseguito il wrapping in un `VNCoreMLModel` il modello che rende disponibili per le attività di visione artificiale. Questo codice crea inoltre due richieste di visione artificiale: prima di tutto per trovare i rettangoli in un'immagine, quindi per l'elaborazione di un rettangolo con il modello CoreML:

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

La classe deve comunque implementare il `HandleRectangles` e `HandleClassification` metodi per le richieste di visione artificiale, illustrate nei passaggi 3 e 4 di seguito.

### <a name="2-start-the-vision-processing"></a>2. Avviare l'elaborazione di visione artificiale

Il codice seguente avvia l'elaborazione della richiesta. Nel **CoreMLVision** esempio, questo codice viene eseguito dopo che l'utente ha selezionato un'immagine:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Questo gestore passa il `ciImage` al framework Vision `VNDetectRectanglesRequest` che è stato creato nel passaggio 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Gestire i risultati dell'elaborazione di visione artificiale

Una volta completato il rilevamento di rettangolo, viene eseguita la `HandleRectangles` metodo, che ritaglia l'immagine per estrarre il primo rettangolo, converte l'immagine del rettangolo in scala di grigi e lo passa al modello CoreML per la classificazione.

Il `request` parametro passato a questo metodo contiene i dettagli della richiesta di visione artificiale e l'uso di `GetResults<VNRectangleObservation>()` metodo, viene restituito un elenco dei rettangoli individuati nell'immagine. Il primo rettangolo `observations[0]` viene estratto e passato al modello CoreML:

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

Il `ClassificationRequest` inizializzato nel passaggio 1 per usare il `HandleClassification` metodo definito nel passaggio successivo.

### <a name="4-handle-the-coreml"></a>4. Gestire il CoreML

Il `request` parametro passato a questo metodo contiene i dettagli della richiesta e CoreML e l'uso di `GetResults<VNClassificationObservation>()` metodo, viene restituito un elenco di possibili risultati ordinati in tutta sicurezza (confidenza più elevato prima):

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

Esistono tre campioni CoreML per provare:

* Il [esempio di Predittore dei prezzi Habitat Mars](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) dispone di semplice input e output numerici.

* Il [Vision e CoreML esempio](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/) accetta un parametro di immagine e Usa il framework di visione artificiale per identificare aree quadrate nell'immagine, in cui vengono passate a un modello CoreML che riconosce singole cifre.

* Infine, il [esempio di riconoscimento di immagini CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/) Usa CoreML per identificare le funzionalità di una foto. Per impostazione predefinita viene utilizzato il valore più piccolo **SqueezeNet** modello (5MB), ma è stato scritto in modo che è possibile scaricare e incorporare il più grande **VGG16** modello (553 MB). Per altre informazioni, vedere la [Leggimi dell'esempio](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md).

## <a name="related-links"></a>Collegamenti correlati

- [Machine Learning (Apple)](https://developer.apple.com/machine-learning/)
- [Esempio CoreML (Mars Habitat) (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)
- [CoreML e visione artificiale (riconoscimento di numero) (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)
- [Riconoscimento di immagini CoreML (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)
- [CoreML con visione artificiale personalizzato di Azure (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
- [Introduzione a CoreML (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/703/)
