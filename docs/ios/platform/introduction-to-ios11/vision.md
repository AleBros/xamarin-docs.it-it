---
title: Framework Vision in xamarin. IOS
description: Questo documento viene descritto come utilizzare iOS 11 Framework Vision in xamarin. IOS. In particolare, illustra il rilevamento di rettangolo e rilevamento viso.
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/31/2017
ms.openlocfilehash: 4746de2f351e866fd72946b204f97e997c3e88c4
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350664"
---
# <a name="vision-framework-in-xamarinios"></a>Framework Vision in xamarin. IOS

Il framework Vision aggiunge il numero di nuove funzionalità a iOS 11, tra cui di elaborazione di immagini:

- [Rilevamento rettangolo](#rectangles)
- [Rilevamento viso](#faces)
- Machine Learning analisi delle immagini (descritto in [CoreML](~/ios/platform/introduction-to-ios11/coreml.md))
- Rilevamento di codice a barre
- Analisi di allineamento dell'immagine
- Rilevamento di testo
- Rilevamento horizon
- Rilevamento e rilevamento oggetti

![Fotografare con tre rettangoli rilevati](vision-images/found-rectangles-tiny.png) ![Fotografare con due visi rilevati](vision-images/xamarin-home-faces-tiny.png)

Rilevamento di rettangolo e rilevamento del viso sono illustrati in dettaglio più avanti.

<a name="rectangles" />

## <a name="rectangle-detection"></a>Rilevamento rettangolo

Il [VisionRects esempio](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) Mostra come elaborare un'immagine e disegnare rettangoli rilevati su di esso.

### <a name="1-initialize-the-vision-request"></a>1. Inizializzare la richiesta di visione artificiale

Nelle `ViewDidLoad`, creare un `VNDetectRectanglesRequest` che fa riferimento il `HandleRectangles` metodo che verrà chiamato alla fine di ogni richiesta:

Il `MaximumObservations` proprietà deve essere impostata anche, in caso contrario, per impostazione predefinita sarà 1 e verrà restituito solo un singolo risultato.

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. Avviare l'elaborazione di visione artificiale

Il codice seguente avvia l'elaborazione della richiesta. Nel **VisionRects** esempio, questo codice viene eseguito dopo che l'utente ha selezionato un'immagine:

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Questo gestore passa il `ciImage` al framework Vision `VNDetectRectanglesRequest` che è stato creato nel passaggio 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Gestire i risultati dell'elaborazione di visione artificiale

Una volta completato il rilevamento di rettangolo, il framework esegue il `HandleRectangles` metodo, di seguito è riportato un riepilogo dei quali:

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

Il `OverlayRectangles` metodo nella **VisionRectangles** esempio ha tre funzioni:

- Il rendering dell'immagine di origine,
- Disegna un rettangolo per indicare dove ciascuna di esse è stato rilevato, e
- Aggiunta di un'etichetta di testo per ogni rettangolo utilizzando CoreGraphics.

Visualizza i [origine dell'esempio](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/) per il metodo CoreGraphics esatto.

![Fotografare con tre rettangoli rilevati](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. Un'ulteriore elaborazione

Rilevamento rettangolo è spesso solo il primo passaggio in una catena di operazioni, ad esempio con [in questo esempio CoreMLVision](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision), in cui i rettangoli vengono passati a un modello CoreML per analizzare cifre scritte a mano.


<a name="faces" />

## <a name="face-detection"></a>Rilevamento viso

Il [esempio VisionFaces](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) funziona in modo simile al **VisionRectangles** di esempio, usando un'altra classe richiesta di visione artificiale.

### <a name="1-initialize-the-vision-request"></a>1. Inizializzare la richiesta di visione artificiale

Nelle `ViewDidLoad`, creare un `VNDetectFaceRectanglesRequest` che fa riferimento il `HandleRectangles` metodo che verrà chiamato alla fine di ogni richiesta.

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. Avviare l'elaborazione di visione artificiale

Il codice seguente avvia l'elaborazione della richiesta. Nel **VisionFaces** questo codice viene eseguito dopo che l'utente ha selezionato un'immagine di esempio:

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

Questo gestore passa il `ciImage` al framework Vision `VNDetectFaceRectanglesRequest` che è stato creato nel passaggio 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. Gestire i risultati dell'elaborazione di visione artificiale

Una volta completato il rilevamento del viso, il gestore esegue le `HandleRectangles` metodo che esegue la gestione degli errori e visualizza i limiti dei volti rilevati e le chiamate di `OverlayRectangles` su cui disegnare rettangoli di delimitazione dell'immagine originale:

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

Il `OverlayRectangles` metodo nella **VisionFaces** esempio ha tre funzioni:

- Il rendering dell'immagine di origine,
- Disegna un rettangolo di ogni volto rilevato, e
- Aggiunta di un'etichetta di testo per ogni viso usando CoreGraphics.

Visualizza i [origine dell'esempio](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/) per il metodo CoreGraphics esatto.

![Fotografare con due visi rilevati](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. Un'ulteriore elaborazione

Il framework Vision include funzionalità aggiuntive per rilevare le caratteristiche del viso, ad esempio gli occhi e bocca. Usare la `VNDetectFaceLandmarksRequest` tipo, che restituirà `VNFaceObservation` risultati come indicato nel passaggio 3 riportato sopra, ma con ulteriori `VNFaceLandmark` dei dati.


## <a name="related-links"></a>Collegamenti correlati

- [Rettangoli di visione artificiale (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/VisionRectangles/)
- [Visione artificiale visi (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/VisionFaces/)
- [Progressi nell'immagine di Core - filtri, bare Metal, visione artificiale e più (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/510/)
