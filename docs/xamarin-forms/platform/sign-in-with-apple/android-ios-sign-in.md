---
title: "usare l'accesso con Apple per Xamarin.Forms " Descrizione: "informazioni su come implementare l'accesso con Apple nelle Xamarin.Forms applicazioni per dispositivi mobili."
ms. prod: Novell MS. AssetID: 2E47E7F2-93D4-4CA3-9E66-247466D25E4D ms. Technology: Novell-Forms Author: davidortinau ms. Author: daortin ms. Date: 09/10/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="use-sign-in-with-apple-in-xamarinforms"></a>Usare l'accesso con Apple inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/signinwithapple/)

L'accesso con Apple è per tutte le nuove applicazioni in iOS 13 che usano servizi di autenticazione di terze parti. I dettagli di implementazione tra iOS e Android sono piuttosto diversi. Questa guida illustra come eseguire questa operazione oggi in Xamarin.Forms .

In questa guida e nell'esempio vengono usati servizi di piattaforma specifici per gestire l'accesso con Apple:

- Android con un servizio Web generico che comunica con funzioni di Azure con OpenID/OpenAuth
- iOS usa l'API nativa per l'autenticazione in iOS 13 e esegue il fallback a un servizio Web generico per iOS 12 e versioni precedenti

## <a name="a-sample-apple-sign-in-flow"></a>Un flusso di accesso Apple di esempio

Questo esempio offre un'implementazione dogmatica per il funzionamento dell'accesso Apple nell' Xamarin.Forms app.

Per semplificare il flusso di autenticazione vengono usate due funzioni di Azure:

1. `applesignin_auth`: Genera l'URL di autorizzazione per l'accesso Apple e lo reindirizza.  Questa operazione viene eseguita sul lato server invece che sull'app per dispositivi mobili, quindi è possibile memorizzare nella cache `state` e convalidarla quando i server Apple inviano un callback.
2. `applesignin_callback`: Gestisce il callback POST da Apple e scambia in modo sicuro il codice di autorizzazione per un token di accesso e un token ID.  Infine, reindirizza nuovamente allo schema URI dell'app, passando i token in un frammento di URL.

L'app per dispositivi mobili si registra per gestire lo schema URI personalizzato selezionato (in questo caso `xamarinformsapplesignin://` ), in modo che la `applesignin_callback` funzione possa inoltrare nuovamente i token.

Quando l'utente avvia l'autenticazione, si verificano i passaggi seguenti:

1. L'app per dispositivi mobili genera un `nonce` `state` valore e e li passa alla `applesignin_auth` funzione di Azure.
2. La `applesignin_auth` funzione di Azure genera un URL di autorizzazione di accesso Apple (usando il `state` e fornito `nonce` ) e reindirizza il browser dell'app per dispositivi mobili.
3. L'utente immette le proprie credenziali in modo sicuro nella pagina di autorizzazione dell'accesso Apple ospitata nei server Apple.
4. Al termine del flusso di accesso Apple nei server Apple, Apple reindirizza a `redirect_uri` che sarà la `applesignin_callback` funzione di Azure.
5. La richiesta inviata da Apple alla `applesignin_callback` funzione viene convalidata per garantire `state` che venga restituito il valore corretto e che le attestazioni del token ID siano valide.
6. La `applesignin_callback` funzione di Azure scambia il `code` inviato ad esso da Apple, per un _token di accesso_, un _token di aggiornamento_e un _token ID_ , che contiene attestazioni relative all'ID utente, al nome e al messaggio di posta elettronica.
7. La `applesignin_callback` funzione di Azure reindirizza infine allo schema URI dell'app ( `xamarinformsapplesignin://` ) che aggiunge un frammento URI con i token (ad esempio `xamarinformsapplesignin://#access_token=...&refresh_token=...&id_token=...` ).
8. L'app per dispositivi mobili analizza il frammento URI in un `AppleAccount` e convalida l' `nonce` attestazione ricevuta corrisponde all'oggetto `nonce` generato all'inizio del flusso.
9. L'app per dispositivi mobili è ora autenticata.

## <a name="azure-functions"></a>Funzioni di Azure

Questo esempio usa funzioni di Azure. In alternativa, un controller ASP.NET Core o una soluzione server Web simile potrebbe offrire la stessa funzionalità.

### <a name="configuration"></a>Configurazione

Quando si usano funzioni di Azure, è necessario configurare diverse impostazioni dell'app:

- `APPLE_SIGNIN_KEY_ID`: Questa è la `KeyId` prima.
- `APPLE_SIGNIN_TEAM_ID`-Si tratta in genere dell' _ID del team_ trovato nel profilo di [appartenenza](https://developer.apple.com/account/#/membership)
- `APPLE_SIGNIN_SERVER_ID`: Si tratta dell'oggetto `ServerId` di precedente.  *Non* si tratta dell' _ID bundle_dell'app, ma piuttosto dell' *identificatore* dell' *ID Servizi* creato.
- `APPLE_SIGNIN_APP_CALLBACK_URI`: Schema URI personalizzato di cui si vuole eseguire il reindirizzamento all'app.  In questo esempio `xamarinformsapplesignin://` viene usato.
- `APPLE_SIGNIN_REDIRECT_URI`: *URL di reindirizzamento* configurato durante la creazione dell' *ID Servizi* nella sezione *di* configurazione dell'accesso ad Apple.  Per eseguire il test, potrebbe avere un aspetto simile al seguente:`http://local.test:7071/api/applesignin_callback`
- `APPLE_SIGNIN_P8_KEY`: Il contenuto di testo del `.p8` file, con tutte le `\n` nuove righe rimosse, quindi è una stringa lunga

### <a name="security-considerations"></a>Considerazioni relative alla sicurezza

**Non archiviare mai** la chiave P8 all'interno del codice dell'applicazione. Il codice dell'applicazione è facile da scaricare e disassemblare. 

Si consiglia inoltre di utilizzare per `WebView` ospitare il flusso di autenticazione e di intercettare gli eventi di spostamento URL per ottenere il codice di autorizzazione. Al momento non è attualmente disponibile un metodo completamente sicuro per gestire l'accesso con Apple su dispositivi non iOS13 + senza ospitare codice in un server per gestire lo scambio di token. È consigliabile ospitare il codice di generazione dell'URL di autorizzazione in un server in modo che sia possibile memorizzare nella cache lo stato e convalidarlo quando Apple rilascia un callback POST al server.

## <a name="a-cross-platform-sign-in-service"></a>Un servizio di accesso multipiattaforma

Usando Xamarin.Forms DependencyService, è possibile creare servizi di autenticazione distinti che usano i servizi della piattaforma in iOS e un servizio Web generico per Android e altre piattaforme non iOS basate su un'interfaccia condivisa.

```csharp
public interface IAppleSignInService
{
    bool Callback(string url);

    Task<AppleAccount> SignInAsync();
}
```

In iOS vengono usate le API native:

```csharp
public class AppleSignInServiceiOS : IAppleSignInService
{
#if __IOS__13
    AuthManager authManager;
#endif

    bool Is13 => UIDevice.CurrentDevice.CheckSystemVersion(13, 0);
    WebAppleSignInService webSignInService;

    public AppleSignInServiceiOS()
    {
        if (!Is13)
            webSignInService = new WebAppleSignInService();
    }

    public async Task<AppleAccount> SignInAsync()
    {
        // Fallback to web for older iOS versions
        if (!Is13)
            return await webSignInService.SignInAsync();

        AppleAccount appleAccount = default;

#if __IOS__13
        var provider = new ASAuthorizationAppleIdProvider();
        var req = provider.CreateRequest();

        authManager = new AuthManager(UIApplication.SharedApplication.KeyWindow);

        req.RequestedScopes = new[] { ASAuthorizationScope.FullName, ASAuthorizationScope.Email };
        var controller = new ASAuthorizationController(new[] { req });

        controller.Delegate = authManager;
        controller.PresentationContextProvider = authManager;

        controller.PerformRequests();

        var creds = await authManager.Credentials;

        if (creds == null)
            return null;

        appleAccount = new AppleAccount();
        appleAccount.IdToken = JwtToken.Decode(new NSString(creds.IdentityToken, NSStringEncoding.UTF8).ToString());
        appleAccount.Email = creds.Email;
        appleAccount.UserId = creds.User;
        appleAccount.Name = NSPersonNameComponentsFormatter.GetLocalizedString(creds.FullName, NSPersonNameComponentsFormatterStyle.Default, NSPersonNameComponentsFormatterOptions.Phonetic);
        appleAccount.RealUserStatus = creds.RealUserStatus.ToString();
#endif

        return appleAccount;
    }

    public bool Callback(string url) => true;
}

#if __IOS__13
class AuthManager : NSObject, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding
{
    public Task<ASAuthorizationAppleIdCredential> Credentials
        => tcsCredential?.Task;

    TaskCompletionSource<ASAuthorizationAppleIdCredential> tcsCredential;

    UIWindow presentingAnchor;

    public AuthManager(UIWindow presentingWindow)
    {
        tcsCredential = new TaskCompletionSource<ASAuthorizationAppleIdCredential>();
        presentingAnchor = presentingWindow;
    }

    public UIWindow GetPresentationAnchor(ASAuthorizationController controller)
        => presentingAnchor;

    [Export("authorizationController:didCompleteWithAuthorization:")]
    public void DidComplete(ASAuthorizationController controller, ASAuthorization authorization)
    {
        var creds = authorization.GetCredential<ASAuthorizationAppleIdCredential>();
        tcsCredential?.TrySetResult(creds);
    }

    [Export("authorizationController:didCompleteWithError:")]
    public void DidComplete(ASAuthorizationController controller, NSError error)
        => tcsCredential?.TrySetException(new Exception(error.LocalizedDescription));
}
#endif
```

Il flag `__IOS__13` di compilazione viene usato per fornire supporto per iOS 13 e per le versioni legacy che fanno il fallback al servizio Web generico.

In Android viene usato il servizio Web generico con funzioni di Azure:

```csharp
public class WebAppleSignInService : IAppleSignInService
{
    // IMPORTANT: This is what you register each native platform's url handler to be
    public const string CallbackUriScheme = "xamarinformsapplesignin";
    public const string InitialAuthUrl = "http://local.test:7071/api/applesignin_auth";

    string currentState;
    string currentNonce;

    TaskCompletionSource<AppleAccount> tcsAccount = null;

    public bool Callback(string url)
    {
        // Only handle the url with our callback uri scheme
        if (!url.StartsWith(CallbackUriScheme + "://"))
            return false;

        // Ensure we have a task waiting
        if (tcsAccount != null && !tcsAccount.Task.IsCompleted)
        {
            try
            {
                // Parse the account from the url the app opened with
                var account = AppleAccount.FromUrl(url);

                // IMPORTANT: Validate the nonce returned is the same as our originating request!!
                if (!account.IdToken.Nonce.Equals(currentNonce))
                    tcsAccount.TrySetException(new InvalidOperationException("Invalid or non-matching nonce returned"));

                // Set our account result
                tcsAccount.TrySetResult(account);
            }
            catch (Exception ex)
            {
                tcsAccount.TrySetException(ex);
            }
        }

        tcsAccount.TrySetResult(null);
        return false;
    }

    public async Task<AppleAccount> SignInAsync()
    {
        tcsAccount = new TaskCompletionSource<AppleAccount>();

        // Generate state and nonce which the server will use to initial the auth
        // with Apple.  The nonce should flow all the way back to us when our function
        // redirects to our app
        currentState = Util.GenerateState();
        currentNonce = Util.GenerateNonce();

        // Start the auth request on our function (which will redirect to apple)
        // inside a browser (either SFSafariViewController, Chrome Custom Tabs, or native browser)
        await Xamarin.Essentials.Browser.OpenAsync($"{InitialAuthUrl}?&state={currentState}&nonce={currentNonce}",
            Xamarin.Essentials.BrowserLaunchMode.SystemPreferred);

        return await tcsAccount.Task;
    }
}
```

## <a name="summary"></a>Summary

Questo articolo descrive i passaggi necessari per configurare l'accesso con Apple per l'uso nelle Xamarin.Forms applicazioni.

## <a name="related-links"></a>Collegamenti correlati

- [XamarinFormsAppleSignIn (esempio)](https://github.com/Redth/Xamarin.AppleSignIn.Sample)
- [Accedere con le linee guida di Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
