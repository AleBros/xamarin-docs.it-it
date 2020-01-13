---
title: Autenticare gli utenti con un database di documenti Azure Cosmos DB e Xamarin.Forms
description: Questo articolo illustra come combinare il controllo di accesso con le raccolte partizionate di Azure Cosmos DB, in modo che un utente può accedere solo ai propri documenti in un'applicazione Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 64209f905ba07f7efc7368b8f054dfc3ae606af2
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489986"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>Autenticare gli utenti con un database di documenti Azure Cosmos DB e Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Azure Cosmos DB database di documenti supportano le raccolte partizionate, che possono estendersi su più server e partizioni, supportando allo stesso tempo archiviazione e velocità effettiva illimitate. Questo articolo illustra come combinare il controllo di accesso con le raccolte partizionate, in modo che un utente possa accedere solo ai propri documenti in un'applicazione Xamarin.Forms._

## <a name="overview"></a>Panoramica di

Quando si crea una raccolta partizionata è necessario specificare una chiave di partizione e i documenti con la stessa chiave di partizione verranno archiviati nella stessa partizione. Pertanto, se si specifica l'identità dell'utente come chiave di partizione otterrà in una raccolta partizionata in cui verrà archiviati solo i documenti per l'utente. Questo processo assicura anche che il database di documenti di Azure Cosmos DB scalabili in base al numero di utenti e aumentare gli elementi.

Necessario concedere l'accesso a qualsiasi raccolta e il modello di controllo di accesso SQL API definisce due tipi di costrutti di accesso:

- **Le chiavi master** abilitare l'accesso amministrativo completo a tutte le risorse all'interno di un account Cosmos DB e vengono create quando viene creato un account Cosmos DB.
- **I token delle risorse** acquisiscono la relazione tra l'utente di un database e dell'autorizzazione l'utente dispone per una risorsa di Cosmos DB specifica, ad esempio una raccolta o in un documento.

Esposizione di una chiave master viene aperto un account Cosmos DB la possibilità di usare dannoso o non appropriata. Tuttavia, i token delle risorse di Azure Cosmos DB offrono un meccanismo sicuro per consentire ai client di leggere, scrivere ed eliminare risorse specifiche in un account Azure Cosmos DB in base alle autorizzazioni concesse.

Un approccio tipico per la richiesta, la generazione e il recapito di token delle risorse a un'applicazione per dispositivi mobili è usare un gestore di token di risorsa. Il diagramma seguente mostra una panoramica generale del modo in cui l'applicazione di esempio Usa un gestore di token di risorsa per gestire l'accesso ai dati del database di documenti:

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Document Database Authentication Process")

Il gestore di token di risorsa è un servizio API Web di livello intermedio, ospitato nel servizio App di Azure, che possiede la chiave master dell'account Cosmos DB. L'applicazione di esempio Usa il gestore di token di risorsa per gestire l'accesso ai dati del database documenti come indicato di seguito:

1. Account di accesso, l'applicazione Xamarin.Forms contatta il servizio App di Azure per avviare un flusso di autenticazione.
1. Servizio App di Azure esegue un flusso di autenticazione OAuth con Facebook. Dopo aver completato il flusso di autenticazione, l'applicazione Xamarin.Forms riceve un token di accesso.
1. L'applicazione Xamarin.Forms Usa il token di accesso per richiedere un token della risorsa dal gestore di token di risorsa.
1. Il gestore di token di risorsa Usa il token di accesso per richiedere l'identità dell'utente da Facebook. L'identità dell'utente viene quindi usato per richiedere un token delle risorse di Cosmos DB, che consente di concedere l'accesso in lettura/scrittura a raccolta partizionata dell'utente autenticato.
1. L'applicazione Xamarin.Forms Usa il token delle risorse per accedere direttamente alle risorse di Cosmos DB con le autorizzazioni definite dal token delle risorse.

> [!NOTE]
> Quando il token delle risorse scade, le richieste di database di documenti successive riceveranno un'eccezione 401 non autorizzato. A questo punto, le applicazioni Xamarin.Forms devono ristabilire l'identità e richiedere un nuovo token di risorsa.

Per altre informazioni sul partizionamento di Cosmos DB, vedere [come eseguire il partizionamento e ridimensionamento in Azure Cosmos DB](/azure/cosmos-db/partition-data/). Per altre informazioni sul controllo di accesso Cosmos DB, vedere [protezione dell'accesso ai dati di Cosmos DB](/azure/cosmos-db/secure-access-to-data/) e [controllo degli accessi nell'API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Programma di installazione

Il processo per l'integrazione del gestore di token di risorsa in un'applicazione Xamarin.Forms è come segue:

1. Creare un account Cosmos DB che verrà utilizzato il controllo di accesso. Per altre informazioni, vedere [Cosmos DB Configuration](#cosmosdb_configuration).
1. Creare un servizio App di Azure per ospitare il gestore di token di risorsa. Per altre informazioni, vedere [configurazione del servizio App Azure](#app_service_configuration).
1. Creare un'app di Facebook per eseguire l'autenticazione. Per altre informazioni, vedere [Facebook App Configuration](#facebook_configuration).
1. Configurare il servizio App di Azure per eseguire l'autenticazione facile con Facebook. Per altre informazioni, vedere [configurazione di autenticazione di Azure App Service](#app_service_authentication_configuration).
1. Configurare l'applicazione di esempio Xamarin.Forms per comunicare con il servizio App di Azure e Cosmos DB. Per altre informazioni, vedere [configurazione dell'applicazione Xamarin.Forms](#forms_application_configuration).

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Configurazione di Azure Cosmos DB

Il processo per la creazione di un account Cosmos DB che verrà utilizzato il controllo di accesso è come segue:

1. Creare un account Cosmos DB. Per altre informazioni, vedere [creare un account Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. Nell'account Cosmos DB, creare una nuova raccolta denominata `UserItems`, specificando una chiave di partizione di `/userid`.

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Configurazione di servizio App di Azure

Il processo per l'hosting di resource token broker nel servizio App di Azure è come segue:

1. Nel portale di Azure, creare una nuova app web di servizio App. Per altre informazioni, vedere [creare un'app web in un ambiente del servizio App](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. Nel portale di Azure, aprire il pannello impostazioni dell'App per l'app web e aggiungere le impostazioni seguenti:
    - `accountUrl` : il valore deve essere l'URL dell'account Cosmos DB dal pannello chiavi dell'account Cosmos DB.
    - `accountKey` : il valore deve essere la chiave di master Cosmos DB (primaria o secondaria) dal pannello chiavi dell'account Cosmos DB.
    - `databaseId` : il valore deve essere il nome del database Cosmos DB.
    - `collectionId` : il valore deve essere il nome della raccolta di Cosmos DB (in questo caso, `UserItems`).
    - `hostUrl` : il valore deve essere l'URL dell'app web nel Pannello di panoramica dell'account del servizio App.

    Lo screenshot seguente illustra questa configurazione:

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "App Service Web App Settings")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service Web App Settings")

1. Pubblicare la soluzione del gestore di token di risorsa per l'app web di servizio App di Azure.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Configurazione dell'App Facebook

Il processo per la creazione di un'app di Facebook per eseguire l'autenticazione è come segue:

1. Creare un'app Facebook. Per altre informazioni, vedere [registrare e configurare un'App](https://developers.facebook.com/docs/apps/register) nel Developer Center su Facebook.
1. Aggiungere il prodotto di account di accesso di Facebook all'app. Per altre informazioni, vedere [Add Facebook Login to Your App o un sito Web](https://developers.facebook.com/docs/facebook-login) nel Developer Center su Facebook.
1. Configurare account di accesso di Facebook come segue:
   - Abilitare l'accesso di OAuth Client.
   - Abilitare l'accesso di OAuth Web.
   - Impostare l'URI dell'app web del servizio App, URI di reindirizzamento OAuth validi con `/.auth/login/facebook/callback` aggiunto.

  Lo screenshot seguente illustra questa configurazione:

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook Login OAuth Settings")

Per altre informazioni, vedere [registrare l'applicazione con Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Configurazione dell'autenticazione servizio App di Azure

Il processo per la configurazione dell'autenticazione semplice del servizio App è come segue:

1. Nel portale di Azure, passare all'app web di servizio App.
1. Nel portale di Azure, aprire l'autenticazione / pannello autorizzazione ed eseguire la configurazione seguente:
    - Autenticazione del servizio app deve essere attivato.
    - L'azione da intraprendere quando una richiesta non è autenticata deve essere impostato su **account di accesso con Facebook**.

    Lo screenshot seguente illustra questa configurazione:

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "App Service Web App Authentication Settings")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service Web App Authentication Settings")

L'app web del servizio App deve essere configurata anche per comunicare con l'app Facebook per abilitare il flusso di autenticazione. A questo scopo, selezionare il provider di identità Facebook e immettere il **App ID** e **segreto dell'App** valori dalle impostazioni dell'app Facebook nel Developer Center su Facebook. Per altre informazioni, vedere [le informazioni di aggiungere Facebook all'applicazione](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Configurazione dell'applicazione Xamarin.Forms

Il processo per la configurazione dell'applicazione di esempio Xamarin.Forms è come segue:

1. Aprire la soluzione Xamarin.Forms.
1. Apri `Constants.cs` e aggiornare i valori delle costanti seguenti:
    - `EndpointUri` : il valore deve essere l'URL dell'account Cosmos DB dal pannello chiavi dell'account Cosmos DB.
    - `DatabaseName` : il valore deve essere il nome del database di documenti.
    - `CollectionName` : il valore deve essere il nome della raccolta di database di documenti (in questo caso, `UserItems`).
    - `ResourceTokenBrokerUrl` : il valore deve essere l'URL dell'app web resource token broker nel Pannello di panoramica dell'account del servizio App.

## <a name="initiating-login"></a>Avvio di account di accesso

L'applicazione di esempio viene avviato il processo di accesso mediante AUTH reindirizzare un browser a un URL di provider di identità, come illustrato nell'esempio di codice seguente:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

In questo modo un flusso di autenticazione OAuth coincidere tra servizio App di Azure e Facebook, che consente di visualizzare la pagina di accesso di Facebook:

![](azure-cosmosdb-auth-images/login.png "Facebook Login")

L'account di accesso può essere annullata, premere il **annullare** button in iOS o premendo il **nuovamente** pulsante in Android, nel qual caso l'utente rimane non autenticata e l'interfaccia utente del provider di identità è rimossa dallo schermo.

Per altre informazioni sulle AUTH, vedere [autenticare gli utenti con un Provider di identità](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="obtaining-a-resource-token"></a>Come ottenere un Token di risorsa

Dopo l'autenticazione, il `WebRedirectAuthenticator.Completed` viene generato l'evento. Esempio di codice seguente illustra la gestione di questo evento:

```csharp
auth.Completed += async (sender, e) =>
{
  if (e.IsAuthenticated && e.Account.Properties.ContainsKey("token"))
  {
    var easyAuthResponseJson = JsonConvert.DeserializeObject<JObject>(e.Account.Properties["token"]);
    var easyAuthToken = easyAuthResponseJson.GetValue("authenticationToken").ToString();

    // Call the ResourceBroker to get the resource token
    using (var httpClient = new HttpClient())
    {
      httpClient.DefaultRequestHeaders.Add("x-zumo-auth", easyAuthToken);
      var response = await httpClient.GetAsync(Constants.ResourceTokenBrokerUrl + "/api/resourcetoken/");
      var jsonString = await response.Content.ReadAsStringAsync();
      var tokenJson = JsonConvert.DeserializeObject<JObject>(jsonString);
      resourceToken = tokenJson.GetValue("token").ToString();
      UserId = tokenJson.GetValue("userid").ToString();

      if (!string.IsNullOrWhiteSpace(resourceToken))
      {
        client = new DocumentClient(new Uri(Constants.EndpointUri), resourceToken);
        ...
      }
      ...
    }
  }
};
```

Il risultato di un'autenticazione riuscita è un token di accesso disponibile `AuthenticatorCompletedEventArgs.Account` proprietà. Il token di accesso è estratto e usato in una richiesta GET a resource token broker `resourcetoken` API.

Il `resourcetoken` API Usa il token di accesso per richiedere l'identità dell'utente da Facebook, che a sua volta viene usato per richiedere un token delle risorse di Cosmos DB. Se esiste già un documento di autorizzazione valido per l'utente nel database di documenti, che sono stati recuperati e viene restituito un documento JSON che contiene il token della risorsa per l'applicazione Xamarin.Forms. Se non esiste un documento di autorizzazione valido per l'utente, un utente e l'autorizzazione viene creato nel database di documenti e il token della risorsa viene estratto dal documento di autorizzazione e restituito all'applicazione Xamarin.Forms in un documento JSON.

> [!NOTE]
> Un utente di database di documenti è una risorsa associata a un database di documenti e ogni database può contenere zero o più utenti. Un'autorizzazione di database di documenti è una risorsa associata a un utente di database di documenti e ogni utente può contenere zero o più autorizzazioni. Una risorsa autorizzazione fornisce l'accesso a un token di sicurezza che l'utente è necessario quando si tenta di accedere a una risorsa, ad esempio un documento.

Se il `resourcetoken` API viene completata correttamente, ma invierà codice di stato HTTP 200 (OK) nella risposta, insieme a un documento JSON che contiene il token della risorsa. I dati JSON seguenti mostrano un messaggio di risposta con esito positivo tipico:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

Il gestore dell'evento `WebRedirectAuthenticator.Completed` legge la risposta dall'API `resourcetoken` ed estrae il token di risorsa e l'ID utente. Il token della risorsa viene quindi passato come argomento al costruttore `DocumentClient`, che incapsula l'endpoint, le credenziali e i criteri di connessione usati per accedere Cosmos DB e viene usato per configurare ed eseguire le richieste rispetto a Cosmos DB. Il token della risorsa viene inviato con ogni richiesta per accedere direttamente a una risorsa e indica che viene concesso l'accesso in lettura/scrittura a raccolta partizionata gli utenti autenticati.

## <a name="retrieving-documents"></a>Il recupero di documenti

Il recupero di documenti che appartiene solo all'utente autenticato può essere ottenuto creando una query del documento che include l'id dell'utente come chiave di partizione e viene illustrata nell'esempio di codice seguente:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink,
                        new FeedOptions
                        {
                          MaxItemCount = -1,
                          PartitionKey = new PartitionKey(UserId)
                        })
          .Where(item => !item.Id.Contains("permission"))
          .AsDocumentQuery();
while (query.HasMoreResults)
{
  Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
}
```

La query recupera tutti i documenti che appartengono all'utente autenticato, dalla raccolta specificata, in modo asincrono e li inserisce in un `List<TodoItem>` raccolta per la visualizzazione.

Il `CreateDocumentQuery<T>` metodo consente di specificare un `Uri` argomento che rappresenta la raccolta da sottoporre a query per documenti e una `FeedOptions` oggetto. Il `FeedOptions` oggetto specifica che un numero illimitato di elementi può essere restituito dalla query e l'id dell'utente come chiave di partizione. Ciò garantisce che solo i documenti nella raccolta partizionata dell'utente vengono restituiti nel risultato.

> [!NOTE]
> Si noti che i documenti di autorizzazione, che vengono creati per il gestore di token di risorsa, vengono archiviati nella stessa raccolta di documenti i documenti creati dall'applicazione Xamarin.Forms. Pertanto, la query di documento contiene un `Where` clausola che applica un predicato di filtro alla query sulla raccolta di documenti. Questa clausola assicura che i documenti di autorizzazione non vengono restituiti dalla raccolta documenti.

Per altre informazioni sul recupero dei documenti da una raccolta di documenti, vedere [recupero di documenti di una raccolta documenti](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#document_query).

## <a name="inserting-documents"></a>Inserimento di documenti

Prima dell'inserimento di un documento in una raccolta di documenti, il `TodoItem.UserId` proprietà deve essere aggiornata con il valore viene usato come chiave di partizione, come illustrato nell'esempio di codice seguente:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Ciò garantisce che il documento verrà inserito nella raccolta partizionata dell'utente.

Per altre informazioni sull'inserimento di un documento in una raccolta di documenti, vedere [inserimento di un documento in una raccolta di documenti](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting_document).

## <a name="deleting-documents"></a>L'eliminazione di documenti

Il valore di chiave di partizione deve essere specificato quando l'eliminazione di un documento da una raccolta partizionata, come illustrato nell'esempio di codice seguente:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

In questo modo si garantisce che Cosmos DB riconosce quale partizionata per eliminare il documento dalla raccolta.

Per altre informazioni sull'eliminazione di un documento da una raccolta di documenti, vedere [eliminazione di un documento da una raccolta di documenti](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting_document).

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come combinare il controllo di accesso con le raccolte partizionate, in modo che un utente può accedere solo ai propri documenti di database di documenti in un'applicazione Xamarin.Forms. Specifica l'identità dell'utente come chiave di partizione assicura che una raccolta partizionata possa archiviare solo i documenti per tale utente.

## <a name="related-links"></a>Collegamenti correlati

- [TODO Azure Cosmos DB Auth (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [Utilizzo di un database di documenti di Azure Cosmos DB](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [Protezione dell'accesso ai dati di Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Controllo degli accessi nell'API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Come eseguire il partizionamento e ridimensionamento in Azure Cosmos DB](/azure/cosmos-db/partition-data/)
- [Libreria Client di Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API di Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
