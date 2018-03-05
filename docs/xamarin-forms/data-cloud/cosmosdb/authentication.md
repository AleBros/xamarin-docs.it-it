---
title: L'autenticazione degli utenti con un Database di documenti di Azure Cosmos DB
description: "Azure DB Cosmos documento i database supportano le raccolte partizionate, che possono estendersi su più server e le partizioni, supportando la velocità effettiva e archiviazione illimitata. In questo articolo spiega come combinare il controllo di accesso con le raccolte partizionate, in modo che un utente può accedere solo i propri documenti in un'applicazione di xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: a16f72e6aaee93aa313aff0aba23887b51acf701
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="authenticating-users-with-an-azure-cosmos-db-document-database"></a>L'autenticazione degli utenti con un Database di documenti di Azure Cosmos DB

_Azure DB Cosmos documento i database supportano le raccolte partizionate, che possono estendersi su più server e le partizioni, supportando la velocità effettiva e archiviazione illimitata. In questo articolo spiega come combinare il controllo di accesso con le raccolte partizionate, in modo che un utente può accedere solo i propri documenti in un'applicazione di xamarin. Forms._

## <a name="overview"></a>Panoramica

Una chiave di partizione deve essere specificata durante la creazione di una raccolta partizionata e documenti con la stessa chiave di partizione verranno archiviati nella stessa partizione. Pertanto, se si specifica l'identità dell'utente come chiave di partizione otterrà in una raccolta partizionata che verrà archiviate solo documenti per l'utente. Questo processo assicura anche che il database di documenti di Azure Cosmos DB verrà ridimensionato con il numero di utenti e aumentare gli elementi.

Necessario concedere l'accesso a ciascuna raccolta e il modello di controllo di accesso API DocumentDB definisce due tipi di costrutti di accesso:

- **Le chiavi master** abilitare l'accesso amministrativo completo a tutte le risorse all'interno di un account DB Cosmos e vengono creati quando viene creato un account DB Cosmos.
- **I token di risorsa** acquisiscono la relazione tra l'utente di un database e l'autorizzazione di cui l'utente dispone per una risorsa Cosmos DB specifica, ad esempio una raccolta o un documento.

Esposizione di una chiave master viene aperto un account Cosmos DB la possibilità di utilizzare dannoso o non appropriato. Tuttavia, i token di risorsa Cosmos DB forniscono un meccanismo sicuro per consentire ai client di lettura, scrittura ed eliminazione di risorse specifiche in un account Cosmos DB in base alle autorizzazioni concesse.

Un approccio tipico per la richiesta, la generazione e il recapito i token di risorsa a un'applicazione per dispositivi mobili consiste nell'utilizzare un gestore di token di risorsa. Il diagramma seguente mostra una panoramica di come l'applicazione di esempio utilizza un gestore di token di risorsa per gestire l'accesso ai dati di database del documento:

![](authentication-images/documentdb-authentication.png "Processo di autenticazione del Database di documento")

Il gestore di token di risorsa è un servizio di API Web di livello intermedio, ospitato in Azure App Service che possiede la chiave master dell'account DB Cosmos. L'applicazione di esempio utilizza il gestore di token di risorsa per gestire l'accesso ai dati di database del documento come indicato di seguito:

1. Account di accesso, l'applicazione di xamarin. Forms contatta il servizio App di Azure per avviare un flusso di autenticazione.
1. Servizio App di Azure esegue un flusso di autenticazione OAuth con Facebook. Dopo aver completato il flusso di autenticazione, l'applicazione di xamarin. Forms riceve un token di accesso.
1. L'applicazione di xamarin. Forms Usa il token di accesso per richiedere un token della risorsa da Service broker di token di risorsa.
1. Il gestore di token di risorsa utilizza il token di accesso per richiedere l'identità dell'utente da Facebook. L'identità dell'utente viene quindi usato per richiedere un token della risorsa da Cosmos DB, che viene utilizzato per concedere l'accesso in lettura/scrittura alla raccolta partizionata dell'utente autenticato.
1. L'applicazione di xamarin. Forms Usa il token della risorsa per accedere direttamente alle risorse di DB Cosmos con le autorizzazioni definite per il token della risorsa.

> [!NOTE]
> Quando scade il token della risorsa, le richieste di database di documenti successivi riceverà un'eccezione 401 non autorizzato. A questo punto, le applicazioni di xamarin. Forms devono ristabilire l'identità e richiedere un nuovo token di risorsa.

Per ulteriori informazioni sul partizionamento Cosmos DB, vedere [come partizione e la scala in Azure Cosmos DB](/azure/cosmos-db/partition-data/). Per ulteriori informazioni sul controllo di accesso Cosmos DB, vedere [protezione dell'accesso ai dati Cosmos DB](/azure/cosmos-db/secure-access-to-data/) e [controllo degli accessi nell'API DocumentDB](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Configurazione

Il processo per l'integrazione di Service broker di token di risorsa in un'applicazione di xamarin. Forms è come segue:

1. Creare un account DB Cosmos che verrà utilizzato il controllo di accesso. Per ulteriori informazioni, vedere [Cosmos DB configurazione](#cosmosdb_configuration).
1. Creare un servizio App di Azure per ospitare il gestore di token di risorsa. Per ulteriori informazioni, vedere [configurazione del servizio App di Azure](#app_service_configuration).
1. Creare un'app di Facebook per eseguire l'autenticazione. Per ulteriori informazioni, vedere [App Configuration Facebook](#facebook_configuration).
1. Configurare il servizio App di Azure per eseguire l'autenticazione semplice con Facebook. Per ulteriori informazioni, vedere [configurazione autenticazione del servizio App di Azure](#app_service_authentication_configuration).
1. Configurare l'applicazione di esempio xamarin. Forms per comunicare con il servizio App di Azure e Cosmos DB. Per ulteriori informazioni, vedere [configurazione dell'applicazione di xamarin. Forms](#forms_application_configuration).

<a name="cosmosdb_configuration" />

### <a name="cosmos-db-configuration"></a>Configurazione COSMOS DB

Il processo per la creazione di un account di DB Cosmos che verrà utilizzato il controllo di accesso è come segue:

1. Creare un account DB Cosmos. Per ulteriori informazioni, vedere [creare un account Cosmos DB](/azure/cosmos-db/documentdb-dotnetcore-get-started#step-1-create-a-documentdb-account).
1. Nell'account di database Cosmos, creare una nuova raccolta denominata `UserItems`, specificare una chiave di partizione di `/userid`.

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Configurazione di servizio App di Azure

Il processo per ospitare il gestore del token di risorse in Azure App Service è come segue:

1. Nel portale di Azure, creare una nuova app web di servizio App. Per ulteriori informazioni, vedere [creare un'app web in un ambiente del servizio App](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. Nel portale di Azure, aprire il pannello Impostazioni applicazione per l'app web e aggiungere le seguenti impostazioni:
    - `accountUrl` : il valore deve essere l'URL dell'account nel Pannello di chiavi dell'account Cosmos DB DB Cosmos.
    - `accountKey` : il valore deve essere la chiave di master DB Cosmos (primaria o secondaria) nel Pannello di chiavi dell'account DB Cosmos.
    - `databaseId` : il valore deve essere il nome del database DB Cosmos.
    - `collectionId` : il valore deve essere il nome della raccolta Cosmos DB (in questo caso, `UserItems`).
    - `hostUrl` : il valore deve essere l'URL dell'app web nel pannello della panoramica dell'account del servizio App.

    Nella schermata seguente viene illustrata questa configurazione:

    [![](authentication-images/azure-web-app-settings.png "Le impostazioni di servizio App Web App")](authentication-images/azure-web-app-settings-large.png "impostazioni App Web di servizio App")

1. Pubblicare la soluzione di broker token di risorsa per l'app web di servizio App di Azure.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Configurazione delle App per Facebook

Il processo per la creazione di un'applicazione di Facebook per eseguire l'autenticazione è come segue:

1. Creare un'app Facebook. Per ulteriori informazioni, vedere [registrare e configurare un'App](https://developers.facebook.com/docs/apps/register) sul centro per sviluppatori di Facebook.
1. Aggiungere il prodotto di account di accesso di Facebook per l'app. Per ulteriori informazioni, vedere [aggiungere account di accesso Facebook per l'App o un sito Web](https://developers.facebook.com/docs/facebook-login) sul centro per sviluppatori di Facebook.
1. Configurare account di accesso di Facebook come segue:
  - Abilitare l'account di accesso Client OAuth.
  - Abilitare l'accesso OAuth Web.
  - Impostare l'URI per l'URI dell'applicazione web, servizio App di reindirizzamento di OAuth valido con `/.auth/login/facebook/callback` aggiunto.

  Nella schermata seguente viene illustrata questa configurazione:

  ![](authentication-images/facebook-oauth-settings.png "Impostazioni OAuth di account di accesso di Facebook")

Per ulteriori informazioni, vedere [registrare l'applicazione con Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Configurazione dell'autenticazione servizio App di Azure

Il processo per configurare l'autenticazione semplice di servizio App è come segue:

1. Nel portale di Azure, passare all'applicazione di servizio App web.
1. Nel portale di Azure, aprire l'autenticazione / pannello autorizzazione ed eseguire la configurazione seguente:
  - Servizio app deve essere attivata l'autenticazione.
  - L'azione da intraprendere quando non viene autenticata una richiesta deve essere impostato su **account di accesso con Facebook**.

  Nella schermata seguente viene illustrata questa configurazione:

  [![](authentication-images/app-service-authentication-settings.png "Le impostazioni di autenticazione App di servizio App Web")](authentication-images/app-service-authentication-settings-large.png "le impostazioni di autenticazione App di servizio App Web")

L'applicazione di servizio App web deve essere configurato anche per comunicare con l'app Facebook per abilitare il flusso di autenticazione. Questa operazione può essere eseguita selezionando il provider di identità di Facebook e immettendo il **ID App** e **segreto dell'App** valori dalle impostazioni di app Facebook il centro per sviluppatori di Facebook. Per ulteriori informazioni, vedere [Facebook aggiungere informazioni all'applicazione](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Configurazione dell'applicazione di xamarin. Forms

Il processo per la configurazione dell'applicazione di esempio xamarin. Forms è come segue:

1. Aprire la soluzione xamarin. Forms.
1. Aprire `Constants.cs` e aggiornare i valori delle costanti seguenti:
  - `EndpointUri` : il valore deve essere l'URL dell'account nel Pannello di chiavi dell'account Cosmos DB DB Cosmos.
  - `DatabaseName` : il valore deve essere il nome del database di documenti.
  - `CollectionName` : il valore deve essere il nome della raccolta documenti del database (in questo caso, `UserItems`).
  - `ResourceTokenBrokerUrl` : il valore deve essere l'URL dell'app web di Service broker token risorsa dal pannello della panoramica dell'account del servizio App.

## <a name="initiating-login"></a>Avvio di account di accesso

L'applicazione di esempio avvia il processo di accesso utilizzando Xamarin.Auth per reindirizzare un browser a un URL di provider di identità, come illustrato nell'esempio di codice seguente:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

Di conseguenza, un flusso di autenticazione OAuth coincidere tra servizio App di Azure e Facebook, che consente di visualizzare la pagina di accesso Facebook:

![](authentication-images/login.png "Account di accesso di Facebook")

L'account di accesso può essere annullato premendo il **Annulla** button in iOS o premendo il **nuovamente** pulsante in Android, nel qual caso l'utente rimane non autenticato e l'interfaccia utente del provider di identità è rimosso dallo schermo.

Per ulteriori informazioni su Xamarin.Auth, vedere [autenticazione degli utenti con un Provider di identità](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="obtaining-a-resource-token"></a>Ottenere un Token di risorsa

Dopo l'autenticazione, il `WebRedirectAuthenticator.Completed` viene generato l'evento. L'esempio di codice seguente viene illustrato come gestire questo evento:

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

Il risultato di un'autenticazione riuscita è un token di accesso, è disponibile `AuthenticatorCompletedEventArgs.Account` proprietà. Il token di accesso verrà estratti e utilizzato in una richiesta GET per il broker token risorsa `resourcetoken` API.

Il `resourcetoken` API utilizza il token di accesso per richiedere l'identità dell'utente di Facebook, che a sua volta, viene utilizzato per richiedere un token della risorsa da DB Cosmos. Se esiste già un documento di autorizzazione valido per l'utente nel database del documento, questo viene recuperato e viene restituito un documento JSON contenente il token della risorsa per l'applicazione di xamarin. Forms. Se non esiste un documento di autorizzazione valido per l'utente, un utente e l'autorizzazione viene creato nel database di documento e il token della risorsa viene estratto dal documento di autorizzazione e restituito all'applicazione di xamarin. Forms in un documento JSON.

> [!NOTE]
> Un utente del database di documenti è una risorsa associata a un database di documenti e ogni database può contenere zero o più utenti. Un'autorizzazione di database di documenti è una risorsa associata a un utente del database di documento e ogni utente può contenere zero o più autorizzazioni. Una risorsa di autorizzazione fornisce l'accesso a un token di sicurezza che l'utente richiede che durante il tentativo di accedere a una risorsa, ad esempio un documento.

Se il `resourcetoken` API è stata completata correttamente, questo invierà un codice di stato HTTP 200 (OK) nella risposta, insieme a un documento JSON contenente il token della risorsa. I dati JSON seguenti mostrano un messaggio di risposta con esito positivo tipico:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

Il `WebRedirectAuthenticator.Completed` gestore dell'evento legge la risposta di `resourcetoken` API ed estrae il token della risorsa e l'id utente. Il token della risorsa viene quindi passato come argomento per il `DocumentClient` costruttore, che incapsula l'endpoint, credenziali e consentono di accedere ai database Cosmos criteri di connessione e viene utilizzato per configurare ed eseguire le richieste nel DB Cosmos. Il token della risorsa viene inviato con ogni richiesta per accedere direttamente a una risorsa e indica che viene concesso l'accesso di lettura/scrittura alla raccolta partizionata gli utenti autenticati.

## <a name="retrieving-documents"></a>Recupero di documenti

Recupero di documenti che appartengano solo all'utente autenticato può essere ottenuto creando una query di documento che include l'id dell'utente come chiave di partizione, come illustrata nell'esempio di codice seguente:

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

La query recupera tutti i documenti che appartengono all'utente autenticato, dalla raccolta specificata, in modo asincrono e li inserisce in un `List<TodoItem>` insieme per la visualizzazione.

Il `CreateDocumentQuery<T>` metodo specifica un `Uri` argomento che rappresenta la raccolta che è necessario eseguire una query per i documenti, e un `FeedOptions` oggetto. Il `FeedOptions` oggetto specifica che un numero illimitato di elementi può essere restituito dalla query e l'id dell'utente come chiave di partizione. In questo modo si garantisce che solo i documenti nella raccolta partizionata dell'utente vengono restituiti nel risultato.

> [!NOTE]
> Si noti che i documenti di autorizzazione, che vengono creati da Service broker di token di risorsa, vengono archiviati nella stessa raccolta documenti come i documenti creati dall'applicazione xamarin. Forms. Pertanto, la query del documento contiene un `Where` clausola che applica un predicato di filtro alla query sulla raccolta di documenti. Questa clausola assicura che i documenti di autorizzazione non vengono restituiti dalla raccolta documenti.

Per ulteriori informazioni sul recupero di documenti da una raccolta documenti, vedere [recupero raccolta documenti](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#document_query).

## <a name="inserting-documents"></a>Inserimento di documenti

Prima dell'inserimento di un documento in una raccolta documenti, la `TodoItem.UserId` proprietà deve essere aggiornata con il valore viene utilizzato come chiave di partizione, come illustrato nell'esempio di codice seguente:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

In questo modo si garantisce che il documento verrà inserito nella raccolta partizionata dell'utente.

Per ulteriori informazioni sull'inserimento di un documento in una raccolta di documenti, vedere [l'inserimento di un documento in una raccolta di documenti](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#inserting_document).

## <a name="deleting-documents"></a>Eliminazione di documenti

Il valore della chiave di partizione deve essere specificato quando l'eliminazione di un documento da una raccolta partizionata, come illustrato nell'esempio di codice seguente:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

In questo modo si garantisce che DB Cosmos sa quale partizionata raccolta per il documento da eliminare.

Per ulteriori informazioni sull'eliminazione di un documento da una raccolta di documenti, vedere [l'eliminazione di un documento da una raccolta di documenti](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#deleting_document).

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come combinare il controllo di accesso con le raccolte partizionate, in modo che un utente può accedere solo i propri documenti di database in un'applicazione di xamarin. Forms. Specifica l'identità dell'utente come chiave di partizione assicura che una raccolta partizionata possibile solo archiviare i documenti per tale utente.


## <a name="related-links"></a>Collegamenti correlati

- [TodoDocumentDBAuth (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)
- [Utilizzo di un database di documenti di Azure Cosmos DB](~/xamarin-forms/data-cloud/cosmosdb/consuming.md)
- [Protezione dell'accesso ai dati di Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Controllo degli accessi nell'API DocumentDB](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Creazione di partizioni e scala nel database di Azure Cosmos](/azure/cosmos-db/partition-data/)
- [Libreria Client di DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API di Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
