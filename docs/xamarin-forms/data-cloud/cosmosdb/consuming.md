---
title: Utilizzo di un Database di documenti di Azure Cosmos DB
description: "Un database di documenti di Azure Cosmos DB è un database NoSQL che fornisce accesso a bassa latenza per i documenti JSON, che offre un servizio di database veloce, a disponibilità elevata, scalabili per applicazioni che richiedono scalabilità trasparente e replica globale. In questo articolo viene illustrato come utilizzare la libreria Client di Microsoft Azure DocumentDB per integrare un database di documenti di Azure Cosmos DB in un'applicazione di xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 41e28366a856f5f0c12db6087117aebb4de72844
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="consuming-an-azure-cosmos-db-document-database"></a>Utilizzo di un Database di documenti di Azure Cosmos DB

_Un database di documenti di Azure Cosmos DB è un database NoSQL che fornisce accesso a bassa latenza per i documenti JSON, che offre un servizio di database veloce, a disponibilità elevata, scalabili per applicazioni che richiedono scalabilità trasparente e replica globale. In questo articolo viene illustrato come utilizzare la libreria Client di Microsoft Azure DocumentDB per integrare un database di documenti di Azure Cosmos DB in un'applicazione di xamarin. Forms._

## <a name="overview"></a>Panoramica

Effettuare il provisioning un account di database di Azure Cosmos DB documento mediante una sottoscrizione di Azure. Ogni account del database può avere zero o più database. Un database di documenti nel database di Azure Cosmos è un contenitore logico per le raccolte documenti e gli utenti.

Un database di documenti di Azure Cosmos DB può contenere zero o più raccolte di documenti. Ogni raccolta di documento può avere un livello di prestazioni differenti, che consente una velocità effettiva maggiore per le raccolte a cui si accede frequentemente e la minore velocità effettiva per le raccolte di cui si accede raramente.

Ogni raccolta di documento è costituito da zero o più documenti JSON. In una raccolta documenti sono privi di schema e pertanto non è necessario condividere la stessa struttura o i campi. Quando i documenti vengono aggiunti a una raccolta di documenti, DB Cosmos vengono indicizzati automaticamente insieme a mano che diventano disponibili per eseguire una query.

A scopo di sviluppo, un database di documenti può essere utilizzato anche tramite un emulatore. Utilizza l'emulatore, le applicazioni possono essere sviluppate e testate in locale, senza creare una sottoscrizione di Azure o eventuali costi. Per ulteriori informazioni sull'emulatore di, vedere [lo sviluppo in locale con l'emulatore di Azure Cosmos DB](/azure/cosmos-db/local-emulator/).

In questo articolo e accompagna l'applicazione di esempio viene illustrata un'applicazione di elenco di attività in cui le attività vengono archiviate in un database di documenti di Azure Cosmos DB. Per ulteriori informazioni sull'applicazione di esempio, vedere [comprensione dell'esempio](~/xamarin-forms/data-cloud/walkthrough.md).

> [!NOTE]
> La libreria Client di DocumentDB non è attualmente compatibile con le applicazioni di Windows della piattaforma UWP (Universal). Tuttavia, un database di documenti di Azure Cosmos DB utilizzabile da un'applicazione UWP creando un servizio web di livello intermedio che usa la libreria Client di DocumentDB e richiamare il servizio dall'applicazione UWP.

Per ulteriori informazioni su Azure Cosmos DB, vedere il [documentazione di Azure Cosmos DB](/azure/cosmos-db/).

## <a name="setup"></a>Configurazione

Il processo per l'integrazione di un database di documenti di Azure Cosmos DB in un'applicazione di xamarin. Forms è come segue:

1. Creare un account DB Cosmos. Per ulteriori informazioni, vedere [creare un account Cosmos DB](/azure/cosmos-db/documentdb-dotnetcore-get-started#step-1-create-a-documentdb-account).
1. Aggiungere il [libreria Client DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) pacchetto NuGet per i progetti di piattaforma della soluzione xamarin. Forms.
1. Aggiungere `using` direttive per il `Microsoft.Azure.Documents`, `Microsoft.Azure.Documents.Client`, e `Microsoft.Azure.Documents.Linq` gli spazi dei nomi alle classi di accesso all'account DB Cosmos.

Dopo aver eseguito questi passaggi, è possibile utilizzare la libreria Client di DocumentDB per configurare ed eseguire le richieste nel database di documenti.

> [!NOTE]
> La libreria Client di Azure DocumentDB può essere installata solo nei progetti di piattaforma e non in un progetto libreria di classe portabile (PCL). Pertanto, l'applicazione di esempio è un condiviso accesso progetto SAP () per evitare la duplicazione del codice. Tuttavia, la `DependencyService` classe può essere utilizzata in un progetto libreria di classi Portabile per richiamare il codice di libreria Client di Azure DocumentDB contenuto nei progetti specifici della piattaforma.

## <a name="consuming-the-azure-cosmos-db-account"></a>Utilizzo di account Azure Cosmos DB

Il `DocumentClient` tipo incapsula l'endpoint, credenziali e i criteri di connessione utilizzata per accedere all'account Azure Cosmos DB e viene usato per configurare ed eseguire le richieste per l'account. Esempio di codice riportato di seguito viene illustrato come creare un'istanza di questa classe:

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

È necessario specificare l'Uri DB Cosmos e la chiave primaria per la `DocumentClient` costruttore. Che possono essere ottenuti dal portale di Azure. Per ulteriori informazioni, vedere [Connetti a un account Azure Cosmos DB](/azure/cosmos-db/documentdb-dotnetcore-get-started#a-idconnectastep-3-connect-to-an-azure-cosmos-db-account).

### <a name="creating-a-database"></a>Creazione di un Database

Un database di documenti è un contenitore logico per le raccolte documenti e gli utenti e può essere creato nel portale di Azure oppure a livello di codice mediante il `DocumentClient.CreateDatabaseIfNotExistsAsync` metodo:

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

Il `CreateDatabaseIfNotExistsAsync` metodo specifica un `Database` oggetto come argomento, con la `Database` oggetto che specifica il nome del database come relativo `Id` proprietà. Il `CreateDatabaseIfNotExistsAsync` metodo crea il database se non esiste o se il database esiste già. Tuttavia, l'applicazione di esempio ignora tutti i dati restituiti dal `CreateDatabaseIfNotExistsAsync` metodo.

> [!NOTE]
> Il `CreateDatabaseIfNotExistsAsync` metodo restituisce un `Task<ResourceResponse<Database>>` oggetto e il codice di stato della risposta può essere verificata per determinare se è stato creato un database o un database esistente è stato restituito.

### <a name="creating-a-document-collection"></a>Creazione di una raccolta di documenti

Una raccolta di documenti è un contenitore di documenti JSON e può essere creato nel portale di Azure oppure a livello di codice mediante il `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` metodo:

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

Il `CreateDocumentCollectionIfNotExistsAsync` metodo richiede due argomenti obbligatori: un nome di database specificato come un `Uri`e un `DocumentCollection` oggetto. Il `DocumentCollection` oggetto rappresenta una raccolta di documenti il cui nome è specificato con il `Id` proprietà. Il `CreateDocumentCollectionIfNotExistsAsync` metodo crea la raccolta documenti se non esiste o restituisce la raccolta documenti se esiste già. Tuttavia, l'applicazione di esempio ignora tutti i dati restituiti dal `CreateDocumentCollectionIfNotExistsAsync` metodo.

> [!NOTE]
> Il `CreateDocumentCollectionIfNotExistsAsync` metodo restituisce un `Task<ResourceResponse<DocumentCollection>>` oggetto e il codice di stato della risposta può essere controllato per determinare se una raccolta di documenti è stata creata, o è stato restituito un insieme di documento esistente.

Facoltativamente, il `CreateDocumentCollectionIfNotExistsAsync` metodo inoltre è possibile specificare un `RequestOptions` oggetto, che include opzioni che possono essere specificate per le richieste emesse per l'account di database Cosmos. Il `RequestOptions.OfferThroughput` proprietà viene utilizzata per definire il livello di prestazioni della raccolta documenti, e nell'esempio di applicazione, è impostata su 400 unità di richiesta al secondo. Questo valore deve aumentare o diminuire a seconda se la raccolta di frequente o raramente si accederà.

> [!IMPORTANT]
> Si noti che il `CreateDocumentCollectionIfNotExistsAsync` metodo creerà una nuova raccolta con una velocità effettiva riservati, che ha implicazioni prezzi.

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>Recupero di documenti raccolta documenti

Il contenuto di una raccolta di documenti può essere recuperato tramite la creazione e l'esecuzione di una query del documento. Una query del documento viene creata con il `DocumentClient.CreateDocumentQuery` metodo:

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

Questa query in modo asincrono recupera tutti i documenti dalla raccolta specificata e inserisce i documenti in un `List<TodoItem>` insieme per la visualizzazione.

Il `CreateDocumentQuery<T>` metodo specifica un `Uri` argomento che rappresenta la raccolta che è necessario eseguire una query per i documenti. In questo esempio, il `collectionLink` variabile è un campo a livello di classe che specifica il `Uri` che rappresenta la raccolta documenti per recuperare i documenti da:

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

Il `CreateDocumentQuery<T>` metodo crea una query che viene eseguita in modo sincrono e restituisce un `IQueryable<T>` oggetto. Tuttavia, il `AsDocumentQuery` metodo converte il `IQueryable<T>` l'oggetto in un `IDocumentQuery<T>` oggetto che può essere eseguito in modo asincrono. Viene eseguita la query asincrona con il `IDocumentQuery<T>.ExecuteNextAsync` (metodo), che consente di recuperare la pagina successiva di risultati dal database di documento, con la `IDocumentQuery<T>.HasMoreResults` proprietà che indica se sono presenti altri risultati da restituire dalla query.

I documenti possono essere filtrati lato server includendo un `Where` clausola della query, che viene applicato un predicato di filtro alla query sulla raccolta di documenti:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

Questa query recupera tutti i documenti dalla raccolta la cui `Done` proprietà è uguale a `false`.

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>Inserimento di un documento in una raccolta di documenti

I documenti sono contenuto JSON definiti dall'utente e possono essere inseriti in una raccolta di documenti con la `DocumentClient.CreateDocumentAsync` metodo:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

Il `CreateDocumentAsync` metodo specifica un `Uri` argomento che rappresenta la raccolta di documenti devono essere inseriti, e un `object` argomento che rappresenta il documento da inserire.

### <a name="replacing-a-document-in-a-document-collection"></a>Sostituzione di un documento in una raccolta di documenti

Documenti possono essere sostituiti in una raccolta di documenti con la `DocumentClient.ReplaceDocumentAsync` metodo:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

Il `ReplaceDocumentAsync` metodo specifica un `Uri` argomento che rappresenta il documento nella raccolta che deve essere sostituito, e un `object` argomento che rappresenta i dati del documento aggiornato.

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

Il `DeleteDocumentAsync` metodo specifica un `Uri` argomento che rappresenta il documento nella raccolta che dovrebbe essere eliminato.

### <a name="deleting-a-document-collection"></a>L'eliminazione di una raccolta di documenti

Una raccolta di documenti può essere eliminata da un database con il `DocumentClient.DeleteDocumentCollectionAsync` metodo:

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

Il `DeleteDocumentCollectionAsync` metodo specifica un `Uri` argomento che rappresenta la raccolta di documenti da eliminare. Si noti che questo metodo di richiamo eliminerà anche i documenti archiviati nella raccolta.

### <a name="deleting-a-database"></a>Eliminazione di un Database

Un database può essere eliminato da un account di database Cosmos DB con il `DocumentClient.DeleteDatabaesAsync` metodo:

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

Il `DeleteDatabaseAsync` metodo specifica un `Uri` argomento che rappresenta il database da eliminare. Si noti che questo metodo di richiamo eliminerà anche le raccolte documenti archiviate nel database e i documenti archiviati nelle raccolte documenti.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come utilizzare la libreria Client di Microsoft Azure DocumentDB per integrare un database di documenti di Azure Cosmos DB in un'applicazione di xamarin. Forms. Un database di documenti di Azure Cosmos DB è un database NoSQL che fornisce accesso a bassa latenza per i documenti JSON, che offre un servizio di database veloce, a disponibilità elevata, scalabili per applicazioni che richiedono scalabilità trasparente e replica globale.


## <a name="related-links"></a>Collegamenti correlati

- [TodoDocumentDB (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDB/)
- [Documentazione COSMOS DB](/azure/cosmos-db/)
- [Libreria Client di DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API di Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
