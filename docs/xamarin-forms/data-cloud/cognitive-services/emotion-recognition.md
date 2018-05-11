---
title: Riconoscimento emozioni utilizzando quadrante dell'API
description: L'API viso accetta un'espressione facciale in un'immagine come input e restituisce i dati che include i livelli di confidenza in un set di emozioni per ogni carattere tipografico nell'immagine. In questo articolo viene illustrato come utilizzare l'API viso per riconoscere emozioni, per valutare un'applicazione di xamarin. Forms.
ms.prod: xamarin
ms.assetid: 19D36A7C-E8D8-43D1-BE80-48DE6C02879A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2018
ms.openlocfilehash: 4dc04cb077b894b255eb496b2cb2983626573897
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="emotion-recognition-using-the-face-api"></a>Riconoscimento emozioni utilizzando quadrante dell'API

_L'API viso accetta un'espressione facciale in un'immagine come input e restituisce i dati che include i livelli di confidenza in un set di emozioni per ogni carattere tipografico nell'immagine. In questo articolo viene illustrato come utilizzare l'API viso per riconoscere emozioni, per valutare un'applicazione di xamarin. Forms._

## <a name="overview"></a>Panoramica

L'API viso eseguibili emozioni rilevamento per rilevare anger, guidiziaria, pretendere una risposta immediata, paura, poche neutro, sadness e categoria Sorpresa, in un'espressione facciale. Questi emozioni vengono comunicati universalmente e visualizzarli tramite le stesse espressioni facciale base. Nonché restituendo un risultato emozioni per un'espressione del viso, l'API viso può anche restituisce un rettangolo per i caratteri tipografici rilevati. Si noti che è necessario ottenere una chiave API per utilizzare l'API di tipo di carattere. Può essere ottenuto in [provare servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Riconoscimento emozioni può essere eseguito tramite una libreria client e tramite un'API REST. Questo articolo è incentrato sull'esecuzione di riconoscimento emozioni attraverso l'API REST. Per ulteriori informazioni sull'API REST, vedere [API REST faccia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

L'API viso consente inoltre di riconoscere le espressioni facciale delle persone incluse nella video e può restituire un riepilogo delle loro emozioni. Per altre informazioni, vedere [come analizzare video in tempo reale](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

Per ulteriori informazioni sull'API di tipo di carattere, vedere [API viso](/azure/cognitive-services/face/overview/).

## <a name="authentication"></a>Autenticazione

Tutte le richieste inviate all'API viso richiede una chiave API che deve essere specificata come valore della `Ocp-Apim-Subscription-Key` intestazione. Esempio di codice seguente viene illustrato come aggiungere la chiave API per la `Ocp-Apim-Subscription-Key` intestazione di una richiesta:

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

Per passare una chiave API valida per l'API viso vengono rispettate, un errore di 401 risposta.

## <a name="performing-emotion-recognition"></a>Esecuzione di riconoscimento emozioni

Riconoscimento emozioni viene eseguito tramite una richiesta POST che contiene un'immagine per il `detect` API all'indirizzo `https://[location].api.cognitive.microsoft.com/face/v1.0`, dove `[location]]` corrisponde all'area utilizzata per ottenere la chiave API. I parametri della richiesta facoltativo sono:

- `returnFaceId` – se restituire faceIds delle facce rilevate. Il valore predefinito è `true`.
- `returnFaceLandmarks` – se restituire i punti di faccia riferimento di facce rilevate. Il valore predefinito è `false`.
- `returnFaceAttributes` : se si desidera analizzare e restituire uno o più specificati devono affrontare gli attributi. Gli attributi faccia supportati includono `age`, `gender`, `headPose`, `smile`, `facialHair`, `glasses`, `emotion`, `hair`, `makeup`, `occlusion`, `accessories`, `blur`, `exposure`, e `noise`. Si noti che faccia attributo analysis ha costo di calcolo e il tempo aggiuntivo.

Contenuto dell'immagine deve trovarsi nel corpo della richiesta POST come URL o dati binari.

> [!NOTE]
> Formati di file di immagine supportati sono JPEG, PNG, GIF e BMP e le dimensioni consentite del file sono compreso tra 1KB e 4MB.

Nell'applicazione di esempio, il processo di riconoscimento emozioni viene richiamato chiamando il `DetectAsync` metodo:

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

Questa chiamata al metodo specifica il flusso contenente i dati dell'immagine, che deve essere restituito faceIds, che non devono essere restituite faccia punti di riferimento e che devono essere analizzata, emozioni dell'immagine. Specifica inoltre che verranno restituiti i risultati sotto forma di matrice di `Face` oggetti. A sua volta, il `DetectAsync` metodo richiama il `detect` API REST che esegue il riconoscimento emozioni:

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

Questo metodo genera un URI di richiesta e invia la richiesta per il `detect` API tramite il `SendRequestAsync` metodo.

> [!NOTE]
> Nelle chiamate API viso come è stato usato per ottenere le chiavi di sottoscrizione, è necessario utilizzare la stessa area. Ad esempio, se è stato ottenuto le chiavi di sottoscrizione nella `westus` area, l'endpoint di rilevamento viso sarà `https://westus.api.cognitive.microsoft.com/face/v1.0/detect`.

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

Se l'immagine viene fornito tramite un flusso, il metodo compila la richiesta POST eseguendo il wrapping di flusso dell'immagine in un `StreamContent` istanza, che fornisce contenuto HTTP in base a un flusso. In alternativa, se l'immagine viene fornito tramite un URL, il metodo compila la richiesta POST eseguendo il wrapping l'URL in un `StringContent` istanza, che fornisce contenuto HTTP in base a una stringa.

La richiesta POST viene quindi inviata al `detect` API. La risposta è leggere deserializzata e restituita al metodo di chiamata.

Il `detect` API invierà il codice di stato HTTP 200 (OK) in risposta, a condizione che la richiesta sia valida, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste sono presenti nella risposta. Per un elenco delle risposte di errore possibili, vedere [API REST faccia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

### <a name="processing-the-response"></a>L'elaborazione della risposta

Viene restituita la risposta di API in formato JSON. I dati JSON seguenti mostrano un messaggio di risposta con esito positivo tipico che fornisce i dati richiesti dall'applicazione di esempio:

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

Un messaggio di risposta con esito positivo è costituito da una matrice di voci faccia classificate in base alla dimensione del rettangolo faccia in ordine decrescente, mentre una risposta vuota non indica nessuna facce rilevate. Ogni riconosciuto faccia include una serie di attributi faccia facoltativo, che vengono specificati dal `returnFaceAttributes` argomento per il `DetectAsync` metodo.

Nell'applicazione di esempio, la risposta JSON viene deserializzata in una matrice di `Face` oggetti. Quando si interpretano i risultati dall'API viso, emozioni rilevata devono essere interpretate come emozioni con il punteggio più elevato, come i punteggi vengono normalizzati da sommare a uno. Di conseguenza, l'applicazione di esempio visualizza emozioni riconosciuta con il punteggio più elevato per il valore massimo rilevato nell'immagine. Questa operazione viene eseguita con il codice seguente:

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

Nella schermata seguente mostra il risultato del processo di riconoscimento emozioni nell'applicazione di esempio:

![](emotion-recognition-images/emotion-recognition.png "Riconoscimento emozioni")

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come utilizzare l'API viso per riconoscere emozioni, per valutare un'applicazione di xamarin. Forms. L'API viso accetta un'espressione facciale in un'immagine come input e restituisce i dati che include il livello di confidenza in un set di emozioni per ogni carattere tipografico nell'immagine.

## <a name="related-links"></a>Collegamenti correlati

- [API devono affrontare](/azure/cognitive-services/face/overview/).
- [Servizi cognitivi TODO (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Faccia API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
