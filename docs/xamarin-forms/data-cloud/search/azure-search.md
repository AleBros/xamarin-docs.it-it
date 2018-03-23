---
title: Ricerca di dati con ricerca di Azure
description: Ricerca di Azure è un servizio cloud che fornisce funzionalità di caricamento dei dati di query e l'indicizzazione. Questa operazione rimuove i requisiti di infrastruttura e le complessità di algoritmo di ricerca in genere associata a implementare la funzionalità di ricerca in un'applicazione. In questo articolo viene illustrato come utilizzare la libreria di ricerca di Microsoft Azure per l'integrazione di ricerca di Azure in un'applicazione di xamarin. Forms.
ms.topic: article
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: 24db1404e218eea86356f9bbc004e7d5850c2e7a
ms.sourcegitcommit: 7b76c3d761b3ffb49541e2e2bcf292de6587c4e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="searching-data-with-azure-search"></a>Ricerca di dati con ricerca di Azure

_Ricerca di Azure è un servizio cloud che fornisce funzionalità di caricamento dei dati di query e l'indicizzazione. Questa operazione rimuove i requisiti di infrastruttura e le complessità di algoritmo di ricerca in genere associata a implementare la funzionalità di ricerca in un'applicazione. In questo articolo viene illustrato come utilizzare la libreria di ricerca di Microsoft Azure per l'integrazione di ricerca di Azure in un'applicazione di xamarin. Forms._

## <a name="overview"></a>Panoramica

Come gli indici e documenti, i dati vengono archiviati in ricerca di Azure. Un *indice* è un archivio dati che possono essere cercati dal servizio di ricerca di Azure ed è concettualmente simile a una tabella di database. Oggetto *documento* è una singola unità di dati ricercabili in un indice ed è concettualmente simile a una riga di database. Quando il caricamento di documenti e l'invio di query di ricerca per la ricerca di Azure, le richieste vengono effettuate in un indice specifico nel servizio di ricerca.

Ogni richiesta effettuata in ricerca di Azure deve includere il nome del servizio e una chiave API. Esistono due tipi di chiave API:

- *Le chiavi amministratore* concedere diritti completi per tutte le operazioni. Ciò include la gestione del servizio, la creazione e l'eliminazione di indici e le origini dati.
- *Le chiavi di query* concedere l'accesso in sola lettura a indici e documenti e deve essere utilizzato da applicazioni che inviano richieste di ricerca.

La richiesta più comune di ricerca di Azure consiste nell'eseguire una query. Esistono due tipi di query che possono essere inviati:

- Oggetto *ricerca* query esegue la ricerca per uno o più elementi in tutti i campi ricercabili in un indice. Query di ricerca vengono compilate mediante la sintassi semplificata, o la sintassi di query Lucene. Per ulteriori informazioni, vedere [semplice sintassi di query in ricerca di Azure](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/), e [Lucene sintassi delle query in ricerca di Azure](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- Oggetto *filtro* query valuta un'espressione booleana su tutti i campi filtrabili in un indice. Filtrare le query vengono compilate mediante un subset del linguaggio filtro OData. Per ulteriori informazioni, vedere [sintassi dell'espressione OData per ricerca di Azure](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Le query di ricerca e filtro possono essere usati separatamente o insieme. Se utilizzati insieme, la query del filtro viene applicata per primo all'intero indice e quindi la query di ricerca viene eseguita sui risultati della query del filtro.

Ricerca di Azure supporta anche il recupero dei suggerimenti in base a input di ricerca. Per ulteriori informazioni, vedere [suggerimento query](#suggestions).

## <a name="setup"></a>Configurazione

Il processo per l'integrazione di ricerca di Azure in un'applicazione di xamarin. Forms è come segue:

1. Creare un servizio di ricerca di Azure. Per ulteriori informazioni, vedere [creare un servizio di ricerca di Azure tramite il portale di Azure](/azure/search/search-create-service-portal/).
1. Rimuovere la soluzione xamarin. Forms libreria di classe portabile (PCL) Silverlight come framework di destinazione. Questo può essere eseguito modificando il profilo della libreria di classi Portabile a qualsiasi profilo che supporta lo sviluppo multipiattaforma, ma non supporta Silverlight, ad esempio profilo 151 o 92.
1. Aggiungere il [libreria di ricerca di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search) pacchetto NuGet al progetto PCL nella soluzione xamarin. Forms.

Dopo aver eseguito questi passaggi, è possibile utilizzare l'API di libreria di ricerca Microsoft per gestire origini dati e indici di ricerca, caricare e gestire documenti ed eseguire query.

## <a name="creating-the-azure-search-index"></a>Creazione dell'indice di ricerca di Azure

È necessario definire uno schema di indice che esegue il mapping alla struttura dei dati da cercare. Può essere effettuata nel portale di Azure o a livello di codice usando la `SearchServiceClient` classe. Questa classe gestisce le connessioni alla ricerca di Azure e può essere utilizzata per creare un indice. Esempio di codice riportato di seguito viene illustrato come creare un'istanza di questa classe:

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

Il `SearchServiceClient` overload del costruttore accetta un nome di servizio di ricerca e un `SearchCredentials` oggetto come argomenti, con il `SearchCredentials` il wrapping di oggetti di *chiave amministratore* per il servizio di ricerca di Azure. Il *chiave amministratore* è necessaria per creare un indice.

> [!NOTE]
>  Un singolo `SearchServiceClient` istanza deve essere utilizzata in un'applicazione per evitare di aprire troppe connessioni in ricerca di Azure.

Un indice è definito dal `Index` dell'oggetto, come illustrato nell'esempio di codice seguente:

```csharp
static void CreateSearchIndex()
{
  var index = new Index()
  {
    Name = Constants.Index,
    Fields = new[]
    {
      new Field("id", DataType.String) { IsKey = true, IsRetrievable = true },
      new Field("name", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("location", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("details", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSearchable = true },
      new Field("imageUrl", DataType.String) { IsRetrievable = true }
    },
    Suggesters = new[]
    {
      new Suggester("nameSuggester", SuggesterSearchMode.AnalyzingInfixMatching, new[] { "name" })
    }
  };

  searchClient.Indexes.Create(index);
}
```

Il `Index.Name` proprietà deve essere impostata sul nome dell'indice e `Index.Fields` proprietà deve essere impostata su una matrice di `Field` oggetti. Ogni `Field` istanza specifica di un nome, un tipo e le proprietà che specificano la modalità in cui viene usato il campo. Queste proprietà includono:

- `IsKey` : indica se il campo è la chiave dell'indice. Un solo campo dell'indice, di tipo `DataType.String`, deve essere designata come campo chiave.
- `IsFacetable` : indica se è possibile eseguire operazioni di navigazione con facet su questo campo. Il valore predefinito è `false`.
- `IsFilterable` : indica se il campo può essere utilizzato nelle query di filtro. Il valore predefinito è `false`.
- `IsRetrievable` : indica se il campo può essere recuperato nei risultati della ricerca. Il valore predefinito è `true`.
- `IsSearchable` : indica se il campo è incluso nelle ricerche full-text. Il valore predefinito è `false`.
- `IsSortable` : indica se il campo può essere utilizzato in `OrderBy` espressioni. Il valore predefinito è `false`.

> [!NOTE]
> La modifica di un indice dopo la distribuzione comporta la ricompilazione e ricaricare i dati.

Un `Index` oggetto è possibile specificare facoltativamente un `Suggesters` proprietà, che definisce i campi dell'indice per essere utilizzato per supportare il completamento automatico o le query di suggerimenti di ricerca. Il `Suggesters` proprietà deve essere impostata su una matrice di `Suggester` oggetti che definiscono i campi utilizzati per compilare la ricerca dei risultati di suggerimento.

Dopo aver creato il `Index` dell'oggetto, l'indice viene creato chiamando `Indexes.Create` sul `SearchServiceClient` istanza.

> [!NOTE]
> Quando si crea un indice da un'applicazione deve essere mantenuta attiva, utilizzare il `Indexes.CreateAsync` metodo.

Per ulteriori informazioni, vedere [creare un indice di ricerca di Azure mediante .NET SDK](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>L'eliminazione dell'indice di ricerca di Azure

Un indice può essere eliminato chiamando `Indexes.Delete` sul `SearchServiceClient` istanza:

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Caricamento dei dati per l'indice di ricerca di Azure

Dopo aver definito l'indice, è possano caricare i dati utilizzando uno dei due modelli:

- **Modello pull** : i dati vengono periodicamente acquisiti da Azure Cosmos DB, Database SQL di Azure, archiviazione Blob di Azure o SQL Server ospitato in una macchina virtuale di Azure.
- **Modello push** -dati a livello di codice viene inviati all'indice. Si tratta del modello adottato in questo articolo.

Oggetto `SearchIndexClient` per importare dati in corrispondenza dell'indice, è necessario creare l'istanza. Questa operazione può essere eseguita chiamando il `SearchServiceClient.Indexes.GetClient` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
static void UploadDataToSearchIndex()
{
  var indexClient = searchClient.Indexes.GetClient(Constants.Index);

  var monkeyList = MonkeyData.Monkeys.Select(m => new
  {
    id = Guid.NewGuid().ToString(),
    name = m.Name,
    location = m.Location,
    details = m.Details,
    imageUrl = m.ImageUrl
  });

  var batch = IndexBatch.New(monkeyList.Select(IndexAction.Upload));
  try
  {
    indexClient.Documents.Index(batch);
  }
  catch (IndexBatchException ex)
  {
    // Sometimes when the Search service is under load, indexing will fail for some
    // documents in the batch. Compensating actions like delaying and retrying should be taken.
    // Here, the failed document keys are logged.
    Console.WriteLine("Failed to index some documents: {0}",
      string.Join(", ", ex.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
  }
}
```

Dati da importare in corrispondenza dell'indice viene compresso come un `IndexBatch` oggetto che incapsula una raccolta di `IndexAction` oggetti. Ogni `IndexAction` istanza contiene un documento e una proprietà che indica l'azione da eseguire sul documento di ricerca di Azure. Nell'esempio di codice precedente, il `IndexAction.Upload` azione viene specificata, in base alla quale il documento viene inserito in corrispondenza dell'indice se è nuovo, o sostituita se esiste già. Il `IndexBatch` oggetto viene quindi inviato all'indice tramite la chiamata di `Documents.Index` metodo il `SearchIndexClient` oggetto. Per informazioni su altre operazioni di indicizzazione, vedere [decidere l'azione di indicizzazione da utilizzare](/azure/search/search-import-data-dotnet#ii-decide-which-indexing-action-to-use).

> [!NOTE]
> Solo 1000 documenti possono essere inclusi in una singola richiesta di indicizzazione.

Si noti che nell'esempio di codice precedente, il `monkeyList` raccolta viene creata come un oggetto anonimo da una raccolta di `Monkey` oggetti. Questo crea i dati per il `id` campo e di risolvere il mapping di minuscole Pascal `Monkey` i nomi dei campi di indice di ricerca di nomi di proprietà in notazione camel. In alternativa, questo mapping può essere effettuato anche aggiungendo il `[SerializePropertyNamesAsCamelCase]` attributo la `Monkey` classe.

Per ulteriori informazioni, vedere [caricare i dati di ricerca di Azure mediante .NET SDK](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>L'esecuzione di query dell'indice di ricerca di Azure

Oggetto `SearchIndexClient` per un indice di query, è necessario creare l'istanza. Quando un'applicazione esegue una query, è consigliabile seguire il principio di privilegio minimo e creare un `SearchIndexClient` direttamente, passando il *chiave di query* come argomento. In questo modo si garantisce che gli utenti dispongano di accesso di sola lettura a indici e documenti. Questo approccio è illustrato nell'esempio di codice seguente:

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

Il `SearchIndexClient` overload del costruttore accetta un nome di servizio di ricerca, il nome dell'indice e un `SearchCredentials` oggetto come argomenti, con la `SearchCredentials` il wrapping di oggetti di *chiave di query* per il servizio di ricerca di Azure.

### <a name="search-queries"></a>Query di ricerca

L'indice è possibile eseguire query tramite la chiamata di `Documents.SearchAsync` metodo il `SearchIndexClient` dell'istanza, come illustrato nell'esempio di codice seguente:

```csharp
async Task AzureSearch(string text)
{
  Monkeys.Clear();

  var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text);
  foreach (SearchResult<Monkey> result in searchResults.Results)
  {
    Monkeys.Add(new Monkey
    {
      Name = result.Document.Name,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

Il `SearchAsync` metodo accetta un argomento di testo di ricerca ed eventualmente `SearchParameters` oggetto che può essere usato per definire ulteriormente la query. Una query di ricerca viene specificata come argomento di testo di ricerca, mentre una query di filtro può essere specificata impostando il `Filter` proprietà del `SearchParameters` argomento. Esempio di codice riportato di seguito viene illustrato come che entrambi i tipi di query:

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Questa query di filtro viene applicata all'intero indice e rimuove i risultati di documenti in cui il `location` campo non è uguale a Cina e non è uguale a Vietnam. Dopo il filtro, la query di ricerca viene eseguita sui risultati della query del filtro.

> [!NOTE]
> Per filtrare senza eseguire ricerche, passare `*` come argomento di testo di ricerca.

Il `SearchAsync` metodo restituisce un `DocumentSearchResult` oggetto che contiene i risultati della query. Questo oggetto viene enumerato, con ogni `Document` dell'oggetto viene creato come un `Monkey` dell'oggetto e quindi aggiungervi il `Monkeys` `ObservableCollection` per la visualizzazione. I seguente screenshot Mostra query risultati di ricerca restituiti da ricerca di Azure:

![](azure-search-images/search.png "Risultati della ricerca")

Per ulteriori informazioni sulla ricerca e filtro, vedere [Query l'indice di ricerca di Azure mediante .NET SDK](/azure/search/search-query-dotnet/).

<a name="suggestions" />

### <a name="suggestion-queries"></a>Query di suggerimento

Ricerca di Azure consente di suggerimenti per la richiesta in base a una query di ricerca, chiamando la `Documents.SuggestAsync` metodo il `SearchIndexClient` istanza. Come illustrato nell'esempio di codice seguente:

```csharp
async Task AzureSuggestions(string text)
{
  Suggestions.Clear();

  var parameters = new SuggestParameters()
  {
    UseFuzzyMatching = true,
    HighlightPreTag = "[",
    HighlightPostTag = "]",
    MinimumCoverage = 100,
    Top = 10
  };

  var suggestionResults =
    await indexClient.Documents.SuggestAsync<Monkey>(text, "nameSuggester", parameters);

  foreach (var result in suggestionResults.Results)
  {
    Suggestions.Add(new Monkey
    {
      Name = result.Text,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

Il `SuggestAsync` metodo accetta un argomento di testo di ricerca, il nome del componente da utilizzare per il suggerimento (definito nell'indice), ed eventualmente `SuggestParameters` oggetto che può essere usato per definire ulteriormente la query. Il `SuggestParameters` istanza imposta le proprietà seguenti:

- `UseFuzzyMatching` -Se impostato su `true`, ricerca di Azure trova i suggerimenti anche se è presente un carattere sostituto o manca nel testo di ricerca.
- `HighlightPreTag` : il tag che viene anteposto ai riscontri suggerimento.
- `HighlightPostTag` : il tag che viene aggiunto al suggerimento riscontri.
- `MinimumCoverage` : rappresenta la percentuale di indice che deve essere analizzata da una query di suggerimenti per la query viene segnalato un esito positivo. Il valore predefinito è 80.
- `Top` : il numero di suggerimenti da recuperare. Deve essere un numero intero compreso tra 1 e 100, con un valore predefinito di 5.

Di conseguenza è che i primi 10 risultati dall'indice verranno restituiti con evidenziazione e i risultati includeranno documenti che includono digitato in modo analogo i termini di ricerca.

Il `SuggestAsync` metodo restituisce un `DocumentSuggestResult` oggetto che contiene i risultati della query. Questo oggetto viene enumerato, con ogni `Document` dell'oggetto viene creato come un `Monkey` dell'oggetto e quindi aggiungervi il `Monkeys` `ObservableCollection` per la visualizzazione. Le schermate seguenti mostrano i risultati di suggerimenti restituiti dalla ricerca di Azure:

![](azure-search-images/suggest.png "Risultati di suggerimento")

Si noti che nell'applicazione di esempio, il `SuggestAsync` metodo viene richiamato solo quando l'utente termina di immissione di un termine di ricerca. Tuttavia, può anche essere utilizzato per supportare le query di ricerca di completamento automatico tramite l'esecuzione in ogni pressione.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare la libreria di ricerca di Microsoft Azure per l'integrazione di ricerca di Azure in un'applicazione di xamarin. Forms. Ricerca di Azure è un servizio cloud che fornisce funzionalità di caricamento dei dati di query e l'indicizzazione. Questa operazione rimuove i requisiti di infrastruttura e le complessità di algoritmo di ricerca in genere associata a implementare la funzionalità di ricerca in un'applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Ricerca di Azure (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)
- [Documentazione di ricerca di Azure](/azure/search/)
- [Libreria di ricerca di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search/)
