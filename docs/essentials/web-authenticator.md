---
title: 'Xamarin.Essentials: Web Authenticator'
description: Questo documento descrive la classe WebAuthenticator in Xamarin.Essentials, che consente di avviare flussi di autenticazione basati su browser che ascoltano un callback all'app.
ms.assetid: 3D95371E-5D59-440E-8D31-F3C04E493DC1
author: redth
ms.author: jodick
ms.date: 03/26/2020
ms.openlocfilehash: b090ea8491afccb7078de8333a44a4888819a46a
ms.sourcegitcommit: ddd2cb3a102df339bb269380cb2c0617dbb1acb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688276"
---
# <a name="xamarinessentials-web-authenticator"></a>Xamarin.Essentials: Web Authenticator

La classe **WebAuthenticator** consente di avviare flussi basati su browser che intercettano un callback a un URL specifico registrato nell'app.

## <a name="overview"></a>Panoramica

Molte app richiedono l'aggiunta dell'autenticazione utente e questo spesso significa consentire agli utenti di accedere ai propri account Microsoft, Facebook, Google e ora Apple Sign In esistenti.

[Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) offre un'eccellente soluzione chiavi in mente per l'aggiunta dell'autenticazione all'app. C'è anche il supporto per le applicazioni Xamarin nel loro pacchetto NuGet client.

Se si è interessati a utilizzare il proprio servizio Web per l'autenticazione, è possibile utilizzare **WebAuthenticator** per implementare la funzionalità lato client.

## <a name="why-use-a-server-back-end"></a>Perché utilizzare un server back-end?

Molti provider di autenticazione sono stati spostati solo offrendo flussi di autenticazione espliciti o a due vie per garantire una maggiore sicurezza. Ciò significa che è necessario un _'segreto client'_ dal provider per completare il flusso di autenticazione. Sfortunatamente, le app per dispositivi mobili non sono un luogo ideale per archiviare segreti e tutto ciò che viene archiviato nel codice di un'app per dispositivi mobili, nei file binari o in altro modo è generalmente considerato non sicuro.

La procedura consigliata consiste nell'usare un back-end Web come livello intermedio tra l'app per dispositivi mobili e il provider di autenticazione.

> [!IMPORTANT]
> È consigliabile non utilizzare librerie e modelli di autenticazione solo per dispositivi mobili meno recenti che non sfruttano un back-end Web nel flusso di autenticazione a causa della mancanza intrinseca di sicurezza per l'archiviazione dei segreti client.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

Per accedere alla funzionalità **WebAuthenticator** è necessaria la seguente configurazione specifica della piattaforma.

# <a name="android"></a>[Android](#tab/android)

Android richiede una configurazione del filtro intento per gestire l'URI di callback. Ciò è possibile ottenere facilmente `WebAuthenticatorCallbackActivity` una sottoclasse della classe:

> [!NOTE]
> È consigliabile implementare i collegamenti alle [app Android](https://developer.android.com/training/app-links/) per gestire l'URI di callback e assicurarsi che l'applicazione sia l'unica che può registrarsi per gestire l'URI di callback.

```csharp
const string CALLBACK_SCHEME = "myapp";

[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop)]
[IntentFilter(new[] { Intent.ActionView },
    Categories = new[] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataScheme = CALLBACK_SCHEME)]
public class WebAuthenticationCallbackActivity : Xamarin.Essentials.WebAuthenticatorCallbackActivity
{
}
```

Sarà inoltre necessario richiamare in Essentials `OnResume` dalla `MainActivity`sostituzione nel :

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume();
}
```

# <a name="ios"></a>[iOS](#tab/ios)

In iOS dovrai aggiungere il modello URI di callback dell'app a Info.plist.

> [!NOTE]
> È consigliabile usare [Universal App Links](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content) per registrare l'URI di callback dell'app come procedura consigliata.

Sarà inoltre necessario eseguire `AppDelegate`l'override del `OpenUrl` metodo per chiamare Essentials:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    if (Xamarin.Essentials.Platform.OpenUrl(app, url, options))
        return true;

    return base.OpenUrl(app, url, options);
}
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Per la piattaforma UWP, è necessario dichiarare `Package.appxmanifest` l'URI di callback nel file:For UWP, you'll need to declare your callback URI in your file:

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

## <a name="using-webauthenticator"></a>Utilizzo di WebAuthenticatorUsing WebAuthenticator

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

L'API è costituita `AuthenticateAsync` principalmente da un singolo metodo che accetta due parametri: l'URL che deve essere usato per avviare il flusso del browser Web e l'URI che si prevede che il flusso richiami e che l'app è registrata sia in grado di gestire.

Il risultato `WebAuthenticatorResult` è un che include tutti i parametri di query analizzati dall'URI di callback:The result is a which includes any query parameters parsed from the callback URI:

```csharp
var authResult = await WebAuthenticator.AuthenticateAsync(
        new Uri("https://mysite.com/mobileauth/Microsoft"),
        new Uri("myapp://"));

var accessToken = authResult?.AccessToken;
```

L'API `WebAuthenticator` si occupa di avviare l'URL nel browser e attendere la ricezione del callback:

![Flusso di autenticazione Web tipico](images/web-authenticator.png)

Se l'utente annulla il flusso `null` in qualsiasi momento, viene restituito un risultato.

## <a name="platform-differences"></a>Differenze di piattaforma

# <a name="android"></a>[Android](#tab/android)

Le schede personalizzate vengono utilizzate quando disponibili, altrimenti viene avviato un intento per l'URL.

# <a name="ios"></a>[iOS](#tab/ios)

In iOS 12 `ASWebAuthenticationSession` o versione successiva, viene utilizzato.  In iOS 11, `SFAuthenticationSession` viene utilizzato.  Nelle versioni precedenti di `SFSafariViewController` iOS, viene utilizzato se disponibile, in caso contrario viene utilizzato Safari.

# <a name="uwp"></a>[UWP](#tab/uwp)

Nella piattaforma UWP, l'oggetto `WebAuthenticationBroker` viene utilizzato se supportato, altrimenti viene utilizzato il browser di sistema.

-----

## <a name="apple-sign-in"></a>Accesso Apple

Secondo [le linee guida](https://developer.apple.com/app-store/review/guidelines/#sign-in-with-apple)di Apple, se la tua app utilizza un servizio di accesso social per l'autenticazione, deve anche offrire apple Sign In come opzione.

Per aggiungere Apple Sign In alle tue app, devi prima [configurare l'app per l'uso](https://docs.microsoft.com/xamarin/ios/platform/ios13/sign-in)di Apple Sign In .

Per iOS 13 e versioni successive `AppleSignInAuthenticator.AuthenticateAsync()` è consigliabile chiamare il metodo . Questo userà le API native di Apple Sign in sotto il cofano in modo che gli utenti ottenete la migliore esperienza possibile su questi dispositivi. È possibile scrivere il codice condiviso per usare l'API corretta in fase di esecuzione in questo modo:You can write your shared code to use the right API at runtime like this:

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
> Per i dispositivi non iOS 13 questo avvierà il flusso di autenticazione web, che può essere utilizzato anche per abilitare l'accesso Apple sui dispositivi Android e UWP.
> Puoi accedere al tuo account iCloud sul tuo simulatore iOS per testare apple Sign In.

-----

## <a name="aspnet-core-server-back-end"></a>ASP.NET back-end del server principale

È possibile usare l'API `WebAuthenticator` con qualsiasi servizio web back-end.  Per usarla con un'app ASP.NET core, devi prima configurare l'app Web con i passaggi seguenti:

1. Impostare i provider di [autenticazione sociale esterni](https://docs.microsoft.com/aspnet/core/security/authentication/social/?view=aspnetcore-3.1&tabs=visual-studio) desiderati in un'app Web ASP.NET Core.
2. Impostare lo schema `CookieAuthenticationDefaults.AuthenticationScheme` di `.AddAuthentication()` autenticazione predefinito su nella chiamata.
3. Utilizzare `.AddCookies()` nella `.AddAuthentication()` chiamata Startup.cs.
4. Tutti i provider `.SaveTokens = true;`devono essere configurati con .

> [!TIP]
> Se vuoi includere Apple Sign In, puoi `AspNet.Security.OAuth.Apple` usare il pacchetto NuGet.  È possibile visualizzare l'esempio di Startup.cs completo nel repository GitHub di Essentials.You can view the full [Startup.cs sample](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Startup.cs#L32-L60) in the Essentials GitHub repository.

### <a name="add-a-custom-mobile-auth-controller"></a>Aggiungere un controller di autenticazione mobile personalizzatoAdd a custom mobile auth controller

Con un flusso di autenticazione mobile è in genere opportuno avviare il flusso direttamente a un provider scelto dall'utente (ad esempio facendo clic su un pulsante "Microsoft" nella schermata di accesso dell'app).  È inoltre importante essere in grado di restituire informazioni rilevanti all'app in un URI di callback specifico per terminare il flusso di autenticazione.

A tale scopo, usare un controller API personalizzato:To achieve this, use a custom API Controller:

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

Lo scopo di questo controller è dedurre lo schema (provider) richiesto dall'app e avviare il flusso di autenticazione con il provider di social network. Quando il provider richiama il back-end Web, il controller analizza il risultato e reindirizza all'URI di callback dell'app con parametri.

In alcuni casi può essere necessario restituire `access_token` dati come la parte del provider all'app che è possibile eseguire tramite i parametri di query dell'URI di callback. In alternativa, è possibile creare invece la propria identità sul server e passare nuovamente il proprio token all'app. Cosa e come si fa questa parte è a voi!

Estrarre [l'esempio di controller completo](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Controllers/MobileAuthController.cs) nel repository di Essentials.

-----
## <a name="api"></a>API

- [Codice sorgente WebAuthenticator](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/WebAuthenticator)
- [Documentazione dell'API WebAuthenticator](xref:Xamarin.Essentials.WebAuthenticator)
- [Esempio di server ASP.NET Core](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/)
