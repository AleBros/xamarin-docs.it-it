---
title: Utilizzo di un servizio Web RESTful
description: L'integrazione di un servizio web in un'applicazione è uno scenario comune. Questo articolo viene illustrato come utilizzare un servizio web RESTful da un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 5ae425df1d2bf28428c51366de28474a040bf368
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061336"
---
# <a name="consuming-a-restful-web-service"></a>Utilizzo di un servizio Web RESTful

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)

_L'integrazione di un servizio web in un'applicazione è uno scenario comune. Questo articolo viene illustrato come utilizzare un servizio web RESTful da un'applicazione xamarin. Forms._

Representational State Transfer (REST) è uno stile architetturale per la creazione di servizi web. Richieste REST vengono effettuate tramite HTTP utilizzando gli stessi verbi HTTP che utilizzano i web browser per recuperare le pagine web e per inviare dati ai server. I verbi sono:

- **OTTENERE** : questa operazione viene usata per recuperare i dati dal servizio web.
- **POST** : questa operazione viene usata per creare un nuovo elemento di dati nel servizio web.
- **INSERIRE** : questa operazione viene usata per aggiornare un elemento di dati nel servizio web.
- **PATCH** : questa operazione viene usata per aggiornare un elemento di dati nel servizio web mediante la descrizione di un set di istruzioni sul modo in cui l'elemento deve essere modificato. Questo verbo non viene utilizzato nell'applicazione di esempio.
- **Elimina** : questa operazione viene usata per eliminare un elemento di dati nel servizio web.

Le API che rispettano REST del servizio Web vengono chiamate le API RESTful e vengono definiti utilizzando:

- Un URI di base.
- Metodi HTTP, ad esempio GET, POST, PUT, PATCH o DELETE.
- Un tipo di supporto per i dati, ad esempio JavaScript Object Notation (JSON).

Servizi web rESTful utilizzano in genere i messaggi JSON per restituire dati al client. JSON è un formato di interscambio dei dati basata su testo che genera payload compact, con conseguente ridotti i requisiti di larghezza di banda durante l'invio di dati. L'applicazione di esempio Usa open source [libreria NewtonSoft JSON.NET](http://www.newtonsoft.com/json) per serializzare e deserializzare i messaggi.

La semplicità di REST ci ha aiutati a renderlo il principale metodo per l'accesso ai servizi web nelle applicazioni per dispositivi mobili.

Istruzioni sulla configurazione del servizio REST sono reperibile nel file Leggimi che accompagna l'applicazione di esempio. Tuttavia, quando viene eseguita l'applicazione di esempio, connetterà a un servizio REST basato su Xamarin che fornisce accesso in lettura ai dati, come illustrato nello screenshot seguente:

![](rest-images/portal.png "Applicazione di esempio")

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
>
>Può essere scelto ATS fuori se non è possibile usare la **HTTPS** protocol e proteggere le comunicazioni per le risorse internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Utilizzo del servizio Web

Il servizio REST è scritta in ASP.NET Core e fornisce le operazioni seguenti:

|Operazione|Metodo HTTP|URI relativo|Parametri|
|--- |--- |--- |--- |
|Ottenere un elenco di elementi attività|GET|/ API/todoitems /|
|Creare un nuovo elemento di attività|INSERISCI|/ API/todoitems /|Un oggetto JSON formattato TodoItem|
|Aggiornare un elemento attività|PUT|/ API/todoitems /|Un oggetto JSON formattato TodoItem|
|Eliminare un elemento attività|DELETE|/api/todoitems/{id}|

La maggior parte degli URI include il `TodoItem` ID nel percorso. Ad esempio, per eliminare il `TodoItem` il cui ID viene `6bb8a868-dba1-4f1a-93b7-24ebce87e243`, il client invia una richiesta DELETE a `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`. Per altre informazioni sul modello di dati usato nell'applicazione di esempio, vedere [modellazione dei dati](~/xamarin-forms/data-cloud/walkthrough.md).

Quando il framework API Web riceve una richiesta indirizza la richiesta a un'azione. Queste azioni sono metodi pubblici di `TodoItemsController` classe. Il framework utilizza una tabella di routing per determinare l'azione da richiamare in risposta a una richiesta, che viene visualizzata nell'esempio di codice seguente:

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

La tabella di routing contiene un modello di route e quando il framework API Web riceve una richiesta HTTP, Cerca la corrispondenza con l'URI in base al modello di route nella tabella di routing. Se un oggetto corrispondente route non viene trovato che il client riceve un errore 404 (non trovato). Se viene trovata una route corrispondente, API Web consente di selezionare il controller e azione come indicato di seguito:

- Per trovare il controller, API Web aggiunge "controller" al valore della *{controller}* variabile.
- Per trovare l'azione, API Web esamina il metodo HTTP ed esamina le azioni del controller che vengono decorate con lo stesso metodo HTTP come attributo.
- Il *{id}* variabile segnaposto è mappato a un parametro di azione.

Il servizio REST Usa l'autenticazione di base. Per altre informazioni, vedere [autenticare un servizio web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Per altre informazioni sul routing di ASP.NET Web API, vedere [Routing in API Web ASP.NET](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) sul sito Web ASP.NET. Per altre informazioni sulla compilazione del servizio REST tramite ASP.NET Core, vedere [creazione di servizi back-end per App native per dispositivi mobili](/aspnet/core/mobile/native-mobile-backend/).

> [!NOTE]
> L'applicazione di esempio Usa il servizio ospitato Xamarin REST che fornisce accesso in lettura al servizio web. Di conseguenza, le operazioni che creano, aggiornano ed eliminare dati non modificheranno i dati usati nell'applicazione. Tuttavia, è disponibile in una versione eseguibile del servizio REST il **TodoRESTService** cartella di accompagnamento [esempi di codice](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/).
> Se si ospita il servizio REST autonomamente, esso consente completo creare, aggiornare, leggere ed eliminare l'accesso ai dati.

Il `HttpClient` classe viene utilizzata per inviare e ricevere richieste tramite HTTP. Fornisce funzionalità per l'invio di richieste HTTP e ricevere risposte HTTP da un URI di risorsa identificato. Ogni richiesta viene inviata come operazione asincrona. Per altre informazioni sulle operazioni asincrone, vedere [Panoramica del supporto asincrono](~/cross-platform/platform/async.md).

Il `HttpResponseMessage` classe rappresenta un messaggio di risposta HTTP ricevuto dal servizio web dopo aver effettuata una richiesta HTTP. Contiene informazioni relative alla risposta, incluso il codice di stato, intestazioni e qualsiasi corpo. Il `HttpContent` classe rappresenta il corpo HTTP e le intestazioni del contenuto, ad esempio `Content-Type` e `Content-Encoding`. Il contenuto può essere letto utilizzando uno qualsiasi dei `ReadAs` metodi, ad esempio `ReadAsStringAsync` e `ReadAsByteArrayAsync`, in base al formato dei dati.

### <a name="creating-the-httpclient-object"></a>Creazione dell'oggetto di HTTPClient

Il `HttpClient` istanza viene dichiarata a livello di classe in modo che l'oggetto dura per fino a quando l'applicazione deve effettuare richieste HTTP, come illustrato nell'esempio di codice seguente:

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    client = new HttpClient ();
    client.MaxResponseContentBufferSize = 256000;
  }
  ...
}
```

Il `HttpClient.MaxResponseContentBufferSize` proprietà viene utilizzata per specificare il numero massimo di byte da memorizzare nel buffer durante la lettura del contenuto nel messaggio di risposta HTTP. La dimensione predefinita di questa proprietà è la dimensione massima di un numero intero. Pertanto, la proprietà è impostata su un valore inferiore, come misura di sicurezza, per limitare la quantità di dati che l'applicazione accetterà una risposta dal servizio web.

### <a name="retrieving-data"></a>Recupero dei dati

Il `HttpClient.GetAsync` metodo viene utilizzato per inviare la richiesta GET al servizio web specificato dall'URI e quindi riceve la risposta dal servizio web, come illustrato nell'esempio di codice seguente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));
  ...
  var response = await client.GetAsync (uri);
  if (response.IsSuccessStatusCode) {
      var content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

Il servizio REST invia un codice di stato HTTP di `HttpResponseMessage.IsSuccessStatusCode` proprietà, per indicare se la richiesta HTTP ha avuto esito positivo o negativo. Per questa operazione il resto servizio invia codice di stato HTTP 200 (OK) in risposta, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste nella risposta.

Se l'operazione HTTP ha esito positivo, viene letto il contenuto della risposta, per la visualizzazione. Il `HttpResponseMessage.Content` proprietà rappresenta il contenuto della risposta HTTP e il `HttpContent.ReadAsStringAsync` metodo scrive in modo asincrono il contenuto HTTP in una stringa. Questo contenuto viene quindi convertito da JSON a un `List` di `TodoItem` istanze.

### <a name="creating-data"></a>Creazione di dati

Il `HttpClient.PostAsync` metodo viene utilizzato per inviare la richiesta POST al servizio web specificato dall'URI, quindi per ricevere la risposta dal servizio web, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));

  ...
  var json = JsonConvert.SerializeObject (item);
  var content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem) {
    response = await client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"                TodoItem successfully saved.");

  }
  ...
}
```

Il `TodoItem` istanza viene convertita in un payload JSON per l'invio al servizio web. Questo payload viene poi incorporato nel corpo del contenuto HTTP che verrà inviato al servizio web prima che la richiesta viene effettuata con la `PostAsync` (metodo).

Il servizio REST invia un codice di stato HTTP di `HttpResponseMessage.IsSuccessStatusCode` proprietà, per indicare se la richiesta HTTP ha avuto esito positivo o negativo. Le risposte comuni per questa operazione sono:

- **201 (creato)** : la richiesta ha comportato una nuova risorsa viene creata prima dell'invio della risposta.
- **400 (BAD REQUEST)** : la richiesta non è riconosciuta dal server.
- **409 (conflitto)** : la richiesta non può essere eseguita a causa di un conflitto nel server.

### <a name="updating-data"></a>Aggiornamento di dati

Il `HttpClient.PutAsync` metodo viene utilizzato per inviare la richiesta PUT al servizio web specificato dall'URI e quindi riceve la risposta dal servizio web, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```
L'operazione dei `PutAsync` è identico al metodo il `PostAsync` metodo utilizzato per la creazione di dati nel servizio web. Tuttavia, le possibili risposte inviate dal servizio web diverso.

Il servizio REST invia un codice di stato HTTP di `HttpResponseMessage.IsSuccessStatusCode` proprietà, per indicare se la richiesta HTTP ha avuto esito positivo o negativo. Le risposte comuni per questa operazione sono:

- **204 (nessun contenuto)** : la richiesta è stata elaborata correttamente e la risposta è intenzionalmente vuota.
- **400 (BAD REQUEST)** : la richiesta non è riconosciuta dal server.
- **404 (non trovato)** : la risorsa richiesta non esiste nel server.

### <a name="deleting-data"></a>Eliminazione di dati

Il `HttpClient.DeleteAsync` metodo viene utilizzato per inviare la richiesta DELETE al servizio web specificato dall'URI e quindi riceve la risposta dal servizio web, come illustrato nell'esempio di codice seguente:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/{0}
  var uri = new Uri (string.Format (Constants.RestUrl, id));
  ...
  var response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"                TodoItem successfully deleted.");
  }
  ...
}
```

Il servizio REST invia un codice di stato HTTP di `HttpResponseMessage.IsSuccessStatusCode` proprietà, per indicare se la richiesta HTTP ha avuto esito positivo o negativo. Le risposte comuni per questa operazione sono:

- **204 (nessun contenuto)** : la richiesta è stata elaborata correttamente e la risposta è intenzionalmente vuota.
- **400 (BAD REQUEST)** : la richiesta non è riconosciuta dal server.
- **404 (non trovato)** : la risorsa richiesta non esiste nel server.

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato come utilizzare un servizio web RESTful da un'applicazione xamarin. Forms, utilizzando il `HttpClient` classe. La semplicità di REST ci ha aiutati a renderlo il principale metodo per l'accesso ai servizi web nelle applicazioni per dispositivi mobili.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di servizi back-end per applicazioni native per dispositivi mobili](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
