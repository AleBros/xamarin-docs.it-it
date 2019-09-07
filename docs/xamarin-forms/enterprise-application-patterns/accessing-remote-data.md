---
title: Accesso ai dati remoti
description: Questo capitolo illustra il modo in cui l'app per dispositivi mobili eShopOnContainers accede ai dati dai microservizi in contenitori.
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 9c793f4d5f0cda5bff2dedef5e4e5e5bdfca69e5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770809"
---
# <a name="accessing-remote-data"></a>Accesso ai dati remoti

Molte soluzioni moderne basate sul Web utilizzano i servizi Web, ospitati da server Web, per fornire funzionalità per le applicazioni client Remote. Le operazioni esposte da un servizio Web costituiscono un'API Web.

Le app client devono essere in grado di usare l'API Web senza sapere come vengono implementati i dati o le operazioni esposte dall'API. Questa operazione richiede che l'API rispetti gli standard comuni che consentono a un'app client e a un servizio Web di concordare i formati di dati da usare e la struttura dei dati scambiati tra le app client e il servizio Web.

## <a name="introduction-to-representational-state-transfer"></a>Introduzione al trasferimento di stato di presentazione

REST (Representational State Transfer) è uno stile di architettura per la creazione di sistemi distribuiti basati su ipersupporti. Un vantaggio principale del modello REST è che si basa su standard aperti e non associa l'implementazione del modello o delle app client che vi accedono a un'implementazione specifica. Un servizio Web REST potrebbe pertanto essere implementato usando Microsoft ASP.NET Core MVC e le app client potrebbero svilupparsi usando qualsiasi linguaggio e set di strumenti in grado di generare richieste HTTP e analizzare le risposte HTTP.

Il modello REST usa uno schema di spostamento per rappresentare gli oggetti e i servizi in una rete, detti risorse. I sistemi che implementano REST usano in genere il protocollo HTTP per trasmettere le richieste di accesso a queste risorse. In tali sistemi, un'app client invia una richiesta sotto forma di URI che identifica una risorsa e un metodo HTTP, ad esempio GET, POST, PUT o DELETE, che indica l'operazione da eseguire sulla risorsa. Il corpo della richiesta HTTP contiene i dati necessari per eseguire l'operazione.

> [!NOTE]
> REST definisce un modello di richiesta senza stato. Pertanto, le richieste HTTP devono essere indipendenti e possono verificarsi in qualsiasi ordine.

La risposta da una richiesta REST usa i codici di stato HTTP standard. Una richiesta che restituisce dati validi, ad esempio, deve includere il codice di risposta HTTP 200 (OK), mentre una richiesta che non riesce a trovare o eliminare una risorsa specificata deve restituire una risposta che include il codice di stato HTTP 404 (non trovato).

Un'API Web RESTful espone un set di risorse connesse e fornisce le operazioni principali che consentono a un'app di modificare tali risorse e di spostarsi facilmente tra loro. Per questo motivo, gli URI che costituiscono una tipica API Web RESTful sono orientati verso i dati esposti e usano le funzionalità fornite da HTTP per operare su questi dati.

I dati inclusi da un'app client in una richiesta HTTP e i messaggi di risposta corrispondenti provenienti dal server Web possono essere presentati in diversi formati, noti come tipi di supporti. Quando un'app client invia una richiesta che restituisce i dati nel corpo di un messaggio, può specificare i tipi di supporti che è in grado di `Accept` gestire nell'intestazione della richiesta. Se il server Web supporta questo tipo di supporto, può rispondere con una risposta che include l' `Content-Type` intestazione che specifica il formato dei dati nel corpo del messaggio. È quindi responsabilità dell'app client analizzare il messaggio di risposta e interpretare i risultati nel corpo del messaggio in modo appropriato.

Per altre informazioni su REST, vedere [API Design](/azure/architecture/best-practices/api-design/) and [API Implementation](/azure/architecture/best-practices/api-implementation/).

## <a name="consuming-restful-apis"></a>Utilizzo di API RESTful

L'app per dispositivi mobili eShopOnContainers usa il modello MVC (Model-View-ViewModel) e gli elementi del modello del modello rappresentano le entità di dominio usate nell'app. Le classi controller e repository nell'applicazione di riferimento eShopOnContainers accettano e restituiscono molti di questi oggetti modello. Vengono quindi usati come oggetti DTO (Data Transfer Object) che contengono tutti i dati passati tra l'app per dispositivi mobili e i microservizi in contenitori. Il vantaggio principale dell'uso di DTO per passare dati e ricevere dati da un servizio Web consiste nel fatto che la trasmissione di più dati in una singola chiamata remota può ridurre il numero di chiamate remote che devono essere effettuate.

### <a name="making-web-requests"></a>Esecuzione di richieste Web

L'app per dispositivi mobili eShopOnContainers `HttpClient` usa la classe per eseguire richieste su http, con JSON usato come tipo di supporto. Questa classe fornisce funzionalità per l'invio asincrono di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata dall'URI. La `HttpResponseMessage` classe rappresenta un messaggio di risposta http ricevuto da un'API REST dopo che è stata effettuata una richiesta HTTP. Contiene informazioni relative alla risposta, incluso il codice di stato, intestazioni e qualsiasi corpo. Il `HttpContent` classe rappresenta il corpo HTTP e le intestazioni del contenuto, ad esempio `Content-Type` e `Content-Encoding`. Il contenuto può essere letto usando uno qualsiasi dei `ReadAs` metodi, `ReadAsStringAsync` ad esempio e `ReadAsByteArrayAsync`, a seconda del formato dei dati.

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>Creazione di una richiesta GET

La `CatalogService` classe viene usata per gestire il processo di recupero dei dati dal microservizio Catalog. Nel metodo della classe, la `CatalogService` classe viene registrata come mapping del tipo con il `ICatalogService` contenitore di inserimento delle dipendenze Autofac. `ViewModelLocator` `RegisterDependencies` Quindi, quando viene creata un'istanza `CatalogViewModel` della classe, il relativo costruttore accetta un `ICatalogService` tipo, che Autofac risolve, `CatalogService` restituendo un'istanza della classe. Per altre informazioni sull'inserimento delle dipendenze, vedere [Introduzione all'inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

La figura 10-1 illustra l'interazione delle classi che leggono i dati del catalogo dal microservizio Catalog per la `CatalogView`visualizzazione da parte di.

[Recupero di dati dal microservizio Catalog ![(accessing-remote-data-images/catalogdata.png " ")]] (accessing-remote-data-images/catalogdata-large.png#lightbox "Recupero di dati dal microservizio Catalog")

**Figura 10-1**: Recupero di dati dal microservizio Catalog

Quando si passa a, viene chiamato il `OnInitialize` metodo nella `CatalogViewModel` classe. `CatalogView` Questo metodo recupera i dati del catalogo dal microservizio Catalog, come illustrato nell'esempio di codice seguente:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

Questo metodo chiama il `GetCatalogAsync` metodo `CatalogService` dell'istanza inserita nell'oggetto `CatalogViewModel` da Autofac. L'esempio di codice seguente illustra il metodo `GetCatalogAsync`:

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

Questo metodo compila l'URI che identifica la risorsa alla quale verrà inviata la richiesta e usa la `RequestProvider` classe per richiamare il metodo HTTP Get sulla risorsa, prima di restituire i risultati `CatalogViewModel`a. La `RequestProvider` classe contiene funzionalità che inviano una richiesta sotto forma di URI che identifica una risorsa, un metodo HTTP che indica l'operazione da eseguire su tale risorsa e un corpo contenente i dati necessari per eseguire l'operazione. Per informazioni sul modo `RequestProvider` `CatalogService class`in cui viene inserita la classe in, vedere [Introduzione all'inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Nell'esempio `GetAsync` `RequestProvider` di codice riportato di seguito viene illustrato il metodo nella classe:

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

Questo metodo chiama il `CreateHttpClient` metodo, che restituisce un'istanza `HttpClient` della classe con le intestazioni appropriate impostate. Invia quindi una richiesta Get asincrona alla risorsa identificata dall'URI, con la risposta archiviata nell' `HttpResponseMessage` istanza. Viene `HandleResponse` quindi richiamato il metodo, che genera un'eccezione se la risposta non include un codice di stato http di esito positivo. La risposta viene quindi letta come stringa, convertita da JSON in un `CatalogRoot` oggetto e restituita `CatalogService`a.

Il `CreateHttpClient` metodo viene illustrato nell'esempio di codice seguente:

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

Questo metodo crea una nuova istanza `HttpClient` della classe e imposta l' `Accept` intestazione di `HttpClient` qualsiasi richiesta eseguita dall'istanza su `application/json`, che indica che il contenuto di qualsiasi risposta deve essere formattato usando JSON. Quindi, se un token di accesso è stato passato come argomento al `CreateHttpClient` metodo, viene aggiunto `Authorization` all'intestazione di `HttpClient` qualsiasi richiesta eseguita dall'istanza, preceduta dalla stringa `Bearer`. Per ulteriori informazioni sull'autorizzazione, vedere [autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Quando il `GetAsync` `RequestProvider` metodo nella classe chiama `HttpClient.GetAsync`, viene richiamato il `Items` metodo `CatalogController` nella classe nel progetto Catalog. API, illustrato nell'esempio di codice seguente:

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

Questo metodo recupera i dati del catalogo dal database SQL tramite EntityFramework e li restituisce come un messaggio di risposta che include un codice di stato http di esito positivo e una raccolta `CatalogItem` di istanze formattate JSON.

#### <a name="making-a-post-request"></a>Creazione di una richiesta POST

La `BasketService` classe viene usata per gestire il processo di recupero e aggiornamento dei dati con il microservizio basket. Nel metodo della classe, la `BasketService` classe viene registrata come mapping del tipo con il `IBasketService` contenitore di inserimento delle dipendenze Autofac. `ViewModelLocator` `RegisterDependencies` Quindi, quando viene creata un'istanza `BasketViewModel` della classe, il relativo costruttore accetta un `IBasketService` tipo, che Autofac risolve, `BasketService` restituendo un'istanza della classe. Per altre informazioni sull'inserimento delle dipendenze, vedere [Introduzione all'inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

La figura 10-2 illustra l'interazione delle classi che inviano i dati di basket `BasketView`visualizzati da al microservizio basket.

[Invio di dati al microservizio basket ![(accessing-remote-data-images/basketdata.png " ")]] (accessing-remote-data-images/basketdata-large.png#lightbox "Invio di dati al microservizio basket")

**Figura 10-2**: Invio di dati al microservizio basket

Quando un elemento viene aggiunto al carrello acquisti, viene chiamato `ReCalculateTotalAsync` il metodo `BasketViewModel` nella classe. Questo metodo aggiorna il valore totale degli elementi nel cestino e invia i dati del carrello al microservizio basket, come illustrato nell'esempio di codice seguente:

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

Questo metodo chiama il `UpdateBasketAsync` metodo `BasketService` dell'istanza inserita nell'oggetto `BasketViewModel` da Autofac. Il metodo seguente illustra il `UpdateBasketAsync` metodo:

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

Questo metodo compila l'URI che identifica la risorsa alla quale verrà inviata la richiesta e usa la `RequestProvider` classe per richiamare il metodo HTTP post sulla risorsa, prima di restituire i risultati `BasketViewModel`a. Si noti che per autorizzare le richieste al microservizio basket è necessario un token di accesso, ottenuto da IdentityServer durante il processo di autenticazione. Per ulteriori informazioni sull'autorizzazione, vedere [autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Nell'esempio di codice riportato di seguito viene `PostAsync` illustrato uno dei `RequestProvider` metodi della classe:

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

Questo metodo chiama il `CreateHttpClient` metodo, che restituisce un'istanza `HttpClient` della classe con le intestazioni appropriate impostate. Invia quindi una richiesta post asincrona alla risorsa identificata dall'URI, con i dati del cestino serializzati inviati in formato JSON e la risposta archiviata nell' `HttpResponseMessage` istanza. Viene `HandleResponse` quindi richiamato il metodo, che genera un'eccezione se la risposta non include un codice di stato http di esito positivo. Quindi, la risposta viene letta come stringa, convertita da JSON in un `CustomerBasket` oggetto e restituita `BasketService`a. Per ulteriori informazioni sul `CreateHttpClient` metodo, vedere Creazione di [una richiesta Get](#making_a_get_request).

Quando il `PostAsync` `RequestProvider` metodo nella classe chiama `HttpClient.PostAsync`, viene richiamato il `Post` metodo `BasketController` nella classe nel progetto basket. API, illustrato nell'esempio di codice seguente:

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

Questo metodo usa un'istanza della `RedisBasketRepository` classe per salvare in modo permanente i dati del cestino nella cache Redis e li restituisce come un messaggio di risposta che include un codice di stato http di esito positivo e un'istanza in formato `CustomerBasket` JSON.

#### <a name="making-a-delete-request"></a>Creazione di una richiesta DELETE

La figura 10-3 illustra le interazioni delle classi che eliminano i dati del cestino dal microservizio basket `CheckoutView`per il.

![](accessing-remote-data-images/checkoutdata.png "Eliminazione di dati dal microservizio basket")

**Figura 10-3**: Eliminazione di dati dal microservizio basket

Quando il processo `CheckoutViewModel` di estrazione viene richiamato `CheckoutAsync` , viene chiamato il metodo nella classe. Questo metodo crea un nuovo ordine, prima di cancellare il carrello acquisti, come illustrato nell'esempio di codice seguente:

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

Questo metodo chiama il `ClearBasketAsync` metodo `BasketService` dell'istanza inserita nell'oggetto `CheckoutViewModel` da Autofac. Il metodo seguente illustra il `ClearBasketAsync` metodo:

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

Questo metodo compila l'URI che identifica la risorsa a cui verrà inviata la richiesta e usa la `RequestProvider` classe per richiamare il metodo HTTP DELETE sulla risorsa. Si noti che per autorizzare le richieste al microservizio basket è necessario un token di accesso, ottenuto da IdentityServer durante il processo di autenticazione. Per ulteriori informazioni sull'autorizzazione, vedere [autorizzazione](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Nell'esempio `DeleteAsync` `RequestProvider` di codice riportato di seguito viene illustrato il metodo nella classe:

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

Questo metodo chiama il `CreateHttpClient` metodo, che restituisce un'istanza `HttpClient` della classe con le intestazioni appropriate impostate. Invia quindi una richiesta di eliminazione asincrona alla risorsa identificata dall'URI. Per ulteriori informazioni sul `CreateHttpClient` metodo, vedere Creazione di [una richiesta Get](#making_a_get_request).

Quando il `DeleteAsync` `RequestProvider` metodo nella classe chiama `HttpClient.DeleteAsync`, viene richiamato il `Delete` metodo `BasketController` nella classe nel progetto basket. API, illustrato nell'esempio di codice seguente:

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

Questo metodo usa un'istanza della `RedisBasketRepository` classe per eliminare i dati del cestino dalla cache Redis.

## <a name="caching-data"></a>Memorizzazione di dati nella cache

Le prestazioni di un'app possono essere migliorate memorizzando nella cache i dati a cui si accede di frequente per l'archiviazione veloce che si trova vicino all'app. Se lo spazio di archiviazione veloce si trova più vicino all'app rispetto all'origine originale, la memorizzazione nella cache può migliorare significativamente i tempi di risposta durante il recupero dei dati.

La forma più comune di memorizzazione nella cache è la memorizzazione nella cache read-through, in cui un'app recupera i dati facendo riferimento alla cache. Se i dati non sono presenti nella cache, vengono recuperati dall'archivio dati e aggiunti alla cache. Le app possono implementare la memorizzazione nella cache read-through con il modello cache-aside. Questo modello determina se l'elemento è attualmente nella cache. Se l'elemento non si trova nella cache, viene letto dall'archivio dati e aggiunto alla cache. Per ulteriori informazioni, vedere il modello [cache-aside](/azure/architecture/patterns/cache-aside/) .

> [!TIP]
> Memorizzare nella cache i dati letti frequentemente e che cambiano raramente. Questi dati possono essere aggiunti alla cache su richiesta la prima volta che vengono recuperati da un'app. Ciò significa che l'app deve recuperare i dati una sola volta dall'archivio dati e che l'accesso successivo può essere soddisfatto usando la cache.

Le applicazioni distribuite, ad esempio l'applicazione di riferimento eShopOnContainers, devono fornire una o entrambe le cache seguenti:

- Una cache condivisa, a cui è possibile accedere da più processi o computer.
- Una cache privata, in cui i dati vengono conservati localmente sul dispositivo che esegue l'app.

L'app per dispositivi mobili eShopOnContainers usa una cache privata, in cui i dati vengono conservati localmente sul dispositivo che esegue un'istanza dell'app. Per informazioni sulla cache usata dall'applicazione di riferimento eShopOnContainers, vedere [microservizi .NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservizi .NET. Architettura per le applicazioni .NET incluse in contenitori).

> [!TIP]
> È possibile considerare la cache come un archivio dati temporaneo che può scomparire in qualsiasi momento. Assicurarsi che i dati vengano mantenuti nell'archivio dati originale e nella cache. Se la cache non è più disponibile, le probabilità di perdita di dati vengono ridotte a icona.

### <a name="managing-data-expiration"></a>Gestione della scadenza dei dati

Non è pratico prevedere che i dati memorizzati nella cache saranno sempre coerenti con i dati originali. I dati nell'archivio dati originale potrebbero cambiare dopo essere stati memorizzati nella cache, facendo sì che i dati memorizzati nella cache diventino obsoleti. Pertanto, le app devono implementare una strategia che consente di garantire che i dati nella cache siano più aggiornati possibile, ma anche di rilevare e gestire le situazioni che si verificano quando i dati nella cache sono diventati obsoleti. La maggior parte dei meccanismi di memorizzazione nella cache consente di configurare la cache in modo che scada i dati e quindi di ridurre il periodo di tempo per cui i dati potrebbero non essere aggiornati.

> [!TIP]
> Impostare una data di scadenza predefinita durante la configurazione di una cache. Molte cache implementano la scadenza, che invalida i dati e li rimuove dalla cache se non è possibile accedervi per un periodo di tempo specificato. Tuttavia, è necessario prestare attenzione quando si sceglie il periodo di scadenza. Se è troppo breve, i dati scadranno troppo rapidamente e i vantaggi della memorizzazione nella cache verranno ridotti. Se è stato troppo lungo, i dati rischiano di diventare obsoleti. L'ora di scadenza deve pertanto corrispondere al modello di accesso per le app che usano i dati.

Quando i dati memorizzati nella cache scadono, è necessario rimuoverli dalla cache e l'app deve recuperare i dati dall'archivio dati originale e inserirli nuovamente nella cache.

È anche possibile che una cache venga riempita se i dati possono rimanere per un periodo troppo lungo. Pertanto, potrebbe essere necessario aggiungere nuovi elementi alla cache per rimuovere alcuni elementi in un processo noto come *rimozione*. I servizi di memorizzazione nella cache in genere eliminano i dati in base a un utilizzo meno recente. Tuttavia, esistono altri criteri di rimozione, tra cui quelli usati più di recente e il primo in primo piano. Per ulteriori informazioni, vedere informazioni [aggiuntive sulla memorizzazione nella cache](/azure/architecture/best-practices/caching/).

<a name="caching_images" />

### <a name="caching-images"></a>Memorizzazione nella cache di immagini

L'app per dispositivi mobili eShopOnContainers utilizza immagini del prodotto remote che traggono vantaggio dalla memorizzazione nella cache. Queste immagini vengono visualizzate dal [`Image`](xref:Xamarin.Forms.Image) controllo `CachedImage` e dal controllo fornito dalla libreria [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) .

Il controllo Novell. [`Image`](xref:Xamarin.Forms.Image) Forms supporta la memorizzazione nella cache delle immagini scaricate. La memorizzazione nella cache è abilitata per impostazione predefinita e archivia l'immagine in locale per 24 ore. Inoltre, l'ora di scadenza può essere configurata [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) con la proprietà. Per ulteriori informazioni, vedere la pagina relativa alla [memorizzazione nella cache delle immagini scaricata](~/xamarin-forms/user-interface/images.md#downloaded-image-caching).

Il controllo `CachedImage` di FFImageLoading è una sostituzione per il controllo Novell [`Image`](xref:Xamarin.Forms.Image) . Forms, che fornisce proprietà aggiuntive che consentono funzionalità supplementari. Tra queste funzionalità, il controllo fornisce la memorizzazione nella cache configurabile, supportando al tempo stesso gli errori e il caricamento dei segnaposto di immagine. Nell'esempio di codice seguente `CachedImage` viene illustrato il modo `ProductTemplate`in cui l'app per dispositivi mobili eShopOnContainers utilizza il controllo in, ovvero il [`ListView`](xref:Xamarin.Forms.ListView) modello di dati `CatalogView`utilizzato dal controllo in:

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

Il `CachedImage` controllo imposta le `LoadingPlaceholder` proprietà `ErrorPlaceholder` e su immagini specifiche della piattaforma. La `LoadingPlaceholder` proprietà specifica l'immagine da visualizzare mentre viene recuperata l'immagine specificata `Source` dalla proprietà e la `ErrorPlaceholder` proprietà specifica l'immagine da visualizzare se si verifica un errore durante il tentativo di recuperare l'immagine specificato dalla `Source` proprietà.

Come suggerisce il nome, il `CachedImage` controllo memorizza nella cache le immagini remote sul dispositivo per l'ora specificata dal valore `CacheDuration` della proprietà. Se il valore di questa proprietà non è impostato in modo esplicito, viene applicato il valore predefinito di 30 giorni.

## <a name="increasing-resilience"></a>Aumento della resilienza

Tutte le app che comunicano con i servizi remoti e le risorse devono essere sensibili agli errori temporanei. Gli errori temporanei includono la perdita momentanea della connettività di rete ai servizi, l'indisponibilità temporanea di un servizio o i timeout che si verificano quando un servizio è occupato. Questi errori sono spesso di correzione automatica e se l'azione viene ripetuta dopo un ritardo appropriato è probabile che abbia esito positivo.

Gli errori temporanei possono avere un notevole effetto sulla qualità percepita di un'applicazione, anche se è stata testata accuratamente in tutte le circostanze prevedibili. Per assicurarsi che un'app che comunica con i servizi remoti funzioni in modo affidabile, deve essere in grado di eseguire tutte le operazioni seguenti:

- Rilevare gli errori quando si verificano e determinare se è probabile che si verifichino errori temporanei.
- Ripetere l'operazione se determina che è probabile che l'errore sia temporaneo e tenere traccia del numero di volte in cui è stata ritentata l'operazione.
- Utilizzare una strategia di ripetizione dei tentativi appropriata, che specifica il numero di tentativi, il ritardo tra ogni tentativo e le azioni da intraprendere dopo un tentativo non riuscito.

Questa gestione degli errori temporanei può essere eseguita eseguendo il wrapping di tutti i tentativi di accesso a un servizio remoto nel codice che implementa il modello di ripetizione dei tentativi.

### <a name="retry-pattern"></a>Modello di ripetizione dei tentativi

Se un'app rileva un errore durante il tentativo di inviare una richiesta a un servizio remoto, può gestire l'errore in uno dei modi seguenti:

- Nuovo tentativo di esecuzione dell'operazione. L'app potrebbe ritentare immediatamente la richiesta non riuscita.
- Riprovare l'operazione dopo un ritardo. L'app deve attendere un periodo di tempo appropriato prima di ritentare la richiesta.
- Annullamento dell'operazione. L'applicazione deve annullare l'operazione e segnalare un'eccezione.

La strategia di ripetizione dei tentativi deve essere ottimizzata in base ai requisiti aziendali dell'app. Ad esempio, è importante ottimizzare il numero di tentativi e l'intervallo tra tentativi per l'operazione tentata. Se l'operazione fa parte di un'interazione dell'utente, l'intervallo tra i tentativi deve essere breve e solo pochi tentativi hanno tentato di evitare che gli utenti attendano una risposta. Se l'operazione fa parte di un flusso di lavoro a esecuzione prolungata, in cui l'annullamento o il riavvio del flusso di lavoro è costoso o dispendioso in termini di tempo, è opportuno attendere più a lungo tra i tentativi e ripetere più volte.

> [!NOTE]
> Una strategia di ripetizione dei tentativi aggressiva con un ritardo minimo tra i tentativi e un numero elevato di tentativi può compromettere un servizio remoto in esecuzione vicino o alla capacità. Inoltre, una strategia di ripetizione dei tentativi potrebbe influenzare anche la velocità di risposta dell'app se tenta continuamente di eseguire un'operazione non riuscita.

Se una richiesta ha ancora esito negativo dopo diversi tentativi, è preferibile che l'app eviti altre richieste che passano alla stessa risorsa e segnali un errore. Quindi, dopo un determinato periodo di tempo, l'app può effettuare una o più richieste alla risorsa per verificare se hanno avuto esito positivo. Per altre informazioni, vedere [modello](#circuit_breaker_pattern)di interruttore.

> [!TIP]
> Non implementare mai un meccanismo di ripetizione infinita. Usare un numero finito di tentativi o implementare [il modello di interruttore per](/azure/architecture/patterns/circuit-breaker/) consentire il ripristino di un servizio.

L'app per dispositivi mobili eShopOnContainers non implementa attualmente il modello di ripetizione dei tentativi quando si effettuano richieste Web RESTful. Tuttavia, il `CachedImage` controllo fornito dalla libreria [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) supporta la gestione degli errori temporanei ritentando il caricamento delle immagini. Se il caricamento dell'immagine non riesce, verranno effettuati ulteriori tentativi. Il numero di tentativi è specificato dalla `RetryCount` proprietà e i tentativi si verificheranno dopo un ritardo specificato `RetryDelay` dalla proprietà. Se questi valori di proprietà non sono impostati in modo esplicito, vengono applicati i valori predefiniti `RetryCount` -3 per la proprietà e `RetryDelay` 250ms per la proprietà. Per ulteriori informazioni sul `CachedImage` controllo, vedere [caching images](#caching_images).

L'applicazione di riferimento eShopOnContainers implementa il modello di ripetizione dei tentativi. Per altre informazioni, inclusa una discussione su come combinare il modello di ripetizione dei tentativi `HttpClient` con la classe [, vedere microservizi .NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservizi .NET. Architettura per le applicazioni .NET incluse in contenitori).

Per ulteriori informazioni sul modello di ripetizione dei tentativi, vedere il modello di [ripetizione dei tentativi](/azure/architecture/patterns/retry/) .

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>Modello di interruttore

In alcune situazioni è possibile che si verifichino errori a causa di eventi previsti che importano più tempo per la correzione. Questi errori possono variare da una perdita parziale di connettività al guasto completo di un servizio. In questi casi, è inutile che un'app ritenti un'operazione che è improbabile che abbia esito positivo e accetti invece che l'operazione abbia avuto esito negativo e che questo errore venga gestito di conseguenza.

Il modello a interruttore può impedire che un'app tenti ripetutamente di eseguire un'operazione che probabilmente avrà esito negativo, consentendo allo stesso tempo all'app di rilevare se l'errore è stato risolto.

> [!NOTE]
> Lo scopo del modello di interruttore è diverso dal modello di ripetizione dei tentativi. Il modello di ripetizione dei tentativi consente a un'app di ritentare un'operazione nella previsione che avrà esito positivo. Il modello di interruttore impedisce a un'app di eseguire un'operazione che probabilmente avrà esito negativo.

Un interruttore funge da proxy per le operazioni che potrebbero avere esito negativo. Il proxy deve monitorare il numero di errori recenti che si sono verificati e usare queste informazioni per decidere se consentire o meno l'esecuzione dell'operazione oppure per restituire immediatamente un'eccezione.

L'app per dispositivi mobili eShopOnContainers non implementa attualmente il modello di interruttore. Tuttavia, il eShopOnContainers esegue l'operazione. Per altre informazioni, vedere [microservizi .NET: Architecture for Containerized .NET Applications](https://aka.ms/microservicesebook) (Microservizi .NET. Architettura per le applicazioni .NET incluse in contenitori).

> [!TIP]
> Combinare i modelli di ripetizione dei tentativi e degli interruttori. Un'app può combinare i modelli di ripetizione dei tentativi e degli interruttori di circuito usando il modello di ripetizione dei tentativi per richiamare un'operazione tramite un interruttore. Tuttavia, la logica di ripetizione dei tentativi deve essere sensibile alle eventuali eccezioni restituite dall'interruttore e abbandonare i tentativi se l'interruttore indica che un errore non è temporaneo.

Per altre informazioni sul modello di interruttore, [vedere il modello di interruttore.](/azure/architecture/patterns/circuit-breaker/)

## <a name="summary"></a>Riepilogo

Molte soluzioni moderne basate sul Web utilizzano i servizi Web, ospitati da server Web, per fornire funzionalità per le applicazioni client Remote. Le operazioni esposte da un servizio Web costituiscono un'API Web e le app client devono essere in grado di utilizzare l'API Web senza conoscere il modo in cui vengono implementati i dati o le operazioni esposte dall'API.

Le prestazioni di un'app possono essere migliorate memorizzando nella cache i dati a cui si accede di frequente per l'archiviazione veloce che si trova vicino all'app. Le app possono implementare la memorizzazione nella cache read-through con il modello cache-aside. Questo modello determina se l'elemento è attualmente nella cache. Se l'elemento non si trova nella cache, viene letto dall'archivio dati e aggiunto alla cache.

Quando si comunica con le API Web, le app devono essere sensibili agli errori temporanei. Gli errori temporanei includono la perdita momentanea della connettività di rete ai servizi, l'indisponibilità temporanea di un servizio o i timeout che si verificano quando un servizio è occupato. Questi errori sono spesso di correzione automatica e, se l'azione viene ripetuta dopo un intervallo di tempo appropriato, è probabile che abbia esito positivo. Pertanto, le app devono eseguire il wrapping di tutti i tentativi di accesso a un'API Web nel codice che implementa un meccanismo di gestione degli errori temporanei.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
