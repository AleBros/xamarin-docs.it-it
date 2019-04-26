---
title: Riconoscimento vocale usando l'API riconoscimento vocale di Microsoft
description: L'API traduzione vocale Microsoft è un'API basata sul cloud che fornisce algoritmi per elaborare il linguaggio parlato. Questo articolo illustra come usare l'API REST di riconoscimento vocale Microsoft per convertire audio in testo in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: B435FF6B-8785-48D9-B2D9-1893F5A87EA1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: dbcb86c6076d717966639a8c6117bc59c17e86bf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330231"
---
# <a name="speech-recognition-using-the-microsoft-speech-api"></a>Riconoscimento vocale usando l'API riconoscimento vocale di Microsoft

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_L'API traduzione vocale Microsoft è un'API basata sul cloud che fornisce algoritmi per elaborare il linguaggio parlato. Questo articolo illustra come usare l'API REST di riconoscimento vocale Microsoft per convertire audio in testo in un'applicazione xamarin. Forms._

## <a name="overview"></a>Panoramica

L'API traduzione vocale Microsoft presenta due componenti:

- Un'API di riconoscimento vocale per la conversione vocali in testo. Riconoscimento vocale può essere eseguito tramite un'API REST, libreria client o della libreria di servizi.
- Un'API sintesi vocale per la conversione del testo in parole. Sintesi vocale viene eseguita tramite l'API REST.

Questo articolo è incentrato su come eseguire il riconoscimento vocale tramite l'API REST. Mentre le librerie client e il servizio supportano la restituzione dei risultati parziali, l'API REST può restituire solo un risultato del riconoscimento singola, senza eventuali risultati parziali.

Per usare l'API traduzione vocale Microsoft, è necessario ottenere una chiave API. Ciò può essere ottenuto da Azure [portale](https://portal.azure.com/). Per altre informazioni, vedere [creare un account servizi cognitivi nel portale di Azure](/azure/cognitive-services/cognitive-services-apis-create-account).

Per altre informazioni sull'API riconoscimento vocale di Microsoft, vedere [documentazione dell'API riconoscimento vocale Microsoft](/azure/cognitive-services/speech/home/).

## <a name="authentication"></a>Autenticazione

Tutte le richieste inviate all'API REST di riconoscimento vocale di Microsoft richiede un token di accesso JSON Web Token (JWT), che può essere ottenuto dal servizio token di servizi cognitivi in `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Un token può essere ottenuto eseguendo una richiesta POST al servizio token, che specifica un `Ocp-Apim-Subscription-Key` intestazione che contiene la chiave API come relativo valore.

Esempio di codice seguente viene illustrato come richiedere l'accesso in un token dal servizio token:

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

Il token di accesso restituito, ovvero testo Base64, contiene un'ora di scadenza di 10 minuti. Pertanto, l'applicazione di esempio rinnova il token di accesso ogni 9 minuti.

Il token di accesso deve essere specificato in ogni REST API traduzione vocale Microsoft chiamare come un `Authorization` intestazione con la stringa di prefisso `Bearer`, come illustrato nell'esempio di codice seguente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Errore per passare un token di accesso valido per l'API REST di riconoscimento vocale Microsoft comporterà un errore di 403 risposta.

## <a name="performing-speech-recognition"></a>Esegue il riconoscimento vocale

Riconoscimento vocale viene ottenuto effettuando una richiesta POST per il `recognition` all'API `https://speech.platform.bing.com/speech/recognition/`. Una singola richiesta non può contenere più di 10 secondi di audio e la durata delle richieste totale non può superare i 14 secondi.

Contenuto audio deve essere inserito nel corpo del POST della richiesta in formato wav.

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

L'audio viene registrato in ogni progetto specifico della piattaforma dati wav PCM e il `RecognizeSpeechAsync` metodo viene utilizzato il `PCLStorage` pacchetto NuGet per aprire il file come flusso audio. La richiesta di riconoscimento vocale URI viene generato e un token di accesso viene recuperato dal servizio token. La richiesta di riconoscimento vocale viene registrata la `recognition` API, che restituisce una risposta JSON che contiene il risultato. La risposta JSON viene deserializzata, con il risultato viene restituito al metodo di chiamata per la visualizzazione.

### <a name="configuring-speech-recognition"></a>Configurazione del riconoscimento vocale

Il processo di riconoscimento vocale può essere configurato specificando i parametri di query HTTP:

```csharp
string GenerateRequestUri(string speechEndpoint)
{
    // To build a request URL, you should follow:
    // https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest
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

Il `SendRequestAsync` metodo effettua la richiesta POST all'API REST di riconoscimento vocale di Microsoft e restituisce la risposta:

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

- Racchiude il flusso audio in un `StreamContent` istanza che fornisce contenuto HTTP in base a un flusso.
- Impostando il `Content-Type` intestazione della richiesta di `audio/wav; codec="audio/pcm"; samplerate=16000`.
- Aggiungere il token di accesso per il `Authorization` intestazione, con la stringa di prefisso `Bearer`.

La richiesta POST viene quindi inviata a `recognition` API. La risposta viene quindi letto e restituita al metodo di chiamata.

Il `recognition` API invierà il codice di stato HTTP 200 (OK) in risposta, fornita che la richiesta sia valida, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste sono presenti nella risposta. Per un elenco di possibili risposte di errore, vedere [Troubleshooting](/azure/cognitive-services/speech/troubleshooting).

### <a name="processing-the-response"></a>L'elaborazione della risposta

La risposta dell'API viene restituita in formato JSON, con il testo riconosciuto sia indipendente nel `name` tag. I dati JSON seguenti mostrano un messaggio di risposta con esito positivo tipico:

```json
{  
   "RecognitionStatus":"Success",
   "DisplayText":"Go shopping tomorrow.",
   "Offset":16000000,
   "Duration":17100000
}
```

Nell'applicazione di esempio, la risposta JSON viene deserializzata in un `SpeechResult` istanza, con il risultato viene restituito al metodo di chiamata per la visualizzazione, come illustrato negli screenshot seguenti:

![](speech-recognition-images/speech-recognition.png "Riconoscimento vocale")

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare l'API REST di riconoscimento vocale di Microsoft per convertire audio in testo in un'applicazione xamarin. Forms. Oltre a eseguire il riconoscimento vocale, l'API traduzione vocale Microsoft può anche convertire testo in parole.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione API traduzione vocale Microsoft](/azure/cognitive-services/speech/home/).
- [Utilizzo di un servizio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Servizi cognitivi TODO (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
