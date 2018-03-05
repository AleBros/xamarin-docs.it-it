---
title: Conversione di testo tramite l'API di conversione
description: "Per convertire il riconoscimento vocale e testo tramite un'API REST, è possibile utilizzare l'API di Microsoft Translator. In questo articolo viene illustrato come utilizzare l'API di Microsoft testo traduzione per tradurre il testo da una lingua a altra in un'applicazione di xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 68330242-92C5-46F1-B1E3-2395D8823B0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: f403ebaffdf742c22e61b73aee7a42648fe597dc
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="text-translation-using-the-translator-api"></a>Conversione di testo tramite l'API di conversione

_Per convertire il riconoscimento vocale e testo tramite un'API REST, è possibile utilizzare l'API di Microsoft Translator. In questo articolo viene illustrato come utilizzare l'API di Microsoft testo traduzione per tradurre il testo da una lingua a altra in un'applicazione di xamarin. Forms._

## <a name="overview"></a>Panoramica

L'API di conversione presenta due componenti:

- Una traduzione di testo API REST per tradurre il testo da una lingua nel testo di un'altra lingua. L'API rileva automaticamente la lingua del testo che è stato inviato prima di traduzione.
- Una traduzione di riconoscimento vocale API REST per trascrivere vocale da una lingua nel testo di un'altra lingua. L'API integra anche le funzionalità di sintesi vocale per pronunciare il testo tradotto nuovamente.

Questo articolo illustra la traduzione del testo da una lingua a un altro utilizzando l'API di conversione di testo.

Per utilizzare l'API di conversione di testo, è necessario ottenere una chiave API. Questo può essere ottenuto seguendo le istruzioni in [Introduzione](http://docs.microsofttranslator.com/text-translate.html) su [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

Per ulteriori informazioni sull'API di conversione di Microsoft, vedere [Microsoft Translator documentazione](https://www.microsoft.com/cognitive-services/translator-api/documentation/TranslatorInfo/overview) sul sito Web microsoft.com.

## <a name="authentication"></a>Autenticazione

Ogni richiesta effettuata all'API di conversione di testo richiede un token di accesso JSON Web Token (JWT), che può essere ottenuto dal servizio token di servizi cognitivi in `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`. Un token può essere ottenuto tramite una richiesta POST al servizio token di specificando un `Ocp-Apim-Subscription-Key` intestazione che contiene la chiave API come relativo valore.

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

Il token di accesso deve essere specificato in ogni API di conversione di testo come un `Authorization` intestazione con la stringa di prefisso `Bearer`, come illustrato nell'esempio di codice seguente:

```csharp
using (var httpClient = new HttpClient())
{
  httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
  ...
}  
```

Per ulteriori informazioni relative al servizio token di servizi cognitivi, vedere [API Token di autenticazione](http://docs.microsofttranslator.com/oauth-token.html) su [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

## <a name="performing-text-translation"></a>Effettua la conversione di testo

Conversione di testo può essere ottenuto eseguendo una richiesta GET per il `Translate` API `https://api.microsofttranslator.com/v2/http.svc/Translate`. Nell'applicazione di esempio, il `TranslateTextAsync` metodo richiama il processo di conversione di testo:

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

Il `TranslateTextAsync` metodo genera un URI di richiesta e recupera un token di accesso dal servizio di token. La richiesta di conversione di testo viene quindi inviata al `Translate` API, che restituisce una risposta XML che contiene il risultato. La risposta XML viene analizzata e viene restituito il risultato della conversione al metodo di chiamata per la visualizzazione.

Per ulteriori informazioni sulle API REST di traduzione di testo, vedere [codice di esempio](http://docs.microsofttranslator.com/text-translate.html#/default) su [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

### <a name="configuring-text-translation"></a>Configurazione della conversione di testo

Il processo di conversione di testo può essere configurato specificando i parametri di query HTTP. Sono previsti parametri obbligatori e facoltativi, con il metodo seguente che mostra i parametri obbligatori che devono essere impostati:

```csharp
string GenerateRequestUri(string endpoint, string text, string to)
{
  string requestUri = endpoint;
  requestUri += string.Format("?text={0}", Uri.EscapeUriString(text));
  requestUri += string.Format("&to={0}", to);
  return requestUri;
}
```

Questo metodo imposta il testo da convertire e la lingua per il testo da convertire. Per un elenco di lingue supportate da Microsoft Translator, vedere [lingue](https://www.microsoft.com/translator/languages.aspx) sul sito Web microsoft.com.

> [!NOTE]
> Se un'applicazione deve conoscere la lingua in cui si trova il testo di `Detect` API può essere chiamato per rilevare la lingua della stringa di testo.

Per ulteriori informazioni sui parametri obbligatori e facoltativi, vedere [API di conversione di testo](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate) su [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

### <a name="sending-the-request"></a>L'invio della richiesta

Il `SendRequestAsync` metodo effettua la richiesta GET all'API REST di conversione di testo e restituisce la risposta:

```csharp
async Task<string> SendRequestAsync(string url, string bearerToken)
{
  using (var httpClient = new HttpClient())
  {
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", bearerToken);
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
  }
}
```

Questo metodo crea la richiesta di recupero aggiungendo il token di accesso per il `Authorization` intestazione, con la stringa di prefisso `Bearer`. La richiesta di recupero viene quindi inviata al `Translate` API, con l'URL della richiesta che specifica il testo da convertire e la lingua per il testo da convertire. La risposta viene quindi letto e restituita al metodo di chiamata.

Il `Translate` API invierà il codice di stato HTTP 200 (OK) in risposta, a condizione che la richiesta sia valida, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste sono presenti nella risposta. Per un elenco di possibili risposte d'errore, vedere i messaggi di risposta a [ottenere tradurre](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate) su [docs.microsofttranslator.com](http://docs.microsofttranslator.com/).

### <a name="processing-the-response"></a>L'elaborazione della risposta

Viene restituita la risposta di API in formato XML. I dati XML seguenti viene mostrato un messaggio di risposta con esito positivo tipico:

```xml
<string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">Morgen kaufen gehen ein</string>
```

Nell'applicazione di esempio, la risposta XML viene analizzata in un `XDocument` istanza, con il valore radice XML viene restituito al metodo di chiamata per la visualizzazione, come illustrato nelle schermate seguenti:

![](text-translation-images/text-translation.png "Conversione di testo in tedesco")

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come utilizzare l'API di Microsoft testo traduzione per tradurre il testo da una lingua nel testo di un'altra lingua in un'applicazione di xamarin. Forms. Oltre alla conversione di testo, è possibile che l'API Microsoft Translator trascrivere anche vocale da una lingua nel testo di un'altra lingua.



## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di Microsoft Translator](https://www.microsoft.com/cognitive-services/translator-api/documentation/TranslatorInfo/overview)
- [Utilizzo di un servizio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Servizi cognitivi TODO (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Conversione di testo API](http://docs.microsofttranslator.com/text-translate.html)
