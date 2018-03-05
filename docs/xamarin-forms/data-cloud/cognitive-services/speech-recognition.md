---
title: Riconoscimento vocale utilizzando l'API di sintesi vocale Bing
description: "L'API di riconoscimento vocale Bing è un'API basata su cloud che fornisce gli algoritmi per l'elaborazione di lingua parlata. In questo articolo viene illustrato come utilizzare l'API REST di riconoscimento vocale Bing per convertire l'audio in testo in un'applicazione di xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 186ea6277ec7bd4ceb52855186e6fd88344b1b86
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="speech-recognition-using-the-bing-speech-api"></a>Riconoscimento vocale utilizzando l'API di sintesi vocale Bing

_L'API di riconoscimento vocale Bing è un'API basata su cloud che fornisce gli algoritmi per l'elaborazione di lingua parlata. In questo articolo viene illustrato come utilizzare l'API REST di riconoscimento vocale Bing per convertire l'audio in testo in un'applicazione di xamarin. Forms._

![](~/media/shared/preview.png "Questa API è attualmente pre-release.")

> [!NOTE]
> L'API di riconoscimento vocale Bing è ancora in anteprima. Potrebbe essere ultime modifiche all'API prima della versione finale.

## <a name="overview"></a>Panoramica

L'API di riconoscimento vocale Bing presenta due componenti:

- Riconoscimento vocale API per la conversione di parole in testo. Il riconoscimento vocale può essere eseguito tramite un'API REST, una libreria client o una libreria di servizi.
- Una sintesi vocale API per la conversione di testo in parole. Conversione di sintesi vocale viene eseguita tramite un'API REST.

In questo articolo è incentrato sull'esecuzione di riconoscimento vocale attraverso l'API REST. Mentre le librerie client e il servizio supportano la restituzione di risultati parziali, l'API REST può restituire solo un risultato di riconoscimento singola, senza eventuali risultati parziali.

Per utilizzare l'API di riconoscimento vocale Bing, è necessario ottenere una chiave API. Può essere ottenuto in [introduzione gratuitamente](https://www.microsoft.com/cognitive-services/sign-up?ReturnUrl=/cognitive-services/subscriptions?productId=%2fproducts%2fBing.Speech.Preview) sul sito Web microsoft.com.

Per ulteriori informazioni sull'API di riconoscimento vocale Bing, vedere [documentazione vocale Bing](https://www.microsoft.com/cognitive-services/Speech-api/documentation/overview) sul sito Web microsoft.com.

## <a name="authentication"></a>Autenticazione

Ogni richiesta effettuata all'API REST di riconoscimento vocale Bing richiede un token di accesso JSON Web Token (JWT), che può essere ottenuto dal servizio token di servizi cognitivi in `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Un token può essere ottenuto tramite una richiesta POST al servizio token di specificando un `Ocp-Apim-Subscription-Key` intestazione che contiene la chiave API come relativo valore.

Esempio di codice seguente viene illustrato come richiedere un accesso token dal servizio token di:

```csharp
async Task<string> FetchTokenAsync(string fetchUri, string apiKey)
{
  using (var client = new HttpClient())
  {
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";

    var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
  }
}
```

Il token di accesso restituito, ovvero testo Base64, dispone di un'ora di scadenza di 10 minuti. Pertanto, l'applicazione di esempio rinnova il token di accesso ogni 9 minuti.

Il token di accesso deve essere specificato in ogni API REST di riconoscimento vocale Bing chiamare come un `Authorization` intestazione con la stringa di prefisso `Bearer`, come illustrato nell'esempio di codice seguente:

```csharp
using (var httpClient = new HttpClient())
{
  httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
  ...
}  
```

Il mancato superamento di un token di accesso valido per l'API REST di riconoscimento vocale Bing comporterà un errore di 403 risposta.

## <a name="performing-speech-recognition"></a>Esecuzione di riconoscimento vocale

Il riconoscimento vocale viene ottenuto tramite una richiesta POST per `recognize` API `https://speech.platform.bing.com/recognize`. Una singola richiesta non può contenere più di 10 secondi di audio e la durata totale delle richieste non può superare 14 secondi.

Contenuto audio deve trovarsi nel corpo della richiesta in formato wav POST. Per informazioni sui codec supportati, vedere [codec supportati da](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#supported-codecs) sul sito Web microsoft.com.

Nell'applicazione di esempio, il `RecognizeSpeechAsync` metodo richiama il processo di riconoscimento vocale:

```csharp
public async Task<SpeechResult> RecognizeSpeechAsync(string filename)
{
    ...

    // Read audio file to a stream
    var file = await PCLStorage.FileSystem.Current.LocalStorage.GetFileAsync(filename);
    var fileStream = await file.OpenAsync(PCLStorage.FileAccess.Read);

    // Send audio stream to Bing and deserialize the response
    string requestUri = GenerateRequestUri(Constants.SpeechRecognitionEndpoint);
    string accessToken = authenticationService.GetAccessToken();
    var response = await SendRequestAsync(fileStream, requestUri, accessToken, Constants.AudioContentType);
    var speechResults = JsonConvert.DeserializeObject<SpeechResults>(response);

    fileStream.Dispose();
    return speechResults.results.FirstOrDefault();
}
```

Audio viene registrato in ogni progetto specifico della piattaforma sotto forma di dati wav PCM e `RecognizeSpeechAsync` metodo utilizza il `PCLStorage` pacchetto NuGet per aprire il file audio in un flusso. La richiesta di riconoscimento vocale URI viene generato e un token di accesso viene recuperato dal servizio token di. La richiesta di riconoscimento vocale viene inserita il `recognize` API, che restituisce una risposta JSON contenente il risultato. La risposta JSON viene deserializzata, con il risultato viene restituito al metodo di chiamata per la visualizzazione.

### <a name="configuring-speech-recognition"></a>Configurazione del riconoscimento vocale

Il processo di riconoscimento vocale può essere configurato specificando i parametri di query HTTP. Sono previsti parametri obbligatori e facoltativi, con il metodo seguente che mostra i parametri obbligatori che devono essere impostati:

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    string requestUri = speechEndpoint;
    requestUri += @"?scenarios=ulm";                                    // websearch is the other option
    requestUri += @"&appid=D4D52672-91D7-4C74-8AD8-42B1D98141A5";       // You must use this ID
    requestUri += @"&locale=en-US";                                     // Other languages supported
    requestUri += string.Format("&device.os={0}", operatingSystem);     // Open field
    requestUri += @"&version=3.0";                                      // Required value
    requestUri += @"&format=json";                                      // Required value
    requestUri += @"&instanceid=fe34a4de-7927-4e24-be60-f0629ce1d808";  // GUID for device making the request
    requestUri += @"&requestid=" + Guid.NewGuid().ToString();           // GUID for the request
    return requestUri;
}
```

La configurazione principale eseguita dal `GenerateRequestUri` metodo consiste nell'impostare le impostazioni locali del contenuto audio. Per un elenco delle impostazioni locali supportate, vedere [impostazioni locali supportate ](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#supported-locales) sul sito Web microsoft.com.

Per ulteriori informazioni sui possibili valori per i parametri obbligatori, vedere [parametri obbligatori](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#required-parameters) sul sito Web microsoft.com. Per informazioni sui parametri facoltativi, vedere [parametri facoltativi](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition) sul sito Web microsoft.com.

### <a name="sending-the-request"></a>L'invio della richiesta

Il `SendRequestAsync` metodo effettua la richiesta POST all'API REST di riconoscimento vocale Bing e restituisce la risposta:

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);

    using (var httpClient = new HttpClient())
    {
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
        var response = await httpClient.PostAsync(url, content);

        return await response.Content.ReadAsStringAsync();
    }
}
```

Questo metodo crea la richiesta POST per:

- Racchiude il flusso audio in un `StreamContent` istanza, che fornisce contenuto HTTP in base a un flusso.
- L'impostazione di `Content-Type` intestazione della richiesta da `audio/wav; codec="audio/pcm"; samplerate=16000`.
- Il token di accesso per l'aggiunta di `Authorization` intestazione, con la stringa di prefisso `Bearer`.

La richiesta POST viene quindi inviata al `recognize` API. La risposta viene quindi letto e restituita al metodo di chiamata.

Il `recognize` API invierà il codice di stato HTTP 200 (OK) in risposta, a condizione che la richiesta sia valida, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste sono presenti nella risposta. Per un elenco di possibili risposte d'errore, vedere [risposte di errore](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#error-responses) sul sito Web microsoft.com.

### <a name="processing-the-response"></a>L'elaborazione della risposta

Viene restituita la risposta di API in formato JSON, con il testo riconosciuto sia indipendente nel `name` tag. I dati JSON seguenti mostrano un messaggio di risposta con esito positivo tipico:

```csharp
{
  "version": "3.0",
  "header": {
    "status": "success",
    "scenario": "ulm",
    "name": "go shopping tomorrow",
    "lexical": "go shopping tomorrow",
    "properties": {
      "requestid": "e06c059d-fa37-4bb1-843f-4914350279a8",
      "HIGHCONF": "1"
    }
  },
  "results": [
    {
      "scenario": "ulm",
      "name": "go shopping tomorrow",
      "lexical": "go shopping tomorrow",
      "confidence": "0.9493451",
      "properties": {
        "HIGHCONF": "1"
      }
    }
  ]
}
```

Nell'applicazione di esempio, la risposta JSON viene deserializzata in un `SpeechResult` istanza, con il risultato viene restituito al metodo di chiamata per la visualizzazione, come illustrato nelle schermate seguenti:

![](speech-recognition-images/speech-recognition.png "Riconoscimento vocale")

Per informazioni sui valori dei singoli tag JSON, vedere [le risposte di riconoscimento vocale](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition#speech-recognition-responses) sul sito Web microsoft.com.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare l'API REST di riconoscimento vocale Bing per convertire l'audio in testo in un'applicazione di xamarin. Forms. Oltre a eseguire il riconoscimento vocale, l'API di riconoscimento vocale Bing anche convertire testo in parole.



## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di riconoscimento vocale Bing](https://www.microsoft.com/cognitive-services/Speech-api/documentation/overview)
- [Utilizzo di un servizio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Servizi cognitivi TODO (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Riconoscimento vocale Bing API REST](https://www.microsoft.com/cognitive-services/Speech-api/documentation/API-Reference-REST/BingVoiceRecognition)
