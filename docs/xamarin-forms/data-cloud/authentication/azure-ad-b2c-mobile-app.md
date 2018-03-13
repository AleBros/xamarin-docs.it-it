---
title: L'integrazione di Azure Active Directory B2C con le App per dispositivi mobili di Azure
description: "Azure Active B2C di Directory è una soluzione di gestione di identità cloud per applicazioni mobili e web per consumatori. In questo articolo viene illustrato come usare Azure Active Directory B2C per fornire l'autenticazione e autorizzazione a un'istanza di Azure App per dispositivi mobili con xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: c28ddc09b07066de67f5c974cf5c2128726c6932
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>L'integrazione di Azure Active Directory B2C con le App per dispositivi mobili di Azure

_Azure Active B2C di Directory è una soluzione di gestione di identità cloud per applicazioni mobili e web per consumatori. In questo articolo viene illustrato come usare Azure Active Directory B2C per fornire l'autenticazione e autorizzazione a un'istanza di Azure App per dispositivi mobili con xamarin. Forms._

![](~/media/shared/preview.png "Questa API è attualmente pre-release.")

> [!NOTE]
> Il [libreria di autenticazione Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) è ancora in anteprima, ma è adatto per l'uso in un ambiente di produzione. Tuttavia, vi può essere modifiche di rilievo per l'API, il formato della cache interna e altri meccanismi della libreria, che può rallentare l'applicazione.

## <a name="overview"></a>Panoramica

Azure App per dispositivi mobili consentono di sviluppare applicazioni con scalabilità back-end ospitato in Azure App Service, con supporto per l'autenticazione per dispositivi mobili, non in linea sincronizzazione e le notifiche push. Per ulteriori informazioni sulle applicazioni per dispositivi mobili di Azure, vedere [utilizzo di un'App Mobile Azure](~/xamarin-forms/data-cloud/consuming/azure.md), e [autenticazione degli utenti con App mobili di Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

Azure Active B2C di Directory è un servizio di gestione di identità per le applicazioni per consumatori, che consente ai consumer di accedere all'applicazione da:

- Utilizzando gli account esistenti di social networking (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Creazione di nuove credenziali (indirizzo di posta elettronica e password, o nome utente e password). Queste credenziali vengono dette *locale* account.

Per ulteriori informazioni su Azure Active Directory B2C, vedere [autenticazione degli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Per gestire il flusso di lavoro di autenticazione per un'App Mobile di Azure, è possibile utilizzare Azure B2C Directory attiva. Con questo approccio, l'esperienza di gestione di identità è completamente definita nel cloud e può essere modificato senza modificare il codice dell'applicazione per dispositivi mobili.

Sono disponibili due flussi di lavoro autenticazione che è possibile adottare per l'integrazione di un tenant di Azure Active Directory B2C con un'istanza di App mobili di Azure:

- [Client gestito](#client_managed) : in questo approccio l'utente avvia applicazione per dispositivi mobili di xamarin. Forms il processo di autenticazione con il tenant di Azure Active Directory B2C e passa il token di autenticazione ricevuto per l'istanza di App mobili di Azure.
- [Server gestito](#server_managed) – in questo approccio App per dispositivi mobili di Azure l'istanza utilizza il tenant di Azure Active Directory B2C per avviare il processo di autenticazione tramite un flusso di lavoro basata sul web.

In entrambi i casi, l'esperienza di autenticazione viene fornito dal tenant Azure Active Directory B2C. Nell'applicazione di esempio, in questo modo la schermata di accesso illustrata nelle schermate seguenti:

![](azure-ad-b2c-mobile-app-images/screenshots.png "Pagina di accesso")

Accedi con il provider di identità di social networking, o con un account locale, sono consentiti. Mentre Microsoft e Google, Facebook vengono utilizzati come provider di identità di social networking in questo esempio, altri provider di identità è inoltre utilizzabile.

## <a name="setup"></a>Configurazione

Indipendentemente dal flusso di lavoro di autenticazione utilizzato, il processo iniziale per l'integrazione di un tenant di Azure Active Directory B2C con un'istanza di App mobili di Azure è la seguente:

1. Creare un'istanza di App mobili di Azure. Per ulteriori informazioni, vedere [utilizzo di un'App Mobile Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Abilitare l'autenticazione nell'istanza App mobili di Azure e l'applicazione di xamarin. Forms. Per ulteriori informazioni, vedere [autenticazione degli utenti con App mobili di Azure](~/xamarin-forms/data-cloud/authentication/azure.md).
1. Creare un tenant di Azure Active Directory B2C. Per ulteriori informazioni, vedere [autenticazione degli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Si noti che la libreria di autenticazione di Microsoft (MSAL) è necessaria quando si utilizza un flusso di lavoro di autenticazione client gestito. MSAL Usa web browser del dispositivo per eseguire l'autenticazione. Ciò migliora l'usabilità di un'applicazione, come gli utenti devono solo effettuare l'accesso una volta per ogni dispositivo, migliorando i tassi di conversione di accesso e autorizzazione flussi nell'applicazione. Il browser del dispositivo offre inoltre una maggiore sicurezza. Dopo che l'utente completa il processo di autenticazione, il controllo verrà restituito all'applicazione dalla scheda del web browser. Questo risultato viene ottenuto tramite la registrazione di uno schema URL personalizzato per l'URL di reindirizzamento restituito dal processo di autenticazione, quindi rileva e gestisce l'URL personalizzato dopo averla inviata. Per ulteriori informazioni sull'utilizzo MSAL per comunicare con un tenant di Azure Active Directory B2C, vedere [autenticazione degli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

<a name="client_managed" />

## <a name="client-managed-authentication"></a>Autenticazione client gestito

L'autenticazione client gestito, un'applicazione per dispositivi mobili con xamarin. Forms contatta un tenant di Azure Active Directory B2C per avviare un flusso di autenticazione. Dopo l'esito positivo sign-on di Active Directory B2C di Azure tenant restituisce un token di identità che viene quindi fornito durante l'accesso all'istanza di App mobili di Azure. In questo modo l'applicazione di xamarin. Forms eseguire azioni sull'istanza di App mobili di Azure che richiede le autorizzazioni utente autenticato.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Configurazione del Tenant Azure Active Directory B2C

Per un flusso di lavoro di autenticazione client gestito, il tenant di Azure Active Directory B2C deve essere configurato come segue:

- Includere un client nativo.
- Impostare l'URI di reindirizzamento personalizzato a uno schema di URL che identifica in modo univoco l'applicazione mobile, seguito da `://auth/`. Per ulteriori informazioni sulla scelta di uno schema URL personalizzato, vedere [scelta di un URI di reindirizzamento app nativa](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri).

Nella schermata seguente viene illustrata questa configurazione:

[![](azure-ad-b2c-mobile-app-images/client-flow-config-sml.png "Configurazione di Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/client-flow-config.png#lightbox "configurazione di Azure Active Directory B2C")

I criteri usati in Azure Active Directory B2C tenant deve inoltre essere configurato in modo che l'URL di risposta è impostato lo stesso schema URL personalizzato, seguito da `://auth/`. Nella schermata seguente viene illustrata questa configurazione:

![](azure-ad-b2c-mobile-app-images/client-flow-policies.png "Criteri di Azure Active Directory B2C")

### <a name="azure-mobile-app-configuration"></a>Configurazione delle App mobili di Azure

Per un flusso di lavoro di autenticazione client gestito, l'istanza di App mobili di Azure deve essere configurata come segue:

- Servizio app deve essere attivata l'autenticazione.
- L'azione da intraprendere quando non viene autenticata una richiesta deve essere impostato su **Accedi con Azure Active Directory**.

Nella schermata seguente viene illustrata questa configurazione:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-config.png "Configurazione di App per dispositivi mobili di Azure")

L'istanza di App mobili di Azure deve essere configurato anche per comunicare con il tenant di Azure Active Directory B2C. Questo può essere eseguito abilitando **avanzate** modalità per il provider di autenticazione di Azure Active Directory, con la **ID Client** dal **ID applicazione** di Azure Tenant di Active Directory B2C e **Url autorità di certificazione** dall'endpoint dei metadati per i criteri di Azure Active Directory B2C. Nella schermata seguente viene illustrata questa configurazione:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-advanced-config.png "App per dispositivi mobili Azure configurazione avanzata")

### <a name="signing-in"></a>Accedi

Esempio di codice seguente viene illustrato come avviare un flusso di lavoro di autenticazione client gestito:

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

La libreria di autenticazione di Microsoft (MSAL) viene utilizzato per avviare un flusso di lavoro di autenticazione con il tenant di Azure Active Directory B2C. Il `AcquireTokenAsync` metodo Avvia browser del dispositivo e visualizza le opzioni di autenticazione definite nei criteri di Azure Active Directory B2C specificato dai criteri di cui viene fatto riferimento tramite il `Constants.Authority` costante. Tali criteri definiscono le esperienze che verranno esaminate consumer durante l'iscrizione e accesso e le attestazioni che l'applicazione riceverà corretta iscrizione o accesso.

Il risultato di `AcquireTokenAsync` chiamata al metodo è un `AuthenticationResult` istanza. Se l'autenticazione ha esito positivo, il `AuthenticationResult` istanza conterrà un token di identità, verrà memorizzato localmente. Se l'autenticazione ha esito negativo, il `AuthenticationResult` istanza conterrà dati che indicano il motivo per cui autenticazione non riuscita. Per informazioni su come usare MSAL per comunicare con un tenant di Azure Active Directory B2C, vedere [autenticazione degli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Quando il `MobileServiceClient.LoginAsync` metodo viene richiamato, l'istanza di App mobili di Azure riceve il token di identità inserito in un `JObject`. La presenza di un mezzo di token valido che l'istanza di App mobili di Azure non è necessario avviare il proprio flusso di autenticazione OAuth 2.0. Al contrario, il `MobileServiceClient.LoginAsync` metodo restituisce un `MobileServiceUser` istanza in cui verrà archiviato nel `MobileServiceClient.CurrentUser` proprietà. Questa proprietà fornisce `UserId` e `MobileServiceAuthenticationToken` proprietà. Questi rappresentano l'utente autenticato e un token di autenticazione per l'utente, che può essere usato fino alla scadenza. Il token di autenticazione verrà inclusi in tutte le richieste effettuate all'istanza di App mobili di Azure, consentendo all'applicazione di xamarin. Forms eseguire azioni sull'istanza di App mobili di Azure che richiedono autorizzazioni di utente autenticato.

### <a name="signing-out"></a>La disconnessione

Esempio di codice seguente viene illustrato come viene richiamato la il processo di disconnessione client gestito:

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

Il `MobileServiceClient.LogoutAsync` metodo deallocare autentica l'utente con l'istanza di App mobili di Azure e quindi tutti i token di autenticazione vengono cancellati dalla cache locale creata da MSAL.

<a name="server_managed" />

## <a name="server-managed-authentication"></a>Autenticazione server gestito

L'autenticazione server gestito, un'applicazione di xamarin. Forms contatta un'istanza di App mobili di Azure, che usa il tenant di Azure Active Directory B2C per gestire il flusso di autenticazione OAuth 2.0 mediante la visualizzazione di una pagina di accesso, come definito nel criterio B2C. Dopo l'esito positivo sign-on, l'istanza di App mobili di Azure restituisce un token che consente all'applicazione di xamarin. Forms eseguire azioni sull'istanza di App mobili di Azure che richiedono autorizzazioni di utente autenticato.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Configurazione del Tenant Azure Active Directory B2C

Per un flusso di lavoro di autenticazione server gestito, il tenant di Azure Active Directory B2C deve essere configurato come segue:

- Include una web app o API web e consentire il flusso implicito.
- Impostare l'URL di risposta per l'indirizzo dell'App Mobile Azure, seguito da `/.auth/login/aad/callback`.

Nella schermata seguente viene illustrata questa configurazione:

[![](azure-ad-b2c-mobile-app-images/server-flow-config-sml.png "Configurazione di Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "configurazione di Azure Active Directory B2C")

I criteri usati in Azure Active Directory B2C tenant deve inoltre essere configurato in modo che l'URL di risposta è impostato per l'indirizzo dell'App Mobile Azure, seguito da `/.auth/login/aad/callback`. Nella schermata seguente viene illustrata questa configurazione:

![](azure-ad-b2c-mobile-app-images/server-flow-policies.png "Criteri di Azure Active Directory B2C")

### <a name="azure-mobile-apps-instance-configuration"></a>Configurazione dell'istanza di App per dispositivi mobili di Azure

Per un flusso di lavoro di autenticazione server gestito, l'istanza di App mobili di Azure deve essere configurata come segue:

- Servizio app deve essere attivata l'autenticazione.
- L'azione da intraprendere quando non viene autenticata una richiesta deve essere impostato su **Accedi con Azure Active Directory**.

Nella schermata seguente viene illustrata questa configurazione:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-config.png "Configurazione di App per dispositivi mobili di Azure")

L'istanza di App mobili di Azure deve essere configurato anche per comunicare con il tenant di Azure Active Directory B2C. Questo può essere eseguito abilitando **avanzate** modalità per il provider di autenticazione di Azure Active Directory, con la **ID Client** dal **ID applicazione** di Azure Tenant di Active Directory B2C e **Url autorità di certificazione** dall'endpoint dei metadati per i criteri di Azure Active Directory B2C. Nella schermata seguente viene illustrata questa configurazione:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-advanced-config.png "App per dispositivi mobili Azure configurazione avanzata")

### <a name="signing-in"></a>Accedi

Esempio di codice seguente viene illustrato come avviare un flusso di lavoro gestiti dal server di autenticazione:

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

Quando il `MobileServiceClient.LoginAsync` metodo viene richiamato, l'istanza di App mobili di Azure, esegue i criteri di Azure Active Directory B2C collegato, che avvia il flusso di autenticazione OAuth 2.0. Si noti che ogni `AuthenticateAsync` metodo è specifica della piattaforma. Tuttavia, ogni `AuthenticateAsync` metodo utilizza il `MobileServiceClient.LoginAsync` (metodo) e specifica che un tenant Azure Active Directory verrà utilizzato nel processo di autenticazione. Per ulteriori informazioni, vedere [la connessione degli utenti](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in).

Il `MobileServiceClient.LoginAsync` metodo restituisce un `MobileServiceUser` istanza in cui verrà archiviato nel `MobileServiceClient.CurrentUser` proprietà. Questa proprietà fornisce `UserId` e `MobileServiceAuthenticationToken` proprietà. Questi rappresentano l'utente autenticato e un token di autenticazione per l'utente, che può essere usato fino alla scadenza. Il token di autenticazione verrà inclusi in tutte le richieste effettuate all'istanza di App mobili di Azure, consentendo all'applicazione di xamarin. Forms eseguire azioni sull'istanza di App mobili di Azure che richiedono autorizzazioni di utente autenticato.

### <a name="signing-out"></a>La disconnessione

Esempio di codice seguente viene illustrato come viene richiamato la il processo di disconnessione di server gestito:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
    ...
}
```

Il `MobileServiceClient.LogoutAsync` metodo deallocare autentica l'utente con l'istanza di App mobili di Azure. Per ulteriori informazioni, vedere [registrazione Out utenti](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out).

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare Azure Active Directory B2C per fornire l'autenticazione e autorizzazione a un'istanza di Azure App per dispositivi mobili con xamarin. Forms. Azure Active B2C di Directory è una soluzione di gestione di identità cloud per applicazioni mobili e web per consumatori.


## <a name="related-links"></a>Collegamenti correlati

- [TodoAzureAuth ServerFlow (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [TodoAzureAuth ClientFlow (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [Utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [L'autenticazione degli utenti con App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [L'autenticazione degli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Libreria di autenticazione Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
