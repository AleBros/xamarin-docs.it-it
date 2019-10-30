---
title: Traduzione testuale con l'API Translator
description: L'API di Microsoft Translator può essere usata per tradurre la voce e il testo tramite un'API REST. Questo articolo illustra come usare Microsoft API Traduzione testuale per tradurre il testo da una lingua a un'altra in un'applicazione Novell. Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 50d13532585e6edc3dac530558937ee6e0a02268
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032802"
---
# <a name="text-translation-using-the-translator-api"></a>Traduzione testuale con l'API Translator

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_L'API di Microsoft Translator può essere usata per tradurre la voce e il testo tramite un'API REST. Questo articolo illustra come usare Microsoft API Traduzione testuale per tradurre il testo da una lingua a un'altra in un'applicazione Novell. Forms._

## <a name="overview"></a>Panoramica

L'API Translator include due componenti:

- API REST per la traduzione di testo per tradurre il testo da una lingua a un'altra lingua. L'API rileva automaticamente la lingua del testo inviato prima della traduzione.
- Un'API REST di traduzione vocale per trascrivere il riconoscimento vocale da un linguaggio a un altro. L'API integra anche le funzionalità di sintesi vocale per riportare il testo tradotto.

Questo articolo è incentrato sulla conversione di testo da una lingua a un'altra usando il API Traduzione testuale.

Per usare la API Traduzione testuale, è necessario ottenere una chiave API. Questa operazione può essere ottenuta in [come iscriversi a Microsoft API traduzione testuale](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Per ulteriori informazioni su Microsoft API Traduzione testuale, vedere [API traduzione testuale documentazione](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Authentication

Ogni richiesta effettuata al API Traduzione testuale richiede un token di accesso JWT (JSON Web Token), che può essere ottenuto dal servizio token di servizi cognitivi in `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. È possibile ottenere un token effettuando una richiesta POST al servizio token, specificando un'intestazione `Ocp-Apim-Subscription-Key` che contiene la chiave API come valore.

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

Il token di accesso deve essere specificato in ogni chiamata API Traduzione testuale come intestazione di `Authorization` preceduta dalla stringa `Bearer`, come illustrato nell'esempio di codice seguente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Per altre informazioni sul servizio token di servizi cognitivi, vedere [API del token di autenticazione](https://docs.microsofttranslator.com/oauth-token.html).

## <a name="performing-text-translation"></a>Esecuzione della traduzione del testo

La traduzione del testo può essere eseguita effettuando una richiesta GET all'API `translate` in `https://api.microsofttranslator.com/v2/http.svc/translate`. Nell'applicazione di esempio, il metodo `TranslateTextAsync` richiama il processo di traduzione del testo:

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

Il metodo `TranslateTextAsync` genera un URI di richiesta e recupera un token di accesso dal servizio token. La richiesta di traduzione testuale viene quindi inviata all'API `translate`, che restituisce una risposta XML contenente il risultato. La risposta XML viene analizzata e il risultato della conversione viene restituito al metodo chiamante per la visualizzazione.

Per ulteriori informazioni sulle API REST per la traduzione testuale, vedere [Microsoft API traduzione testuale](https://docs.microsofttranslator.com/text-translate.html).

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
> Se un'applicazione deve conoscere la lingua in cui si trova il testo, è possibile chiamare l'API `Detect` per rilevare la lingua della stringa di testo.

### <a name="sending-the-request"></a>Invio della richiesta

Il metodo `SendRequestAsync` effettua la richiesta GET all'API REST per la traduzione di testo e restituisce la risposta:

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

Questo metodo compila la richiesta GET aggiungendo il token di accesso all'intestazione `Authorization`, preceduta dalla stringa `Bearer`. La richiesta GET viene quindi inviata all'API di `translate`, con l'URL della richiesta che specifica il testo da tradurre e la lingua in cui tradurre il testo. La risposta viene quindi letta e restituita al metodo chiamante.

L'API `translate` invierà il codice di stato HTTP 200 (OK) nella risposta, purché la richiesta sia valida, a indicare che la richiesta è riuscita e che le informazioni richieste sono nella risposta. Per un elenco di possibili risposte di errore, vedere messaggi di risposta in [Get Translate](https://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate).

### <a name="processing-the-response"></a>Elaborazione della risposta

La risposta dell'API viene restituita in formato XML. I dati XML seguenti mostrano un tipico messaggio di risposta riuscito:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

Nell'applicazione di esempio, la risposta XML viene analizzata in un'istanza di `XDocument`, con il valore radice XML restituito al metodo chiamante per la visualizzazione, come illustrato nelle schermate seguenti:

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare Microsoft API Traduzione testuale per tradurre il testo da una lingua a un'altra in un testo di un'altra lingua in un'applicazione Novell. Forms. Oltre a tradurre il testo, l'API di Microsoft Translator può anche trascrivere il riconoscimento vocale da un linguaggio a un altro linguaggio.

## <a name="related-links"></a>Collegamenti correlati

- [API traduzione testuale documentazione](/azure/cognitive-services/translator/).
- [Utilizzare un servizio Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Servizi cognitivi todo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Microsoft API traduzione testuale](https://docs.microsofttranslator.com/text-translate.html).
