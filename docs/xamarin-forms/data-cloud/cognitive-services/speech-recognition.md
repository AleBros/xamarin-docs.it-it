---
title: Riconoscimento vocale utilizzando l'API di sintesi vocale Microsoft
description: L'API di riconoscimento vocale Microsoft è un'API basata su cloud che fornisce gli algoritmi per l'elaborazione di lingua parlata. In questo articolo viene illustrato come utilizzare l'API REST di riconoscimento vocale Microsoft per convertire audio in testo in un'applicazione di xamarin. Forms.
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 81e645749d239f8964047e92255e786c9b35409d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>Riconoscimento vocale utilizzando l'API di sintesi vocale Microsoft

_L'API di riconoscimento vocale Microsoft è un'API basata su cloud che fornisce gli algoritmi per l'elaborazione di lingua parlata. In questo articolo viene illustrato come utilizzare l'API REST di riconoscimento vocale Microsoft per convertire audio in testo in un'applicazione di xamarin. Forms._

## <a name="overview"></a>Panoramica

API riconoscimento vocale Microsoft include due componenti:

- Riconoscimento vocale API per la conversione di parole in testo. Il riconoscimento vocale può essere eseguito tramite un'API REST, una libreria client o una libreria di servizi.
- Una sintesi vocale API per la conversione di testo in parole. Conversione di sintesi vocale viene eseguita tramite un'API REST.

In questo articolo è incentrato sull'esecuzione di riconoscimento vocale attraverso l'API REST. Mentre le librerie client e il servizio supportano la restituzione di risultati parziali, l'API REST può restituire solo un risultato di riconoscimento singola, senza eventuali risultati parziali.

Per utilizzare l'API di riconoscimento vocale Microsoft, è necessario ottenere una chiave API. Può essere ottenuto in [provare servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/).

Per ulteriori informazioni sull'API di riconoscimento vocale Microsoft, vedere [documentazione dell'API Microsoft vocale](/azure/cognitive-services/speech/home/).

## <a name="authentication"></a>Autenticazione

Tutte le richieste inviate all'API REST di Microsoft vocale richiede un token di accesso JSON Web Token (JWT), che può essere ottenuto dal servizio token di servizi cognitivi in `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Un token può essere ottenuto tramite una richiesta POST al servizio token di specificando un `Ocp-Apim-Subscription-Key` intestazione che contiene la chiave API come relativo valore.

Esempio di codice seguente viene illustrato come richiedere un accesso token dal servizio token di:

```csharp
public AuthenticationService(string apiKey)
{
    subscriptionKey = apiKey;
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
}
...
async Task<string> FetchTokenAsync(string fetchUri)
{
    UriBuilder uriBuilder = new UriBuilder(fetchUri);
    uriBuilder.Path += "/issueToken";
    var result = await httpClient.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
    return await result.Content.ReadAsStringAsync();
}
```

Il token di accesso restituito, ovvero testo Base64, dispone di un'ora di scadenza di 10 minuti. Pertanto, l'applicazione di esempio rinnova il token di accesso ogni 9 minuti.

Il token di accesso deve essere specificato in ogni API REST di Microsoft vocale chiamare come un `Authorization` intestazione con la stringa di prefisso `Bearer`, come illustrato nell'esempio di codice seguente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Per passare un token di accesso valido per l'API REST di Microsoft vocale vengono rispettate, un errore di 403 risposta.

## <a name="performing-speech-recognition"></a>Esecuzione di riconoscimento vocale

Il riconoscimento vocale viene ottenuto tramite una richiesta POST per il `recognition` API `https://speech.platform.bing.com/speech/recognition/`. Una singola richiesta non può contenere più di 10 secondi di audio e la durata totale delle richieste non può superare 14 secondi.

Contenuto audio deve trovarsi nel corpo della richiesta in formato wav POST.

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
    var speechResult = JsonConvert.DeserializeObject<SpeechResult>(response);

    fileStream.Dispose();
    return speechResult;
}
```

Audio viene registrato in ogni progetto specifico della piattaforma sotto forma di dati wav PCM e `RecognizeSpeechAsync` metodo utilizza il `PCLStorage` pacchetto NuGet per aprire il file audio in un flusso. La richiesta di riconoscimento vocale URI viene generato e un token di accesso viene recuperato dal servizio token di. La richiesta di riconoscimento vocale viene inserita il `recognition` API, che restituisce una risposta JSON contenente il risultato. La risposta JSON viene deserializzata, con il risultato viene restituito al metodo di chiamata per la visualizzazione.

### <a name="configuring-speech-recognition"></a>Configurazione del riconoscimento vocale

È possibile configurare il processo di riconoscimento vocale specificando i parametri di query HTTP:

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    // To build a request URL, you should follow:
    // https://docs.microsoft.com/en-us/azure/cognitive-services/speech/getstarted/getstartedrest
    string requestUri = speechEndpoint;
    requestUri += @"dictation/cognitiveservices/v1?";
    requestUri += @"language=en-us";
    requestUri += @"&format=simple";
    System.Diagnostics.Debug.WriteLine(requestUri.ToString());
    return requestUri;
}
```

La configurazione principale eseguita dal `GenerateRequestUri` metodo consiste nell'impostare le impostazioni locali del contenuto audio. Per un elenco delle impostazioni locali supportate, vedere [lingue supportate ](/azure/cognitive-services/speech/api-reference-rest/supportedlanguages/).

### <a name="sending-the-request"></a>L'invio della richiesta

Il `SendRequestAsync` metodo effettua la richiesta POST all'API REST di Microsoft vocale e restituisce la risposta:

```csharp
async Task<string> SendRequestAsync(Stream fileStream, string url, string bearerToken, string contentType)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var content = new StreamContent(fileStream);
    content.Headers.TryAddWithoutValidation("Content-Type", contentType);
    var response = await httpClient.PostAsync(url, content);
    return await response.Content.ReadAsStringAsync();
}
```

Questo metodo crea la richiesta POST per:

- Racchiude il flusso audio in un `StreamContent` istanza, che fornisce contenuto HTTP in base a un flusso.
- L'impostazione di `Content-Type` intestazione della richiesta da `audio/wav; codec="audio/pcm"; samplerate=16000`.
- Il token di accesso per l'aggiunta di `Authorization` intestazione, con la stringa di prefisso `Bearer`.

La richiesta POST viene quindi inviata al `recognition` API. La risposta viene quindi letto e restituita al metodo di chiamata.

Il `recognition` API invierà il codice di stato HTTP 200 (OK) in risposta, a condizione che la richiesta sia valida, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste sono presenti nella risposta. Per un elenco delle risposte di errore possibili, vedere [Troubleshooting](/azure/cognitive-services/speech/troubleshooting).

### <a name="processing-the-response"></a>L'elaborazione della risposta

Viene restituita la risposta di API in formato JSON, con il testo riconosciuto sia indipendente nel `name` tag. I dati JSON seguenti mostrano un messaggio di risposta con esito positivo tipico:

```json
{  
   "RecognitionStatus":"Success",
   "DisplayText":"Go shopping tomorrow.",
   "Offset":16000000,
   "Duration":17100000
}
```

Nell'applicazione di esempio, la risposta JSON viene deserializzata in un `SpeechResult` istanza, con il risultato viene restituito al metodo di chiamata per la visualizzazione, come illustrato nelle schermate seguenti:

![](speech-recognition-images/speech-recognition.png "Riconoscimento vocale")

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come utilizzare l'API REST di Microsoft vocale per convertire audio in testo in un'applicazione di xamarin. Forms. Oltre a eseguire il riconoscimento vocale, l'API di riconoscimento vocale Microsoft può anche convertire testo in parole.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di riconoscimento vocale Microsoft API](/azure/cognitive-services/speech/home/).
- [Utilizzo di un servizio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Servizi cognitivi TODO (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
