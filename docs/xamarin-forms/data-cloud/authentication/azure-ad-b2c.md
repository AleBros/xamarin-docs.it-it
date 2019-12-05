---
title: Autenticare gli utenti con Azure Active Directory B2C
description: Azure Active Directory B2C offre la gestione delle identità cloud per applicazioni Web e per dispositivi mobili rivolte agli utenti. Questo articolo illustra come usare Azure Active Directory B2C per integrare la gestione delle identità in un'applicazione per dispositivi mobili con Microsoft Authentication Library.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2019
ms.openlocfilehash: c1df3adfa67a363609b397731ed298155d7531be
ms.sourcegitcommit: 483e0ab0e9f30382219084c0345519f1025169b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74809023"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>Autenticare gli utenti con Azure Active Directory B2C

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)

_Azure Active Directory B2C offre la gestione delle identità cloud per applicazioni Web e per dispositivi mobili rivolte agli utenti. Questo articolo illustra come usare Azure Active Directory B2C per integrare la gestione delle identità in un'applicazione per dispositivi mobili con Microsoft Authentication Library._

## <a name="overview"></a>Panoramica di

Azure Active Directory B2C (ADB2C) è un servizio di gestione delle identità per le applicazioni rivolte agli utenti. Consente agli utenti di accedere all'applicazione usando gli account di social networking esistenti o credenziali personalizzate, ad esempio posta elettronica o nome utente e password. Gli account delle credenziali personalizzate sono denominati account _locali_ .

Il processo per integrare il servizio di gestione di identità di Azure Active Directory B2C in un'applicazione per dispositivi mobili è come segue:

1. Creare un tenant di Azure Active Directory B2C
1. Registrare l'applicazione per dispositivi mobili con il tenant di Azure Active Directory B2C
1. Creare criteri per l'iscrizione e l'accesso e per i flussi utente con password dimenticata
1. Usare Microsoft Authentication Library (MSAL) per avviare un flusso di lavoro di autenticazione con il tenant di Azure Active Directory B2C.

> [!NOTE]
> Azure Active Directory B2C supporta più provider di identità, tra cui Microsoft, GitHub, Facebook, Twitter e altro ancora. Per ulteriori informazioni sulle funzionalità di Azure Active Directory B2C, vedere [Azure Active Directory B2C documentazione](/azure/active-directory-b2c/).
>
> Microsoft Authentication Library supporta più architetture e piattaforme di applicazione. Per informazioni sulle funzionalità di MSAL, vedere [Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) su GitHub.

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>Configurare un tenant di Azure Active Directory B2C

Per eseguire il progetto di esempio, è necessario creare un tenant di Azure Active Directory B2C. Per altre informazioni, vedere [creare un tenant di Azure Active Directory B2C nel portale di Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).

Dopo aver creato un tenant, sarà necessario il **nome del tenant** e l' **ID tenant** per configurare l'applicazione per dispositivi mobili. L'ID e il nome del tenant sono definiti dal dominio generato al momento della creazione dell'URL del tenant. Se l'URL del tenant generato è `https://contoso20190410tenant.onmicrosoft.com/` l' **ID tenant** è `contoso20190410tenant.onmicrosoft.com` e il **nome del tenant** è `contoso20190410tenant`. Trovare il dominio tenant nella portale di Azure facendo clic sul **filtro directory e sottoscrizione** nel menu in alto. La schermata seguente mostra il pulsante di filtro della sottoscrizione e della directory di Azure e il dominio del tenant:

[![nome del tenant nella visualizzazione filtro della sottoscrizione e della directory di Azure](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

Nel progetto di esempio, modificare il file **Constants.cs** per impostare i campi `tenantName` e `tenantId`. Il codice seguente illustra come impostare questi valori se il dominio del tenant è `https://contoso20190410tenant.onmicrosoft.com/`, sostituire questi valori con i valori del portale:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>Registrare l'applicazione per dispositivi mobili con Azure Active Directory B2C

Un'applicazione per dispositivi mobili deve essere registrata con il tenant per potersi connettere e autenticare gli utenti. Il processo di registrazione assegna un **ID applicazione** univoco all'applicazione e un URL di **Reindirizzamento** che reindirizza le risposte all'applicazione dopo l'autenticazione. Per altre informazioni, vedere [Azure Active Directory B2C: registrare l'applicazione](/azure/active-directory-b2c/active-directory-b2c-app-registration/). È necessario essere a conoscenza dell' **ID applicazione** assegnato all'applicazione, che viene elencato dopo il nome dell'applicazione nella visualizzazione delle proprietà. Lo screenshot seguente mostra dove trovare l'ID applicazione:

[![ID applicazione nella visualizzazione delle proprietà dell'applicazione Azure](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

Microsoft Authentication Library prevede che l' **URL di reindirizzamento** dell'applicazione sia l' **ID applicazione** preceduto dal testo "MSAL" e quindi da un endpoint denominato "auth". Se l'ID applicazione è "1234abcd", l'URL completo deve essere `msal1234abcd://auth`. Assicurarsi che l'applicazione abbia abilitato l'impostazione **Native Client** e creare un **URI di reindirizzamento personalizzato** usando l'ID applicazione, come illustrato nello screenshot seguente:

![URI di reindirizzamento personalizzato nella visualizzazione delle proprietà dell'applicazione Azure](azure-ad-b2c-images/azure-redirect-uri.png)

L'URL verrà usato in un secondo momento sia in Android **ApplicationManifest. XML** che in iOS **info. plist**.

Nel progetto di esempio, modificare il file **Constants.cs** per impostare il campo `clientId` sull' **ID applicazione**. Il codice seguente illustra come impostare questo valore se l'ID applicazione è `1234abcd`:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>Creare criteri di iscrizione e accesso e criteri password dimenticati

Un criterio è un'esperienza che gli utenti passano per completare un'attività, ad esempio la creazione di un account o la reimpostazione di una password. Un criterio specifica anche il contenuto dei token ricevuti dall'applicazione quando l'utente torna dall'esperienza. È necessario configurare i criteri per l'iscrizione e l'accesso all'account e la reimpostazione della password. Azure include criteri predefiniti che semplificano la creazione di criteri comuni. Per altre informazioni, vedere [Azure Active Directory B2C: criteri predefiniti](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

Dopo aver completato la configurazione dei criteri, è necessario disporre di due criteri nella visualizzazione **flussi utente (criteri)** nella portale di Azure. Lo screenshot seguente illustra due criteri configurati nella portale di Azure:

![Due criteri configurati nella visualizzazione flussi utente di Azure (criteri)](azure-ad-b2c-images/azure-application-policies.png)

Nel progetto di esempio, modificare il file **Constants.cs** per impostare i campi `policySignin` e `policyPassword` in modo che corrispondano ai nomi scelti durante la configurazione dei criteri:

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

Il pacchetto NuGet Microsoft Authentication Library (MSAL) deve essere aggiunto al progetto condiviso .NET Standard e ai progetti di piattaforma in una soluzione Novell. Forms. MSAL include una classe `PublicClientApplicationBuilder` che costruisce un oggetto che aderisce all'interfaccia `IPublicClientApplication`. MSAL utilizza le clausole `With` per fornire parametri aggiuntivi al costruttore e ai metodi di autenticazione.

Nel progetto di esempio, il code-behind per **app. XAML** definisce proprietà statiche denominate `AuthenticationClient` e `UIParent`e crea un'istanza dell'oggetto `AuthenticationClient` nel costruttore. La clausola `WithIosKeychainSecurityGroup` fornisce un nome di gruppo di sicurezza per le applicazioni iOS. La clausola `WithB2CAuthority` fornisce l' **autorità**o i criteri predefiniti che verranno usati per autenticare gli utenti. La clausola `WithRedirectUri` indica all'istanza di hub di notifica di Azure l'URI di Reindirizzamento da usare se sono specificati più URI. Nell'esempio seguente viene illustrato come creare un'istanza della `PublicClientApplication`:

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
            .WithRedirectUri($"msal{Constants.ClientId}://auth")
            .Build();

        MainPage = new NavigationPage(new LoginPage());
    }

    ...
```

> [!NOTE]
> Se per l'istanza di hub di notifica di Azure è stato definito un solo URI di reindirizzamento, l'istanza di `AuthenticationClient` può funzionare senza specificare l'URI di reindirizzamento con la clausola `WithRedirectUri`. Tuttavia, è sempre necessario specificare questo valore nel caso in cui la configurazione di Azure si espande per supportare altri client o metodi di autenticazione.

Il gestore dell'evento `OnAppearing` nel code-behind **LoginPage.XAML.cs** chiama `AcquireTokenSilentAsync` per aggiornare il token di autenticazione per gli utenti che hanno eseguito l'accesso in precedenza. Il processo di autenticazione reindirizza al `LogoutPage` se ha esito positivo e non esegue alcuna operazione in caso di errore. Nell'esempio seguente viene illustrato il processo di riautenticazione invisibile all'utente in `OnAppearing`:

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

Il gestore dell'evento `OnLoginButtonClicked` (generato quando viene fatto clic sul pulsante di accesso) chiama `AcquireTokenAsync`. La libreria MSAL apre automaticamente il browser per dispositivi mobili e passa alla pagina di accesso. L'URL di accesso, denominato **autorità**, è una combinazione del nome del tenant e dei criteri definiti nel file **Constants.cs** . Se l'utente sceglie l'opzione password dimenticata, viene restituita all'app con un'eccezione, che avvia l'esperienza di password dimenticata. Nell'esempio seguente viene illustrato il processo di autenticazione:

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

Il metodo `OnForgotPassword` è simile al processo di accesso, ma implementa un criterio personalizzato. `OnForgotPassword` usa un overload diverso di `AcquireTokenAsync`, che consente di fornire un' **autorità**specifica. Nell'esempio seguente viene illustrato come fornire un' **autorità** personalizzata durante l'acquisizione di un token:

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

L'ultima parte dell'autenticazione è il processo di disconnessione. Il metodo `OnLogoutButtonClicked` viene chiamato quando l'utente preme il pulsante di disconnessione. Esegue il ciclo di tutti gli account e garantisce che i token siano stati invalidati. Nell'esempio seguente viene illustrata l'implementazione di disconnessione:

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

In iOS, lo schema URL personalizzato registrato con Azure Active Directory B2C deve essere registrato in **info. plist**. MSAL prevede che lo schema URL rispetti un modello specifico, descritto in precedenza in [registrare l'applicazione per dispositivi mobili con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). Lo screenshot seguente mostra lo schema URL personalizzato in **info. plist**.

!["Registrazione di uno schema URL personalizzato in iOS"](azure-ad-b2c-images/customurl-ios.png)

MSAL richiede anche diritti keychain in iOS, registrati in **Entitilements. plist**, come illustrato nello screenshot seguente:

!["Impostazione dei diritti dell'applicazione in iOS"](azure-ad-b2c-images/entitlements-ios.png)

Al termine di richiesta di autorizzazione, Azure Active Directory B2C reindirizza all'URL di reindirizzamento registrato. Con lo schema dell'URL personalizzato, iOS avvia l'applicazione per dispositivi mobili e passa l'URL come parametro di avvio, dove viene elaborato dal `OpenUrl` override della classe `AppDelegate` dell'applicazione e restituisce il controllo dell'esperienza a MSAL. Nell'esempio di codice seguente viene illustrata l'implementazione di `OpenUrl`:

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

In Android è necessario che lo schema URL personalizzato registrato con Azure Active Directory B2C sia registrato in **file AndroidManifest. XML**. MSAL prevede che lo schema URL rispetti un modello specifico, descritto in precedenza in [registrare l'applicazione per dispositivi mobili con Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). Nell'esempio seguente viene illustrato lo schema URL personalizzato in **file AndroidManifest. XML**.

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
        <!-- example -->
        <!-- <data android:scheme="msalaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" android:host="auth" /> -->
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

È necessario modificare la classe `MainActivity` per fornire l'oggetto `UIParent` all'applicazione durante la chiamata di `OnCreate`. Quando Azure Active Directory B2C completa la richiesta di autorizzazione, reindirizza allo schema URL registrato da **file AndroidManifest. XML**. Lo schema URI registrato genera Android chiamando il metodo `OnActivityResult` con l'URL come parametro Launch, in cui viene elaborato dal metodo `SetAuthenticationContinuationEventArgs`.

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

Non è necessaria alcuna configurazione aggiuntiva per usare MSAL nel piattaforma UWP (Universal Windows Platform)

## <a name="run-the-project"></a>Eseguire il progetto

Eseguire l'applicazione in un dispositivo virtuale o fisico. Toccando il pulsante di **accesso** si apre il browser e si passa a una pagina in cui è possibile accedere o creare un account. Dopo aver completato il processo di accesso, si dovrebbe tornare alla pagina di disconnessione dell'applicazione. Lo screenshot seguente mostra la schermata di accesso utente in esecuzione in Android e iOS:

!["Schermata di accesso di Azure ADB2C in Android e iOS"](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>Collegamenti correlati

- [AzureADB2CAuth (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentazione di Microsoft Authentication Library](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
