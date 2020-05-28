---
title: ''
description: L'API di Microsoft Translator può essere usata per tradurre la voce e il testo tramite un'API REST. Questo articolo illustra come usare Microsoft API Traduzione testuale per tradurre il testo da una lingua a un'altra in un' Xamarin.Forms applicazione.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f0f43f8f2113b6bd0a800ed3e0bd96b641575b1c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139282"
---
# <a name="text-translation-using-the-translator-api"></a>Traduzione testuale con l'API Translator

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_L'API di Microsoft Translator può essere usata per tradurre la voce e il testo tramite un'API REST. Questo articolo illustra come usare Microsoft API Traduzione testuale per tradurre il testo da una lingua a un'altra in un' Xamarin.Forms applicazione._

## <a name="overview"></a>Panoramica

L'API Translator include due componenti:

- API REST per la traduzione di testo per tradurre il testo da una lingua a un'altra lingua. L'API rileva automaticamente la lingua del testo inviato prima della traduzione.
- Un'API REST di traduzione vocale per trascrivere il riconoscimento vocale da un linguaggio a un altro. L'API integra anche funzionalità di sintesi vocale per riconvertire in audio il testo tradotto.

Questo articolo è incentrato sulla conversione di testo da una lingua a un'altra usando il API Traduzione testuale.

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

Per usare la API Traduzione testuale, è necessario ottenere una chiave API. Questa operazione può essere ottenuta in [come iscriversi a Microsoft API traduzione testuale](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Per ulteriori informazioni su Microsoft API Traduzione testuale, vedere [API traduzione testuale documentazione](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Authentication

Ogni richiesta effettuata all'API Traduzione testuale richiede un token di accesso JWT (JSON Web Token), che può essere ottenuto dal servizio token di servizi cognitivi all'indirizzo `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` . È possibile ottenere un token effettuando una richiesta POST al servizio token, specificando un' `Ocp-Apim-Subscription-Key` intestazione che contiene la chiave API come valore.

Nell'esempio di codice seguente viene illustrato come richiedere un token di accesso dal servizio token:

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

Il token di accesso restituito, che è un testo Base64, ha una scadenza di 10 minuti. Pertanto, l'applicazione di esempio rinnova il token di accesso ogni 9 minuti.

Il token di accesso deve essere specificato in ogni chiamata di API Traduzione testuale come un' `Authorization` intestazione preceduta dalla stringa `Bearer` , come illustrato nell'esempio di codice seguente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Per ulteriori informazioni sul servizio token di servizi cognitivi, vedere [Authentication](/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="performing-text-translation"></a>Esecuzione della traduzione del testo

La traduzione del testo può essere eseguita effettuando una richiesta GET all' `translate` API in `https://api.microsofttranslator.com/v2/http.svc/translate` . Nell'applicazione di esempio, il `TranslateTextAsync` metodo richiama il processo di traduzione del testo:

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

Il `TranslateTextAsync` metodo genera un URI di richiesta e recupera un token di accesso dal servizio token. La richiesta di traduzione testuale viene quindi inviata all' `translate` API, che restituisce una risposta XML contenente il risultato. La risposta XML viene analizzata e il risultato della conversione viene restituito al metodo chiamante per la visualizzazione.

Per ulteriori informazioni sulle API REST per la traduzione testuale, vedere [API traduzione testuale](/azure/cognitive-services/translator/reference/v3-0-reference).

### <a name="configuring-text-translation"></a>Configurazione della traduzione del testo

Il processo di traduzione del testo può essere configurato specificando i parametri di query HTTP:

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

Questo metodo imposta il testo da tradurre e la lingua in cui tradurre il testo. Per un elenco delle lingue supportate da Microsoft Translator, vedere [le lingue supportate nella API traduzione testuale Microsoft](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Se un'applicazione deve conoscere la lingua in cui si trova il testo, è `Detect` possibile chiamare l'API per rilevare la lingua della stringa di testo.

### <a name="sending-the-request"></a>Invio della richiesta

Il `SendRequestAsync` metodo effettua la richiesta GET all'API REST per la traduzione di testo e restituisce la risposta:

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

Questo metodo compila la richiesta GET aggiungendo il token di accesso all' `Authorization` intestazione, preceduta dalla stringa `Bearer` . La richiesta GET viene quindi inviata all' `translate` API, con l'URL della richiesta che specifica il testo da tradurre e la lingua in cui tradurre il testo. La risposta viene quindi letta e restituita al metodo chiamante.

L'API invierà il `translate` codice di stato HTTP 200 (OK) nella risposta, purché la richiesta sia valida, a indicare che la richiesta è riuscita e che le informazioni richieste sono nella risposta. Per un elenco di possibili risposte di errore, vedere messaggi di risposta in [Get Translate](/azure/cognitive-services/translator/reference/v3-0-translate).

### <a name="processing-the-response"></a>Elaborazione della risposta

La risposta dell'API viene restituita in formato XML. I dati XML seguenti mostrano un tipico messaggio di risposta riuscito:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

Nell'applicazione di esempio, la risposta XML viene analizzata in un' `XDocument` istanza di, con il valore radice XML restituito al metodo chiamante per la visualizzazione, come illustrato nelle schermate seguenti:

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare Microsoft API Traduzione testuale per tradurre il testo da una lingua a un'altra in un altro linguaggio in un' Xamarin.Forms applicazione. Oltre a tradurre il testo, l'API di Microsoft Translator può anche trascrivere il riconoscimento vocale da un linguaggio a un altro linguaggio.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione dell'API Traduzione testuale](/azure/cognitive-services/translator/)
- [Utilizzare un servizio Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Servizi cognitivi todo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [API Traduzione testuale](/azure/cognitive-services/translator/reference/v3-0-reference)
