---
title: L'integrazione di Azure Active Directory B2C con App per dispositivi mobili di Azure
description: Azure Active B2C di Directory è una soluzione di gestione identità cloud per applicazioni rivolte agli utenti web e mobili. Questo articolo illustra come usare Azure Active Directory B2C per fornire l'autenticazione e autorizzazione a un'istanza di App per dispositivi mobili di Azure con xamarin. Forms.
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2019
ms.openlocfilehash: 2f9cfceee4765dea946dfdac974ac2d56595ef94
ms.sourcegitcommit: 9aaae4f0af096cd136b3dcfbb9af591ba307dc25
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2019
ms.locfileid: "67398686"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>L'integrazione di Azure Active Directory B2C con App per dispositivi mobili di Azure

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)

_Azure Active B2C di Directory consente la gestione delle identità cloud per le applicazioni rivolte agli utenti. Questo articolo illustra come usare Azure Active Directory B2C per integrare la gestione delle identità in un'istanza di App per dispositivi mobili di Azure con xamarin. Forms._

## <a name="overview"></a>Panoramica

Le App per dispositivi mobili di Azure consentono di sviluppare applicazioni con un back-end scalabili, ospitata in servizio App di Azure. Supporta l'autenticazione per dispositivi mobili, la sincronizzazione offline e push delle notifiche. Per altre informazioni sulle App per dispositivi mobili di Azure, vedere [utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md), e [agli utenti l'autenticazione con Azure Mobile Apps](~/xamarin-forms/data-cloud/authentication/azure.md).

Azure Active B2C di Directory è un servizio di gestione di identità per le applicazioni rivolte ai consumatori, che consente ai consumer:

- Accedere con gli account di social networking esistenti (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Creare nuove credenziali (indirizzo di posta elettronica e password, o nome utente e password). Queste credenziali sono dette *locale* account.

Per altre informazioni su Azure Active Directory B2C, vedere [autenticare gli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Azure Active B2C di Directory può essere utilizzato per gestire il flusso di lavoro di autenticazione per un'App per dispositivi mobili di Azure. Questo approccio consente di configurare la gestione delle identità nel cloud e consente di apportare modifiche senza modificare il codice dell'applicazione.


Le App per dispositivi mobili di Azure con Azure Active Directory B2C consente due metodi di autenticazione:

- [Client gestito](#client-managed-authentication) : l'applicazione per dispositivi mobili xamarin. Forms viene avviato il processo di autenticazione con il tenant di Azure Active Directory B2C e passa il token di autenticazione ricevute all'istanza di App per dispositivi mobili di Azure.
- [Gestita dal server](#server-managed-authentication) – l'istanza di App per dispositivi mobili di Azure Usa il tenant di Azure Active Directory B2C per avviare il processo di autenticazione tramite un flusso di lavoro basato sul web.

In entrambi i casi, l'esperienza di autenticazione viene fornito dal tenant Azure Active Directory B2C. Nell'applicazione di esempio, la schermata di accesso dovrebbe essere simile agli screenshot seguenti:

![](azure-ad-b2c-mobile-app-images/screenshots.png "Pagina di accesso")

Questo esempio viene illustrato l'accesso con un account locale, ma è anche possibile abilitare Microsoft, Google, Facebook e altri provider di identità.

## <a name="setup"></a>Configurazione

In entrambi i flussi di lavoro, il processo iniziale per l'integrazione di un tenant di Azure Active Directory B2C con App per dispositivi mobili di Azure è come segue:

1. Creare un'istanza di App per dispositivi mobili di Azure nel portale di Azure. Viene generato un progetto iniziale simile al progetto di esempio. Per altre informazioni, vedere [utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Creare un tenant di Azure Active Directory B2C. Per altre informazioni, vedere [autenticare gli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).
1. Abilitare l'autenticazione nell'istanza di App per dispositivi mobili di Azure e l'applicazione xamarin. Forms. Per altre informazioni, vedere [autenticare gli utenti con App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

> [!NOTE]
> Microsoft Authentication Library (MSAL) è obbligatorio quando si usa un flusso di lavoro di autenticazione gestita dal client. MSAL Usa il web browser del dispositivo per l'autenticazione. Una volta completata l'autenticazione nel browser, l'utente viene reindirizzato a uno schema URL personalizzato. L'applicazione registra lo schema URL personalizzato, in modo che possa riprendere il controllo dell'esperienza utente e rispondere all'autenticazione. Per altre informazioni sull'uso di MSAL per comunicare con un tenant di Azure Active Directory B2C, vedere [autenticare gli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

## <a name="client-managed-authentication"></a>Autenticazione gestita dal client

L'autenticazione gestita dal client, un'applicazione xamarin. Forms contatta un tenant di Azure Active Directory B2C per avviare il flusso di autenticazione. Dopo aver sign-on, il tenant di Azure Active Directory B2C restituisce un token di identità, che viene fornito all'istanza di App per dispositivi mobili di Azure. In questo modo l'applicazione xamarin. Forms eseguire azioni che richiedono le autorizzazioni utente autenticato.

### <a name="azure-active-directory-b2c-client-managed-tenant-configuration"></a>Configurazione del tenant gestita dal client di Azure Active Directory B2C

Per un flusso di lavoro di autenticazione gestita dal client, il tenant di Azure Active Directory B2C deve essere configurato come segue:

- Impostare **Includi client nativo** su "Sì".
- Impostare l'URI di reindirizzamento personalizzato. La documentazione di MSAL consiglia con "msal" combinata con l'ID dell'App e seguito da ":/ /. auth /". Per altre informazioni, vedere [URI di reindirizzamento alle applicazioni client MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#redirect-uri).

Lo screenshot seguente illustra questa configurazione:

[!["Configurazione di azure Active B2C di Directory"](azure-ad-b2c-mobile-app-images/azure-redirect-uri-cropped.png)](azure-ad-b2c-mobile-app-images/azure-redirect-uri.png#lightbox "configurazione di Azure Active Directory B2C")

Il criterio di accesso utilizzato nel tenant di Azure Active Directory B2C deve anche essere configurato in modo che l'URL di risposta viene impostato sullo stesso schema URL personalizzato. Ciò può essere ottenuta selezionando **eseguire il flusso utente** nel portale di Azure per le impostazioni dei criteri di accesso. Salvare l'URL dei metadati contenuto in questa schermata perché sarà necessaria per la configurazione di App per dispositivi mobili. Lo screenshot seguente illustra questa configurazione e l'URL è necessario copiare:

[!["Azure Active Directory B2C criteri"](azure-ad-b2c-mobile-app-images/client-flow-policies-cropped.png)](azure-ad-b2c-mobile-app-images/client-flow-policies.png#lightbox "configurazione dei criteri di Azure Active B2C di Directory")

### <a name="azure-mobile-app-configuration"></a>Configurazione di App per dispositivi mobili di Azure

Per un flusso di lavoro di autenticazione gestita dal client, configurare l'istanza di App per dispositivi mobili di Azure come indicato di seguito:

- Autenticazione del servizio app deve essere attivato.
- L'azione da intraprendere quando non viene autenticata una richiesta deve essere impostato su **Accedi con Azure Active Directory**.

Lo screenshot seguente illustra questa configurazione:

[!["Configurazione dell'autenticazione delle App per dispositivi mobili di azure"](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "configurazione dell'autenticazione delle App per dispositivi mobili di Azure")

Configurare l'istanza di App per dispositivi mobili di Azure per comunicare con il tenant di Azure Active Directory B2C:

- Fare clic su configurazione di Azure Active Directory e abilitare **avanzate** modalità per il provider di autenticazione di Azure Active Directory.
- Impostare **ID Client** per il **ID applicazione** del tenant di Azure Active Directory B2C.
- Impostare il **Url autorità di certificazione** all'URL di metadati del criterio che è stato copiato in precedenza durante la configurazione del tenant.

Lo screenshot seguente illustra questa configurazione:

!["Configurazione avanzata di App per dispositivi mobili di azure"](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>Accesso

Esempio di codice seguente mostra le chiamate di metodo chiave per l'avvio di un flusso di lavoro di autenticazione gestita dal client:

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...

    authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        string.Empty,
        UIBehavior.SelectAccount,
        string.Empty,
        App.UiParent);

    ...

    var payload = new JObject();
    if (authenticationResult != null && !string.IsNullOrWhiteSpace(authenticationResult.AccessToken))
    {
        payload["access_token"] = authenticationResult.AccessToken;
    }

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    success = true;

    ...
}
```

Microsoft Authentication Library (MSAL) viene utilizzato per avviare un flusso di lavoro di autenticazione con il tenant di Azure Active Directory B2C. Il `AcquireTokenAsync` metodo avvia web browser del dispositivo e visualizza le opzioni di autenticazione definite nei criteri di Azure Active Directory B2C specificato dai criteri di cui viene fatto riferimento tramite il `Constants.AuthoritySignin` costante. Questo criterio definisce l'esperienza di accesso e iscrizione dell'utente e le attestazioni che l'applicazione riceve al termine dell'autenticazione.

Il risultato del `AcquireTokenAsync` chiamata al metodo è un `AuthenticationResult` istanza. Se l'autenticazione ha esito positivo, il `AuthenticationResult` istanza conterrà un token di accesso, verrà memorizzato localmente. Se l'autenticazione ha esito negativo, il `AuthenticationResult` istanza conterrà i dati che indicano il motivo per cui l'autenticazione non è riuscita. Per informazioni su come usare MSAL per comunicare con un tenant di Azure Active Directory B2C, vedere [autenticare gli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Quando la `CurrentClient.LoginAsync` metodo viene richiamato, l'istanza di App per dispositivi mobili di Azure riceve il token di accesso sottoposta a wrapping in un `JObject`. La presenza di un mezzo di token valido che l'istanza di App per dispositivi mobili di Azure non è necessario avviare il proprio flusso di autenticazione OAuth 2.0. Al contrario, il `CurrentClient.LoginAsync` metodo restituisce un `MobileServiceUser` istanza in cui verrà archiviato nel `MobileServiceClient.CurrentUser` proprietà. Questa proprietà fornisce `UserId` e `MobileServiceAuthenticationToken` proprietà. Questi rappresentano l'utente autenticato e il token, che può essere usato fino alla scadenza. Il token di autenticazione verrà incluso in tutte le richieste effettuate all'istanza di App per dispositivi mobili di Azure, consentendo all'applicazione xamarin. Forms eseguire azioni che richiedono le autorizzazioni utente autenticato.

### <a name="signing-out"></a>Disconnessione

Esempio di codice seguente mostra come viene richiamato il processo di disconnessione gestita dal client:

```csharp
public async Task<bool> LogoutAsync()
{
    ...

    IEnumerable<IAccount> accounts = await ADB2CClient.GetAccountsAsync();
    while (accounts.Any())
    {
        await ADB2CClient.RemoveAsync(accounts.First());
        accounts = await ADB2CClient.GetAccountsAsync();
    }
    User = null;

    ...
}
```

Il `CurrentClient.LogoutAsync` metodo deprovisioning autentica l'utente con l'istanza di App per dispositivi mobili di Azure e quindi tutti i token di autenticazione vengono cancellati dalla cache locale creata da MSAL.

## <a name="server-managed-authentication"></a>Autenticazione gestita dal server

L'autenticazione gestita dal server, un'applicazione xamarin. Forms contatta un'istanza di App per dispositivi mobili di Azure, che usa il tenant di Azure Active Directory B2C per gestire il flusso di autenticazione OAuth 2.0 visualizzando una pagina di accesso base a quanto definito nei criteri di B2C. In seguito ha esito positivo sign-on, l'istanza di App per dispositivi mobili di Azure restituisce un token che consente all'applicazione xamarin. Forms eseguire azioni che richiedono le autorizzazioni utente autenticato.

### <a name="azure-active-directory-b2c-server-managed-tenant-configuration"></a>Configurazione del tenant gestita dal server di Azure Active Directory B2C

Per un flusso di lavoro di autenticazione gestita dal server, il tenant di Azure Active Directory B2C deve essere configurato come segue:

- Includono un'app web/web API e consentire il flusso implicito.
- Impostare l'URL di risposta per l'indirizzo della App per dispositivi mobili di Azure, seguita da `/.auth/login/aad/callback`.

Lo screenshot seguente illustra questa configurazione:

[!["Configurazione di azure Active B2C di Directory"](azure-ad-b2c-mobile-app-images/server-flow-config-cropped.png)](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "configurazione di Azure Active Directory B2C")

I criteri usati nel tenant di Azure Active Directory B2C devono avere l'URL di risposta configurato. Questo risultato viene ottenuto impostando l'URL di risposta per l'indirizzo della tua App per dispositivi mobili di Azure, seguita da `/.auth/login/aad/callback`. È inoltre consigliabile salvare l'URL dei metadati trovato nella parte superiore della schermata perché sarà necessaria per la configurazione di App per dispositivi mobili. Lo screenshot seguente illustra questa configurazione e l'URL dei metadati è consigliabile salvare:

!["Configurazione dei criteri di azure Active B2C di Directory"](azure-ad-b2c-mobile-app-images/server-flow-policies.png)

### <a name="azure-mobile-apps-instance-configuration"></a>Configurazione dell'istanza di App per dispositivi mobili Azure

Per un flusso di lavoro di autenticazione gestita dal server, l'istanza di App per dispositivi mobili di Azure deve essere configurato come segue:

- Autenticazione del servizio app deve essere attivato.
- L'azione da intraprendere quando non viene autenticata una richiesta deve essere impostato su **Accedi con Azure Active Directory**.

Lo screenshot seguente illustra questa configurazione:

[!["Configurazione dell'autenticazione delle App per dispositivi mobili di azure"](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "configurazione dell'autenticazione delle App per dispositivi mobili di Azure")

L'istanza di App per dispositivi mobili di Azure deve essere configurato anche per comunicare con il tenant di Azure Active Directory B2C:

- Fare clic su configurazione di Azure Active Directory e abilitare **avanzate** modalità per il provider di autenticazione di Azure Active Directory.
- Impostare **ID Client** per il **ID applicazione** del tenant di Azure Active Directory B2C.
- Il **Url autorità di certificazione** è l'URL dei metadati del criterio è stato copiato in precedenza durante la configurazione del tenant.

Lo screenshot seguente illustra questa configurazione:

!["Configurazione avanzata di App per dispositivi mobili di azure"](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>Accesso

Esempio di codice seguente viene illustrato come avviare un flusso di lavoro di autenticazione gestita dal server:

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...

    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);

    ...
}
```

Quando il `CurrentClient.LoginAsync` metodo viene richiamato, l'istanza di App per dispositivi mobili di Azure esegue i criteri di Azure Active Directory B2C collegato, che avvia il flusso di autenticazione OAuth 2.0. Ogni `AuthenticateAsync` metodo è specifica della piattaforma. Tuttavia, ogni `AuthenticateAsync` chiamate al metodo il `CurrentClient.LoginAsync` (metodo) e specifica che un tenant di Azure Active Directory verrà utilizzato nel processo di autenticazione. Per altre informazioni, vedere [la registrazione degli utenti](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in).

Il `CurrentClient.LoginAsync` metodo restituisce un `MobileServiceUser` istanza in cui verrà archiviato nel `CurrentClient.CurrentUser` proprietà. Questa proprietà fornisce `UserId` e `MobileServiceAuthenticationToken` proprietà. Questi rappresentano l'utente autenticato e un token di autenticazione per l'utente, che può essere usato fino alla scadenza. Il token di autenticazione verrà incluso in tutte le richieste effettuate all'istanza di App per dispositivi mobili di Azure, consentendo all'applicazione xamarin. Forms eseguire azioni nell'istanza di App per dispositivi mobili di Azure che richiedono le autorizzazioni utente autenticato.

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


## <a name="related-links"></a>Collegamenti correlati

- [TodoAzureAuth ClientFlow (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [TodoAzureAuth ServerFlow (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [Utilizzo di un'App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticazione degli utenti con App per dispositivi mobili di Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Autenticazione degli utenti con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Pacchetto nuget Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentazione di Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
