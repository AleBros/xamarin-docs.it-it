---
title: Framework di visione in Novell. iOS
description: Questo documento descrive come usare il Framework di visione di iOS 11 in Novell. iOS. In particolare, vengono illustrati il rilevamento del rettangolo e il rilevamento del viso.
ms.prod: xamarin
ms.assetid: 7273ED68-7B7D-4252-B3A0-02DB2E357A8C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/31/2017
ms.openlocfilehash: b58e7b1fffed3253d9765401d52f16b751db134d
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304751"
---
# <a name="vision-framework-in-xamarinios"></a>Framework di visione in Novell. iOS

Il Framework di visione aggiunge una serie di nuove funzionalità di elaborazione delle immagini a iOS 11, tra cui:

- [Rilevamento rettangolo](#rectangles)
- [Rilevamento viso](#faces)
- Analisi delle immagini Machine Learning (illustrata in [CoreML](~/ios/platform/introduction-to-ios11/coreml.md))
- Rilevamento del codice a barre
- Analisi allineamento immagine
- Rilevamento del testo
- Rilevamento dell'orizzonte
- Rilevamento di oggetti & rilevamento

![Foto con tre rettangoli rilevati](vision-images/found-rectangles-tiny.png) ![Fotografa con due visi rilevati](vision-images/xamarin-home-faces-tiny.png)

Il rilevamento e la Rilevamento viso del rettangolo vengono descritti in dettaglio di seguito.

<a name="rectangles" />

## <a name="rectangle-detection"></a>Rilevamento rettangolo

Nell' [esempio VisionRects](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles) viene illustrato come elaborare un'immagine e creare i rettangoli rilevati.

### <a name="1-initialize-the-vision-request"></a>1. Inizializza la richiesta di visione

In `ViewDidLoad`creare un `VNDetectRectanglesRequest` che faccia riferimento al metodo `HandleRectangles` che verrà chiamato alla fine di ogni richiesta:

È necessario impostare anche la proprietà `MaximumObservations`. in caso contrario, il valore predefinito è 1 e verrà restituito solo un singolo risultato.

```csharp
RectangleRequest = new VNDetectRectanglesRequest(HandleRectangles);
RectangleRequest.MaximumObservations = 10;
```

### <a name="2-start-the-vision-processing"></a>2. avviare l'elaborazione della visione

Il codice seguente avvia l'elaborazione della richiesta. Nell'esempio **VisionRects** questo codice viene eseguito dopo che l'utente ha selezionato un'immagine:

```csharp
// Run the rectangle detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {RectangleRequest}, out NSError error);
});
```

Questo gestore passa il `ciImage` al Framework di visione `VNDetectRectanglesRequest` creato nel passaggio 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. gestire i risultati dell'elaborazione della visione

Al termine del rilevamento del rettangolo, il Framework esegue il `HandleRectangles` metodo, un riepilogo di cui è illustrato di seguito:

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

### <a name="4-display-the-results"></a>4. visualizzare i risultati

Il metodo `OverlayRectangles` nell'esempio **VisionRectangles** dispone di tre funzioni:

- Rendering dell'immagine di origine
- Disegno di un rettangolo per indicare dove è stato rilevato ogni oggetto e
- Aggiunta di un'etichetta di testo per ogni rettangolo utilizzando CoreGraphics.

Consente di visualizzare l' [origine dell'esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles) per il metodo CoreGraphics esatto.

![Foto con tre rettangoli rilevati](vision-images/found-rectangles-phone-sml.png)

### <a name="5-further-processing"></a>5. ulteriore elaborazione

Il rilevamento del rettangolo è spesso solo il primo passaggio di una catena di operazioni, ad esempio con [questo esempio CoreMLVision](~/ios/platform/introduction-to-ios11/coreml.md#coremlvision), in cui i rettangoli vengono passati a un modello CoreML per analizzare le cifre scritte a mano.

<a name="faces" />

## <a name="face-detection"></a>Rilevamento viso

L' [esempio VisionFaces](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces) funziona in modo simile all'esempio **VisionRectangles** , usando una classe di richiesta di visione differente.

### <a name="1-initialize-the-vision-request"></a>1. Inizializza la richiesta di visione

In `ViewDidLoad`creare un `VNDetectFaceRectanglesRequest` che faccia riferimento al metodo `HandleRectangles` che verrà chiamato alla fine di ogni richiesta.

```csharp
FaceRectangleRequest = new VNDetectFaceRectanglesRequest(HandleRectangles);
```

### <a name="2-start-the-vision-processing"></a>2. avviare l'elaborazione della visione

Il codice seguente avvia l'elaborazione della richiesta. Nell'esempio **VisionFaces** questo codice viene eseguito dopo che l'utente ha selezionato un'immagine:

```csharp
// Run the face detector
var handler = new VNImageRequestHandler(ciImage, uiImage.Orientation.ToCGImagePropertyOrientation(), new VNImageOptions());
DispatchQueue.DefaultGlobalQueue.DispatchAsync(()=>{
  handler.Perform(new VNRequest[] {FaceRectangleRequest}, out NSError error);
});
```

Questo gestore passa il `ciImage` al Framework di visione `VNDetectFaceRectanglesRequest` creato nel passaggio 1.

### <a name="3-handle-the-results-of-vision-processing"></a>3. gestire i risultati dell'elaborazione della visione

Al termine del rilevamento della faccia, il gestore esegue il `HandleRectangles` metodo che esegue la gestione degli errori e Visualizza i limiti delle facce rilevate e chiama il `OverlayRectangles` per creare rettangoli di delimitazione nell'immagine originale:

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

### <a name="4-display-the-results"></a>4. visualizzare i risultati

Il metodo `OverlayRectangles` nell'esempio **VisionFaces** dispone di tre funzioni:

- Rendering dell'immagine di origine
- Disegno di un rettangolo per ogni viso rilevato
- Aggiunta di un'etichetta di testo per ogni face con CoreGraphics.

Consente di visualizzare l' [origine dell'esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces) per il metodo CoreGraphics esatto.

![Fotografa con due visi rilevati](vision-images/found-faces-phone-sml.png)

### <a name="5-further-processing"></a>5. ulteriore elaborazione

Il Framework di visione include funzionalità aggiuntive per rilevare le funzionalità del viso, ad esempio gli occhi e la bocca. Usare il tipo di `VNDetectFaceLandmarksRequest`, che restituirà `VNFaceObservation` risultati come indicato nel passaggio 3, ma con ulteriori dati di `VNFaceLandmark`.

## <a name="related-links"></a>Collegamenti correlati

- [Rettangoli visivi (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionrectangles)
- [Visi visivi (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-visionfaces)
- [Progressi nell'immagine principale-filtri, Metal, visione e altro (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/510/)
