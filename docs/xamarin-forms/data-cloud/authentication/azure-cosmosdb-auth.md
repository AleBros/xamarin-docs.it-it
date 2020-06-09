---
title: "autenticare gli utenti con un Azure Cosmos DB database di documenti e Xamarin.Forms " Descrizione: "in questo articolo viene illustrato come combinare il controllo di accesso con Azure Cosmos DB raccolte partizionate, in modo che un utente possa accedere solo ai propri documenti in un' Xamarin.Forms applicazione."
ms. prod: Novell MS. AssetID: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 06/16/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>Autenticare gli utenti con un database di documenti Azure Cosmos DB eXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Azure Cosmos DB database di documenti supportano le raccolte partizionate, che possono estendersi su più server e partizioni, supportando allo stesso tempo archiviazione e velocità effettiva illimitate. Questo articolo illustra come combinare il controllo di accesso con le raccolte partizionate, in modo che un utente possa accedere solo ai propri documenti in un' Xamarin.Forms applicazione._

## <a name="overview"></a>Panoramica

È necessario specificare una chiave di partizione quando si crea una raccolta partizionata e i documenti con la stessa chiave di partizione verranno archiviati nella stessa partizione. Pertanto, specificando l'identità dell'utente come chiave di partizione si otterrà una raccolta partizionata in cui vengono archiviati solo i documenti per tale utente. In questo modo si garantisce che il database dei documenti Azure Cosmos DB aumenterà il numero di utenti ed elementi.

È necessario concedere l'accesso a qualsiasi raccolta e il modello di controllo di accesso dell'API SQL definisce due tipi di costrutti di accesso:

- Le **chiavi master** consentono l'accesso amministrativo completo a tutte le risorse all'interno di un account di Cosmos DB e vengono create quando viene creato un account di Cosmos DB.
- I **token delle risorse** acquisiscono la relazione tra l'utente di un database e l'autorizzazione dell'utente per una risorsa di Cosmos DB specifica, ad esempio una raccolta o un documento.

L'esposizione di una chiave master consente di aprire un account di Cosmos DB alla possibilità di utilizzo dannoso o negligente. Tuttavia, Azure Cosmos DB token delle risorse rappresentano un meccanismo sicuro per consentire ai client di leggere, scrivere ed eliminare risorse specifiche in un account Azure Cosmos DB in base alle autorizzazioni concesse.

Un approccio tipico per la richiesta, la generazione e la distribuzione di token di risorsa a un'applicazione per dispositivi mobili consiste nell'usare un gestore di token di risorsa. Il diagramma seguente illustra una panoramica generale del modo in cui l'applicazione di esempio usa un gestore di token di risorsa per gestire l'accesso ai dati del database di documenti:

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Document Database Authentication Process")

Resource token Broker è un servizio API Web di livello intermedio, ospitato nel servizio app Azure, che possiede la chiave master dell'account di Cosmos DB. L'applicazione di esempio usa il broker del token di risorsa per gestire l'accesso ai dati del database del documento come indicato di seguito:

1. All'accesso, l' Xamarin.Forms applicazione contatta app Azure servizio per avviare un flusso di autenticazione.
1. App Azure servizio esegue un flusso di autenticazione OAuth con Facebook. Al termine del flusso di autenticazione, l' Xamarin.Forms applicazione riceve un token di accesso.
1. L' Xamarin.Forms applicazione usa il token di accesso per richiedere un token di risorsa dal broker del token di risorsa.
1. Resource token broker usa il token di accesso per richiedere l'identità dell'utente da Facebook. L'identità dell'utente viene quindi usata per richiedere un token di risorsa da Cosmos DB, che viene usato per concedere l'accesso in lettura/scrittura alla raccolta partizionata dell'utente autenticato.
1. L' Xamarin.Forms applicazione usa il token di risorsa per accedere direttamente alle risorse Cosmos DB con le autorizzazioni definite dal token delle risorse.

> [!NOTE]
> Quando il token delle risorse scade, le richieste successive del database di documenti riceveranno un'eccezione 401 non autorizzata. A questo punto, le Xamarin.Forms applicazioni devono ristabilire l'identità e richiedere un nuovo token di risorsa.

Per ulteriori informazioni sul partizionamento Cosmos DB, vedere [How to Partition and scale in Azure Cosmos DB](/azure/cosmos-db/partition-data/). Per ulteriori informazioni sul controllo di accesso Cosmos DB, vedere [protezione dell'accesso ai dati Cosmos DB](/azure/cosmos-db/secure-access-to-data/) e al [controllo di accesso nell'API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Configurazione

Di seguito viene descritto il processo di integrazione del broker di token di risorsa in un' Xamarin.Forms applicazione:

1. Creare un account Cosmos DB che utilizzerà il controllo di accesso. Per ulteriori informazioni, vedere [configurazione Azure Cosmos DB](#azure-cosmos-db-configuration).
1. Creare un servizio di app Azure per ospitare il broker del token di risorsa. Per ulteriori informazioni, vedere [configurazione del servizio app Azure](#azure-app-service-configuration).
1. Creare un'app Facebook per eseguire l'autenticazione. Per altre informazioni, vedere la pagina relativa alla [configurazione dell'app Facebook](#facebook-app-configuration).
1. Configurare il servizio app Azure per eseguire facilmente l'autenticazione con Facebook. Per ulteriori informazioni, vedere la pagina relativa alla [configurazione dell'autenticazione del servizio app Azure](#azure-app-service-authentication-configuration).
1. Configurare l' Xamarin.Forms applicazione di esempio per comunicare con app Azure servizio e Cosmos DB. Per ulteriori informazioni, vedere [ Xamarin.Forms configurazione delle applicazioni](#xamarinforms-application-configuration).

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

### <a name="azure-cosmos-db-configuration"></a>Configurazione di Azure Cosmos DB

Il processo per la creazione di un account Cosmos DB che utilizzerà il controllo di accesso è il seguente:

1. Creare un account Cosmos DB. Per ulteriori informazioni, vedere la pagina relativa alla [creazione di un account Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. Nell'account Cosmos DB creare una nuova raccolta denominata `UserItems` , specificando una chiave di partizione di `/userid` .

### <a name="azure-app-service-configuration"></a>Configurazione del servizio app Azure

Il processo di hosting del broker di token di risorsa nel servizio app Azure è il seguente:

1. Nella portale di Azure creare una nuova app Web del servizio app. Per altre informazioni, vedere [creare un'app Web in un ambiente del servizio app](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. Nel portale di Azure aprire il pannello impostazioni app per l'app Web e aggiungere le impostazioni seguenti:
    - `accountUrl`: il valore deve essere l'URL dell'account Cosmos DB dal pannello chiavi dell'account di Cosmos DB.
    - `accountKey`: il valore deve essere la chiave Master Cosmos DB (primaria o secondaria) dal pannello chiavi dell'account di Cosmos DB.
    - `databaseId`: il valore deve corrispondere al nome del database Cosmos DB.
    - `collectionId`: il valore deve essere il nome della raccolta di Cosmos DB (in questo caso, `UserItems` ).
    - `hostUrl`: il valore deve essere l'URL dell'app Web nel pannello Panoramica dell'account del servizio app.

    La schermata seguente illustra questa configurazione:

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "App Service Web App Settings")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service Web App Settings")

1. Pubblicare la soluzione broker token Resource nell'app Web del servizio app Azure.

### <a name="facebook-app-configuration"></a>Configurazione dell'app Facebook

Il processo per la creazione di un'app Facebook per eseguire l'autenticazione è il seguente:

1. Creare un'app Facebook. Per altre informazioni, vedere [registrare e configurare un'app](https://developers.facebook.com/docs/apps/register) nel centro per sviluppatori di Facebook.
1. Aggiungere il prodotto di accesso a Facebook all'app. Per altre informazioni, vedere [aggiungere un account di accesso di Facebook all'app o al sito Web](https://developers.facebook.com/docs/facebook-login) nel centro per sviluppatori Facebook.
1. Configurare l'accesso a Facebook come indicato di seguito:
   - Abilitare l'accesso OAuth del client.
   - Abilitare l'accesso OAuth Web.
   - Impostare l'URI di reindirizzamento OAuth valido sull'URI dell'app Web del servizio app, con `/.auth/login/facebook/callback` accodato.

  La schermata seguente illustra questa configurazione:

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook Login OAuth Settings")

Per altre informazioni, vedere [registrare l'applicazione con Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

### <a name="azure-app-service-authentication-configuration"></a>Configurazione dell'autenticazione del servizio app Azure

Il processo per la configurazione dell'autenticazione semplice del servizio app è il seguente:

1. Nel portale di Azure passare all'app Web del servizio app.
1. Nel portale di Azure aprire il pannello autenticazione/autorizzazione ed eseguire la configurazione seguente:
    - L'autenticazione del servizio app deve essere attivata.
    - L'azione da intraprendere quando una richiesta non è autenticata deve essere impostata per l' **accesso con Facebook**.

    La schermata seguente illustra questa configurazione:

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "App Service Web App Authentication Settings")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service Web App Authentication Settings")

L'app Web del servizio app deve essere configurata anche per comunicare con l'app Facebook per abilitare il flusso di autenticazione. Questa operazione può essere eseguita selezionando il provider di identità Facebook e immettendo i valori di **ID app** e **segreto app** dalle impostazioni dell'app Facebook nel centro per sviluppatori Facebook. Per altre informazioni, vedere [aggiungere le informazioni di Facebook all'applicazione](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

### <a name="xamarinforms-application-configuration"></a>Xamarin.FormsConfigurazione dell'applicazione

Il processo per la configurazione dell' Xamarin.Forms applicazione di esempio è il seguente:

1. Aprire la Xamarin.Forms soluzione.
1. Aprire `Constants.cs` e aggiornare i valori delle costanti seguenti:
    - `EndpointUri`: il valore deve essere l'URL dell'account Cosmos DB dal pannello chiavi dell'account di Cosmos DB.
    - `DatabaseName`: il valore deve essere il nome del database del documento.
    - `CollectionName`: il valore deve essere il nome della raccolta di database di documenti (in questo caso `UserItems` ).
    - `ResourceTokenBrokerUrl`: il valore deve essere l'URL dell'app Web broker token di risorsa nel pannello Panoramica dell'account del servizio app.

## <a name="initiating-login"></a>Avvio dell'accesso

L'applicazione di esempio avvia il processo di accesso tramite il reindirizzamento di un browser a un URL del provider di identità, come illustrato nel codice di esempio seguente:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

In questo modo viene avviato un flusso di autenticazione OAuth tra app Azure Service e Facebook, che visualizza la pagina di accesso di Facebook:

![](azure-cosmosdb-auth-images/login.png "Facebook Login")

L'account di accesso può essere annullato premendo il pulsante **Annulla** in iOS o premendo il pulsante **indietro** in Android, nel qual caso l'utente rimane non autenticato e l'interfaccia utente del provider di identità viene rimossa dalla schermata.

## <a name="obtaining-a-resource-token"></a>Acquisizione di un token di risorsa

Dopo l'autenticazione corretta, viene `WebRedirectAuthenticator.Completed` generato l'evento. Nell'esempio di codice seguente viene illustrata la gestione di questo evento:

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

Il risultato di un'autenticazione riuscita è un token di accesso, che è la `AuthenticatorCompletedEventArgs.Account` proprietà disponibile. Il token di accesso viene estratto e usato in una richiesta GET all'API del broker del token di risorsa `resourcetoken` .

L' `resourcetoken` API usa il token di accesso per richiedere l'identità dell'utente da Facebook, che a sua volta viene usato per richiedere un token di risorsa da Cosmos DB. Se un documento di autorizzazione valido esiste già per l'utente nel database di documenti, viene recuperato e viene restituito all'applicazione un documento JSON contenente il token di risorsa Xamarin.Forms . Se per l'utente non esiste un documento di autorizzazione valido, viene creato un utente e un'autorizzazione nel database del documento e il token di risorsa viene estratto dal documento di autorizzazione e restituito all' Xamarin.Forms applicazione in un documento JSON.

> [!NOTE]
> Un utente del database di documenti è una risorsa associata a un database di documenti e ogni database può contenere zero o più utenti. Un'autorizzazione per il database di documenti è una risorsa associata a un utente del database di documenti e ogni utente può contenere zero o più autorizzazioni. Una risorsa di autorizzazione consente di accedere a un token di sicurezza richiesto dall'utente durante il tentativo di accedere a una risorsa, ad esempio un documento.

Se l' `resourcetoken` API viene completata correttamente, invierà il codice di stato HTTP 200 (OK) nella risposta, insieme a un documento JSON che contiene il token di risorsa. I dati JSON seguenti mostrano un tipico messaggio di risposta riuscito:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

Il `WebRedirectAuthenticator.Completed` gestore eventi legge la risposta dall' `resourcetoken` API ed estrae il token di risorsa e l'ID utente. Il token della risorsa viene quindi passato come argomento al `DocumentClient` costruttore, che incapsula l'endpoint, le credenziali e i criteri di connessione usati per accedere a Cosmos DB e viene usato per configurare ed eseguire le richieste rispetto a Cosmos DB. Il token della risorsa viene inviato con ogni richiesta per accedere direttamente a una risorsa e indica che l'accesso in lettura/scrittura alla raccolta partizionata degli utenti autenticati viene concesso.

## <a name="retrieving-documents"></a>Recupero di documenti

Il recupero di documenti che appartengono solo all'utente autenticato può essere eseguito creando una query del documento che include l'ID dell'utente come chiave di partizione e viene illustrato nell'esempio di codice seguente:

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

La query recupera in modo asincrono tutti i documenti appartenenti all'utente autenticato, dalla raccolta specificata e li inserisce in una `List<TodoItem>` raccolta per la visualizzazione.

Il `CreateDocumentQuery<T>` metodo specifica un `Uri` argomento che rappresenta la raccolta su cui devono essere eseguite query per i documenti e un `FeedOptions` oggetto. L' `FeedOptions` oggetto specifica che la query può restituire un numero illimitato di elementi e l'ID dell'utente come chiave di partizione. In questo modo si garantisce che nel risultato vengano restituiti solo i documenti della raccolta partizionata dell'utente.

> [!NOTE]
> Si noti che i documenti di autorizzazione creati da Resource token Broker vengono archiviati nella stessa raccolta documenti dei documenti creati dall' Xamarin.Forms applicazione. La query del documento contiene pertanto una `Where` clausola che applica un predicato di filtro alla query sulla raccolta di documenti. Questa clausola garantisce che i documenti di autorizzazione non vengano restituiti dalla raccolta di documenti.

Per ulteriori informazioni sul recupero di documenti da una raccolta di documenti, vedere [recupero di documenti della raccolta](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#retrieving-document-collection-documents)documenti.

## <a name="inserting-documents"></a>Inserimento di documenti

Prima di inserire un documento in una raccolta di documenti, la `TodoItem.UserId` proprietà deve essere aggiornata con il valore usato come chiave di partizione, come illustrato nell'esempio di codice seguente:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

In questo modo si garantisce che il documento venga inserito nella raccolta partizionata dell'utente.

Per ulteriori informazioni sull'inserimento di un documento in una raccolta di documenti, vedere [inserimento di un documento in una raccolta di documenti](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting-a-document-into-a-document-collection).

## <a name="deleting-documents"></a>Eliminazione di documenti

Il valore della chiave di partizione deve essere specificato quando si elimina un documento da una raccolta partizionata, come illustrato nell'esempio di codice seguente:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

In questo modo si garantisce che Cosmos DB sappia da quale raccolta partizionata eliminare il documento.

Per ulteriori informazioni sull'eliminazione di un documento da una raccolta di documenti, vedere Eliminazione di un [documento da una raccolta di documenti](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting-a-document-from-a-document-collection).

## <a name="summary"></a>Summary

Questo articolo ha illustrato come combinare il controllo di accesso con le raccolte partizionate, in modo che un utente possa accedere solo ai propri documenti del database di documenti in un' Xamarin.Forms applicazione. La specifica dell'identità dell'utente come chiave di partizione assicura che una raccolta partizionata possa archiviare solo i documenti per tale utente.

## <a name="related-links"></a>Collegamenti correlati

- [Todo Azure Cosmos DB auth (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [Utilizzo di un database di documenti di Azure Cosmos DB](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [Protezione dell'accesso ai dati in Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Controllo di accesso nell'API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Come eseguire il partizionamento e il ridimensionamento in Azure Cosmos DB](/azure/cosmos-db/partition-data/)
- [Libreria client di Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API di Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
