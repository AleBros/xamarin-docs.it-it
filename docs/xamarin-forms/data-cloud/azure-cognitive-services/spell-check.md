---
title: ''
description: Controllo ortografico Bing esegue il controllo ortografico contestuale del testo, fornendo suggerimenti inline per le parole errate. Questo articolo illustra come usare l'API REST di Controllo ortografico Bing per correggere gli errori di ortografia in un' Xamarin.Forms applicazione.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1703f0049408381a86da73fb28696ef8708cc790
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139295"
---
# <a name="spell-checking-using-the-bing-spell-check-api"></a>Controllo ortografico con l'API Controllo ortografico Bing

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Controllo ortografico Bing esegue il controllo ortografico contestuale del testo, fornendo suggerimenti inline per le parole errate. Questo articolo illustra come usare l'API REST di Controllo ortografico Bing per correggere gli errori di ortografia in un' Xamarin.Forms applicazione._

## <a name="overview"></a>Panoramica

L'API REST di Controllo ortografico Bing dispone di due modalità operative ed è necessario specificare una modalità quando si effettua una richiesta all'API:

- `Spell`corregge il testo breve (fino a 9 parole) senza modifiche di maiuscole e minuscole.
- `Proof`corregge il testo lungo, fornisce le correzioni di maiuscole e minuscole e la punteggiatura di base ed evita le correzioni aggressive.

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

È necessario ottenere una chiave API per usare l'API Controllo ortografico Bing. Questa operazione può essere ottenuta in [prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/)

Per un elenco delle lingue supportate dall'API Controllo ortografico Bing, vedere [linguaggi supportati](/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages/). Per ulteriori informazioni sull'API di Controllo ortografico Bing, vedere [controllo ortografico Bing documentazione](/azure/cognitive-services/bing-spell-check/).

## <a name="authentication"></a>Authentication

Ogni richiesta effettuata all'API Controllo ortografico Bing richiede una chiave API da specificare come valore dell' `Ocp-Apim-Subscription-Key` intestazione. Nell'esempio di codice seguente viene illustrato come aggiungere la chiave API all' `Ocp-Apim-Subscription-Key` intestazione di una richiesta:

```csharp
public BingSpellCheckService()
{
    httpClient = new HttpClient();
    httpClient.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", Constants.BingSpellCheckApiKey);
}
```

Se non si passa una chiave API valida al Controllo ortografico Bing API, verrà generato un errore di risposta 401.

## <a name="performing-spell-checking"></a>Esecuzione del controllo ortografico

Il controllo ortografico può essere ottenuto effettuando una richiesta GET o POST all' `SpellCheck` API in `https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck` . Quando si esegue una richiesta GET, il testo da sottoposto a spelling viene inviato come parametro di query. Quando si effettua una richiesta POST, il testo da sottoporre a spelling viene inviato nel corpo della richiesta. Le richieste GET sono limitate al controllo ortografico di 1500 caratteri a causa del limite di lunghezza della stringa del parametro di query. Pertanto, le richieste POST devono essere in genere eseguite a meno che non venga eseguita la verifica ortografica delle stringhe brevi.

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

Il `SpellCheckTextAsync` metodo genera un URI di richiesta e quindi Invia la richiesta all' `SpellCheck` API, che restituisce una risposta JSON contenente il risultato. La risposta JSON viene deserializzata e il risultato viene restituito al metodo chiamante per la visualizzazione.

### <a name="configuring-spell-checking"></a>Configurazione del controllo ortografico

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

Questo metodo imposta il testo da controllare per l'ortografia e la modalità di controllo ortografico.

Per altre informazioni sull'API REST di Controllo ortografico Bing, vedere [riferimento API controllo ortografico V7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/).

### <a name="sending-the-request"></a>Invio della richiesta

Il `SendRequestAsync` metodo effettua la richiesta GET all'API REST di controllo ortografico Bing e restituisce la risposta:

```csharp
async Task<string> SendRequestAsync(string url)
{
    var response = await httpClient.GetAsync(url);
    return await response.Content.ReadAsStringAsync();
}
```

Questo metodo Invia la richiesta GET all' `SpellCheck` API, con l'URL della richiesta che specifica il testo da tradurre e la modalità di controllo ortografico. La risposta viene quindi letta e restituita al metodo chiamante.

L'API invierà il `SpellCheck` codice di stato HTTP 200 (OK) nella risposta, purché la richiesta sia valida, a indicare che la richiesta è riuscita e che le informazioni richieste sono nella risposta. Per un elenco di oggetti risposta, vedere [oggetti risposta](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#response-objects).

### <a name="processing-the-response"></a>Elaborazione della risposta

La risposta dell'API viene restituita in formato JSON. I dati JSON seguenti mostrano il messaggio di risposta per il testo con errori di ortografia `Go shappin tommorow` :

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

La `flaggedTokens` matrice contiene una matrice di parole nel testo contrassegnate come non digitate correttamente o che non sono grammaticalmente corrette. La matrice sarà vuota se non vengono trovati errori di ortografia o grammatica. I tag all'interno della matrice sono:

- `offset`: offset in base zero dall'inizio della stringa di testo alla parola contrassegnata.
- `token`: la parola nella stringa di testo che non è stata digitata correttamente o che non è grammaticalmente corretta.
- `type`: tipo dell'errore che ha causato la segnalazione della parola. Esistono due valori possibili: `RepeatedToken` e `UnknownToken` .
- `suggestions`: una matrice di parole che correggeranno l'errore di ortografia o di grammatica. La matrice è costituita da un oggetto `suggestion` e un oggetto `score` , che indica il livello di confidenza che la correzione suggerita è corretta.

Nell'applicazione di esempio, la risposta JSON viene deserializzata in un' `SpellCheckResult` istanza di, con il risultato restituito al metodo chiamante per la visualizzazione. Nell'esempio di codice seguente viene illustrata l' `SpellCheckResult` elaborazione dell'istanza per la visualizzazione:

```csharp
var spellCheckResult = await bingSpellCheckService.SpellCheckTextAsync(TodoItem.Name);
foreach (var flaggedToken in spellCheckResult.FlaggedTokens)
{
  TodoItem.Name = TodoItem.Name.Replace(flaggedToken.Token, flaggedToken.Suggestions.FirstOrDefault().Suggestion);
}
```

Questo codice esegue l'iterazione della `FlaggedTokens` raccolta e sostituisce le parole errate ortografiche o grammaticali nel testo di origine con il primo suggerimento. Gli screenshot seguenti mostrano prima e dopo il controllo ortografico:

![](spell-check-images/before-spell-check.png "Before Spell Check")

![](spell-check-images/after-spell-check.png "After Spell Check")

> [!NOTE]
> Nell'esempio precedente viene usato `Replace` per semplicità, ma in una grande quantità di testo potrebbe sostituire il token errato. L'API fornisce il `offset` valore che deve essere usato nelle app di produzione per identificare la posizione corretta nel testo di origine per eseguire un aggiornamento.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare l'API REST di Controllo ortografico Bing per correggere gli errori di ortografia in un' Xamarin.Forms applicazione. Controllo ortografico Bing esegue il controllo ortografico contestuale del testo, fornendo suggerimenti inline per le parole errate.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di Controllo ortografico Bing](/azure/cognitive-services/bing-spell-check/)
- [Utilizzare un servizio Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Servizi cognitivi todo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)
- [Informazioni di riferimento sull'API Controllo ortografico Bing V7](/rest/api/cognitiveservices/bing-spell-check-api-v7-reference/)
