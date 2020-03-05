---
title: Traduzione testuale tramite l'API Microsoft Translator
description: L'API Microsoft Translator è utilizzabile per tradurre il riconoscimento vocale e testo tramite un'API REST. Questo articolo illustra come usare l'API traduzione testuale Microsoft Translator per tradurre il testo da una lingua a altra in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 841b1d4abab5e4c09249174b221da20794771a86
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292093"
---
# <a name="text-translation-using-the-translator-api"></a>Traduzione testuale tramite l'API Microsoft Translator

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_L'API di Microsoft Translator può essere usata per tradurre la voce e il testo tramite un'API REST. Questo articolo illustra come usare Microsoft API Traduzione testuale per tradurre il testo da una lingua a un'altra in un'applicazione Novell. Forms._

## <a name="overview"></a>Panoramica

L'API Microsoft Translator include due componenti:

- Una traduzione di testo l'API REST per tradurre il testo da una lingua nel testo di un altro linguaggio. L'API rileva automaticamente la lingua del testo che è stata inviata prima traduzione.
- Un'API REST di trascrizione vocale da una lingua nel testo di un'altra lingua di traduzione vocale. L'API integra anche funzionalità di sintesi vocale per riconvertire in audio il testo tradotto.

Questo articolo illustra la traduzione del testo da una lingua a un'altra tramite l'API traduzione testuale.

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

Per usare l'API traduzione testuale, è necessario ottenere una chiave API. Questa operazione può essere ottenuta in [come iscriversi a Microsoft API traduzione testuale](/azure/cognitive-services/translator/translator-text-how-to-signup/).

Per ulteriori informazioni su Microsoft API Traduzione testuale, vedere [API traduzione testuale documentazione](/azure/cognitive-services/translator/).

## <a name="authentication"></a>Autenticazione

Ogni richiesta effettuata al API Traduzione testuale richiede un token di accesso JWT (JSON Web Token), che può essere ottenuto dal servizio token di servizi cognitivi in `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. È possibile ottenere un token effettuando una richiesta POST al servizio token, specificando un'intestazione `Ocp-Apim-Subscription-Key` che contiene la chiave API come valore.

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

Il token di accesso deve essere specificato in ogni chiamata API Traduzione testuale come intestazione di `Authorization` preceduta dalla stringa `Bearer`, come illustrato nell'esempio di codice seguente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
```

Per ulteriori informazioni sul servizio token di servizi cognitivi, vedere [Authentication](/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="performing-text-translation"></a>Esecuzione di traduzione testuale

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

Il metodo `TranslateTextAsync` genera un URI di richiesta e recupera un token di accesso dal servizio token. La richiesta di traduzione testuale viene quindi inviata all'API `translate`, che restituisce una risposta XML contenente il risultato. La risposta XML viene analizzata e viene restituito il risultato di traduzione al metodo di chiamata per la visualizzazione.

Per ulteriori informazioni sulle API REST per la traduzione testuale, vedere [API traduzione testuale](/azure/cognitive-services/translator/reference/v3-0-reference).

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

Questo metodo imposta il testo da tradurre e linguaggio da tradurre il testo. Per un elenco delle lingue supportate da Microsoft Translator, vedere [le lingue supportate nella API traduzione testuale Microsoft](/azure/cognitive-services/translator/languages/).

> [!NOTE]
> Se un'applicazione deve conoscere la lingua in cui si trova il testo, è possibile chiamare l'API `Detect` per rilevare la lingua della stringa di testo.

### <a name="sending-the-request"></a>L'invio della richiesta

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

Questo metodo compila la richiesta GET aggiungendo il token di accesso all'intestazione `Authorization`, preceduta dalla stringa `Bearer`. La richiesta GET viene quindi inviata all'API di `translate`, con l'URL della richiesta che specifica il testo da tradurre e la lingua in cui tradurre il testo. La risposta viene quindi letto e restituita al metodo di chiamata.

L'API `translate` invierà il codice di stato HTTP 200 (OK) nella risposta, purché la richiesta sia valida, a indicare che la richiesta è riuscita e che le informazioni richieste sono nella risposta. Per un elenco di possibili risposte di errore, vedere messaggi di risposta in [Get Translate](/azure/cognitive-services/translator/reference/v3-0-translate).

### <a name="processing-the-response"></a>L'elaborazione della risposta

La risposta dell'API viene restituita in formato XML. I dati XML seguenti mostrano un messaggio di risposta con esito positivo tipico:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

Nell'applicazione di esempio, la risposta XML viene analizzata in un'istanza di `XDocument`, con il valore radice XML restituito al metodo chiamante per la visualizzazione, come illustrato nelle schermate seguenti:

![](text-translation-images/text-translation.png "Text Translation to German")

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare l'API traduzione testuale Microsoft Translator per tradurre il testo da una lingua nel testo di un'altra lingua in un'applicazione xamarin. Forms. Oltre alla traduzione del testo, è possibile che l'API di Microsoft Translator trascrivere anche vocale da una lingua nel testo di un altro linguaggio.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di API Traduzione testuale](/azure/cognitive-services/translator/)
- [Utilizzare un servizio Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Servizi cognitivi todo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [API Traduzione testuale](/azure/cognitive-services/translator/reference/v3-0-reference)
