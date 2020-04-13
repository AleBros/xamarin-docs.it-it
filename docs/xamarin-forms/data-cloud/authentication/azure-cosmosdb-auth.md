---
title: Autenticare gli utenti con un database di database Cosmos di Azure e Xamarin.FormsAuthenticate Users with an Azure Cosmos DB Document Database and Xamarin.Forms
description: Questo articolo illustra come combinare il controllo di accesso con le raccolte partizionate di Azure Cosmos DB, in modo che un utente possa accedere solo ai propri documenti in un'applicazione Xamarin.Forms.This article explains how to combine access control with Azure Cosmos DB partitioned collections, so that a user can only access their own documents in a Xamarin.Forms application.
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 6e79e647d64103b6d257de7233f488899bcaff40
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388603"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>Autenticare gli utenti con un database di database Cosmos di Azure e Xamarin.FormsAuthenticate Users with an Azure Cosmos DB Document Database and Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_I database dei documenti di Azure Cosmos DB supportano raccolte partizionate, che possono estendersi su più server e partizioni, supportando al contempo capacità di archiviazione e velocità effettiva illimitate. In questo articolo viene illustrato come combinare il controllo di accesso con raccolte partizionate, in modo che un utente possa accedere solo ai propri documenti in un'applicazione Xamarin.Forms._

## <a name="overview"></a>Panoramica

È necessario specificare una chiave di partizione quando si crea una raccolta partizionata e i documenti con la stessa chiave di partizione verranno archiviati nella stessa partizione. Pertanto, se si specifica l'identità dell'utente come chiave di partizione, verrà restituita una raccolta partizionata che archivierà solo i documenti per tale utente. Ciò garantisce inoltre che il database dei documenti del database Cosmos di Azure verrà ridimensionato con l'aumentare del numero di utenti ed elementi.

L'accesso deve essere concesso a qualsiasi raccolta e il modello di controllo di accesso dell'API SQL definisce due tipi di costrutti di accesso:Access must be granted to any collection, and the SQL API access control model defines two types of access constructs:

- **Le chiavi master** consentono l'accesso amministrativo completo a tutte le risorse all'interno di un account Cosmos DB e vengono create quando viene creato un account Cosmos DB.
- **I token** di risorsa acquisiscono la relazione tra l'utente di un database e l'autorizzazione di cui dispone l'utente per una risorsa Cosmos DB specifica, ad esempio una raccolta o un documento.

L'esposizione di una chiave master apre un account Cosmos DB alla possibilità di un utilizzo dannoso o negligente. Tuttavia, i token di risorsa di Azure Cosmos DB forniscono un meccanismo sicuro per consentire ai client di leggere, scrivere ed eliminare risorse specifiche in un account di database Cosmos di Azure in base alle autorizzazioni concesse.

Un approccio tipico alla richiesta, alla generazione e al recapito di token di risorsa a un'applicazione per dispositivi mobili consiste nell'usare un broker di token di risorsa. Il diagramma seguente mostra una panoramica generale di come l'applicazione di esempio usa un broker di token di risorse per gestire l'accesso ai dati del database dei documenti:The following diagram shows a high-level overview of how the sample application uses a resource token broker to manage access to the document database data:

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Document Database Authentication Process")

Il broker di token di risorse è un servizio API Web di livello intermedio, ospitato nel servizio app di Azure, che possiede la chiave master dell'account Cosmos DB. L'applicazione di esempio usa il broker di token di risorsa per gestire l'accesso ai dati del database dei documenti come segue:The sample application uses the resource token broker to manage access to the document database data as follows:

1. All'accesso, l'applicazione Xamarin.Forms contatta il servizio app di Azure per avviare un flusso di autenticazione.
1. Azure App Service performs an OAuth authentication flow with Facebook. Al termine del flusso di autenticazione, l'applicazione Xamarin.Forms riceve un token di accesso.
1. L'applicazione Xamarin.Forms utilizza il token di accesso per richiedere un token di risorsa dal gestore di token di risorsa.
1. The resource token broker uses the access token to request the user's identity from Facebook. L'identità dell'utente viene quindi utilizzata per richiedere un token di risorsa da Cosmos DB, che viene utilizzato per concedere l'accesso in lettura/scrittura alla raccolta partizionata dell'utente autenticato.
1. L'applicazione Xamarin.Forms utilizza il token di risorsa per accedere direttamente alle risorse Cosmos DB con le autorizzazioni definite dal token di risorsa.

> [!NOTE]
> Alla scadenza del token di risorsa, le richieste di database dei documenti successive riceveranno un'eccezione non autorizzata 401. A questo punto, le applicazioni Xamarin.Forms devono ristabilire l'identità e richiedere un nuovo token di risorsa.

Per altre informazioni sul partizionamento di Cosmos DB, vedere [Come partizionare e ridimensionare in Azure Cosmos DB](/azure/cosmos-db/partition-data/). Per ulteriori informazioni sul controllo di accesso Cosmos DB, vedere [protezione dell'accesso ai dati Cosmos DB](/azure/cosmos-db/secure-access-to-data/) e [Controllo di accesso nell'API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Configurazione

Il processo di integrazione del broker di token di risorse in un'applicazione Xamarin.Forms è il seguente:The process for integrating the resource token broker into a Xamarin.Forms application is as follows:

1. Creare un account Cosmos DB che utilizzerà il controllo di accesso. Per ulteriori informazioni, vedere [Configurazione di Cosmos DB](#cosmosdb_configuration).
1. Creare un servizio app di Azure per ospitare il broker di token di risorsa. Per altre informazioni, vedere Configurazione del servizio app di Azure .For more information, see [Azure App Service Configuration](#app_service_configuration).
1. Crea un'app Facebook per eseguire l'autenticazione. Per ulteriori informazioni, vedere [Configurazione dell'app Facebook](#facebook_configuration).
1. Configurare il servizio app di Azure per eseguire facilmente l'autenticazione con Facebook.Configure the Azure App Service to perform easy authentication with Facebook. Per altre informazioni, vedere [Configurazione dell'autenticazione](#app_service_authentication_configuration)del servizio app di Azure .For more information, see Azure App Service Authentication Configuration .
1. Configurare l'applicazione di esempio Xamarin.Forms per comunicare con il servizio app di Azure e Cosmos DB. Per ulteriori informazioni, vedere [Configurazione dell'applicazione Xamarin.Forms](#forms_application_configuration).

> [!NOTE]
> Se non si dispone di una sottoscrizione di [Azure,](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Azure Cosmos DB Configuration

Il processo per la creazione di un account Cosmos DB che utilizzerà il controllo di accesso è il seguente:The process for creating a Cosmos DB account that will use access control is as follows:

1. Creare un account Cosmos DB. Per altre informazioni, vedere [Creare un account di database Cosmos](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account)di Azure.For more information, see Create an Azure Cosmos DB account .
1. Nell'account Cosmos DB creare un `UserItems`nuovo insieme denominato `/userid`, specificando una chiave di partizione di .

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Configurazione del servizio app di AzureAzure App Service Configuration

Il processo per l'hosting del gestore di token di risorse nel servizio app di Azure è il seguente:The process for hosting the resource token broker in Azure App Service is as follows:

1. Nel portale di Azure creare una nuova app Web del servizio app. Per altre informazioni, vedere [Creare un'app Web in un ambiente](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/)del servizio app.
1. Nel portale di Azure aprire il pannello Impostazioni app per l'app Web e aggiungere le impostazioni seguenti:
    - `accountUrl`– il valore deve essere l'URL dell'account Cosmos DB dal pannello Chiavi dell'account Cosmos DB.
    - `accountKey`– il valore deve essere la chiave master Cosmos DB (primaria o secondaria) dal pannello Chiavi dell'account Cosmos DB.
    - `databaseId`– il valore deve essere il nome del database Cosmos DB.
    - `collectionId`– il valore deve essere il nome della raccolta `UserItems`Cosmos DB (in questo caso, ).
    - `hostUrl`: il valore deve essere l'URL dell'app Web dal pannello Panoramica dell'account del servizio app.

    La schermata seguente illustra questa configurazione:The following screenshot demonstrates this configuration:

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "App Service Web App Settings")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service Web App Settings")

1. Pubblicare la soluzione di broker di token di risorsa nell'app Web del servizio app di Azure.Publish the resource token broker solution to the Azure App Service web app.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Configurazione dell'app Facebook

Il processo per la creazione di un'app Facebook per eseguire l'autenticazione è il seguente:The process for creating a Facebook app to perform authentication is as follows:

1. Crea un'app Facebook. Per altre informazioni, vedere [Registrare e configurare un'app](https://developers.facebook.com/docs/apps/register) nel Centro per sviluppatori di Facebook.For more information, see Register and Configure an App on the Facebook Developer Center.
1. Aggiungi il prodotto Facebook Login all'app. Per ulteriori informazioni, consulta [Aggiungere l'accesso a Facebook alla tua app o al tuo sito web](https://developers.facebook.com/docs/facebook-login) nel Centro per sviluppatori di Facebook.
1. Configurare Facebook Login come segue:
   - Abilitare l'accesso OAuth client.
   - Abilitare l'accesso OAuth Web.
   - Impostare l'URI di reindirizzamento OAuth valido sull'URI dell'app Web del servizio app con `/.auth/login/facebook/callback` l'aggiunta.

  La schermata seguente illustra questa configurazione:The following screenshot demonstrates this configuration:

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook Login OAuth Settings")

Per ulteriori informazioni, consultate [Registrare l'applicazione con Facebook.](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook)

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Configurazione dell'autenticazione del servizio app di AzureAzure App Service Authentication Configuration

Il processo per la configurazione dell'autenticazione semplice del servizio app è il seguente:The process for configuring App Service easy authentication is as follows:

1. Nel portale di Azure passare all'app Web del servizio app.
1. Nel portale di Azure aprire il pannello Autenticazione/Autorizzazione ed eseguire la configurazione seguente:In the Azure Portal, open the Authentication / Authorization blade and perform the following configuration:
    - L'autenticazione del servizio app deve essere attivata.
    - L'azione da eseguire quando una richiesta non è autenticata deve essere impostata su **Accedi con Facebook**.

    La schermata seguente illustra questa configurazione:The following screenshot demonstrates this configuration:

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "App Service Web App Authentication Settings")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service Web App Authentication Settings")

L'app Web del servizio app deve inoltre essere configurata per comunicare con l'app Facebook per abilitare il flusso di autenticazione. Questa operazione può essere eseguita selezionando il provider di identità di Facebook e immettendo i valori **di ID app** e **segreto app** dalle impostazioni dell'app Facebook nel Centro per sviluppatori di Facebook. Per ulteriori informazioni, consultate [Aggiungere informazioni di Facebook all'applicazione.](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application)

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Configurazione dell'applicazione Xamarin.Forms

Il processo per la configurazione dell'applicazione di esempio Xamarin.Forms è il seguente:

1. Aprire la soluzione Xamarin.Forms.
1. Aprire `Constants.cs` e aggiornare i valori delle costanti seguenti:
    - `EndpointUri`– il valore deve essere l'URL dell'account Cosmos DB dal pannello Chiavi dell'account Cosmos DB.
    - `DatabaseName`– il valore deve essere il nome del database dei documenti.
    - `CollectionName`– il valore deve essere il nome della raccolta `UserItems`di database di documenti (in questo caso, ).
    - `ResourceTokenBrokerUrl`: il valore deve essere l'URL dell'app Web del broker di token di risorsa dal pannello Panoramica dell'account del servizio app.

## <a name="initiating-login"></a>Accesso all'accesso

L'applicazione di esempio avvia il processo di accesso reindirizzando un browser a un URL del provider di identità, come illustrato nel codice di esempio seguente:The sample application initiates the login process by redirecting a browser to an identity provider URL, as demonstrated in the following example code:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

In questo modo un flusso di autenticazione OAuth da avviare tra il servizio app di Azure e Facebook, che visualizza la pagina di accesso di Facebook:This causes an OAuth authentication flow to be initiated between Azure App Service and Facebook, which displays the Facebook login page:

![](azure-cosmosdb-auth-images/login.png "Facebook Login")

L'accesso può essere annullato premendo il pulsante **Annulla** su iOS o premendo il pulsante **Indietro** su Android, nel qual caso l'utente rimane non autenticato e l'interfaccia utente del provider di identità viene rimossa dallo schermo.

## <a name="obtaining-a-resource-token"></a>Recupero di un token di risorsaObtaining a Resource Token

Dopo la corretta `WebRedirectAuthenticator.Completed` autenticazione, viene generato l'evento. Esempio di codice seguente viene illustrata la gestione di questo evento:The following code example demonstrates handling this event:

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

Il risultato di una corretta autenticazione `AuthenticatorCompletedEventArgs.Account` è un token di accesso, che è disponibile proprietà. Il token di accesso viene estratto e utilizzato in una `resourcetoken` richiesta GET all'API del gestore di token di risorsa.

L'API `resourcetoken` usa il token di accesso per richiedere l'identità dell'utente da Facebook, che a sua volta viene usato per richiedere un token di risorsa da Cosmos DB. Se esiste già un documento di autorizzazione valido per l'utente nel database dei documenti, viene recuperato e un documento JSON contenente il token di risorsa viene restituito all'applicazione Xamarin.Forms. Se non esiste un documento di autorizzazione valido per l'utente, un utente e un'autorizzazione vengono creati nel database dei documenti e il token di risorsa viene estratto dal documento di autorizzazione e restituito all'applicazione Xamarin.Forms in un documento JSON.

> [!NOTE]
> Un utente del database di documenti è una risorsa associata a un database di documenti e ogni database può contenere zero o più utenti. Un'autorizzazione del database dei documenti è una risorsa associata a un utente del database di documenti e ogni utente può contenere zero o più autorizzazioni. Una risorsa di autorizzazione fornisce l'accesso a un token di sicurezza richiesto dall'utente quando tenta di accedere a una risorsa, ad esempio un documento.

Se `resourcetoken` l'API viene completata correttamente, invierà il codice di stato HTTP 200 (OK) nella risposta, insieme a un documento JSON contenente il token di risorsa. I dati JSON seguenti mostrano un tipico messaggio di risposta riuscita:The following JSON data shows a typical successful response message:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

Il `WebRedirectAuthenticator.Completed` gestore eventi legge `resourcetoken` la risposta dall'API ed estrae il token di risorsa e l'ID utente. Il token di risorsa viene quindi `DocumentClient` passato come argomento al costruttore, che incapsula l'endpoint, le credenziali e i criteri di connessione utilizzati per accedere a Cosmos DB e viene utilizzato per configurare ed eseguire richieste nel database Cosmos. Il token di risorsa viene inviato con ogni richiesta di accesso diretto a una risorsa e indica che viene concesso l'accesso in lettura/scrittura alla raccolta partizionata degli utenti autenticati.

## <a name="retrieving-documents"></a>Recupero di documenti

Il recupero di documenti che appartengono solo all'utente autenticato può essere ottenuto creando una query del documento che include l'ID dell'utente come chiave di partizione ed è illustrato nell'esempio di codice seguente:Retrieving documents that only belong to the authenticated user can be achieved by creating a document query that includes the user's id as a partition key, and is demonstrated in the following code example:

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

La query recupera in modo asincrono tutti i documenti appartenenti all'utente `List<TodoItem>` autenticato, dalla raccolta specificata, e li inserisce in una raccolta per la visualizzazione.

Il `CreateDocumentQuery<T>` metodo specifica `Uri` un argomento che rappresenta la raccolta che deve `FeedOptions` essere sottoposta a query per i documenti e un oggetto. L'oggetto `FeedOptions` specifica che un numero illimitato di elementi può essere restituito dalla query e l'ID dell'utente come chiave di partizione. In questo modo si garantisce che nel risultato vengano restituiti solo i documenti nella raccolta partizionata dell'utente.

> [!NOTE]
> Si noti che i documenti di autorizzazione, creati dal broker di token di risorsa, vengono archiviati nella stessa raccolta di documenti dei documenti creati dall'applicazione Xamarin.Forms. Pertanto, la query `Where` del documento contiene una clausola che applica un predicato di filtro alla query alla raccolta di documenti. Questa clausola garantisce che i documenti di autorizzazione non vengano restituiti dalla raccolta documenti.

Per ulteriori informazioni sul recupero di documenti da una raccolta documenti, vedere [Recupero di documenti di raccolta](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#document_query)documenti .

## <a name="inserting-documents"></a>Inserimento di documenti

Prima di inserire un documento in `TodoItem.UserId` una raccolta documenti, la proprietà deve essere aggiornata con il valore utilizzato come chiave di partizione, come illustrato nell'esempio di codice seguente:Prior to inserting a document into a document collection, the property should be updated with the value being used as the partition key, as demonstrated in the following code example:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

In questo modo il documento verrà inserito nella raccolta partizionata dell'utente.

Per ulteriori informazioni sull'inserimento di un documento in una raccolta documenti, vedere [Inserimento di un documento in una raccolta documenti](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting_document).

## <a name="deleting-documents"></a>Eliminazione di documenti

Il valore della chiave di partizione deve essere specificato quando si elimina un documento da una raccolta partizionata, come illustrato nell'esempio di codice seguente:The partition key value must be specified when deleting a document from a partitioned collection, as demonstrated in the following code example:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

In questo modo Cosmos DB conosce la raccolta partizionata da cui eliminare il documento.

Per ulteriori informazioni sull'eliminazione di un documento da una raccolta documenti, vedere [Eliminazione di un documento da una raccolta documenti](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting_document).

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come combinare il controllo di accesso con raccolte partizionate, in modo che un utente possa accedere solo ai propri documenti di database di documenti in un'applicazione Xamarin.Forms. Se si specifica l'identità dell'utente come chiave di partizione, una raccolta partizionata può archiviare solo documenti per tale utente.

## <a name="related-links"></a>Collegamenti correlati

- [Todo Azure Cosmos DB Auth (esempio)Todo Azure Cosmos DB Auth (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [Utilizzo di un database di documenti di Azure Cosmos DB](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [Protezione dell'accesso ai dati in Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Controllo dell'accesso nell'API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Come eseguire il partizionamento e il ridimensionamento in Azure Cosmos DB](/azure/cosmos-db/partition-data/)
- [Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API di Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
