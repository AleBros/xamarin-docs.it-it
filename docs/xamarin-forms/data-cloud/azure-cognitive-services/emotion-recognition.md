---
title: Riconoscimento delle emozioni usando l'API viso
description: L'API viso accetta un'espressione del viso in un'immagine come input e restituisce i dati che includono livelli di confidenza in un set di emozioni per ogni viso nell'immagine. Questo articolo illustra come usare l'API viso per riconoscere le emozioni, per valutare un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 6f03ae1030ef4a69b15c5e219785eee12c4e603b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656568"
---
# <a name="emotion-recognition-using-the-face-api"></a>Riconoscimento delle emozioni usando l'API viso

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_L'API viso accetta un'espressione del viso in un'immagine come input e restituisce i dati che includono livelli di confidenza in un set di emozioni per ogni viso nell'immagine. Questo articolo illustra come usare l'API viso per riconoscere le emozioni, per valutare un'applicazione xamarin. Forms._

## <a name="overview"></a>Panoramica

L'API viso è possibile eseguire il rilevamento di emozioni per rilevare rabbia, biasimo, disgusto, paura, felicità, neutralità, tristezza e sorpresa, in un'espressione del viso. Queste emozioni vengono comunicate universalmente e visualizzarli tramite espressioni del viso base stesso. Oltre a restituire un risultato di emozioni per un'espressione del viso, l'API viso possono anche restituisce un riquadro delimitatore per i volti rilevati. Si noti che una chiave API deve essere ottenuta per usare l'API viso. Per ottenere tale valore in [prova servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Riconoscimento delle emozioni può essere eseguito tramite una libreria client e tramite l'API REST. Questo articolo è incentrato su come eseguire il riconoscimento delle emozioni tramite l'API REST. Per altre informazioni sull'API REST, vedere [API REST viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

L'API viso consente inoltre di riconoscere l'espressione del volto delle persone nel video e può restituire un riepilogo delle emozioni. Per altre informazioni, vedere [come analizzare i video in tempo reale](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

Per altre informazioni sull'API viso, vedere [API viso](/azure/cognitive-services/face/overview/).

## <a name="authentication"></a>Autenticazione

Tutte le richieste inviate all'API viso richiede una chiave API che deve essere specificata come valore del `Ocp-Apim-Subscription-Key` intestazione. Esempio di codice seguente viene illustrato come aggiungere la chiave API per il `Ocp-Apim-Subscription-Key` intestazione di una richiesta:

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

Errore per passare una chiave API valida per l'API viso comporterà un errore di 401 risposta.

## <a name="performing-emotion-recognition"></a>Esecuzione di riconoscimento delle emozioni

Riconoscimento delle emozioni viene eseguito mediante una richiesta POST contenente un'immagine per il `detect` all'API `https://[location].api.cognitive.microsoft.com/face/v1.0`, dove `[location]]` è l'area è utilizzato per ottenere la chiave API. I parametri della richiesta facoltativo sono:

- `returnFaceId` -se restituire faceIds delle facce rilevate. Il valore predefinito è `true`.
- `returnFaceLandmarks` -se restituire i riquadri delle facce rilevate. Il valore predefinito è `false`.
- `returnFaceAttributes` -Se si desidera analizzare e restituire uno o più specificati gli attributi facciali. Attributi facciali supportati includono `age`, `gender`, `headPose`, `smile`, `facialHair`, `glasses`, `emotion`, `hair`, `makeup`, `occlusion`, `accessories`, `blur`, `exposure`, e `noise`. Si noti che analysis attributo face ha costo di calcolo e il tempo aggiuntivo.

Contenuto dell'immagine deve essere inserito nel corpo della richiesta POST come URL o dati binari.

> [!NOTE]
> Formati di file di immagine supportati sono JPEG, PNG, GIF e BMP e le dimensioni di file consentiti sono compreso tra 1KB e 4MB.

Nell'applicazione di esempio, viene richiamato il processo di riconoscimento delle emozioni chiamando il `DetectAsync` metodo:

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

Questa chiamata al metodo specifica il flusso contenente i dati dell'immagine, che deve essere restituito faceIds, che non devono essere restituite riquadri e che devono essere analizzate le emozioni dell'immagine. Specifica inoltre che i risultati verranno restituiti come matrice di `Face` oggetti. A sua volta, il `DetectAsync` metodo richiama il `detect` l'API REST che esegue il riconoscimento delle emozioni:

```csharp
public async Task<Face[]> DetectAsync(Stream imageStream, bool returnFaceId, bool returnFaceLandmarks, IEnumerable<FaceAttributeType> returnFaceAttributes)
{
  var requestUrl =
    $"{Constants.FaceEndpoint}/detect?returnFaceId={returnFaceId}" +
    "&returnFaceLandmarks={returnFaceLandmarks}" +
    "&returnFaceAttributes={GetAttributeString(returnFaceAttributes)}";
  return await SendRequestAsync<Stream, Face[]>(HttpMethod.Post, requestUrl, imageStream);
}
```

Questo metodo genera un URI di richiesta e quindi invia la richiesta per il `detect` API tramite la `SendRequestAsync` (metodo).

> [!NOTE]
> Nelle chiamate API viso come è stato usato per ottenere le chiavi di sottoscrizione, è necessario usare la stessa area. Ad esempio, se è stato ottenuto le chiavi di sottoscrizione dal `westus` area, l'endpoint di rilevamento viso sarà `https://westus.api.cognitive.microsoft.com/face/v1.0/detect`.

### <a name="sending-the-request"></a>L'invio della richiesta

Il `SendRequestAsync` metodo effettua la richiesta POST all'API viso e restituisce il risultato come un `Face` matrice:

```csharp
async Task<TResponse> SendRequestAsync<TRequest, TResponse>(HttpMethod httpMethod, string requestUrl, TRequest requestBody)
{
  var request = new HttpRequestMessage(httpMethod, Constants.FaceEndpoint);
  request.RequestUri = new Uri(requestUrl);
  if (requestBody != null)
  {
    if (requestBody is Stream)
    {
      request.Content = new StreamContent(requestBody as Stream);
      request.Content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
    }
    else
    {
      // If the image is supplied via a URL
      request.Content = new StringContent(JsonConvert.SerializeObject(requestBody, s_settings), Encoding.UTF8, "application/json");
    }
  }

  HttpResponseMessage responseMessage = await _client.SendAsync(request);
  if (responseMessage.IsSuccessStatusCode)
  {
    string responseContent = null;
    if (responseMessage.Content != null)
    {
      responseContent = await responseMessage.Content.ReadAsStringAsync();
    }
    if (!string.IsNullOrWhiteSpace(responseContent))
    {
      return JsonConvert.DeserializeObject<TResponse>(responseContent, s_settings);
    }
    return default(TResponse);
  }
  else
  {
    ...
  }
  return default(TResponse);
}
```

Se l'immagine viene fornita tramite un flusso, il metodo si basa la richiesta POST e racchiude il flusso di immagine in un `StreamContent` istanza che fornisce contenuto HTTP in base a un flusso. In alternativa, se l'immagine viene fornita tramite un URL, il metodo crea la richiesta POST eseguendo il wrapping l'URL in un `StringContent` istanza che fornisce contenuto HTTP basato su una stringa.

La richiesta POST viene quindi inviata a `detect` API. La risposta viene letto, deserializzata e restituita al metodo di chiamata.

Il `detect` API invierà il codice di stato HTTP 200 (OK) in risposta, fornita che la richiesta sia valida, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste sono presenti nella risposta. Per un elenco di possibili risposte di errore, vedere [API REST viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

### <a name="processing-the-response"></a>L'elaborazione della risposta

La risposta dell'API viene restituita in formato JSON. I dati JSON seguenti mostrano un messaggio di risposta con esito positivo tipico che fornisce i dati richiesti dall'applicazione di esempio:

```json
[  
   {  
      "faceId":"8a1a80fe-1027-48cf-a7f0-e61c0f005051",
      "faceRectangle":{  
         "top":192,
         "left":164,
         "width":339,
         "height":339
      },
      "faceAttributes":{  
         "emotion":{  
            "anger":0.0,
            "contempt":0.0,
            "disgust":0.0,
            "fear":0.0,
            "happiness":1.0,
            "neutral":0.0,
            "sadness":0.0,
            "surprise":0.0
         }
      }
   }
]
```

Un messaggio di risposta con esito positivo è costituito da una matrice di voci di volti classificati in base alle dimensioni rettangolo viso in ordine decrescente, mentre una risposta vuota non indica nessun volti rilevati. Ogni riconosciuto volti includono una serie di attributi facciali facoltativo, che sono specificati dal `returnFaceAttributes` argomento per il `DetectAsync` (metodo).

Nell'applicazione di esempio, la risposta JSON viene deserializzata in una matrice di `Face` oggetti. Per l'interpretazione dei risultati dell'API viso, le emozioni rilevate devono essere interpretate come le emozioni con il punteggio più elevato, come i punteggi vengono normalizzati a somma a uno. Di conseguenza, l'applicazione di esempio consente di visualizzare le emozioni riconosciuto con il punteggio più elevato per il viso rilevato più grande dell'immagine. Questo risultato viene ottenuto con il codice seguente:

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

Lo screenshot seguente mostra il risultato del processo di riconoscimento delle emozioni nell'applicazione di esempio:

![](emotion-recognition-images/emotion-recognition.png "Riconoscimento delle emozioni")

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare l'API viso per riconoscere le emozioni, per valutare un'applicazione xamarin. Forms. L'API viso accetta un'espressione del viso in un'immagine come input e restituisce i dati che include il livello di confidenza in un set di emozioni per ogni viso nell'immagine.

## <a name="related-links"></a>Collegamenti correlati

- [API viso](/azure/cognitive-services/face/overview/).
- [Servizi cognitivi TODO (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [REST API viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
