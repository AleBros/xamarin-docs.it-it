---
title: Autenticazione e autorizzazione
description: Questo capitolo illustra il modo in cui l'app per dispositivi mobili eShopOnContainers esegue l'autenticazione e l'autorizzazione per i microservizi in contenitori.
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: 528ccd66cc013f83752d93251cb9714115b29819
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915281"
---
# <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

L'autenticazione è il processo di recupero delle credenziali di identificazione, ad esempio il nome e la password, da un utente e la convalida delle credenziali rispetto a un'autorità. Se le credenziali sono valide, l'entità che ha inviato le credenziali viene considerata un'identità autenticata. Una volta autenticata un'identità, un processo di autorizzazione determina se tale identità ha accesso a una determinata risorsa.

Sono disponibili molti approcci per l'integrazione di autenticazione e autorizzazione in un'app Novell. Forms che comunica con un'applicazione Web MVC ASP.NET, tra cui l'uso di ASP.NET Core identità, provider di autenticazione esterni, ad esempio Microsoft, Google, Il middleware di autenticazione di Facebook o Twitter. L'app per dispositivi mobili eShopOnContainers esegue l'autenticazione e l'autorizzazione con un microservizio di identità in contenitori che usa IdentityServer 4. L'app per dispositivi mobili richiede token di sicurezza da IdentityServer per l'autenticazione di un utente o per l'accesso a una risorsa. Per IdentityServer per emettere token per conto di un utente, l'utente deve accedere a IdentityServer. Tuttavia, IdentityServer non fornisce un'interfaccia utente o un database per l'autenticazione. Nell'applicazione di riferimento eShopOnContainers viene quindi usata ASP.NET Core identità a questo scopo.

## <a name="authentication"></a>Autenticazione

L'autenticazione è obbligatoria quando un'applicazione deve essere in grado di identificare l'identità dell'utente corrente. Il meccanismo principale di ASP.NET Core per identificare gli utenti è il sistema di appartenenze ASP.NET Core identità, che archivia le informazioni sugli utenti in un archivio dati configurato dallo sviluppatore. In genere, questo archivio dati sarà un archivio EntityFramework, anche se è possibile usare archivi personalizzati o pacchetti di terze parti per archiviare le informazioni sull'identità in archiviazione di Azure, Azure Cosmos DB o in altre posizioni.

Per gli scenari di autenticazione che usano un archivio dati utente locale e che mantengono le informazioni di identità tra le richieste tramite cookie (come avviene in genere nelle applicazioni Web MVC ASP.NET), ASP.NET Core identità è una soluzione adatta. Tuttavia, i cookie non sono sempre un mezzo naturale per salvare in modo permanente e trasmettere i dati. Ad esempio, un'applicazione Web ASP.NET Core che espone gli endpoint RESTful a cui si accede da un'app per dispositivi mobili in genere deve usare l'autenticazione bearer token, perché in questo scenario non è possibile usare i cookie. Tuttavia, i token di porta possono essere facilmente recuperati e inclusi nell'intestazione dell'autorizzazione delle richieste Web effettuate dall'app per dispositivi mobili.

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>Emissione di token di portali con IdentityServer 4

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4) è un framework open source di OpenID Connect e OAuth 2,0 per ASP.NET Core, che può essere usato per molti scenari di autenticazione e autorizzazione, tra cui l'emissione di token di sicurezza per gli utenti di ASP.NET Core identità locali.

> [!NOTE]
> OpenID Connect e OAuth 2,0 sono molto simili, con responsabilità diverse.

OpenID Connect è un livello di autenticazione al di sopra del protocollo OAuth 2,0. OAuth 2 è un protocollo che consente alle applicazioni di richiedere token di accesso da un servizio token di sicurezza e di usarli per comunicare con le API. Questa delega riduce la complessità nelle applicazioni client e nelle API, poiché l'autenticazione e l'autorizzazione possono essere centralizzate.

La combinazione di OpenID Connect e OAuth 2,0 combina le due problematiche di sicurezza fondamentali relative all'autenticazione e all'accesso alle API e IdentityServer 4 è un'implementazione di questi protocolli.

Nelle applicazioni che usano la comunicazione da client a microservizio diretta, ad esempio l'applicazione di riferimento eShopOnContainers, un microservizio di autenticazione dedicato che funge da servizio token di sicurezza (STS) può essere usato per autenticare gli utenti, come illustrato nella figura 9-1. Per altre informazioni sulla comunicazione da client a microservizio diretta, vedere [comunicazione tra client e microservizi](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices).

![](authentication-and-authorization-images/authentication.png "Authentication by a dedicated authentication microservice")

**Figura 9-1:** Autenticazione tramite un microservizio di autenticazione dedicato

L'app per dispositivi mobili eShopOnContainers comunica con il microservizio di identità, che usa IdentityServer 4 per eseguire l'autenticazione e il controllo di accesso per le API. Quindi, l'app per dispositivi mobili richiede token da IdentityServer per l'autenticazione di un utente o per l'accesso a una risorsa:

- L'autenticazione degli utenti con IdentityServer viene eseguita dall'app per dispositivi mobili che richiede un token di *identità* , che rappresenta il risultato di un processo di autenticazione. Al minimo, contiene un identificatore per l'utente e informazioni su come e quando l'utente è stato autenticato. Può inoltre contenere dati di identità aggiuntivi.
- L'accesso a una risorsa con IdentityServer viene effettuato dall'app per dispositivi mobili che richiede un token di *accesso* , che consente l'accesso a una risorsa API. I client richiedono token di accesso e li inviano all'API. I token di accesso contengono informazioni sul client e l'utente (se presente). Le API usano quindi tali informazioni per autorizzare l'accesso ai propri dati.

> [!NOTE]
> Un client deve essere registrato con IdentityServer prima di poter richiedere i token.

### <a name="adding-identityserver-to-a-web-application"></a>Aggiunta di IdentityServer a un'applicazione Web

Per consentire a un'applicazione Web ASP.NET Core di utilizzare IdentityServer 4, è necessario aggiungerla alla soluzione Visual Studio dell'applicazione Web. Per ulteriori informazioni, vedere la pagina relativa alla [Panoramica](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) nella documentazione di IdentityServer.

Quando IdentityServer è incluso nella soluzione Visual Studio dell'applicazione Web, è necessario aggiungerlo alla pipeline di elaborazione delle richieste HTTP dell'applicazione Web in modo che possa gestire le richieste agli endpoint OpenID Connect e OAuth 2,0. Questa operazione viene eseguita nel metodo `Configure` nella classe `Startup` dell'applicazione Web, come illustrato nell'esempio di codice seguente:

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

L'ordine è importante nella pipeline di elaborazione delle richieste HTTP dell'applicazione Web. Di conseguenza, IdentityServer deve essere aggiunto alla pipeline prima del Framework dell'interfaccia utente che implementa la schermata di accesso.

### <a name="configuring-identityserver"></a>Configurazione di IdentityServer

IdentityServer deve essere configurato nel metodo `ConfigureServices` nella classe `Startup` dell'applicazione Web chiamando il metodo `services.AddIdentityServer`, come illustrato nell'esempio di codice seguente dall'applicazione di riferimento eShopOnContainers:

```csharp
public void ConfigureServices(IServiceCollection services)  
{  
    ...  
    services.AddIdentityServer(x => x.IssuerUri = "null")  
        .AddSigningCredential(Certificate.Get())                 
        .AddAspNetIdentity<ApplicationUser>()  
        .AddConfigurationStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .AddOperationalStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .Services.AddTransient<IProfileService, ProfileService>();  
}
```

Dopo la chiamata del metodo `services.AddIdentityServer`, vengono chiamate API Fluent aggiuntive per configurare gli elementi seguenti:

- Credenziali utilizzate per la firma.
- Risorse di identità e API a cui gli utenti possono richiedere l'accesso.
- Client che si connetteranno ai token della richiesta.
- Identità ASP.NET Core.

> [!TIP]
> Caricare dinamicamente la configurazione di IdentityServer 4. Le API di IdentityServer 4 consentono di configurare IdentityServer da un elenco in memoria di oggetti di configurazione. Nell'applicazione di riferimento eShopOnContainers queste raccolte in memoria sono hardcoded nell'applicazione. Negli scenari di produzione, tuttavia, possono essere caricati dinamicamente da un file di configurazione o da un database.

Per informazioni sulla configurazione di IdentityServer per l'uso di ASP.NET Core identità, vedere [uso di ASP.NET Core identità](https://identityserver4.readthedocs.io/en/latest/quickstarts/6_aspnet_identity.html) nella documentazione di IdentityServer.

#### <a name="configuring-api-resources"></a>Configurazione delle risorse API

Quando si configurano le risorse API, il metodo `AddInMemoryApiResources` prevede una raccolta di `IEnumerable<ApiResource>`. Nell'esempio di codice seguente viene illustrato il metodo `GetApis` che fornisce questa raccolta nell'applicazione di riferimento eShopOnContainers:

```csharp
public static IEnumerable<ApiResource> GetApis()  
{  
    return new List<ApiResource>  
    {  
        new ApiResource("orders", "Orders Service"),  
        new ApiResource("basket", "Basket Service")  
    };  
}
```

Questo metodo specifica che IdentityServer deve proteggere le API Orders e basket. Di conseguenza, i token di accesso gestiti di IdentityServer saranno necessari quando si effettuano chiamate a queste API. Per ulteriori informazioni sul tipo di `ApiResource`, vedere la pagina relativa alle [risorse API](https://identityserver4.readthedocs.io/en/latest/reference/api_resource.html) nella documentazione di IdentityServer 4.

#### <a name="configuring-identity-resources"></a>Configurazione delle risorse di identità

Quando si configurano le risorse Identity, il metodo `AddInMemoryIdentityResources` prevede una raccolta di `IEnumerable<IdentityResource>`. Le risorse di identità sono dati come l'ID utente, il nome o l'indirizzo di posta elettronica. Ogni risorsa di identità ha un nome univoco e possono essere assegnati tipi di attestazione arbitrari, che verranno quindi inclusi nel token di identità per l'utente. Nell'esempio di codice seguente viene illustrato il metodo `GetResources` che fornisce questa raccolta nell'applicazione di riferimento eShopOnContainers:

```csharp
public static IEnumerable<IdentityResource> GetResources()  
{  
    return new List<IdentityResource>  
    {  
        new IdentityResources.OpenId(),  
        new IdentityResources.Profile()  
    };  
}
```

La specifica di OpenID Connect specifica alcune [risorse di identità standard](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). Il requisito minimo è che è disponibile il supporto per la creazione di un ID univoco per gli utenti. Questa operazione viene eseguita esponendo la risorsa di identità `IdentityResources.OpenId`.

> [!NOTE]
> La classe `IdentityResources` supporta tutti gli ambiti definiti nella specifica di OpenID Connect (OpenID, posta elettronica, profilo, telefono e indirizzo).

IdentityServer supporta anche la definizione di risorse di identità personalizzate. Per ulteriori informazioni, vedere [definizione di risorse di identità personalizzate](http://docs.identityserver.io/en/latest/topics/resources.html#defining-custom-identity-resources) nella documentazione di IdentityServer. Per ulteriori informazioni sul tipo di `IdentityResource`, vedere la pagina relativa alla [risorsa Identity](https://identityserver4.readthedocs.io/en/latest/reference/identity_resource.html) nella documentazione di IdentityServer 4.

#### <a name="configuring-clients"></a>Configurazione dei client

I client sono applicazioni che possono richiedere token da IdentityServer. In genere, è necessario definire le impostazioni seguenti per ogni client come minimo:

- ID client univoco.
- Interazioni consentite con il servizio token (noto come tipo di concessione).
- Il percorso a cui vengono inviati i token di accesso e di identità (noto come URI di reindirizzamento).
- Elenco di risorse a cui il client è autorizzato ad accedere (noti come ambiti).

Quando si configurano i client, il metodo `AddInMemoryClients` prevede una raccolta di `IEnumerable<Client>`. L'esempio di codice seguente illustra la configurazione per l'app per dispositivi mobili eShopOnContainers nel metodo `GetClients` che fornisce questa raccolta nell'applicazione di riferimento eShopOnContainers:

```csharp
public static IEnumerable<Client> GetClients(Dictionary<string,string> clientsUrl)
{
    return new List<Client>
    {
        ...
        new Client
        {
            ClientId = "xamarin",
            ClientName = "eShop Xamarin OpenId Client",
            AllowedGrantTypes = GrantTypes.Hybrid,
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            RedirectUris = { clientsUrl["Xamarin"] },
            RequireConsent = false,
            RequirePkce = true,
            PostLogoutRedirectUris = { $"{clientsUrl["Xamarin"]}/Account/Redirecting" },
            AllowedCorsOrigins = { "http://eshopxamarin" },
            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.OfflineAccess,
                "orders",
                "basket"
            },
            AllowOfflineAccess = true,
            AllowAccessTokensViaBrowser = true
        },
        ...
    };
}
```

Questa configurazione specifica i dati per le proprietà seguenti:

- `ClientId`: ID univoco per il client.
- `ClientName`: il nome visualizzato del client utilizzato per la registrazione e la schermata di consenso.
- `AllowedGrantTypes`: specifica il modo in cui un client vuole interagire con IdentityServer. Per altre informazioni, vedere [configurazione del flusso di autenticazione](#configuring_the_authentication_flow).
- `ClientSecrets`: specifica le credenziali del segreto client usate per la richiesta di token dall'endpoint del token.
- `RedirectUris`: specifica gli URI consentiti a cui restituire i token o i codici di autorizzazione.
- `RequireConsent`: specifica se è necessaria una schermata di consenso.
- `RequirePkce`: specifica se i client che usano un codice di autorizzazione devono inviare una chiave di prova.
- `PostLogoutRedirectUris`: specifica gli URI consentiti a cui eseguire il reindirizzamento dopo la disconnessione.
- `AllowedCorsOrigins`: specifica l'origine del client in modo che IdentityServer possa consentire chiamate tra le origini dall'origine.
- `AllowedScopes`: specifica le risorse a cui il client può accedere. Per impostazione predefinita, un client non ha accesso alle risorse.
- `AllowOfflineAccess`: specifica se il client può richiedere token di aggiornamento.

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>Configurazione del flusso di autenticazione

Il flusso di autenticazione tra un client e IdentityServer può essere configurato specificando i tipi di concessione nella proprietà `Client.AllowedGrantTypes`. Le specifiche di OpenID Connect e OAuth 2,0 definiscono una serie di flussi di autenticazione, tra cui:

- Implicito. Questo flusso è ottimizzato per le applicazioni basate su browser e deve essere usato per le richieste di autenticazione utente o per l'autenticazione e il token di accesso. Tutti i token vengono trasmessi tramite il browser e pertanto le funzionalità avanzate come i token di aggiornamento non sono consentite.
- Codice di autorizzazione. Questo flusso consente di recuperare i token su un canale indietro, anziché sul canale front browser, supportando al tempo stesso l'autenticazione client.
- Ibrido. Questo flusso è una combinazione dei tipi di concessione del codice implicito e di autorizzazione. Il token di identità viene trasmesso tramite il canale del browser e contiene la risposta del protocollo firmato insieme ad altri elementi, ad esempio il codice di autorizzazione. Al termine della convalida della risposta, è necessario usare il canale back per recuperare il token di accesso e di aggiornamento.

> [!TIP]
> Usare il flusso di autenticazione ibrida. Il flusso di autenticazione ibrida attenua un numero di attacchi che si applicano al canale del browser ed è il flusso consigliato per le applicazioni native che vogliono recuperare i token di accesso (e possibilmente i token di aggiornamento).

Per altre informazioni sui flussi di autenticazione, vedere [concedere i tipi](https://identityserver4.readthedocs.io/en/latest/topics/grant_types.html) nella documentazione di IdentityServer 4.

### <a name="performing-authentication"></a>Esecuzione dell'autenticazione

Per IdentityServer per emettere token per conto di un utente, l'utente deve accedere a IdentityServer. Tuttavia, IdentityServer non fornisce un'interfaccia utente o un database per l'autenticazione. Nell'applicazione di riferimento eShopOnContainers viene quindi usata ASP.NET Core identità a questo scopo.

L'app per dispositivi mobili eShopOnContainers esegue l'autenticazione con IdentityServer con il flusso di autenticazione ibrido, illustrato nella figura 9-2.

![](authentication-and-authorization-images/sign-in.png "High-level overview of the sign-in process")

**Figura 9-2:** Panoramica di alto livello del processo di accesso

Viene eseguita una richiesta di accesso per `<base endpoint>:5105/connect/authorize`. Dopo l'autenticazione riuscita, IdentityServer restituisce una risposta di autenticazione contenente un codice di autorizzazione e un token di identità. Il codice di autorizzazione viene quindi inviato a `<base endpoint>:5105/connect/token`, che risponde con i token di accesso, identità e aggiornamento.

L'app per dispositivi mobili eShopOnContainers si disconnette da IdentityServer inviando una richiesta a `<base endpoint>:5105/connect/endsession`con parametri aggiuntivi. Dopo la disconnessione, IdentityServer risponde inviando un URI di reindirizzamento dopo la disconnessione all'app per dispositivi mobili. La figura 9-3 illustra questo processo.

![](authentication-and-authorization-images/sign-out.png "High-level overview of the sign-out process")

**Figura 9-3:** Panoramica di alto livello del processo di disconnessione

Nell'app per dispositivi mobili eShopOnContainers, la comunicazione con IdentityServer viene eseguita dalla classe `IdentityService`, che implementa l'interfaccia `IIdentityService`. Questa interfaccia specifica che la classe di implementazione deve fornire i metodi `CreateAuthorizationRequest`, `CreateLogoutRequest`e `GetTokenAsync`.

#### <a name="signing-in"></a>Accesso

Quando l'utente tocca il pulsante di **accesso** nella `LoginView`, viene eseguita la `SignInCommand` della classe `LoginViewModel`, che a sua volta esegue il metodo `SignInAsync`. L'esempio di codice seguente illustra il metodo:

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

Questo metodo richiama il metodo `CreateAuthorizationRequest` nella classe `IdentityService`, illustrato nell'esempio di codice seguente:

```csharp
public string CreateAuthorizationRequest()
{
    // Create URI to authorization endpoint
    var authorizeRequest = new AuthorizeRequest(GlobalSetting.Instance.IdentityEndpoint);

    // Dictionary with values for the authorize request
    var dic = new Dictionary<string, string>();
    dic.Add("client_id", GlobalSetting.Instance.ClientId);
    dic.Add("client_secret", GlobalSetting.Instance.ClientSecret); 
    dic.Add("response_type", "code id_token");
    dic.Add("scope", "openid profile basket orders locations marketing offline_access");
    dic.Add("redirect_uri", GlobalSetting.Instance.Callback);
    dic.Add("nonce", Guid.NewGuid().ToString("N"));
    dic.Add("code_challenge", CreateCodeChallenge());
    dic.Add("code_challenge_method", "S256");

    // Add CSRF token to protect against cross-site request forgery attacks.
    var currentCSRFToken = Guid.NewGuid().ToString("N");
    dic.Add("state", currentCSRFToken);

    var authorizeUri = authorizeRequest.Create(dic); 
    return authorizeUri;
}

```

Questo metodo crea l'URI per l' [endpoint di autorizzazione](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)di IdentityServer, con i parametri obbligatori. L'endpoint di autorizzazione si trova in `/connect/authorize` sulla porta 5105 dell'endpoint di base esposto come impostazione utente. Per ulteriori informazioni sulle impostazioni utente, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> La superficie di attacco dell'app per dispositivi mobili eShopOnContainers viene ridotta implementando l'estensione Proof Key for code Exchange (PKCE) in OAuth. PKCE protegge il codice di autorizzazione da usare se viene intercettato. Questa operazione viene eseguita dal client che genera un verificatore di segreto, un hash che viene passato nella richiesta di autorizzazione e che viene presentato con unhash quando si riscatta il codice di autorizzazione. Per ulteriori informazioni su PKCE, vedere la [chiave di prova per lo scambio di codice da parte di client pubblici OAuth](https://tools.ietf.org/html/rfc7636) nel sito Web di Internet Engineering Task Force.

L'URI restituito viene archiviato nella proprietà `LoginUrl` della classe `LoginViewModel`. Quando la proprietà `IsLogin` diventa `true`, il [`WebView`](xref:Xamarin.Forms.WebView) nel `LoginView` diventa visibile. Il `WebView` dati associa la relativa proprietà [`Source`](xref:Xamarin.Forms.WebView.Source) alla proprietà `LoginUrl` della classe `LoginViewModel` e quindi effettua una richiesta di accesso a IdentityServer quando la proprietà `LoginUrl` è impostata sull'endpoint di autorizzazione di IdentityServer. Quando IdentityServer riceve la richiesta e l'utente non viene autenticato, il `WebView` verrà reindirizzato alla pagina di accesso configurata, come illustrato nella figura 9-4.

![](authentication-and-authorization-images/login.png "Login page displayed by the WebView")

**Figura 9-4:** Pagina di accesso visualizzata da WebView

Una volta completato l'accesso, l' [`WebView`](xref:Xamarin.Forms.WebView) verrà reindirizzato a un URI restituito. Questa navigazione `WebView` provocherà l'esecuzione del metodo `NavigateAsync` della classe `LoginViewModel`, illustrato nell'esempio di codice seguente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    var authResponse = new AuthorizeResponse(url);  
    if (!string.IsNullOrWhiteSpace(authResponse.Code))  
    {  
        var userToken = await _identityService.GetTokenAsync(authResponse.Code);  
        string accessToken = userToken.AccessToken;  

        if (!string.IsNullOrWhiteSpace(accessToken))  
        {  
            Settings.AuthAccessToken = accessToken;  
            Settings.AuthIdToken = authResponse.IdentityToken;  

            await NavigationService.NavigateToAsync<MainViewModel>();  
            await NavigationService.RemoveLastFromBackStackAsync();  
        }  
    }  
    ...  
}
```

Questo metodo analizza la risposta di autenticazione contenuta nell'URI restituito e, a condizione che sia presente un codice di autorizzazione valido, esegue una richiesta all' [endpoint token](https://identityserver4.readthedocs.io/en/latest/endpoints/token.html)di IdentityServer, passando il codice di autorizzazione, il PKCE Secret Verifier e altri parametri obbligatori. L'endpoint token si trova in `/connect/token` sulla porta 5105 dell'endpoint di base esposto come impostazione utente. Per ulteriori informazioni sulle impostazioni utente, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!TIP]
> Convalidare gli URI restituiti. Sebbene l'app per dispositivi mobili eShopOnContainers non convalidi l'URI restituito, la procedura consigliata consiste nel convalidare che l'URI restituito si riferisce a una posizione nota, per impedire gli attacchi open-redirect.

Se l'endpoint del token riceve un codice di autorizzazione valido e PKCE Secret Verifier, risponde con un token di accesso, un token di identità e un token di aggiornamento. Il token di accesso, che consente l'accesso alle risorse API, e il token di identità vengono quindi archiviati come impostazioni dell'applicazione e l'esplorazione delle pagine viene eseguita. Pertanto, l'effetto complessivo nell'app per dispositivi mobili eShopOnContainers è il seguente: purché gli utenti siano in grado di eseguire l'autenticazione con IdentityServer, vengono spostati nella pagina `MainView`, ovvero un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) che visualizza il `CatalogView` come scheda selezionata.

Per informazioni sull'esplorazione delle pagine, vedere [navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md). Per informazioni sul modo in cui la navigazione [`WebView`](xref:Xamarin.Forms.WebView) comporta l'esecuzione di un metodo di visualizzazione del modello, vedere [richiamo della navigazione mediante i comportamenti](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Per informazioni sulle impostazioni dell'applicazione, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> Il eShopOnContainers consente anche l'accesso fittizio quando l'app è configurata per l'uso di servizi fittizi nel `SettingsView`. In questa modalità l'app non comunica con IdentityServer, ma consente all'utente di eseguire l'accesso con qualsiasi credenziale.

#### <a name="signing-out"></a>Disconnessione

Quando l'utente tocca il pulsante **Disconnetti** nella `ProfileView`, viene eseguito il `LogoutCommand` nella classe `ProfileViewModel`, che a sua volta esegue il metodo `LogoutAsync`. Questo metodo esegue l'esplorazione delle pagine della pagina `LoginView`, passando un'istanza di `LogoutParameter` impostata su `true` come parametro. Per ulteriori informazioni sul passaggio di parametri durante l'esplorazione delle pagine, vedere [passaggio di parametri durante la navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Quando si crea e si passa a una vista, viene eseguito il `InitializeAsync` metodo del modello di visualizzazione associato della vista, che esegue quindi il metodo `Logout` della classe `LoginViewModel`, illustrato nell'esempio di codice seguente:

```csharp
private void Logout()  
{  
    var authIdToken = Settings.AuthIdToken;  
    var logoutRequest = _identityService.CreateLogoutRequest(authIdToken);  

    if (!string.IsNullOrEmpty(logoutRequest))  
    {  
        // Logout  
        LoginUrl = logoutRequest;  
    }  
    ...  
}
```

Questo metodo richiama il metodo `CreateLogoutRequest` nella classe `IdentityService`, passando il token di identità recuperato dalle impostazioni dell'applicazione come parametro. Per ulteriori informazioni sulle impostazioni dell'applicazione, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md). L'esempio di codice seguente illustra il metodo `CreateLogoutRequest`:

```csharp
public string CreateLogoutRequest(string token)  
{  
    ...  
    return string.Format("{0}?id_token_hint={1}&post_logout_redirect_uri={2}",   
        GlobalSetting.Instance.LogoutEndpoint,  
        token,  
        GlobalSetting.Instance.LogoutCallback);  
}
```

Questo metodo crea l'URI dell'endpoint della [sessione finale](https://identityserver4.readthedocs.io/en/latest/endpoints/endsession.html#refendsession)di IdentityServer con i parametri necessari. L'endpoint della sessione finale si trova in `/connect/endsession` sulla porta 5105 dell'endpoint di base esposto come impostazione utente. Per ulteriori informazioni sulle impostazioni utente, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

L'URI restituito viene archiviato nella proprietà `LoginUrl` della classe `LoginViewModel`. Mentre la proprietà `IsLogin` è `true`, il [`WebView`](xref:Xamarin.Forms.WebView) nel `LoginView` è visibile. Il `WebView` dati associa la relativa proprietà [`Source`](xref:Xamarin.Forms.WebView.Source) alla proprietà `LoginUrl` della classe `LoginViewModel` e quindi effettua una richiesta di disconnessione a IdentityServer quando la proprietà `LoginUrl` è impostata sull'endpoint della sessione finale di IdentityServer. Quando IdentityServer riceve questa richiesta, a condizione che l'utente abbia eseguito l'accesso, si verifica la disconnessione. L'autenticazione viene rilevata con un cookie gestito dal middleware di autenticazione dei cookie da ASP.NET Core. Quindi, la disconnessione da IdentityServer rimuove il cookie di autenticazione e invia di nuovo un URI di reindirizzamento post-disconnessione al client.

Nell'app per dispositivi mobili, il [`WebView`](xref:Xamarin.Forms.WebView) verrà reindirizzato all'URI di reindirizzamento post-disconnessione. Questa navigazione `WebView` provocherà l'esecuzione del metodo `NavigateAsync` della classe `LoginViewModel`, illustrato nell'esempio di codice seguente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    Settings.AuthAccessToken = string.Empty;  
    Settings.AuthIdToken = string.Empty;  
    IsLogin = false;  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    ...  
}
```

Questo metodo cancella sia il token di identità che il token di accesso dalle impostazioni dell'applicazione e imposta la proprietà `IsLogin` su `false`, che fa sì che il [`WebView`](xref:Xamarin.Forms.WebView) nella pagina `LoginView` diventi invisibile. Infine, la proprietà `LoginUrl` viene impostata sull'URI dell' [endpoint di autorizzazione](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)di IdentityServer, con i parametri obbligatori, in preparazione per la prossima volta che l'utente avvia un accesso.

Per informazioni sull'esplorazione delle pagine, vedere [navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md). Per informazioni sul modo in cui la navigazione [`WebView`](xref:Xamarin.Forms.WebView) comporta l'esecuzione di un metodo di visualizzazione del modello, vedere [richiamo della navigazione mediante i comportamenti](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Per informazioni sulle impostazioni dell'applicazione, vedere [Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> Il eShopOnContainers consente anche una disconnessione fittizia quando l'app è configurata per l'uso di servizi fittizi in SettingsView. In questa modalità l'app non comunica con IdentityServer e cancella invece tutti i token archiviati dalle impostazioni dell'applicazione.

<a name="authorization" />

## <a name="authorization"></a>Autorizzazione

Dopo l'autenticazione, le API Web di ASP.NET Core spesso devono autorizzare l'accesso, che consente a un servizio di rendere disponibili le API per alcuni utenti autenticati, ma non per tutti.

La limitazione dell'accesso a una route ASP.NET Core MVC può essere ottenuta applicando un attributo di autorizzazione a un controller o un'azione, che limita l'accesso al controller o all'azione agli utenti autenticati, come illustrato nell'esempio di codice seguente:

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

Se un utente non autorizzato tenta di accedere a un controller o a un'azione contrassegnata con l'attributo `Authorize`, il framework MVC restituisce un codice di stato HTTP 401 (non autorizzato).

> [!NOTE]
> I parametri possono essere specificati nell'attributo `Authorize` per limitare l'API a utenti specifici. Per ulteriori informazioni, vedere [autorizzazione](/aspnet/core/security/authorization/introduction/).

IdentityServer possono essere integrate nel flusso di lavoro di autorizzazione in modo che i token di accesso forniscano l'autorizzazione Control. Questo approccio è illustrato nella figura 9-5.

![](authentication-and-authorization-images/authorization.png "Authorization by access token")

**Figura 9-5:** Autorizzazione per token di accesso

L'app per dispositivi mobili eShopOnContainers comunica con il microservizio di identità e richiede un token di accesso come parte del processo di autenticazione. Il token di accesso viene quindi inviato alle API esposte dai microservizi ordering e basket come parte delle richieste di accesso. I token di accesso contengono informazioni sul client e sull'utente. Le API usano quindi tali informazioni per autorizzare l'accesso ai propri dati. Per informazioni su come configurare IdentityServer per proteggere le API, vedere [Configuring API Resources](#configuring-api-resources).

### <a name="configuring-identityserver-to-perform-authorization"></a>Configurazione di IdentityServer per l'esecuzione dell'autorizzazione

Per eseguire l'autorizzazione con IdentityServer, è necessario aggiungere il middleware di autorizzazione alla pipeline di richieste HTTP dell'applicazione Web. Il middleware viene aggiunto nel metodo `ConfigureAuth` nella classe `Startup` dell'applicazione Web, che viene richiamata dal metodo `Configure` e viene illustrato nell'esempio di codice seguente dall'applicazione di riferimento eShopOnContainers:

```csharp
protected virtual void ConfigureAuth(IApplicationBuilder app)  
{  
    var identityUrl = Configuration.GetValue<string>("IdentityUrl");  
    app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions  
    {  
        Authority = identityUrl.ToString(),  
        ScopeName = "basket",  
        RequireHttpsMetadata = false  
    });  
} 
```

Questo metodo assicura che l'API possa accedere solo con un token di accesso valido. Il middleware convalida il token in ingresso per assicurarsi che venga inviato da un'autorità emittente attendibile e verifica che il token sia valido per l'uso con l'API che lo riceve. Pertanto, l'esplorazione del controller di ordinamento o basket restituirà un codice di stato HTTP 401 (non autorizzato), che indica che è necessario un token di accesso.

> [!NOTE]
> Il middleware di autorizzazione di IdentityServer deve essere aggiunto alla pipeline di richieste HTTP dell'applicazione Web prima di aggiungere MVC con `app.UseMvc()` o `app.UseMvcWithDefaultRoute()`.

### <a name="making-access-requests-to-apis"></a>Creazione di richieste di accesso alle API

Quando si effettuano richieste ai microservizi ordering e basket, il token di accesso ottenuto da IdentityServer durante il processo di autenticazione deve essere incluso nella richiesta, come illustrato nell'esempio di codice seguente:

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

Il token di accesso viene archiviato come impostazione dell'applicazione e viene recuperato dall'archiviazione specifica della piattaforma e incluso nella chiamata al metodo `GetOrderAsync` nella classe `OrderService`.

Analogamente, il token di accesso deve essere incluso quando si inviano dati a un'API protetta IdentityServer, come illustrato nell'esempio di codice seguente:

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

Il token di accesso viene recuperato dall'archiviazione specifica della piattaforma e incluso nella chiamata al metodo `UpdateBasketAsync` nella classe `BasketService`.

La classe `RequestProvider`, nell'app per dispositivi mobili eShopOnContainers, usa la classe `HttpClient` per eseguire richieste alle API RESTful esposte dall'applicazione di riferimento eShopOnContainers. Quando si effettuano richieste alle API Order e basket, che richiedono l'autorizzazione, è necessario includere nella richiesta un token di accesso valido. Questa operazione viene eseguita aggiungendo il token di accesso alle intestazioni dell'istanza di `HttpClient`, come illustrato nell'esempio di codice seguente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

La proprietà `DefaultRequestHeaders` della classe `HttpClient` espone le intestazioni inviate con ogni richiesta e il token di accesso viene aggiunto all'intestazione `Authorization` preceduta dalla stringa `Bearer`. Quando la richiesta viene inviata a un'API RESTful, il valore dell'intestazione `Authorization` viene estratto e convalidato per assicurarsi che venga inviato da un'autorità emittente attendibile e usato per determinare se l'utente dispone dell'autorizzazione per richiamare l'API che la riceve.

Per altre informazioni sul modo in cui l'app per dispositivi mobili eShopOnContainers esegue le richieste Web, vedere [accesso ai dati remoti](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).

## <a name="summary"></a>Riepilogo

Sono disponibili molti approcci per l'integrazione di autenticazione e autorizzazione in un'app Novell. Forms che comunica con un'applicazione Web MVC ASP.NET. L'app per dispositivi mobili eShopOnContainers esegue l'autenticazione e l'autorizzazione con un microservizio di identità in contenitori che usa IdentityServer 4. IdentityServer è un framework open source di OpenID Connect e OAuth 2,0 per ASP.NET Core che si integra con ASP.NET Core identità per eseguire bearer token l'autenticazione.

L'app per dispositivi mobili richiede token di sicurezza da IdentityServer per l'autenticazione di un utente o per l'accesso a una risorsa. Quando si accede a una risorsa, un token di accesso deve essere incluso nella richiesta alle API che richiedono l'autorizzazione. Il middleware di IdentityServer consente di convalidare i token di accesso in ingresso per assicurarsi che vengano inviati da un'autorità emittente attendibile e che siano validi per l'uso con l'API che li riceve.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (esempio)](https://github.com/dotnet-architecture/eShopOnContainers)
