---
title: "utilizzare un servizio Web RESTful" Descrizione: "l'integrazione di un servizio Web in un'applicazione è uno scenario comune. Questo articolo illustra come utilizzare un servizio Web RESTful da un' Xamarin.Forms applicazione ".
ms. prod: Novell MS. AssetID: B540910C-9C51-416A-AAB9-057BF76489C3 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 05/28/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="consume-a-restful-web-service"></a>Utilizzare un servizio Web RESTful

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_L'integrazione di un servizio Web in un'applicazione è uno scenario comune. Questo articolo illustra come utilizzare un servizio Web RESTful da un' Xamarin.Forms applicazione._

REST (Representational State Transfer) è uno stile di architettura per la creazione di servizi Web. Le richieste REST vengono effettuate tramite HTTP usando gli stessi verbi HTTP usati dai Web browser per recuperare le pagine Web e inviare dati ai server. I verbi sono:

- **Get** : questa operazione viene usata per recuperare i dati dal servizio Web.
- **Post** : questa operazione viene usata per creare un nuovo elemento di dati nel servizio Web.
- **Put** : questa operazione viene usata per aggiornare un elemento di dati nel servizio Web.
- **Patch** : questa operazione viene usata per aggiornare un elemento di dati nel servizio Web descrivendo una serie di istruzioni su come modificare l'elemento. Questo verbo non viene utilizzato nell'applicazione di esempio.
- **Delete** : questa operazione viene usata per eliminare un elemento di dati nel servizio Web.

Le API del servizio Web che rispettano REST sono denominate API RESTful e vengono definite usando:

- URI di base.
- Metodi HTTP, ad esempio GET, POST, PUT, PATCH o DELETE.
- Tipo di supporto per i dati, ad esempio JavaScript Object Notation (JSON).

I servizi Web RESTful usano in genere i messaggi JSON per restituire i dati al client. JSON è un formato di interscambio dei dati basato su testo che genera payload Compact, con conseguente riduzione dei requisiti di larghezza di banda quando si inviano dati. L'applicazione di esempio usa la libreria open source [NewtonSoft JSON.NET](https://www.newtonsoft.com/json) per serializzare e deserializzare i messaggi.

La semplicità di REST ha aiutato a renderlo il metodo principale per accedere ai servizi Web nelle applicazioni per dispositivi mobili.

Quando viene eseguita l'applicazione di esempio, si connetterà a un servizio REST ospitato localmente, come illustrato nello screenshot seguente:

![](rest-images/portal.png "Sample Application")

> [!NOTE]
> In iOS 9 e versioni successive, la sicurezza del trasporto app impone connessioni sicure tra le risorse Internet (ad esempio il server back-end dell'app) e l'app, impedendo così la divulgazione accidentale di informazioni riservate. Poiché ATS è abilitato per impostazione predefinita nelle app compilate per iOS 9, tutte le connessioni saranno soggette ai requisiti di sicurezza di ATS. Se le connessioni non soddisfano questi requisiti, avranno esito negativo con un'eccezione.
>
>È possibile escludere ATS da se non è possibile usare il protocollo **https** e proteggere le comunicazioni per le risorse Internet. Questa operazione può essere eseguita aggiornando il file **info. plist** dell'app. Per altre informazioni, vedere [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Utilizzo del servizio Web

Il servizio REST viene scritto utilizzando ASP.NET Core e fornisce le operazioni seguenti:

|Operazione|Metodo HTTP|URI relativo|Parametri|
|--- |--- |--- |--- |
|Ottenere un elenco di elementi attività|GET|/api/todoitems/|
|Crea nuovo elemento attività|POST|/api/todoitems/|TodoItem formattato JSON|
|Aggiornare un elemento attività|PUT|/api/todoitems/|TodoItem formattato JSON|
|Eliminare un elemento attività|DELETE|/api/todoitems/{id}|

La maggior parte degli URI include l' `TodoItem` ID nel percorso. Ad esempio, per eliminare il il `TodoItem` cui ID è `6bb8a868-dba1-4f1a-93b7-24ebce87e243` , il client invia una richiesta DELETE a `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243` . Per ulteriori informazioni sul modello di dati utilizzato nell'applicazione di esempio, vedere [modellazione dei dati](~/xamarin-forms/data-cloud/web-services/introduction.md).

Quando il Framework API Web riceve una richiesta, instrada la richiesta a un'azione. Queste azioni sono semplicemente metodi pubblici nella `TodoItemsController` classe. Il Framework usa una tabella di routing per determinare l'azione da richiamare in risposta a una richiesta, come illustrato nell'esempio di codice seguente:

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

La tabella di routing contiene un modello di route e, quando il Framework API Web riceve una richiesta HTTP, tenta di trovare una corrispondenza tra l'URI e il modello di route nella tabella di routing. Se non è possibile trovare una route corrispondente, il client riceve un errore 404 (non trovato). Se viene trovata una route corrispondente, l'API Web seleziona il controller e l'azione come indicato di seguito:

- Per trovare il controller, l'API Web aggiunge "controller" al valore della variabile *{controller}* .
- Per trovare l'azione, l'API Web esamina il metodo HTTP ed esamina le azioni del controller che sono decorate con lo stesso metodo HTTP di un attributo.
- Viene eseguito il mapping della variabile segnaposto *{ID}* a un parametro di azione.

Il servizio REST usa l'autenticazione di base. Per altre informazioni, vedere [autenticazione di un servizio Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Per ulteriori informazioni sul routing API Web ASP.NET, vedere [routing in API Web ASP.NET](https://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) nel sito Web ASP.NET. Per ulteriori informazioni sulla compilazione del servizio REST utilizzando ASP.NET Core, vedere [creazione di servizi back-end per applicazioni per dispositivi mobili native](/aspnet/core/mobile/native-mobile-backend/).

La `HttpClient` classe viene usata per inviare e ricevere richieste tramite http. Fornisce funzionalità per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata dall'URI. Ogni richiesta viene inviata come operazione asincrona. Per ulteriori informazioni sulle operazioni asincrone, vedere [Cenni preliminari sul supporto](~/cross-platform/platform/async.md)asincrono.

La `HttpResponseMessage` classe rappresenta un messaggio di risposta http ricevuto dal servizio Web dopo che è stata effettuata una richiesta HTTP. Contiene informazioni sulla risposta, tra cui il codice di stato, le intestazioni e qualsiasi corpo. La `HttpContent` classe rappresenta le intestazioni di contenuto e del corpo HTTP, ad esempio `Content-Type` e `Content-Encoding` . Il contenuto può essere letto usando uno qualsiasi dei `ReadAs` metodi, ad esempio `ReadAsStringAsync` e `ReadAsByteArrayAsync` , a seconda del formato dei dati.

### <a name="creating-the-httpclient-object"></a>Creazione dell'oggetto HTTPClient

L' `HttpClient` istanza viene dichiarata a livello di classe in modo che l'oggetto sia disponibile fino a quando l'applicazione deve eseguire richieste HTTP, come illustrato nell'esempio di codice seguente:

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    client = new HttpClient ();
  }
  ...
}
```

### <a name="retrieving-data"></a>Recupero di dati

Il `HttpClient.GetAsync` metodo viene utilizzato per inviare la richiesta GET al servizio Web specificato dall'URI, quindi ricevere la risposta dal servizio Web, come illustrato nell'esempio di codice seguente:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));
  ...
  HttpResponseMessage response = await client.GetAsync (uri);
  if (response.IsSuccessStatusCode)
  {
      string content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

Il servizio REST Invia un codice di stato HTTP nella `HttpResponseMessage.IsSuccessStatusCode` proprietà per indicare se la richiesta HTTP ha avuto esito positivo o negativo. Per questa operazione, il servizio REST invia il codice di stato HTTP 200 (OK) nella risposta, che indica che la richiesta è riuscita e che le informazioni richieste sono nella risposta.

Se l'operazione HTTP ha avuto esito positivo, viene letto il contenuto della risposta per la visualizzazione. La `HttpResponseMessage.Content` proprietà rappresenta il contenuto della risposta http e il `HttpContent.ReadAsStringAsync` metodo scrive in modo asincrono il contenuto HTTP in una stringa. Questo contenuto viene quindi convertito da JSON in un `List` di `TodoItem` istanze di.

> [!WARNING]
> L'utilizzo del `ReadAsStringAsync` metodo per recuperare una risposta di grandi dimensioni può avere un impatto negativo sulle prestazioni. In questi casi, la risposta deve essere deserializzata direttamente per evitare che venga completamente memorizzata nel buffer.

### <a name="creating-data"></a>Creazione di dati

Il `HttpClient.PostAsync` metodo viene utilizzato per inviare la richiesta post al servizio Web specificato dall'URI e quindi per ricevere la risposta dal servizio Web, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));

  ...
  string json = JsonConvert.SerializeObject (item);
  StringContent content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem)
  {
    response = await client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully saved.");
  }
  ...
}
```

L' `TodoItem` istanza viene convertita in un payload JSON per l'invio al servizio Web. Questo payload viene quindi incorporato nel corpo del contenuto HTTP che verrà inviato al servizio Web prima che la richiesta venga effettuata con il `PostAsync` metodo.

Il servizio REST Invia un codice di stato HTTP nella `HttpResponseMessage.IsSuccessStatusCode` proprietà per indicare se la richiesta HTTP ha avuto esito positivo o negativo. Le risposte comuni per questa operazione sono:

- **201 (creato)** : la richiesta ha causato la creazione di una nuova risorsa prima dell'invio della risposta.
- **400 (richiesta non valida)** : la richiesta non è riconosciuta dal server.
- **409 (conflitto)** : Impossibile eseguire la richiesta a causa di un conflitto nel server.

### <a name="updating-data"></a>Aggiornamento dei dati

Il `HttpClient.PutAsync` metodo viene utilizzato per inviare la richiesta PUT al servizio Web specificato dall'URI, quindi ricevere la risposta dal servizio Web, come illustrato nell'esempio di codice seguente:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```

L'operazione del `PutAsync` metodo è identica al `PostAsync` metodo utilizzato per la creazione di dati nel servizio Web. Tuttavia, le risposte possibili inviate dal servizio Web sono diverse.

Il servizio REST Invia un codice di stato HTTP nella `HttpResponseMessage.IsSuccessStatusCode` proprietà per indicare se la richiesta HTTP ha avuto esito positivo o negativo. Le risposte comuni per questa operazione sono:

- **204 (nessun contenuto)** : la richiesta è stata elaborata correttamente e la risposta è intenzionalmente vuota.
- **400 (richiesta non valida)** : la richiesta non è riconosciuta dal server.
- **404 (non trovato)** : la risorsa richiesta non esiste nel server.

### <a name="deleting-data"></a>Eliminazione di dati

Il `HttpClient.DeleteAsync` metodo viene utilizzato per inviare la richiesta DELETE al servizio Web specificato dall'URI, quindi ricevere la risposta dal servizio Web, come illustrato nell'esempio di codice seguente:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, id));
  ...
  HttpResponseMessage response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully deleted.");
  }
  ...
}
```

Il servizio REST Invia un codice di stato HTTP nella `HttpResponseMessage.IsSuccessStatusCode` proprietà per indicare se la richiesta HTTP ha avuto esito positivo o negativo. Le risposte comuni per questa operazione sono:

- **204 (nessun contenuto)** : la richiesta è stata elaborata correttamente e la risposta è intenzionalmente vuota.
- **400 (richiesta non valida)** : la richiesta non è riconosciuta dal server.
- **404 (non trovato)** : la risorsa richiesta non esiste nel server.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di servizi back-end per applicazioni native per dispositivi mobili](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
