---
title: 'Xamarin.Essentials: Web Authenticator'
description: Questo documento descrive la classe webauthenticator in Xamarin.Essentials , che consente di avviare flussi di autenticazione basati su browser che restano in attesa di un callback all'app.
ms.assetid: 3D95371E-5D59-440E-8D31-F3C04E493DC1
author: redth
ms.author: jodick
ms.date: 03/26/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 578e750afd718c684d70217355629a0286d2b028
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84801881"
---
# <a name="xamarinessentials-web-authenticator"></a>Xamarin.Essentials: Web Authenticator

La classe **webauthenticator** consente di avviare flussi basati su browser che restano in ascolto di un callback a un URL specifico registrato nell'app.

## <a name="overview"></a>Panoramica

Molte app richiedono l'aggiunta dell'autenticazione utente e questo significa spesso consentire agli utenti di accedere agli account di accesso Microsoft, Facebook, Google e ora Apple esistenti.

[Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) offre un'eccellente soluzione chiavi a chiave per aggiungere l'autenticazione all'app. Il pacchetto NuGet client include anche il supporto per le app Novell.

Se si è interessati a usare il proprio servizio Web per l'autenticazione, è possibile usare **webauthenticator** per implementare la funzionalità lato client.

## <a name="why-use-a-server-back-end"></a>Perché usare un back-end server?

Molti provider di autenticazione sono passati a offrire solo flussi di autenticazione espliciti o a due gambe per garantire una maggiore sicurezza. Ciò significa che è necessario un _"segreto client"_ dal provider per completare il flusso di autenticazione. Sfortunatamente, le app per dispositivi mobili non sono un'ottima posizione per archiviare i segreti e qualsiasi elemento archiviato nel codice di un'app per dispositivi mobili, nei file binari o in caso contrario è considerato non sicuro.

La procedura consigliata consiste nell'usare un back-end Web come livello intermedio tra l'app per dispositivi mobili e il provider di autenticazione.

> [!IMPORTANT]
> È consigliabile evitare di usare le librerie di autenticazione solo per dispositivi mobili meno recenti e i modelli che non sfruttano un back-end Web nel flusso di autenticazione, a causa della mancanza di sicurezza intrinseca per l'archiviazione dei segreti client.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

Per accedere alla funzionalità **webauthenticator** , è necessaria la seguente configurazione specifica della piattaforma.

# <a name="android"></a>[Android](#tab/android)

Android richiede l'installazione di un filtro preventivo per gestire l'URI di callback. Questa operazione viene eseguita facilmente eseguendo la sottoclasse della `WebAuthenticatorCallbackActivity` classe:

> [!NOTE]
> È consigliabile implementare i [collegamenti delle app Android](https://developer.android.com/training/app-links/) per gestire l'URI di callback e assicurarsi che l'applicazione sia l'unica che è in grado di eseguire la registrazione per gestire l'URI di callback.

```csharp
const string CALLBACK_SCHEME = "myapp";

[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop)]
[IntentFilter(new[] { Android.Content.Intent.ActionView },
    Categories = new[] { Android.Content.Intent.CategoryDefault, Android.Content.Intent.CategoryBrowsable },
    DataScheme = CALLBACK_SCHEME)]
public class WebAuthenticationCallbackActivity : Xamarin.Essentials.WebAuthenticatorCallbackActivity
{
}
```

Sarà anche necessario richiamarlo in Essentials dalla `OnResume` sostituzione nel `MainActivity` :

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume();
}
```

# <a name="ios"></a>[iOS](#tab/ios)

In iOS è necessario aggiungere il modello di URI di callback dell'app al file INFO. plist.

> [!NOTE]
> È consigliabile usare i [collegamenti dell'app universale](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content) per registrare l'URI di callback dell'app come procedura consigliata.

Sarà inoltre necessario eseguire l'override del `AppDelegate` `OpenUrl` metodo di per effettuare chiamate in Essentials:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    if (Xamarin.Essentials.Platform.OpenUrl(app, url, options))
        return true;

    return base.OpenUrl(app, url, options);
}
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Per UWP, è necessario dichiarare l'URI di callback nel `Package.appxmanifest` file:

```xml
    <Extensions>
        <uap:Extension Category="windows.protocol">
            <uap:Protocol Name="myapp">
                <uap:DisplayName>My App</uap:DisplayName>
            </uap:Protocol>
        </uap:Extension>
    </Extensions>
```

-----

## <a name="using-webauthenticator"></a>Uso di webauthenticator

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

L'API è costituita principalmente da un singolo metodo `AuthenticateAsync` che accetta due parametri: l'URL da usare per avviare il flusso del browser Web e l'URI che si prevede venga richiamato dal flusso e che l'app è registrata per poter gestire.

Il risultato è un oggetto `WebAuthenticatorResult` che include tutti i parametri di query analizzati dall'URI di callback:

```csharp
var authResult = await WebAuthenticator.AuthenticateAsync(
        new Uri("https://mysite.com/mobileauth/Microsoft"),
        new Uri("myapp://"));

var accessToken = authResult?.AccessToken;
```

L' `WebAuthenticator` API si occupa dell'avvio dell'URL nel browser e dell'attesa fino alla ricezione del callback:

![Flusso di autenticazione Web tipico](images/web-authenticator.png)

Se l'utente annulla il flusso in qualsiasi momento, `null` viene restituito un risultato.

## <a name="platform-differences"></a>Differenze della piattaforma

# <a name="android"></a>[Android](#tab/android)

Le schede personalizzate vengono usate ogni volta che è disponibile; in caso contrario, viene avviato un preventivo per l'URL.

# <a name="ios"></a>[iOS](#tab/ios)

In iOS 12 o versione successiva `ASWebAuthenticationSession` viene usato.  In iOS 11 `SFAuthenticationSession` viene usato.  Nelle versioni precedenti di iOS, `SFSafariViewController` viene usato se disponibile; in caso contrario, viene usato Safari.

# <a name="uwp"></a>[UWP](#tab/uwp)

In UWP, `WebAuthenticationBroker` viene utilizzato se supportato, in caso contrario viene utilizzato il browser del sistema.

-----

## <a name="apple-sign-in"></a>Accesso Apple

Secondo le [linee guida](https://developer.apple.com/app-store/review/guidelines/#sign-in-with-apple)per la revisione di Apple, se l'app usa un servizio di accesso di social networking per l'autenticazione, deve anche offrire l'accesso Apple come opzione.

Per aggiungere l'accesso Apple alle app, è prima di tutto necessario [configurare l'app per l'uso dell'accesso Apple](https://docs.microsoft.com/xamarin/ios/platform/ios13/sign-in).

Per iOS 13 e versioni successive è necessario chiamare il `AppleSignInAuthenticator.AuthenticateAsync()` metodo. In questo modo, l'API di accesso Apple nativa verrà usata dietro le quinte, in modo che gli utenti ottengano la migliore esperienza possibile su questi dispositivi. È possibile scrivere il codice condiviso per usare l'API corretta in fase di esecuzione, come indicato di seguito:

```csharp
var scheme = "..."; // Apple, Microsoft, Google, Facebook, etc.
WebAuthenticatorResult r = null;

if (scheme.Equals("Apple")
    && DeviceInfo.Platform == DevicePlatform.iOS
    && DeviceInfo.Version.Major >= 13)
{
    // Use Native Apple Sign In API's
    r = await AppleSignInAuthenticator.AuthenticateAsync();
}
else
{
    // Web Authentication flow
    var authUrl = new Uri(authenticationUrl + scheme);
    var callbackUrl = new Uri("xamarinessentials://");

    r = await WebAuthenticator.AuthenticateAsync(authUrl, callbackUrl);
}

var accessToken = r?.AccessToken;
```

> [!TIP]
> Per i dispositivi non iOS 13 viene avviato il flusso di autenticazione Web, che può essere usato anche per abilitare l'accesso ad Apple nei dispositivi Android e UWP.
> È possibile accedere all'account iCloud nel simulatore iOS per testare l'accesso ad Apple.

-----

## <a name="aspnet-core-server-back-end"></a>Back-end del server ASP.NET Core

È possibile usare l' `WebAuthenticator` API con qualsiasi servizio back-end Web.  Per usarlo con un'app ASP.NET Core, è prima di tutto necessario configurare l'app Web con i passaggi seguenti:

1. Configurare i [provider di autenticazione sociale esterna](https://docs.microsoft.com/aspnet/core/security/authentication/social/?view=aspnetcore-3.1&tabs=visual-studio) desiderati in un'app Web ASP.NET Core.
2. Impostare lo schema di autenticazione predefinito su `CookieAuthenticationDefaults.AuthenticationScheme` nella `.AddAuthentication()` chiamata.
3. Usare `.AddCookies()` nella chiamata a Startup.cs `.AddAuthentication()` .
4. Tutti i provider devono essere configurati con `.SaveTokens = true;` .

> [!TIP]
> Se si vuole includere l'accesso ad Apple, è possibile usare il `AspNet.Security.OAuth.Apple` pacchetto NuGet.  È possibile visualizzare l' [esempio Startup.cs](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Startup.cs#L32-L60) completo nel repository GitHub di Essentials.

### <a name="add-a-custom-mobile-auth-controller"></a>Aggiungere un controller di autenticazione mobile personalizzato

Con un flusso di autenticazione mobile è in genere consigliabile avviare il flusso direttamente a un provider scelto dall'utente, ad esempio facendo clic sul pulsante "Microsoft" nella schermata di accesso dell'app.  È anche importante poter restituire le informazioni rilevanti all'app in un URI di callback specifico per terminare il flusso di autenticazione.

A tale scopo, usare un controller API personalizzato:

```csharp
[Route("mobileauth")]
[ApiController]
public class AuthController : ControllerBase
{
    const string callbackScheme = "myapp";

    [HttpGet("{scheme}")] // eg: Microsoft, Facebook, Apple, etc
    public async Task Get([FromRoute]string scheme)
    {
        // 1. Initiate authentication flow with the scheme (provider)
        // 2. When the provider calls back to this URL
        //    a. Parse out the result
        //    b. Build the app callback URL
        //    c. Redirect back to the app
    }
}
```

Lo scopo di questo controller è dedurre lo schema (provider) richiesto dall'app e avviare il flusso di autenticazione con il provider di servizi sociali. Quando il provider richiama il back-end Web, il controller analizza il risultato e reindirizza all'URI di callback dell'app con i parametri.

In alcuni casi può essere utile restituire dati come il provider `access_token` all'app, che è possibile eseguire tramite i parametri di query dell'URI di callback. In alternativa, è possibile creare una propria identità sul server e passare di nuovo il token all'app. Le informazioni e le modalità di esecuzione di questa parte sono le tue.

Vedere l' [esempio di controller completo](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Controllers/MobileAuthController.cs) nel repository di Essentials.

-----
## <a name="api"></a>API

- [Codice sorgente webauthenticator](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/WebAuthenticator)
- [Documentazione dell'API webauthenticator](xref:Xamarin.Essentials.WebAuthenticator)
- [Esempio di ASP.NET Core Server](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/)
