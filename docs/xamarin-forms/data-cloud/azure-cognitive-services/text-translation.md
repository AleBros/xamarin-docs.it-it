---
title: Traduzione testuale tramite l'API Microsoft Translator
description: L'API Microsoft Translator è utilizzabile per tradurre il riconoscimento vocale e testo tramite un'API REST. Questo articolo illustra come usare l'API traduzione testuale Microsoft Translator per tradurre il testo da una lingua a altra in un'applicazione Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 4f73ea249d29075b0e9e115e86afc971632b7b61
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487503"
---
# <a name="text-translation-using-the-translator-api"></a>Traduzione testuale tramite l'API Microsoft Translator

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_L'API di Microsoft Translator può essere usata per tradurre la voce e il testo tramite un'API REST. Questo articolo illustra come usare Microsoft API Traduzione testuale per tradurre il testo da una lingua a un'altra in un'applicazione Xamarin.Forms._

## <a name="overview"></a>Panoramica di

L'API Microsoft Translator include due componenti:

- Una traduzione di testo l'API REST per tradurre il testo da una lingua nel testo di un altro linguaggio. L'API rileva automaticamente la lingua del testo che è stata inviata prima traduzione.
- Un'API REST di trascrizione vocale da una lingua nel testo di un'altra lingua di traduzione vocale. L'API si integra anche funzionalità di sintesi vocale per pronunciare il testo tradotto nuovamente.

Questo articolo illustra la traduzione del testo da una lingua a un'altra tramite l'API traduzione testuale.

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

Per usare l'API traduzione testuale, è necessario ottenere una chiave API. Per ottenere tale valore in [come iscriversi per l'API traduzione testuale Microsoft Translator](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Per altre informazioni sull'API traduzione testuale Microsoft Translator, vedere [documentazione dell'API Translator testo](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Autenticazione

Ogni richiesta effettuata per l'API traduzione testuale richiede un token di accesso JSON Web Token (JWT), che può essere ottenuto dal servizio token di servizi cognitivi in `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Un token può essere ottenuto eseguendo una richiesta POST al servizio token, che specifica un `Ocp-Apim-Subscription-Key` intestazione che contiene la chiave API come relativo valore.

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

Il token di accesso deve essere specificato in ogni API traduzione testuale chiamare come un `Authorization` intestazione con la stringa di prefisso `Bearer`, come illustrato nell'esempio di codice seguente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Per altre informazioni sul servizio token di servizi cognitivi, vedere [autenticazione Token API](https://docs.microsofttranslator.com/oauth-token.html).

## <a name="performing-text-translation"></a>Esecuzione di traduzione testuale

Traduzione testuale ottenibile tramite una richiesta GET per il `translate` all'API `https://api.microsofttranslator.com/v2/http.svc/translate`. Nell'applicazione di esempio, il `TranslateTextAsync` metodo richiama il processo di traduzione di testo:

```csharp
public async Task<string> TranslateTextAsync(string text)
{
  ...
  string requestUri = GenerateRequestUri(Constants.TextTranslatorEndpoint, text, "en", "de");
  string accessToken = authenticationService.GetAccessToken();
  var response = await SendRequestAsync(requestUri, accessToken);
  var xml = XDocument.Parse(response);
  return xml.Root.Value;
}
```

Il `TranslateTextAsync` metodo genera un URI di richiesta e recupera un token di accesso dal servizio token. La richiesta di traduzione di testo viene quindi inviata per la `translate` API, che restituisce una risposta XML che contiene il risultato. La risposta XML viene analizzata e viene restituito il risultato di traduzione al metodo di chiamata per la visualizzazione.

Per altre informazioni sulle API REST traduzione di testo, vedere [API traduzione testuale Microsoft Translator](https://docs.microsofttranslator.com/text-translate.html).

### <a name="configuring-text-translation"></a>Configurazione di traduzione testuale

Il processo di traduzione di testo può essere configurato specificando i parametri di query HTTP:

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

Questo metodo imposta il testo da tradurre e linguaggio da tradurre il testo. Per un elenco di lingue supportate da Microsoft Translator, vedere [lingue supportate nell'API traduzione testuale Microsoft Translator](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Se un'applicazione deve sapere quale lingua è il testo, il `Detect` API può essere chiamato per rilevare la lingua della stringa di testo.

### <a name="sending-the-request"></a>L'invio della richiesta

Il `SendRequestAsync` metodo effettua la richiesta GET all'API REST di traduzione di testo e restituisce la risposta:

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
    if (httpClient == null)
    {
        httpClient = new HttpClient();
    }
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);

    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Questo metodo crea la richiesta GET, aggiungere il token di accesso per il `Authorization` intestazione, con la stringa di prefisso `Bearer`. La richiesta GET viene quindi inviata per la `translate` API, con l'URL della richiesta che specifica il testo da tradurre e la lingua per tradurre il testo. La risposta viene quindi letto e restituita al metodo di chiamata.

Il `translate` API invierà il codice di stato HTTP 200 (OK) in risposta, fornita che la richiesta sia valida, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste sono presenti nella risposta. Per un elenco di possibili risposte di errore, vedere i messaggi di risposta al [ottenere tradurre](https://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate).

### <a name="processing-the-response"></a>L'elaborazione della risposta

La risposta dell'API viene restituita in formato XML. I dati XML seguenti mostrano un messaggio di risposta con esito positivo tipico:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

Nell'applicazione di esempio, la risposta XML viene convertita in un `XDocument` istanza, con il valore radice XML viene restituito al metodo di chiamata per la visualizzazione, come illustrato negli screenshot seguenti:

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare l'API traduzione testuale Microsoft Translator per tradurre il testo da una lingua nel testo di un'altra lingua in un'applicazione Xamarin.Forms. Oltre alla traduzione del testo, è possibile che l'API di Microsoft Translator trascrivere anche vocale da una lingua nel testo di un altro linguaggio.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione API traduzione testuale Microsoft Translator](/azure/cognitive-services/translator/).
- [Utilizzare un servizio Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Servizi cognitivi TODO (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [API traduzione testuale Microsoft Translator](https://docs.microsofttranslator.com/text-translate.html).
