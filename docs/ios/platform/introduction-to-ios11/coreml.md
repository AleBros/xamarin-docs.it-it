---
title: Introduzione a CoreML
description: Machine learning per App per dispositivi mobili su iOS 11
ms.topic: article
ms.prod: xamarin
ms.assetid: BE1E2CA1-E3AE-4C90-914C-CFDBD1DCB82B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: 0932130275cd037b3d4414c9c7a421dbee360eeb
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-coreml"></a>Introduzione a CoreML

_Machine learning per App per dispositivi mobili su iOS 11_

CoreML porta machine learning per iOS: le applicazioni possono sfruttare i modelli di apprendimento con training macchina per eseguire tutti i tipi di attività, da problemi di riconoscimento di immagini.

In questa introduzione vengono illustrate le operazioni seguenti:

- [Introduzione a CoreML](#coreml)
- [Utilizzo di CoreML con framework Vision](#coremlvision)

<a name="coreml" />

## <a name="getting-started-with-coreml"></a>Introduzione a CoreML

Questi passaggi viene descritto come aggiungere CoreML a un progetto iOS. Consultare la [esempio Mars Habitat Pricer](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) per un esempio pratico.

![Schermata di esempio Predittore dei prezzi Habitat MARS](coreml-images/marspricer-heading.png)

### <a name="1-add-the-model-to-the-project"></a>1. Aggiungere il modello al progetto

Aggiunta di un modello compilato (una directory con il **.modelc** estensione) per il **risorse** directory del progetto. Il contenuto della directory deve avere un'azione di compilazione **BundleResource**:

![La cartella di risorse deve contenere il modello compilato](coreml-images/resources-modelc.png)

Il [esempi](https://developer.xamarin.com/samples/monotouch/ios11/) utilizzare i modelli compilati in Xcode 9 o manualmente utilizzando il seguente comando terminal:

```csharp
xcrun coremlcompiler compile {model.mlmodel} {outputFolder}
```

> [!NOTE]
> **Model** file _deve_ compilata in **.modelc** prima di poter usati da CoreML

### <a name="2-load-the-model"></a>2. Caricare il modello

Prima di utilizzare un modello, caricarlo utilizzando il `MLModel.FromUrl` metodo statico:

```csharp
var assetPath = NSBundle.MainBundle.GetUrlForResource("NameOfModel", "mlmodelc");
model = MLModel.FromUrl(assetPath, out NSError error1);
```

### <a name="3-set-the-parameters"></a>3. Impostare i parametri

Vengono passati i parametri del modello utilizzando una classe contenitore che implementa `IMLFeatureProvider`.

Classi di provider di funzionalità si comportano come un dizionario di stringhe e `MLFeatureValue`s, in cui ogni valore funzionalità potrebbe essere una stringa semplice o numero, una matrice o dati o un buffer di pixel che contiene un'immagine.

Codice per un provider di funzionalità a valore singolo è illustrato di seguito:

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

Utilizzo delle classi simile al seguente, i parametri di input possono essere forniti in modo che è stato riconosciuto dal CoreML. I nomi delle funzionalità (ad esempio `myParam` nell'esempio di codice) deve corrispondere a quello previsto il modello.

### <a name="4-run-the-model"></a>4. Eseguire il modello

Utilizzo del modello richiede che il provider di funzionalità deve essere creata un'istanza e i parametri impostati, quindi che il `GetPrediction` metodo chiamato:

```csharp
var input = new MyInput {MyParam = 13};
var outFeatures = model.GetPrediction(inputFeatures, out NSError error2);
```

### <a name="5-extract-the-results"></a>5. Estrarre i risultati

Il risultato di stima `outFeatures` è anche un'istanza di `IMLFeatureProvider`; output valori è possibile accedere tramite `GetFeatureValue` con il nome di ogni parametro di output (ad esempio `theResult`), come nel seguente esempio:

```csharp
var result = outFeatures.GetFeatureValue("theResult").DoubleValue; // eg. 6227020800
```

<a name="coremlvision" />

## <a name="using-coreml-with-the-vision-framework"></a>Utilizzo di CoreML con Framework Vision

CoreML anche utilizzabile in combinazione con il framework di vista per eseguire operazioni su immagine, ad esempio il riconoscimento delle forme, identificazione dell'oggetto e altre attività.

I passaggi seguenti descrivono come CoreML e visione sono usate insieme nel [CoreMLVision esempio](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/). Combina il codice di esempio di [riconoscimento rettangoli](~/ios/platform/introduction-to-ios11/vision.md#rectangles) dal framework visione con il _MNINSTClassifier_ CoreML modello per identificare una cifra scritte a mano in scatta una foto.

![Riconoscimento di immagini di numero 3](coreml-images/vision3.png) ![Riconoscimento di immagini del numero 5](coreml-images/vision5.png)

### <a name="1-create-a-vision-coreml-model"></a>1. Creare un modello CoreML Vision

Il modello CoreML _MNISTClassifier_ viene caricato ed eseguito il wrapping in un `VNCoreMLModel` che rende disponibili per l'attività di visione del modello. Questo codice crea inoltre due richieste visione: prima di tutto per trovare i rettangoli in un'immagine, quindi per l'elaborazione di un rettangolo con il modello CoreML:

```csharp
// Load the ML model
var assetPath = NSBundle.MainBundle.GetUrlForResource("MNISTClassifier", "mlmodelc");
var mlModel = MLModel.FromUrl(assetPath, out NSError mlErr);
var vModel = VNCoreMLModel.FromMLModel(mlModel, out NSError vnErr);

// Initialize Vision requests
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
ClassificationRequest = new VNCoreMLRequest(vModel, HandleClassification);
```

La classe deve implementare il `HandleRectangles` e `HandleClassification` metodi per le richieste di visione, mostrate nei passaggi 3 e 4 di seguito.

### <a name="2-start-the-vision-processing"></a>2. Avviare l'elaborazione degli obiettivi

Il codice seguente avvia l'elaborazione della richiesta. Nel **CoreMLVision** esempio, questo codice viene eseguito dopo che l'utente ha selezionato un'immagine:

```csharp
// Run the rectangle detector, which upon completion runs the ML classifier.
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Questo gestore passa il `ciImage` a framework visione `VNDetectRectanglesRequest` che è stato creato nel passaggio 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Gestire i risultati dell'elaborazione degli obiettivi

Una volta completato il rilevamento del rettangolo, viene eseguito il `HandleRectangles` metodo, che ritaglia l'immagine per estrarre il primo rettangolo, converte l'immagine del rettangolo in scala di grigi e lo passa al modello CoreML per la classificazione.

Il `request` parametro passato a questo metodo contiene i dettagli della richiesta, visione e l'utilizzo di `GetResults<VNRectangleObservation>()` metodo, viene restituito un elenco di rettangoli trovato nell'immagine. Il primo rettangolo `observations[0]` vengono estratti e passati al modello CoreML:

```csharp
void HandleRectangles(VNRequest request, NSError error) {
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  var detectedRectangle = observations[0]; // first rectangle
  // ... omitted cropping and greyscale conversion ...
  // Run the Core ML MNIST classifier -- results in handleClassification method
  var handler = new VNImageRequestHandler(correctedImage, new VNImageOptions());
  DispatchQueue.DefaultGlobalQueue.DispatchAsync(() => {
    handler.Perform(new VNRequest[] { ClassificationRequest }, out NSError err);
  });
}
```

Il `ClassificationRequest` è stato inizializzato nel passaggio 1 per utilizzare il `HandleClassification` metodo definito nel passaggio successivo.

### <a name="4-handle-the-coreml"></a>4. Handle di CoreML

Il `request` parametro passato a questo metodo contiene i dettagli della richiesta CoreML e l'utilizzo di `GetResults<VNClassificationObservation>()` metodo, viene restituito un elenco di possibili risultati ordinati confidenza (confidenza più elevato prima):

```csharp
void HandleClassification(VNRequest request, NSError error){
  var observations = request.GetResults<VNClassificationObservation>();
  ... omitted error handling ...
  var best = observations[0]; // first/best classification result
  // render in UI
  DispatchQueue.MainQueue.DispatchAsync(()=>{
    ClassificationLabel.Text = $"Classification: {best.Identifier} Confidence: {best.Confidence * 100f:#.00}%";
  });
}
```



## <a name="samples"></a>Esempi

Esistono tre esempi CoreML per provare:

* Il [esempio Predittore dei prezzi Habitat Mars](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/) è semplice input e output numerici.

* Il [esempio Vision & CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/) accetta un parametro di immagini e utilizza il framework di vista per identificare le aree quadrate dell'immagine, che vengono passate a un modello CoreML che riconosce sola cifre.

* Infine, il [esempio riconoscimento di immagini CoreML](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/) utilizza CoreML per identificare le funzionalità di una foto. Per impostazione predefinita viene utilizzato il più piccolo **SqueezeNet** modello (5MB), ma è stato scritto in modo che è possibile scaricare e incorporare il più elevato **VGG16** modello (553 MB). Per ulteriori informazioni, vedere il [Leggimi dell'esempio](https://github.com/xamarin/ios-samples/blob/master/ios11/CoreMLImageRecognition/CoreMLImageRecognition/README.md).


## <a name="related-links"></a>Collegamenti correlati

- [Machine Learning (mela)](https://developer.apple.com/machine-learning/)
- [Esempio CoreML (Mars Habitat) (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/CoreML/)
- [CoreML e visione (numero di riconoscimento) (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLVision/)
- [Riconoscimento di immagini CoreML (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLImageRecognition/)
- [CoreML con vista personalizzata di Azure (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/CoreMLAzureModel)
- [Introduzione a CoreML (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/703/)
