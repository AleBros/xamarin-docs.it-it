---
title: AuthenticateUsers con un provider di identità
description: Questo articolo illustra come usare AUTH per gestire il processo di autenticazione in un'applicazione Xamarin.Forms.
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 25a09e27fb25e477c5176af0ee4a75a836751ccf
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487633"
---
# <a name="authenticate-users-with-an-identity-provider"></a>Autenticare gli utenti con un provider di identità

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)

_Xamarin.auth è un SDK multipiattaforma per l'autenticazione degli utenti e l'archiviazione dei relativi account. Sono inclusi gli autenticatori OAuth che forniscono supporto per l'utilizzo di provider di identità quali Google, Microsoft, Facebook e Twitter. Questo articolo illustra come usare Xamarin.auth per gestire il processo di autenticazione in un'applicazione Xamarin.Forms._

OAuth è uno standard aperto per l'autenticazione e consente a un proprietario della risorsa notificare a un provider di risorse che deve essere concesso l'autorizzazione a terze parti per accedere alle informazioni senza condividere l'identità dei proprietari di risorse. Un esempio di questo sarebbe possibile consentire a un utente notificare a un provider di identità (ad esempio, Google, Microsoft, Facebook o Twitter) che deve essere concesso l'autorizzazione a un'applicazione per accedere ai dati, senza condividere l'identità dell'utente. Viene comunemente utilizzato come un approccio per gli utenti per l'accesso ai siti Web e alle applicazioni tramite un provider di identità, ma senza dover esporre la password per il sito Web o l'applicazione.

Una panoramica generale del flusso di autenticazione quando si utilizza un provider di identità OAuth è il seguente:

1. L'applicazione consente di passare un browser all'URL di un provider di identità.
1. Il provider di identità gestisce l'autenticazione utente e restituisce un codice di autorizzazione all'applicazione.
1. L'applicazione scambia il codice di autorizzazione per un token di accesso dal provider di identità.
1. L'applicazione usa il token di accesso per accedere alle API nel provider di identità, ad esempio un'API per la richiesta di dati di base dell'utente.

L'applicazione di esempio viene illustrato come utilizzare AUTH per implementare un flusso di autenticazione nativo contro Google. Mentre viene usato Google come provider di identità in questo argomento, l'approccio è ugualmente applicabile ad altri provider di identità. Per altre informazioni sull'autenticazione tramite endpoint di OAuth 2.0 di Google, vedere [tramite OAuth 2.0 per l'accesso Google APIs](https://developers.google.com/identity/protocols/OAuth2) sul sito Web di Google.

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
> Può essere scelto ATS fuori se non è possibile usare il `HTTPS` protocol e proteggere le comunicazioni per le risorse internet. Questo scopo, l'aggiornamento dell'app **Info. plist** file. Per altre informazioni, vedere [App Transport Security](~/ios/app-fundamentals/ats.md).

## <a name="using-xamarinauth-to-authenticate-users"></a>Utilizzando AUTH per autenticare gli utenti

AUTH supporta due approcci per le applicazioni di interagire con endpoint di autorizzazione di un provider di identità:

1. Utilizzo di una visualizzazione web incorporati. Mentre questo è stato una pratica comune, non è più consigliato per i motivi seguenti:

    - L'applicazione che ospita la visualizzazione web sono accessibili le credenziali dell'utente un'autenticazione completa, non solo la concessione di autorizzazione di OAuth che era destinata all'applicazione. Questa condizione viola il principio del privilegio minimo, come l'applicazione può accedere a credenziali più elevate rispetto a quanto richiesto, aumentando potenzialmente la superficie di attacco dell'applicazione.
    - L'applicazione host è stato possibile acquisire i nomi utente e password, automaticamente invio dei moduli e ignorare il consenso dell'utente e copiare i cookie di sessione e usarli per eseguire azioni autenticate come utente.
    - Visualizzazioni web incorporate non condividono lo stato dell'autenticazione con altre applicazioni o web browser del dispositivo, richiedere all'utente di effettuare l'accesso per ogni richiesta di autorizzazione che viene considerata come un'esperienza utente sia inferiore.
    - Alcuni endpoint di autorizzazione intervenire per rilevare e bloccare le richieste di autorizzazione che provengono da visualizzazioni web.

1. Utilizzo di web browser del dispositivo, che è l'approccio consigliato. Usando il browser del dispositivo per le richieste OAuth migliora l'usabilità di un'applicazione, come gli utenti devono solo eseguire l'accesso al provider di identità una sola volta per ogni dispositivo, migliorando i tassi di conversione dei flussi di accesso e autorizzazione nell'applicazione. Il browser del dispositivo offre inoltre una maggiore sicurezza, come le applicazioni in grado di ispezionare e modificare contenuto in una visualizzazione web, ma non il contenuto visualizzato nel browser. Questo è l'approccio adottato in questo articolo ed esempio di applicazione.

Una panoramica generale del modo in cui l'applicazione di esempio Usa AUTH per autenticare gli utenti e recuperare i dati di base è illustrata nel diagramma seguente:

![](oauth-images/google-auth.png "Using Xamarin.Auth to Authenticate with Google")

L'applicazione effettua una richiesta di autenticazione a Google usando il `OAuth2Authenticator` classe. Viene restituita una risposta di autenticazione, una volta che l'utente è autenticato correttamente con Google tramite la pagina di accesso, che include un token di accesso. Quindi, l'applicazione effettua una richiesta a Google per i dati di base dell'utente, mediante il `OAuth2Request` (classe), con il token di accesso viene incluso nella richiesta.

### <a name="setup"></a>Programma di installazione

Deve essere creato un progetto Console API Google per l'integrazione di accesso di Google con un'applicazione Xamarin.Forms. Per ottenere questo risultato, è possibile procedere come segue:

1. Andare alla [Console di Google API](https://console.developers.google.com) sito Web e accedere con le credenziali dell'account Google.
1. Dall'elenco a discesa progetto, selezionare un progetto esistente o crearne uno nuovo.
1. Nella barra laterale in "gestione API" selezionare **credenziali**, quindi selezionare la **scheda schermata di consenso OAuth**. Scegliere un **indirizzo di posta elettronica**, specificare un **nome di prodotto visualizzato dagli utenti**e quindi fare clic su **Salva**.
1. Nel **credenziali** scheda, seleziona la **creare credenziali** elenco a discesa elenco e scegliere **ID client OAuth**.
1. Sotto **tipo di applicazione**, selezionare la piattaforma a cui verrà eseguita l'applicazione per dispositivi mobili su (**iOS** oppure **Android**).
1. Compilare i dettagli necessari, quindi selezionare il **Create** pulsante.

> [!NOTE]
> Un ID Client consente a un'applicazione di accedere a Google APIs abilitata e per le applicazioni per dispositivi mobili è univoco per una singola piattaforma. Pertanto, un **ID client OAuth** deve essere creata per ogni piattaforma che userà l'accesso di Google.

Dopo aver eseguito questi passaggi, auth può essere utilizzato per avviare un flusso di autenticazione OAuth2 con Google.

### <a name="creating-and-configuring-an-authenticator"></a>Creazione e configurazione di un autenticatore

Del AUTH `OAuth2Authenticator` classe è responsabile della gestione flusso di autenticazione OAuth. Esempio di codice seguente illustra la creazione dell'istanza di `OAuth2Authenticator` classe durante l'autenticazione con web browser del dispositivo:

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

Il `OAuth2Authenticator` classe richiede un numero di parametri, ovvero come indicato di seguito:

- **ID client** : identifica il client che effettua la richiesta e può essere recuperato dal progetto nel [Console API Google](https://console.developers.google.com).
- **Segreto client** : deve trattarsi `null` o `string.Empty`.
- **Ambito** : identifica l'accesso all'API richiesto dall'applicazione e il valore indica la schermata di consenso che viene visualizzata all'utente. Per altre informazioni sugli ambiti, vedere [richiesta di autorizzazione dell'API](https://developers.google.com/+/web/api/rest/oauth) sul sito Web di Google.
- **Autorizzazione URL** – identifica l'URL dove verrà ottenuto dal codice di autorizzazione.
- **URL di reindirizzamento** – identifica l'URL in cui verrà inviata la risposta. Il valore di questo parametro deve corrispondere a uno dei valori che viene visualizzato nei **credenziali** scheda per il progetto nel [Google Developers Console](https://console.developers.google.com/).
- **AccessToken Url** – identifica l'URL usato per richiedere i token di accesso dopo aver ottenuto un codice di autorizzazione.
- **GetUserNameAsync Func** : facoltativa `Func` che verrà utilizzato per recuperare in modo asincrono il nome dell'account utente dopo che è stato autenticato correttamente.
- **Usare l'interfaccia utente nativi** : un `boolean` valore che indica se usare web browser del dispositivo per eseguire la richiesta di autenticazione.

### <a name="setup-authentication-event-handlers"></a>Configurare i gestori di eventi di autenticazione

Prima di presentare l'interfaccia utente, un gestore eventi per il `OAuth2Authenticator.Completed` evento deve essere registrato, come illustrato nell'esempio di codice seguente:

```csharp
authenticator.Completed += OnAuthCompleted;
```

Questo evento viene attivato quando l'utente viene autenticato correttamente o Annulla accesso.

Facoltativamente, un gestore eventi per il `OAuth2Authenticator.Error` eventi possono anche essere registrati.

### <a name="presenting-the-sign-in-user-interface"></a>Presentata l'interfaccia utente di accesso

L'interfaccia utente di accesso può essere presentato all'utente tramite un presentatore di account di accesso AUTH, che deve essere inizializzato in ogni progetto della piattaforma. Esempio di codice seguente viene illustrato come inizializzare un presentatore di account di accesso nel `AppDelegate` classe nel progetto iOS:

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

Esempio di codice seguente viene illustrato come inizializzare un presentatore di account di accesso nel `MainActivity` classe nel progetto Android:

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

Il progetto di libreria .NET Standard può quindi richiamare il presentatore di account di accesso come indicato di seguito:

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Si noti che l'argomento per il `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` metodo è il `OAuth2Authenticator` istanza. Quando il `Login` metodo viene richiamato, l'interfaccia utente di accesso viene presentato all'utente in una scheda da web browser del dispositivo, che viene illustrato negli screenshot seguenti:

![](oauth-images/login.png "Google Sign-In")

### <a name="processing-the-redirect-url"></a>Elaborazione dell'URL di reindirizzamento

Dopo che l'utente ha completato il processo di autenticazione, il controllo tornerà all'applicazione dalla scheda Web browser. Questa operazione viene eseguita registrando uno schema URL personalizzato per l'URL di reindirizzamento restituito dal processo di autenticazione e quindi rilevando e gestendo l'URL personalizzato dopo che è stato inviato.

Quando si sceglie uno schema URL personalizzato da associare a un'applicazione, le applicazioni devono utilizzare uno schema basato su un nome sotto il proprio controllo. Ciò può essere ottenuto utilizzando il nome di identificatore del bundle in iOS e il nome del pacchetto in Android e invertendo in modo da visualizzare lo schema URL. Tuttavia, alcuni provider di identità, ad esempio Google, assegnare gli identificatori dei client basati su nomi di dominio, che quindi sono invertiti e utilizzati come lo schema URL. Se, ad esempio, Google consente di creare un id client `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, lo schema dell'URL sarà `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`. Si noti che solo un singolo `/` possono essere visualizzati dopo il componente di schema. Pertanto, è un esempio completo di un URL di reindirizzamento che usano uno schema URL personalizzato `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`.

Quando il web browser riceve una risposta dal provider di identità che contiene uno schema URL personalizzato, prova a caricare l'URL, che avrà esito negativo. Al contrario, lo schema URL personalizzato viene segnalato al sistema operativo generando un evento. Il sistema operativo controlla quindi per gli schemi registrati e se ne viene trovato, il sistema operativo verrà avviare l'applicazione che ha registrato lo schema e inviare l'URL di reindirizzamento.

Il meccanismo per la registrazione di uno schema URL personalizzato con il sistema operativo e di schema per la gestione è specifico per ogni piattaforma.

#### <a name="ios"></a>iOS

In iOS, è registrato uno schema URL personalizzato nella **Info. plist**, come illustrato nello screenshot seguente:

![](oauth-images/info-plist.png "URL Scheme Registration")

Il **Identifier** valore può essere qualsiasi oggetto e il **ruolo** valore deve essere impostato su **Visualizzatore**. Il **schemi Url** valore, che inizia con `com.googleusercontent.apps`, è possibile ottenere dall'id client iOS per il progetto [Console API Google](https://console.developers.google.com).

Al termine di richiesta di autorizzazione, il provider di identità reindirizza all'URL di reindirizzamento dell'applicazione. Poiché l'URL viene utilizzato uno schema personalizzato comporta l'avvio dell'applicazione iOS, passando in URL come parametro di avvio, in cui viene elaborato dal `OpenUrl` eseguire l'override dell'applicazione `AppDelegate` (classe), che è illustrata nell'esempio di codice seguente:

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

Il `OpenUrl` metodo converte l'URL ricevuto da un' `NSUrl` per .NET `Uri`, prima di elaborare l'URL di reindirizzamento con il `OnPageLoading` metodo pubblico `OAuth2Authenticator` oggetto. In questo modo AUTH per chiudere la scheda del browser web e per analizzare i dati ricevuti OAuth.

#### <a name="android"></a>Android

In Android, viene registrato uno schema URL personalizzato specificando un' [ `IntentFilter` ](xref:Android.App.IntentFilterAttribute) attributo di `Activity` che consente di gestire lo schema. Al termine di richiesta di autorizzazione, il provider di identità reindirizza all'URL di reindirizzamento dell'applicazione. Come l'URL viene utilizzato uno schema personalizzato comporta l'avvio dell'applicazione Android, passando in URL come parametro di avvio, in cui viene elaborato dal `OnCreate` metodo di `Activity` registrato per gestire lo schema URL personalizzato. Esempio di codice seguente viene illustrata la classe dall'applicazione di esempio che gestisce lo schema URL personalizzato:

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

Il `DataSchemes` proprietà del [ `IntentFilter` ](xref:Android.App.IntentFilterAttribute) deve essere impostata all'identificatore del client invertito viene ottenuto dall'id client Android per il progetto sul [Console API Google](https://console.developers.google.com).

Il `OnCreate` metodo converte l'URL ricevuto da un' `Android.Net.Url` per .NET `Uri`, prima di elaborare l'URL di reindirizzamento con il `OnPageLoading` metodo pubblico `OAuth2Authenticator` oggetto. In questo modo AUTH chiudere la scheda del browser web e analizzare i dati ricevuti OAuth.

> [!IMPORTANT]
> In Android Usa AUTH il `CustomTabs` API per comunicare con il web browser e sistema operativo. Tuttavia, non è necessariamente che una `CustomTabs` browser compatibili verrà installato nel dispositivo dell'utente.

### <a name="examining-the-oauth-response"></a>Esaminare la risposta di OAuth

Dopo l'analisi di dati ricevuti OAuth, auth genererà il `OAuth2Authenticator.Completed` evento. Nel gestore eventi per questo evento, il `AuthenticatorCompletedEventArgs.IsAuthenticated` proprietà può essere utilizzata per identificare se l'autenticazione ha avuto esito positivo, come illustrato nell'esempio di codice seguente:

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

I dati raccolti da un'autenticazione riuscita sono disponibili nel `AuthenticatorCompletedEventArgs.Account` proprietà. Ciò include un token di accesso, che può essere usato per firmare le richieste per i dati a un'API fornita dal provider di identità.

### <a name="making-requests-for-data"></a>Creazione di richieste per i dati

Dopo l'applicazione ha ottenuto un token di accesso, viene usato per effettuare una richiesta per il `https://www.googleapis.com/oauth2/v2/userinfo` API, di richiedere i dati di base dell'utente dal provider di identità. Questa richiesta viene effettuata con del AUTH `OAuth2Request` classe, che rappresenta una richiesta che viene autenticata utilizzando un account recuperato da un `OAuth2Authenticator` dell'istanza, come illustrato nell'esempio di codice seguente:

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

Nonché il metodo HTTP e l'URL dell'API, il `OAuth2Request` istanza specifica inoltre un `Account` istanza che contiene il token di accesso che firma la richiesta all'URL specificato per il `Constants.UserInfoUrl` proprietà. Il provider di identità restituisce quindi i dati di base dell'utente come una risposta JSON, compresi nome degli utenti e l'indirizzo di posta elettronica, a condizione che il token di accesso viene riconosciuto come valido. La risposta JSON viene quindi letto e deserializzata di `user` variabile.

Per altre informazioni, vedere [chiama un'API di Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) sul portale agli sviluppatori di Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Archiviare e recuperare informazioni sull'Account nei dispositivi

Archivia in modo sicuro AUTH `Account` gli oggetti in un account di archiviano in modo che le applicazioni non sono sempre necessario ripetere l'autenticazione degli utenti. Il `AccountStore` classe è responsabile per l'archiviazione delle informazioni sull'account e supportata da servizi di Keychain in iOS e il `KeyStore` classe in Android.

> [!IMPORTANT]
> La classe `AccountStore` in Xamarin.auth è stata deprecata ed è invece necessario usare la classe Xamarin.Essentials `SecureStorage`. Per altre informazioni, vedere [migrazione da AccountStore a Xamarin.Essentials SecureStorage](https://github.com/xamarin/Xamarin.Auth/wiki/Migrating-from-AccountStore-to-Xamarin.Essentials-SecureStorage).

Il codice seguente esempio viene illustrato come un `Account` oggetto viene salvato in modo sicuro:

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

Salvato gli account vengano identificati univocamente usando una chiave composta dell'account di `Username` proprietà e un ID servizio, che è una stringa che viene usata quando il recupero degli account dall'archivio di account. Se un' `Account` salvato in precedenza, la chiamata di `Save` metodo nuovamente lo sovrascrive.

`Account` gli oggetti per un servizio può essere recuperato chiamando il `FindAccountsForService` metodo, come illustrato nell'esempio di codice seguente:

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

Il `FindAccountsForService` metodo restituisce un `IEnumerable` insieme di `Account` oggetti con il primo elemento nella raccolta viene impostata come account corrispondente.

## <a name="troubleshooting"></a>Risoluzione dei problemi

- In Android, se si riceve una notifica di tipo avviso popup quando si chiude il browser dopo l'autenticazione e si desidera arrestare la notifica di tipo avviso popup, aggiungere il codice seguente al progetto Android dopo l'inizializzazione di Xamarin.auth:

```csharp
Xamarin.Auth.CustomTabsConfiguration.CustomTabsClosingMessage = null;
```

- In Android, se il browser non si chiude automaticamente, una soluzione temporanea è eseguire il downgrade del pacchetto Xamarin.auth alla versione 1.5.0.3. Quindi, aggiungere [PCL Crypto v 2.0.147](https://www.nuget.org/packages/PCLCrypto/2.0.147) al progetto Android.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare AUTH per gestire il processo di autenticazione in un'applicazione Xamarin.Forms. AUTH fornisce il `OAuth2Authenticator` e `OAuth2Request` classi utilizzate dalle applicazioni Xamarin.Forms per utilizzare i provider di identità, ad esempio Google, Microsoft, Facebook e Twitter.

## <a name="related-links"></a>Collegamenti correlati

- [OAuthNativeFlow (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)
- [OAuth 2.0 per le app Native](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [Uso di OAuth 2.0 per accedere alle API di Google](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin.Auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin.Auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
