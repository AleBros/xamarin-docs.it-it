---
title: Autenticazione e autorizzazione
ms.topic: article
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: 5f27c1acefa63ee26184b8997594630e24cb0acc
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

L'autenticazione è il processo di ottenere le credenziali di identificazione, ad esempio nome e la password da un utente e la convalida di tali credenziali rispetto a un'autorità. Se le credenziali siano valide, l'entità che ha inviato le credenziali è considerata un'identità autenticata. Una volta un'identità autenticata, un processo di autorizzazione determina se l'identità ha accesso a una determinata risorsa.

Esistono molti approcci per l'integrazione di autenticazione e autorizzazione in un'app xamarin. Forms che comunica con un'applicazione web ASP.NET MVC, incluso l'utilizzo di ASP.NET Identity Core, il provider di autenticazione esterni, ad esempio Microsoft, Google, Middleware, Facebook o Twitter e l'autenticazione. L'app mobile eShopOnContainers esegue l'autenticazione e autorizzazione con un microservizio identità nei contenitori che utilizza 4 IdentityServer. L'app per dispositivi mobili richiede token di sicurezza da IdentityServer, per autenticare un utente o per l'accesso a una risorsa. Per IdentityServer al rilascio di token per conto dell'utente, l'utente deve Accedi a IdentityServer. Tuttavia, IdentityServer non fornisce un'interfaccia utente o un database per l'autenticazione. Pertanto, nell'applicazione, riferimento eShopOnContainers ASP.NET Identity Core viene utilizzato per questo scopo.

## <a name="authentication"></a>Autenticazione

Autenticazione è necessaria quando un'applicazione deve conoscere l'identità dell'utente corrente. Meccanismo principale del ASP.NET Core per identificare gli utenti è il sistema di appartenenze di ASP.NET Identity Core, che archivia le informazioni utente in un archivio dati configurato dallo sviluppatore. L'archivio dati sarà in genere, un archivio, EntityFramework proposte archivi personalizzati o pacchetti di terze parti possono essere usati per archiviare le informazioni di identità in archiviazione di Azure, DocumentDB o altre posizioni.

Per scenari di autenticazione che utilizzano dell'archivio dati utente locali, e vengono mantenute le informazioni di identità tra le richieste tramite i cookie (come avviene in genere nelle applicazioni web ASP.NET MVC), ASP.NET Identity Core è una soluzione adeguata. Tuttavia, i cookie non sono sempre un mezzo naturale di persistenza e la trasmissione dei dati. Ad esempio, un'applicazione web ASP.NET di base che espone endpoint REST che è possibile accedere da un'app per dispositivi mobili in genere necessario utilizzare l'autenticazione token della connessione, poiché i cookie non possono essere utilizzati in questo scenario. Tuttavia, i token di connessione possono facilmente essere recuperati e incluso nell'intestazione di autorizzazione delle richieste web effettuate dall'app mobile.

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>Emissione di token di connessione utilizzando IdentityServer 4

[4 IdentityServer](https://github.com/IdentityServer/IdentityServer4) è un framework open source di OpenID Connect e OAuth 2.0 per ASP.NET Core, che può essere utilizzato per molti scenari di autenticazione e autorizzazione inclusa l'emissione di token di sicurezza per gli utenti locali di ASP.NET Identity Core.

> [!NOTE]
> OpenID Connect e OAuth 2.0 sono molto simili, ma la responsabilità diverse.

OpenID Connect è un livello di autenticazione superiore il protocollo OAuth 2.0. OAuth 2 è un protocollo che consente alle applicazioni di richiedere token di accesso da un servizio token di sicurezza e li userà per comunicare con le API. Questa delega riduce la complessità in entrambe le API e le applicazioni client, poiché l'autenticazione e autorizzazione può essere centralizzate.

La combinazione di OpenID Connect e OAuth 2.0 combinare i due problemi di protezione fondamentali dell'autenticazione e l'accesso all'API e 4 IdentityServer è un'implementazione di questi protocolli.

Nelle applicazioni che usano la comunicazione client-a-microservizio diretta, ad esempio l'applicazione di riferimento, eShopOnContainers un microservizio autenticazione dedicato che agisce come un Token di sicurezza del servizio (STS) consente di autenticare gli utenti, come illustrato nella figura 9-1. Per ulteriori informazioni sulla comunicazione client-a-microservizio diretta, vedere [la comunicazione tra Client e Microservizi](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices).

![](authentication-and-authorization-images/authentication.png "Autenticazione mediante un microservizio autenticazione dedicato")

**Figura 9-1:** l'autenticazione mediante un microservizio autenticazione dedicato

L'app mobile eShopOnContainers comunica con l'identità microservizio, che utilizza 4 IdentityServer per eseguire l'autenticazione e controllo di accesso per le API. Pertanto, l'app per dispositivi mobili richiede token da IdentityServer, per autenticare un utente o per l'accesso a una risorsa:

-   L'autenticazione degli utenti con IdentityServer infatti, l'app per dispositivi mobili che richiede un *identità* token che rappresenta il risultato di un processo di autenticazione. Minimo, contiene un identificatore per l'utente e informazioni su come e quando l'utente autenticato. Può contenere anche dati di identità aggiuntive.
-   L'accesso a una risorsa con IdentityServer infatti, l'app per dispositivi mobili che richiede un *accesso* token, che consente l'accesso a una risorsa API. I client di richiedere token di accesso e li inoltrano all'API. I token di accesso contengono informazioni sul client e l'utente (se presente). API quindi utilizzano tali informazioni per autorizzare l'accesso ai dati.

> [!NOTE]
> Un client deve essere registrato con IdentityServer prima di poter richiedere i token.

### <a name="adding-identityserver-to-a-web-application"></a>Aggiunta di IdentityServer a un'applicazione Web

Per un'applicazione web ASP.NET Core utilizzare IdentityServer 4, è necessario aggiungerlo alla soluzione di Visual Studio dell'applicazione web. Per ulteriori informazioni, vedere [panoramica e il programma di installazione](https://identityserver4.readthedocs.io/en/release/quickstarts/0_overview.html) nella documentazione di IdentityServer.

Una volta IdentityServer è incluso nella soluzione di Visual Studio dell'applicazione web, è necessario aggiungerlo alla richiesta HTTP dell'applicazione web l'elaborazione della pipeline, in modo che possa servire le richieste agli endpoint di OpenID Connect e OAuth 2.0. Questa operazione viene eseguita nel `Configure` metodo nell'applicazione web di `Startup` classe, come illustrato nell'esempio di codice seguente:

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

Ordine è importante nella richiesta HTTP dell'applicazione web pipeline di elaborazione. Pertanto, IdentityServer deve essere aggiunto alla pipeline prima framework dell'interfaccia utente che implementa la schermata di accesso.

### <a name="configuring-identityserver"></a>Configurazione IdentityServer

IdentityServer deve essere configurato nel `ConfigureServices` metodo nell'applicazione web di `Startup` classe chiamando il `services.AddIdentityServer` (metodo), come illustrato nell'esempio di codice seguente dall'applicazione eShopOnContainers riferimento:

```csharp
public void ConfigureServices(IServiceCollection services)  
{  
    ...  
    services.AddIdentityServer(x => x.IssuerUri = "null")  
        .AddSigningCredential(Certificate.Get())                 
        .AddAspNetIdentity<ApplicationUser>()  
        .AddConfigurationStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .AddOperationalStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .Services.AddTransient<IProfileService, ProfileService>();  
}
```

Dopo la chiamata di `services.AddIdentityServer` metodo, l'API fluent aggiuntive vengono chiamate per configurare le opzioni seguenti:

-   Credenziali utilizzate per la firma.
-   Risorse di API e l'identità che gli utenti potrebbero richiedere l'accesso.
-   Client che si connettono ai token di richiesta.
-   ASP.NET Core Identity.

>💡 **Suggerimento**: caricare in modo dinamico la configurazione di 4 IdentityServer. API IdentityServer 4 consentono di configurare IdentityServer da un elenco di oggetti di configurazione in memoria. Nell'applicazione di riferimento eShopOnContainers, queste raccolte in memoria sono hardcoded nell'applicazione. Tuttavia, negli scenari di produzione possono essere caricati in modo dinamico da un file di configurazione o da un database.

Per informazioni sulla configurazione IdentityServer per l'utilizzo di ASP.NET Identity Core, vedere [usando ASP.NET Core Identity](https://identityserver4.readthedocs.io/en/release/quickstarts/6_aspnet_identity.html) nella documentazione di IdentityServer.

#### <a name="configuring-api-resources"></a>Configurazione delle risorse di API

Durante la configurazione delle risorse dell'API, il `AddInMemoryApiResources` metodo prevede un `IEnumerable<ApiResource>` insieme. Nell'esempio di codice riportato di seguito viene illustrato il `GetApis` fanno riferimento a metodo che fornisce questo insieme di eShopOnContainers applicazione:

```csharp
public static IEnumerable<ApiResource> GetApis()  
{  
    return new List<ApiResource>  
    {  
        new ApiResource("orders", "Orders Service"),  
        new ApiResource("basket", "Basket Service")  
    };  
}
```

Questo metodo specifica che devono proteggere IdentityServer gli ordini e acquisti API. Pertanto, IdentityServer gestita accesso verrà richiesto di token quando si effettuano chiamate a queste API. Per ulteriori informazioni sul `ApiResource` del tipo, vedere [risorsa API](https://identityserver4.readthedocs.io/en/release/reference/api_resource.html#refapiresource) nella documentazione di 4 IdentityServer.

#### <a name="configuring-identity-resources"></a>Configurazione delle risorse di identità

Quando si configurano le risorse di identità, il `AddInMemoryIdentityResources` metodo prevede un `IEnumerable<IdentityResource>` insieme. Risorse di identità sono dati, ad esempio l'ID utente, nome o indirizzo di posta elettronica. Ogni risorsa di identità ha un nome univoco e tipi di attestazioni arbitrarie possono essere assegnati a esso, che verranno quindi incluse nel token di identità per l'utente. Nell'esempio di codice riportato di seguito viene illustrato il `GetResources` fanno riferimento a metodo che fornisce questo insieme di eShopOnContainers applicazione:

```csharp
public static IEnumerable<IdentityResource> GetResources()  
{  
    return new List<IdentityResource>  
    {  
        new IdentityResources.OpenId(),  
        new IdentityResources.Profile()  
    };  
}
```

La specifica di OpenID Connect consente di specificare alcuni [risorse identità standard](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). Il requisito minimo è che viene fornito supporto per la creazione di un ID univoco per gli utenti. A questo scopo, esponendo il `IdentityResources.OpenId` risorse di identità.

> [!NOTE]
> La `IdentityResources` classe supporta tutti gli ambiti definiti nella specifica OpenID Connect (openid, posta elettronica, profilo, telefono e indirizzo).

IdentityServer supporta anche la definizione delle risorse di identità personalizzata. Per ulteriori informazioni, vedere [la definizione delle risorse di identità personalizzato](https://identityserver4.readthedocs.io/en/release/topics/resources.html#defining-custom-identity-resources) nella documentazione di IdentityServer. Per ulteriori informazioni sul `IdentityResource` del tipo, vedere [risorsa Identity](https://identityserver4.readthedocs.io/en/release/reference/identity_resource.html) nella documentazione di 4 IdentityServer.

#### <a name="configuring-clients"></a>Configurazione dei client

I client sono applicazioni che possono richiedere un token da IdentityServer. In genere, è necessario definire le impostazioni seguenti per ogni client come minimo:

-   Un ID univoco client.
-   Le interazioni consentite con il servizio token (noto come il tipo di concessione).
-   Il percorso in cui vengono inviati i token di identità e accessi (noto come un URI di reindirizzamento).
-   Un elenco di risorse che il client è autorizzato ad accedere a (noto come ambiti).

Quando si configurano i client, il `AddInMemoryClients` metodo prevede un `IEnumerable<Client>` insieme. Esempio di codice seguente viene illustrata la configurazione di App per dispositivi mobili in eShopOnContainers il `GetClients` fanno riferimento a metodo che fornisce questo insieme di eShopOnContainers applicazione:

```csharp
public static IEnumerable<Client> GetClients(Dictionary<string,string> clientsUrl)
{
    return new List<Client>
    {
        ...
        new Client
        {
            ClientId = "xamarin",
            ClientName = "eShop Xamarin OpenId Client",
            AllowedGrantTypes = GrantTypes.Hybrid,
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            RedirectUris = { clientsUrl["Xamarin"] },
            RequireConsent = false,
            RequirePkce = true,
            PostLogoutRedirectUris = { $"{clientsUrl["Xamarin"]}/Account/Redirecting" },
            AllowedCorsOrigins = { "http://eshopxamarin" },
            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.OfflineAccess,
                "orders",
                "basket"
            },
            AllowOfflineAccess = true,
            AllowAccessTokensViaBrowser = true
        },
        ...
    };
}
```

Questa configurazione specifica dei dati per le proprietà seguenti:

-   `ClientId`: Un ID univoco per il client.
-   `ClientName`: Il client Visualizza nome, viene utilizzato per la registrazione e la schermata di consenso.
-   `AllowedGrantTypes`: Specifica come un client deve interagire con IdentityServer. Per ulteriori informazioni vedere [configurazione flusso di autenticazione](#configuring_the_authentication_flow).
-   `ClientSecrets`: Specifica le credenziali del client secret che vengono utilizzate quando la richiesta di token dall'endpoint del token.
-   `RedirectUris`: Specifica l'URI consentito per cui restituire i token o i codici di autorizzazione.
-   `RequireConsent`: Specifica se è necessaria una schermata di consenso.
-   `RequirePkce`: Specifica se i client utilizzando un codice di autorizzazione necessario inviare una chiave di prova.
-   `PostLogoutRedirectUris`: Specifica l'URI consentito per reindirizzare dopo la disconnessione.
-   `AllowedCorsOrigins`: Specifica l'origine del client in modo che IdentityServer consente chiamate multiorigine dall'origine.
-   `AllowedScopes`: Specifica le risorse a cui a che il client ha accesso. Per impostazione predefinita, un client non ha accesso a qualsiasi risorsa.
-   `AllowOfflineAccess`: Specifica se il client può richiedere token di aggiornamento.

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>Configurazione del flusso di autenticazione

Il flusso di autenticazione tra un client e IdentityServer può essere configurato specificando i tipi di concessione di `Client.AllowedGrantTypes` proprietà. Le specifiche di OpenID Connect e OAuth 2.0 definiscono una serie di flussi di autenticazione, tra cui:

-   Implicita. Questo flusso è ottimizzato per le applicazioni basate su browser e deve essere utilizzato per utente solo l'autenticazione, o le richieste di token di autenticazione e accesso. Tutti i token vengono trasmessi tramite il browser e pertanto funzioni come token di aggiornamento non sono consentiti.
-   Codice di autorizzazione. Questo flusso offre la possibilità di recuperare i token in un canale back, anziché il canale anteriore del browser, supportando anche l'autenticazione client.
-   Ibrido. Questo flusso è una combinazione dell'operatore implicito e tipi di concessione del codice di autorizzazione. Il token di identità viene trasmesso tramite il canale di browser e contiene la risposta di protocollo firmato insieme agli altri elementi, ad esempio il codice di autorizzazione. Dopo la convalida ha esito positivo della risposta canale deve essere utilizzato per recuperare l'accesso e token di aggiornamento.

> [!TIP]
> Utilizzare il flusso di autenticazione ibrido. Il flusso di autenticazione ibrida, è possibile ridurre un numero di attacchi che si applicano al canale di browser e viene illustrato il flusso consigliato per applicazioni native che desidera recuperare i token di accesso (e possibilmente i token di aggiornamento).

Per ulteriori informazioni sui flussi di autenticazione, vedere [tipi di concessione](https://identityserver4.readthedocs.io/en/release/topics/grant_types.html) nella documentazione di 4 IdentityServer.

### <a name="performing-authentication"></a>Esegue l'autenticazione

Per IdentityServer al rilascio di token per conto dell'utente, l'utente deve Accedi a IdentityServer. Tuttavia, IdentityServer non fornisce un'interfaccia utente o un database per l'autenticazione. Pertanto, nell'applicazione, riferimento eShopOnContainers ASP.NET Identity Core viene utilizzato per questo scopo.

L'app mobile eShopOnContainers autentica con IdentityServer con il flusso di autenticazione ibrido, come illustrato nella figura 9-2.

![](authentication-and-authorization-images/sign-in.png "Panoramica di alto livello del processo di accesso")

**Figura 9-2:** Panoramica del processo di accesso

Viene effettuata una richiesta di accesso per `<base endpoint>:5105/connect/authorize`. Dopo l'autenticazione, IdentityServer restituisce una risposta di autenticazione contenente un codice di autorizzazione e un token di identità. Il codice di autorizzazione viene quindi inviato al `<base endpoint>:5105/connect/token`, che risponde con l'accesso, l'identità e i token di aggiornamento.

Il eShopOnContainers app per dispositivi mobili segni-out di IdentityServer inviando una richiesta di `<base endpoint>:5105/connect/endsession`, con parametri aggiuntivi. Dopo l'uscita, IdentityServer risponde inviando un URI di reindirizzamento disconnessione registra l'app per dispositivi mobili. Nella figura 9-3 viene illustrato questo processo.

![](authentication-and-authorization-images/sign-out.png "Panoramica di alto livello del processo di disconnessione")

**Figura 9-3:** Panoramica del processo di disconnessione

Nell'app mobile eShopOnContainers, viene eseguita la comunicazione con IdentityServer dal `IdentityService` classe che implementa il `IIdentityService` interfaccia. Questa interfaccia consente di specificare che la classe di implementazione deve fornire `CreateAuthorizationRequest`, `CreateLogoutRequest`, e `GetTokenAsync` metodi.

#### <a name="signing-in"></a>Accesso

Quando l'utente tocca il **account di accesso** pulsante il `LoginView`, `SignInCommand` nel `LoginViewModel` classe viene eseguita, che a sua volta esegue il `SignInAsync` (metodo). Esempio di codice seguente viene illustrato questo metodo:

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

Questo metodo richiama il `CreateAuthorizationRequest` metodo il `IdentityService` (classe), come illustrato nell'esempio di codice seguente:

```csharp
public string CreateAuthorizationRequest()
{
    // Create URI to authorization endpoint
    var authorizeRequest = new AuthorizeRequest(GlobalSetting.Instance.IdentityEndpoint);

    // Dictionary with values for the authorize request
    var dic = new Dictionary<string, string>();
    dic.Add("client_id", GlobalSetting.Instance.ClientId);
    dic.Add("client_secret", GlobalSetting.Instance.ClientSecret); 
    dic.Add("response_type", "code id_token");
    dic.Add("scope", "openid profile basket orders locations marketing offline_access");
    dic.Add("redirect_uri", GlobalSetting.Instance.IdentityCallback);
    dic.Add("nonce", Guid.NewGuid().ToString("N"));
    dic.Add("code_challenge", CreateCodeChallenge());
    dic.Add("code_challenge_method", "S256");

    // Add CSRF token to protect against cross-site request forgery attacks.
    var currentCSRFToken = Guid.NewGuid().ToString("N");
    dic.Add("state", currentCSRFToken);

    var authorizeUri = authorizeRequest.Create(dic); 
    return authorizeUri;
}

```

Questo metodo crea l'URI del IdentityServer [endpoint di autorizzazione](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html), con i parametri necessari. Trova l'endpoint di autorizzazione `/connect/authorize` sulla porta 5105 dell'endpoint di base esposta come un'impostazione utente. Per ulteriori informazioni sulle impostazioni utente, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> La superficie di attacco dell'app mobile eShopOnContainers viene ridotto implementando la chiave di prova per l'estensione di codice Exchange (PKCE) per OAuth. PKCE impedisce che il codice di autorizzazione utilizzato se questo viene intercettato. A questo scopo, il client genera un verificatore segreto, un hash di cui viene passato nella richiesta di autorizzazione, e che viene visualizzato senza hash quando riscattando il codice di autorizzazione. Per ulteriori informazioni su PKCE, vedere [chiave di prova per lo scambio di codice dai client di OAuth pubblica](https://tools.ietf.org/html/rfc7636) sul sito web di Internet Engineering Task Force.

L'URI restituito viene archiviato nel `LoginUrl` proprietà la `LoginViewModel` classe. Quando il `IsLogin` proprietà diventa `true`, [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) nel `LoginView` diventa visibile. Il `WebView` Associa dati relativo [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/) proprietà per il `LoginUrl` proprietà del `LoginViewModel` classe e quindi effettua una richiesta di accesso a IdentityServer quando il `LoginUrl` è impostata su Endpoint di autorizzazione del IdentityServer. Quando IdentityServer riceve la richiesta e l'utente non autenticato, il `WebView` verrà reindirizzato alla pagina di accesso configurato, come illustrata nella figura 9-4.

![](authentication-and-authorization-images/login.png "Pagina di accesso visualizzato per la visualizzazione Web")

**Figura 9-4:** pagina account di accesso visualizzata dal WebView

Al termine dell'account di accesso, il [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) verrà reindirizzato a un URI restituito. Questo `WebView` navigazione causerà il `NavigateAsync` metodo la `LoginViewModel` classe da eseguire, come illustrato nell'esempio di codice seguente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    var authResponse = new AuthorizeResponse(url);  
    if (!string.IsNullOrWhiteSpace(authResponse.Code))  
    {  
        var userToken = await _identityService.GetTokenAsync(authResponse.Code);  
        string accessToken = userToken.AccessToken;  

        if (!string.IsNullOrWhiteSpace(accessToken))  
        {  
            Settings.AuthAccessToken = accessToken;  
            Settings.AuthIdToken = authResponse.IdentityToken;  

            await NavigationService.NavigateToAsync<MainViewModel>();  
            await NavigationService.RemoveLastFromBackStackAsync();  
        }  
    }  
    ...  
}
```

Questo metodo analizza la risposta di autenticazione che è contenuta nell'URI restituito e un codice di autorizzazione valido sono presentano, effettua una richiesta del IdentityServer [endpoint token](https://identityserver4.readthedocs.io/en/release/endpoints/token.html), passando il codice di autorizzazione, il Verifier secret PKCE e altri parametri necessari. Endpoint del token è `/connect/token` sulla porta 5105 dell'endpoint di base esposta come un'impostazione utente. Per ulteriori informazioni sulle impostazioni utente, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

>💡 **Suggerimento**: convalidare restituire gli URI. Anche se l'app mobile eShopOnContainers non convalida l'URI restituito, la procedura consigliata è per convalidare che l'URI restituito fa riferimento a un percorso noto, per impedire attacchi di reindirizzamento di apertura.

Se l'endpoint del token riceve un codice di autorizzazione valido e verifier secret PKCE, risponde con un token di accesso, un token di identità e un token di aggiornamento. Il token di accesso (che consente l'accesso alle risorse dell'API) e un token di identità vengono quindi archiviati come impostazioni dell'applicazione e viene eseguita la navigazione tra le pagine. Di conseguenza, l'effetto nell'app mobile eShopOnContainers generale è: condizione che gli utenti sono in grado di autenticarsi a IdentityServer, cui ci si sposta il `MainView` pagina, ovvero un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) che consente di visualizzare il `CatalogView` come la scheda selezionata.

Per informazioni sulla navigazione tra le pagine, vedere [navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md). Per informazioni su come [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) spostamento determina un metodo di modello di visualizzazione da eseguire, vedere [navigazione chiamata usando i comportamenti](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Per informazioni sulle impostazioni dell'applicazione, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> Consente inoltre una simulazione Accedi quando l'app è configurato per utilizzare i servizi fittizi nel eShopOnContainers il `SettingsView`. In questa modalità l'app comunica con IdentityServer, invece che consente all'utente di accedere utilizzando le credenziali.

#### <a name="signing-out"></a>Signing-out

Quando l'utente tocca il **LOG OUT** pulsante il `ProfileView`, `LogoutCommand` nel `ProfileViewModel` classe viene eseguita, che a sua volta esegue il `LogoutAsync` (metodo). Questo metodo esegue navigazione tra le pagine per il `LoginView` pagina, passando un `LogoutParameter` istanza impostata su `true` come parametro. Per ulteriori informazioni sul passaggio di parametri durante la navigazione tra le pagine, vedere [passando i parametri durante la navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Quando una vista viene creata e ci si sposta, la `InitializeAsync` esecuzione del metodo del modello di visualizzazione associata della vista, che viene quindi eseguito il `Logout` metodo il `LoginViewModel` (classe), come illustrato nell'esempio di codice seguente:

```csharp
private void Logout()  
{  
    var authIdToken = Settings.AuthIdToken;  
    var logoutRequest = _identityService.CreateLogoutRequest(authIdToken);  

    if (!string.IsNullOrEmpty(logoutRequest))  
    {  
        // Logout  
        LoginUrl = logoutRequest;  
    }  
    ...  
}
```

Questo metodo richiama il `CreateLogoutRequest` metodo la `IdentityService` classe, passando il token di identità recuperati dalle impostazioni dell'applicazione come un parametro. Per ulteriori informazioni sulle impostazioni dell'applicazione, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md). Nell'esempio di codice riportato di seguito viene illustrato il `CreateLogoutRequest` metodo:

```csharp
public string CreateLogoutRequest(string token)  
{  
    ...  
    return string.Format("{0}?id_token_hint={1}&post_logout_redirect_uri={2}",   
        GlobalSetting.Instance.LogoutEndpoint,  
        token,  
        GlobalSetting.Instance.LogoutCallback);  
}
```

Questo metodo crea l'URI del IdentityServer [terminare endpoint sessione](https://identityserver4.readthedocs.io/en/release/endpoints/endsession.html#refendsession), con i parametri necessari. Trova l'endpoint di fine sessione `/connect/endsession` sulla porta 5105 dell'endpoint di base esposta come un'impostazione utente. Per ulteriori informazioni sulle impostazioni utente, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

L'URI restituito viene archiviato nel `LoginUrl` proprietà la `LoginViewModel` classe. Mentre il `IsLogin` proprietà `true`, [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) nel `LoginView` è visibile. Il `WebView` Associa dati relativo [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/) proprietà per il `LoginUrl` proprietà del `LoginViewModel` classe e quindi effettua una richiesta di disconnessione a IdentityServer quando il `LoginUrl` è impostata su Endpoint di sessione finali del IdentityServer. Quando si IdentityServer riceve la richiesta, a condizione che l'utente è connesso, disconnessione. L'autenticazione viene registrata con un cookie gestito dal middleware di autenticazione del cookie da ASP.NET Core. Pertanto, disconnette IdentityServer rimuove il cookie di autenticazione e invia un reindirizzamento disconnessione post URI al client.

Nell'app mobile, il [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) verrà reindirizzato all'URI di reindirizzamento di disconnessione post. Questo `WebView` navigazione causerà il `NavigateAsync` metodo la `LoginViewModel` classe da eseguire, come illustrato nell'esempio di codice seguente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    Settings.AuthAccessToken = string.Empty;  
    Settings.AuthIdToken = string.Empty;  
    IsLogin = false;  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    ...  
}
```

Questo metodo cancella il token di identità e il token di accesso da impostazioni applicazione e imposta il `IsLogin` proprietà `false`, causando il [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) sul `LoginView` pagina diventi invisibile . Infine, il `LoginUrl` proprietà è impostata per l'URI di IdentityServer [endpoint di autorizzazione](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html), con i parametri necessari, in preparazione per la volta successiva che l'utente avvia un accesso aggiuntivo.

Per informazioni sulla navigazione tra le pagine, vedere [navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md). Per informazioni su come [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) spostamento determina un metodo di modello di visualizzazione da eseguire, vedere [navigazione chiamata usando i comportamenti](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Per informazioni sulle impostazioni dell'applicazione, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> Il eShopOnContainers consente inoltre una simulazione disconnessione quando l'app è configurato per usare servizi fittizi di SettingsView. In questa modalità l'app comunica con IdentityServer e invece Cancella tutti i token stored dall'impostazione dell'applicazione.

<a name="authorization" />

## <a name="authorization"></a>Autorizzazione

Dopo l'autenticazione, per autorizzare l'accesso, è spesso necessario API web di ASP.NET Core che consente a un servizio eseguire le API disponibili per alcuni utenti autenticati, ma non a tutti.

Limitare l'accesso a una route ASP.NET MVC di base può essere ottenuta applicando un attributo Authorize a un controller di o azione, che limita l'accesso al controller o un'azione per utenti autenticati, come illustrato nell'esempio di codice seguente:

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

Se un utente non autorizzato tenta di accedere a un controller o un'azione che è contrassegnato con il `Authorize` attributo, il framework MVC restituisce un codice di stato HTTP 401 (non autorizzato).

> [!NOTE]
> In è possono specificare i parametri di `Authorize` attributo per un'API per gli utenti specifici. Per ulteriori informazioni, vedere [autorizzazione](/aspnet/core/security/authorization/introduction/).

IdentityServer può essere integrato nel flusso di lavoro di autorizzazione in modo che i token di accesso fornisce autorizzazioni di controllo. Questo approccio è illustrato nella figura 9-5.

![](authentication-and-authorization-images/authorization.png "Autorizzazione da token di accesso")

**Figura 9-5:** autorizzazione dal token di accesso

L'app mobile eShopOnContainers comunica con l'identità di microservizio e richiede un token di accesso come parte del processo di autenticazione. Il token di accesso viene inoltrato alle API esposte dal microservizi ordinamento e acquisti come parte delle richieste di accesso. I token di accesso contengono informazioni sul client e l'utente. API quindi utilizzano tali informazioni per autorizzare l'accesso ai dati. Per informazioni su come configurare IdentityServer per proteggere le API, vedere [configurazione delle risorse dell'API](#configuring-api-resources).

### <a name="configuring-identityserver-to-perform-authorization"></a>Configurazione IdentityServer per eseguire l'autorizzazione

Per eseguire l'autorizzazione con IdentityServer, il middleware di autorizzazione deve essere aggiunto alla pipeline di richieste HTTP dell'applicazione web. Il middleware viene aggiunto nel `ConfigureAuth` metodo nell'applicazione web di `Startup` (classe), che viene richiamato dal `Configure` (metodo) e viene illustrato nell'esempio di codice seguente dall'applicazione eShopOnContainers riferimento:

```csharp
protected virtual void ConfigureAuth(IApplicationBuilder app)  
{  
    var identityUrl = Configuration.GetValue<string>("IdentityUrl");  
    app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions  
    {  
        Authority = identityUrl.ToString(),  
        ScopeName = "basket",  
        RequireHttpsMetadata = false  
    });  
} 
```

Questo metodo garantisce che l'API solo è possibile accedere con un token di accesso valido. Il middleware convalida il token in ingresso per garantire che venga inviata da un'autorità emittente attendibile e che il token è valido per l'utilizzo con l'API che riceve la convalida. Pertanto, esplorazione al controller di ordinamento o basket restituirà un 401 (non autorizzato) codice di stato HTTP, che indica che è necessario un token di accesso.

> [!NOTE]
> Il middleware di autorizzazione del IdentityServer deve essere aggiunto alla pipeline di richieste HTTP dell'applicazione web prima di aggiungere MVC con `app.UseMvc()` o `app.UseMvcWithDefaultRoute()`.

### <a name="making-access-requests-to-apis"></a>Creazione di richieste di accesso alle API

Quando si effettuano richieste di microservizi carrello e l'ordinamento, l'accesso token, ottenuti da IdentityServer durante il processo di autenticazione deve essere incluso nella richiesta, come illustrato nell'esempio di codice seguente:

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

Il token di accesso viene archiviato come un'impostazione dell'applicazione e viene recuperato dall'archivio specifico della piattaforma e inclusi nella chiamata al `GetOrderAsync` metodo la `OrderService` classe.

Analogamente, il token di accesso deve essere incluso quando l'invio dei dati per un IdentityServer protetto API, come illustrato nell'esempio di codice seguente:

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

Il token di accesso viene recuperato dall'archivio specifico della piattaforma e incluso nella chiamata al `UpdateBasketAsync` metodo la `BasketService` classe.

Il `RequestProvider` (classe), nell'app mobile eShopOnContainers, utilizza la `HttpClient` classe per effettuare richieste alle API REST esposti dall'applicazione di riferimento eShopOnContainers. Quando apportato richieste per l'ordinamento e basket API, che richiedono l'autorizzazione, un token di accesso valido deve essere incluso con la richiesta. A questo scopo, aggiungere il token di accesso alle intestazioni del `HttpClient` dell'istanza, come illustrato nell'esempio di codice seguente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

Il `DefaultRequestHeaders` proprietà del `HttpClient` classe espone le intestazioni che vengono inviate con ogni richiesta e il token di accesso viene aggiunto per il `Authorization` intestazione con la stringa di prefisso `Bearer`. Quando la richiesta viene inviata a un'API REST, il valore di `Authorization` intestazione verrà estratti e convalidata per garantire che ha inviato da un'autorità emittente attendibile e usata per determinare se l'utente dispone dell'autorizzazione per richiamare l'API che riceve.

Per ulteriori informazioni su come l'app mobile eShopOnContainers effettua le richieste web, vedere [l'accesso a dati remoti](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).

## <a name="summary"></a>Riepilogo

Esistono molti approcci per l'integrazione di autenticazione e autorizzazione in un'app xamarin. Forms che comunica con un'applicazione web ASP.NET MVC. L'app mobile eShopOnContainers esegue l'autenticazione e autorizzazione con un microservizio identità nei contenitori che utilizza 4 IdentityServer. IdentityServer è un framework open source di OpenID Connect e OAuth 2.0 per ASP.NET Core che si integra con ASP.NET Identity Core per eseguire l'autenticazione token di connessione.

L'app per dispositivi mobili richiede token di sicurezza da IdentityServer, per autenticare un utente o per l'accesso a una risorsa. Quando si accede a una risorsa, un token di accesso deve essere incluso nella richiesta per le API che richiedono l'autorizzazione. Middleware del IdentityServer convalida i token di accesso in ingresso per garantire che inviati da un'autorità emittente attendibile e che sono validi per l'utilizzo con l'API che li riceve.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
