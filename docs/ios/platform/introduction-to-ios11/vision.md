---
title: Framework Vision
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/31/2016
ms.openlocfilehash: 698bf829128cff1263e98b49d29a77b75ec32ad9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="vision-framework"></a>Framework Vision

Il framework di Vision aggiunge un numero di immagine di nuovo l'elaborazione delle funzionalità a 11, tra cui iOS:

- [Rilevamento rettangolo](#rectangles)
- [Rilevamento viso](#faces)
- Analisi di apprendimento dell'immagine del computer (descritto in [CoreML](~/ios/platform/introduction-to-ios11/coreml.md))
- Rilevamento di codice a barre
- Analisi di allineamento dell'immagine
- Rilevamento di testo
- Rilevamento horizon
- Rilevamento e il rilevamento di oggetto

![Fotografare con tre rettangoli rilevati](vision-images/found-rectangles-tiny.png) ![Fotografare con due caratteri tipografici rilevati](vision-images/xamarin-home-faces-tiny.png)

Rilevamento rettangolo e rilevamento viso sono descritti in dettaglio più avanti.

<a name="rectangles" />

## <a name="rectangle-detection"></a>Rilevamento rettangolo

Il [VisionRects esempio](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) viene illustrato come elaborare un'immagine e disegnare rettangoli rilevati su di esso.

### <a name="1-initialize-the-vision-request"></a>1. Inizializzare la richiesta di Vision

In `ViewDidLoad`, creare un `VNDetectRectanglesRequest` che fa riferimento il `HandleRectangles` metodo che verrà chiamato alla fine di ogni richiesta:

Il `MaximumObservations` deve anche essere impostata, in caso contrario è impostata su 1 e verrà restituito un unico risultato.

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. Avviare l'elaborazione degli obiettivi

Il codice seguente avvia l'elaborazione della richiesta. Nel **VisionRects** esempio, questo codice viene eseguito dopo che l'utente ha selezionato un'immagine:

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Questo gestore passa il `ciImage` a framework visione `VNDetectRectanglesRequest` che è stato creato nel passaggio 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Gestire i risultati dell'elaborazione degli obiettivi

Una volta completato il rilevamento del rettangolo, il framework esegue il `HandleRectangles` metodo, di seguito è riportato un riepilogo dei quali:

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNRectangleObservation>();
  // ... omitted error handling ...
  bool atLeastOneValid = false;
  foreach (var o in observations){
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  if (!atLeastOneValid) return;
  // Show the pre-processed image
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. Visualizzare i risultati

Il `OverlayRectangles` metodo il **VisionRectangles** esempio dispone di tre funzioni:

- Il rendering dell'immagine di origine,
- Creazione di un rettangolo per indicare dove ciascuna di esse è stato rilevato, e
- Aggiunta di un'etichetta di testo per ogni rettangolo utilizzando CoreGraphics.

Visualizzazione di [origine dell'esempio](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) per il metodo CoreGraphics esatto.

![Fotografare con tre rettangoli rilevati](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. Un'ulteriore elaborazione

Rilevamento rettangolo è spesso il primo passaggio in una catena di operazioni, ad esempio con [in questo esempio CoreMLVision](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision), in cui i rettangoli vengono passati a un modello CoreML analizzare cifre scritte a mano.


<a name="faces" />

## <a name="face-detection"></a>Rilevamento viso

Il [VisionFaces esempio](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) funziona in modo simile al **VisionRectangles** di esempio, utilizzando un'altra classe richiesta visione.

### <a name="1-initialize-the-vision-request"></a>1. Inizializzare la richiesta di Vision

In `ViewDidLoad`, creare un `VNDetectFaceRectanglesRequest` che fa riferimento il `HandleRectangles` metodo che verrà chiamato alla fine di ogni richiesta.

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. Avviare l'elaborazione degli obiettivi

Il codice seguente avvia l'elaborazione della richiesta. Nel **VisionFaces** questo codice viene eseguito dopo che l'utente ha selezionato un'immagine di esempio:

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

Questo gestore passa il `ciImage` a framework visione `VNDetectFaceRectanglesRequest` che è stato creato nel passaggio 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Gestire i risultati dell'elaborazione degli obiettivi

Una volta completato il rilevamento del tipo di carattere, il gestore viene eseguito il `HandleRectangles` metodo che esegue la gestione degli errori e visualizza i limiti del facce rilevate e chiama il `OverlayRectangles` per disegnare rettangoli di delimitazione dell'immagine originale:

```csharp
private void HandleRectangles(VNRequest request, NSError error){
  var observations = request.GetResults<VNFaceObservation>();
  // ... omitted error handling...
  var summary = "";
  var imageSize = InputImage.Extent.Size;
  bool atLeastOneValid = false;
  Console.WriteLine("Faces:");
  summary += "Faces:";
  foreach (var o in observations) {
    // Verify detected rectangle is valid. omitted
    var boundingBox = o.BoundingBox.Scaled(imageSize);
    if (InputImage.Extent.Contains(boundingBox)) {
      atLeastOneValid |= true;
    }
  }
  // Show the pre-processed image (on UI thread)
  DispatchQueue.MainQueue.DispatchAsync(() =>
  {
    ClassificationLabel.Text = summary;
    ImageView.Image = OverlayRectangles(RawImage, imageSize, observations);
  });
}
```

### <a name="4-display-the-results"></a>4. Visualizzare i risultati

Il `OverlayRectangles` metodo il **VisionFaces** esempio dispone di tre funzioni:

- Il rendering dell'immagine di origine,
- Creazione di un rettangolo per ogni carattere tipografico rilevato, e
- Aggiunta di un'etichetta di testo per ogni tipo di carattere utilizzando CoreGraphics.

Visualizzazione di [origine dell'esempio](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) per il metodo CoreGraphics esatto.

![Fotografare con due caratteri tipografici rilevati](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. Un'ulteriore elaborazione

Il framework di Vision include funzionalità aggiuntive per rilevare le caratteristiche del viso, ad esempio occhi e bocca. Utilizzare il `VNDetectFaceLandmarksRequest` tipo, verrà restituito `VNFaceObservation` risultati come passaggio 3 riportato sopra, ma con ulteriori `VNFaceLandmark` dati.


## <a name="related-links"></a>Collegamenti correlati

- [Visione rettangoli (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)
- [Caratteri tipografici Vision (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)
- [Miglioramenti introdotti in un'immagine di base: i filtri, metallo, visione e più (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/510/)
