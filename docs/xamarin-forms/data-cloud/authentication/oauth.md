---
title: AuthenticateUsers con un provider di identità
description: Questo articolo illustra come usare Novell. auth per gestire il processo di autenticazione in un'applicazione Novell. Forms.
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 83fbad8a9bbb9afef5ee80705fe9e86e51284e7d
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842989"
---
# <a name="authenticate-users-with-an-identity-provider"></a>Autenticare gli utenti con un provider di identità

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)

_Novell. auth è un SDK multipiattaforma per l'autenticazione degli utenti e l'archiviazione dei relativi account. Sono inclusi gli autenticatori OAuth che forniscono supporto per l'utilizzo di provider di identità quali Google, Microsoft, Facebook e Twitter. Questo articolo illustra come usare Novell. auth per gestire il processo di autenticazione in un'applicazione Novell. Forms._

OAuth è uno standard aperto per l'autenticazione e consente a un proprietario di risorse di notificare a un provider di risorse che l'autorizzazione deve essere concessa a terze parti per accedere alle informazioni senza condividere l'identità dei proprietari delle risorse. Un esempio potrebbe consentire a un utente di notificare a un provider di identità, ad esempio Google, Microsoft, Facebook o Twitter, che l'autorizzazione deve essere concessa a un'applicazione per accedere ai dati, senza condividere l'identità dell'utente. Viene comunemente usato come approccio per consentire agli utenti di accedere a siti Web e applicazioni usando un provider di identità, ma senza esporre la password al sito Web o all'applicazione.

Una panoramica di alto livello del flusso di autenticazione quando si usa un provider di identità OAuth è la seguente:

1. L'applicazione passa a un browser per un URL del provider di identità.
1. Il provider di identità gestisce l'autenticazione utente e restituisce un codice di autorizzazione all'applicazione.
1. L'applicazione scambia il codice di autorizzazione per un token di accesso dal provider di identità.
1. L'applicazione usa il token di accesso per accedere alle API del provider di identità, ad esempio un'API per la richiesta di dati utente di base.

L'applicazione di esempio illustra come usare Novell. auth per implementare un flusso di autenticazione nativo per Google. Mentre Google viene usato come provider di identità in questo argomento, l'approccio è applicabile anche ad altri provider di identità. Per altre informazioni sull'autenticazione con l'endpoint OAuth 2,0 di Google, vedere [uso di OAuth 2.0 per accedere alle API Google](https://developers.google.com/identity/protocols/OAuth2) nel sito Web di Google.

> [!NOTE]
> In iOS 9 e versioni successive, la sicurezza del trasporto app impone connessioni sicure tra le risorse Internet (ad esempio il server back-end dell'app) e l'app, impedendo così la divulgazione accidentale di informazioni riservate. Poiché ATS è abilitato per impostazione predefinita nelle app compilate per iOS 9, tutte le connessioni saranno soggette ai requisiti di sicurezza di ATS. Se le connessioni non soddisfano questi requisiti, avranno esito negativo con un'eccezione.
> È possibile escludere ATS da se non è possibile usare il protocollo `HTTPS` e proteggere le comunicazioni per le risorse Internet. Questa operazione può essere eseguita aggiornando il file **info. plist** dell'app. Per altre informazioni, vedere [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md).

## <a name="using-xamarinauth-to-authenticate-users"></a>Uso di Novell. auth per autenticare gli utenti

Novell. auth supporta due approcci che consentono alle applicazioni di interagire con un endpoint di autorizzazione del provider di identità:

1. Utilizzo di una visualizzazione Web incorporata. Sebbene si trattasse di una pratica comune, non è più consigliabile per i motivi seguenti:

    - L'applicazione che ospita la visualizzazione Web può accedere alle credenziali di autenticazione complete dell'utente e non solo alla concessione di autorizzazione OAuth destinata all'applicazione. Questo viola il principio dei privilegi minimi, perché l'applicazione ha accesso a credenziali più potenti rispetto a quanto richiesto, aumentando potenzialmente la superficie di attacco dell'applicazione.
    - L'applicazione host può acquisire nomi utente e password, inviare automaticamente moduli e ignorare il consenso dell'utente e copiare i cookie di sessione e usarli per eseguire azioni autenticate come utente.
    - Le visualizzazioni Web incorporate non condividono lo stato di autenticazione con altre applicazioni o il Web browser del dispositivo, richiedendo all'utente di eseguire l'accesso per ogni richiesta di autorizzazione considerata un'esperienza utente inferiore.
    - Alcuni endpoint di autorizzazione accettano i passaggi per rilevare e bloccare le richieste di autorizzazione provenienti da visualizzazioni Web.

1. Usando il Web browser del dispositivo, che è l'approccio consigliato. L'uso di Device browser per le richieste OAuth migliora l'usabilità di un'applicazione, in quanto gli utenti devono solo accedere al provider di identità una volta per ogni dispositivo, migliorando i tassi di conversione dei flussi di accesso e autorizzazione nell'applicazione. Il browser per dispositivi fornisce inoltre una maggiore sicurezza, in quanto le applicazioni sono in grado di controllare e modificare il contenuto di una visualizzazione Web, ma non il contenuto visualizzato nel browser. Questo è l'approccio adottato in questo articolo e nell'applicazione di esempio.

Il diagramma seguente illustra una panoramica generale del modo in cui l'applicazione di esempio usa Novell. auth per autenticare gli utenti e recuperare i dati di base.

![](oauth-images/google-auth.png "Using Xamarin.Auth to Authenticate with Google")

L'applicazione effettua una richiesta di autenticazione a Google usando la classe `OAuth2Authenticator`. Viene restituita una risposta di autenticazione, dopo che l'utente ha eseguito correttamente l'autenticazione con Google tramite la pagina di accesso, che include un token di accesso. L'applicazione esegue quindi una richiesta a Google per i dati utente di base, usando la classe `OAuth2Request`, con il token di accesso incluso nella richiesta.

### <a name="setup"></a>Configurazione

Per integrare Google Sign-in con un'applicazione Novell. Forms, è necessario creare un progetto console API Google. Per ottenere questo risultato, è possibile procedere come segue:

1. Accedere al sito Web della [console API Google](https://console.developers.google.com) e accedere con le credenziali dell'account Google.
1. Dall'elenco a discesa progetto selezionare un progetto esistente o crearne uno nuovo.
1. Nella barra laterale in "gestione API" selezionare **credenziali**, quindi selezionare la **scheda schermata di consenso OAuth**. Scegliere un **indirizzo di posta elettronica**, specificare un **nome di prodotto visualizzato dagli utenti**e quindi fare clic su **Salva**.
1. Nella scheda **credenziali** selezionare l'elenco a discesa **Crea credenziali** e scegliere **ID client OAuth**.
1. In **tipo di applicazione**selezionare la piattaforma in cui verrà eseguita l'applicazione per dispositivi mobili (**iOS** o **Android**).
1. Immettere i dettagli richiesti e selezionare il pulsante **Crea** .

> [!NOTE]
> Un ID client consente a un'applicazione di accedere alle API Google e per le applicazioni per dispositivi mobili è univoca per un'unica piattaforma. Pertanto, è necessario creare un **ID client OAuth** per ogni piattaforma che userà l'accesso a Google.

Dopo aver eseguito questi passaggi, è possibile usare Novell. auth per avviare un flusso di autenticazione OAuth2 con Google.

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
- **Ambito** : identifica l'accesso all'API richiesto dall'applicazione e il valore informa la schermata di consenso visualizzata all'utente. Per ulteriori informazioni sugli ambiti, vedere [autorizzazione della richiesta API](https://developers.google.com/+/web/api/rest/oauth) nel sito Web di Google.
- **Autorizza URL** : identifica l'URL da cui verrà ottenuto il codice di autorizzazione.
- **URL di reindirizzamento** : identifica l'URL in cui verrà inviata la risposta. Il valore di questo parametro deve corrispondere a uno dei valori visualizzati nella scheda **credenziali** per il progetto in [Google Developers Console](https://console.developers.google.com/).
- **URL di AccessToken** : identifica l'URL usato per richiedere i token di accesso dopo che è stato ottenuto un codice di autorizzazione.
- **GetUserNameAsync Func** : `Func` facoltativo che verrà usato per recuperare in modo asincrono il nome utente dell'account dopo che è stato autenticato correttamente.
- **Usa interfaccia utente nativa** : valore `boolean` che indica se usare il Web browser del dispositivo per eseguire la richiesta di autenticazione.

### <a name="setup-authentication-event-handlers"></a>Configurare i gestori eventi di autenticazione

Prima di presentare l'interfaccia utente, è necessario registrare un gestore eventi per l'evento `OAuth2Authenticator.Completed`, come illustrato nell'esempio di codice seguente:

```csharp
authenticator.Completed += OnAuthCompleted;
```

Questo evento viene generato quando l'utente autentica o Annulla l'accesso.

Facoltativamente, è possibile registrare anche un gestore eventi per l'evento `OAuth2Authenticator.Error`.

### <a name="presenting-the-sign-in-user-interface"></a>Presentazione dell'interfaccia utente di accesso

L'interfaccia utente di accesso può essere presentata all'utente usando un Novell. auth login Presenter, che deve essere inizializzato in ogni progetto di piattaforma. L'esempio di codice seguente illustra come inizializzare un presentatore di accesso nella classe `AppDelegate` nel progetto iOS:

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

L'esempio di codice seguente illustra come inizializzare un presentatore di accesso nella classe `MainActivity` nel progetto Android:

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

Il progetto di libreria .NET Standard può quindi richiamare il presentatore di accesso come segue:

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

Si noti che l'argomento per il metodo `Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` è l'istanza di `OAuth2Authenticator`. Quando viene richiamato il metodo `Login`, l'interfaccia utente di accesso viene presentata all'utente in una scheda dal Web browser del dispositivo, come illustrato nelle schermate seguenti:

![](oauth-images/login.png "Google Sign-In")

### <a name="processing-the-redirect-url"></a>Elaborazione dell'URL di Reindirizzamento

Dopo che l'utente ha completato il processo di autenticazione, il controllo tornerà all'applicazione dalla scheda Web browser. Questa operazione viene eseguita registrando uno schema URL personalizzato per l'URL di reindirizzamento restituito dal processo di autenticazione e quindi rilevando e gestendo l'URL personalizzato dopo che è stato inviato.

Quando si sceglie uno schema URL personalizzato da associare a un'applicazione, le applicazioni devono usare uno schema basato su un nome sotto il controllo. A tale scopo, è possibile usare il nome dell'identificatore del bundle in iOS e il nome del pacchetto in Android, quindi invertirli per creare lo schema URL. Tuttavia, alcuni provider di identità, ad esempio Google, assegnano identificatori client basati sui nomi di dominio, che vengono quindi invertiti e usati come schema URL. Se, ad esempio, Google crea un ID client di `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`, lo schema URL verrà `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`. Si noti che dopo il componente dello schema può essere presente una sola `/`. Pertanto, un esempio completo di URL di reindirizzamento che utilizza uno schema URL personalizzato è `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`.

Quando il Web browser riceve una risposta dal provider di identità che contiene uno schema URL personalizzato, tenta di caricare l'URL, operazione che avrà esito negativo. Lo schema URL personalizzato viene invece segnalato al sistema operativo mediante la generazione di un evento. Il sistema operativo verifica quindi la presenza di schemi registrati. se ne viene individuato uno, il sistema operativo avvierà l'applicazione che ha registrato lo schema e invierà l'URL di reindirizzamento.

Il meccanismo per la registrazione di uno schema URL personalizzato con il sistema operativo e la gestione dello schema è specifico per ogni piattaforma.

#### <a name="ios"></a>iOS

In iOS viene registrato uno schema URL personalizzato in **info. plist**, come illustrato nello screenshot seguente:

![](oauth-images/info-plist.png "URL Scheme Registration")

Il valore dell' **identificatore** può essere qualsiasi elemento e il valore del **ruolo** deve essere impostato su **Visualizzatore**. Il valore degli **schemi URL** , che inizia con `com.googleusercontent.apps`, può essere ottenuto dall'ID client iOS per il progetto nella [console API Google](https://console.developers.google.com).

Quando il provider di identità completa la richiesta di autorizzazione, reindirizza all'URL di reindirizzamento dell'applicazione. Poiché l'URL usa uno schema personalizzato, viene avviata l'applicazione iOS, passando l'URL come parametro di avvio, in cui viene elaborato dal `OpenUrl` override della classe `AppDelegate` dell'applicazione, illustrato nell'esempio di codice seguente. :

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

Il metodo `OpenUrl` converte l'URL ricevuto da un `NSUrl` a un `Uri`.NET, prima di elaborare l'URL di reindirizzamento con il metodo `OnPageLoading` di un oggetto `OAuth2Authenticator` pubblico. Questo fa sì che Novell. auth chiuda la scheda del Web browser e analizzi i dati OAuth ricevuti.

#### <a name="android"></a>Android

In Android, uno schema URL personalizzato viene registrato specificando un [`IntentFilter`](xref:Android.App.IntentFilterAttribute) attributo sul `Activity` che gestirà lo schema. Quando il provider di identità completa la richiesta di autorizzazione, reindirizza all'URL di reindirizzamento dell'applicazione. Poiché l'URL usa uno schema personalizzato, l'applicazione viene avviata da Android, passando l'URL come parametro di avvio, in cui viene elaborato dal `OnCreate` metodo del `Activity` registrato per gestire lo schema dell'URL personalizzato. Nell'esempio di codice seguente viene illustrata la classe dell'applicazione di esempio che gestisce lo schema URL personalizzato:

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

Il metodo `OnCreate` converte l'URL ricevuto da un `Android.Net.Url` a un `Uri`.NET, prima di elaborare l'URL di reindirizzamento con il metodo `OnPageLoading` di un oggetto `OAuth2Authenticator` pubblico. Questo fa sì che Novell. auth chiuda la scheda del Web browser e analizzi i dati OAuth ricevuti.

> [!IMPORTANT]
> In Android, Novell. auth usa l'API `CustomTabs` per comunicare con il browser Web e il sistema operativo. Tuttavia, non è garantito che nel dispositivo dell'utente venga installato un browser compatibile con `CustomTabs`.

### <a name="examining-the-oauth-response"></a>Esame della risposta OAuth

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

I dati raccolti da un'autenticazione con esito positivo sono disponibili nella proprietà `AuthenticatorCompletedEventArgs.Account`. Include un token di accesso, che può essere usato per firmare le richieste di dati a un'API fornita dal provider di identità.

### <a name="making-requests-for-data"></a>Esecuzione di richieste di dati

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

Così come il metodo HTTP e l'URL dell'API, l'istanza di `OAuth2Request` specifica anche un'istanza di `Account` che contiene il token di accesso che firma la richiesta all'URL specificato dalla proprietà `Constants.UserInfoUrl`. Il provider di identità restituisce quindi i dati utente di base come risposta JSON, inclusi il nome e l'indirizzo di posta elettronica degli utenti, purché riconosca il token di accesso come valido. La risposta JSON viene quindi letta e deserializzata nella variabile `user`.

Per altre informazioni, vedere [chiamata di un'API Google](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) nel portale per sviluppatori Google.

### <a name="storing-and-retrieving-account-information-on-devices"></a>Archiviazione e recupero delle informazioni sull'account nei dispositivi

Novell. auth archivia in modo sicuro `Account` gli oggetti in un archivio account, in modo che le applicazioni non debbano sempre autenticare di nuovo gli utenti. La classe `AccountStore` è responsabile dell'archiviazione delle informazioni sull'account ed è supportata dai servizi keychain in iOS e dalla classe `KeyStore` in Android.

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

## <a name="troubleshooting"></a>Troubleshooting

- In Android, se si riceve una notifica di tipo avviso popup quando si chiude il browser dopo l'autenticazione e si desidera arrestare la notifica di tipo avviso popup, aggiungere il codice seguente al progetto Android dopo l'inizializzazione di Novell. auth:

```csharp
Xamarin.Auth.CustomTabsConfiguration.CustomTabsClosingMessage = null;
```

- In Android, se il browser non si chiude automaticamente, una soluzione temporanea è eseguire il downgrade del pacchetto Novell. auth alla versione 1.5.0.3. Quindi, aggiungere [PCL Crypto v 2.0.147](https://www.nuget.org/packages/PCLCrypto/2.0.147) al progetto Android.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare Novell. auth per gestire il processo di autenticazione in un'applicazione Novell. Forms. Novell. auth fornisce le classi `OAuth2Authenticator` e `OAuth2Request` utilizzate dalle applicazioni Novell. Forms per l'utilizzo di provider di identità quali Google, Microsoft, Facebook e Twitter.

## <a name="related-links"></a>Collegamenti correlati

- [OAuthNativeFlow (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)
- [OAuth 2,0 per app native](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [Uso di OAuth 2.0 per accedere alle API Google](https://developers.google.com/identity/protocols/OAuth2)
- [Novell. auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Novell. auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
