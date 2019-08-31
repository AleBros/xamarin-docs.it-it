---
title: Accesso all'API Graph
description: Questo documento descrive come aggiungere Azure Active Directory autenticazione a un'applicazione per dispositivi mobili compilata con Novell.
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 74072a48e190478af79ec06ca8e5048d2cb61e36
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198579"
---
# <a name="accessing-the-graph-api"></a>Accesso all'API Graph

Attenersi alla procedura seguente per usare la API Graph all'interno di un'applicazione Novell:

1. Per la [registrazione con Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) nel portale di *WindowsAzure.com* ,
2. [Configurare i servizi](~/cross-platform/data-cloud/active-directory/get-started/configure.md).

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>Passaggio 3. Aggiunta di Active Directory autenticazione a un'app

Nell'applicazione aggiungere un riferimento a **Azure Active Directory Authentication Library (Azure adal)** usando Gestione pacchetti NuGet in Visual Studio o Visual Studio per Mac.
Assicurarsi di selezionare **Mostra pacchetti di versioni** non definitive per includere questo pacchetto, in quanto è ancora in fase di anteprima.

> [!IMPORTANT]
> Nota: Azure ADAL 3,0 è attualmente un'anteprima ed è possibile che vengano apportate modifiche di rilievo prima del rilascio della versione finale. 


![](graph-images/06.-adal-nuget-package.jpg "Aggiungere un riferimento a Azure Active Directory Authentication Library (Azure ADAL)")

Nell'applicazione è ora necessario aggiungere le variabili a livello di classe seguenti necessarie per il flusso di autenticazione.

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

Un aspetto da tenere presente è `commonAuthority`. Quando l'endpoint di autenticazione `common`è, l'app diventa **multi-tenant**, il che significa che qualsiasi utente può usare l'account di accesso con le credenziali Active Directory. Dopo l'autenticazione, l'utente funzionerà nel contesto del proprio Active Directory, ovvero visualizzerà i dettagli relativi al Active Directory.

### <a name="write-method-to-acquire-access-token"></a>Metodo Write per acquisire il token di accesso

Il codice seguente (per Android) avvierà l'autenticazione e al termine assegnare il risultato in `authResult`. Le implementazioni di iOS e Windows Phone differiscono leggermente: il secondo`Activity`parametro () è diverso in iOS e assente su Windows Phone.

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

Nel codice precedente, `AuthenticationContext` è responsabile dell'autenticazione con commonAuthority. Dispone di un `AcquireTokenAsync` metodo che accetta parametri come risorsa a cui è necessario accedere, in questo `clientId`caso `graphResourceUri`, e `returnUri`. L'app tornerà a al termine `returnUri` dell'autenticazione. Questo codice rimarrà lo stesso per tutte le piattaforme, tuttavia, l'ultimo parametro `AuthorizationParameters`,, sarà diverso su piattaforme diverse ed è responsabile della gestione del flusso di autenticazione.

Nel caso di Android o iOS passa `this` il parametro a `AuthorizationParameters(this)` per condividere il contesto, mentre in Windows viene passato senza alcun parametro come nuovo `AuthorizationParameters()`.

### <a name="handle-continuation-for-android"></a>Gestire la continuazione per Android

Al termine dell'autenticazione, il flusso dovrebbe tornare all'app. Nel caso di Android è gestito dal codice seguente, che deve essere aggiunto a **MainActivity.cs**:


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="handle-continuation-for-windows-phone"></a>Gestire la continuazione per Windows Phone

Per Windows Phone modificare il `OnActivated` metodo nel file **app.XAML.cs** con il codice seguente:

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

A questo punto, se si esegue l'applicazione, viene visualizzata una finestra di dialogo di autenticazione.
Al completamento dell'autenticazione, verrà chiesto le autorizzazioni per accedere alle risorse (in questo caso API Graph):

![](graph-images/08.-authentication-flow.jpg "Al completamento dell'autenticazione, le autorizzazioni per accedere alle risorse vengono richieste in questo caso API Graph")

Se l'autenticazione ha esito positivo e l'app è stata autorizzata ad accedere alle risorse, `AccessToken` è `RefreshToken` necessario ottenere `authResult`una combinazione di e in. Questi token sono necessari per ulteriori chiamate API e per l'autorizzazione con Azure Active Directory dietro le quinte.

![](graph-images/07.-access-token-for-authentication.jpg "Questi token sono necessari per ulteriori chiamate API e per l'autorizzazione con Azure Active Directory dietro le quinte")

Il codice seguente, ad esempio, consente di ottenere un elenco di utenti da Active Directory. È possibile sostituire l'URL dell'API Web con l'API Web protetta da Azure AD.

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```

