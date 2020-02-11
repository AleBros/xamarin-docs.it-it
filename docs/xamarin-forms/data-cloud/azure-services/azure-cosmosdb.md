---
title: Utilizzare un Azure Cosmos DB database di documenti in Xamarin.Forms
description: Questo articolo illustra come usare la libreria client .NET Standard di Azure Cosmos DB per l'integrazione di un database di documenti di Azure Cosmos DB in un'applicazione Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 13abfa9789cee32ed2c7cd2401c1402d9c7c3886
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489778"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-xamarinforms"></a>Utilizzare un Azure Cosmos DB database di documenti in Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)

_Un database di documenti Azure Cosmos DB è un database NoSQL che offre accesso a bassa latenza ai documenti JSON, che offre un servizio di database rapido, a disponibilità elevata e scalabile per le applicazioni che richiedono scalabilità e replica globale senza problemi. Questo articolo illustra come usare la libreria client di .NET Standard Azure Cosmos DB per integrare un database di documenti Azure Cosmos DB in un'applicazione Xamarin.Forms._

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Video Microsoft Azure Cosmos DB**

Essere eseguito il provisioning di un account di database di documenti di Azure Cosmos DB usando una sottoscrizione di Azure. Ogni account di database può avere zero o più database. Un database di documenti in Azure Cosmos DB è un contenitore logico per le raccolte documenti e gli utenti.

Un database di documenti di Azure Cosmos DB può contenere zero o più raccolte di documenti. Ogni raccolta di documenti può avere un livello di prestazioni diverso, che consente una velocità effettiva maggiore per specificare per le raccolte a cui si accede di frequente e una velocità effettiva minore per le raccolte ad accesso sporadico.

Ogni raccolta di documenti è costituito da zero o più documenti JSON. I documenti in una raccolta sono privi di schema e pertanto non sono necessario condividere la stessa struttura o i campi. Man mano che i documenti vengono aggiunti a una raccolta di documenti, Cosmos DB indicizza automaticamente e diventano disponibili per eseguire una query.

Per scopi di sviluppo, un database di documenti può essere utilizzato anche tramite un emulatore. Usa l'emulatore, le applicazioni possono essere sviluppate e testate localmente, senza creare una sottoscrizione di Azure né sostenere costi. Per altre informazioni sull'emulatore, vedere [sviluppa in locale con l'emulatore Azure Cosmos DB](/azure/cosmos-db/local-emulator/).

Questo articolo e applicazione di esempio di accompagnamento illustra un'applicazione elenco attività in cui le attività vengono memorizzate in un database di documenti di Azure Cosmos DB. Per altre informazioni sull'applicazione di esempio, vedere [informazioni sull'esempio](~/xamarin-forms/data-cloud/web-services/introduction.md).

Per altre informazioni su Azure Cosmos DB, vedere la [documentazione di Azure Cosmos DB](/azure/cosmos-db/).

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

## <a name="setup"></a>Programma di installazione

Il processo per l'integrazione di un database di documenti di Azure Cosmos DB in un'applicazione Xamarin.Forms è come segue:

1. Creare un account Cosmos DB. Per altre informazioni, vedere [creare un account Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account).
1. Aggiungere il [libreria client .NET Standard di Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) pacchetto NuGet per i progetti di piattaforma della soluzione Xamarin.Forms.
1. Aggiungere `using` direttive per il `Microsoft.Azure.Documents`, `Microsoft.Azure.Documents.Client`, e `Microsoft.Azure.Documents.Linq` spazi dei nomi per le classi che avrà accesso all'account Cosmos DB.

Dopo aver eseguito questi passaggi, la libreria client di Azure Cosmos DB .NET Standard è utilizzabile per configurare ed eseguire le richieste del database di documenti.

> [!NOTE]
> La libreria client .NET Standard di Azure Cosmos DB può essere installata solo nei progetti di piattaforma e non in un progetto libreria di classi portabile (PCL). Pertanto, l'applicazione di esempio è un Shared Access progetto (SAP) per evitare la duplicazione del codice. Tuttavia, il `DependencyService` classe può essere utilizzata in un progetto libreria di classi Portabile per richiamare il codice di libreria client .NET Standard di Azure Cosmos DB contenuta nei progetti specifici della piattaforma.

## <a name="consuming-the-azure-cosmos-db-account"></a>Utilizzo di account Azure Cosmos DB

Il `DocumentClient` tipo incapsula l'endpoint, credenziali e i criteri di connessione utilizzata per accedere all'account Azure Cosmos DB e viene utilizzato per configurare ed eseguire le richieste in base all'account. Esempio di codice seguente viene illustrato come creare un'istanza di questa classe:

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

È necessario specificare l'Uri di Cosmos DB e la chiave primaria per la `DocumentClient` costruttore. Possono essere ottenuti dal portale di Azure. Per altre informazioni, vedere [connettersi a un account Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect).

### <a name="creating-a-database"></a>Creazione di un database

Un database di documenti è un contenitore logico per le raccolte documenti e gli utenti e può essere creata nel portale di Azure o a livello di codice usando il `DocumentClient.CreateDatabaseIfNotExistsAsync` metodo:

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

Il `CreateDatabaseIfNotExistsAsync` metodo consente di specificare un `Database` dell'oggetto come argomento, con la `Database` oggetto che specifica il nome del database come relativo `Id` proprietà. Il `CreateDatabaseIfNotExistsAsync` metodo crea il database, se non esiste, o restituisce il database se esiste già. Tuttavia, l'applicazione di esempio ignora tutti i dati restituiti dai `CreateDatabaseIfNotExistsAsync` (metodo).

> [!NOTE]
> Il `CreateDatabaseIfNotExistsAsync` metodo restituisce un `Task<ResourceResponse<Database>>` oggetto e il codice di stato della risposta può essere controllato per determinare se è stato creato un database o un database esistente è stato restituito.

### <a name="creating-a-document-collection"></a>Creazione di una raccolta di documenti

Una raccolta di documenti è un contenitore per i documenti JSON e può essere creata nel portale di Azure o a livello di codice usando il `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` metodo:

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

Il `CreateDocumentCollectionIfNotExistsAsync` metodo richiede due argomenti obbligatori: un nome di database specificato come un `Uri`e un `DocumentCollection` oggetto. Il `DocumentCollection` oggetto rappresenta una raccolta di documenti il cui nome è specificato con il `Id` proprietà. Il `CreateDocumentCollectionIfNotExistsAsync` metodo crea la raccolta di documenti, se non esiste, o restituisce la raccolta di documenti, se esiste già. Tuttavia, l'applicazione di esempio ignora tutti i dati restituiti dai `CreateDocumentCollectionIfNotExistsAsync` (metodo).

> [!NOTE]
> Il `CreateDocumentCollectionIfNotExistsAsync` metodo restituisce un `Task<ResourceResponse<DocumentCollection>>` oggetto e il codice di stato della risposta può essere controllato per determinare se è stata creata una raccolta di documenti o se è stata restituita una raccolta di documenti esistenti.

Facoltativamente, il `CreateDocumentCollectionIfNotExistsAsync` metodo inoltre è possibile specificare un `RequestOptions` oggetto che incapsula le opzioni che possono essere specificate per richieste inviate all'account Cosmos DB. Il `RequestOptions.OfferThroughput` proprietà viene utilizzata per definire il livello di prestazioni della raccolta di documenti e nell'esempio di applicazione, è impostato a 400 unità richiesta al secondo. Questo valore deve essere aumentato o ridotto a seconda del fatto che sarà possibile accedere alla raccolta di frequente o sporadico.

> [!IMPORTANT]
> Si noti che il `CreateDocumentCollectionIfNotExistsAsync` metodo creerà una nuova raccolta con una velocità effettiva riservata, che presenta implicazioni in termini di prezzi.

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>Recupero di documenti di una raccolta documenti

Il contenuto di una raccolta di documenti può essere recuperato creando ed eseguendo una query del documento. Viene creata una query di documenti con la `DocumentClient.CreateDocumentQuery` metodo:

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

Questa query in modo asincrono recupera tutti i documenti dalla raccolta specificata e inserisce i documenti in un `List<TodoItem>` raccolta per la visualizzazione.

Il `CreateDocumentQuery<T>` metodo specifica un `Uri` argomento che rappresenta la raccolta da sottoporre a query per i documenti. In questo esempio, il `collectionLink` variabile è un campo a livello di classe che specifica il `Uri` che rappresenta la raccolta di documenti per recuperare i documenti da:

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

Il `CreateDocumentQuery<T>` metodo crea una query che viene eseguita in modo sincrono e restituisce un `IQueryable<T>` oggetto. Tuttavia, il `AsDocumentQuery` metodo converte il `IQueryable<T>` dell'oggetto a un `IDocumentQuery<T>` oggetto che può essere eseguito in modo asincrono. La query asincrona viene eseguita con il `IDocumentQuery<T>.ExecuteNextAsync` metodo, che consente di recuperare la pagina successiva di risultati dal database di documenti, con la `IDocumentQuery<T>.HasMoreResults` proprietà che indica se sono presenti altri risultati da restituire dalla query.

I documenti possono essere filtrati sul lato server includendo un `Where` clausola della query, che viene applicato un predicato del filtro alla query sulla raccolta di documenti:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

Questa query recupera tutti i documenti dalla raccolta la cui `Done` è uguale alla proprietà `false`.

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>Inserimento di un documento in una raccolta di documenti

I documenti sono contenuto JSON definito dall'utente e possono essere inseriti in una raccolta di documenti con la `DocumentClient.CreateDocumentAsync` metodo:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

Il `CreateDocumentAsync` metodo consente di specificare un `Uri` argomento che rappresenta la raccolta di documenti devono essere inseriti in, e un `object` argomento che rappresenta il documento da inserire.

### <a name="replacing-a-document-in-a-document-collection"></a>Sostituzione di un documento in una raccolta di documenti

I documenti possono essere sostituiti in una raccolta di documenti con la `DocumentClient.ReplaceDocumentAsync` metodo:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

Il `ReplaceDocumentAsync` metodo consente di specificare un `Uri` argomento che rappresenta il documento nella raccolta che deve essere sostituito, e un `object` argomento che rappresenta i dati del documento aggiornato.

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>Eliminazione di un documento da una raccolta di documenti

Un documento può essere eliminato da una raccolta di documenti con la `DocumentClient.DeleteDocumentAsync` metodo:

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

Il `DeleteDocumentAsync` metodo specifica un `Uri` argomento che rappresenta il documento nella raccolta da eliminare.

### <a name="deleting-a-document-collection"></a>L'eliminazione di una raccolta di documenti

Una raccolta di documenti può essere eliminata da un database con il `DocumentClient.DeleteDocumentCollectionAsync` metodo:

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

Il `DeleteDocumentCollectionAsync` metodo specifica un `Uri` argomento che rappresenta la raccolta di documenti da eliminare. Si noti che questo metodo di richiamo eliminerà anche i documenti archiviati nella raccolta.

### <a name="deleting-a-database"></a>Eliminazione di un database

Un database può essere eliminato da un account di database Cosmos DB con il `DocumentClient.DeleteDatabaesAsync` metodo:

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

Il `DeleteDatabaseAsync` metodo specifica un `Uri` argomento che rappresenta il database da eliminare. Si noti che questo metodo di richiamo eliminerà anche le raccolte di documenti archiviate nel database e che i documenti archiviati nelle raccolte documenti.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare la libreria client .NET Standard di Azure Cosmos DB per l'integrazione di un database di documenti di Azure Cosmos DB in un'applicazione Xamarin.Forms. Un database di documenti di Azure Cosmos DB è un database NoSQL che fornisce l'accesso a bassa latenza per i documenti JSON, che offre un servizio di database veloce, a disponibilità elevata e scalabile per le applicazioni che richiedono scalabilità e semplicità e la replica globale.

## <a name="related-links"></a>Collegamenti correlati

- [TODO Azure Cosmos DB (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)
- [Documentazione di Azure Cosmos DB](/azure/cosmos-db/)
- [Azure Cosmos DB .NET Standard library di client](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API di Azure Cosmos DB](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
