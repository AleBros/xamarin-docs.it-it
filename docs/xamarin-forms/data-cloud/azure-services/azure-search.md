---
title: Dati di ricerca con ricerca di Azure e xamarin. Forms
description: Questo articolo illustra come usare la libreria di ricerca di Microsoft Azure per integrare ricerca di Azure in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: 0ecb546cfb657aa138f05ade64354eb46380e9ab
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658638"
---
# <a name="search-data-with-azure-search-and-xamarinforms"></a>Dati di ricerca con ricerca di Azure e xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)

_Ricerca di Azure è un servizio cloud che offre funzionalità per i dati caricati di query e indicizzazione. Questa operazione rimuove i requisiti di infrastruttura e le complessità di algoritmo di ricerca tradizionalmente associate all'implementazione di funzionalità di ricerca in un'applicazione. Questo articolo illustra come usare la libreria di ricerca di Microsoft Azure per integrare ricerca di Azure in un'applicazione xamarin. Forms._

## <a name="overview"></a>Panoramica

Come gli indici e documenti, i dati vengono archiviati in ricerca di Azure. Un' *indice* è un archivio di dati che possono essere cercati dal servizio di ricerca di Azure ed è concettualmente simile a una tabella di database. Oggetto *documento* è una singola unità di dati ricercabili nell'indice ed è concettualmente simile a una riga di database. Quando il caricamento di documenti e l'invio di query di ricerca in ricerca di Azure, le richieste vengono effettuate in un indice specifico nel servizio di ricerca.

Ogni richiesta effettuata a ricerca di Azure deve includere il nome del servizio e una chiave API. Esistono due tipi di chiave API:

- *Le chiavi amministratore* concedere diritti completi a tutte le operazioni. Ciò include la gestione del servizio, la creazione e l'eliminazione di indici e origini dati.
- *Chiavi di query* concedere l'accesso in lettura a indici e documenti e deve essere utilizzato dalle applicazioni che inviano richieste di ricerca.

La richiesta più comune in ricerca di Azure consiste nell'eseguire una query. Esistono due tipi di query che può essere inviato:

- Oggetto *ricerca* query cerca uno o più elementi in tutti i campi ricercabili in un indice. Le query di ricerca vengono compilate usando la sintassi semplificata, o la sintassi di query Lucene. Per altre informazioni, vedere [semplice sintassi di query in ricerca di Azure](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/), e [sintassi di query Lucene in ricerca di Azure](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- Oggetto *filtro* query valuta un'espressione booleana su tutti i campi filtrabili in un indice. Filtrare le query vengono compilate usando un subset del linguaggio di filtro OData. Per altre informazioni, vedere [sintassi delle espressioni OData per ricerca di Azure](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Query di ricerca e filtrare le query possono essere utilizzate insieme o separatamente. Se usati insieme, la query di filtro viene applicata per primo per l'intero indice e quindi la query di ricerca viene eseguita sui risultati della query di filtro.

Ricerca di Azure supporta anche il recupero dei suggerimenti in base a input di ricerca. Per altre informazioni, vedere [le query di suggerimento](#suggestions).

## <a name="setup"></a>Configurazione

Il processo per l'integrazione di ricerca di Azure in un'applicazione xamarin. Forms è come segue:

1. Creare un servizio di ricerca di Azure. Per altre informazioni, vedere [creare un servizio di ricerca di Azure usando il portale di Azure](/azure/search/search-create-service-portal/).
1. Rimuovere Silverlight come framework di destinazione dalla soluzione xamarin. Forms libreria di classi portabile (PCL). È possibile modificare il profilo di libreria di classi Portabile a qualsiasi profilo che supporta lo sviluppo multipiattaforma, ma non supporta Silverlight, ad esempio profilo 151 o 92.
1. Aggiungere il [libreria di ricerca di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search) pacchetto NuGet al progetto libreria di classi Portabile della soluzione xamarin. Forms.

Dopo aver eseguito questi passaggi, l'API di libreria di ricerca di Microsoft è utilizzabile per gestire origini dati e gli indici di ricerca, caricare e gestire documenti ed eseguire query.

## <a name="creating-the-azure-search-index"></a>Creazione dell'indice di ricerca di Azure

È necessario definire uno schema di indice che viene eseguito il mapping alla struttura dei dati da cercare. Ciò può essere eseguita nel portale di Azure o a livello di codice mediante il `SearchServiceClient` classe. Questa classe gestisce le connessioni a ricerca di Azure e può essere utilizzata per creare un indice. Esempio di codice seguente viene illustrato come creare un'istanza di questa classe:

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

Il `SearchServiceClient` overload del costruttore accetta un nome di servizio di ricerca e una `SearchCredentials` dell'oggetto come argomenti, con la `SearchCredentials` ritorno a capo oggetto il *chiave amministratore* per il servizio di ricerca di Azure. Il *chiave di amministrazione* è necessaria per creare un indice.

> [!NOTE]
>  Un singolo `SearchServiceClient` istanza deve essere usata in un'applicazione per evitare l'apertura di un numero eccessivo di connessioni a ricerca di Azure.

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

Il `Index.Name` proprietà deve essere impostata sul nome dell'indice e il `Index.Fields` deve essere impostata su una matrice di `Field` oggetti. Ogni `Field` istanza specifica un nome di un tipo e le proprietà, che specificano come viene usato il campo. Queste proprietà includono:

- `IsKey` : indica se il campo è la chiave dell'indice. Un solo campo nell'indice, di tipo `DataType.String`, deve essere designato come campo di chiave.
- `IsFacetable` : indica se è possibile eseguire l'esplorazione in base a facet su questo campo. Il valore predefinito è `false`.
- `IsFilterable` : indica se il campo può essere usato nelle query di filtro. Il valore predefinito è `false`.
- `IsRetrievable` : indica se il campo può essere recuperato nei risultati della ricerca. Il valore predefinito è `true`.
- `IsSearchable` : indica se il campo è incluso nelle ricerche full-text. Il valore predefinito è `false`.
- `IsSortable` : indica se il campo può essere utilizzato in `OrderBy` espressioni. Il valore predefinito è `false`.

> [!NOTE]
> La modifica di un indice dopo la distribuzione comporta la ricompilazione e ricaricare i dati.

Un' `Index` oggetti è possono specificare facoltativamente un `Suggesters` proprietà, che definisce i campi dell'indice da utilizzare per supportare il completamento automatico o le query di suggerimento di ricerca. Il `Suggesters` proprietà deve essere impostata su una matrice di `Suggester` gli oggetti che definiscono i campi che vengono usati per compilare la ricerca dei risultati di suggerimento.

Dopo aver creato il `Index` dell'oggetto, l'indice viene creato chiamando `Indexes.Create` nel `SearchServiceClient` istanza.

> [!NOTE]
> Quando si crea un indice da un'applicazione deve mantenere reattiva, usare il `Indexes.CreateAsync` (metodo).

Per altre informazioni, vedere [creare un indice di ricerca di Azure con .NET SDK](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>L'eliminazione dell'indice di ricerca di Azure

Un indice può essere eliminato chiamando `Indexes.Delete` nella `SearchServiceClient` istanza:

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Caricamento dei dati per l'indice di ricerca di Azure

Dopo aver definito l'indice, i dati possono essere caricati usando uno dei due modelli:

- **Modello pull** : i dati vengono periodicamente inseriti da Azure Cosmos DB, Database SQL di Azure, archiviazione Blob di Azure o SQL Server ospitato in una macchina virtuale di Azure.
- **Modello push** : i dati vengono inviati a livello di codice per l'indice. Si tratta del modello adottato in questo articolo.

Oggetto `SearchIndexClient` istanza deve essere creata per importare dati in corrispondenza dell'indice. Questa operazione può essere eseguita chiamando il `SearchServiceClient.Indexes.GetClient` metodo, come illustrato nell'esempio di codice seguente:

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

I dati da importare in corrispondenza dell'indice vengano compresso come un `IndexBatch` oggetto che incapsula una raccolta di `IndexAction` oggetti. Ogni `IndexAction` istanza contiene un documento e una proprietà che indica l'azione da eseguire sul documento di ricerca di Azure. Nell'esempio di codice precedente, il `IndexAction.Upload` azione viene specificata, in base alla quale il documento viene inserito in corrispondenza dell'indice se è nuovo, o sostituita se esiste già. Il `IndexBatch` oggetto viene quindi inviato all'indice chiamando il `Documents.Index` metodo su di `SearchIndexClient` oggetto. Per informazioni sulle altre azioni di indicizzazione, vedere [decidere quale azione di indicizzazione usare](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use).

> [!NOTE]
> Solo 1000 documenti possono essere inclusi in una singola richiesta di indicizzazione.

Si noti che nell'esempio di codice precedente, il `monkeyList` raccolta viene creata come un'oggetto anonimo da una raccolta di `Monkey` oggetti. Ciò consente di creare i dati per il `id` campo che risolve il mapping di maiuscole minuscole Pascal `Monkey` i nomi dei campi di indice di ricerca di nomi di proprietà in notazione camel. In alternativa, questo mapping può essere eseguito anche aggiungendo il `[SerializePropertyNamesAsCamelCase]` dell'attributo di `Monkey` classe.

Per altre informazioni, vedere [caricare i dati in ricerca di Azure usando .NET SDK](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>L'esecuzione di query dell'indice di ricerca di Azure

Oggetto `SearchIndexClient` istanza deve essere creata per eseguire query su un indice. Quando un'applicazione esegue le query, è consigliabile seguire il principio del privilegio minimo e creare un `SearchIndexClient` direttamente, passando il *chiave di query* come argomento. Ciò garantisce che gli utenti abbiano accesso in lettura a indici e documenti. Questo metodo viene dimostrato nell'esempio di codice seguente:

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

Il `SearchIndexClient` overload del costruttore accetta un nome di servizio di ricerca, il nome dell'indice e una `SearchCredentials` dell'oggetto come argomenti, con la `SearchCredentials` ritorno a capo oggetto il *chiave di query* per il servizio di ricerca di Azure.

### <a name="search-queries"></a>Query di ricerca

L'indice è possibile eseguire query chiamando il `Documents.SearchAsync` metodo sul `SearchIndexClient` istanze, come illustrato nell'esempio di codice seguente:

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

Il `SearchAsync` metodo accetta un argomento di testo di ricerca e facoltativa `SearchParameters` oggetto che può essere utilizzato per specificare ulteriormente la query. Una query di ricerca viene specificata come argomento di testo di ricerca, mentre una query di filtro può essere specificata impostando il `Filter` proprietà del `SearchParameters` argomento. L'esempio di codice seguente illustra che entrambi i tipi di query:

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Questa query di filtro viene applicata all'intero indice e rimuove i documenti nei risultati in cui il `location` campo non è uguale a Cina e non è uguale a Vietnam. Dopo aver filtrato, query di ricerca viene eseguita sui risultati della query di filtro.

> [!NOTE]
> Per filtrare senza eseguire ricerche, passare `*` come argomento di testo di ricerca.

Il `SearchAsync` metodo restituisce un `DocumentSearchResult` oggetto che contiene i risultati della query. Questo oggetto viene enumerato, con ogni `Document` dell'oggetto viene creato come un `Monkey` dell'oggetto e quindi aggiungervi le `Monkeys` `ObservableCollection` per la visualizzazione. I seguente screenshot Mostra query risultati della ricerca restituiti da ricerca di Azure:

![](azure-search-images/search.png "Risultati della ricerca")

Per altre informazioni sulla ricerca e filtro, vedere [eseguire Query nell'indice di ricerca di Azure usando .NET SDK](/azure/search/search-query-dotnet/).

<a name="suggestions" />

### <a name="suggestion-queries"></a>Query di suggerimento

Ricerca di Azure consente di suggerimenti richiesta basato su una query di ricerca, chiamando il `Documents.SuggestAsync` metodo su di `SearchIndexClient` istanza. Ciò è dimostrato nell'esempio di codice seguente:

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

Il `SuggestAsync` metodo accetta un argomento di testo di ricerca, il nome del componente da usare (che è definito nell'indice), ed eventualmente `SuggestParameters` oggetto che può essere utilizzato per specificare ulteriormente la query. Il `SuggestParameters` istanza imposta le proprietà seguenti:

- `UseFuzzyMatching` -Se impostato su `true`, ricerca di Azure trova i suggerimenti anche se è presente un carattere sostituto o manca nel testo di ricerca.
- `HighlightPreTag` : il tag che viene anteposto ai riscontri suggerimento.
- `HighlightPostTag` : il tag che viene aggiunto al suggerimento riscontri.
- `MinimumCoverage` : rappresenta la percentuale di indice che deve essere inclusa da una query di suggerimento per la query viene segnalato un esito positivo. Il valore predefinito è 80.
- `Top` -il numero di suggerimenti da recuperare. Deve essere un numero intero compreso tra 1 e 100, con valore predefinito è 5.

Il risultato complessivo è che i primi 10 risultati tra l'indice verranno restituiti con evidenziazione dei risultati e i risultati includeranno i documenti che includono digitato in modo analogo i termini di ricerca.

Il `SuggestAsync` metodo restituisce un `DocumentSuggestResult` oggetto che contiene i risultati della query. Questo oggetto viene enumerato, con ogni `Document` dell'oggetto viene creato come un `Monkey` dell'oggetto e quindi aggiungervi le `Monkeys` `ObservableCollection` per la visualizzazione. Gli screenshot seguenti mostrano i risultati del suggerimento restituiti da ricerca di Azure:

![](azure-search-images/suggest.png "Risultati di suggerimento")

Si noti che nell'applicazione di esempio, il `SuggestAsync` metodo viene richiamato solo quando l'utente termina l'immissione di un termine di ricerca. Tuttavia, può anche essere utilizzato per supportare le query di ricerca con completamento automatico tramite l'esecuzione in ogni keypress.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare la libreria di ricerca di Microsoft Azure per integrare ricerca di Azure in un'applicazione xamarin. Forms. Ricerca di Azure è un servizio cloud che offre funzionalità per i dati caricati di query e indicizzazione. Questa operazione rimuove i requisiti di infrastruttura e le complessità di algoritmo di ricerca tradizionalmente associate all'implementazione di funzionalità di ricerca in un'applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Ricerca di Azure (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)
- [Documentazione di ricerca di Azure](/azure/search/)
- [Libreria di ricerca di Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search/)
