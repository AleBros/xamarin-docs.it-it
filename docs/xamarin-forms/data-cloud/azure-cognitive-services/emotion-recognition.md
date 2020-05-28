---
title: ''
description: Il API Viso accetta un'espressione facciale in un'immagine come input e restituisce dati che includono livelli di confidenza in un set di emozioni per ogni viso nell'immagine. Questo articolo illustra come usare la API Viso per riconoscere le emozioni, per valutare un' Xamarin.Forms applicazione.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ff384605b35f6406b628da99de500b550da811c9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136058"
---
# <a name="perceived-emotion-recognition-using-the-face-api"></a>Riconoscimento delle emozioni percepito usando il API Viso

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

Il API Viso è in grado di eseguire il rilevamento delle emozioni per rilevare rabbia, distemperanza, disgusto, paura, felicità, neutralità, tristezza e sorpresa, in un'espressione facciale basata su annotazioni percepite da parte dei codificatori umani. È importante notare, tuttavia, che solo le espressioni facciali potrebbero non rappresentare necessariamente gli Stati interni delle persone.

Oltre a restituire un risultato emotivo per un'espressione facciale, il API Viso può anche restituire un rettangolo di delimitazione per i visi rilevati.

Il riconoscimento delle emozioni può essere eseguito tramite una libreria client e tramite un'API REST. Questo articolo è incentrato sull'esecuzione del riconoscimento delle emozioni tramite l'API REST. Per altre informazioni sull'API REST, vedere [API REST del viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Il API Viso può essere usato anche per riconoscere le espressioni facciali delle persone in video e può restituire un riepilogo delle emozioni. Per ulteriori informazioni, vedere [come analizzare i video in tempo reale](/azure/cognitive-services/face/face-api-how-to-topics/howtoanalyzevideo_face/).

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

Per usare la API Viso, è necessario ottenere una chiave API. Questa operazione può essere ottenuta in [prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

Per ulteriori informazioni sulla API Viso, vedere [API viso](/azure/cognitive-services/face/overview/).

## <a name="authentication"></a>Authentication

Ogni richiesta effettuata al API Viso richiede una chiave API da specificare come valore dell' `Ocp-Apim-Subscription-Key` intestazione. Nell'esempio di codice seguente viene illustrato come aggiungere la chiave API all' `Ocp-Apim-Subscription-Key` intestazione di una richiesta:

```csharp
public FaceRecognitionService()
{
  _client = new HttpClient();
  _client.DefaultRequestHeaders.Add("ocp-apim-subscription-key", Constants.FaceApiKey);
}
```

Se non si passa una chiave API valida al API Viso, verrà generato un errore di risposta 401.

## <a name="perform-emotion-recognition"></a>Eseguire il riconoscimento delle emozioni

Il riconoscimento delle emozioni viene eseguito effettuando una richiesta POST contenente un'immagine all' `detect` API in `https://[location].api.cognitive.microsoft.com/face/v1.0` , dove `[location]]` è l'area usata per ottenere la chiave API. I parametri facoltativi della richiesta sono:

- `returnFaceId`: indica se restituire faceIds dei visi rilevati. Il valore predefinito è `true`.
- `returnFaceLandmarks`: indica se restituire i punti di riferimento dei visi rilevati. Il valore predefinito è `false`.
- `returnFaceAttributes`: se analizzare e restituire uno o più attributi visi specificati. Gli attributi visi supportati sono,,, `age` `gender` `headPose` `smile` , `facialHair` , `glasses` , `emotion` , `hair` , `makeup` , `occlusion` , `accessories` , `blur` , `exposure` e `noise` . Si noti che l'analisi degli attributi viso presenta costi di calcolo e di tempo aggiuntivi.

Il contenuto dell'immagine deve essere inserito nel corpo della richiesta POST come URL o come dati binari.

> [!NOTE]
> I formati di file di immagine supportati sono JPEG, PNG, GIF e BMP e le dimensioni dei file consentiti sono da 1 KB a 4 MB.

Nell'applicazione di esempio, il processo di riconoscimento delle emozioni viene richiamato chiamando il `DetectAsync` Metodo:

```csharp
Face[] faces = await _faceRecognitionService.DetectAsync(photoStream, true, false, new FaceAttributeType[] { FaceAttributeType.Emotion });
```

Questa chiamata al metodo specifica il flusso che contiene i dati dell'immagine, che devono essere restituiti faceIds, che non devono essere restituiti i punti di riferimento della faccia e che l'emozione dell'immagine deve essere analizzata. Specifica inoltre che i risultati verranno restituiti come matrice di `Face` oggetti. A sua volta, il `DetectAsync` metodo richiama l' `detect` API REST che esegue il riconoscimento delle emozioni:

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

Questo metodo genera un URI di richiesta e quindi Invia la richiesta all' `detect` API tramite il `SendRequestAsync` metodo.

> [!NOTE]
> Per ottenere le chiavi di sottoscrizione, è necessario usare la stessa area nel API Viso le chiamate utilizzate. Ad esempio, se sono state ottenute le chiavi di sottoscrizione dall' `westus` area, l'endpoint di rilevamento viso sarà `https://westus.api.cognitive.microsoft.com/face/v1.0/detect` .

### <a name="send-the-request"></a>Inviare la richiesta

Il `SendRequestAsync` metodo effettua la richiesta post al API viso e restituisce il risultato sotto forma di `Face` matrice:

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

Se l'immagine viene fornita tramite un flusso, il metodo compila la richiesta POST eseguendo il wrapping del flusso dell'immagine in un' `StreamContent` istanza, che fornisce il contenuto HTTP basato su un flusso. In alternativa, se l'immagine viene fornita tramite un URL, il metodo compila la richiesta POST eseguendo il wrapping dell'URL in un' `StringContent` istanza, che fornisce il contenuto HTTP in base a una stringa.

La richiesta POST viene quindi inviata all' `detect` API. La risposta viene letta, deserializzata e restituita al metodo chiamante.

L'API invierà il `detect` codice di stato HTTP 200 (OK) nella risposta, purché la richiesta sia valida, a indicare che la richiesta è riuscita e che le informazioni richieste sono nella risposta. Per un elenco di possibili risposte di errore, vedere [interfaccia API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

### <a name="process-the-response"></a>Elaborare la risposta

La risposta dell'API viene restituita in formato JSON. I dati JSON seguenti mostrano un tipico messaggio di risposta con esito positivo che fornisce i dati richiesti dall'applicazione di esempio:

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

Un messaggio di risposta con esito positivo è costituito da una matrice di voci del volto classificate in ordine decrescente in base al rettangolo della faccia, mentre una risposta vuota indica che non è stato rilevato alcun visi Ogni face riconosciuto include una serie di attributi della faccia facoltativi, specificati dall' `returnFaceAttributes` argomento al `DetectAsync` metodo.

Nell'applicazione di esempio, la risposta JSON viene deserializzata in una matrice di `Face` oggetti. Quando si interpretano i risultati dalla API Viso, l'emozione rilevata deve essere interpretata come l'emozione con il punteggio più alto, perché i punteggi vengono normalizzati in modo da essere sommati a uno. Pertanto, l'applicazione di esempio Visualizza l'emozione riconosciuta con il punteggio più alto per il quadrante più grande rilevato nell'immagine. Questo risultato si ottiene con il codice seguente:

```csharp
emotionResultLabel.Text = faces.FirstOrDefault().FaceAttributes.Emotion.ToRankedList().FirstOrDefault().Key;
```

La schermata seguente mostra il risultato del processo di riconoscimento delle emozioni nell'applicazione di esempio:

![](emotion-recognition-images/emotion-recognition.png "Emotion Recognition")

## <a name="related-links"></a>Collegamenti correlati

- [API viso](/azure/cognitive-services/face/overview/).
- [Servizi cognitivi todo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [API REST viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
