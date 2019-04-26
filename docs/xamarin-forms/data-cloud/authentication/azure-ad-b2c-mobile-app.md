---
title: L'integrazione di Azure Active Directory B2C con App per dispositivi mobili di Azure
description: Azure Active B2C di Directory è una soluzione di gestione identità cloud per applicazioni rivolte agli utenti web e mobili. Questo articolo illustra come usare Azure Active Directory B2C per fornire l'autenticazione e autorizzazione a un'istanza di App per dispositivi mobili di Azure con xamarin. Forms.
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 135977329e2a190dd4c611937f6b8a664f135f5c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61332193"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>L'integrazione di Azure Active Directory B2C con App per dispositivi mobili di Azure

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)

_Azure Active B2C di Directory è una soluzione di gestione identità cloud per applicazioni rivolte agli utenti web e mobili. Questo articolo illustra come usare Azure Active Directory B2C per fornire l'autenticazione e autorizzazione a un'istanza di App per dispositivi mobili di Azure con xamarin. Forms._

![](~/media/shared/preview.png "Questa API è ancora in versione definitiva")

> [!NOTE]
> Il [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client) è ancora in anteprima, ma è adatto per l'uso in un ambiente di produzione. Tuttavia, si può determinare interruzioni modifiche per l'API di formato della cache interna e altri meccanismi per la libreria, che possono influire sull'applicazione.

## <a name="overview"></a>Panoramica

Le App per dispositivi mobili di Azure consentono di sviluppare applicazioni con back-end scalabile ospitati nel servizio App di Azure con supporto per l'autenticazione per dispositivi mobili, la sincronizzazione offline e notifiche push. Per altre informazioni sulle App per dispositivi mobili di Azure, vedere [utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md), e [agli utenti l'autenticazione con Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md).

Azure Active B2C di Directory è un servizio di gestione di identità per le applicazioni rivolte ai consumatori, che consente agli utenti di accedere all'applicazione da:

- Usando gli account di social networking esistenti (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Creazione di nuove credenziali (indirizzo di posta elettronica e password, o nome utente e password). Queste credenziali sono dette *locale* account.

Per altre informazioni su Azure Active Directory B2C, vedere [autenticare gli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Azure Active B2C di Directory può essere utilizzato per gestire il flusso di lavoro di autenticazione per un'App per dispositivi mobili di Azure. Con questo approccio, l'esperienza di gestione di identità sia definita completamente nel cloud e può essere modificato senza modificare il codice dell'applicazione per dispositivi mobili.

Sono disponibili due flussi di autenticazione che possono essere adottati quando l'integrazione di un tenant di Azure Active Directory B2C con un'istanza di App per dispositivi mobili di Azure:

- [Client gestito](#client_managed) : in questo approccio l'avviato dall'applicazione per dispositivi mobili xamarin. Forms il processo di autenticazione con il tenant di Azure Active Directory B2C e passa il token di autenticazione ricevute all'istanza di App per dispositivi mobili di Azure.
- [Gestita dal server](#server_managed) : l'App per dispositivi mobili di Azure con questo approccio istanza utilizza il tenant di Azure Active Directory B2C per avviare il processo di autenticazione tramite un flusso di lavoro basato sul web.

In entrambi i casi, l'esperienza di autenticazione viene fornito dal tenant Azure Active Directory B2C. Nell'applicazione di esempio, in questo modo la schermata di accesso illustrata negli screenshot seguenti:

![](azure-ad-b2c-mobile-app-images/screenshots.png "Pagina di accesso")

Accesso con provider di identità basati su social network o con un account locale, sono consentiti. Microsoft, Google e Facebook vengono usati come provider di identità di social networking in questo esempio, altri provider di identità è anche utilizzabile.

## <a name="setup"></a>Configurazione

Indipendentemente dal flusso di lavoro di autenticazione usato, il processo iniziale per l'integrazione di un tenant di Azure Active Directory B2C in un'istanza di App per dispositivi mobili di Azure è come segue:

1. Creare un'istanza di App per dispositivi mobili di Azure. Per altre informazioni, vedere [utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Abilitare l'autenticazione nell'istanza di App per dispositivi mobili di Azure e l'applicazione xamarin. Forms. Per altre informazioni, vedere [autenticare gli utenti con App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/authentication/azure.md).
1. Creare un tenant di Azure Active Directory B2C. Per altre informazioni, vedere [autenticare gli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Si noti che Microsoft Authentication Library (MSAL) è obbligatorio quando si usa un flusso di lavoro di autenticazione gestita dal client. MSAL Usa il web browser del dispositivo per eseguire l'autenticazione. Ciò migliora l'usabilità di un'applicazione, come gli utenti devono solo eseguire l'accesso una volta per ogni dispositivo, migliorando i tassi di conversione di accesso e autorizzazione flussi nell'applicazione. Il browser del dispositivo offre inoltre una maggiore sicurezza. Dopo che l'utente ha completato il processo di autenticazione, il controllo verrà restituito all'applicazione dalla scheda del browser web. Questo risultato viene ottenuto tramite la registrazione di uno schema URL personalizzato per l'URL di reindirizzamento restituito dal processo di autenticazione e quindi il rilevamento e la gestione degli URL personalizzato dopo averla inviata. Per altre informazioni sull'uso di MSAL per comunicare con un tenant di Azure Active Directory B2C, vedere [autenticare gli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

<a name="client_managed" />

## <a name="client-managed-authentication"></a>Autenticazione gestita dal client

L'autenticazione gestita dal client, un'applicazione per dispositivi mobili xamarin. Forms contatta un tenant di Azure Active Directory B2C per avviare un flusso di autenticazione. Dopo l'esito positivo sign-on di B2C di Azure Active Directory tenant restituisce un token di identità che viene quindi fornito durante l'accesso all'istanza di App per dispositivi mobili di Azure. In questo modo l'applicazione xamarin. Forms eseguire azioni nell'istanza di App per dispositivi mobili di Azure che richiede le autorizzazioni utente autenticato.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Configurazione del Tenant Azure Active Directory B2C

Per un flusso di lavoro di autenticazione gestita dal client, il tenant di Azure Active Directory B2C deve essere configurato come segue:

- Include un client nativo.
- Impostare l'URI di reindirizzamento personalizzato a uno schema URL che identifica in modo univoco l'applicazione per dispositivi mobili, seguito da `://auth/`. Per altre informazioni sulla scelta di uno schema URL personalizzato, vedere [scelta di un URI di reindirizzamento app native](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri).

Lo screenshot seguente illustra questa configurazione:

[![](azure-ad-b2c-mobile-app-images/client-flow-config-sml.png "Configurazione di Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/client-flow-config.png#lightbox "configurazione di Azure Active Directory B2C")

I criteri usati in Azure Active Directory B2C tenant deve inoltre essere configurato in modo che l'URL di risposta viene impostato sullo stesso schema URL personalizzato, seguita da `://auth/`. Lo screenshot seguente illustra questa configurazione:

![](azure-ad-b2c-mobile-app-images/client-flow-policies.png "Criteri di Azure Active Directory B2C")

### <a name="azure-mobile-app-configuration"></a>Configurazione delle App per dispositivi mobili di Azure

Per un flusso di lavoro di autenticazione gestita dal client, l'istanza di App per dispositivi mobili di Azure deve essere configurato come segue:

- Autenticazione del servizio app deve essere attivato.
- L'azione da intraprendere quando una richiesta non è autenticata deve essere impostato su **Accedi con Azure Active Directory**.

Lo screenshot seguente illustra questa configurazione:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-config.png "Configurazione di App per dispositivi mobili di Azure")

L'istanza di App per dispositivi mobili di Azure deve essere configurata anche per comunicare con il tenant di Azure Active Directory B2C. Questa operazione può essere eseguita abilitando **avanzate** modalità per il provider di autenticazione di Azure Active Directory, con la **ID Client** in corso la **ID applicazione** di Azure Tenant di Active Directory B2C e il **Url autorità di certificazione** in corso l'endpoint dei metadati per i criteri di Azure Active Directory B2C. Lo screenshot seguente illustra questa configurazione:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-advanced-config.png "App per dispositivi mobili di Azure configurazione avanzata")

### <a name="signing-in"></a>Accedi

Esempio di codice seguente mostra come avviare un flusso di lavoro di autenticazione gestita dal client:

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);

    ...
    var payload = new JObject();
    payload["access_token"] = authenticationResult.IdToken;

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    ...
}
```

Microsoft Authentication Library (MSAL) viene usato per avviare un flusso di lavoro di autenticazione con il tenant di Azure Active Directory B2C. Il `AcquireTokenAsync` metodo avvia web browser del dispositivo e visualizza le opzioni di autenticazione definite nei criteri di Azure Active Directory B2C specificato dai criteri di cui viene fatto riferimento tramite il `Constants.Authority` costante. Tali criteri definiscono le esperienze consumer passerà attraverso durante l'iscrizione e l'accesso e le attestazioni che l'applicazione riceverà corretta iscrizione o accesso.

Il risultato del `AcquireTokenAsync` chiamata al metodo è un `AuthenticationResult` istanza. Se l'autenticazione ha esito positivo, il `AuthenticationResult` istanza conterrà un token di identità, che verrà memorizzato localmente. Se l'autenticazione ha esito negativo, il `AuthenticationResult` istanza conterrà i dati che indicano il motivo per cui l'autenticazione non è riuscita. Per informazioni su come usare MSAL per comunicare con un tenant di Azure Active Directory B2C, vedere [autenticare gli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Quando la `MobileServiceClient.LoginAsync` metodo viene richiamato, l'istanza di App per dispositivi mobili di Azure riceve il token dell'identità sottoposta a wrapping in un `JObject`. La presenza di un mezzo di token valido che l'istanza di App per dispositivi mobili di Azure non è necessario per avviare il proprio flusso di autenticazione OAuth 2.0. Al contrario, il `MobileServiceClient.LoginAsync` metodo restituisce un `MobileServiceUser` istanza in cui verrà archiviato nel `MobileServiceClient.CurrentUser` proprietà. Questa proprietà fornisce `UserId` e `MobileServiceAuthenticationToken` proprietà. Questi rappresentano l'utente autenticato e un token di autenticazione per l'utente, che può essere usato fino alla scadenza. Il token di autenticazione verrà incluso in tutte le richieste effettuate all'istanza di App per dispositivi mobili di Azure, consentendo all'applicazione xamarin. Forms eseguire azioni nell'istanza di App per dispositivi mobili di Azure che richiedono le autorizzazioni utente autenticato.

### <a name="signing-out"></a>Disconnessione

Esempio di codice seguente mostra come viene richiamato il processo di disconnessione gestita dal client:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();

    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

Il `MobileServiceClient.LogoutAsync` metodo deprovisioning autentica l'utente con l'istanza di App per dispositivi mobili di Azure e quindi tutti i token di autenticazione vengono cancellati dalla cache locale creata da MSAL.

<a name="server_managed" />

## <a name="server-managed-authentication"></a>Autenticazione gestita dal server

L'autenticazione gestita dal server, un'applicazione xamarin. Forms contatta un'istanza di App per dispositivi mobili di Azure, che usa il tenant di Azure Active Directory B2C per gestire il flusso di autenticazione OAuth 2.0 visualizzando una pagina di accesso base a quanto definito nei criteri di B2C. In seguito ha esito positivo sign-on, l'istanza di App per dispositivi mobili di Azure restituisce un token che consente all'applicazione xamarin. Forms eseguire azioni nell'istanza di App per dispositivi mobili di Azure che richiedono le autorizzazioni utente autenticato.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Configurazione del Tenant Azure Active Directory B2C

Per un flusso di lavoro di autenticazione gestita dal server, il tenant di Azure Active Directory B2C deve essere configurato come segue:

- Includono un'app web/web API e consentire il flusso implicito.
- Impostare l'URL di risposta per l'indirizzo della App per dispositivi mobili di Azure, seguita da `/.auth/login/aad/callback`.

Lo screenshot seguente illustra questa configurazione:

[![](azure-ad-b2c-mobile-app-images/server-flow-config-sml.png "Configurazione di Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "configurazione di Azure Active Directory B2C")

I criteri usati in Azure Active Directory B2C tenant deve inoltre essere configurato in modo che l'URL di risposta viene impostato sull'indirizzo della App per dispositivi mobili di Azure, seguita da `/.auth/login/aad/callback`. Lo screenshot seguente illustra questa configurazione:

![](azure-ad-b2c-mobile-app-images/server-flow-policies.png "Criteri di Azure Active Directory B2C")

### <a name="azure-mobile-apps-instance-configuration"></a>Configurazione dell'istanza di App per dispositivi mobili di Azure

Per un flusso di lavoro di autenticazione gestita dal server, l'istanza di App per dispositivi mobili di Azure deve essere configurato come segue:

- Autenticazione del servizio app deve essere attivato.
- L'azione da intraprendere quando una richiesta non è autenticata deve essere impostato su **Accedi con Azure Active Directory**.

Lo screenshot seguente illustra questa configurazione:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-config.png "Configurazione di App per dispositivi mobili di Azure")

L'istanza di App per dispositivi mobili di Azure deve essere configurata anche per comunicare con il tenant di Azure Active Directory B2C. Questa operazione può essere eseguita abilitando **avanzate** modalità per il provider di autenticazione di Azure Active Directory, con la **ID Client** in corso la **ID applicazione** di Azure Tenant di Active Directory B2C e il **Url autorità di certificazione** in corso l'endpoint dei metadati per i criteri di Azure Active Directory B2C. Lo screenshot seguente illustra questa configurazione:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-advanced-config.png "App per dispositivi mobili di Azure configurazione avanzata")

### <a name="signing-in"></a>Accedi

Esempio di codice seguente mostra come avviare un flusso di lavoro di autenticazione gestita dal server:

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...
    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        UIApplication.SharedApplication.KeyWindow.RootViewController,
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);
    ...
}
```

Quando il `MobileServiceClient.LoginAsync` metodo viene richiamato, l'istanza di App per dispositivi mobili di Azure esegue i criteri di Azure Active Directory B2C collegato, che avvia il flusso di autenticazione OAuth 2.0. Si noti che ogni `AuthenticateAsync` metodo è specifica della piattaforma. Tuttavia, ogni `AuthenticateAsync` metodo viene utilizzato il `MobileServiceClient.LoginAsync` (metodo) e specifica che un tenant di Azure Active Directory verrà utilizzato nel processo di autenticazione. Per altre informazioni, vedere [la registrazione degli utenti](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in).

Il `MobileServiceClient.LoginAsync` metodo restituisce un `MobileServiceUser` istanza in cui verrà archiviato nel `MobileServiceClient.CurrentUser` proprietà. Questa proprietà fornisce `UserId` e `MobileServiceAuthenticationToken` proprietà. Questi rappresentano l'utente autenticato e un token di autenticazione per l'utente, che può essere usato fino alla scadenza. Il token di autenticazione verrà incluso in tutte le richieste effettuate all'istanza di App per dispositivi mobili di Azure, consentendo all'applicazione xamarin. Forms eseguire azioni nell'istanza di App per dispositivi mobili di Azure che richiedono le autorizzazioni utente autenticato.

### <a name="signing-out"></a>Disconnessione

Esempio di codice seguente mostra come viene richiamato il processo di disconnessione gestita dal server:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
    ...
}
```

Il `MobileServiceClient.LogoutAsync` metodo deprovisioning autentica l'utente con l'istanza di App per dispositivi mobili di Azure. Per altre informazioni, vedere [registrazione Out utenti](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out).

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come usare Azure Active Directory B2C per fornire l'autenticazione e autorizzazione a un'istanza di App per dispositivi mobili di Azure con xamarin. Forms. Azure Active B2C di Directory è una soluzione di gestione identità cloud per applicazioni rivolte agli utenti web e mobili.


## <a name="related-links"></a>Collegamenti correlati

- [TodoAzureAuth ServerFlow (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [TodoAzureAuth ClientFlow (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [Utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticazione degli utenti con App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Autenticazione degli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client)
