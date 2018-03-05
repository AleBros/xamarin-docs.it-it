---
title: Accesso all'API Graph
description: Utilizzo di Active Directory per le query sull'API Graph con Xamarin
ms.topic: article
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 2b0e4a9466ab59ec27b957bf284a7d3895f6a4fc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="accessing-the-graph-api"></a>Accesso all'API Graph

_Utilizzo di Active Directory per le query sull'API Graph con Xamarin_

Seguire questi passaggi per l'utilizzo dell'API Graph da all'interno di un'applicazione di Xamarin:

1. [Registrazione con Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) sul *windowsazure.com* portale, quindi
2. [Configurare i servizi](~/cross-platform/data-cloud/active-directory/get-started/configure.md).

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>Passaggio 3. Aggiunta a un'app di autenticazione di Active Directory

Nell'applicazione, aggiungere un riferimento a **Azure Active Directory Authentication Library (ADAL Azure)** tramite Gestione pacchetti NuGet in Visual Studio o Visual Studio per Mac.
Assicurarsi di selezionare **Mostra i pacchetti pre-release** per includere questo pacchetto, perché è ancora in anteprima.

> [!IMPORTANT]
> Nota: 3.0 ADAL Azure è attualmente un'anteprima e potrebbe verificarsi modifiche di rilievo viene rilasciata la versione finale. 


![](graph-images/06.-adal-nuget-package.jpg "Aggiungere un riferimento a Azure Active Directory Authentication Library (ADAL Azure)")

Nell'applicazione, è necessario ora aggiungere le seguenti variabili di livello di classe necessarie per il flusso di autenticazione.

```csharp
//Client ID
public static string clientId = "25927d3c-.....-63f2304b90de";
public static string commonAuthority = "https://login.windows.net/common"
//Redirect URI
public static Uri returnUri = new Uri("http://xam-demo-redirect");
//Graph URI if you've given permission to Azure Active Directory
const string graphResourceUri = "https://graph.windows.net";
public static string graphApiVersion = "2013-11-08";
//AuthenticationResult will hold the result after authentication completes
AuthenticationResult authResult = null;
```

Una cosa da notare `commonAuthority`. Quando l'endpoint di autenticazione è `common`, diventa l'app **multi-tenant**, ovvero qualsiasi utente può utilizzare l'account di accesso con le proprie credenziali di Active Directory. Dopo l'autenticazione, tale utente funzionerà sul contesto del proprio Active Directory: ad esempio visualizzeranno i dettagli relativi a suo Active Directory.

### <a name="write-method-to-acquire-access-token"></a>Write per acquisire un Token di accesso

Il codice seguente (per Android) avvia l'autenticazione e al termine dell'operazione, assegnare il risultato in `authResult`. Le implementazioni di Windows Phone iOS variano leggermente: il secondo parametro (`Activity`) è diverso in iOS e non sono presenti in Windows Phone.

```csharp
public static async Task<AuthenticationResult> GetAccessToken
            (string serviceResourceId, Activity activity)
{
    authContext = new AuthenticationContext(Authority);
    if (authContext.TokenCache.ReadItems().Count() > 0)
        authContext = new AuthenticationContext(authContext.TokenCache.ReadItems().First().Authority);
    var authResult = await authContext.AcquireTokenAsync(serviceResourceId, clientId, returnUri, new AuthorizationParameters(activity));
    return authResult;
}  
```

Nel codice precedente, il `AuthenticationContext` è responsabile per l'autenticazione con commonAuthority. Ha un `AcquireTokenAsync` metodo, che accettano parametri come risorsa che deve essere accessibile in questo caso `graphResourceUri`, `clientId`, e `returnUri`. L'app verrà visualizzata nuovamente la `returnUri` quando viene completata l'autenticazione. Questo codice rimarrà uguale per tutte le piattaforme, tuttavia, l'ultimo parametro `AuthorizationParameters`, saranno diversi su piattaforme diverse ed è responsabile che controllano il flusso di autenticazione.

Nel caso di Android o iOS, passiamo `this` parametro `AuthorizationParameters(this)` per condividere il contesto, mentre in Windows viene passato senza alcun parametro come nuovo `AuthorizationParameters()`.

### <a name="handle-continuation-for-android"></a>Handle di continuazione per Android

Una volta completata l'autenticazione, il flusso deve tornare all'app. Nel caso di Android viene gestita dal codice seguente, che devono essere aggiunti a **Mainactivity**:


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);

    
}
```

### <a name="handle-continuation-for-windows-phone"></a>Handle di continuazione per Windows Phone

Per Windows Phone, modificare il `OnActivated` metodo il **App.xaml.cs** file con il codice riportato di seguito:

```csharp
protected override void OnActivated(IActivatedEventArgs args)
{
#if WINDOWS_PHONE_APP
  if (args is IWebAuthenticationBrokerContinuationEventArgs)
  {
     WebAuthenticationBrokerContinuationHelper.SetWebAuthenticationBrokerContinuationEventArgs(args as IWebAuthenticationBrokerContinuationEventArgs);
  }
#endif
  base.OnActivated(args);
}
```

Ora se si esegue l'applicazione, verrà visualizzato una finestra di dialogo di autenticazione.
Quando l'autenticazione ha esito positivo, richiede le autorizzazioni per accedere alle risorse (in questo caso API Graph):

![](graph-images/08.-authentication-flow.jpg "Quando l'autenticazione ha esito positivo, richiede le autorizzazioni per accedere alle risorse in questo caso API Graph")

Se l'autenticazione ha esito positivo e si è autorizzato all'app di accedere alle risorse, è necessario ottenere un `AccessToken` e `RefreshToken` combinata in `authResult`. I token interessati sono richiesti per ulteriormente le chiamate API e per l'autorizzazione con Azure Active Directory in background.

![](graph-images/07.-access-token-for-authentication.jpg "I token interessati sono richiesti per ulteriormente le chiamate API e per l'autorizzazione con Azure Active Directory in background")

Ad esempio, il codice seguente consente di ottenere un elenco di utenti da Active Directory. È possibile sostituire l'URL dell'API Web con l'API Web protetta da Azure AD.

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```

