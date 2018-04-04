---
title: Riconoscimento emozioni utilizzando quadrante dell'API
description: L'API viso accetta un'espressione facciale in un'immagine come input e restituisce i dati che include i livelli di confidenza in un set di emozioni per ogni carattere tipografico nell'immagine. In questo articolo viene illustrato come utilizzare l'API viso per riconoscere emozioni, per valutare un'applicazione di xamarin. Forms.
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 49e53425dbaf3aadd74d02ab030929e3311c7c8c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="emotion-recognition-using-the-face-api"></a>Riconoscimento emozioni utilizzando quadrante dell'API

_L'API viso accetta un'espressione facciale in un'immagine come input e restituisce i dati che include i livelli di confidenza in un set di emozioni per ogni carattere tipografico nell'immagine. In questo articolo viene illustrato come utilizzare l'API viso per riconoscere emozioni, per valutare un'applicazione di xamarin. Forms._

## <a name="overview"></a>Panoramica

L'API viso eseguibili emozioni rilevamento per rilevare anger, guidiziaria, pretendere una risposta immediata, paura, poche neutro, sadness e categoria Sorpresa, in un'espressione facciale. Questi emozioni vengono comunicati universalmente e visualizzarli tramite le stesse espressioni facciale base. Nonché restituendo un risultato emozioni per un'espressione del viso, l'API viso può anche restituisce un rettangolo per i caratteri tipografici rilevati. Si noti che è necessario ottenere una chiave API per utilizzare l'API di tipo di carattere. Può essere ottenuto in [provare servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Riconoscimento emozioni può essere eseguito tramite una libreria client e tramite un'API REST. Questo articolo è incentrato sull'esecuzione di riconoscimento emozioni tramite il [Microsoft.ProjectOxford.Face](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/) libreria client, che può essere scaricata da NuGet.

L'API viso consente inoltre di riconoscere le espressioni facciale delle persone incluse nella video e può restituire un riepilogo delle loro emozioni. Per altre informazioni, vedere [come analizzare video in tempo reale](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

Per ulteriori informazioni sull'API di tipo di carattere, vedere [API viso](/azure/cognitive-services/face/overview/).

## <a name="performing-emotion-recognition"></a>Esecuzione di riconoscimento emozioni

Riconoscimento emozioni scopo è necessario caricare un flusso di immagini per l'API viso. Le dimensioni del file di immagine non devono essere superiori a 4MB e formati di file supportati sono BMP, PNG, GIF e JPEG.

Esempio di codice seguente viene illustrato il processo di riconoscimento emozioni:

```csharp
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;

var faceServiceClient = new FaceServiceClient(Constants.FaceApiKey, Constants.FaceEndpoint);
// e.g. var faceServiceClient = new FaceServiceClient("a3dbe2ed6a5a9231bb66f9a964d64a12", "https://westus.api.cognitive.microsoft.com/face/v1.0/detect");

var faceAttributes = new FaceAttributeType[] { FaceAttributeType.Emotion };
using (var photoStream = photo.GetStream())
{
    Face[] faces = await faceServiceClient.DetectAsync(photoStream, true, false, faceAttributes);
    if (faces.Any())
    {
        // Emotions detected are happiness, sadness, surprise, anger, fear, contempt, disgust, or neutral.
        emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
    }
    // Store emotion as app rating
    ...
}
```

Un' `FaceServiceClient` per eseguire il riconoscimento emozioni, con la chiave API viso punti iniziale e passati come argomenti, è necessario creare l'istanza di `FaceServiceClient` costruttore.

> [!NOTE]
> Nelle chiamate API viso come è stato usato per ottenere le chiavi di sottoscrizione, è necessario utilizzare la stessa area. Ad esempio, se è stato ottenuto le chiavi di sottoscrizione nella `westus` area, l'endpoint di rilevamento viso sarà `https://westus.api.cognitive.microsoft.com/face/v1.0/detect`.

Il `DetectAsync` metodo, che viene richiamato sul `FaceServiceClient` dell'istanza, carica un'immagine per l'API di tipo di carattere, come un `Stream`. La chiave API verrà inviata all'API viso quando viene richiamata l'operazione. Un errore di invio di una chiave API valida comporterà un `Microsoft.ProjectOxford.Face.FaceAPIException` generata, con il messaggio di eccezione che indica che è stata inviata una chiave API non valida.

Il `DetectAsync` metodo restituirà un `Face` matrice, a condizione che un tipo di carattere è stata riconosciuta. Ogni oggetto restituito faccia contiene un rettangolo per indicare la posizione, combinata con una serie di attributi faccia facoltativo, che vengono specificati dal `faceAttributes` argomento per il `DetectAsync` metodo. Se non viene rilevato alcun tipo di carattere, un oggetto vuoto `Face` matrice verrà restituita.

Quando si interpretano i risultati dall'API viso, emozioni rilevata devono essere interpretate come emozioni con il punteggio più elevato, come i punteggi vengono normalizzati da sommare a uno. Di conseguenza, l'applicazione di esempio visualizza l'emozioni riconosciuta con il punteggio più elevato per il tipo di carattere rilevato più grande dell'immagine, come illustrato nelle schermate seguenti:

![](emotion-recognition-images/emotion-recognition.png "Riconoscimento emozioni")

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come utilizzare l'API viso per riconoscere emozioni, per valutare un'applicazione di xamarin. Forms. L'API viso accetta un'espressione facciale in un'immagine come input e restituisce i dati che include il livello di confidenza in un set di emozioni per ogni carattere tipografico nell'immagine.

## <a name="related-links"></a>Collegamenti correlati

- [API devono affrontare](/azure/cognitive-services/face/overview/).
- [Servizi cognitivi TODO (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Microsoft.ProjectOxford.Face](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/)
