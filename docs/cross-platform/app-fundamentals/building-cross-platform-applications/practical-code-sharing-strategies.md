---
title: Parte 5 - Strategie pratiche di condivisione del codice
description: Questo documento vengono illustrate le strategie per scenari quali database, l'accesso ai file, le operazioni di rete e codice asincrono di condivisione del codice pratico.
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 0e37e138607fb0e00fbdc463ac7c53facf81395d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291931"
---
# <a name="part-5---practical-code-sharing-strategies"></a>Parte 5 - Strategie pratiche di condivisione del codice

Questa sezione fornisce esempi su come condividere il codice per scenari applicativi comuni.

## <a name="data-layer"></a>Livello dati

Il livello dati è costituito da un motore di archiviazione e i metodi per leggere e scrivere informazioni. Per garantire la compatibilità delle prestazioni, flessibilità e lo sviluppo multipiattaforma di SQLite motore di database è consigliabile per le applicazioni multipiattaforma con Xamarin.
Viene eseguito in un'ampia gamma di piattaforme, tra cui Windows, Android, iOS e Mac.

### <a name="sqlite"></a>SQLite

SQLite è un'implementazione di database open source. L'origine e la documentazione si trovano in [SQLite.org](https://www.sqlite.org/). Il supporto SQLite è disponibile in ogni piattaforma mobile:

- **iOS** : integrato nel sistema operativo.
- **Android** : integrato nel sistema operativo da Android 2,2 (livello API 10).
- **Windows** : vedere [SQLite per piattaforma UWP (Universal Windows Platform) estensione](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).

Anche con il motore di database disponibile in tutte le piattaforme, i metodi nativi per accedere al database sono diversi. Entrambi iOS e Android offre alle API incorporate per accedere a SQLite che può essere usato da xamarin. IOS o xamarin. Android, tuttavia tramite i metodi SDK nativi non offre alcuna possibilità di condividere il codice (ad eccezione forse la query SQL, presupponendo che vengono archiviati come stringhe) . Per informazioni dettagliate sulle funzionalità di database native, cercare `CoreData` nella classe `SQLiteOpenHelper` di iOS o Android; Poiché queste opzioni non sono multipiattaforma, esulano dall'ambito di questo documento.

### <a name="adonet"></a>ADO.NET

Sia Novell. iOS che Novell. Android supportano `System.Data` e `Mono.Data.Sqlite` (per altre informazioni, vedere la [documentazione](~/ios/data-cloud/system.data.md) di Novell. iOS).
Utilizzando questi spazi dei nomi è possibile scrivere codice ADO.NET che funziona in entrambe le piattaforme. Modificare i riferimenti del progetto in modo da includere `System.Data.dll` e `Mono.Data.Sqlite.dll` e aggiungere le istruzioni using seguenti al codice:

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

Esempio di codice seguente è possibile usare:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "items.db3");
bool exists = File.Exists (dbPath);
if (!exists)
    SqliteConnection.CreateFile (dbPath);
var connection = new SqliteConnection ("Data Source=" + dbPath);
connection.Open ();
if (!exists) {
    // This is the first time the app has run and/or that we need the DB.
    // Copy a "template" DB from your assets, or programmatically create one like this:
    var commands = new[]{
        "CREATE TABLE [Items] (Key ntext, Value ntext);",
        "INSERT INTO [Items] ([Key], [Value]) VALUES ('sample', 'text')"
    };
    foreach (var command in commands) {
        using (var c = connection.CreateCommand ()) {
            c.CommandText = command;
            c.ExecuteNonQuery ();
        }
    }
}
// use `connection`... here, we'll just append the contents to a TextView
using (var contents = connection.CreateCommand ()) {
    contents.CommandText = "SELECT [Key], [Value] from [Items]";
    var r = contents.ExecuteReader ();
    while (r.Read ())
        Console.Write("\n\tKey={0}; Value={1}",
                r ["Key"].ToString (),
                r ["Value"].ToString ());
}
connection.Close ();
```

Implementazioni reali di ADO.NET sarebbero ovviamente essere suddivise tra diversi metodi e classi (in questo esempio è solo a scopo dimostrativo).

### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET-ORM multipiattaforma

Un ORM (o Object-Relational Mapper) tenta di semplificare l'archiviazione dei dati modellati nelle classi. Anziché manualmente la scrittura di query SQL che crea tabelle o SELECT, INSERT e DELETE data manualmente estratto dalla classe campi e proprietà, un ORM aggiunge un livello di codice che esegue automaticamente. Uso della reflection per esaminare la struttura delle classi, un ORM può creare automaticamente le tabelle e colonne che corrispondono a una classe e generano query per leggere e scrivere i dati. In questo modo il codice dell'applicazione è sufficiente inviare e recuperare istanze di oggetti di ORM, che si occupa di tutte le operazioni SQL dietro le quinte.

SQLite-NET agisce come un semplice ORM che consentirà di salvare e recuperare le classi in SQLite. Nasconde la complessità di cross-platform SQLite accesso con una combinazione di direttive del compilatore e altri consigli.

Funzionalità di SQLite-NET:

- Le tabelle vengono definite aggiungendo attributi alle classi di modello.
- Un'istanza di database è rappresentata da una sottoclasse di `SQLiteConnection`, la classe principale nella libreria SQLite-NET.
- È possibile inserire i dati, sottoposti a query ed eliminate tramite gli oggetti. Nessun istruzioni SQL sono necessari (sebbene sia possibile scrivere istruzioni SQL, se necessario).
- Le query Linq di base possono essere eseguite nelle raccolte restituite da SQLite-NET.

Il codice sorgente e la documentazione per SQLite-NET sono disponibili in [sqlite-net su GitHub](https://github.com/praeclarum/sqlite-net) ed è stata implementata in entrambi i casi di studio. Di seguito è riportato un esempio semplice di codice SQLite-NET (del case study *pro attività* ).

Per prima cosa, la classe `TodoItem` usa gli attributi per definire un campo come chiave primaria del database:

```csharp
public class TodoItem : IBusinessEntity
{
    public TodoItem () {}
    [PrimaryKey, AutoIncrement]
    public int ID { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
}
```

In questo modo è possibile creare una tabella `TodoItem` con la seguente riga di codice (e nessuna istruzione SQL) in un'istanza `SQLiteConnection`:

```csharp
CreateTable<TodoItem> ();
```

I dati nella tabella possono essere modificati anche con altri metodi nel `SQLiteConnection` (anche in questo caso, senza richiedere istruzioni SQL):

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

Vedere il codice sorgente di case study per esempi completi.

## <a name="file-access"></a>Accesso ai file

Accesso ai file è determinato da una parte fondamentale di qualsiasi applicazione. Esempi comuni di file che potrebbero far parte di un'applicazione includono:

- File di database SQLite.
- Dati generati dall'utente (testo, immagini, audio, video).
- Dati scaricati per la memorizzazione nella cache (immagini, html o i file PDF).

### <a name="systemio-direct-access"></a>Accesso diretto System.IO

Sia Novell. iOS che Novell. Android consentono l'accesso file system usando le classi nello spazio dei nomi `System.IO`.

Ogni piattaforma presentano restrizioni di accesso diversi che devono essere prese in considerazione:

- le applicazioni iOS vengono eseguite in una sandbox con accesso molto limitato di file system. Apple ulteriormente determina come si deve usare il file system specificando determinati percorsi che vengono sottoposti a backup (e altri che non sono). Per altri dettagli, vedere la Guida [uso del file System in Novell. iOS](~/ios/app-fundamentals/file-system.md) .
- Android limita inoltre l'accesso a determinate directory relativa all'applicazione, ma supporta anche un supporto esterno (ad es. Le schede SD) e l'accesso ai dati condivisi.
- Windows Phone 8 (Silverlight) non consente l'accesso diretto ai file: i file possono essere modificati solo usando `IsolatedStorage`.
- Windows 8.1 WinRT e i progetti UWP di Windows 10 offrono solo operazioni asincrone sui file tramite `Windows.Storage` API, che sono diverse dalle altre piattaforme.

#### <a name="example-for-ios-and-android"></a>Esempio per iOS e Android

Di seguito è riportato un esempio semplice che scrive e legge un file di testo.
L'uso di `Environment.GetFolderPath` consente di eseguire lo stesso codice in iOS e Android, ognuno dei quali restituisce una directory valida basata sulle convenzioni del file System.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.File.ReadAllText (filePath));
```

Per altre informazioni sulle funzionalità del [file System](~/ios/app-fundamentals/file-system.md) specifiche di iOS, vedere il documento relativo all'uso di Novell. iOS. Durante la scrittura di codice di accesso ai file cross-platform, tenere presente che alcuni file-System sono tra maiuscole e minuscole e presenti separatori di directory diversa. È consigliabile usare sempre la stessa combinazione di maiuscole e minuscole per i nomi di file e il metodo `Path.Combine()` durante la costruzione di percorsi di file o directory.

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows. Storage per Windows 8 e Windows 10

Il [libro](https://developer.xamarin.com/r/xamarin-forms/book/) *creazione di app per dispositivi mobili con Novell. Forms*
[capitolo 20. Async e I/O di file](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) includono [esempi per Windows 8.1 e Windows 10](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20).

L'uso di un [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) è possibile leggere e file su queste piattaforme usando le API supportate:

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

Per altri dettagli, vedere il [capitolo del libro](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) .

<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Spazio di memorizzazione isolato in Windows Phone 7 e 8 (Silverlight)

Spazio di memorizzazione isolato è un'API comune per il salvataggio e caricamento dei file in tutti i iOS, Android e meno recenti piattaforme Windows Phone.

È il meccanismo predefinito per l'accesso ai file di Windows Phone (Silverlight) che è stato implementato in xamarin. IOS e xamarin. Android per consentire al codice di accesso ai file comuni da scrivere. È possibile fare riferimento alla classe `System.IO.IsolatedStorage` in tutte e tre le piattaforme di un [progetto condiviso](~/cross-platform/app-fundamentals/shared-projects.md).

Per ulteriori informazioni, fare riferimento alla [Panoramica dello spazio di memorizzazione isolato per Windows Phone](https://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx) .

Le API dello spazio di memorizzazione isolato non sono disponibili nelle [librerie di classi](~/cross-platform/app-fundamentals/pcl.md)portabili. Un'alternativa per PCL è [PCLStorage NuGet](https://pclstorage.codeplex.com/)

### <a name="cross-platform-file-access-in-pcls"></a>Accesso ai file tra piattaforme con librerie di classi portabili

È anche disponibile un NuGet- [PCLStorage](https://www.nuget.org/packages/PCLStorage/) , compatibile con PCL, che facilita l'accesso ai file multipiattaforma per le piattaforme supportate da Novell e le API Windows più recenti.

## <a name="network-operations"></a>Operazioni per la rete

Applicazioni per dispositivi mobili più avranno il componente di rete, ad esempio:

- Download di immagini, video e audio (ad es. le anteprime, fotografie, brani musicali).
- Download dei documenti (ad es. HTML, PDF).
- Caricamento dei dati utente (ad esempio foto o testo).
- L'accesso a servizi web o parti 3rd API (tra cui SOAP, XML o JSON).

Il .NET Framework fornisce alcune classi diverse per accedere alle risorse di rete: `HttpClient`, `WebClient`e `HttpWebRequest`.

### <a name="httpclient"></a>HttpClient

La classe `HttpClient` nello spazio dei nomi `System.Net.Http` è disponibile in Novell. iOS, Novell. Android e la maggior parte delle piattaforme Windows. È disponibile una [libreria client HTTP Microsoft NuGet](https://www.nuget.org/packages/Microsoft.Net.Http/) che può essere usata per portare questa API in librerie di classi portabili (e Windows Phone 8 Silverlight).

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

La classe `WebClient` fornisce un'API semplice per recuperare i dati remoti dai server remoti.

Piattaforma UWP (Universal Windows Platform) operazioni *devono* essere asincrone, anche se Novell. iOS e Novell. Android supportano le operazioni sincrone, che possono essere eseguite su thread in background.

Il codice per una semplice operazione di `WebClient` asincrono è:

```csharp
var webClient = new WebClient ();
webClient.DownloadStringCompleted += (sender, e) =>
{
    var resultString = e.Result;
    // do something with downloaded string, do UI interaction on main thread
};
webClient.Encoding = System.Text.Encoding.UTF8;
webClient.DownloadStringAsync (new Uri ("http://some-server.com/file.xml"));
```

 `WebClient` dispone anche di `DownloadFileCompleted` e `DownloadFileAsync` per il recupero di dati binari.

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>httpWebRequest

`HttpWebRequest` offre una maggiore personalizzazione rispetto a `WebClient` e, di conseguenza, richiede un maggior numero di codice da usare.

Il codice per una semplice operazione sincrona `HttpWebRequest` è:

```csharp
var request = HttpWebRequest.Create(@"http://some-server.com/file.xml ");
request.ContentType = "text/xml";
request.Method = "GET";
using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
    if (response.StatusCode != HttpStatusCode.OK)
        Console.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
    using (StreamReader reader = new StreamReader(response.GetResponseStream()))
    {
        var content = reader.ReadToEnd();
        // do something with downloaded string, do UI interaction on main thread
    }
}
```

Un esempio è disponibile nella [documentazione relativa ai servizi Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="Reachability" />

### <a name="reachability"></a>Raggiungibilità

I dispositivi mobili funzionano sotto una varietà di condizioni di rete da veloce Wi-Fi o connessioni 4g ad aree di accoglienza scarse e collegamenti dati EDGE lenti. Per questo motivo, è buona norma rilevare se la rete sia disponibile e se, pertanto, il tipo di rete è disponibile, prima di tentare di connettersi ai server remoti.

Le azioni di che un'app per dispositivi mobili potrebbero essere necessari in queste situazioni includono:

- Se la rete non è disponibile, richiedere all'utente. Se è stato disabilitato manualmente dall'utente (ad es. Modalità aereo o la disattivazione di Wi-Fi), quindi è possibile risolvere il problema.
- Se la connessione è 3G, le applicazioni possono comportarsi in modo diverso (ad esempio, Apple non consente le app maggiori di 20Mb per essere scaricato oltre 3G). Le applicazioni è stato possibile usare queste informazioni per avvertire l'utente download eccessivo volte durante il recupero dei file di grandi dimensioni.
- Anche se la rete è disponibile, è buona norma per verificare la connettività con il server di destinazione prima dell'avvio di altre richieste. Questo impedirà ripetutamente operazioni di rete dell'app dal timeout e consentono inoltre di un messaggio di errore più informativo da visualizzare all'utente.

## <a name="webservices"></a>WebServices

Vedere la documentazione sull' [uso dei servizi Web](~/cross-platform/data-cloud/web-services/index.md), che illustra l'accesso agli endpoint REST, SOAP e WCF con Novell. iOS. È possibile per le richieste di servizio web per creare e analizzare le risposte, tuttavia sono disponibili librerie disponibili per rendere molto più semplice, tra cui Azure RestSharp e ServiceStack. Operazioni di base anche WCF sono accessibili nelle App Xamarin.

### <a name="azure"></a>Azure

Microsoft Azure è una piattaforma cloud che offre un'ampia gamma di servizi per le App per dispositivi mobili, incluse le notifiche push e sincronizzazione e archiviazione di dati.

Visitare [Azure.Microsoft.com](https://azure.microsoft.com/) per provarlo gratuitamente.

### <a name="restsharp"></a>RestSharp

RestSharp è una libreria .NET che può essere incluso nelle applicazioni per dispositivi mobili per fornire un client REST che semplifica l'accesso ai servizi web. È utile, fornendo un'API semplice per richiedere i dati e analizzare la risposta REST. Può essere utile RestSharp

Il [sito Web RestSharp](http://restsharp.org/) contiene la [documentazione](https://github.com/restsharp/RestSharp/wiki) su come implementare un client REST usando RestSharp.
RestSharp fornisce esempi di Novell. iOS e Novell. Android su [GitHub](https://github.com/restsharp/RestSharp/).

Nella [documentazione dei servizi Web](~/cross-platform/data-cloud/web-services/index.md)è presente anche un frammento di codice Novell. iOS.

 <a name="ServiceStack" />

### <a name="servicestack"></a>ServiceStack

A differenza di RestSharp, ServiceStack è sia una soluzione lato server per ospitare un servizio web, nonché una libreria client che può essere implementata in applicazioni per dispositivi mobili per accedere a tali servizi.

Il [sito Web ServiceStack](http://servicestack.net/) illustra lo scopo del progetto e i collegamenti a documenti ed esempi di codice. Gli esempi includono un'implementazione sul lato server completa di un servizio web, nonché varie applicazioni lato client che possono accedervi.

### <a name="wcf"></a>WCF

Gli strumenti Xamarin consente di utilizzare alcuni servizi Windows Communication Foundation (WCF). In generale, Xamarin supporta lo stesso subset di lato client di WCF, che viene fornito con il runtime di Silverlight. Sono incluse le implementazioni più comuni di codifica e protocollo di WCF: messaggi SOAP con codifica testo sul protocollo di trasporto HTTP utilizzando il `BasicHttpBinding`.

A causa della dimensione e complessità del framework WCF, potrebbero essere presenti le implementazioni del servizio correnti e futuri che rientrano di fuori dell'ambito è supportata dal dominio di subset di client di Xamarin. Inoltre, il supporto WCF richiede l'uso degli strumenti disponibili solo in un ambiente di Windows per generare il proxy.

 <a name="Threading" />

## <a name="threading"></a>Threading

La velocità di risposta dell'applicazione è importante per le applicazioni per dispositivi mobili, gli utenti si aspettano alle applicazioni di caricare ed eseguire più rapidamente. Una schermata 'bloccata' in cui viene arrestata l'accettazione dell'input dell'utente verrà visualizzato per indicare il che arresto anomalo dell'applicazione, pertanto è importante non bloccare il thread dell'interfaccia utente con le chiamate di blocco a esecuzione prolungata, ad esempio le richieste di rete o lente operazioni locali (ad esempio, decomprimere un file). In particolare il processo di avvio non deve contenere attività a esecuzione prolungata, termina un'app che impiega troppo tempo per caricare tutte le piattaforme per dispositivi mobili.

Ciò significa che l'interfaccia utente deve implementare un indicatore di stato' ' o un'interfaccia utente in caso contrario, utilizzabile che è un'operazione rapida da visualizzare e le attività asincrone per eseguire operazioni in background. L'esecuzione di attività in background richiede l'uso di thread, ovvero le esigenze di attività in background un modo per comunicare con il thread principale per indicare lo stato di avanzamento o quando vengono completati.

 <a name="Parallel_Task_Library" />

### <a name="parallel-task-library"></a>Task Parallel Library

Le attività create con la libreria Task Parallel Library possono eseguire in modo asincrono e restituire il thread chiamante, che lo rendono particolarmente utile per attivare le operazioni di lunga durata senza bloccare l'interfaccia utente.

Un'operazione parallela di attività semplice potrebbe essere simile al seguente:

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

La chiave è `TaskScheduler.FromCurrentSynchronizationContext()` che riutilizzerà SynchronizationContext. Current del thread che chiama il metodo (in questo caso il thread principale che esegue `MainThreadMethod`) come metodo per eseguire il marshalling delle chiamate a tale thread. Ciò significa che se il metodo viene chiamato sul thread dell'interfaccia utente, l'operazione di `ContinueWith` verrà eseguita nuovamente sul thread UI.

Se il codice di avvio attività rispetto agli altri thread, usare il modello seguente per creare un riferimento al thread UI e l'attività può richiamare comunque a esso:

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />

### <a name="invoking-on-the-ui-thread"></a>Chiamata sul Thread UI

Per il codice che non usa la libreria Task Parallel Library, ogni piattaforma ha una propria sintassi per le operazioni di marshalling nel thread dell'interfaccia utente:

- **iOS** -`owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** -`owner.RunOnUiThread(action)`
- **Novell. Forms** : `Device.BeginInvokeOnMainThread(action)`
- **Windows** -`Deployment.Current.Dispatcher.BeginInvoke(action)`

Sia la sintassi di Android e iOS richiede una classe 'context' sia disponibile, ovvero il codice deve passare questo oggetto in qualsiasi metodo che richiede un callback sul thread UI.

Per eseguire chiamate al thread dell'interfaccia utente nel codice condiviso, seguire l' [esempio IDispatchOnUIThread](https://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) (cortesia di [@follesoe](https://twitter.com/follesoe)). Dichiarare e programmare in un'interfaccia `IDispatchOnUIThread` nel codice condiviso e quindi implementare le classi specifiche della piattaforma, come illustrato di seguito:

```csharp
// program to the interface in shared code
public interface IDispatchOnUIThread {
    void Invoke (Action action);
}
// iOS
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly NSObject owner;
    public DispatchAdapter (NSObject owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.BeginInvokeOnMainThread(new NSAction(action));
    }
}
// Android
public class DispatchAdapter : IDispatchOnUIThread {
    public readonly Activity owner;
    public DispatchAdapter (Activity owner) {
        this.owner = owner;
    }
    public void Invoke (Action action) {
        owner.RunOnUiThread(action);
    }
}
// WP7
public class DispatchAdapter : IDispatchOnUIThread {
    public void Invoke (Action action) {
        Deployment.Current.Dispatcher.BeginInvoke(action);
    }
}
```

Gli sviluppatori Novell. Forms devono usare [`Device.BeginInvokeOnMainThread`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads) nel codice comune (progetti condivisi o PCL).

 <a name="Platform_and_Device_Capabilities_and_Degradation" />

## <a name="platform-and-device-capabilities-and-degradation"></a>Piattaforma e funzionalità del dispositivo e riduzione delle prestazioni

Inoltre vengono forniti esempi specifici di gestione con diverse funzionalità nella documentazione di funzionalità della piattaforma. Gestisce il rilevamento di diverse funzionalità e come in modo che un'applicazione può offrire un'esperienza utente ottimale, anche quando l'app non può funzionare al massimo del loro potenziale.
