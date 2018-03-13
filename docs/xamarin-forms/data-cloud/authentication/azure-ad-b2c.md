---
title: L'autenticazione degli utenti con Azure Active Directory B2C
description: "Azure Active B2C di Directory è una soluzione di gestione di identità cloud per applicazioni mobili e web per consumatori. In questo articolo viene illustrato come utilizzare la libreria di autenticazione Microsoft e Azure Active Directory B2C per integrare la gestione delle identità utente in un'applicazione per dispositivi mobili."
ms.topic: article
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 5d64c7c1dbc502acd3876c2442f9bae1c46eeb74
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="authenticating-users-with-azure-active-directory-b2c"></a>L'autenticazione degli utenti con Azure Active Directory B2C

_Azure Active B2C di Directory è una soluzione di gestione di identità cloud per applicazioni mobili e web per consumatori. In questo articolo viene illustrato come utilizzare la libreria di autenticazione Microsoft e Azure Active Directory B2C per integrare la gestione delle identità utente in un'applicazione per dispositivi mobili._

![](~/media/shared/preview.png "Questa API è attualmente pre-release.")

> [!NOTE]
> Il [libreria di autenticazione Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) è ancora in anteprima, ma è adatto per l'uso in un ambiente di produzione. Tuttavia, vi può essere modifiche di rilievo per l'API, il formato della cache interna e altri meccanismi della libreria, che può rallentare l'applicazione.

## <a name="overview"></a>Panoramica

Azure Active B2C di Directory è un servizio di gestione di identità per le applicazioni per consumatori, che consente ai consumer di accedere all'applicazione da:

- Utilizzando gli account esistenti di social networking (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Creazione di nuove credenziali (indirizzo di posta elettronica e password, o nome utente e password). Queste credenziali vengono dette *locale* account.

Il processo per integrare il servizio di gestione di Azure Active Directory B2C identità in un'applicazione per dispositivi mobili è come segue:

1. Creare un tenant di Azure Active Directory B2C. Per ulteriori informazioni, vedere [creare un tenant di Azure Active Directory B2C nel portale di Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).
1. Registrare l'applicazione per dispositivi mobili con il tenant di Azure Active Directory B2C. Il processo di registrazione viene assegnata un' **ID applicazione** che identifica in modo univoco l'applicazione e un **l'URL di reindirizzamento** che può essere utilizzato per indirizzare le risposte dell'applicazione. Per ulteriori informazioni, vedere [Azure Active Directory B2C: registrare l'applicazione](/azure/active-directory-b2c/active-directory-b2c-app-registration/).
1. Creare un criterio di iscrizione e accesso. Questo criterio definiranno le esperienze che i consumer verranno esaminate durante l'iscrizione e Accedi e specifica anche il contenuto del token, l'applicazione riceverà corretta iscrizione o accesso. Per ulteriori informazioni, vedere [Azure Active Directory B2C: criteri predefiniti](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).
1. Utilizzare il [libreria di autenticazione Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) (MSAL) nell'applicazione per dispositivi mobili per avviare un flusso di lavoro di autenticazione con il tenant di Azure Active Directory B2C.

> [!NOTE]
> Nonché l'integrazione di gestione delle identità di Azure Active Directory B2C in applicazioni per dispositivi mobili, MSAL inoltre consente di integrare gestione delle identità di Azure Active Directory in applicazioni per dispositivi mobili. Questo può essere eseguito tramite la registrazione di un'applicazione per dispositivi mobili con Azure Active Directory il [portale di registrazione applicazione](https://apps.dev.microsoft.com/). Il processo di registrazione viene assegnata un' **ID applicazione** che identifica in modo univoco l'applicazione, che deve essere specificato quando si utilizza MSAL. Per ulteriori informazioni, vedere [come registrare un'app con l'endpoint v 2.0](/azure/active-directory/develop/active-directory-v2-app-registration/), e [autenticare Your Mobile App utilizzando Microsoft Authentication Library](https://blog.xamarin.com/authenticate-mobile-apps-using-microsoft-authentication-library/) sul blog di Xamarin.

MSAL Usa web browser del dispositivo per eseguire l'autenticazione. Ciò migliora l'usabilità di un'applicazione, come gli utenti devono solo effettuare l'accesso una volta per ogni dispositivo, migliorando i tassi di conversione di accesso e autorizzazione flussi nell'applicazione. Il browser del dispositivo offre inoltre una maggiore sicurezza. Dopo che l'utente completa il processo di autenticazione, il controllo verrà restituito all'applicazione dalla scheda del web browser. Questo risultato viene ottenuto tramite la registrazione di uno schema URL personalizzato per l'URL di reindirizzamento restituito dal processo di autenticazione, quindi rileva e gestisce l'URL personalizzato dopo averla inviata. Per ulteriori informazioni sulla scelta di uno schema URL personalizzato, vedere [scelta di un URI di reindirizzamento app nativa](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri/).

> [!NOTE]
> Il meccanismo per la registrazione di uno schema URL personalizzato con il sistema operativo e di schema per la gestione è specifico per ogni piattaforma.

Ogni richiesta inviata a un tenant di Azure Active Directory B2C specifica un *criteri*. I criteri vengono descritti i consumer esperienze di identità, ad esempio l'iscrizione o accesso. Ad esempio, un criterio per l'abbonamento consente il comportamento del tenant di Azure Active Directory B2C essere configurata mediante le impostazioni seguenti:

- Tipi di conto che i consumer è possono utilizzare per accedere all'applicazione.
- Raccogliere dati dal consumer durante l'iscrizione.
- Autenticazione a più fattori.
- Contenuto della pagina di iscrizione.
- Attestazioni nei token ricevuti dall'applicazione per dispositivi mobili quando il criterio è stato eseguito.

Un tenant Azure Active Directory può contenere più criteri di tipi diversi, che possono quindi essere usati nell'applicazione in base alle esigenze. Inoltre, i criteri possono essere riutilizzati per le applicazioni, che consente di definire e modificare esperienze identità consumer senza modificare il codice. Per ulteriori informazioni sui criteri, vedere [Azure Active Directory B2C: criteri predefiniti](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

## <a name="setup"></a>Configurazione

La libreria di NuGet della libreria di autenticazione di Microsoft (MSAL) deve essere aggiunto al progetto libreria di classe portabile (PCL) e progetti di piattaforma in una soluzione xamarin. Forms. Le sezioni seguenti forniscono istruzioni di configurazione aggiuntive per l'utilizzo di MSAL per comunicare con un tenant di Azure Active Directory B2C da un'applicazione per dispositivi mobili.

### <a name="portable-class-library"></a>Libreria di classi portabile

MSAL non supporta Windows Phone 8.1 e pertanto PCLs che utilizzano MSAL sarà necessario rimuovere questa destinazione. Questo può essere eseguito da PCLs per l'utilizzo di Profile7 di reindirizzamento. Per altre informazioni sulle librerie di classi portabili, vedere l'[introduzione alle librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md).

### <a name="ios"></a>iOS

In iOS, lo schema URL personalizzato che è stato registrato con Azure Active Directory B2C deve essere registrato **Info. plist**, come illustrato nella schermata seguente:

![](azure-ad-b2c-images/customurl-ios.png "Registrazione di uno schema URL personalizzato in iOS")

Quando Azure Active Directory B2C completa la richiesta di autorizzazione, viene reindirizzato all'URL di reindirizzamento registrato. Poiché l'URL viene utilizzato uno schema personalizzato viene avviato l'applicazione per dispositivi mobili iOS, passando l'URL come un parametro di avvio, in cui viene elaborata la `OpenUrl` eseguire l'override dell'applicazione `AppDelegate` (classe), come illustrato nel codice seguente esempio:

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

Il codice di `OpenURL` metodo assicura che controllo viene restituito al MSAL una volta terminata la parte interattiva del flusso di lavoro autenticazione.

### <a name="android"></a>Android

In Android, lo schema URL personalizzato che è stato registrato con Azure Active Directory B2C deve essere registrato **AndroidManifest.xml**, aggiungendo un `<activity>` elemento all'interno esistente `<application>` elemento. Il `<activity>` elemento specifica il `IntentFilter` sul `Activity` che gestisce lo schema e viene illustrato nell'esempio seguente:

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

Quando Azure Active Directory B2C completa la richiesta di autorizzazione, viene reindirizzato all'URL di reindirizzamento registrato. Poiché l'URL viene utilizzato uno schema personalizzato viene avviato l'applicazione per dispositivi mobili Android, passando l'URL come un parametro di avvio, in cui viene elaborata la `microsoft.identity.client.BrowserTabActivity`. Si noti che il `data android:scheme` proprietà deve essere impostata per lo schema URL personalizzato che è registrato con l'applicazione Azure Active Directory B2C.

Inoltre, la `MainActivity` classe deve essere modificata, come illustrato nell'esempio di codice seguente:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.Droid
{
    ...
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
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

Il `OnCreate` metodo viene modificato tramite l'assegnazione di un `UIParent` istanza per il `App.UiParent` proprietà. In questo modo si garantisce che il flusso di autenticazione si verifica nel contesto dell'attività corrente.

Il codice di `OnActivityResult` metodo assicura che controllo viene restituito al MSAL una volta terminata la parte interattiva del flusso di lavoro autenticazione.

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Nella piattaforma Windows universale, alcuna impostazione aggiuntiva non è necessario utilizzare MSAL.

## <a name="initialization"></a>Inizializzazione

La libreria di autenticazione di Microsoft utilizza i membri della `PublicClientApplication` classe per avviare un flusso di lavoro di autenticazione. L'applicazione di esempio dichiara e Inizializza un `public` proprietà di questo tipo, denominato `ADB2CClient`nella `AuthenticationProvider` classe. Esempio di codice seguente viene illustrato come questa proprietà viene inizializzata:

```csharp
ADB2CClient = new PublicClientApplication(Constants.ClientID, Constants.Authority);
```

Quando l'applicazione per dispositivi mobili è stato registrato con il tenant di Azure Active Directory B2C, il processo di registrazione è assegnato un **ID applicazione**. Questo ID deve essere specificato nel `PublicClientApplication` costruttore, insieme a un `Authority` costante è costituito da un URL di base e i criteri di Azure Active Directory B2C da eseguire.

## <a name="signing-in"></a>Accedi

Schermata di accesso nell'applicazione di esempio è illustrata nelle schermate seguenti:

![](azure-ad-b2c-images/login.png "Pagina di accesso")

Accedi con il provider di identità di social networking, o con un account locale, sono consentiti. Mentre Microsoft e Google, Facebook, come illustrato in precedenza, vengono utilizzati come provider di identità di social networking, altri provider di identità può anche essere utilizzato.

Esempio di codice seguente viene illustrato come viene richiamata la procedura di accesso:

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

Il `AcquireTokenAsync` metodo Avvia browser del dispositivo e visualizza le opzioni di autenticazione definite nei criteri di Azure Active Directory B2C specificato dai criteri di cui viene fatto riferimento tramite il `Constants.Authority` costante. Tali criteri definiscono le esperienze che verranno esaminate consumer durante l'iscrizione e accesso e le attestazioni che l'applicazione riceverà corretta iscrizione o accesso.

Il risultato di `AcquireTokenAsync` chiamata al metodo è un `AuthenticationResult` istanza. Se l'autenticazione ha esito positivo, il `AuthenticationResult` istanza conterrà un token di identità, verrà memorizzato localmente. Se l'autenticazione ha esito negativo, il `AuthenticationResult` istanza conterrà dati che indicano il motivo per cui autenticazione non riuscita.

Nell'applicazione di esempio, se l'autenticazione ha esito positivo, il `TodoList` ci si sposta a pagina.

## <a name="silent-re-authentication"></a>Riautenticazione invisibile all'utente

Quando il `LoginPage` visualizzato nell'esempio di applicazione, viene effettuato un tentativo di recuperare un token utente non venga visualizzata alcuna interfaccia utente di autenticazione. Questa operazione viene eseguita con il `AcquireTokenSilentAsync` (metodo), come illustrato nell'esempio di codice seguente:

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

Il `AcquireTokenSilentAsync` metodo tenta di recuperare un token utente dalla cache, senza richiedere all'utente di accesso. In grado di gestire lo scenario in cui un token adatto potrebbe essere già presente nella cache dalle sessioni precedenti. Se il tentativo di ottenere un token ha esito positivo, il `TodoList` ci si sposta a pagina. Se il tentativo di ottenere un token ha esito negativo, non accade nulla e l'utente avrà la possibilità di avviare un nuovo flusso di autenticazione.

## <a name="signing-out"></a>La disconnessione

Esempio di codice seguente viene illustrato come viene richiamato la il processo di disconnessione:

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

Consente di cancellare tutti i token di autenticazione dalla cache locale.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare Microsoft Authentication Library (MSAL) e Azure Active Directory B2C per integrare la gestione delle identità utente in un'applicazione per dispositivi mobili. Azure Active B2C di Directory è una soluzione di gestione di identità cloud per applicazioni mobili e web per consumatori.


## <a name="related-links"></a>Collegamenti correlati

- [AzureADB2CAuth (sample)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Libreria di autenticazione Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
