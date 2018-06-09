---
title: L'autenticazione degli utenti con un Provider di identità
description: In questo articolo viene illustrato come utilizzare Xamarin.Auth per gestire il processo di autenticazione in un'applicazione di xamarin. Forms.
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2017
ms.openlocfilehash: 361b5e5583b10b7ea07abd1460350d6445cae1c2
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241260"
---
# <a name="authenticating-users-with-an-identity-provider"></a>L'autenticazione degli utenti con un Provider di identità

_Xamarin.Auth è un SDK multipiattaforma per l'autenticazione degli utenti e gli account di archiviazione. Include gli autenticatori OAuth che forniscono il supporto per l'utilizzo del provider di identità, ad esempio Google, Microsoft, Facebook e Twitter. In questo articolo viene illustrato come utilizzare Xamarin.Auth per gestire il processo di autenticazione in un'applicazione di xamarin. Forms._

È uno standard aperto per l'autenticazione OAuth e consente a un proprietario della risorsa notificare a un provider di risorse che l'autorizzazione deve essere concesso a una terza parte per accedere alle informazioni senza condividere l'identità di proprietari della risorsa. Un esempio di questa verrebbe abilitazione di un utente notificare a un provider di identità (ad esempio Google, Microsoft, Facebook o Twitter) che autorizzazioni devono essere concesse a un'applicazione di accedere ai dati, senza condividere l'identità dell'utente. Viene usata in genere come approccio per gli utenti di accedere a siti Web e applicazioni che usano un provider di identità, ma senza esporre la propria password per il sito Web o l'applicazione.

Una panoramica generale del flusso di autenticazione durante l'utilizzo di un provider di identità di OAuth sia come indicato di seguito:

1. L'applicazione passa un browser all'URL di un provider di identità.
1. Il provider di identità gestisce l'autenticazione utente e restituisce un codice di autorizzazione per l'applicazione.
1. L'applicazione scambia il codice di autorizzazione per un token di accesso dal provider di identità.
1. L'applicazione utilizza il token di accesso per accedere alle API nel provider di identità, ad esempio un'API per la richiesta di dati utente di base.

L'applicazione di esempio viene illustrato come utilizzare Xamarin.Auth per implementare un flusso di autenticazione nativo con Google. Mentre Google viene utilizzata come provider di identità in questo argomento, l'approccio è applicabile ad altri provider di identità. Per ulteriori informazioni sull'autenticazione tramite l'endpoint di Google OAuth 2.0, vedere [tramite OAuth 2.0 per l'accesso Google APIs](https://developers.google.com/identity/protocols/OAuth2) nel sito Web di Google.

> [!NOTE]
> In iOS 9 e versioni successive, sicurezza trasporto App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app, impedendo in tal modo la diffusione accidentale di informazioni riservate. Poiché AT è attivata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.
> Può essere scelto at fuori se non è possibile utilizzare il `HTTPS` del protocollo e proteggere le comunicazioni per le risorse internet. Ciò può essere ottenuto l'aggiornamento dell'app **Info. plist** file. Per ulteriori informazioni vedere [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md).

## <a name="using-xamarinauth-to-authenticate-users"></a>Utilizzo di Xamarin.Auth per autenticare gli utenti

Xamarin.Auth supporta due approcci per le applicazioni interagire con endpoint di autorizzazione di un provider di identità:

1. Utilizzo di una visualizzazione web incorporato. Anche se è stata una pratica comune, non è più consigliato per i motivi seguenti:

    - L'applicazione che ospita la visualizzazione web può accedere a credenziali di autenticazione completa dell'utente, non solo la concessione di autorizzazione OAuth cui è previsto per l'applicazione. Questa condizione viola il principio di privilegio minimo, l'applicazione abbia accesso a credenziali più elevate rispetto a richiesto potenzialmente aumentando la superficie di attacco dell'applicazione.
    - L'applicazione host può acquisire i nomi utente e password, automaticamente dell'invio dei moduli e ignorare il consenso dell'utente e copiare i cookie di sessione e usarli per eseguire azioni autenticate come utente.
    - Visualizzazioni web incorporato non condividono lo stato di autenticazione con altre applicazioni o browser del dispositivo, richiedere all'utente di accesso per ogni richiesta di autorizzazione che è considerata un'esperienza utente inferiore.
    - Alcuni endpoint di autorizzazione eseguire i passaggi per rilevare e bloccare le richieste di autorizzazione che provengono da visualizzazioni web.

1. Tramite l'utilizzo di browser del dispositivo, che è l'approccio consigliato. Tramite il browser del dispositivo per le richieste OAuth migliora l'usabilità di un'applicazione, come gli utenti devono solo per accedere al provider di identità di una volta per ogni dispositivo, migliorando i tassi di conversione dei flussi di accesso e autorizzazione nell'applicazione. Il browser del dispositivo offre inoltre una maggiore sicurezza, come le applicazioni sono in grado di controllare e modificare contenuto in una visualizzazione web, ma non visualizzato nel Visualizzatore. Questo è l'approccio adottato in questa applicazione di esempio e l'articolo.

Una panoramica di come l'applicazione di esempio utilizza Xamarin.Auth per autenticare gli utenti e recuperare i dati di base è illustrata nel diagramma seguente:

![](oauth-images/google-auth.png "Utilizzo di Xamarin.Auth per l'autenticazione con Google")

L'applicazione effettua una richiesta di autenticazione a Google usando la `OAuth2Authenticator` classe. Viene restituita una risposta di autenticazione, una volta che l'utente è autenticato correttamente con Google tramite la pagina di accesso, che include un token di accesso. L'applicazione quindi effettua una richiesta di Google per i dati utente di base, mediante il `OAuth2Request` (classe), con il token di accesso siano incluso nella richiesta.

### <a name="setup"></a>Configurazione

Per integrare Accedi Google con un'applicazione di xamarin. Forms, è necessario creare un progetto Console API Google. Per ottenere questo risultato, è possibile procedere come segue:

1. Passare al [Console API Google](http://console.developers.google.com) sito Web e accedere con le credenziali dell'account Google.
1. Dall'elenco a discesa progetto selezionare un progetto esistente o crearne uno nuovo.
1. Nella barra laterale in "API di gestione", selezionare **credenziali**, quindi selezionare il **scheda schermata di consenso OAuth**. Scegliere un **indirizzo di posta elettronica**, specificare un **nome prodotto mostrato agli utenti**e premere **salvare**.
1. Nel **credenziali** , selezionare il **creare credenziali** elenco a discesa elenco e scegliere **ID client OAuth**.
1. In **tipo di applicazione**, selezionare la piattaforma che eseguiranno l'applicazione per dispositivi mobili su (**iOS** o **Android**).
1. Compilare i dettagli richiesti e selezionare il **crea** pulsante.

> [!NOTE]
> Consente a un'applicazione di accedere a abilitato Google APIs un ID Client e applicazioni per dispositivi mobili è univoco per una singola piattaforma. Pertanto, un **ID client OAuth** deve essere creato per ogni piattaforma che verrà utilizzato Accedi Google.

Dopo aver eseguito questi passaggi, Xamarin.Auth consente di avviare un flusso di autenticazione OAuth2 con Google.

### <a name="creating-and-configuring-an-authenticator"></a>Creazione e configurazione di un autenticatore

Del Xamarin.Auth `OAuth2Authenticator` classe è responsabile della gestione flusso di autenticazione OAuth. Esempio di codice seguente viene illustrata la creazione dell'istanza di `OAuth2Authenticator` classe durante l'autenticazione con i web browser del dispositivo:

```csharp
var authenticator = new OAuth2Authenticator(
    clientId,
    null,
    Constants.Scope,
    new Uri(Constants.AuthorizeUrl),
    new Uri(redirectUri),
    new Uri(Constants.AccessTokenUrl),
    null,
    true);
```

La `OAuth2Authenticator` classe richiede un numero di parametri, come indicato di seguito:

- **ID client** – identifica il client che effettua la richiesta e può essere recuperato dal progetto nel [Console API Google](http://console.developers.google.com).
- **Segreto client** : deve essere `null` o `string.Empty`.
- **Ambito** : identifica l'accesso all'API richiesto dall'applicazione e il valore indica la schermata di consenso dell'utente che viene visualizzata all'utente. Per ulteriori informazioni sugli ambiti, vedere [richiesta di autorizzazione dell'API](https://developers.google.com/+/web/api/rest/oauth) nel sito Web di Google.
- **Autorizzazione URL** : identifica l'URL in cui verrà ottenuto dal codice di autorizzazione.
- **URL di reindirizzamento** : identifica l'URL in cui verrà inviata la risposta. Il valore di questo parametro deve corrispondere a uno dei valori di che è presente il **credenziali** scheda per il progetto nel [Google Developers Console](https://console.developers.google.com/).
- **AccessToken Url** : identifica l'URL utilizzato per richiedere token di accesso dopo aver ottenuto un codice di autorizzazione.
- **GetUserNameAsync Func** : facoltativo `Func` che verrà usato per recuperare in modo asincrono il nome utente dell'account dopo che è stato autenticato correttamente.
- **Utilizzare l'interfaccia utente nativa** : un `boolean` valore che indica se utilizzare i web browser del dispositivo per eseguire la richiesta di autenticazione.

### <a name="setup-authentication-event-handlers"></a>Gestori di eventi di autenticazione del programma di installazione

Prima di presentata l'interfaccia utente, un gestore eventi per il `OAuth2Authenticator.Completed` deve essere registrato un evento, come illustrato nell'esempio di codice seguente:

```csharp
authenticator.Completed += OnAuthCompleted;
```

Questo evento viene attivato quando l'utente viene autenticato correttamente o Annulla accesso.

Facoltativamente, un gestore eventi per il `OAuth2Authenticator.Error` può anche essere registrato un evento.

### <a name="presenting-the-sign-in-user-interface"></a>Presentata l'interfaccia utente

L'interfaccia utente di accesso possa essere presentato all'utente tramite relatore Xamarin.Auth account di accesso, che deve essere inizializzato in ogni progetto della piattaforma. Esempio di codice seguente viene illustrato come inizializzare un relatore di account di accesso nel `AppDelegate` classe nel progetto iOS:

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

Esempio di codice seguente viene illustrato come inizializzare un relatore di account di accesso nel `MainActivity` classe nel progetto Android:

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

Il progetto libreria di classe portabile (PCL) può quindi richiamare relatore account di accesso come indicato di seguito:

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Si noti che l'argomento di `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` metodo è il `OAuth2Authenticator` istanza. Quando il `Login` metodo viene richiamato, l'interfaccia utente di accesso viene presentato all'utente in una scheda da web browser del dispositivo, come illustrato nelle schermate seguenti:

![](oauth-images/login.png "Accedi Google")

### <a name="processing-the-redirect-url"></a>Elaborazione dell'URL di reindirizzamento

Dopo che l'utente completa il processo di autenticazione, il controllo verrà restituito all'applicazione dalla scheda del web browser. Questo risultato viene ottenuto tramite la registrazione di uno schema URL personalizzato per l'URL di reindirizzamento restituito dal processo di autenticazione, quindi rileva e gestisce l'URL personalizzato dopo averla inviata.

Quando si sceglie uno schema URL personalizzato da associare a un'applicazione, applicazioni devono utilizzare uno schema basato su un nome sotto il controllo. Ciò può essere ottenuto utilizzando il nome dell'identificatore bundle in iOS e il nome del pacchetto in Android e quindi invertire in modo da ottenere lo schema dell'URL. Tuttavia, alcuni provider di identità, ad esempio Google, assegnare gli identificatori dei client in base ai nomi di dominio, che quindi sono invertiti e utilizzati come schema di URL. Se, ad esempio, Google crea un id client `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, lo schema dell'URL sarà `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`. Si noti che solo un singolo `/` possono essere visualizzati dopo il componente di schema. Pertanto, un esempio completo di un URL di reindirizzamento che utilizzano uno schema URL personalizzato è `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`.

Quando il browser web riceve una risposta dal provider di identità che contiene uno schema URL personalizzato, tenta di caricare l'URL, che avrà esito negativo. Al contrario, lo schema dell'URL personalizzato viene segnalato al sistema operativo generando un evento. Il sistema operativo controlla quindi per gli schemi registrati e se ne viene trovato, il sistema operativo verrà avviare l'applicazione che ha registrato lo schema e inviare l'URL di reindirizzamento.

Il meccanismo per la registrazione di uno schema URL personalizzato con il sistema operativo e di schema per la gestione è specifico per ogni piattaforma.

#### <a name="ios"></a>iOS

In iOS, uno schema URL personalizzato è registrato **Info. plist**, come illustrato nella schermata seguente:

![](oauth-images/info-plist.png "Registrazione dello schema URL")

Il **identificatore** valore può essere qualsiasi tipo e **ruolo** valore deve essere impostato su **Visualizzatore**. Il **schemi Url** valore, che inizia con `com.googleusercontent.apps`, è possibile ottenere dall'id client iOS per il progetto [Console API Google](http://console.developers.google.com).

Quando il provider di identità completa la richiesta di autorizzazione, viene reindirizzato all'URL di reindirizzamento dell'applicazione. Poiché l'URL viene utilizzato uno schema personalizzato comporta l'avvio dell'applicazione iOS, passando l'URL come un parametro di avvio, in cui viene elaborata la `OpenUrl` eseguire l'override dell'applicazione `AppDelegate` (classe), come illustrato nell'esempio di codice seguente:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    // Convert NSUrl to Uri
    var uri = new Uri(url.AbsoluteString);

    // Load redirectUrl page
    AuthenticationState.Authenticator.OnPageLoading(uri);

    return true;
}
```

Il `OpenUrl` metodo converte l'URL ricevuto da un `NSUrl` per .NET `Uri`, prima di elaborare l'URL di reindirizzamento con il `OnPageLoading` metodo per un pubblico `OAuth2Authenticator` oggetto. In questo modo Xamarin.Auth per chiudere la scheda del web browser e analizzare i dati di OAuth ricevuti.

#### <a name="android"></a>Android

In Android, viene registrato uno schema URL personalizzato specificando un [ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) attributo la `Activity` che gestirà lo schema. Quando il provider di identità completa la richiesta di autorizzazione, viene reindirizzato all'URL di reindirizzamento dell'applicazione. Come uno schema personalizzato comporta Android avviando l'applicazione viene utilizzato l'URL, passando l'URL come un parametro di avvio, in cui viene elaborata la `OnCreate` metodo il `Activity` registrato per gestire lo schema dell'URL personalizzato. Esempio di codice seguente viene illustrata la classe dall'applicazione di esempio che gestisce lo schema dell'URL personalizzato:

```csharp
[Activity(Label = "CustomUrlSchemeInterceptorActivity", NoHistory = true, LaunchMode = LaunchMode.SingleTop )]
[IntentFilter(
    new[] { Intent.ActionView },
    Categories = new [] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataSchemes = new [] { "<insert custom URL here>" },
    DataPath = "/oauth2redirect")]
public class CustomUrlSchemeInterceptorActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        // Convert Android.Net.Url to Uri
        var uri = new Uri(Intent.Data.ToString());

        // Load redirectUrl page
        AuthenticationState.Authenticator.OnPageLoading(uri);

        Finish();
    }
}
```

Il `DataSchemes` proprietà del [ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/) deve essere impostata sull'identificatore client invertito ottenuto dall'id client Android per il progetto in [Console API Google](http://console.developers.google.com).

Il `OnCreate` metodo converte l'URL ricevuto da un `Android.Net.Url` per .NET `Uri`, prima di elaborare l'URL di reindirizzamento con il `OnPageLoading` metodo per un pubblico `OAuth2Authenticator` oggetto. In questo modo Xamarin.Auth chiudere la scheda del web browser e analizzare i dati di OAuth ricevuti.

> [!IMPORTANT]
> In Android, Xamarin.Auth utilizza il `CustomTabs` API per comunicare con il browser web e il sistema operativo. Tuttavia, non è garantito che una `CustomTabs` browser compatibile verrà installata sul dispositivo dell'utente.

### <a name="examining-the-oauth-response"></a>Esaminare la risposta di OAuth

Dopo l'analisi di dati ricevuti OAuth, Xamarin.Auth genererà il `OAuth2Authenticator.Completed` evento. Nel gestore eventi per questo evento, il `AuthenticatorCompletedEventArgs.IsAuthenticated` proprietà può essere utilizzata per identificare se l'autenticazione ha avuto esito positivo, come illustrato nell'esempio di codice seguente:

```csharp
async void OnAuthCompleted(object sender, AuthenticatorCompletedEventArgs e)
{
  ...
  if (e.IsAuthenticated)
  {
    ...
  }
}
```

I dati raccolti da un'autenticazione riuscita sono disponibili nel `AuthenticatorCompletedEventArgs.Account` proprietà. Ciò include un token di accesso, che può essere usato per firmare le richieste per i dati a un'API fornito dal provider di identità.

### <a name="making-requests-for-data"></a>Effettua richieste per i dati

Dopo l'applicazione ha ottenuto un token di accesso, viene utilizzato per eseguire una richiesta per il `https://www.googleapis.com/oauth2/v2/userinfo` API, di richiedere i dati utente di base dal provider di identità. Questa richiesta viene eseguita con del Xamarin.Auth `OAuth2Request` (classe), che rappresenta una richiesta che viene autenticata utilizzando un account recuperato da un `OAuth2Authenticator` dell'istanza, come illustrato nell'esempio di codice seguente:

```csharp
// UserInfoUrl = https://www.googleapis.com/oauth2/v2/userinfo
var request = new OAuth2Request ("GET", new Uri (Constants.UserInfoUrl), null, e.Account);
var response = await request.GetResponseAsync ();
if (response != null)
{
  string userJson = response.GetResponseText ();
  var user = JsonConvert.DeserializeObject<User> (userJson);
}
```

Nonché il metodo HTTP e l'URL dell'API, il `OAuth2Request` istanza specifica inoltre un `Account` istanza contenente il token di accesso che firma la richiesta all'URL specificato per il `Constants.UserInfoUrl` proprietà. Il provider di identità restituisce quindi i dati utente di base come una risposta JSON, compresi il nome degli utenti e l'indirizzo di posta elettronica, a condizione che il token di accesso viene riconosciuto come valido. La risposta JSON viene quindi leggere e deserializzata nel `user` variabile.

Per ulteriori informazioni, vedere [chiamando un'API di Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) nel portale di sviluppatori Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Archiviare e recuperare informazioni sull'Account nei dispositivi

Archivia in modo sicuro Xamarin.Auth `Account` oggetti in un account di archiviano in modo che le applicazioni non è sempre necessario nuovamente autenticare gli utenti. Il `AccountStore` classe è responsabile per l'archiviazione delle informazioni sull'account e supportata da servizi di portachiavi in iOS e `KeyStore` classe in Android.

Nell'esempio di codice riportato di seguito viene illustrato come un `Account` viene salvato in modo sicuro:

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

Account salvato vengono identificati in modo univoco utilizzando una chiave composta l'account `Username` proprietà e un ID di servizio, che è una stringa che viene utilizzata quando il recupero degli account dall'archivio di account. Se un `Account` salvato in precedenza, la chiamata di `Save` metodo nuovamente lo sovrascrive.

`Account` gli oggetti per un servizio può essere recuperato chiamando il `FindAccountsForService` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

Il `FindAccountsForService` metodo restituisce un `IEnumerable` insieme di `Account` gli oggetti con il primo elemento nella raccolta viene impostata come l'account corrispondente.

## <a name="summary"></a>Riepilogo

In questo articolo viene spiegato come utilizzare Xamarin.Auth per gestire il processo di autenticazione in un'applicazione di xamarin. Forms. Xamarin.Auth fornisce il `OAuth2Authenticator` e `OAuth2Request` le classi utilizzate dalle applicazioni di xamarin. Forms per utilizzare il provider di identità, ad esempio Google, Microsoft, Facebook e Twitter.


## <a name="related-links"></a>Collegamenti correlati

- [OAuthNativeFlow (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/OAuthNativeFlow/)
- [OAuth 2.0 per applicazioni Native](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [Utilizzo di OAuth 2.0 per accedere alle API di Google](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin.Auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin.Auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
