---
title: Accesso ai dati remoti
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0f29377c52dcf65247c11079178efe0745c654eb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="accessing-remote-data"></a>Accesso ai dati remoti

Molte soluzioni moderne basate sul web utilizzo di servizi web ospitati in server web, per fornire funzionalità di client remoti applicazioni. Le operazioni che espone un servizio web costituiscano un'API web.

Le applicazioni client devono essere in grado di utilizzare l'API web senza conoscere la modalità di implementazione delle operazioni che espone l'API o i dati. È necessario che l'API aderito standard comune che abilita un servizio web e dell'app client concordare i formati di dati da utilizzare e la struttura dei dati scambiati tra le applicazioni client e il servizio web.

## <a name="introduction-to-representational-state-transfer"></a>Introduzione a Representational State Transfer

Representational State Transfer (REST) è uno stile per la creazione di sistemi distribuiti in base a hypermedia architetturale. Dei principali vantaggi del modello REST è che è basata su standard aperti e non viene associato l'implementazione del modello o le applicazioni client di diritti di accesso per qualsiasi implementazione specifica. Pertanto, potrebbe essere implementato un servizio web REST mediante Microsoft ASP.NET MVC di base e Impossibile sviluppo di applicazioni client utilizzando qualsiasi linguaggio e un set di strumenti che possono generare richieste HTTP e analizzare le risposte HTTP.

Il modello REST utilizza uno schema per la navigazione per rappresentare gli oggetti e servizi in una rete, detta risorse. Sistemi che implementano REST in genere utilizzano il protocollo HTTP per trasmettere le richieste per accedere a tali risorse. In questi sistemi, un'applicazione client invia una richiesta sotto forma di un URI che identifica una risorsa e un metodo HTTP (ad esempio, GET, POST, PUT o DELETE) che indica l'operazione da eseguire su tale risorsa. Il corpo della richiesta HTTP contiene tutti i dati necessari per eseguire l'operazione.

> [!NOTE]
> REST definisce un modello di richiesta senza stato. Pertanto, le richieste HTTP devono essere indipendente e potrebbero verificarsi in qualsiasi ordine.

La risposta da un resto richiesta fa uso di codici di stato HTTP standard. Ad esempio, una richiesta che restituisce dati valido deve includere il codice di risposta HTTP 200 (OK), mentre una richiesta che non riesce a trovare o eliminare una risorsa specificata deve restituire una risposta che include il codice di stato HTTP 404 (non trovato).

Un'API web RESTful espone un set di risorse collegate e fornisce le operazioni di base che consentono a un'app di gestire tali risorse e passare facilmente tra di essi. Per questo motivo, gli URI che costituiscono un'API web RESTful tipiche sono orientate verso i dati che espone e utilizza la funzionalità fornita da HTTP a operare su questi dati.

I dati inclusi per un'applicazione client in una richiesta HTTP e i messaggi di risposta corrispondente dal server web, potrebbero essere presentati in una vasta gamma di formati, noti come tipi di supporto. Quando un'applicazione client invia una richiesta che restituisce i dati nel corpo di un messaggio, è possibile specificare i tipi di supporto che è possibile gestire nel `Accept` intestazione della richiesta. Se il server web supporta questo tipo di supporto, può rispondere con una risposta che include il `Content-Type` intestazione che specifica il formato dei dati nel corpo del messaggio. È responsabilità dell'applicazione client per analizzare il messaggio di risposta e interpretare i risultati nel corpo del messaggio in modo appropriato.

Per ulteriori informazioni su REST, vedere [progettazione API](/azure/architecture/best-practices/api-design/) e [implementazione delle API](/azure/architecture/best-practices/api-implementation/).

## <a name="consuming-restful-apis"></a>Utilizzo di API REST

L'app mobile eShopOnContainers utilizza il modello Model-View-ViewModel (MVVM) e gli elementi del modello di rappresentano il modello le entità di dominio utilizzate nell'app. Le classi controller e del repository dell'applicazione di riferimento eShopOnContainers accettano e restituiscono molti di questi oggetti modello. Pertanto, vengono utilizzati come dati trasferimento oggetti DTO che contengono tutti i dati passati tra le app per dispositivi mobili e di microservizi nei contenitori. Il principale vantaggio dell'utilizzo di DTO per passare i dati e ricevere dati da un servizio web è mediante la trasmissione di più dati in una singola chiamata remota, l'app può ridurre il numero di chiamate remote che devono essere apportate.

### <a name="making-web-requests"></a>Esecuzione di richieste Web

Gli utilizzi di app per dispositivi mobili eShopOnContainers la `HttpClient` classe per effettuare richieste su HTTP, con JSON viene utilizzato come tipo di supporto. Questa classe fornisce funzionalità per l'invio in modo asincrono le richieste HTTP e la ricezione di risposte HTTP da un URI di risorsa identificato. La `HttpResponseMessage` classe rappresenta un messaggio di risposta HTTP ricevuto da un'API REST, dopo aver effettuata una richiesta HTTP. Contiene informazioni sulla risposta, incluso il codice di stato, intestazioni e un corpo. Il `HttpContent` classe rappresenta il corpo HTTP e le intestazioni del contenuto, ad esempio `Content-Type` e `Content-Encoding`. Il contenuto può essere letto utilizzando uno qualsiasi del `ReadAs` metodi, ad esempio `ReadAsStringAsync` e `ReadAsByteArrayAsync`, a seconda del formato dei dati.

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>Effettua una richiesta GET

La `CatalogService` classe viene utilizzata per gestire il processo di recupero di dati da microservizio il catalogo. Nel `RegisterDependencies` metodo nel `ViewModelLocator` (classe), il `CatalogService` classe viene registrata come un mapping dei tipi di base di `ICatalogService` tipo con il contenitore dell'inserimento di dipendenza Autofac. Quindi, quando un'istanza del `CatalogViewModel` viene creata una classe, il costruttore accetta un `ICatalogService` tipo, che viene risolta Autofac, restituisce un'istanza di `CatalogService` classe. Per ulteriori informazioni sull'inserimento di dipendenze, vedere [introduzione Dependency Injection](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Nella figura 10-1 viene illustrata l'interazione tra le classi che leggono i dati del catalogo da microservizio il catalogo per la visualizzazione per il `CatalogView`.

[![](accessing-remote-data-images/catalogdata.png "Recupero di dati da microservizio il catalogo")](accessing-remote-data-images/catalogdata-large.png#lightbox "il recupero dei dati da microservizio il catalogo")

**Nella figura 10-1**: recupero di dati da microservizio il catalogo

Quando il `CatalogView` , ci si sposta il `OnInitialize` metodo nel `CatalogViewModel` classe viene chiamata. Questo metodo recupera i dati del catalogo da microservizio il catalogo, come illustrato nell'esempio di codice seguente:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

Questo metodo chiama il `GetCatalogAsync` metodo il `CatalogService` istanza che era stato inserito nel `CatalogViewModel` da Autofac. Nell'esempio di codice riportato di seguito viene illustrato il `GetCatalogAsync` metodo:

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

Questo metodo compila l'URI che identifica la risorsa verrà inviata per la richiesta e utilizza il `RequestProvider` classe per richiamare il metodo HTTP GET sulla risorsa, prima di restituire i risultati di `CatalogViewModel`. La `RequestProvider` classe contiene una funzionalità che invia una richiesta sotto forma di un URI che identifica una risorsa, un metodo HTTP che indica l'operazione da eseguire su tale risorsa, e un corpo contenente tutti i dati necessari per eseguire l'operazione. Per informazioni su come `RequestProvider` classe, viene inserita nel `CatalogService class`, vedere [introduzione Dependency Injection](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Nell'esempio di codice riportato di seguito viene illustrato il `GetAsync` metodo la `RequestProvider` classe:

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Questo metodo chiama il `CreateHttpClient` metodo, che restituisce un'istanza di `HttpClient` classe con il set di intestazioni appropriate. Viene quindi inviato una richiesta GET asincrona alla risorsa identificata dall'URI, con la risposta viene memorizzata nel `HttpResponseMessage` istanza. Il `HandleResponse` viene quindi richiamato metodo, che genera un'eccezione se la risposta non include un codice di stato HTTP esito positivo. Quindi la risposta viene letto come una stringa, convertita da JSON a un `CatalogRoot` dell'oggetto e restituito il `CatalogService`.

Il `CreateHttpClient` metodo è illustrato nell'esempio di codice seguente:

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

Questo metodo crea una nuova istanza del `HttpClient` classe e imposta il `Accept` intestazione delle richieste effettuate dal `HttpClient` istanza `application/json`, che indica che è previsto che il contenuto di una risposta da formattare l'uso di JSON. Quindi, se un token di accesso è stato passato come argomento per il `CreateHttpClient` (metodo), viene aggiunto al `Authorization` intestazione delle richieste effettuate dal `HttpClient` istanza, con la stringa di prefisso `Bearer`. Per ulteriori informazioni sull'autorizzazione, vedere [autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Quando il `GetAsync` metodo nel `RequestProvider` classe chiamate `HttpClient.GetAsync`, `Items` metodo nel `CatalogController` classe nel progetto Catalog.API viene richiamato, mostrato nell'esempio di codice seguente:

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

Questo metodo recupera i dati del catalogo dal database SQL mediante EntityFramework e li restituisce come un messaggio di risposta che include un codice di stato HTTP di successo e formattati di una raccolta di JSON `CatalogItem` istanze.

#### <a name="making-a-post-request"></a>Effettua una richiesta POST

La `BasketService` classe viene utilizzata per gestire il recupero di dati e processo di aggiornamento con microservizio il carrello. Nel `RegisterDependencies` metodo nel `ViewModelLocator` (classe), il `BasketService` classe viene registrata come un mapping dei tipi di base di `IBasketService` tipo con il contenitore dell'inserimento di dipendenza Autofac. Quindi, quando un'istanza del `BasketViewModel` viene creata una classe, il costruttore accetta un `IBasketService` tipo, che viene risolta Autofac, restituisce un'istanza di `BasketService `classe. Per ulteriori informazioni sull'inserimento di dipendenze, vedere [introduzione Dependency Injection](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Figura 10-2 viene illustrata l'interazione delle classi che inviano i dati del carrello visualizzati il `BasketView`, per microservizio il carrello.

[![](accessing-remote-data-images/basketdata.png "L'invio di dati microservizio il carrello")](accessing-remote-data-images/basketdata-large.png#lightbox "l'invio di dati microservizio il carrello")

**Figura 10-2**: invio di dati microservizio il carrello

Quando viene aggiunto un elemento al carrello degli acquisti, il `ReCalculateTotalAsync` metodo la `BasketViewModel` classe viene chiamata. Questo metodo aggiorna il valore totale di elementi tra gli acquisti e invia i dati del carrello microservizio il carrello, come illustrato nell'esempio di codice seguente:

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

Questo metodo chiama il `UpdateBasketAsync` metodo il `BasketService` istanza che era stato inserito nel `BasketViewModel` da Autofac. Il metodo seguente viene illustrato il `UpdateBasketAsync` metodo:

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

Questo metodo compila l'URI che identifica la risorsa verrà inviata per la richiesta e utilizza il `RequestProvider` classe per richiamare il metodo HTTP POST sulla risorsa, prima di restituire i risultati di `BasketViewModel`. Si noti che un token di accesso, ottenuti da IdentityServer durante il processo di autenticazione, è necessario per autorizzare le richieste per microservizio il carrello. Per ulteriori informazioni sull'autorizzazione, vedere [autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

L'esempio di codice seguente mostra uno del `PostAsync` metodi di `RequestProvider` classe:

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Questo metodo chiama il `CreateHttpClient` metodo, che restituisce un'istanza di `HttpClient` classe con il set di intestazioni appropriate. Viene quindi inviato una richiesta POST asincrona alla risorsa identificata dall'URI, con i dati serializzati basket inviati in formato JSON e la risposta viene memorizzato nel `HttpResponseMessage` istanza. Il `HandleResponse` viene quindi richiamato metodo, che genera un'eccezione se la risposta non include un codice di stato HTTP esito positivo. Quindi, è di lettura della risposta come stringa, convertita da JSON a un `CustomerBasket` dell'oggetto e restituito il `BasketService`. Per ulteriori informazioni sul `CreateHttpClient` metodo, vedere [effettua una richiesta di ottenere](#making_a_get_request).

Quando il `PostAsync` metodo nel `RequestProvider` classe chiamate `HttpClient.PostAsync`, `Post` metodo nel `BasketController` classe nel progetto Basket.API viene richiamato, mostrato nell'esempio di codice seguente:

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

Questo metodo utilizza un'istanza di `RedisBasketRepository` classe per rendere persistenti i dati di acquisti per la cache Redis e lo restituisce come un messaggio di risposta che include un codice di stato HTTP esito positivo e un formato JSON formattati `CustomerBasket` istanza.

#### <a name="making-a-delete-request"></a>Effettua una richiesta di eliminazione

Nella figura 10-3 mostra le interazioni di eliminare i dati del carrello microservizio il carrello, per le classi di `CheckoutView`.

![](accessing-remote-data-images/checkoutdata.png "Dati l'eliminazione da microservizio il carrello")

**Nella figura 10-3**: eliminazione di dati da microservizio il carrello

Quando viene richiamato il processo di estrazione, la `CheckoutAsync` metodo la `CheckoutViewModel` classe viene chiamata. Questo metodo crea un nuovo ordine prima di eliminare il carrello acquisto, come illustrato nell'esempio di codice seguente:

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

Questo metodo chiama il `ClearBasketAsync` metodo il `BasketService` istanza che era stato inserito nel `CheckoutViewModel` da Autofac. Il metodo seguente viene illustrato il `ClearBasketAsync` metodo:

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

Questo metodo compila l'URI che identifica la risorsa che verrà inviata per la richiesta e utilizza la `RequestProvider` classe per richiamare il metodo HTTP DELETE sulla risorsa. Si noti che un token di accesso, ottenuti da IdentityServer durante il processo di autenticazione, è necessario per autorizzare le richieste per microservizio il carrello. Per ulteriori informazioni sull'autorizzazione, vedere [autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Nell'esempio di codice riportato di seguito viene illustrato il `DeleteAsync` metodo la `RequestProvider` classe:

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

Questo metodo chiama il `CreateHttpClient` metodo, che restituisce un'istanza di `HttpClient` classe con il set di intestazioni appropriate. Quindi, invia una richiesta di eliminazione asincrona alla risorsa identificata dall'URI. Per ulteriori informazioni sul `CreateHttpClient` metodo, vedere [effettua una richiesta di ottenere](#making_a_get_request).

Quando il `DeleteAsync` metodo nel `RequestProvider` classe chiamate `HttpClient.DeleteAsync`, `Delete` metodo nel `BasketController` classe nel progetto Basket.API viene richiamato, mostrato nell'esempio di codice seguente:

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

Questo metodo utilizza un'istanza di `RedisBasketRepository` classe per eliminare i dati del carrello dalla cache Redis.

## <a name="caching-data"></a>Memorizzazione di dati nella cache

Per migliorare le prestazioni di un'app, è possono la memorizzazione nella cache i dati utilizzati di frequente per l'archiviazione veloce che si trova chiudere l'app. Se l'archiviazione veloce trova più vicino all'app da quello di origine originale, allora la memorizzazione nella cache può migliorare significativamente risposta volte durante il recupero dei dati.

La forma più comune di memorizzazione nella cache è Read-through la memorizzazione nella cache, in cui un'applicazione recupera dati facendo riferimento a cache. Se i dati non sono nella cache, dispone di recuperato dall'archivio dati e aggiunto alla cache. Le applicazioni possono implementare la memorizzazione nella cache di read-through con il modello cache-aside. Questo modello determina se l'elemento è attualmente nella cache. Se l'elemento non è nella cache, letto dall'archivio dati e aggiunto alla cache. Per ulteriori informazioni, vedere il [Cache-Aside](/azure/architecture/patterns/cache-aside/) modello.

> [!TIP]
> Memorizzare nella cache di dati che vengono letti frequentemente e che vengono modificati raramente. Questi dati possono essere aggiunti nella cache su richiesta, la prima volta che viene recuperato da un'app. Ciò significa che l'applicazione deve recuperare i dati una sola volta dall'archivio dati e che gli accessi successivi possono essere soddisfatti tramite la cache.

Le applicazioni distribuite, ad esempio il eShopOnContainers fanno riferimento l'applicazione, devono fornire una o entrambe le cache seguenti:

-   Una cache condivisa, è possibile accedere da più processi o computer.
-   Cache privato, in cui i dati sono contenuti in locale sul dispositivo che esegue l'app.

L'app mobile eShopOnContainers Usa una cache privata, in cui vengono conservati dati localmente nel dispositivo che esegue un'istanza dell'app. Per informazioni sulla cache utilizzata dall'applicazione eShopOnContainers riferimento, vedere [Microservizi .NET: architettura per le applicazioni .NET contenitore](https://aka.ms/microservicesebook).

> [!TIP]
> La cache può essere paragonato a un archivio dati temporaneo che sparisce in qualsiasi momento. Verificare che i dati vengono gestiti nell'archivio dati originale, nonché la cache. Quindi è ridotto al minimo il rischio di perdere dati se la cache non è più disponibile.

### <a name="managing-data-expiration"></a>La gestione della scadenza di dati

Non è possibile prevedere che i dati memorizzati nella cache sarà sempre coerenti con i dati originali. I dati nell'archivio dati originale potrebbero cambiare dopo che è stata memorizzata nella cache, per risultino non aggiornati i dati memorizzati nella cache. Pertanto, le app devono implementare una strategia che contribuisce a garantire che i dati nella cache viene aggiornati, ma può anche rilevare e gestire situazioni che si verificano quando i dati nella cache sono diventata non aggiornati. La memorizzazione nella cache più meccanismi abilitano la cache da configurare per la data di scadenza e pertanto di ridurre il periodo per il quale i dati potrebbero essere obsoleti.

> [!TIP]
> Impostare una scadenza predefinita di tempo quando si configura una cache. Cache molti implementano scadenza, che invalida i dati e la rimuove dalla cache, se non è disponibile per un periodo specificato. Tuttavia, prestare attenzione quando si sceglie il periodo di scadenza. Se viene resa troppo breve, scadono troppo rapidamente dati e i vantaggi della memorizzazione nella cache verranno ridotta. Se troppo lungo, obsolescenza dei rischi di dati. Pertanto, l'ora di scadenza deve corrispondere al modello di accesso per le applicazioni che utilizzano i dati.

Quando scadono i dati memorizzati nella cache, deve essere rimosso dalla cache e l'applicazione deve recuperare i dati dai dati originali archiviano e inserirlo nuovamente nella cache.

È anche possibile che una cache potrebbe esaurirsi se sono consentito che rimangono per molto tempo i dati. Pertanto, le richieste per aggiungere nuovi elementi alla cache potrebbero essere necessario rimuovere alcuni elementi in un processo noto come *rimozione*. Servizi di memorizzazione nella cache in genere la rimozione di dati utilizzati meno di recente. Tuttavia, esistono altri criteri di eliminazione, tra cui utilizzati più di recente e first-in-first-out. Per ulteriori informazioni, vedere [la memorizzazione nella cache indicazioni](/azure/architecture/best-practices/caching/).

<a name="caching_images" />

### <a name="caching-images"></a>La memorizzazione nella cache delle immagini

L'app mobile eShopOnContainers utilizza immagini remoto del prodotto che traggono vantaggio dalla memorizzazione nella cache. Queste immagini vengono visualizzate per il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) (controllo) e `CachedImage` fornito dal [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) libreria.

Di xamarin. Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) controllo supporta la memorizzazione nella cache delle immagini scaricate. La memorizzazione nella cache è abilitata per impostazione predefinita e archivierà l'immagine localmente per 24 ore. Inoltre, l'ora di scadenza può essere configurato con il [ `CacheValidity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) proprietà. Per ulteriori informazioni, vedere [memorizzazione nella cache di immagine scaricato](~/xamarin-forms/user-interface/images.md#Image_Caching).

Del FFImageLoading `CachedImage` controllo è una sostituzione per il xamarin. Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) (controllo), che fornisce le proprietà aggiuntive che consentono la funzionalità supplementari. Tra questa funzionalità, il controllo fornisce configurabile la memorizzazione nella cache, durante il supporto di errore e il caricamento di segnaposto dell'immagine. Esempio di codice seguente viene illustrato l'utilizzo dell'app mobile eShopOnContainers il `CachedImage` controllo il `ProductTemplate`, che è il modello di dati utilizzato dal [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controllare nel `CatalogView`:

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP, WinRT, WinPhone" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP, WinRT, WinPhone" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

Il `CachedImage` controllo imposta il `LoadingPlaceholder` e `ErrorPlaceholder` proprietà alle immagini specifiche della piattaforma. Il `LoadingPlaceholder` proprietà specifica l'immagine da visualizzare durante l'immagine specificata per il `Source` viene recuperata una proprietà e `ErrorPlaceholder` proprietà specifica l'immagine da visualizzare se si verifica un errore durante il tentativo di recuperare l'immagine specificato per il `Source` proprietà.

Come suggerisce il nome, il `CachedImage` controllo memorizza nella cache delle immagini remote sul dispositivo per il tempo specificato dal valore della `CacheDuration` proprietà. Quando il valore della proprietà non è impostato in modo esplicito, viene applicato il valore predefinito di 30 giorni.

## <a name="increasing-resilience"></a>Incremento di resilienza

Tutte le app che comunicano con servizi remoti e le risorse devono essere riservate per gli errori temporanei. Gli errori temporanei includono la perdita temporanea della connettività di rete per servizi, la temporanea indisponibilità di un servizio o un timeout che si verificano quando un servizio è occupato. Questi errori sono spesso risolvono automaticamente e se l'azione viene ripetuto dopo un ritardo appropriato è probabilmente avranno esito positivo.

Gli errori temporanei possono avere un impatto notevole sulla qualità percepita di un'app, anche se è stata testata con attenzione in tutte le circostanze. Per garantire che un'applicazione che comunica con i servizi remoti opera in modo affidabile, deve essere in grado di eseguire tutte le operazioni seguenti:

-   Quando si verificano e determinare se gli errori possono essere temporanei, rilevare gli errori.
-   Ripetere l'operazione se viene rilevato che il messaggio di errore è probabilmente essere temporaneo e tenere traccia del numero di volte in cui che è stata ritentata l'operazione.
-   Usare una strategia di tentativi appropriata, che specifica il numero di tentativi, il ritardo tra ogni tentativo e le azioni da eseguire dopo un tentativo non riuscito.

La gestione degli errori temporanei può essere ottenuto eseguendo il wrapping di tutti i tentativi di accedere a un servizio remoto nel codice che implementa il pattern di tentativi.

### <a name="retry-pattern"></a>Ripetere modello

Se un'applicazione rileva un errore durante il tentativo di inviare una richiesta a un servizio remoto, è possibile gestire l'errore in uno dei modi seguenti:

-   Ripetere l'operazione. L'app può ritentare la richiesta di errore immediatamente.
-   Riprovare dopo un ritardo. L'applicazione deve attendere per un periodo di tempo prima di ritentare la richiesta adatto.
-   L'annullamento dell'operazione. L'applicazione deve annullare l'operazione e un'eccezione del report.

La strategia di tentativi deve essere ottimizzata in base ai requisiti di business dell'app. Ad esempio, è importante ottimizzare il numero di tentativi e ripetere l'intervallo per l'operazione. Se l'operazione fa parte di un'interazione utente, l'intervallo tra tentativi deve essere breve e solo alcuni tentativi per evitare di rendere gli utenti di attendere una risposta. Se l'operazione fa parte di un flusso di lavoro di lunga esecuzione, in cui l'annullamento o il riavvio del flusso di lavoro è dispendiosa o lunghi, è opportuno attendere più tra i tentativi e ripetere più volte.

> [!NOTE]
> Una strategia aggressiva tentativi con ritardo minimo tra tentativi e un numero elevato di tentativi, potrebbe influire negativamente su un servizio remoto che esegue chiudere o capacità. Inoltre, una strategia di ripetizione influenzare anche la velocità di risposta dell'app se continuamente tenta di eseguire un'operazione di failover.

Se una richiesta non riesce dopo un numero di tentativi, è preferibile per l'app per impedire ulteriori richieste di passare alla stessa risorsa nonché per segnalare un errore. Quindi, dopo un determinato periodo, l'app può effettuare una o più richieste per la risorsa per vedere se si ha esito positivo. Per ulteriori informazioni, vedere [modello di interruttore](#circuit_breaker_pattern).

> [!TIP]
> Non implementare un meccanismo di ciclo infinito di tentativi. Utilizzare un numero finito di tentativi, oppure implementare il [interruttore](/azure/architecture/patterns/circuit-breaker/) modello per consentire a un servizio da recuperare.

L'app mobile eShopOnContainers non implementa attualmente il criterio di ripetizione quando si effettuano richieste web RESTful. Tuttavia, il `CachedImage` al controllo, tramite il [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) libreria supporta la gestione degli errori temporanei con un nuovo tentativo di caricamento dell'immagine. Se il caricamento dell'immagine non riesce, verrà effettuata ulteriore tentativo. Il numero di tentativi è specificato dal `RetryCount` proprietà e i tentativi verranno eseguito dopo un intervallo di tempo specificato per il `RetryDelay` proprietà. Questi valori di proprietà non è impostato in modo esplicito, valori predefiniti vengono applicati i valori: 3 per il `RetryCount` , proprietà e 250ms per il `RetryDelay` proprietà. Per ulteriori informazioni sul `CachedImage` di controllo, vedere [la memorizzazione nella cache di immagini](#caching_images).

L'applicazione di riferimento eShopOnContainers implementare il modello di tentativi. Per ulteriori informazioni, inclusi una descrizione delle modalità combinare il criterio di ripetizione con la `HttpClient` classe, vedere [Microservizi .NET: architettura per le applicazioni .NET contenitore](https://aka.ms/microservicesebook).

Per ulteriori informazioni sul modello di ripetizione, vedere il [ripetere](/azure/architecture/patterns/retry/) modello.

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>Modello di interruttore

In alcuni casi, gli errori possono verificarsi a causa di eventi previsti che richiedono più tempo per risolvere. Questi errori possono variare da una perdita parziale di connettività per l'errore completo di un servizio. In questi casi, è inutile per un'app ripetere un'operazione che è improbabile che abbia esito positivo e invece deve accettare che l'operazione non è riuscita e gestire questo errore, di conseguenza.

Il modello di interruttore può impedire a un'app più volte durante il tentativo di eseguire un'operazione che potrebbe non riuscire, consentendo anche all'app di rilevare se il messaggio di errore è stato risolto.

> [!NOTE]
> Lo scopo del modello di interruttore è diverso dal criterio di ripetizione. Il criterio di ripetizione consente a un'applicazione ripetere un'operazione nella previsione che essa verrà esito positivo. Il modello di interruttore impedisce all'app eseguendo un'operazione che potrebbe non riuscire.

Un interruttore funge da proxy per le operazioni che potrebbe non riuscire. Il proxy deve monitorare il numero di errori recenti che si sono verificati e usare queste informazioni per decidere se consentire l'operazione per continuare, o per restituire immediatamente un'eccezione.

Attualmente l'app mobile eShopOnContainers non implementa il modello di interruttore. Tuttavia, non il eShopOnContainers. Per ulteriori informazioni, vedere [Microservizi .NET: architettura per le applicazioni .NET contenitore](https://aka.ms/microservicesebook).

> [!TIP]
> Combinare i modelli di tentativi e interruttore. Un'app è possibile combinare i modelli di tentativi e interruttore con il criterio di ripetizione per richiamare un'operazione tramite un interruttore. Tuttavia, la logica di riesecuzione deve essere sensibili alle eventuali eccezioni restituite dall'interruttore e abbandona tentativi se l'interruttore indica che un errore non è temporaneo.

Per ulteriori informazioni sul modello di interruttore, vedere il [interruttore](/azure/architecture/patterns/circuit-breaker/) modello.

## <a name="summary"></a>Riepilogo

Molte soluzioni moderne basate sul web utilizzo di servizi web ospitati in server web, per fornire funzionalità di client remoti applicazioni. Le operazioni che espone un servizio web costituiscano un'API web e applicazioni client devono essere in grado di utilizzare l'API web senza conoscere la modalità di implementazione delle operazioni che espone l'API o i dati.

Per migliorare le prestazioni di un'app, è possono la memorizzazione nella cache i dati utilizzati di frequente per l'archiviazione veloce che si trova chiudere l'app. Le applicazioni possono implementare la memorizzazione nella cache di read-through con il modello cache-aside. Questo modello determina se l'elemento è attualmente nella cache. Se l'elemento non è nella cache, letto dall'archivio dati e aggiunto alla cache.

Quando si comunica con l'API web, App devono essere riservate per gli errori temporanei. Gli errori temporanei includono la perdita temporanea della connettività di rete per servizi, la temporanea indisponibilità di un servizio o un timeout che si verificano quando un servizio è occupato. Questi errori sono spesso risolvono automaticamente e se l'azione viene ripetuto dopo un ritardo adatto, è probabile che venga eseguita correttamente. Pertanto, le app devono eseguire il wrapping di tutti i tentativi di accedere a un'API web nel codice che implementa un meccanismo di gestione di errori temporanei.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
