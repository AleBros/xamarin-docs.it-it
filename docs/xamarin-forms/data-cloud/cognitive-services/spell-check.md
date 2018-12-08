---
title: Controllo ortografico usando l'API controllo ortografico Bing
description: Controllo ortografico Bing esegue ortografico contestuale per il testo, che fornisce suggerimenti inline per le parole errate. Questo articolo illustra come usare l'API REST controllo ortografico Bing per correggere gli errori ortografici in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 08ac86674e4f10d6bd17d765de2bcdf7c2d3f901
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061759"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>Controllo ortografico usando l'API controllo ortografico Bing

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)

_Controllo ortografico Bing esegue ortografico contestuale per il testo, che fornisce suggerimenti inline per le parole errate. Questo articolo illustra come usare l'API REST controllo ortografico Bing per correggere gli errori ortografici in un'applicazione xamarin. Forms._

## <a name="overview"></a>Panoramica

REST API controllo ortografico Bing ha due modalità operative, e una modalità deve essere specificata quando si effettua una richiesta all'API:

- `Spell` corregge il breve testo (parole fino a 9) senza alcuna modifica di maiuscole e minuscole.
- `Proof` corregge testo lungo, fornisce correzioni di maiuscole e minuscole, punteggiatura di base ed Elimina correzioni aggressive.

Per usare l'API controllo ortografico Bing, è necessario ottenere una chiave API. Ciò può essere ottenuto in [prova servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/)

Per un elenco delle lingue supportate per l'API controllo ortografico Bing, vedere [lingue supportate](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Per altre informazioni sull'API controllo ortografico Bing, vedere [documentazione di controllo ortografico Bing](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Autenticazione

Ogni richiesta effettuata per l'API controllo ortografico Bing richiede una chiave API che deve essere specificata come valore del `Ocp-Apim-Subscription-Key` intestazione. Esempio di codice seguente viene illustrato come aggiungere la chiave API per il `Ocp-Apim-Subscription-Key` intestazione di una richiesta:

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Errore per passare una chiave API valida per l'API controllo ortografico Bing comporterà un errore di 401 risposta.

## <a name="performing-spell-checking"></a>Eseguire il controllo ortografico

Controllo ortografico scopo, effettuare una richiesta GET o POST per il `SpellCheck` all'API `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck`. Quando si effettua una richiesta GET, il testo su ortografico selezionato viene inviato come parametro di query. Quando si effettua una richiesta POST, il testo su ortografico selezionato viene inviato nel corpo della richiesta. Le richieste GET sono limitate a 1500 caratteri a causa della limitazione di lunghezza stringa parametro query di controllo ortografico. Di conseguenza, le richieste POST devono essere apportate in genere, a meno che le stringhe brevi sono si ortografico selezionato.

Nell'applicazione di esempio, il `SpellCheckTextAsync` metodo richiama il processo di controllo ortografico:

```csharp
public async Task<SpellCheckResult> SpellCheckTextAsync(string text)
{
    string requestUri = GenerateRequestUri(Constants.BingSpellCheckEndpoint, text, SpellCheckMode.Spell);
    var response = await SendRequestAsync(requestUri);
    var spellCheckResults = JsonConvert.DeserializeObject<SpellCheckResult>(response);
    return spellCheckResults;
}
```

Il `SpellCheckTextAsync` metodo genera un URI di richiesta e quindi invia la richiesta al `SpellCheck` API, che restituisce una risposta JSON che contiene il risultato. La risposta JSON viene deserializzata, con il risultato viene restituito al metodo di chiamata per la visualizzazione.

### <a name="configuring-spell-checking"></a>Configurare il controllo ortografico

Il processo di controllo ortografico può essere configurato specificando i parametri di query HTTP:

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

Questo metodo imposta il testo su ortografico selezionata e la modalità di controllo ortografico.

Per altre informazioni sull'API REST controllo ortografico Bing, vedere [riferimento di API controllo ortografico v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>L'invio della richiesta

Il `SendRequestAsync` metodo invia la richiesta GET all'API REST controllo ortografico Bing e restituisce la risposta:

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Questo metodo invia la richiesta GET per il `SpellCheck` API, con l'URL della richiesta che specifica il testo da tradurre e la modalità di controllo ortografico. La risposta viene quindi letto e restituita al metodo di chiamata.

Il `SpellCheck` API invierà il codice di stato HTTP 200 (OK) in risposta, fornita che la richiesta sia valida, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste sono presenti nella risposta. Per un elenco di oggetti di risposta, vedere [gli oggetti della risposta](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>L'elaborazione della risposta

La risposta dell'API viene restituita in formato JSON. I dati JSON seguenti mostrano il messaggio di risposta per il testo con errori di ortografia `Go shappin tommorow`:

```json
{  
   "_type":"SpellCheck",
   "flaggedTokens":[  
      {  
         "offset":3,
         "token":"shappin",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"shopping",
               "score":1
            }
         ]
      },
      {  
         "offset":11,
         "token":"tommorow",
         "type":"UnknownToken",
         "suggestions":[  
            {  
               "suggestion":"tomorrow",
               "score":1
            }
         ]
      }
   ],
   "correctionType":"High"
}
```

Il `flaggedTokens` matrice contiene una matrice di parole nel testo che sono stati contrassegnati come non in corso è stato digitato correttamente oppure sono errori di grammatica. La matrice sarà vuota se non vengono trovati alcun controllo ortografico o errori di grammatica. I tag all'interno della matrice sono:

- `offset` -un offset dall'inizio della stringa di testo in base zero per la parola che è stato contrassegnato.
- `token` – la parola nella stringa di testo che non sia stata digitata correttamente o errori di grammatica.
- `type` : il tipo di errore che ha causato la parola viene contrassegnata. Esistono due possibili valori: `RepeatedToken` e `UnknownToken`.
- `suggestions` : una matrice di parole che consente di correggere l'errore ortografico o grammaticale. La matrice è costituita da un `suggestion` e un `score`, che indica il livello di probabilità che la correzione proposta sia corretta.

Nell'applicazione di esempio, la risposta JSON viene deserializzata in un `SpellCheckResult` istanza, con il risultato viene restituito al metodo di chiamata per la visualizzazione. Nell'esempio di codice riportato di seguito viene illustrato come il `SpellCheckResult` istanza viene elaborata per la visualizzazione:

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Questo codice esegue l'iterazione attraverso la `FlaggedTokens` insieme e sostituisce eventuali errori di ortografia o parole errori di grammatica nel testo di origine con il primo suggerimento. Le schermate seguenti illustrano prima e dopo il controllo ortografico:

![](spell-check-images/before-spell-check.png "Prima di controllo ortografico")

![](spell-check-images/after-spell-check.png "Dopo il controllo ortografico")

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare l'API REST controllo ortografico Bing per correggere gli errori ortografici in un'applicazione xamarin. Forms. Controllo ortografico Bing esegue ortografico contestuale per il testo, che fornisce suggerimenti inline per le parole errate.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di controllo ortografico Bing](/azure/cognitive-services/bing-spell-check/)
- [Utilizzo di un servizio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Servizi cognitivi TODO (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Riferimento di API controllo ortografico Bing v7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
