---
title: Accesso ai dati remoti
description: In questo capitolo viene illustrato come l'app per dispositivi mobili di eShopOnContainers accede ai dati dei microservizi in contenitori.
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 3a46b939fa87cd6535c9f86c46981c098542e7c9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105480"
---
# <a name="accessing-remote-data"></a>Accesso ai dati remoti

Molte soluzioni moderne basate sul web rendere l'uso dei servizi web, ospitate dai server web, per fornire funzionalità per client remoto delle applicazioni. Le operazioni che espone un servizio web costituiscono un'API web.

Le app client devono essere in grado di usare l'API web senza conoscere come vengono implementate i dati o le operazioni che espone l'API. Ciò richiede che l'API deve rispettare gli standard comuni che consentono a un servizio web e app client di concordare i formati di dati da utilizzare e la struttura dei dati scambiati tra le app client e il servizio web.

## <a name="introduction-to-representational-state-transfer"></a>Introduzione a Representational State Transfer

Representational State Transfer (REST) è uno stile architetturale per la creazione di sistemi distribuiti basati su ipermedia. Dei principali vantaggi del modello REST è che è basata su standard aperti e non vincola l'implementazione del modello o le app client che vi accedono a qualsiasi implementazione specifica. Pertanto, un servizio web REST può essere implementato usando Microsoft ASP.NET Core MVC e può sviluppare le app client usando qualsiasi linguaggio e set di strumenti che possono generare richieste HTTP e analizzare risposte HTTP.

Il modello REST usa uno schema di spostamento per rappresentare gli oggetti e i servizi in una rete, definita come risorse. I sistemi che implementano REST in genere utilizzano il protocollo HTTP per trasmettere le richieste per accedere alle risorse. In questi sistemi, un'app client invia una richiesta sotto forma di un URI che identifica una risorsa e un metodo HTTP (ad esempio GET, POST, PUT o DELETE) che indica l'operazione da eseguire su tale risorsa. Il corpo della richiesta HTTP contiene tutti i dati necessari per eseguire l'operazione.

> [!NOTE]
> REST definisce un modello di richiesta senza stato. Di conseguenza, le richieste HTTP devono essere indipendenti e potrebbero verificarsi in qualsiasi ordine.

La risposta da un REST richiesta fa uso di codici di stato HTTP standard. Ad esempio, una richiesta che restituisce dati validi deve includere il codice di risposta HTTP 200 (OK), mentre una richiesta che non riesce a trovare o eliminare una risorsa specificata deve restituire una risposta che include il codice di stato HTTP 404 (non trovato).

Un'API web RESTful espone un set di risorse connesse e fornisce le operazioni principali che consentono a un'app manipolare tali risorse e spostarsi facilmente tra loro. Per questo motivo, gli URI che costituiscono un'API web RESTful standard sono orientati ai dati che espone e utilizzare le funzionalità fornite da HTTP a operare su questi dati.

I dati inclusi per un'app client in una richiesta HTTP e i messaggi di risposta corrispondente dal server web, possono essere presentati in una vasta gamma di formati, noti come tipi di supporto. Quando un'app client invia una richiesta che restituisce i dati nel corpo di un messaggio, può specificare i tipi di supporto che possono essere gestite nel `Accept` intestazione della richiesta. Se il server web supporta questo tipo di supporto, può rispondere con una risposta che include il `Content-Type` intestazione che specifica il formato dei dati nel corpo del messaggio. È quindi responsabilità dell'app client per analizzare il messaggio di risposta e interpretare i risultati nel corpo del messaggio in modo appropriato.

Per altre informazioni su REST, vedere [la progettazione delle API](/azure/architecture/best-practices/api-design/) e [implementazione API](/azure/architecture/best-practices/api-implementation/).

## <a name="consuming-restful-apis"></a>Utilizzare le API RESTful

L'app per dispositivi mobili di eShopOnContainers Usa il modello Model-View-ViewModel (MVVM) e gli elementi del modello di rappresentano il modello le entità di dominio usate nell'app. Le classi controller e del repository nell'applicazione di riferimento eShopOnContainers accettano e restituiscono molti di questi oggetti modello. Pertanto, vengono utilizzati come oggetti di trasferimento dei dati (DTO) che contengono tutti i dati passati tra le app per dispositivi mobili e i microservizi in contenitori. Il principale vantaggio dell'uso di oggetti DTO per passare i dati e ricevere dati da un servizio web è mediante la trasmissione di più dati in una singola chiamata remota, l'app può ridurre il numero di chiamate remote che è necessario apportare.

### <a name="making-web-requests"></a>Esecuzione di richieste Web

L'app per dispositivi mobili di eShopOnContainers Usa i `HttpClient` classi per effettuare richieste tramite HTTP, con JSON viene usato come tipo di supporto. Questa classe fornisce funzionalità per l'invio in modo asincrono le richieste HTTP e ricevere risposte HTTP da un URI di risorsa identificato. Il `HttpResponseMessage` classe rappresenta un messaggio di risposta HTTP ricevuto da un'API REST, dopo aver effettuata una richiesta HTTP. Contiene informazioni relative alla risposta, incluso il codice di stato, intestazioni e qualsiasi corpo. Il `HttpContent` classe rappresenta il corpo HTTP e le intestazioni del contenuto, ad esempio `Content-Type` e `Content-Encoding`. Il contenuto può essere letto utilizzando uno qualsiasi dei `ReadAs` metodi, ad esempio `ReadAsStringAsync` e `ReadAsByteArrayAsync`, a seconda del formato dei dati.

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>Effettua una richiesta GET

Il `CatalogService` classe viene utilizzata per gestire il processo di recupero dei dati dal microservizio catalog. Nel `RegisterDependencies` metodo nella `ViewModelLocator` (classe), il `CatalogService` classe registrata come un mapping dei tipi rispetto il `ICatalogService` tipo con il contenitore di inserimento delle dipendenze di Autofac. Quindi, quando un'istanza del `CatalogViewModel` accetta il relativo costruttore di classe viene creata, un' `ICatalogService` digita, che viene risolto Autofac, restituendo un'istanza del `CatalogService` classe. Per altre informazioni sull'inserimento delle dipendenze, vedere [Introduzione all'inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Figura 10-1 illustra l'interazione delle classi che leggono i dati del catalogo dal microservizio catalogo per la visualizzazione per il `CatalogView`.

[![](accessing-remote-data-images/catalogdata.png "Recupero di dati dal microservizio catalog")](accessing-remote-data-images/catalogdata-large.png#lightbox "il recupero dei dati dal microservizio catalogo")

**Figura 10-1**: recupero di dati dal microservizio catalogo

Quando il `CatalogView` si accede, il `OnInitialize` metodo nel `CatalogViewModel` classe viene chiamata. Questo metodo recupera i dati del catalogo il microservizio catalogo, come illustrato nell'esempio di codice seguente:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

Questo metodo chiama il `GetCatalogAsync` metodo del `CatalogService` istanza in cui è stata inserita nel `CatalogViewModel` da Autofac. Nell'esempio di codice riportato di seguito viene illustrato il `GetCatalogAsync` metodo:

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

Questo metodo genera l'URI che identifica la risorsa la richiesta verrà inviata a e Usa il `RequestProvider` classe per richiamare il metodo HTTP GET sulla risorsa, prima di restituire i risultati per il `CatalogViewModel`. Il `RequestProvider` classe contiene una funzionalità che invia una richiesta sotto forma di un URI che identifica una risorsa, un metodo HTTP che indica l'operazione da eseguire su tale risorsa, e un corpo che contiene tutti i dati necessari per eseguire l'operazione. Per informazioni sul modo in cui `RequestProvider` classe viene inserita la `CatalogService class`, vedere [Introduzione all'inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Nell'esempio di codice riportato di seguito viene illustrato il `GetAsync` metodo nel `RequestProvider` classe:

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Questo metodo chiama il `CreateHttpClient` metodo, che restituisce un'istanza del `HttpClient` classe con il set di intestazioni appropriate. Quindi invia una richiesta GET asincrona alla risorsa identificata dall'URI, con la risposta viene memorizzata nel `HttpResponseMessage` istanza. Il `HandleResponse` viene quindi richiamato metodo, che genera un'eccezione se la risposta non include un codice di stato HTTP esito positivo. Quindi la lettura della risposta come stringa, convertita da JSON a un `CatalogRoot` dell'oggetto e restituito il `CatalogService`.

Il `CreateHttpClient` metodo è illustrato nell'esempio di codice seguente:

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

Questo metodo crea una nuova istanza della `HttpClient` classe e imposta la `Accept` intestazione di tutte le richieste effettuate dal `HttpClient` istanza a `application/json`, che indica che è previsto che il contenuto di un'eventuale risposta essere formattati mediante JSON. Quindi, se un token di accesso è stato passato come argomento per il `CreateHttpClient` metodo, viene aggiunto al `Authorization` intestazione di tutte le richieste effettuate dalle `HttpClient` istanza, con la stringa di prefisso `Bearer`. Per altre informazioni sull'autorizzazione, vedere [autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Quando la `GetAsync` metodo nella `RequestProvider` classe chiamate `HttpClient.GetAsync`, il `Items` metodo nel `CatalogController` classe nel progetto Catalog. API viene richiamato, come illustrato nell'esempio di codice seguente:

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

Questo metodo recupera i dati del catalogo dal database SQL di mediante EntityFramework e lo restituisce come messaggio di risposta che include un codice di stato HTTP di successo e una raccolta di JSON formattato `CatalogItem` istanze.

#### <a name="making-a-post-request"></a>Effettua una richiesta POST

Il `BasketService` classe viene utilizzata per gestire il recupero di dati e processo di aggiornamento con il microservizio basket. Nel `RegisterDependencies` metodo nella `ViewModelLocator` (classe), il `BasketService` classe registrata come un mapping dei tipi rispetto il `IBasketService` tipo con il contenitore di inserimento delle dipendenze di Autofac. Quindi, quando un'istanza del `BasketViewModel` accetta il relativo costruttore di classe viene creata, un' `IBasketService` digita, che viene risolto Autofac, restituendo un'istanza del `BasketService `classe. Per altre informazioni sull'inserimento delle dipendenze, vedere [Introduzione all'inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Figura 10-2 viene illustrata l'interazione delle classi che inviano i dati del carrello visualizzati il `BasketView`, per il microservizio basket.

[![](accessing-remote-data-images/basketdata.png "L'invio di dati per il microservizio basket")](accessing-remote-data-images/basketdata-large.png#lightbox "l'invio di dati per il microservizio basket")

**Figura 10-2**: invio di dati per il microservizio basket

Quando un elemento viene aggiunto al carrello degli acquisti, il `ReCalculateTotalAsync` metodo nel `BasketViewModel` classe viene chiamata. Questo metodo aggiorna il valore totale degli elementi nel carrello e invia i dati sugli acquisti per il microservizio basket, come illustrato nell'esempio di codice seguente:

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

Questo metodo chiama il `UpdateBasketAsync` metodo del `BasketService` istanza in cui è stata inserita nel `BasketViewModel` da Autofac. Il metodo seguente viene illustrato il `UpdateBasketAsync` metodo:

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

Questo metodo genera l'URI che identifica la risorsa la richiesta verrà inviata a e Usa il `RequestProvider` classe per richiamare il metodo HTTP POST sulla risorsa, prima di restituire i risultati per il `BasketViewModel`. Si noti che un token di accesso ottenuti da IdentityServer durante il processo di autenticazione, è necessario autorizzare le richieste per il microservizio basket. Per altre informazioni sull'autorizzazione, vedere [autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Esempio di codice seguente viene illustrato uno dei `PostAsync` metodi nel `RequestProvider` classe:

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Questo metodo chiama il `CreateHttpClient` metodo, che restituisce un'istanza del `HttpClient` classe con il set di intestazioni appropriate. Quindi invia una richiesta POST asincrona alla risorsa identificata dall'URI, con i dati serializzati basket inviati in formato JSON e la risposta viene memorizzato nel `HttpResponseMessage` istanza. Il `HandleResponse` viene quindi richiamato metodo, che genera un'eccezione se la risposta non include un codice di stato HTTP esito positivo. Quindi, la lettura della risposta come stringa, convertita da JSON a un `CustomerBasket` dell'oggetto e restituito il `BasketService`. Per altre informazioni sul `CreateHttpClient` metodo, vedere [effettua una richiesta GET](#making_a_get_request).

Quando la `PostAsync` metodo nella `RequestProvider` classe chiamate `HttpClient.PostAsync`, il `Post` metodo nel `BasketController` classe nel progetto basket viene richiamato, come illustrato nell'esempio di codice seguente:

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

Questo metodo utilizza un'istanza di `RedisBasketRepository` classe per rendere persistenti i dati del carrello alla cache Redis e lo restituisce come un messaggio di risposta che include un codice di stato HTTP esito positivo e un oggetto JSON formattato `CustomerBasket` istanza.

#### <a name="making-a-delete-request"></a>Effettua una richiesta DELETE

Figura 10-3 mostra le interazioni di classi che eliminare i dati del carrello il microservizio basket, per il `CheckoutView`.

![](accessing-remote-data-images/checkoutdata.png "Data di eliminazione dal microservizio basket")

**Figura 10-3**: eliminazione di dati dal microservizio basket

Quando viene richiamato il processo di estrazione, la `CheckoutAsync` metodo nel `CheckoutViewModel` classe viene chiamata. Questo metodo crea un nuovo ordine, prima di cancellare il carrello acquisti, come illustrato nell'esempio di codice seguente:

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

Questo metodo chiama il `ClearBasketAsync` metodo del `BasketService` istanza in cui è stata inserita nel `CheckoutViewModel` da Autofac. Il metodo seguente viene illustrato il `ClearBasketAsync` metodo:

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

Questo metodo si basa l'URI che identifica la risorsa che la richiesta verrà inviata a e utilizza il `RequestProvider` classe per richiamare il metodo HTTP DELETE sulla risorsa. Si noti che un token di accesso ottenuti da IdentityServer durante il processo di autenticazione, è necessario autorizzare le richieste per il microservizio basket. Per altre informazioni sull'autorizzazione, vedere [autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Nell'esempio di codice riportato di seguito viene illustrato il `DeleteAsync` metodo nel `RequestProvider` classe:

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

Questo metodo chiama il `CreateHttpClient` metodo, che restituisce un'istanza del `HttpClient` classe con il set di intestazioni appropriate. Quindi invia una richiesta di eliminazione asincrona alla risorsa identificata dall'URI. Per altre informazioni sul `CreateHttpClient` metodo, vedere [effettua una richiesta GET](#making_a_get_request).

Quando la `DeleteAsync` metodo nella `RequestProvider` classe chiamate `HttpClient.DeleteAsync`, il `Delete` metodo nel `BasketController` classe nel progetto basket viene richiamato, come illustrato nell'esempio di codice seguente:

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

Questo metodo utilizza un'istanza di `RedisBasketRepository` classe per eliminare i dati del carrello dalla cache Redis.

## <a name="caching-data"></a>Memorizzazione di dati nella cache

Possono migliorare le prestazioni di un'app per la memorizzazione nella cache i dati utilizzati di frequente a risorsa di archiviazione rapida vicina all'app. Se l'archiviazione rapida si trova più vicino all'app rispetto all'origine iniziale, allora la memorizzazione nella cache può migliorare significativamente risposta volte durante il recupero dei dati.

La forma più comune di memorizzazione nella cache è Read-through la memorizzazione nella cache, in cui un'applicazione recupera dati fa riferimento alla cache. Se i dati non sono nella cache, dispone di recuperati dall'archivio dati e aggiunti alla cache. Le app possono implementare la memorizzazione nella cache di read-through con il modello cache-aside. Questo modello determina se l'elemento è attualmente nella cache. Se l'elemento non è nella cache, letto dall'archivio dati e aggiunti alla cache. Per altre informazioni, vedere la [Cache-Aside](/azure/architecture/patterns/cache-aside/) pattern.

> [!TIP]
> Memorizzare nella cache i dati che sono letti frequentemente e che vengono modificati raramente. Questi dati possono essere aggiunti alla cache su richiesta la prima volta che viene recuperato da un'app. Ciò significa che l'app deve recuperare una sola volta i dati dall'archivio dati e che l'accesso successivo può essere soddisfatta utilizzando la cache.

Le applicazioni distribuite, ad esempio applicazione, di riferimento eShopOnContainers devono fornire una o entrambe le cache seguenti:

-   Una cache condivisa, che sono accessibili da più processi o computer.
-   Una cache privata, in cui i dati vengono conservati in locale nel dispositivo che esegue l'app.

L'app per dispositivi mobili di eShopOnContainers Usa una cache privata, in cui i dati vengono conservati in locale nel dispositivo che esegue un'istanza dell'app. Per informazioni sulla cache usata dall'applicazione di riferimento eShopOnContainers, vedere [Microservizi .NET: architettura per applicazioni .NET in contenitori](https://aka.ms/microservicesebook).

> [!TIP]
> La cache può essere paragonato a un archivio dati temporaneo che potrebbe scomparire in qualsiasi momento. Assicurarsi che i dati vengono mantenuti nell'archivio dati originale, nonché della cache. Le probabilità di perdita di dati vengono quindi ridotto se la cache è più disponibile.

### <a name="managing-data-expiration"></a>Gestire la scadenza dei dati

È pensabile che i dati memorizzati nella cache saranno sempre coerenti con i dati originali. I dati nell'archivio dati originale potrebbero cambiare dopo che è stata memorizzata nella cache, provocando l'obsolescenza dei dati memorizzati nella cache. Di conseguenza, le app devono implementare una strategia che consente di garantire che i dati nella cache siano il più possibile aggiornati, ma può anche rilevare e gestire situazioni che si verificano quando i dati nella cache diventano obsoleti. Meccanismi di memorizzazione nella cache più abilitano la cache essere configurato per la data di scadenza e quindi ridurre il periodo per cui i dati potrebbero essere obsoleti.

> [!TIP]
> Impostare una scadenza predefinita a tempo quando si configura una cache. Molte cache implementano scadenza, che invalida i dati e lo rimuove dalla cache se non viene eseguito l'accesso per un periodo specificato. Tuttavia, prestare attenzione quando si sceglie il periodo di scadenza. Se viene reso troppo breve, i dati scadranno troppo rapidamente e i vantaggi della memorizzazione nella cache, verranno ridotta. Se viene reso troppo lungo, i rischi di dati non è aggiornata. Pertanto, l'ora di scadenza deve corrispondere al modello di accesso per le app che usano i dati.

Quando i dati memorizzati nella cache scadono, questa deve essere rimossa dalla cache e l'app deve recuperare i dati dai dati originali archiviano e inserirlo nuovamente nella cache.

È anche possibile che una cache si riempia se i dati può rimangono per molto tempo. Di conseguenza, le richieste per aggiungere nuovi elementi alla cache potrebbero essere necessario rimuovere alcuni elementi in un processo noto come *rimozione*. Servizi di memorizzazione nella cache tipicamente rimuovono i dati a intervalli usati meno di recente. Tuttavia, esistono altri criteri di rimozione, tra cui utilizzati più di recente e first-in-first-out. Per altre informazioni, vedere [materiale sussidiario di memorizzazione nella cache](/azure/architecture/best-practices/caching/).

<a name="caching_images" />

### <a name="caching-images"></a>La memorizzazione nella cache di immagini

L'app per dispositivi mobili di eShopOnContainers Usa le immagini dei prodotti remoto che traggono vantaggio dalla memorizzazione nella cache. Queste immagini vengono visualizzate per il [ `Image` ](xref:Xamarin.Forms.Image) (controllo) e il `CachedImage` controllo fornito dal [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) libreria.

Xamarin. Forms [ `Image` ](xref:Xamarin.Forms.Image) controllo supporta la memorizzazione nella cache delle immagini scaricate. La memorizzazione nella cache è abilitata per impostazione predefinita e archivierà l'immagine in locale per 24 ore. Inoltre, l'ora di scadenza può essere configurata con il [ `CacheValidity` ](xref:Xamarin.Forms.UriImageSource.CacheValidity) proprietà. Per altre informazioni, vedere [memorizzazione nella cache di immagine scaricato](~/xamarin-forms/user-interface/images.md#downloaded-image-caching).

Del FFImageLoading `CachedImage` controllo è una sostituzione per xamarin. Forms [ `Image` ](xref:Xamarin.Forms.Image) (controllo), che fornisce le proprietà aggiuntive che abilitano funzionalità supplementari. Tra questa funzionalità, il controllo fornisce configurabile la memorizzazione nella cache, durante il supporto di errore e il caricamento di segnaposto immagine. Esempio di codice seguente viene illustrato come l'app per dispositivi mobili di eShopOnContainers Usa il `CachedImage` controllare nel `ProductTemplate`, che è il modello di dati utilizzato dal [ `ListView` ](xref:Xamarin.Forms.ListView) controllare nel `CatalogView`:

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

Il `CachedImage` controllano i set di `LoadingPlaceholder` e `ErrorPlaceholder` proprietà alle immagini specifiche della piattaforma. Il `LoadingPlaceholder` proprietà consente di specificare l'immagine da visualizzare durante l'immagine specificata dal `Source` proprietà viene recuperata e il `ErrorPlaceholder` proprietà consente di specificare l'immagine da visualizzare se si verifica un errore durante il tentativo di recuperare l'immagine specificato per il `Source` proprietà.

Come suggerisce il nome, il `CachedImage` controllo memorizza nella cache delle immagini remote sul dispositivo per il tempo specificato dal valore della `CacheDuration` proprietà. Quando il valore della proprietà non è impostato in modo esplicito, viene applicato il valore predefinito di 30 giorni.

## <a name="increasing-resilience"></a>Aumentare la resilienza

Tutte le app che comunicano con servizi e risorse remoti devono essere sensibili agli errori temporanei. Gli errori temporanei includono la perdita temporanea della connettività di rete ai servizi, l'indisponibilità temporanea di un servizio o i timeout generati perché un servizio è occupato. Questi errori spesso si risolvono automaticamente e se l'azione viene ripetuta dopo un intervallo di tempo appropriato è probabile che abbia esito positivo.

Gli errori temporanei possono avere un notevole impatto sulla qualità percepita di un'app, anche se è stata accuratamente testata in tutte le circostanze prevedibili. Per garantire che un'app che comunica con i servizi remoti opera in modo affidabile, deve essere in grado di eseguire le operazioni seguenti:

-   Rilevare gli errori quando si verificano e per determinare se gli errori sono probabilmente temporaneo.
-   Ripetere l'operazione se determina che l'errore è probabilmente temporanea e tenere traccia del numero di volte in cui che è stata ritentata l'operazione.
-   Usare una strategia di ripetizione dei tentativi appropriata, che specifica il numero di tentativi, il ritardo tra ogni tentativo e le azioni da intraprendere dopo un tentativo non riuscito.

Questa gestione degli errori temporanei può essere ottenuto eseguendo il wrapping di tutti i tentativi di accedere a un servizio remoto nel codice che implementa il modello di ripetizione dei tentativi.

### <a name="retry-pattern"></a>Modello nuovo tentativo

Se un'applicazione rileva un errore quando prova a inviare una richiesta a un servizio remoto, può gestire l'errore in uno dei modi seguenti:

-   Ripetere l'operazione. L'app è stato possibile ripetere immediatamente la richiesta in errore.
-   Ripetere l'operazione dopo un ritardo. L'app deve attendere per un periodo di tempo prima di ritentare la richiesta appropriato.
-   Annullamento dell'operazione. L'applicazione deve annullare l'operazione e segnalare un'eccezione.

La strategia di ripetizione dei tentativi dovrebbe essere ottimizzata per soddisfare le esigenze di business dell'app. Ad esempio, è importante ottimizzare il numero di tentativi e l'intervallo per l'operazione tentata tra i tentativi. Se l'operazione fa parte di un'interazione utente, l'intervallo di ripetizione dei tentativi deve essere breve e solo alcuni tentativi per evitare di fare gli utenti di attendere una risposta. Se l'operazione fa parte di un flusso di lavoro di lunga esecuzione, in cui l'annullamento o il riavvio del flusso di lavoro è costoso o richiederebbe molto tempo, è appropriato per il periodo di attesa tra i tentativi e ripetere più volte.

> [!NOTE]
> Una strategia di ripetizione aggressivi con un ritardo minimo tra tentativi e un numero elevato di tentativi, è stato possibile ridurre le prestazioni di un servizio remoto che esegue prossime o in base alla capacità. Inoltre, tale strategia di ripetizione dei tentativi può influire anche sulla velocità di risposta dell'app se tenta continuamente di eseguire un'operazione in errore.

Se una richiesta non riesce dopo diversi tentativi, è preferibile per l'app per impedire ulteriori richieste in entrata alla stessa risorsa e per segnalare un errore. Quindi, dopo un periodo, l'app può effettuare una o più richieste per la risorsa per verificare che siano ha esito positivo. Per altre informazioni, vedere [schema Circuit Breaker](#circuit_breaker_pattern).

> [!TIP]
> Non implementare mai un meccanismo di ripetizione infiniti. Usare un numero finito di ripetizione dei tentativi o implementare il [interruttore](/azure/architecture/patterns/circuit-breaker/) criterio per consentire un ripristino del servizio.

L'app per dispositivi mobili di eShopOnContainers non implementa attualmente il modello di ripetizione dei tentativi quando si effettuano richieste web RESTful. Tuttavia, il `CachedImage` al controllo, tramite il [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) libreria supporta la gestione degli errori temporanei con un nuovo tentativo di caricamento dell'immagine. Se il caricamento dell'immagine non riesce, verranno effettuati tentativi di ulteriore. Il numero di tentativi viene specificato per il `RetryCount` proprietà e i tentativi verranno eseguito dopo un intervallo di tempo specificato dal `RetryDelay` proprietà. Se i valori delle proprietà non sono esplicitamente impostate, valori predefiniti vengono applicati i valori: 3 per il `RetryCount` property e 250 ms per le `RetryDelay` proprietà. Per altre informazioni sul `CachedImage` controllano, vedere [la memorizzazione nella cache immagini](#caching_images).

L'applicazione di riferimento eShopOnContainers implementa il modello di ripetizione dei tentativi. Per altre informazioni, incluse informazioni su come combinare il modello di ripetizione dei tentativi con il `HttpClient` classe, vedere [Microservizi .NET: architettura per applicazioni .NET in contenitori](https://aka.ms/microservicesebook).

Per altre informazioni sul modello di ripetizione dei tentativi, vedere la [ripetere](/azure/architecture/patterns/retry/) pattern.

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>Modello a interruttore

In alcuni casi, gli errori possono verificarsi a causa di eventi previsti che richiedono più tempo a correggere. Questi errori può variare da una perdita parziale di connettività al guasto completo di un servizio. In queste situazioni, è inutile per un'app ripetere un'operazione che è improbabile che abbia esito positivo e invece deve accettare che l'operazione non è riuscita e gestirli di conseguenza questo errore.

Il modello a interruttore può impedire a un'app ripetutamente il tentativo di eseguire un'operazione che potrebbe non riuscire, consentendo anche l'app rilevare se l'errore è stato risolto.

> [!NOTE]
> Lo scopo del modello a interruttore è diverso dal criterio di ripetizione dei tentativi. Lo schema retry consente a un'applicazione Ritenta un'operazione quando si prevede che avrà esito positivo. Lo schema circuit breaker impedisce a un'app di eseguire un'operazione che potrebbe non riuscire.

Un interruttore funge da proxy per le operazioni che potrebbe non riuscire. Il proxy deve monitorare il numero di errori recenti che si sono verificati e usare queste informazioni per decidere se consentire l'operazione per procedere o per restituire subito un'eccezione.

L'app per dispositivi mobili di eShopOnContainers non implementa attualmente lo schema circuit breaker. Tuttavia, non di eShopOnContainers. Per altre informazioni, vedere [Microservizi .NET: architettura per applicazioni .NET in contenitori](https://aka.ms/microservicesebook).

> [!TIP]
> Combinare i modelli di ripetizione dei tentativi e l'interruttore di circuito. Un'app è possibile combinare i modelli di ripetizione dei tentativi e l'interruttore di circuito usando lo schema retry per richiamare un'operazione con un interruttore. Tuttavia, la logica di ripetizione dei tentativi deve essere sensibile alle eventuali eccezioni restituite dall'interruttore e abbandonare i tentativi di ripetizione dei tentativi se l'interruttore di circuito indica che un errore non temporaneo.

Per altre informazioni sul modello di interruttore di circuito, vedere la [interruttore](/azure/architecture/patterns/circuit-breaker/) pattern.

## <a name="summary"></a>Riepilogo

Molte soluzioni moderne basate sul web rendere l'uso dei servizi web, ospitate dai server web, per fornire funzionalità per client remoto delle applicazioni. Le operazioni che espone un servizio web costituiscono un'API web e App client devono essere in grado di usare l'API web senza conoscere come vengono implementate i dati o le operazioni che espone l'API.

Possono migliorare le prestazioni di un'app per la memorizzazione nella cache i dati utilizzati di frequente a risorsa di archiviazione rapida vicina all'app. Le app possono implementare la memorizzazione nella cache di read-through con il modello cache-aside. Questo modello determina se l'elemento è attualmente nella cache. Se l'elemento non è nella cache, letto dall'archivio dati e aggiunti alla cache.

Durante la comunicazione con le API web, le app devono essere sensibili agli errori temporanei. Gli errori temporanei includono la perdita temporanea della connettività di rete ai servizi, l'indisponibilità temporanea di un servizio o i timeout generati perché un servizio è occupato. Questi errori spesso si risolvono automaticamente e se l'azione viene ripetuta dopo un intervallo di tempo appropriato, quindi è probabile che abbia esito positivo. Di conseguenza, le app devono eseguire il wrapping di tutti i tentativi di accedere a un'API web nel codice che implementa un meccanismo di gestione di errori temporanei.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
