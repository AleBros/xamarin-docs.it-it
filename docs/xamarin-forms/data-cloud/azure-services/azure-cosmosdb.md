---
title: Utilizzare un database di documenti Azure Cosmos DB inXamarin.Forms
description: Questo articolo illustra come usare la libreria client di .NET Standard Azure Cosmos DB per integrare un database di documenti Azure Cosmos DB in un' Xamarin.Forms applicazione.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
ms.custom: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 47b35d394eab339a8e9a1f81880e6de4233f29b6
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127086"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-xamarinforms"></a>Utilizzare un database di documenti Azure Cosmos DB inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)

_Un database di documenti Azure Cosmos DB è un database NoSQL che offre accesso a bassa latenza ai documenti JSON, che offre un servizio di database rapido, a disponibilità elevata e scalabile per le applicazioni che richiedono scalabilità e replica globale senza problemi. Questo articolo illustra come usare la libreria client di .NET Standard Azure Cosmos DB per integrare un database di documenti Azure Cosmos DB in un' Xamarin.Forms applicazione._

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Video Microsoft Azure Cosmos DB**

È possibile eseguire il provisioning di un account del database di documenti Azure Cosmos DB usando una sottoscrizione di Azure. Ogni account di database può avere zero o più database. Un database di documenti in Azure Cosmos DB è un contenitore logico per raccolte di documenti e utenti.

Un database di documenti Azure Cosmos DB può contenere zero o più raccolte di documenti. Ogni raccolta di documenti può avere un livello di prestazioni diverso, consentendo di specificare una velocità effettiva maggiore per le raccolte a cui si accede di frequente e meno velocità effettiva per le raccolte a cui si accede raramente.

Ogni raccolta di documenti è costituita da zero o più documenti JSON. I documenti in una raccolta sono privi di schema e pertanto non è necessario condividere la stessa struttura o i campi. Man mano che i documenti vengono aggiunti a una raccolta di documenti, Cosmos DB li indicizza automaticamente e diventano disponibili per essere sottoposti a query.

A scopo di sviluppo, un database di documenti può essere utilizzato anche tramite un emulatore. Usando l'emulatore, le applicazioni possono essere sviluppate e testate localmente, senza creare una sottoscrizione di Azure né sostenere costi. Per ulteriori informazioni sull'emulatore, vedere [sviluppo locale con l'emulatore Azure Cosmos DB](/azure/cosmos-db/local-emulator/).

Questo articolo e l'applicazione di esempio associata illustrano un'applicazione elenco attività in cui le attività vengono archiviate in un database di documenti Azure Cosmos DB. Per ulteriori informazioni sull'applicazione di esempio, vedere [Understanding the sample](~/xamarin-forms/data-cloud/web-services/introduction.md).

Per ulteriori informazioni su Azure Cosmos DB, vedere la [documentazione di Azure Cosmos DB](/azure/cosmos-db/).

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

## <a name="setup"></a>Configurazione

Il processo per l'integrazione di un database di documenti Azure Cosmos DB in un' Xamarin.Forms applicazione è il seguente:

1. Creare un account Cosmos DB. Per ulteriori informazioni, vedere la pagina relativa alla [creazione di un account Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account).
1. Aggiungere il pacchetto NuGet della [libreria client .NET Standard Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) ai progetti della piattaforma nella Xamarin.Forms soluzione.
1. Aggiungere `using` direttive per gli `Microsoft.Azure.Documents` `Microsoft.Azure.Documents.Client` `Microsoft.Azure.Documents.Linq` spazi dei nomi, e alle classi che accedono all'account Cosmos DB.

Dopo aver eseguito questi passaggi, è possibile usare la libreria client .NET Standard Azure Cosmos DB per configurare ed eseguire le richieste nel database del documento.

> [!NOTE]
> È possibile installare la libreria client di Azure Cosmos DB .NET Standard solo in progetti di piattaforma e non in un progetto libreria di classi portabile (PCL). Pertanto, l'applicazione di esempio è un progetto di accesso condiviso (SAP) per evitare la duplicazione del codice. Tuttavia, la `DependencyService` classe può essere utilizzata in un progetto PCL per richiamare Azure Cosmos DB .NET standard codice della libreria client contenuto in progetti specifici della piattaforma.

## <a name="consuming-the-azure-cosmos-db-account"></a>Utilizzo dell'account di Azure Cosmos DB

Il `DocumentClient` tipo incapsula l'endpoint, le credenziali e i criteri di connessione usati per accedere all'account Azure Cosmos DB e viene usato per configurare ed eseguire le richieste per l'account. Nell'esempio di codice riportato di seguito viene illustrato come creare un'istanza di questa classe:

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

È necessario fornire al costruttore l'URI Cosmos DB e la chiave primaria `DocumentClient` . Questi possono essere ottenuti dal portale di Azure. Per altre informazioni, vedere [connettersi a un account Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect).

### <a name="creating-a-database"></a>Creazione di un database

Un database di documenti è un contenitore logico per raccolte di documenti e utenti e può essere creato nel portale di Azure o a livello di codice usando il `DocumentClient.CreateDatabaseIfNotExistsAsync` Metodo:

```csharp
public async Task CreateDatabase(string databaseName)
{
  ...
  await client.CreateDatabaseIfNotExistsAsync(new Database
  {
    Id = databaseName
  });
  ...
}
```

Il `CreateDatabaseIfNotExistsAsync` metodo specifica un `Database` oggetto come argomento, con l' `Database` oggetto che specifica il nome del database come `Id` Proprietà. Il `CreateDatabaseIfNotExistsAsync` metodo crea il database se non esiste oppure restituisce il database se esiste già. Tuttavia, l'applicazione di esempio Ignora tutti i dati restituiti dal `CreateDatabaseIfNotExistsAsync` metodo.

> [!NOTE]
> Il `CreateDatabaseIfNotExistsAsync` metodo restituisce un `Task<ResourceResponse<Database>>` oggetto e il codice di stato della risposta può essere controllato per determinare se è stato creato un database o se è stato restituito un database esistente.

### <a name="creating-a-document-collection"></a>Creazione di una raccolta di documenti

Una raccolta di documenti è un contenitore per i documenti JSON, che può essere creata nel portale di Azure o a livello di codice usando il `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` Metodo:

```csharp
public async Task CreateDocumentCollection(string databaseName, string collectionName)
{
  ...
  // Create collection with 400 RU/s
  await client.CreateDocumentCollectionIfNotExistsAsync(
    UriFactory.CreateDatabaseUri(databaseName),
    new DocumentCollection
    {
      Id = collectionName
    },
    new RequestOptions
    {
      OfferThroughput = 400
    });
  ...
}
```

Il `CreateDocumentCollectionIfNotExistsAsync` metodo richiede due argomenti obbligatori, ovvero un nome di database specificato come `Uri` e un `DocumentCollection` oggetto. L' `DocumentCollection` oggetto rappresenta una raccolta di documenti il cui nome è specificato con la `Id` Proprietà. Il `CreateDocumentCollectionIfNotExistsAsync` metodo crea la raccolta di documenti, se non esiste, o restituisce la raccolta di documenti, se già esistente. Tuttavia, l'applicazione di esempio Ignora tutti i dati restituiti dal `CreateDocumentCollectionIfNotExistsAsync` metodo.

> [!NOTE]
> Il `CreateDocumentCollectionIfNotExistsAsync` metodo restituisce un `Task<ResourceResponse<DocumentCollection>>` oggetto e il codice di stato della risposta può essere controllato per determinare se è stata creata una raccolta di documenti o se è stata restituita una raccolta di documenti esistente.

Facoltativamente, il `CreateDocumentCollectionIfNotExistsAsync` metodo può anche specificare un `RequestOptions` oggetto che incapsula le opzioni che possono essere specificate per le richieste inviate all'account Cosmos DB. La `RequestOptions.OfferThroughput` proprietà viene utilizzata per definire il livello di prestazioni della raccolta di documenti e nell'applicazione di esempio è impostata su 400 unità richiesta al secondo. Questo valore deve essere aumentato o diminuito a seconda che la raccolta venga utilizzata di frequente o meno.

> [!IMPORTANT]
> Si noti che il `CreateDocumentCollectionIfNotExistsAsync` metodo creerà una nuova raccolta con una velocità effettiva riservata, che ha implicazioni relative ai prezzi.

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>Recupero dei documenti della raccolta documenti

Il contenuto di una raccolta di documenti può essere recuperato tramite la creazione e l'esecuzione di una query del documento. Viene creata una query di documento con il `DocumentClient.CreateDocumentQuery` Metodo:

```csharp
public async Task<List<TodoItem>> GetTodoItemsAsync()
{
  ...
  var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
            .AsDocumentQuery();
  while (query.HasMoreResults)
  {
    Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
  }
  ...
}
```

Questa query recupera in modo asincrono tutti i documenti dalla raccolta specificata e inserisce i documenti in una `List<TodoItem>` raccolta per la visualizzazione.

Il `CreateDocumentQuery<T>` metodo specifica un `Uri` argomento che rappresenta la raccolta di cui è necessario eseguire una query per i documenti. In questo esempio, la `collectionLink` variabile è un campo a livello di classe che specifica l'oggetto `Uri` che rappresenta la raccolta di documenti da cui recuperare i documenti:

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

Il `CreateDocumentQuery<T>` metodo crea una query che viene eseguita in modo sincrono e restituisce un `IQueryable<T>` oggetto. Tuttavia, il `AsDocumentQuery` metodo converte l' `IQueryable<T>` oggetto in un `IDocumentQuery<T>` oggetto che può essere eseguito in modo asincrono. La query asincrona viene eseguita con il `IDocumentQuery<T>.ExecuteNextAsync` metodo, che recupera la pagina di risultati successiva dal database del documento, con la `IDocumentQuery<T>.HasMoreResults` proprietà che indica se sono presenti altri risultati che devono essere restituiti dalla query.

È possibile filtrare i documenti lato server includendo una `Where` clausola nella query, che applica un predicato di filtro alla query sulla raccolta di documenti:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

Questa query recupera tutti i documenti dalla raccolta la cui `Done` proprietà è uguale a `false` .

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>Inserimento di un documento in una raccolta di documenti

I documenti sono contenuti JSON definiti dall'utente e possono essere inseriti in una raccolta di documenti con il `DocumentClient.CreateDocumentAsync` Metodo:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

Il `CreateDocumentAsync` metodo specifica un `Uri` argomento che rappresenta la raccolta in cui inserire il documento e un `object` argomento che rappresenta il documento da inserire.

### <a name="replacing-a-document-in-a-document-collection"></a>Sostituzione di un documento in una raccolta di documenti

I documenti possono essere sostituiti in una raccolta di documenti con il `DocumentClient.ReplaceDocumentAsync` Metodo:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

Il `ReplaceDocumentAsync` metodo specifica un `Uri` argomento che rappresenta il documento nella raccolta che deve essere sostituito e un `object` argomento che rappresenta i dati del documento aggiornati.

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>Eliminazione di un documento da una raccolta di documenti

Un documento può essere eliminato da una raccolta di documenti con il `DocumentClient.DeleteDocumentAsync` Metodo:

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

Il `DeleteDocumentAsync` metodo specifica un `Uri` argomento che rappresenta il documento nella raccolta da eliminare.

### <a name="deleting-a-document-collection"></a>Eliminazione di una raccolta di documenti

Una raccolta di documenti può essere eliminata da un database con il `DocumentClient.DeleteDocumentCollectionAsync` Metodo:

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

Il `DeleteDocumentCollectionAsync` metodo specifica un `Uri` argomento che rappresenta la raccolta di documenti da eliminare. Si noti che la chiamata a questo metodo eliminerà anche i documenti archiviati nella raccolta.

### <a name="deleting-a-database"></a>Eliminazione di un database

È possibile eliminare un database da un account di database Cosmos DB con il `DocumentClient.DeleteDatabaesAsync` Metodo:

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

Il `DeleteDatabaseAsync` metodo specifica un `Uri` argomento che rappresenta il database da eliminare. Si noti che la chiamata a questo metodo eliminerà anche le raccolte di documenti archiviate nel database e i documenti archiviati nelle raccolte di documenti.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare la libreria client di .NET Standard Azure Cosmos DB per integrare un database di documenti Azure Cosmos DB in un' Xamarin.Forms applicazione. Un database di documenti Azure Cosmos DB è un database NoSQL che offre accesso a bassa latenza ai documenti JSON, che offre un servizio di database rapido, a disponibilità elevata e scalabile per le applicazioni che richiedono scalabilità e replica globale senza problemi.

## <a name="related-links"></a>Collegamenti correlati

- [Todo Azure Cosmos DB (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)
- [Documentazione di Azure Cosmos DB](/azure/cosmos-db/)
- [Azure Cosmos DB libreria client .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API di Azure Cosmos DB](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
