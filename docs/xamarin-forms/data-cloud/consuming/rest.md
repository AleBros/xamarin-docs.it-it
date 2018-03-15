---
title: Utilizzo di un servizio Web RESTful
description: "Integrazione di un servizio web in un'applicazione è in uno scenario comune. In questo articolo viene illustrato come utilizzare un servizio web RESTful da un'applicazione di xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 98c38001ea7751c419d4be5b0f68339b06ec656f
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2018
---
# <a name="consuming-a-restful-web-service"></a>Utilizzo di un servizio Web RESTful

_Integrazione di un servizio web in un'applicazione è in uno scenario comune. In questo articolo viene illustrato come utilizzare un servizio web RESTful da un'applicazione di xamarin. Forms._

Representational State Transfer (REST) è uno stile architetturale per la compilazione di servizi web. Richieste REST vengono effettuate tramite HTTP utilizzando gli stessi verbi HTTP utilizzato dal web browser per recuperare le pagine web e per inviare dati al server. I verbi sono:

- **OTTENERE** : questa operazione viene utilizzata per recuperare i dati dal servizio web.
- **POST** : questa operazione viene utilizzata per creare un nuovo elemento di dati nel servizio web.
- **INSERIRE** : questa operazione viene utilizzata per aggiornare un elemento di dati nel servizio web.
- **PATCH** : questa operazione viene utilizzata per aggiornare un elemento di dati nel servizio web che descrive un set di istruzioni su come l'elemento deve essere modificato. Nell'applicazione di esempio non viene utilizzato questo verbo.
- **ELIMINARE** : questa operazione viene utilizzata per eliminare un elemento di dati nel servizio web.

API conformi al resto del servizio Web vengono chiamati RESTful API e vengono definiti utilizzando:

- Un URI di base.
- Metodi HTTP, ad esempio GET, POST, PUT, PATCH o DELETE.
- Un tipo di supporto per i dati, ad esempio JavaScript Object Notation (JSON).

I servizi web rESTful utilizzano in genere i messaggi JSON per restituire dati al client. JSON è un formato di interscambio dei dati basata su testo che produce compact payload, determinando ridotti i requisiti di larghezza di banda durante l'invio di dati. L'applicazione di esempio utilizza open source [NewtonSoft JSON.NET libreria](http://www.newtonsoft.com/json) per serializzare e deserializzare i messaggi.

La semplicità di resto ha contribuito a renderlo il metodo principale per l'accesso ai servizi web in applicazioni per dispositivi mobili.

Istruzioni sulla configurazione di servizio REST sono reperibile nel file Leggimi che accompagna l'applicazione di esempio. Tuttavia, quando viene eseguita l'applicazione di esempio, la si connetterà a un servizio ospitato Xamarin REST che fornisce l'accesso in sola lettura ai dati, come illustrato nella schermata seguente:

![](rest-images/portal.png "Applicazione di esempio")

> [!NOTE]
> In iOS 9 e versioni successive, sicurezza trasporto App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app, impedendo in tal modo la diffusione accidentale di informazioni riservate. Poiché AT è attivata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.
>
>Può essere scelto at fuori se non è possibile utilizzare il **HTTPS** del protocollo e proteggere le comunicazioni per le risorse internet. Ciò può essere ottenuto l'aggiornamento dell'app **Info. plist** file. Per ulteriori informazioni vedere [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Utilizzo del servizio Web

Il servizio REST viene scritto usando ASP.NET Core e fornisce le seguenti operazioni:

|Operazione|Metodo HTTP|URI relativo|Parametri|
|--- |--- |--- |--- |
|Ottenere un elenco di elementi di attività da eseguire|GET|/api/todoitems/|
|Creare un nuovo elemento di attività da eseguire|INSERISCI|/api/todoitems/|TodoItem formattato JSON|
|Aggiornare un elemento di attività|PUT|/api/todoitems/|TodoItem formattato JSON|
|Eliminare un elemento di attività|DELETE|/api/todoitems/{id}|

La maggior parte degli URI includono il `TodoItem` ID nel percorso. Ad esempio, per eliminare il `TodoItem` il cui ID è `6bb8a868-dba1-4f1a-93b7-24ebce87e243`, il client invia una richiesta di eliminazione per `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`. Per ulteriori informazioni sul modello di data utilizzato nell'applicazione di esempio, vedere [modellazione dati](~/xamarin-forms/data-cloud/walkthrough.md).

Quando il framework di API Web riceve una richiesta viene indirizzata la richiesta a un'azione. Queste azioni sono metodi pubblici di `TodoItemsController` classe. Il framework utilizza una tabella di routing per determinare l'azione da richiamare in risposta a una richiesta, come illustrata nell'esempio di codice seguente:

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

La tabella di routing contiene un modello di route e quando il framework di API Web riceve una richiesta HTTP, il tentativo di corrispondere URI di base del modello di route nella tabella di routing. Se un oggetto corrispondente route Impossibile trovare che il client riceve un errore 404 (non trovato). Se viene trovata una route corrispondente, API Web consente di selezionare il controller e l'azione come indicato di seguito:

- Per trovare il controller, API Web aggiunge "controller" il valore di *{controller}* variabile.
- Per trovare l'azione, API Web esamina il metodo HTTP ed esamina le azioni del controller che vengono decorate con lo stesso metodo HTTP come un attributo.
- Il *{id}* variabile segnaposto è mappato a un parametro di azione.

Il servizio REST utilizza l'autenticazione di base. Per ulteriori informazioni vedere [l'autenticazione di un servizio web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Per ulteriori informazioni sul routing di ASP.NET Web API, vedere [Routing in ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) nel sito Web ASP.NET. Per ulteriori informazioni sulla compilazione servizio REST mediante ASP.NET Core, vedere [la creazione di servizi back-end per le applicazioni Native di dispositivi mobili](/aspnet/core/mobile/native-mobile-backend/).

> [!NOTE]
> L'applicazione di esempio utilizza il servizio ospitato Xamarin REST che fornisce l'accesso in sola lettura per il servizio web. Le operazioni di creano, aggiornano ed eliminare dati, pertanto, non eliminerà i dati utilizzati nell'applicazione. È tuttavia disponibile in una versione eseguibile del servizio REST di **TodoRESTService** nella cartella [codice di esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/).
> Se si ospita il servizio REST manualmente, consente completo creare, aggiornare, leggere ed eliminare l'accesso ai dati.

La `HttpClient` classe viene utilizzata per inviare e ricevere richieste su HTTP. Fornisce funzionalità per l'invio di richieste HTTP e la ricezione di risposte HTTP da un URI di risorsa identificato. Ogni richiesta viene inviata come operazione asincrona. Per ulteriori informazioni sulle operazioni asincrone, vedere [Cenni preliminari sul supporto di Async](~/cross-platform/platform/async.md).

La `HttpResponseMessage` classe rappresenta un messaggio di risposta HTTP ricevuto dal servizio web dopo aver effettuata una richiesta HTTP. Contiene informazioni sulla risposta, incluso il codice di stato, intestazioni e un corpo. Il `HttpContent` classe rappresenta il corpo HTTP e le intestazioni del contenuto, ad esempio `Content-Type` e `Content-Encoding`. Il contenuto può essere letto utilizzando uno qualsiasi del `ReadAs` metodi, ad esempio `ReadAsStringAsync` e `ReadAsByteArrayAsync`, in base al formato dei dati.

### <a name="creating-the-httpclient-object"></a>Creazione dell'oggetto di HTTPClient

Il `HttpClient` istanza viene dichiarata a livello di classe in modo che l'oggetto ha una validità fino a quando l'applicazione deve effettuare le richieste HTTP, come illustrato nell'esempio di codice seguente:

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

Il `HttpClient.MaxResponseContentBufferSize` proprietà viene utilizzata per specificare il numero massimo di byte da memorizzare nel buffer durante la lettura del contenuto nel messaggio di risposta HTTP. La dimensione predefinita di questa proprietà è la dimensione massima di un numero intero. Pertanto, la proprietà è impostata su un valore più piccolo, come misura di sicurezza, per limitare la quantità di dati che l'applicazione accetterà come risposta dal servizio web.

### <a name="retrieving-data"></a>Recupero dei dati

Il `HttpClient.GetAsync` metodo viene utilizzato per inviare la richiesta GET al servizio web specificato dall'URI e ricevere la risposta dal servizio web, come illustrato nell'esempio di codice seguente:

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

Il servizio REST invia un codice di stato HTTP di `HttpResponseMessage.IsSuccessStatusCode` proprietà che indica se la richiesta HTTP ha avuto esito positivo o negativo. Per questa operazione il resto servizio invia il codice di stato HTTP 200 (OK) nella risposta, che indica che la richiesta ha avuto esito positivo e che le informazioni richieste nella risposta.

Se l'operazione HTTP ha esito positivo, viene letto il contenuto della risposta, per la visualizzazione. Il `HttpResponseMessage.Content` proprietà rappresenta il contenuto della risposta HTTP e `HttpContent.ReadAsStringAsync` metodo scrive in modo asincrono il contenuto HTTP in una stringa. Questo contenuto viene quindi convertito da JSON a un `List` di `TodoItem` istanze.

### <a name="creating-data"></a>Creazione di dati

Il `HttpClient.PostAsync` metodo viene utilizzato per inviare la richiesta POST per il servizio web specificato dall'URI, quindi per ricevere la risposta dal servizio web, come illustrato nell'esempio di codice seguente:

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
    Debug.WriteLine (@"             TodoItem successfully saved.");

  }
  ...
}
```

Il `TodoItem` istanza viene convertita in un payload JSON per l'invio al servizio web. Il payload viene quindi incorporato nel corpo del contenuto HTTP che verrà inviato al servizio web prima che la richiesta viene effettuata con il `PostAsync` metodo.

Il servizio REST invia un codice di stato HTTP di `HttpResponseMessage.IsSuccessStatusCode` proprietà che indica se la richiesta HTTP ha avuto esito positivo o negativo. Le risposte comuni per questa operazione sono:

- **201 (creato)** : la richiesta ha restituito una nuova risorsa viene creata prima dell'invio della risposta.
- **400 (BAD REQUEST)** : la richiesta non è stata riconosciuta dal server.
- **409 (conflitto)** : la richiesta non può essere eseguita a causa di un conflitto nel server.

### <a name="updating-data"></a>Aggiornamento di dati

Il `HttpClient.PutAsync` metodo viene utilizzato per inviare la richiesta PUT al servizio web specificato dall'URI e ricevere la risposta dal servizio web, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```
L'operazione del `PutAsync` è identico al metodo di `PostAsync` metodo utilizzato per la creazione di dati nel servizio web. Tuttavia, le possibili risposte inviate dal servizio web diversi.

Il servizio REST invia un codice di stato HTTP di `HttpResponseMessage.IsSuccessStatusCode` proprietà che indica se la richiesta HTTP ha avuto esito positivo o negativo. Le risposte comuni per questa operazione sono:

- **204 (nessun contenuto)** : la richiesta è stata elaborata correttamente e la risposta è intenzionalmente vuota.
- **400 (BAD REQUEST)** : la richiesta non è stata riconosciuta dal server.
- **404 (non trovato)** : la risorsa richiesta non esiste nel server.

### <a name="deleting-data"></a>Eliminazione di dati

Il `HttpClient.DeleteAsync` metodo viene utilizzato per inviare la richiesta di eliminazione per il servizio web specificato dall'URI e ricevere la risposta dal servizio web, come illustrato nell'esempio di codice seguente:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/{0}
  var uri = new Uri (string.Format (Constants.RestUrl, id));
  ...
  var response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"             TodoItem successfully deleted.");
  }
  ...
}
```

Il servizio REST invia un codice di stato HTTP di `HttpResponseMessage.IsSuccessStatusCode` proprietà che indica se la richiesta HTTP ha avuto esito positivo o negativo. Le risposte comuni per questa operazione sono:

- **204 (nessun contenuto)** : la richiesta è stata elaborata correttamente e la risposta è intenzionalmente vuota.
- **400 (BAD REQUEST)** : la richiesta non è stata riconosciuta dal server.
- **404 (non trovato)** : la risorsa richiesta non esiste nel server.

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato come utilizzare un servizio web RESTful da un'applicazione di xamarin. Forms, utilizzando la `HttpClient` classe. La semplicità di resto ha contribuito a renderlo il metodo principale per l'accesso ai servizi web in applicazioni per dispositivi mobili.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di servizi back-end per applicazioni native per dispositivi mobili](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
