---
title: AuthenticateUsers con un provider di identità
description: Questo articolo illustra come usare AUTH per gestire il processo di autenticazione in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 0fa433de7fd1acb6fb27741f1615a644315f373f
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291648"
---
# <a name="authenticate-users-with-an-identity-provider"></a>Autenticare gli utenti con un provider di identità

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)

_Novell. auth è un SDK multipiattaforma per l'autenticazione degli utenti e l'archiviazione dei relativi account. Sono inclusi gli autenticatori OAuth che forniscono supporto per l'utilizzo di provider di identità quali Google, Microsoft, Facebook e Twitter. Questo articolo illustra come usare Novell. auth per gestire il processo di autenticazione in un'applicazione Novell. Forms._

OAuth è uno standard aperto per l'autenticazione e consente a un proprietario della risorsa notificare a un provider di risorse che deve essere concesso l'autorizzazione a terze parti per accedere alle informazioni senza condividere l'identità dei proprietari di risorse. Un esempio di questo sarebbe possibile consentire a un utente notificare a un provider di identità (ad esempio, Google, Microsoft, Facebook o Twitter) che deve essere concesso l'autorizzazione a un'applicazione per accedere ai dati, senza condividere l'identità dell'utente. Viene comunemente utilizzato come un approccio per gli utenti per l'accesso ai siti Web e alle applicazioni tramite un provider di identità, ma senza dover esporre la password per il sito Web o l'applicazione.

Una panoramica generale del flusso di autenticazione quando si utilizza un provider di identità OAuth è il seguente:

1. L'applicazione consente di passare un browser all'URL di un provider di identità.
1. Il provider di identità gestisce l'autenticazione utente e restituisce un codice di autorizzazione all'applicazione.
1. L'applicazione scambia il codice di autorizzazione per un token di accesso dal provider di identità.
1. L'applicazione usa il token di accesso per accedere alle API nel provider di identità, ad esempio un'API per la richiesta di dati di base dell'utente.

L'applicazione di esempio viene illustrato come utilizzare AUTH per implementare un flusso di autenticazione nativo contro Google. Mentre viene usato Google come provider di identità in questo argomento, l'approccio è ugualmente applicabile ad altri provider di identità. Per altre informazioni sull'autenticazione con l'endpoint OAuth 2,0 di Google, vedere [uso di OAuth 2.0 per accedere alle API Google](https://developers.google.com/identity/protocols/OAuth2) nel sito Web di Google.

> [!NOTE]
> In iOS 9 e versioni successive, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app, evitando la diffusione accidentale di informazioni riservate. Poiché ATS è abilitata per impostazione predefinita nelle App per iOS 9, tutte le connessioni saranno soggetti a requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.
> È possibile escludere ATS da se non è possibile usare il protocollo `HTTPS` e proteggere le comunicazioni per le risorse Internet. Questa operazione può essere eseguita aggiornando il file **info. plist** dell'app. Per altre informazioni, vedere [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md).

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

L'applicazione effettua una richiesta di autenticazione a Google usando la classe `OAuth2Authenticator`. Viene restituita una risposta di autenticazione, una volta che l'utente è autenticato correttamente con Google tramite la pagina di accesso, che include un token di accesso. L'applicazione esegue quindi una richiesta a Google per i dati utente di base, usando la classe `OAuth2Request`, con il token di accesso incluso nella richiesta.

### <a name="setup"></a>Programma di installazione

Deve essere creato un progetto Console API Google per l'integrazione di accesso di Google con un'applicazione xamarin. Forms. Per ottenere questo risultato, è possibile procedere come segue:

1. Accedere al sito Web della [console API Google](https://console.developers.google.com) e accedere con le credenziali dell'account Google.
1. Dall'elenco a discesa progetto, selezionare un progetto esistente o crearne uno nuovo.
1. Nella barra laterale in "gestione API" selezionare **credenziali**, quindi selezionare la **scheda schermata di consenso OAuth**. Scegliere un **indirizzo di posta elettronica**, specificare un **nome di prodotto visualizzato dagli utenti**e quindi fare clic su **Salva**.
1. Nella scheda **credenziali** selezionare l'elenco a discesa **Crea credenziali** e scegliere **ID client OAuth**.
1. In **tipo di applicazione**selezionare la piattaforma in cui verrà eseguita l'applicazione per dispositivi mobili (**iOS** o **Android**).
1. Immettere i dettagli richiesti e selezionare il pulsante **Crea** .

> [!NOTE]
> Un ID Client consente a un'applicazione di accedere a Google APIs abilitata e per le applicazioni per dispositivi mobili è univoco per una singola piattaforma. Pertanto, è necessario creare un **ID client OAuth** per ogni piattaforma che userà l'accesso a Google.

Dopo aver eseguito questi passaggi, auth può essere utilizzato per avviare un flusso di autenticazione OAuth2 con Google.

### <a name="creating-and-configuring-an-authenticator"></a>Creazione e configurazione di un autenticatore

La classe `OAuth2Authenticator` di Novell. auth è responsabile della gestione del flusso di autenticazione OAuth. Nell'esempio di codice seguente viene illustrata la creazione di un'istanza della classe `OAuth2Authenticator` quando si esegue l'autenticazione tramite il Web browser del dispositivo:

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

La classe `OAuth2Authenticator` richiede un numero di parametri, come indicato di seguito:

- **ID client** : identifica il client che effettua la richiesta e può essere recuperato dal progetto nella [console API Google](https://console.developers.google.com).
- **Segreto client** : deve essere `null` o `string.Empty`.
- **Ambito** : identifica l'accesso all'API richiesto dall'applicazione e il valore informa la schermata di consenso visualizzata all'utente. Per ulteriori informazioni sugli ambiti, vedere [autorizzare le richieste](https://developers.google.com/docs/api/how-tos/authorizing) nel sito Web di Google.
- **Autorizza URL** : identifica l'URL da cui verrà ottenuto il codice di autorizzazione.
- **URL di reindirizzamento** : identifica l'URL in cui verrà inviata la risposta. Il valore di questo parametro deve corrispondere a uno dei valori visualizzati nella scheda **credenziali** per il progetto in [Google Developers Console](https://console.developers.google.com/).
- **URL di AccessToken** : identifica l'URL usato per richiedere i token di accesso dopo che è stato ottenuto un codice di autorizzazione.
- **GetUserNameAsync Func** : `Func` facoltativo che verrà usato per recuperare in modo asincrono il nome utente dell'account dopo che è stato autenticato correttamente.
- **Usa interfaccia utente nativa** : valore `boolean` che indica se usare il Web browser del dispositivo per eseguire la richiesta di autenticazione.

### <a name="setup-authentication-event-handlers"></a>Configurare i gestori di eventi di autenticazione

Prima di presentare l'interfaccia utente, è necessario registrare un gestore eventi per l'evento `OAuth2Authenticator.Completed`, come illustrato nell'esempio di codice seguente:

```csharp
authenticator.Completed += OnAuthCompleted;
```

Questo evento viene attivato quando l'utente viene autenticato correttamente o Annulla accesso.

Facoltativamente, è possibile registrare anche un gestore eventi per l'evento `OAuth2Authenticator.Error`.

### <a name="presenting-the-sign-in-user-interface"></a>Presentata l'interfaccia utente di accesso

L'interfaccia utente di accesso può essere presentato all'utente tramite un presentatore di account di accesso AUTH, che deve essere inizializzato in ogni progetto della piattaforma. L'esempio di codice seguente illustra come inizializzare un presentatore di accesso nella classe `AppDelegate` nel progetto iOS:

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

L'esempio di codice seguente illustra come inizializzare un presentatore di accesso nella classe `MainActivity` nel progetto Android:

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

Il progetto di libreria .NET Standard può quindi richiamare il presentatore di account di accesso come indicato di seguito:

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Si noti che l'argomento per il metodo `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` è l'istanza di `OAuth2Authenticator`. Quando viene richiamato il metodo `Login`, l'interfaccia utente di accesso viene presentata all'utente in una scheda dal Web browser del dispositivo, come illustrato nelle schermate seguenti:

![](oauth-images/login.png "Google Sign-In")

### <a name="processing-the-redirect-url"></a>Elaborazione dell'URL di reindirizzamento

Dopo che l'utente ha completato il processo di autenticazione, il controllo tornerà all'applicazione dalla scheda Web browser. Questa operazione viene eseguita registrando uno schema URL personalizzato per l'URL di reindirizzamento restituito dal processo di autenticazione e quindi rilevando e gestendo l'URL personalizzato dopo che è stato inviato.

Quando si sceglie uno schema URL personalizzato da associare a un'applicazione, le applicazioni devono utilizzare uno schema basato su un nome sotto il proprio controllo. Ciò può essere ottenuto utilizzando il nome di identificatore del bundle in iOS e il nome del pacchetto in Android e invertendo in modo da visualizzare lo schema URL. Tuttavia, alcuni provider di identità, ad esempio Google, assegnare gli identificatori dei client basati su nomi di dominio, che quindi sono invertiti e utilizzati come lo schema URL. Se, ad esempio, Google crea un ID client di `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, lo schema URL verrà `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`. Si noti che dopo il componente dello schema può essere presente una sola `/`. Pertanto, un esempio completo di URL di reindirizzamento che utilizza uno schema URL personalizzato è `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`.

Quando il web browser riceve una risposta dal provider di identità che contiene uno schema URL personalizzato, prova a caricare l'URL, che avrà esito negativo. Al contrario, lo schema URL personalizzato viene segnalato al sistema operativo generando un evento. Il sistema operativo controlla quindi per gli schemi registrati e se ne viene trovato, il sistema operativo verrà avviare l'applicazione che ha registrato lo schema e inviare l'URL di reindirizzamento.

Il meccanismo per la registrazione di uno schema URL personalizzato con il sistema operativo e di schema per la gestione è specifico per ogni piattaforma.

#### <a name="ios"></a>iOS

In iOS viene registrato uno schema URL personalizzato in **info. plist**, come illustrato nello screenshot seguente:

![](oauth-images/info-plist.png "URL Scheme Registration")

Il valore dell' **identificatore** può essere qualsiasi elemento e il valore del **ruolo** deve essere impostato su **Visualizzatore**. Il valore degli **schemi URL** , che inizia con `com.googleusercontent.apps`, può essere ottenuto dall'ID client iOS per il progetto nella [console API Google](https://console.developers.google.com).

Al termine di richiesta di autorizzazione, il provider di identità reindirizza all'URL di reindirizzamento dell'applicazione. Poiché l'URL usa uno schema personalizzato, viene avviata l'applicazione iOS, passando l'URL come parametro di avvio, in cui viene elaborato dal `OpenUrl` override della classe `AppDelegate` dell'applicazione, illustrato nell'esempio di codice seguente:

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

Il metodo `OpenUrl` converte l'URL ricevuto da un `NSUrl` a un `Uri`.NET, prima di elaborare l'URL di reindirizzamento con il metodo `OnPageLoading` di un oggetto `OAuth2Authenticator` pubblico. In questo modo AUTH per chiudere la scheda del browser web e per analizzare i dati ricevuti OAuth.

#### <a name="android"></a>Android

In Android, uno schema URL personalizzato viene registrato specificando un [`IntentFilter`](xref:Android.App.IntentFilterAttribute) attributo sul `Activity` che gestirà lo schema. Al termine di richiesta di autorizzazione, il provider di identità reindirizza all'URL di reindirizzamento dell'applicazione. Poiché l'URL usa uno schema personalizzato, l'applicazione viene avviata da Android, passando l'URL come parametro di avvio, in cui viene elaborato dal `OnCreate` metodo del `Activity` registrato per gestire lo schema dell'URL personalizzato. Esempio di codice seguente viene illustrata la classe dall'applicazione di esempio che gestisce lo schema URL personalizzato:

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

È necessario impostare la proprietà `DataSchemes` della [`IntentFilter`](xref:Android.App.IntentFilterAttribute) sull'identificatore client invertito ottenuto dall'ID client Android per il progetto nella [console API Google](https://console.developers.google.com).

Il metodo `OnCreate` converte l'URL ricevuto da un `Android.Net.Url` a un `Uri`.NET, prima di elaborare l'URL di reindirizzamento con il metodo `OnPageLoading` di un oggetto `OAuth2Authenticator` pubblico. In questo modo AUTH chiudere la scheda del browser web e analizzare i dati ricevuti OAuth.

> [!IMPORTANT]
> In Android, Novell. auth usa l'API `CustomTabs` per comunicare con il browser Web e il sistema operativo. Tuttavia, non è garantito che nel dispositivo dell'utente venga installato un browser compatibile con `CustomTabs`.

### <a name="examining-the-oauth-response"></a>Esaminare la risposta di OAuth

Dopo aver analizzato i dati OAuth ricevuti, Novell. auth genererà l'evento `OAuth2Authenticator.Completed`. Nel gestore eventi per questo evento, è possibile usare la proprietà `AuthenticatorCompletedEventArgs.IsAuthenticated` per determinare se l'autenticazione ha avuto esito positivo, come illustrato nell'esempio di codice seguente:

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

I dati raccolti da un'autenticazione con esito positivo sono disponibili nella proprietà `AuthenticatorCompletedEventArgs.Account`. Ciò include un token di accesso, che può essere usato per firmare le richieste per i dati a un'API fornita dal provider di identità.

### <a name="making-requests-for-data"></a>Creazione di richieste per i dati

Quando l'applicazione ottiene un token di accesso, viene usato per effettuare una richiesta all'API `https://www.googleapis.com/oauth2/v2/userinfo` per richiedere dati utente di base dal provider di identità. Questa richiesta viene effettuata con la classe `OAuth2Request` di Novell. auth, che rappresenta una richiesta autenticata utilizzando un account recuperato da un'istanza di `OAuth2Authenticator`, come illustrato nell'esempio di codice seguente:

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

Così come il metodo HTTP e l'URL dell'API, l'istanza di `OAuth2Request` specifica anche un'istanza di `Account` che contiene il token di accesso che firma la richiesta all'URL specificato dalla proprietà `Constants.UserInfoUrl`. Il provider di identità restituisce quindi i dati di base dell'utente come una risposta JSON, compresi nome degli utenti e l'indirizzo di posta elettronica, a condizione che il token di accesso viene riconosciuto come valido. La risposta JSON viene quindi letta e deserializzata nella variabile `user`.

Per altre informazioni, vedere [chiamata di un'API Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) nel portale per sviluppatori Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Archiviare e recuperare informazioni sull'Account nei dispositivi

Novell. auth archivia in modo sicuro `Account` gli oggetti in un archivio account, in modo che le applicazioni non debbano sempre autenticare di nuovo gli utenti. La classe `AccountStore` è responsabile dell'archiviazione delle informazioni sull'account ed è supportata dai servizi keychain in iOS e dalla classe `KeyStore` in Android.

> [!IMPORTANT]
> La classe `AccountStore` in Novell. auth è stata deprecata ed è invece necessario usare la classe Novell. Essentials `SecureStorage`. Per altre informazioni, vedere [migrazione da AccountStore a Novell. Essentials SecureStorage](https://github.com/xamarin/Xamarin.Auth/wiki/Migrating-from-AccountStore-to-Xamarin.Essentials-SecureStorage).

Nell'esempio di codice seguente viene illustrato come salvare in modo sicuro un oggetto `Account`:

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

Gli account salvati vengono identificati in modo univoco mediante una chiave composta dalla proprietà `Username` dell'account e un ID servizio, ovvero una stringa utilizzata durante il recupero degli account dall'archivio account. Se una `Account` è stata salvata in precedenza, la chiamata del metodo `Save` lo sovrascriverà.

è possibile recuperare gli oggetti `Account` per un servizio chiamando il metodo `FindAccountsForService`, come illustrato nell'esempio di codice seguente:

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

Il metodo `FindAccountsForService` restituisce una raccolta `IEnumerable` di oggetti `Account`, con il primo elemento della raccolta da impostare come account corrispondente.

## <a name="troubleshooting"></a>Risoluzione dei problemi

- In Android, se si riceve una notifica di tipo avviso popup quando si chiude il browser dopo l'autenticazione e si desidera arrestare la notifica di tipo avviso popup, aggiungere il codice seguente al progetto Android dopo l'inizializzazione di Novell. auth:

```csharp
Xamarin.Auth.CustomTabsConfiguration.CustomTabsClosingMessage = null;
```

- In Android, se il browser non si chiude automaticamente, una soluzione temporanea è eseguire il downgrade del pacchetto Novell. auth alla versione 1.5.0.3. Quindi, aggiungere [PCL Crypto v 2.0.147](https://www.nuget.org/packages/PCLCrypto/2.0.147) al progetto Android.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare AUTH per gestire il processo di autenticazione in un'applicazione xamarin. Forms. Novell. auth fornisce le classi `OAuth2Authenticator` e `OAuth2Request` utilizzate dalle applicazioni Novell. Forms per l'utilizzo di provider di identità quali Google, Microsoft, Facebook e Twitter.

## <a name="related-links"></a>Collegamenti correlati

- [OAuthNativeFlow (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)
- [OAuth 2,0 per app native](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [Uso di OAuth 2.0 per accedere alle API Google](https://developers.google.com/identity/protocols/OAuth2)
- [Novell. auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Novell. auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
