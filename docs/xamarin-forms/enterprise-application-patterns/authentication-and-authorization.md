---
title: Autenticazione e autorizzazione
description: In questo capitolo viene illustrato come l'app per dispositivi mobili di eShopOnContainers esegue l'autenticazione e autorizzazione contro i microservizi in contenitori.
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: edab6b7edd5ca95cb5abe5fc2caccb5714efda56
ms.sourcegitcommit: a6ba6ed086bcde4f52fb05f83c59c68e8aa5e436
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540404"
---
# <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

L'autenticazione è il processo di ottenere le credenziali di identificazione, ad esempio nome e la password da un utente e la convalida di tali credenziali effettuato da un'autorità. Se le credenziali sono valide, l'entità che ha inviato le credenziali è considerata un'identità autenticata. Una volta un'identità è stata autenticata, un processo di autorizzazione determina se l'identità ha accesso a una determinata risorsa.

Esistono molti approcci per l'integrazione di autenticazione e autorizzazione in un'app xamarin. Forms che comunica con un'applicazione web ASP.NET MVC, incluso l'uso di ASP.NET Core Identity, il provider di autenticazione esterni, ad esempio Microsoft, Google, Middleware di Facebook o Twitter e l'autenticazione. L'app per dispositivi mobili di eShopOnContainers esegue l'autenticazione e autorizzazione con un microservizio di identità in contenitori che utilizza 4 IdentityServer. L'app per dispositivi mobili richiede i token di sicurezza da IdentityServer, per l'autenticazione di un utente o per accedere a una risorsa. Per IdentityServer per rilasciare token per conto di un utente, l'utente deve Accedi a IdentityServer. Tuttavia, IdentityServer non fornisce un database o l'interfaccia utente per l'autenticazione. Pertanto, nell'applicazione di riferimento eShopOnContainers, ASP.NET Core Identity è utilizzato per questo scopo.

## <a name="authentication"></a>Autenticazione

L'autenticazione è obbligatorio quando un'applicazione deve conoscere l'identità dell'utente corrente. Meccanismo principale di ASP.NET Core per identificare gli utenti è il sistema di appartenenze di ASP.NET Core Identity, che archivia le informazioni utente in un archivio dati configurato dallo sviluppatore. Questo archivio dati sarà in genere un archivio di Entity Framework, proposte archivi personalizzati o i pacchetti di terze parti possono essere usati per archiviare le informazioni sull'identità in archiviazione di Azure, Azure Cosmos DB o in altre posizioni.

Per scenari di autenticazione che usano un archivio dati utente locale e che vengono mantenute le informazioni sull'identità tra le richieste tramite dei cookie (come è tipico nelle applicazioni web ASP.NET MVC), ASP.NET Core Identity è una soluzione adatta. Tuttavia, i cookie non sono sempre un mezzo naturale della persistenza e la trasmissione dei dati. Ad esempio, un'applicazione web ASP.NET Core che espone endpoint RESTful a cui si accede da un'app per dispositivi mobili in genere necessario usare l'autenticazione di token di connessione, perché i cookie non possono essere usati in questo scenario. Tuttavia, i token di connessione possono facilmente essere recuperati e incluso nell'intestazione dell'autorizzazione di richieste web inviate dall'app per dispositivi mobili.

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>Emissione di token di connessione con IdentityServer 4

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4) è un framework open source di OpenID Connect e OAuth 2.0 per ASP.NET Core, che può essere usato per molti scenari di autenticazione e autorizzazione inclusa l'emissione di token di sicurezza per gli utenti locali di ASP.NET Core Identity.

> [!NOTE]
> OpenID Connect e OAuth 2.0 sono molto simili, ma la responsabilità diverse.

OpenID Connect è un livello di autenticazione sopra il protocollo OAuth 2.0. OAuth 2 è un protocollo che consente alle applicazioni di richiedere i token di accesso da un servizio token di sicurezza e usarli per comunicare con le API. Questa delega riduce la complessità delle applicazioni client sia le API perché autenticazione e autorizzazione possono essere centralizzati.

La combinazione di OpenID Connect e OAuth 2.0 combinare i due problemi fondamentali della sicurezza di autenticazione e l'accesso all'API e IdentityServer 4 è un'implementazione di questi protocolli.

Nelle applicazioni che usano la comunicazione client a microservizio diretta, ad esempio l'applicazione di riferimento eShopOnContainers, un microservizio di autenticazione dedicato che agisce come un Token di sicurezza del servizio (STS) può essere utilizzato per autenticare gli utenti, come illustrato nella figura 9-1. Per altre informazioni sulla comunicazione da client a microservizio diretta, vedere [comunicazioni tra Client e i Microservizi](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices).

![](authentication-and-authorization-images/authentication.png "Autenticazione da parte di un microservizio di autenticazione dedicato")

**Figura 9-1:** Autenticazione da parte di un microservizio di autenticazione dedicato

L'app per dispositivi mobili di eShopOnContainers comunica con il microservizio di identità, che usa 4 IdentityServer per eseguire l'autenticazione e controllo di accesso per le API. Pertanto, l'app per dispositivi mobili richiede i token dal IdentityServer, per l'autenticazione di un utente o per accedere a una risorsa:

-   Autenticazione degli utenti con IdentityServer avviene tramite l'app per dispositivi mobili che richiede un *identità* token, che rappresenta il risultato di un processo di autenticazione. Requisiti minimi, contiene un identificatore per l'utente e informazioni su come e quando l'utente autenticato. Può anche contenere dati di identità aggiuntive.
-   Accedere a una risorsa con IdentityServer avviene tramite l'app per dispositivi mobili che richiede un *accesso* token, che consente l'accesso a una risorsa API. I client di richiedono token di accesso e li inoltrano all'API. I token di accesso contengono informazioni su client e l'utente (se presente). API quindi utilizzano tali informazioni per autorizzare l'accesso ai dati.

> [!NOTE]
> Un client deve essere registrato con IdentityServer prima che possa richiedere i token.

### <a name="adding-identityserver-to-a-web-application"></a>Aggiunta di IdentityServer a un'applicazione Web

Affinché un'applicazione web ASP.NET Core usare IdentityServer 4, è necessario aggiungerlo alla soluzione di Visual Studio dell'applicazione web. Per altre informazioni, vedere [Panoramica](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) nella documentazione di IdentityServer.

Una volta IdentityServer è incluso nella soluzione di Visual Studio dell'applicazione web, è necessario aggiungerlo alla richiesta HTTP dell'applicazione web l'elaborazione della pipeline, in modo che possa servire le richieste agli endpoint OpenID Connect e OAuth 2.0. Questo avviene nella `Configure` metodo dell'applicazione web `Startup` classe, come illustrato nell'esempio di codice seguente:

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

Ordine è importante nell'elaborazione della pipeline delle richieste HTTP dell'applicazione web. Pertanto, IdentityServer deve essere aggiunto alla pipeline prima di framework dell'interfaccia utente che implementa la schermata di accesso.

### <a name="configuring-identityserver"></a>Configurazione IdentityServer

IdentityServer deve essere configurato nel `ConfigureServices` metodo dell'applicazione web `Startup` classe chiamando il `services.AddIdentityServer` metodo, come illustrato nell'esempio di codice seguente dall'applicazione di riferimento eShopOnContainers:

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

Dopo la chiamata di `services.AddIdentityServer` metodo API fluent aggiuntive vengono chiamate per configurare le opzioni seguenti:

-   Credenziali utilizzate per la firma.
-   Le risorse di identità e API che gli utenti potrebbero richiedere l'accesso a.
-   Client che si connetteranno per richiedere token.
-   ASP.NET Core Identity.

>💡 **Suggerimento**: Caricare in modo dinamico la configurazione IdentityServer 4. API dell'IdentityServer 4 consentono di configurare IdentityServer da un elenco in memoria degli oggetti di configurazione. Nell'applicazione di riferimento eShopOnContainers, queste raccolte in memoria sono impostati come hardcoded nell'applicazione. Tuttavia, negli scenari di produzione possono essere caricati in modo dinamico da un file di configurazione o da un database.

Per informazioni sulla configurazione IdentityServer per usare ASP.NET Core Identity, vedere [usando ASP.NET Core Identity](https://identityserver4.readthedocs.io/en/latest/quickstarts/8_aspnet_identity.html) nella documentazione di IdentityServer.

#### <a name="configuring-api-resources"></a>Configurazione delle risorse di API

Quando si configurano le risorse di API, il `AddInMemoryApiResources` metodo prevede un `IEnumerable<ApiResource>` raccolta. Nell'esempio di codice riportato di seguito viene illustrato il `GetApis` applicazione di riferimento di metodo che fornisce questa raccolta in eShopOnContainers:

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

Questo metodo specifica che devono proteggere IdentityServer gli ordini e basket. API. Pertanto, IdentityServer gestito l'accesso token sarà necessari quando si effettuano chiamate a queste API. Per altre informazioni sul `ApiResource` del tipo, vedere [della risorsa API](https://identityserver4.readthedocs.io/en/latest/reference/api_resource.html) nella documentazione di IdentityServer 4.

#### <a name="configuring-identity-resources"></a>Configurazione delle risorse di identità

Quando si configurano le risorse di identità, il `AddInMemoryIdentityResources` metodo prevede un `IEnumerable<IdentityResource>` raccolta. Le risorse di identità sono dati, ad esempio ID utente, nome o indirizzo di posta elettronica. Ogni risorsa di identità ha un nome univoco e tipi di attestazioni arbitrarie possono essere assegnati, che verranno quindi incluse nel token di identità per l'utente. Nell'esempio di codice riportato di seguito viene illustrato il `GetResources` applicazione di riferimento di metodo che fornisce questa raccolta in eShopOnContainers:

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

La specifica di OpenID Connect consente di specificare alcuni [risorse relative alle identità standard](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). Il requisito minimo è che non viene fornito supporto per la creazione di un ID univoco per gli utenti. Questo risultato viene ottenuto tramite l'esposizione di `IdentityResources.OpenId` risorsa identity.

> [!NOTE]
> Il `IdentityResources` classe supporta tutti gli ambiti definiti nella specifica di OpenID Connect (openid, messaggio di posta elettronica, profili, telefono e indirizzo).

IdentityServer supporta anche la definizione delle risorse di gestione delle identità personalizzata. Per altre informazioni, vedere [definizione delle risorse di identità personalizzato](http://docs.identityserver.io/en/latest/topics/resources.html#defining-custom-identity-resources) nella documentazione di IdentityServer. Per altre informazioni sul `IdentityResource` del tipo, vedere [risorsa Identity](https://identityserver4.readthedocs.io/en/latest/reference/identity_resource.html) nella documentazione di IdentityServer 4.

#### <a name="configuring-clients"></a>Configurazione dei client

I client sono applicazioni che possono richiedere i token da IdentityServer. In genere, le impostazioni seguenti devono essere definite per ogni client come minimo:

-   Un ID univoco client.
-   Le interazioni consentite con il servizio token (noto come il tipo di concessione).
-   Il percorso in cui vengono inviati i token di identità e degli accessi (noto come un URI di reindirizzamento).
-   Un elenco di risorse che il client è autorizzato ad accedere a (noto come ambiti).

Quando si configurano i client, il `AddInMemoryClients` metodo prevede un `IEnumerable<Client>` raccolta. Esempio di codice seguente viene illustrata la configurazione per l'app per dispositivi mobili di eShopOnContainers nel `GetClients` applicazione di riferimento di metodo che fornisce questa raccolta in eShopOnContainers:

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

Questa configurazione specifica dei dati per le proprietà seguenti:

-   `ClientId`: ID univoco per il client.
-   `ClientName`: Il nome di visualizzazione client, che viene usato per la registrazione e la schermata di consenso.
-   `AllowedGrantTypes`: Specifica come un client vuole interagire con IdentityServer. Per altre informazioni, vedere [configurazione flusso di autenticazione](#configuring_the_authentication_flow).
-   `ClientSecrets`: Specifica le credenziali del segreto client che vengono utilizzate quando la richiesta dei token dall'endpoint del token.
-   `RedirectUris`: Specifica l'URI consentiti per il quale restituire i codici di autorizzazione o token.
-   `RequireConsent`: Specifica se è necessaria una schermata di consenso.
-   `RequirePkce`: Specifica se i client che usano un codice di autorizzazione devono inviare una chiave di prova.
-   `PostLogoutRedirectUris`: Specifica l'URI consentiti per reindirizzare a dopo la disconnessione.
-   `AllowedCorsOrigins`: Specifica l'origine del client in modo che IdentityServer può permettere le chiamate multiorigine dall'origine.
-   `AllowedScopes`: Specifica le risorse a che il client ha accesso. Per impostazione predefinita, un client non ha accesso a tutte le risorse.
-   `AllowOfflineAccess`: Specifica se il client può richiedere i token di aggiornamento.

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>Configurazione del flusso di autenticazione

Il flusso di autenticazione tra un client e IdentityServer può essere configurato specificando i tipi di concessione nel `Client.AllowedGrantTypes` proprietà. Le specifiche di OpenID Connect e OAuth 2.0 definiscono un numero di flussi di autenticazione, tra cui:

-   Implicita. Questo flusso è ottimizzato per le applicazioni basate su browser e deve essere utilizzato per utente solo l'autenticazione, o per le richieste di token di accesso e l'autenticazione. Tutti i token vengono trasmessi tramite browser e pertanto avanzati funzionalità quali i token di aggiornamento non sono consentiti.
-   Codice di autorizzazione. Questo flusso offre la possibilità di recuperare i token in un canale back, anziché il canale di front-browser, supportando anche l'autenticazione del client.
-   Ibrido. Questo flusso è una combinazione di implicito e i tipi di concessione del codice di autorizzazione. Il token di identità viene trasmesso tramite il canale di browser e contiene la risposta del protocollo con segno e altri elementi, ad esempio il codice di autorizzazione. Al termine della convalida della risposta, canale deve essere usato per recuperare l'accesso e token di aggiornamento.

> [!TIP]
> Usare il flusso di autenticazione ibrido. Il flusso di autenticazione ibrida consente di ridurre un numero di attacchi che si applicano al canale del browser e viene illustrato il flusso consigliato per le applicazioni native che vogliono recuperare i token di accesso (e possibilmente i token di aggiornamento).

Per altre informazioni sui flussi di autenticazione, vedere [tipi di concessione](https://identityserver4.readthedocs.io/en/latest/topics/grant_types.html) nella documentazione di IdentityServer 4.

### <a name="performing-authentication"></a>Esegue l'autenticazione

Per IdentityServer per rilasciare token per conto di un utente, l'utente deve Accedi a IdentityServer. Tuttavia, IdentityServer non fornisce un database o l'interfaccia utente per l'autenticazione. Pertanto, nell'applicazione di riferimento eShopOnContainers, ASP.NET Core Identity è utilizzato per questo scopo.

L'app per dispositivi mobili di eShopOnContainers si autentica con IdentityServer con il flusso di autenticazione ibrido, che è illustrato nella figura 9-2.

![](authentication-and-authorization-images/sign-in.png "Panoramica di alto livello del processo di accesso")

**Figura 9-2:** Panoramica di alto livello del processo di accesso

Viene effettuata una richiesta di accesso a `<base endpoint>:5105/connect/authorize`. Dopo l'autenticazione, IdentityServer restituisce una risposta di autenticazione che contiene un codice di autorizzazione e un token di identità. Il codice di autorizzazione viene quindi inviato al `<base endpoint>:5105/connect/token`, che risponde con l'accesso, l'identità e i token di aggiornamento.

Il eShopOnContainers app per dispositivi mobili segni-out di IdentityServer inviando una richiesta a `<base endpoint>:5105/connect/endsession`, con parametri aggiuntivi. Dopo la disconnessione si verifica, IdentityServer risponde inviando un URI di reindirizzamento disconnessione post tornare all'app per dispositivi mobili. Questo processo è illustrato nella figura 9-3.

![](authentication-and-authorization-images/sign-out.png "Panoramica di alto livello del processo di disconnessione")

**Figura 9-3:** Panoramica di alto livello del processo di disconnessione

Nell'app eShopOnContainers, la comunicazione con IdentityServer avviene mediante il `IdentityService` classe che implementa il `IIdentityService` interfaccia. Questa interfaccia specifica che la classe di implementazione deve fornire `CreateAuthorizationRequest`, `CreateLogoutRequest`, e `GetTokenAsync` metodi.

#### <a name="signing-in"></a>Signing-in

Quando l'utente tocca il **account di accesso** pulsante il `LoginView`, il `SignInCommand` nel `LoginViewModel` classe viene eseguita, che a sua volta esegue il `SignInAsync` (metodo). L'esempio di codice seguente illustra il metodo:

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

Questo metodo richiama il `CreateAuthorizationRequest` metodo nella `IdentityService` (classe), che è illustrata nell'esempio di codice seguente:

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
    dic.Add("redirect_uri", GlobalSetting.Instance.IdentityCallback);
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

Questo metodo crea l'URI dell'IdentityServer [endpoint di autorizzazione](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html), con i parametri obbligatori. L'endpoint di autorizzazione si trova `/connect/authorize` sulla porta 5105 dell'endpoint di base esposta come un'impostazione utente. Per altre informazioni sulle impostazioni utente, vedere [gestione della configurazione](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> La superficie di attacco dell'app per dispositivi mobili eShopOnContainers viene ridotto implementando la chiave di prova per Code Exchange (PKCE) estensione a OAuth. PKCE impedisce che il codice di autorizzazione viene usato se questo viene intercettato. A questo scopo, il client genera un verificatore di segreto, un hash di cui viene passato nella richiesta di autorizzazione, e che viene visualizzato senza hash quando riscattando il codice di autorizzazione. Per altre informazioni sulle PKCE, vedere [chiave di prova per Code Exchange dal client di OAuth pubblici](https://tools.ietf.org/html/rfc7636) sul sito web di Internet Engineering Task Force.

L'URI restituito viene archiviato nel `LoginUrl` proprietà del `LoginViewModel` classe. Quando la `IsLogin` proprietà diventa `true`, il [ `WebView` ](xref:Xamarin.Forms.WebView) nel `LoginView` diventa visibile. Il `WebView` Associa dati relativa [ `Source` ](xref:Xamarin.Forms.WebView.Source) proprietà per il `LoginUrl` proprietà del `LoginViewModel` classe e crea quindi una richiesta di accesso a IdentityServer quando il `LoginUrl` è impostata su Endpoint di autorizzazione dell'IdentityServer. Quando IdentityServer riceve la richiesta e l'utente non è autenticato, il `WebView` verrà reindirizzato alla pagina di accesso configurati, come illustrata nella figura 9-4.

![](authentication-and-authorization-images/login.png "Pagina di accesso visualizzato per la visualizzazione Web")

**Figura 9-4:** Pagina di accesso visualizzato per la visualizzazione Web

Al termine dell'account di accesso, il [ `WebView` ](xref:Xamarin.Forms.WebView) verrà reindirizzato a un URI restituito. Ciò `WebView` navigazione causerà il `NavigateAsync` metodo nel `LoginViewModel` classe deve essere eseguito, mostrata nell'esempio di codice seguente:

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

Questo metodo consente di analizzare la risposta di autenticazione che è contenuta l'URI restituito e un codice di autorizzazione valido sono presentano, effettua una richiesta dell'IdentityServer [endpoint di token](https://identityserver4.readthedocs.io/en/latest/endpoints/token.html), passando il codice di autorizzazione, il Verifier secret PKCE e altri parametri obbligatori. L'endpoint di token è in `/connect/token` sulla porta 5105 dell'endpoint di base esposta come un'impostazione utente. Per altre informazioni sulle impostazioni utente, vedere [gestione della configurazione](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

>💡 **Suggerimento**: Convalidare gli URI restituito. Anche se l'app per dispositivi mobili di eShopOnContainers non convalida l'URI restituito, la procedura consigliata è per convalidare che l'URI restituito fa riferimento a una posizione nota, per impedire attacchi di reindirizzamento aperto.

Se l'endpoint di token riceve un codice di autorizzazione valido e verifier secret PKCE, risponde con un token di accesso, token di identità, token di aggiornamento. Il token di accesso (che consente l'accesso alle risorse dell'API) e un token di identità vengono quindi archiviati come impostazioni dell'applicazione e viene eseguita la navigazione. Di conseguenza, l'effetto complessivo nell'app per dispositivi mobili di eShopOnContainers è questo: condizione che gli utenti siano in grado di eseguire l'autenticazione con IdentityServer, passaggio al `MainView` pagina, ovvero un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) che consente di visualizzare il `CatalogView` come la scheda selezionata.

Per informazioni sulla navigazione tra le pagine, vedere [navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md). Per informazioni su come [ `WebView` ](xref:Xamarin.Forms.WebView) spostamento fa sì che un metodo del modello di visualizzazione da eseguire, vedere [richiamo di navigazione usando i comportamenti](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Per informazioni sulle impostazioni dell'applicazione, vedere [gestione della configurazione](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> Consente inoltre una simulazione Accedi quando l'app è configurata per utilizzare servizi fittizi in eShopOnContainers il `SettingsView`. In questa modalità l'app comunica con IdentityServer, invece che consente all'utente di accedere usando le credenziali.

#### <a name="signing-out"></a>La firma in uscita

Quando l'utente tocca il **DISCONNETTI** pulsante nel `ProfileView`, il `LogoutCommand` nel `ProfileViewModel` classe viene eseguita, che a sua volta esegue il `LogoutAsync` (metodo). Questo metodo esegue la navigazione tra le pagine per il `LoginView` pagina, passando un `LogoutParameter` istanza impostata su `true` come parametro. Per altre informazioni sul passaggio di parametri durante la navigazione tra le pagine, vedere [passando i parametri durante la navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Quando una visualizzazione viene creata e aperto, il `InitializeAsync` esecuzione del metodo del modello di visualizzazione associata della visualizzazione, che viene quindi eseguito il `Logout` metodo il `LoginViewModel` (classe), che è illustrata nell'esempio di codice seguente:

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

Questo metodo richiama il `CreateLogoutRequest` metodo nella `IdentityService` (classe), passando il token di identità recuperate dalle impostazioni dell'applicazione come parametro. Per altre informazioni sulle impostazioni dell'applicazione, vedere [gestione della configurazione](~/xamarin-forms/enterprise-application-patterns/configuration-management.md). L'esempio di codice seguente illustra il metodo `CreateLogoutRequest`:

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

Questo metodo crea l'URI del IdentityServer [terminare endpoint sessione](https://identityserver4.readthedocs.io/en/latest/endpoints/endsession.html#refendsession), con i parametri obbligatori. L'endpoint di sessione finali posizionato `/connect/endsession` sulla porta 5105 dell'endpoint di base esposta come un'impostazione utente. Per altre informazioni sulle impostazioni utente, vedere [gestione della configurazione](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

L'URI restituito viene archiviato nel `LoginUrl` proprietà del `LoginViewModel` classe. Mentre il `IsLogin` proprietà viene `true`, il [ `WebView` ](xref:Xamarin.Forms.WebView) nel `LoginView` è visibile. Il `WebView` Associa dati relativa [ `Source` ](xref:Xamarin.Forms.WebView.Source) proprietà per il `LoginUrl` proprietà del `LoginViewModel` classe e crea quindi una richiesta di disconnessione a IdentityServer quando il `LoginUrl` è impostata su Endpoint di sessione finali del IdentityServer. Quando si IdentityServer riceve questa richiesta, a condizione che l'utente è connesso, Sign-Out. L'autenticazione viene registrata con un cookie gestito dal middleware di autenticazione dei cookie da ASP.NET Core. Pertanto, durante la disconnessione da IdentityServer rimuove il cookie di autenticazione e invia un reindirizzamento post-disconnessione URI al client.

Nell'app per dispositivi mobili, il [ `WebView` ](xref:Xamarin.Forms.WebView) verrà reindirizzato all'URI di reindirizzamento di disconnessione post. Ciò `WebView` navigazione causerà il `NavigateAsync` metodo nel `LoginViewModel` classe deve essere eseguito, mostrata nell'esempio di codice seguente:

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

Questo metodo cancella il token di identità e il token di accesso dalle impostazioni dell'applicazione e imposta il `IsLogin` proprietà `false`, determinando in tal modo il [ `WebView` ](xref:Xamarin.Forms.WebView) sul `LoginView` pagina diventi invisibile . Infine, il `LoginUrl` viene impostata per l'URI di IdentityServer [endpoint di autorizzazione](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html), con i parametri obbligatori, in preparazione per la volta successiva che l'utente avvia un accesso.

Per informazioni sulla navigazione tra le pagine, vedere [navigazione](~/xamarin-forms/enterprise-application-patterns/navigation.md). Per informazioni su come [ `WebView` ](xref:Xamarin.Forms.WebView) spostamento fa sì che un metodo del modello di visualizzazione da eseguire, vedere [richiamo di navigazione usando i comportamenti](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Per informazioni sulle impostazioni dell'applicazione, vedere [gestione della configurazione](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> EShopOnContainers consente inoltre una simulazione disconnessione quando l'app è configurata per usare i servizi fittizi nel SettingsView. In questa modalità l'app non consente la comunicazione con IdentityServer e invece cancella eventuali token stored dalle impostazioni dell'applicazione.

<a name="authorization" />

## <a name="authorization"></a>Autorizzazione

Dopo l'autenticazione, autorizzare l'accesso, è spesso necessario API web di ASP.NET Core che consente a un servizio rendere le API disponibili per alcuni utenti autenticati, ma non per tutti.

Limitare l'accesso a una route di ASP.NET Core MVC può essere ottenuta applicando un attributo Authorize a un controller o azione, che limita l'accesso al controller o azione per utenti autenticati, come illustrato nell'esempio di codice seguente:

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

Se un utente non autorizzato tenta di accedere a un controller o azione che è contrassegnato con il `Authorize` attributo, il framework MVC restituisce un codice di stato HTTP 401 (non autorizzato).

> [!NOTE]
> I parametri possono essere specificati nel `Authorize` attributo per limitare un'API a utenti specifici. Per ulteriori informazioni, vedere [autorizzazione](/aspnet/core/security/authorization/introduction/).

IdentityServer può essere integrato nel flusso di lavoro autorizzazione in modo che i token di accesso fornisce l'autorizzazione di controllo. Questo approccio è illustrato nella figura 9-5.

![](authentication-and-authorization-images/authorization.png "Autorizzazione da token di accesso")

**Figura 9-5:** Autorizzazione da token di accesso

L'app per dispositivi mobili di eShopOnContainers comunica con il microservizio di identità e richiede un token di accesso come parte del processo di autenticazione. Il token di accesso viene quindi inoltrato per le API esposte dai microservizi del carrello e di ordinamento come parte delle richieste di accesso. I token di accesso contengono informazioni su client e l'utente. API quindi utilizzano tali informazioni per autorizzare l'accesso ai dati. Per informazioni su come configurare IdentityServer per proteggere le API, vedere [configurazione di risorse dell'API](#configuring-api-resources).

### <a name="configuring-identityserver-to-perform-authorization"></a>Configurazione IdentityServer per eseguire l'autorizzazione

Per eseguire l'autorizzazione con IdentityServer, il middleware di autorizzazione deve essere aggiunto alla pipeline di richieste HTTP dell'applicazione web. Il middleware viene aggiunto nel `ConfigureAuth` metodo dell'applicazione web `Startup` (classe), che viene richiamato dal `Configure` (metodo) e viene illustrato nell'esempio di codice seguente dall'applicazione di riferimento eShopOnContainers:

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

Questo metodo assicura che l'API è accessibile solo con un token di accesso valido. Il middleware convalida il token in ingresso per verificare che venga inviata da un'autorità emittente attendibile e verifica che il token sia valido per l'uso con l'API che lo riceve. Pertanto, passare al controller di ordinamento o di basket restituirà un 401 (non autorizzato) codice di stato HTTP, che indica che è necessario un token di accesso.

> [!NOTE]
> Middleware di autorizzazione del IdentityServer prima è necessario aggiungerlo alla pipeline di richieste HTTP dell'applicazione web MVC con l'aggiunta `app.UseMvc()` o `app.UseMvcWithDefaultRoute()`.

### <a name="making-access-requests-to-apis"></a>Creazione di richieste di accesso alle API

Quando si effettuano richieste per i microservizi del carrello e ordinamento, l'accesso, token ottenuto da IdentityServer durante il processo di autenticazione deve essere incluso nella richiesta, come illustrato nell'esempio di codice seguente:

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

Il token di accesso viene archiviato come un'impostazione dell'applicazione e viene recuperato da un archivio specifico della piattaforma e inclusi nella chiamata ai `GetOrderAsync` nel metodo il `OrderService` classe.

Analogamente, il token di accesso deve essere incluso durante l'invio di dati a un IdentityServer protetto l'API, come illustrato nell'esempio di codice seguente:

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

Il token di accesso viene recuperato da un archivio specifico della piattaforma e inclusi nella chiamata ai `UpdateBasketAsync` nel metodo il `BasketService` classe.

Il `RequestProvider` (classe), nell'app eShopOnContainers, utilizza il `HttpClient` classi per effettuare richieste per le API REST esposte dall'applicazione di riferimento eShopOnContainers. Quando rendere richiede l'ordinamento e basket API, che richiedono l'autorizzazione, un token di accesso valido deve essere incluso con la richiesta. A questo scopo, aggiungere il token di accesso alle intestazioni del `HttpClient` istanze, come illustrato nell'esempio di codice seguente:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

Il `DefaultRequestHeaders` proprietà del `HttpClient` classe espone le intestazioni che vengono inviate con ogni richiesta e il token di accesso viene aggiunto al `Authorization` intestazione con la stringa di prefisso `Bearer`. Quando la richiesta viene inviata a un'API RESTful, il valore della `Authorization` intestazione viene estratto e convalidata per assicurarsi che ha inviato da un'autorità emittente attendibile e usata per determinare se l'utente dispone dell'autorizzazione per richiamare l'API che lo riceve.

Per altre informazioni sul modo in cui l'app per dispositivi mobili di eShopOnContainers effettua le richieste web, vedere [l'accesso ai dati remoti](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).

## <a name="summary"></a>Riepilogo

Esistono molti approcci per l'integrazione di autenticazione e autorizzazione in un'app xamarin. Forms che comunica con un'applicazione web ASP.NET MVC. L'app per dispositivi mobili di eShopOnContainers esegue l'autenticazione e autorizzazione con un microservizio di identità in contenitori che utilizza 4 IdentityServer. IdentityServer è un framework open source di OpenID Connect e OAuth 2.0 per ASP.NET Core che si integra con ASP.NET Core Identity per eseguire l'autenticazione del bearer token.

L'app per dispositivi mobili richiede i token di sicurezza da IdentityServer, per l'autenticazione di un utente o per accedere a una risorsa. Quando si accede a una risorsa, un token di accesso deve essere incluso nella richiesta per le API che richiedono l'autorizzazione. Middleware di IdentityServer convalida i token di accesso in ingresso per assicurare che vengono inviati da un'autorità emittente attendibile e che sono validi per l'uso con l'API che li riceve.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
