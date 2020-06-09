---
title: "Cerca dati con ricerca di Azure e Xamarin.Forms " Descrizione: "in questo articolo viene illustrato come usare la libreria di ricerca Microsoft Azure per integrare ricerca di Azure in un' Xamarin.Forms applicazione."
ms. prod: Novell MS. AssetID: A4AEF233-3672-4174-9DBA-15BEE3030C0B ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 12/05/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="search-data-with-azure-search-and-xamarinforms"></a>Cerca i dati con ricerca di Azure eXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)

_Ricerca di Azure è un servizio cloud che fornisce funzionalità di indicizzazione ed esecuzione di query per i dati caricati. In questo modo vengono rimossi i requisiti dell'infrastruttura e le complessità degli algoritmi di ricerca tradizionalmente associati all'implementazione della funzionalità di ricerca in un'applicazione. Questo articolo illustra come usare la libreria di ricerca Microsoft Azure per integrare ricerca di Azure in un' Xamarin.Forms applicazione._

## <a name="overview"></a>Panoramica

I dati vengono archiviati in ricerca di Azure come indici e documenti. Un *Indice* è un archivio di dati che possono essere cercati dal servizio ricerca di Azure ed è concettualmente simile a una tabella di database. Un *documento* è una singola unità di dati ricercabili in un indice ed è concettualmente simile a una riga di database. Quando si caricano documenti e si inviano query di ricerca in ricerca di Azure, le richieste vengono effettuate a un indice specifico nel servizio di ricerca.

Ogni richiesta effettuata a ricerca di Azure deve includere il nome del servizio e una chiave API. Esistono due tipi di chiave API:

- Le *chiavi amministratore* concedono diritti completi a tutte le operazioni. Ciò include la gestione del servizio, la creazione e l'eliminazione di indici e origini dati.
- Le *chiavi di query* concedono l'accesso in sola lettura agli indici e ai documenti e devono essere usate dalle applicazioni che inviano richieste di ricerca.

La richiesta più comune a ricerca di Azure consiste nell'eseguire una query. Esistono due tipi di query che è possibile inviare:

- Una query di *ricerca* cerca uno o più elementi in tutti i campi disponibili per la ricerca in un indice. Le query di ricerca vengono compilate usando la sintassi semplificata o la sintassi di query Lucene. Per altre informazioni, vedere [sintassi di query semplice in ricerca di Azure](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/)e [sintassi di query Lucene in ricerca di Azure](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- Una query di *filtro* valuta un'espressione booleana su tutti i campi filtrabili in un indice. Le query di filtro vengono compilate utilizzando un subset del linguaggio del filtro OData. Per altre informazioni, vedere [sintassi delle espressioni OData per ricerca di Azure](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Le query di ricerca e di filtro possono essere usate separatamente o insieme. Quando vengono utilizzate insieme, la query del filtro viene applicata prima all'intero indice, quindi la query di ricerca viene eseguita sui risultati della query del filtro.

Ricerca di Azure supporta anche il recupero di suggerimenti basati sull'input di ricerca. Per altre informazioni, vedere [query di suggerimento](#suggestion-queries).

> [!NOTE]
> Se non si ha una [sottoscrizione di Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://aka.ms/azfree-docs-mobileapps) prima di iniziare.

## <a name="setup"></a>Configurazione

Il processo per l'integrazione di ricerca di Azure in un' Xamarin.Forms applicazione è il seguente:

1. Creare un servizio di ricerca di Azure. Per altre informazioni, vedere [creare un servizio di ricerca di Azure tramite il portale di Azure](/azure/search/search-create-service-portal/).
1. Rimuovere Silverlight come Framework di destinazione dalla libreria di classi portabile Xamarin.Forms (PCL) della soluzione. Questa operazione può essere eseguita modificando il profilo PCL in qualsiasi profilo che supporta lo sviluppo multipiattaforma, ma non supporta Silverlight, ad esempio profile 151 o profile 92.
1. Aggiungere il pacchetto NuGet della [libreria di ricerca Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search) al progetto PCL nella Xamarin.Forms soluzione.

Dopo aver eseguito questi passaggi, è possibile usare l'API di Microsoft Search Library per gestire gli indici di ricerca e le origini dati, caricare e gestire documenti ed eseguire query.

## <a name="creating-the-azure-search-index"></a>Creazione di un indice di Ricerca di Azure

È necessario definire uno schema dell'indice che esegue il mapping alla struttura dei dati in cui eseguire la ricerca. Questa operazione può essere eseguita nel portale di Azure o a livello di codice tramite la `SearchServiceClient` classe. Questa classe gestisce le connessioni a ricerca di Azure e può essere usata per creare un indice. Nell'esempio di codice riportato di seguito viene illustrato come creare un'istanza di questa classe:

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

L' `SearchServiceClient` Overload del costruttore accetta un nome del servizio di ricerca e un `SearchCredentials` oggetto come argomenti, con l'oggetto che esegue `SearchCredentials` il wrapping della *chiave di amministrazione* per il servizio ricerca di Azure. Per creare un indice, è necessaria la *chiave amministratore* .

> [!NOTE]
> `SearchServiceClient`È consigliabile usare una singola istanza in un'applicazione per evitare l'apertura di un numero eccessivo di connessioni a ricerca di Azure.

Un indice viene definito dall' `Index` oggetto, come illustrato nell'esempio di codice seguente:

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

La `Index.Name` proprietà deve essere impostata sul nome dell'indice e la `Index.Fields` proprietà deve essere impostata su una matrice di `Field` oggetti. Ogni `Field` istanza specifica un nome, un tipo e tutte le proprietà che specificano come viene usato il campo. Queste proprietà includono:

- `IsKey`: indica se il campo è la chiave dell'indice. Solo un campo nell'indice, di tipo `DataType.String` , deve essere designato come campo chiave.
- `IsFacetable`: indica se è possibile eseguire l'esplorazione in base a facet in questo campo. Il valore predefinito è `false`.
- `IsFilterable`: indica se il campo può essere usato nelle query di filtro. Il valore predefinito è `false`.
- `IsRetrievable`: indica se il campo può essere recuperato nei risultati della ricerca. Il valore predefinito è `true`.
- `IsSearchable`: indica se il campo è incluso nelle ricerche full-text. Il valore predefinito è `false`.
- `IsSortable`: indica se il campo può essere utilizzato nelle `OrderBy` espressioni. Il valore predefinito è `false`.

> [!NOTE]
> La modifica di un indice dopo la distribuzione comporta la ricompilazione e il ricaricamento dei dati.

Un `Index` oggetto può facoltativamente specificare una `Suggesters` proprietà, che definisce i campi dell'indice da utilizzare per supportare le query di ricerca o di completamento automatico. La `Suggesters` proprietà deve essere impostata su una matrice di `Suggester` oggetti che definiscono i campi utilizzati per compilare i risultati dei suggerimenti di ricerca.

Dopo la creazione dell' `Index` oggetto, l'indice viene creato chiamando `Indexes.Create` sull' `SearchServiceClient` istanza.

> [!NOTE]
> Quando si crea un indice da un'applicazione che deve essere mantenuta reattiva, utilizzare il `Indexes.CreateAsync` metodo.

Per altre informazioni, vedere [creare un indice di ricerca di Azure con .NET SDK](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>Eliminazione dell'indice di ricerca di Azure

Un indice può essere eliminato chiamando `Indexes.Delete` sull' `SearchServiceClient` istanza:

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Caricamento dei dati nell'indice di ricerca di Azure

Dopo aver definito l'indice, è possibile caricarvi i dati usando uno dei due modelli seguenti:

- **Modello pull** : i dati vengono inseriti periodicamente da Azure Cosmos DB, dal database SQL di Azure, dall'archiviazione BLOB di Azure o da SQL Server ospitati in una macchina virtuale di Azure.
- **Modello push** : i dati vengono inviati a livello di codice all'indice. Questo è il modello adottato in questo articolo.

`SearchIndexClient`È necessario creare un'istanza di per importare dati nell'indice. Questa operazione può essere eseguita chiamando il `SearchServiceClient.Indexes.GetClient` metodo, come illustrato nell'esempio di codice seguente:

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

I dati da importare nell'indice vengono inclusi nel pacchetto come `IndexBatch` oggetto, che incapsula una raccolta di `IndexAction` oggetti. Ogni `IndexAction` istanza contiene un documento e una proprietà che indica a ricerca di Azure l'azione da eseguire sul documento. Nell'esempio di codice precedente `IndexAction.Upload` viene specificata l'azione, che consente di inserire il documento nell'indice se è nuovo o di sostituirlo se esiste già. L' `IndexBatch` oggetto viene quindi inviato all'indice chiamando il `Documents.Index` metodo sull' `SearchIndexClient` oggetto. Per informazioni sulle altre azioni di indicizzazione, vedere [decidere quale azione di indicizzazione usare](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use).

> [!NOTE]
> In una singola richiesta di indicizzazione è possibile includere solo 1000 documenti.

Si noti che nell'esempio di codice precedente la `monkeyList` raccolta viene creata come oggetto anonimo da una raccolta di `Monkey` oggetti. In questo modo vengono creati i dati per il `id` campo e viene risolto il mapping dei nomi delle proprietà del case Pascal `Monkey` ai nomi dei campi dell'indice di ricerca Camel. In alternativa, questo mapping può essere eseguito anche aggiungendo l' `[SerializePropertyNamesAsCamelCase]` attributo alla `Monkey` classe.

Per altre informazioni, vedere [caricare dati in ricerca di Azure con .NET SDK](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>Esecuzione di query sull'indice di ricerca di Azure

`SearchIndexClient`Per eseguire una query su un indice, è necessario creare un'istanza. Quando un'applicazione esegue query, è consigliabile seguire il principio dei privilegi minimi e creare direttamente un oggetto `SearchIndexClient` , passando la *chiave di query* come argomento. In questo modo si garantisce che gli utenti dispongano dell'accesso in sola lettura a indici e documenti. Questo approccio viene illustrato nell'esempio di codice seguente:

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

L' `SearchIndexClient` Overload del costruttore accetta un nome del servizio di ricerca, un nome di indice e un `SearchCredentials` oggetto come argomenti, con l'oggetto che esegue il `SearchCredentials` wrapping della *chiave di query* per il servizio ricerca di Azure.

### <a name="search-queries"></a>Query di ricerca

È possibile eseguire una query sull'indice chiamando il `Documents.SearchAsync` metodo sull' `SearchIndexClient` istanza, come illustrato nell'esempio di codice seguente:

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

Il `SearchAsync` metodo accetta un argomento di testo di ricerca e un `SearchParameters` oggetto facoltativo che può essere usato per perfezionare ulteriormente la query. Una query di ricerca viene specificata come argomento del testo di ricerca, mentre è possibile specificare una query di filtro impostando la `Filter` proprietà dell' `SearchParameters` argomento. Nell'esempio di codice seguente vengono illustrati entrambi i tipi di query:

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Questa query di filtro viene applicata all'intero indice e rimuove i documenti dai risultati in cui il `location` campo non è uguale a Cina e non è uguale a Vietnam. Dopo l'applicazione di filtri, la query di ricerca viene eseguita sui risultati della query del filtro.

> [!NOTE]
> Per filtrare senza eseguire ricerche, passare `*` come argomento del testo di ricerca.

Il `SearchAsync` metodo restituisce un `DocumentSearchResult` oggetto che contiene i risultati della query. Questo oggetto viene enumerato, in cui ogni `Document` oggetto viene creato come `Monkey` oggetto e aggiunto a `Monkeys` `ObservableCollection` per la visualizzazione. Gli screenshot seguenti mostrano i risultati della query di ricerca restituiti da ricerca di Azure:

![](azure-search-images/search.png "Search Results")

Per altre informazioni sulla ricerca e sul filtro, vedere [eseguire query nell'indice di ricerca di Azure con .NET SDK](/azure/search/search-query-dotnet/).

### <a name="suggestion-queries"></a>Query sui suggerimenti

Ricerca di Azure consente di richiedere suggerimenti in base a una query di ricerca, chiamando il `Documents.SuggestAsync` metodo sull' `SearchIndexClient` istanza. Questa operazione è illustrata nell'esempio di codice seguente:

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

Il `SuggestAsync` metodo accetta un argomento di testo di ricerca, il nome del suggeritore da usare (definito nell'indice) e un `SuggestParameters` oggetto facoltativo che può essere usato per perfezionare ulteriormente la query. L' `SuggestParameters` istanza di imposta le proprietà seguenti:

- `UseFuzzyMatching`: se è impostato su `true` , ricerca di Azure troverà suggerimenti anche se nel testo di ricerca è presente un carattere sostituito o mancante.
- `HighlightPreTag`: il tag che viene anteposto ai suggerimenti riscontri.
- `HighlightPostTag`: il tag che viene aggiunto ai suggerimenti riscontri.
- `MinimumCoverage`: rappresenta la percentuale di indice che deve essere coperta da una query di suggerimento affinché la query venga segnalata come completata. Il valore predefinito è 80.
- `Top`: numero di suggerimenti da recuperare. Deve essere un numero intero compreso tra 1 e 100 e il valore predefinito è 5.

L'effetto complessivo è che i primi 10 risultati dall'indice verranno restituiti con l'evidenziazione dei risultati e i risultati includeranno documenti che includono termini di ricerca con ortografia.

Il `SuggestAsync` metodo restituisce un `DocumentSuggestResult` oggetto che contiene i risultati della query. Questo oggetto viene enumerato, in cui ogni `Document` oggetto viene creato come `Monkey` oggetto e aggiunto a `Monkeys` `ObservableCollection` per la visualizzazione. Gli screenshot seguenti mostrano i risultati dei suggerimenti restituiti da ricerca di Azure:

![](azure-search-images/suggest.png "Suggestion Results")

Si noti che nell'applicazione di esempio, il `SuggestAsync` metodo viene richiamato solo quando l'utente completa l'inserimento di un termine di ricerca. Tuttavia, può anche essere usato per supportare le query di ricerca con completamento automatico eseguendo a ogni pressione del tasto.

## <a name="summary"></a>Summary

Questo articolo ha illustrato come usare la libreria di ricerca Microsoft Azure per integrare ricerca di Azure in un' Xamarin.Forms applicazione. Ricerca di Azure è un servizio cloud che fornisce funzionalità di indicizzazione ed esecuzione di query per i dati caricati. In questo modo vengono rimossi i requisiti dell'infrastruttura e le complessità degli algoritmi di ricerca tradizionalmente associati all'implementazione della funzionalità di ricerca in un'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Ricerca di Azure (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)
- [Documentazione di ricerca di Azure](/azure/search/)
- [Libreria di ricerca Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search/)
