---
title: Tramite il controllo ortografico Bing API controllo ortografico
description: Controllo ortografico Bing esegue ortografico contestuale per il testo, fornire suggerimenti inline per le parole. In questo articolo viene illustrato come utilizzare l'API REST controllo ortografico Bing per correggere gli errori di ortografia in un'applicazione di xamarin. Forms.
ms.prod: xamarin
ms.assetid: B40EB103-FDC0-45C6-9940-FB4ACDC2F4F9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 41bd79b22aa193dd5303847997bc07e8e8d12e58
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>Tramite il controllo ortografico Bing API controllo ortografico

_Controllo ortografico Bing esegue ortografico contestuale per il testo, fornire suggerimenti inline per le parole. In questo articolo viene illustrato come utilizzare l'API REST controllo ortografico Bing per correggere gli errori di ortografia in un'applicazione di xamarin. Forms._

## <a name="overview"></a>Panoramica

L'API REST controllo ortografico Bing ha due modalità operative, e una modalità deve essere specificata quando si effettua una richiesta all'API:

- `Spell` corregge testo breve (parole fino a 9) senza apportare modifiche di maiuscole e minuscole.
- `Proof` corregge testo lungo e fornisce correzioni di maiuscole e minuscole e la punteggiatura base Elimina correzioni aggressive.

Per utilizzare l'API di controllo ortografico Bing, è necessario ottenere una chiave API. Può essere ottenuto in [provare servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/)

Per un elenco delle lingue supportate dall'API controllo ortografico Bing, vedere [lingue supportate](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Per ulteriori informazioni sull'API controllo ortografico Bing, vedere [documentazione di controllo ortografico Bing](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Autenticazione

Ogni richiesta effettuata all'API controllo ortografico Bing richiede una chiave API che deve essere specificata come valore della `Ocp-Apim-Subscription-Key` intestazione. Esempio di codice seguente viene illustrato come aggiungere la chiave API per la `Ocp-Apim-Subscription-Key` intestazione di una richiesta:

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Il mancato superamento di una chiave API valida per l'API di controllo ortografico Bing comporterà un errore 401 di risposta.

## <a name="performing-spell-checking"></a>Eseguire il controllo ortografico

Il controllo ortografico può essere ottenuto una richiesta GET o POST per il `SpellCheck` API `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck`. Quando si effettua una richiesta GET, il testo da ortografico selezionato viene inviato come parametro di query. Quando si effettua una richiesta POST, il testo da ortografico selezionato viene inviato nel corpo della richiesta. Le richieste GET sono limitate a 1500 caratteri a causa della limitazione di lunghezza stringa di query parametro di controllo ortografico. Pertanto, le richieste POST devono essere apportate in genere, a meno che stringhe brevi vengono ortografico selezionato.

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

Il `SpellCheckTextAsync` metodo genera un URI di richiesta e quindi invia la richiesta alla `SpellCheck` API, che restituisce una risposta JSON contenente il risultato. La risposta JSON viene deserializzata, con il risultato viene restituito al metodo di chiamata per la visualizzazione.

### <a name="configuring-spell-checking"></a>Configurare il controllo ortografico

È possibile configurare il processo di controllo ortografico specificando i parametri di query HTTP:

```csharp
string GenerateRequestUri(string spellCheckEndpoint, string text, SpellCheckMode mode)
{
  string requestUri = spellCheckEndpoint;
  requestUri += string.Format("?text={0}", text);                         // text to spell check
  requestUri += string.Format("&mode={0}", mode.ToString().ToLower());    // spellcheck mode - proof or spell
  return requestUri;
}
```

Questo metodo imposta il testo da ortografico selezionata e la modalità di controllo ortografico.

Per ulteriori informazioni sull'API REST controllo ortografico Bing, vedere [riferimento v7 API controllo ortografico](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>L'invio della richiesta

Il `SendRequestAsync` metodo effettua la richiesta GET all'API REST controllo ortografico Bing e restituisce la risposta:

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Questo metodo crea la richiesta GET, aggiungere la chiave API come valore della `Ocp-Apim-Subscription-Key` intestazione. La richiesta di recupero viene quindi inviata al `SpellCheck` API, con l'URL della richiesta che specifica il testo da convertire e la modalità di controllo ortografico. La risposta viene quindi letto e restituita al metodo di chiamata.

Il `SpellCheck` API invierà il codice di stato HTTP 200 (OK) in risposta, a condizione che la richiesta sia valida, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste sono presenti nella risposta. Per un elenco di oggetti di risposta, vedere [oggetti risposta](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>L'elaborazione della risposta

Viene restituita la risposta di API in formato JSON. I dati JSON seguenti mostrano il messaggio di risposta per il testo di errore di ortografia `Go shappin tommorow`:

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

Il `flaggedTokens` matrice contiene una matrice di parole nel testo che sono stati contrassegnati come non essere stato digitato correttamente o sono errori di grammatica. La matrice sarà vuota se non vengono trovati alcun controllo ortografico o gli errori grammaticali. I tag all'interno della matrice sono:

- `offset` -un offset dall'inizio della stringa di testo in base zero per la parola che è stato contrassegnato.
- `token` : la parola nella stringa di testo che non sia stata digitata correttamente o errori di grammatica.
- `type` : il tipo di errore che ha causato la parola viene contrassegnata. Esistono due valori possibili: `RepeatedToken` e `UnknownToken`.
- `suggestions` : una matrice di parole che consentirà di correggere l'errore di ortografia o grammatica. La matrice è costituita da un `suggestion` e `score`, che indica il livello di probabilità che la correzione proposta sia corretta.

Nell'applicazione di esempio, la risposta JSON viene deserializzata in un `SpellCheckResult` istanza, con il risultato viene restituito al metodo di chiamata per la visualizzazione. Nell'esempio di codice riportato di seguito viene illustrato come la `SpellCheckResult` istanza viene elaborata per la visualizzazione:

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Il codice scorre la `FlaggedTokens` insieme e sostituisce qualsiasi errate o errori di grammatica parole nel testo di origine con il primo suggerimento. Le schermate seguenti mostrano prima e dopo il controllo ortografico:

![](spell-check-images/before-spell-check.png "Prima di controllo ortografico")

![](spell-check-images/after-spell-check.png "Dopo il controllo ortografico")

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare l'API REST controllo ortografico Bing per correggere gli errori di ortografia in un'applicazione di xamarin. Forms. Controllo ortografico Bing esegue ortografico contestuale per il testo, fornire suggerimenti inline per le parole.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di controllo ortografico Bing](/azure/cognitive-services/bing-spell-check/)
- [Utilizzo di un servizio Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)
- [Servizi cognitivi TODO (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoCognitiveServices/)
- [Riferimento v7 API di controllo ortografico Bing](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
