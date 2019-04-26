---
title: Accesso all'API Graph
description: Questo documento descrive come aggiungere l'autenticazione di Azure Active Directory a un'applicazione per dispositivi mobili compilata con Xamarin.
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c43dfa79831f22e55490b27c3c360602ae717627
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61189965"
---
# <a name="accessing-the-graph-api"></a>Accesso all'API Graph

Seguire questi passaggi per usare l'API Graph da un'applicazione Xamarin:

1. [La registrazione con Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) nella *windowsazure.com* portale, quindi
2. [Configurare i servizi](~/cross-platform/data-cloud/active-directory/get-started/configure.md).

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>Passaggio 3. Aggiunta l'autenticazione di Active Directory a un'app

Nell'applicazione, aggiungere un riferimento a **Azure Active Directory Authentication Library (ADAL di Azure)** usando Gestione pacchetti NuGet in Visual Studio o Visual Studio per Mac.
Assicurarsi di selezionare **Mostra i pacchetti di versioni non definitive** per includere questo pacchetto, perché è ancora in anteprima.

> [!IMPORTANT]
> Nota: Azure ADAL 3.0 è attualmente un'anteprima e potrebbero esserci modifiche di rilievo prima del rilascio della versione finale. 


![](graph-images/06.-adal-nuget-package.jpg "Aggiungere un riferimento ad Azure Active Directory Authentication Library (ADAL di Azure)")

Nell'applicazione, è ora necessario aggiungere le seguenti variabili di livello di classe necessarie per il flusso di autenticazione.

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

Un aspetto da sottolineare `commonAuthority`. Quando è l'endpoint di autenticazione `common`, l'app acquisisce **multi-tenant**, ovvero tutti gli utenti possa usare account di accesso con le proprie credenziali Active Directory. Dopo l'autenticazione, quell'utente funzionerà sul contesto del proprio Active Directory, ad esempio visualizzeranno i dettagli relativi alla sua Active Directory.

### <a name="write-method-to-acquire-access-token"></a>Scrivere un metodo per acquisire un Token di accesso

Il codice seguente (per Android) verrà avviare il processo di autenticazione e al completamento assegnare il risultato in `authResult`. IOS e le implementazioni di Windows Phone variano leggermente: il secondo parametro (`Activity`) è diverso in iOS e non sono presenti nel Windows Phone.

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

Nel codice precedente, il `AuthenticationContext` è responsabile per l'autenticazione con commonAuthority. Ha un `AcquireTokenAsync` metodo, che accettano parametri come una risorsa che deve accedere, in questo caso `graphResourceUri`, `clientId`, e `returnUri`. L'app verrà visualizzata nuovamente la `returnUri` quando viene completata l'autenticazione. Questo codice rimarranno gli stessi per tutte le piattaforme, tuttavia, l'ultimo parametro, `AuthorizationParameters`, saranno diversi su piattaforme diverse ed è responsabile che controllano il flusso di autenticazione.

Nel caso di Android o iOS, passiamo `this` parametro per `AuthorizationParameters(this)` condividere il contesto, mentre in Windows viene passato senza alcun parametro come nuovo `AuthorizationParameters()`.

### <a name="handle-continuation-for-android"></a>Handle di continuazione per Android

Al termine dell'autenticazione, il flusso deve tornare all'app. Nel caso di Android viene gestita dal codice seguente, che devono essere aggiunti al **MainActivity.cs**:


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);

    
}
```

### <a name="handle-continuation-for-windows-phone"></a>Handle di continuazione per Windows Phone

Per Windows Phone, modificare il `OnActivated` metodo nella **App.xaml.cs** file con il codice riportato di seguito:

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

Ora se si esegue l'applicazione, si noterà una finestra di dialogo di autenticazione.
Al termine dell'autenticazione, verrà chiesto di autorizzazioni per accedere alle risorse (in questo caso, API Graph):

![](graph-images/08.-authentication-flow.jpg "Al termine dell'autenticazione, verrà chiesto di autorizzazioni per accedere alle risorse in questo caso API Graph")

Se l'autenticazione ha esito positivo e autorizzazione all'app di accedere alle risorse, è necessario ottenere un `AccessToken` e `RefreshToken` combinata in `authResult`. Questi token sono necessari per ulteriormente le chiamate API e per l'autorizzazione con Azure Active Directory in background.

![](graph-images/07.-access-token-for-authentication.jpg "Questi token sono necessari per ulteriormente le chiamate API e per l'autorizzazione con Azure Active Directory in background")

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

