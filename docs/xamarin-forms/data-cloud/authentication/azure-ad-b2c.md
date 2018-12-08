---
title: Autenticazione degli utenti con Azure Active Directory B2C
description: Azure Active B2C di Directory è una soluzione di gestione identità cloud per applicazioni rivolte agli utenti web e mobili. Questo articolo illustra come usare Microsoft Authentication Library e Azure Active Directory B2C per l'integrazione di gestione delle identità degli utenti in un'applicazione per dispositivi mobili.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 7c12136a0dad0165c46f1559e7a2d61abaf7af1e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059704"
---
# <a name="authenticating-users-with-azure-active-directory-b2c"></a>Autenticazione degli utenti con Azure Active Directory B2C

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)

_Azure Active B2C di Directory è una soluzione di gestione identità cloud per applicazioni rivolte agli utenti web e mobili. Questo articolo illustra come usare Microsoft Authentication Library e Azure Active Directory B2C per l'integrazione di gestione delle identità degli utenti in un'applicazione per dispositivi mobili._

![](~/media/shared/preview.png "Questa API è ancora in versione definitiva")

> [!NOTE]
> Il [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client) è ancora in anteprima, ma è adatto per l'uso in un ambiente di produzione. Tuttavia, si può determinare interruzioni modifiche per l'API di formato della cache interna e altri meccanismi per la libreria, che possono influire sull'applicazione.

## <a name="overview"></a>Panoramica

Azure Active B2C di Directory è un servizio di gestione di identità per le applicazioni rivolte ai consumatori, che consente agli utenti di accedere all'applicazione da:

- Usando gli account di social networking esistenti (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Creazione di nuove credenziali (indirizzo di posta elettronica e password, o nome utente e password). Queste credenziali sono dette *locale* account.

Il processo per integrare il servizio di gestione di identità di Azure Active Directory B2C in un'applicazione per dispositivi mobili è come segue:

1. Creare un tenant di Azure Active Directory B2C. Per altre informazioni, vedere [creare un tenant di Azure Active Directory B2C nel portale di Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).
1. Registrare l'applicazione per dispositivi mobili con il tenant di Azure Active Directory B2C. Il processo di registrazione assegna un' **ID applicazione** che identifica in modo univoco l'applicazione e una **URL di reindirizzamento** che può essere utilizzato per indirizzare le risposte all'applicazione. Per altre informazioni, vedere [Azure Active Directory B2C: registrare l'applicazione](/azure/active-directory-b2c/active-directory-b2c-app-registration/).
1. Creare un criterio di iscrizione e l'accesso. Questo criterio verranno definiti le esperienze consumer passerà attraverso durante l'iscrizione e l'accesso e viene inoltre il contenuto dei token, l'applicazione riceverà corretta iscrizione o accesso. Per altre informazioni, vedere [Azure Active Directory B2C: criteri predefiniti](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).
1. Usare la [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client) (MSAL) nell'applicazione per dispositivi mobili per avviare un flusso di lavoro di autenticazione con il tenant di Azure Active Directory B2C.

> [!NOTE]
> Nonché l'integrazione di gestione delle identità di Azure Active Directory B2C in applicazioni per dispositivi mobili, MSAL è anche utilizzabile per integrare la gestione delle identità di Azure Active Directory nelle applicazioni per dispositivi mobili. A questo scopo tramite la registrazione di un'applicazione per dispositivi mobili con Azure Active Directory nel [portale di registrazione applicazione](https://apps.dev.microsoft.com/). Il processo di registrazione assegna un' **ID applicazione** che identifica in modo univoco l'applicazione, che deve essere specificato quando si usa MSAL. Per altre informazioni, vedere [come registrare un'app con l'endpoint v2.0](/azure/active-directory/develop/active-directory-v2-app-registration/), e [autenticare Your Mobile App utilizzando Microsoft Authentication Library](https://blog.xamarin.com/authenticate-mobile-apps-using-microsoft-authentication-library/) sul blog di Xamarin.

MSAL Usa il web browser del dispositivo per eseguire l'autenticazione. Ciò migliora l'usabilità di un'applicazione, come gli utenti devono solo eseguire l'accesso una volta per ogni dispositivo, migliorando i tassi di conversione di accesso e autorizzazione flussi nell'applicazione. Il browser del dispositivo offre inoltre una maggiore sicurezza. Dopo che l'utente ha completato il processo di autenticazione, il controllo verrà restituito all'applicazione dalla scheda del browser web. Questo risultato viene ottenuto tramite la registrazione di uno schema URL personalizzato per l'URL di reindirizzamento restituito dal processo di autenticazione e quindi il rilevamento e la gestione degli URL personalizzato dopo averla inviata. Per altre informazioni sulla scelta di uno schema URL personalizzato, vedere [scelta di un URI di reindirizzamento app native](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri/).

> [!NOTE]
> Il meccanismo per la registrazione di uno schema URL personalizzato con il sistema operativo e di schema per la gestione è specifico per ogni piattaforma.

Ogni richiesta viene inviata a un tenant di Azure Active Directory B2C specifica un *criteri*. I criteri descrivono le esperienze di identità utente, ad esempio iscrizione o accesso. Ad esempio, un criterio di iscrizione consente il comportamento del tenant di Azure Active Directory B2C per essere configurate tramite le impostazioni seguenti:

- Tipi di conto che gli utenti possono usare per accedere all'applicazione.
- Raccolta dei dati dal consumer durante l'iscrizione.
- Multi-factor authentication.
- Contenuto della pagina di iscrizione.
- Attestazioni nei token che l'applicazione per dispositivi mobili riceve quando il criterio è stato eseguito.

Un tenant di Azure Active Directory può contenere più criteri di tipi diversi, che possono quindi essere usati nell'applicazione in base alle esigenze. Inoltre, i criteri possono essere riutilizzati tra le applicazioni, consentendo di definire e modificare le esperienze di identità senza modificare il codice. Per altre informazioni sui criteri, vedere [Azure Active Directory B2C: criteri predefiniti](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

## <a name="setup"></a>Configurazione

La libreria NuGet di Microsoft Authentication Library (MSAL) deve essere aggiunto al progetto libreria di classi portabile (PCL) e i progetti di piattaforma in una soluzione xamarin. Forms. Le sezioni seguenti forniscono istruzioni di configurazione aggiuntive per l'uso di MSAL per comunicare con un tenant di Azure Active Directory B2C da un'applicazione per dispositivi mobili.

### <a name="portable-class-library"></a>Libreria di classi portabile

Librerie di classi portabili che utilizzano MSAL dovranno essere reindirizzati per l'uso Profile7. Per altre informazioni sulle librerie di classi portabili, vedere l'[introduzione alle librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md).

### <a name="ios"></a>iOS

In iOS, lo schema URL personalizzato che è stato registrato con Azure Active Directory B2C deve essere registrato nella **Info. plist**, come illustrato nello screenshot seguente:

![](azure-ad-b2c-images/customurl-ios.png "Registrazione di uno schema URL personalizzato in iOS")

Al termine di richiesta di autorizzazione, Azure Active Directory B2C reindirizza all'URL di reindirizzamento registrato. Poiché l'URL viene utilizzato uno schema personalizzato comporta l'avvio dell'applicazione per dispositivi mobili iOS, passando in URL come parametro di avvio, in cui viene elaborato dal `OpenUrl` eseguire l'override dell'applicazione `AppDelegate` (classe), illustrato nel codice seguente esempio:

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
            return true;
        }
    }
}
```

Il codice nel `OpenURL` metodo assicura che il controllo ritorna al MSAL, una volta terminata la parte interattiva del flusso di lavoro autenticazione.

### <a name="android"></a>Android

In Android, lo schema URL personalizzato che è stato registrato con Azure Active Directory B2C deve essere registrato nella **androidmanifest. XML**, aggiungendo un `<activity>` elemento esistente `<application>` elemento. Il `<activity>` elemento consente di specificare il `IntentFilter` nel `Activity` che gestisce lo schema e viene illustrato nell'esempio seguente:

```xml
<application ...>
  <activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.DEFAULT" />
      <category android:name="android.intent.category.BROWSABLE" />
      <data android:scheme="INSERT_URL_SCHEME_HERE" android:host="auth" />
    </intent-filter>
  </activity>
</application>
```

Al termine di richiesta di autorizzazione, Azure Active Directory B2C reindirizza all'URL di reindirizzamento registrato. Poiché l'URL viene utilizzato uno schema personalizzato comporta l'avvio dell'applicazione per dispositivi mobili Android, passando in URL come parametro di avvio, in cui viene elaborato dal `microsoft.identity.client.BrowserTabActivity`. Si noti che il `data android:scheme` proprietà deve essere impostata sullo schema URL personalizzato che viene registrato con l'applicazione di Azure Active Directory B2C.

Inoltre, il `MainActivity` classe deve essere modificata, come illustrato nell'esempio di codice seguente:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.Droid
{
    ...
    public class MainActivity : FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            global::Xamarin.Forms.Forms.Init(this, bundle);
            Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();
            LoadApplication(new App());
            App.UiParent = new UIParent(this);
        }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
        }
    }
}

```

Il `OnCreate` metodo viene modificato tramite l'assegnazione di un `UIParent` dell'istanza per il `App.UiParent` proprietà. Ciò garantisce che il flusso di autenticazione venga eseguita nel contesto dell'attività corrente.

Il codice nel `OnActivityResult` metodo assicura che il controllo ritorna al MSAL, una volta terminata la parte interattiva del flusso di lavoro autenticazione.

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Nella piattaforma Windows universale, non è necessaria alcuna impostazione aggiuntiva usare MSAL.

## <a name="initialization"></a>Inizializzazione

La libreria di autenticazione Microsoft Usa i membri del `PublicClientApplication` classe per avviare un flusso di lavoro di autenticazione. L'applicazione di esempio dichiara e Inizializza un `public` proprietà di questo tipo, denominato `ADB2CClient`, nella `AuthenticationProvider` classe. Esempio di codice seguente viene illustrato come questa proprietà viene inizializzata:

```csharp
ADB2CClient = new PublicClientApplication(Constants.ClientID, Constants.Authority);
```

Quando l'applicazione per dispositivi mobili è stato registrato con il tenant di Azure Active Directory B2C, il processo di registrazione è stato assegnato un **ID applicazione**. Questo ID deve essere specificato nel `PublicClientApplication` costruttore, insieme a un `Authority` costante che è costituito da un URL di base e i criteri di Azure Active Directory B2C per essere eseguito.

## <a name="signing-in"></a>Accedi

La schermata di accesso nell'applicazione di esempio viene illustrata negli screenshot seguenti:

![](azure-ad-b2c-images/login.png "Pagina di accesso")

Accesso con provider di identità basati su social network o con un account locale, sono consentiti. Mentre Microsoft, Google e Facebook, come illustrato in precedenza, vengono usati come provider di identità di social networking, altri provider di identità è anche utilizzabile.

Esempio di codice seguente mostra come viene richiamata la procedura di accesso:

```csharp
using Microsoft.Identity.Client;

public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);
    ...
}

```

Il `AcquireTokenAsync` metodo avvia web browser del dispositivo e visualizza le opzioni di autenticazione definite nei criteri di Azure Active Directory B2C specificato dai criteri di cui viene fatto riferimento tramite il `Constants.Authority` costante. Tali criteri definiscono le esperienze consumer passerà attraverso durante l'iscrizione e l'accesso e le attestazioni che l'applicazione riceverà corretta iscrizione o accesso.

Il risultato del `AcquireTokenAsync` chiamata al metodo è un `AuthenticationResult` istanza. Se l'autenticazione ha esito positivo, il `AuthenticationResult` istanza conterrà un token di identità, che verrà memorizzato localmente. Se l'autenticazione ha esito negativo, il `AuthenticationResult` istanza conterrà i dati che indicano il motivo per cui l'autenticazione non è riuscita.

Nell'applicazione di esempio, se l'autenticazione ha esito positivo, il `TodoList` passa ad.

## <a name="silent-re-authentication"></a>Ripetere l'autenticazione invisibile all'utente

Quando il `LoginPage` visualizzato nell'esempio di applicazione, viene effettuato un tentativo di recuperare un token utente senza mostrare alcuna interfaccia utente di autenticazione. Questo risultato viene ottenuto con il `AcquireTokenSilentAsync` metodo, come illustrato nell'esempio di codice seguente:

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult;

    if (useSilent)
    {
        authenticationResult = await ADB2CClient.AcquireTokenSilentAsync(
            Constants.Scopes,
            GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
            Constants.Authority,
            false);
    }
    ...
}
```

Il `AcquireTokenSilentAsync` metodo tenta di recuperare un token utente dalla cache, senza richiedere all'utente di accesso. In grado di gestire lo scenario in cui un token appropriato potrebbe essere già presente nella cache dalle sessioni precedenti. Se il tentativo di ottenere un token ha esito positivo, il `TodoList` passa ad. Se il tentativo di ottenere un token ha esito negativo, non accade nulla e l'utente avrà la possibilità di avviare un nuovo flusso di autenticazione.

## <a name="signing-out"></a>Disconnessione

Esempio di codice seguente mostra come viene richiamato il processo di disconnessione:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

Ciò consente di cancellare tutti i token di autenticazione dalla cache locale.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come usare Microsoft Authentication Library (MSAL) e Azure Active Directory B2C per l'integrazione di gestione delle identità degli utenti in un'applicazione per dispositivi mobili. Azure Active B2C di Directory è una soluzione di gestione identità cloud per applicazioni rivolte agli utenti web e mobili.


## <a name="related-links"></a>Collegamenti correlati

- [AzureADB2CAuth (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client)
