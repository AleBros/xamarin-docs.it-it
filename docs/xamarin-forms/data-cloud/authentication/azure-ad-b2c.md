---
title: Autenticare gli utenti con Azure Active Directory B2C
description: Azure Active B2C di Directory consente la gestione di identità cloud per applicazioni rivolte agli utenti web e mobili. Questo articolo illustra come usare Azure Active Directory B2C per integrare la gestione delle identità in un'applicazione per dispositivi mobili con Microsoft Authentication Library.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2019
ms.openlocfilehash: 06f5716c8decb21de39fd46abe734b5fdcd6bd43
ms.sourcegitcommit: 0f78ec17210b915b43ddab75937de8063e472c70
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2019
ms.locfileid: "67417945"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>Autenticare gli utenti con Azure Active Directory B2C

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)

_Azure Active B2C di Directory consente la gestione di identità cloud per applicazioni rivolte agli utenti web e mobili. Questo articolo illustra come usare Azure Active Directory B2C per integrare la gestione delle identità in un'applicazione per dispositivi mobili con Microsoft Authentication Library._

## <a name="overview"></a>Panoramica

Azure Active B2C di Directory (ADB2C) è un servizio di gestione di identità per le applicazioni rivolte agli utenti. Consente agli utenti di accedere all'applicazione usando i propri account social network esistenti o credenziali personalizzate, ad esempio indirizzo di posta elettronica o nome utente e password. Gli account delle credenziali personalizzate sono dette _locale_ account.

Il processo per integrare il servizio di gestione di identità di Azure Active Directory B2C in un'applicazione per dispositivi mobili è come segue:

1. Creare un tenant di Azure Active Directory B2C
1. Registrare l'applicazione per dispositivi mobili con il tenant di Azure Active Directory B2C
1. Creare criteri per l'iscrizione e accesso e i flussi utente password dimenticata
1. Usare Microsoft Authentication Library (MSAL) per avviare un flusso di lavoro di autenticazione con il tenant di Azure Active Directory B2C.

> [!NOTE]
> Azure Active B2C di Directory supporta più provider di identità, tra cui Microsoft, GitHub, Facebook, Twitter e altro ancora. Per altre informazioni sulle funzionalità di Azure Active Directory B2C, vedere [documentazione di Azure Active Directory B2C](/azure/active-directory-b2c/).
>
> Microsoft Authentication Library supporta più architetture applicative e piattaforme. Per informazioni sulle funzionalità di MSAL, vedere [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) su GitHub.

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>Configurare un tenant di Azure Active Directory B2C

Per eseguire il progetto di esempio, è necessario creare un tenant di Azure Active Directory B2C. Per altre informazioni, vedere [creare un tenant di Azure Active Directory B2C nel portale di Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).

Dopo aver creato un tenant, è necessario il **nome del tenant** e **ID tenant** per configurare l'applicazione per dispositivi mobili. L'ID tenant e un nome sono definiti da del dominio generato quando è stato creato l'URL del tenant. Se è l'URL del tenant generata `https://contoso20190410tenant.onmicrosoft.com/` il **ID tenant** viene `contoso20190410tenant.onmicrosoft.com` e il **nome tenant** è `contoso20190410tenant`. Trovare il dominio del tenant nel portale di Azure facendo il **directory e sottoscrizione filtro** nel menu in alto. Lo screenshot seguente mostra la directory di Azure e pulsante di filtro di sottoscrizione e il dominio del tenant:

[![Nome del tenant nella visualizzazione filtro directory e sottoscrizione di Azure](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

Nel progetto di esempio, modificare il **Constants.cs** per impostare le `tenantName` e `tenantId` campi. Il codice seguente viene illustrato come questi valori devono essere impostati se il dominio del tenant è `https://contoso20190410tenant.onmicrosoft.com/`, sostituire questi valori con i valori dal portale:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>Registrare l'applicazione per dispositivi mobili di Azure Active Directory B2C

Un'applicazione per dispositivi mobili deve essere registrata con il tenant prima di connettersi e autenticare gli utenti. Il processo di registrazione viene assegnato un valore univoco **ID applicazione** all'applicazione e una **URL di reindirizzamento** che indirizza le risposte all'applicazione dopo l'autenticazione. Per altre informazioni, vedere [Azure Active Directory B2C: Registrare l'applicazione](/azure/active-directory-b2c/active-directory-b2c-app-registration/). È necessario conoscere il **ID applicazione** assegnato all'applicazione, incluso nell'elenco dopo il nome dell'applicazione nella visualizzazione delle proprietà. Lo screenshot seguente mostra dove trovare l'ID dell'applicazione:

[![ID applicazione nella visualizzazione delle proprietà dell'applicazione Azure](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

Microsoft Authentication Library prevede che il **URL di reindirizzamento** per l'applicazione sia la **ID applicazione** con prefisso con il testo "msal", seguita da un endpoint denominato "auth". Se l'ID dell'applicazione è "1234abcd", l'URL completo dovrebbe essere `msal1234abcd://auth`. Assicurarsi che l'applicazione è abilitato il **Native client** impostazione e creare un **URI di reindirizzamento personalizzato** utilizzando l'ID dell'applicazione, come illustrato nello screenshot seguente:

![URI di reindirizzamento personalizzato nella visualizzazione delle proprietà dell'applicazione Azure](azure-ad-b2c-images/azure-redirect-uri.png)

L'URL verrà usato più avanti in entrambe le Android **ApplicationManifest. XML** e iOS **Info. plist**.

Nel progetto di esempio, modificare il **Constants.cs** per impostare il `clientId` campo le **ID applicazione**. Il codice seguente mostra come questo valore deve essere impostato se l'ID dell'applicazione è `1234abcd`:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>Creare criteri di iscrizione e l'accesso e criteri password dimenticata

Un criterio è un'esperienza che gli utenti passano attraverso per completare un'attività, ad esempio la creazione di un account o reimpostare la password. Un criterio specifica anche il contenuto dei token, che l'applicazione riceve quando l'utente torna dall'esperienza. È necessario configurare i criteri sia per account per l'abbonamento e l'accesso e reimpostazione della password. Azure ha i criteri predefiniti che semplificano la creazione dei criteri comuni. Per altre informazioni, vedere [Azure Active Directory B2C: I criteri predefiniti](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

Dopo aver completato il programma di installazione di criteri, si dovrebbero avere due criteri nel **flussi utente, criteri,** visualizzazione nel portale di Azure. Lo screenshot seguente illustra due criteri configurati nel portale di Azure:

![Consente di visualizzare due criteri configurati per i flussi di utenti di Azure (criteri)](azure-ad-b2c-images/azure-application-policies.png)

Nel progetto di esempio, modificare il **Constants.cs** per impostare le `policySignin` e `policyPassword` campi in modo da riflettere i nomi selezionato durante la configurazione dei criteri:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    static readonly string policySignin = "B2C_1_signupsignin1";
    static readonly string policyPassword = "B2C_1_passwordreset";
    ...
}
```

## <a name="use-the-microsoft-authentication-library-msal-for-authentication"></a>Usare Microsoft Authentication Library (MSAL) per l'autenticazione

Il pacchetto NuGet di Microsoft Authentication Library (MSAL) deve essere aggiunto a condiviso, il progetto .NET Standard e i progetti di piattaforma in una soluzione xamarin. Forms. MSAL include un' `PublicClientApplicationBuilder` classe costruisce un oggetto attenersi alle suddette il `IPublicClientApplication` interfaccia. Usa MSAL `With` clausole per specificare parametri aggiuntivi per i metodi di costruttore e l'autenticazione.

Nel progetto di esempio, il code-behind per **app. XAML** definisce le proprietà statiche denominate `AuthenticationClient` e `UIParent`e crea un'istanza di `AuthenticationClient` oggetto nel costruttore. Il `WithIosKeychainSecurityGroup` clausola fornisce il nome di un gruppo di sicurezza per le applicazioni iOS. Il `WithB2CAuthority` clausola fornisce il valore predefinito **autorità**, o criteri, che verranno usato per autenticare gli utenti. Nell'esempio seguente viene illustrato come creare un'istanza di `PublicClientApplication`:

```csharp
public partial class App : Application
{
    public static IPublicClientApplication AuthenticationClient { get; private set; }

    public static object UIParent { get; set; } = null;

    public App()
    {
        InitializeComponent();

        AuthenticationClient = PublicClientApplicationBuilder.Create(Constants.ClientId)
            .WithIosKeychainSecurityGroup(Constants.IosKeychainSecurityGroups)
            .WithB2CAuthority(Constants.AuthoritySignin)
            .Build();

        MainPage = new NavigationPage(new LoginPage());
    }

    ...
```

Il `OnAppearing` gestore dell'evento nel **LoginPage.xaml.cs** code-behind chiamate `AcquireTokenSilentAsync` ad aggiornare il token di autenticazione per gli utenti che hanno effettuato l'accesso prima. Il processo di autenticazione reindirizza il `LogoutPage` se ha esito positivo e non esegue alcuna operazione in caso di errore. L'esempio seguente illustra il processo di riautenticazione invisibile all'utente in `OnAppearing`:

```csharp
public partial class LoginPage : ContentPage
{
    ...

    protected override async void OnAppearing()
    {
        try
        {
            // Look for existing account
            IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

            AuthenticationResult result = await App.AuthenticationClient
                .AcquireTokenSilent(Constants.Scopes, accounts.FirstOrDefault())
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch
        {
            // Do nothing - the user isn't logged in
        }
        base.OnAppearing();
    }

    ...
}
```

Il `OnLoginButtonClicked` gestore dell'evento, generati quando si fa clic sul pulsante di accesso, le chiamate `AcquireTokenAsync`. La libreria MSAL automaticamente viene aperto il browser del dispositivo per dispositivi mobili e consente di passare alla pagina di accesso. L'URL di accesso, chiamato un' **autorità**, è una combinazione del nome del tenant e i criteri definiti nella **Constants.cs** file. Se l'utente sceglie l'opzione password dimenticata vengono restituiti all'app con un'eccezione che viene avviata l'esperienza di password dimenticata. L'esempio seguente illustra il processo di autenticazione:

```csharp
public partial class LoginPage : ContentPage
{
    ...

    async void OnLoginButtonClicked(object sender, EventArgs e)
    {
        AuthenticationResult result;
        try
        {
            result = await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .ExecuteAsync();
    
            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch (MsalException ex)
        {
            if (ex.Message != null && ex.Message.Contains("AADB2C90118"))
            {
                result = await OnForgotPassword();
                await Navigation.PushAsync(new LogoutPage(result));
            }
            else if (ex.ErrorCode != "authentication_canceled")
            {
                await DisplayAlert("An error has occurred", "Exception message: " + ex.Message, "Dismiss");
            }
        }
    }

    ...
}
```

Il `OnForgotPassword` metodo è simile al processo di accesso, ma implementa un criterio personalizzato. `OnForgotPassword` Usa un altro overload del `AcquireTokenAsync`, che consente di fornire uno specifico **autorità**. Nell'esempio seguente illustra come specificare una classe personalizzata **autorità** durante l'acquisizione di un token:

```csharp
public partial class LoginPage : ContentPage
{
    ...
    async Task<AuthenticationResult> OnForgotPassword()
    {
        try
        {
            return await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .WithB2CAuthority(Constants.AuthorityPasswordReset)
                .ExecuteAsync();
        }
        catch (MsalException)
        {
            // Do nothing - ErrorCode will be displayed in OnLoginButtonClicked
            return null;
        }
    }
}
```

L'ultima parte di autenticazione è il processo di disconnessione. Il `OnLogoutButtonClicked` metodo viene chiamato quando l'utente preme il pulsante di disconnessione. Esegue il ciclo attraverso tutti gli account e garantisce che i token sono stati invalidati. L'esempio seguente viene illustrata l'implementazione di disconnessione:

```csharp
public partial class LogoutPage : ContentPage
{
    ...
    async void OnLogoutButtonClicked(object sender, EventArgs e)
    {
        IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

        while (accounts.Any())
        {
            await App.AuthenticationClient.RemoveAsync(accounts.First());
            accounts = await App.AuthenticationClient.GetAccountsAsync();
        }

        await Navigation.PopAsync();
    }
}
```

### <a name="ios"></a>iOS

In iOS, lo schema URL personalizzato che è stato registrato con Azure Active Directory B2C deve essere registrato nella **Info. plist**. MSAL prevede che lo schema URL per aderire a un modello specifico, descritto in precedenza in [registrare l'applicazione per dispositivi mobili di Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). Lo screenshot seguente illustra lo schema URL personalizzato nella **Info. plist**.

!["La registrazione di uno schema URL personalizzato in iOS"](azure-ad-b2c-images/customurl-ios.png)

MSAL richiede inoltre i diritti di Keychain su iOS, registrato nel **Entitilements.plist**, come illustrato nello screenshot seguente:

!["Impostazione dei diritti delle applicazioni su iOS"](azure-ad-b2c-images/entitlements-ios.png)

Al termine di richiesta di autorizzazione, Azure Active Directory B2C reindirizza all'URL di reindirizzamento registrato. Lo schema URL personalizzato comporta iOS l'avvio dell'applicazione per dispositivi mobili e passando in URL come parametro di avvio, in cui viene elaborato dal `OpenUrl` eseguire l'override dell'applicazione `AppDelegate` classe e restituisce il controllo dell'esperienza per MSAL. Il `OpenUrl` implementazione è illustrata nell'esempio di codice seguente:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        ...
        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
            return base.OpenUrl(app, url, options);
        }
    }
}
```

### <a name="android"></a>Android

In Android, lo schema URL personalizzato che è stato registrato con Azure Active Directory B2C deve essere registrato nel **androidmanifest. XML**. MSAL prevede che lo schema URL per aderire a un modello specifico, descritto in precedenza in [registrare l'applicazione per dispositivi mobili di Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). L'esempio seguente illustra lo schema URL personalizzato nella **androidmanifest. XML**.

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.xamarin.adb2cauthorization">
  <uses-sdk android:minSdkVersion="15" />
  <application android:label="ADB2CAuthorization">
    <activity android:name="microsoft.identity.client.BrowserTabActivity">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />"
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

Il `MainActivity` classe deve essere modificata per specificare il `UIParent` oggetto all'applicazione durante il `OnCreate` chiamare. Al termine di richiesta di autorizzazione, Azure Active Directory B2C reindirizza sullo schema URL registrato dal **androidmanifest. XML**. Lo schema URI registrato risultati nell'Android che chiama il `OnActivityResult` metodo con l'URL come un parametro di avvio, in cui viene elaborato dal `SetAuthenticationContinuationEventArgs` (metodo).

```csharp
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        LoadApplication(new App());
        App.UIParent = this;
    }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
    }
}
```

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Non è necessaria alcuna impostazione aggiuntiva per usare MSAL sulla piattaforma Windows universale

## <a name="run-the-project"></a>Eseguire il progetto

Eseguire l'applicazione in un dispositivo fisico o virtuale. Se si tocca il **account di accesso** pulsante deve aprire il browser e passare a una pagina in cui è possibile accedere o creare un account. Dopo aver completato il processo di accesso, devono essere restituiti alla pagina di disconnessione dell'applicazione. Lo screenshot seguente mostra l'accesso dell'utente nella schermata in esecuzione su Android e iOS:

!["Azure ADB2C schermata di accesso in Android e iOS"](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>Collegamenti correlati

- [AzureADB2CAuth (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentazione di Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
