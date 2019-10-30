---
title: Parte 5 - Strategie pratiche di condivisione del codice
description: Questo documento illustra le strategie pratiche di condivisione del codice per scenari quali database, accesso ai file, operazioni di rete e codice asincrono.
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: fdc9fd6eac8c7b0c9ec91eb66b5d6723cda71006
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016832"
---
# <a name="part-5---practical-code-sharing-strategies"></a>Parte 5 - Strategie pratiche di condivisione del codice

In questa sezione vengono forniti esempi di come condividere il codice per gli scenari di applicazione comuni.

## <a name="data-layer"></a>Livello dati

Il livello dati è costituito da un motore di archiviazione e dai metodi per leggere e scrivere le informazioni. Per garantire prestazioni, flessibilità e compatibilità multipiattaforma, è consigliabile usare il motore di database SQLite per le applicazioni multipiattaforma Novell.
Viene eseguito in un'ampia gamma di piattaforme, tra cui Windows, Android, iOS e Mac.

### <a name="sqlite"></a>SQLite

SQLite è un'implementazione di database open source. L'origine e la documentazione si trovano in [SQLite.org](https://www.sqlite.org/). Il supporto SQLite è disponibile in ogni piattaforma mobile:

- **iOS** : integrato nel sistema operativo.
- **Android** : integrato nel sistema operativo da Android 2,2 (livello API 10).
- **Windows** : vedere [SQLite per piattaforma UWP (Universal Windows Platform) estensione](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).

Anche con il motore di database disponibile su tutte le piattaforme, i metodi nativi per accedere al database sono diversi. IOS e Android offrono API incorporate per accedere a SQLite che può essere usato da Novell. iOS o Novell. Android. Tuttavia, l'uso dei metodi SDK nativi non offre la possibilità di condividere codice (ad eccezione delle query SQL, presumendo che siano archiviate come stringhe) . Per informazioni dettagliate sulle funzionalità di database native, cercare `CoreData` nella classe `SQLiteOpenHelper` di iOS o Android; Poiché queste opzioni non sono multipiattaforma, esulano dall'ambito di questo documento.

### <a name="adonet"></a>ADO.NET

Sia Novell. iOS che Novell. Android supportano `System.Data` e `Mono.Data.Sqlite` (per altre informazioni, vedere la [documentazione](~/ios/data-cloud/system.data.md) di Novell. iOS).
L'uso di questi spazi dei nomi consente di scrivere codice ADO.NET che funziona su entrambe le piattaforme. Modificare i riferimenti del progetto in modo da includere `System.Data.dll` e `Mono.Data.Sqlite.dll` e aggiungere le istruzioni using seguenti al codice:

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

Il codice di esempio seguente funzionerà:

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

Le implementazioni reali di ADO.NET verrebbero ovviamente suddivise in metodi e classi differenti (questo esempio è solo a scopo dimostrativo).

### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET-multi-piattaforma ORM

Un ORM (o un mapper relazionale a oggetti) tenta di semplificare l'archiviazione dei dati modellati nelle classi. Anziché scrivere manualmente query SQL che creano tabelle o SELEZIONAno, inseriscono ed ELIMINAno dati estratti manualmente da proprietà e campi della classe, un ORM aggiunge un livello di codice che esegue questa operazione. Utilizzando la reflection per esaminare la struttura delle classi, un ORM può creare automaticamente tabelle e colonne che corrispondono a una classe e generano query per la lettura e la scrittura dei dati. Questo consente al codice dell'applicazione di inviare e recuperare semplicemente le istanze di oggetti all'ORM, che si occupa di tutte le operazioni SQL dietro le quinte.

SQLite-NET funge da ORM semplice che consente di salvare e recuperare le classi in SQLite. Nasconde la complessità dell'accesso SQLite multipiattaforma con una combinazione di direttive del compilatore e altri trucchi.

Funzionalità di SQLite-NET:

- Le tabelle vengono definite aggiungendo attributi alle classi del modello.
- Un'istanza di database è rappresentata da una sottoclasse di `SQLiteConnection`, la classe principale nella libreria SQLite-NET.
- I dati possono essere inseriti, sottoposti a query ed eliminati mediante oggetti. Non sono necessarie istruzioni SQL (sebbene sia possibile scrivere istruzioni SQL, se necessario).
- È possibile eseguire query LINQ di base sulle raccolte restituite da SQLite-NET.

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

Per esempi completi, vedere il codice sorgente case study.

## <a name="file-access"></a>Accesso ai file

L'accesso ai file è determinato come parte essenziale di qualsiasi applicazione. Esempi comuni di file che possono far parte di un'applicazione includono:

- File di database SQLite.
- Dati generati dall'utente (testo, immagini, audio, video).
- Dati scaricati per la memorizzazione nella cache (immagini, file HTML o PDF).

### <a name="systemio-direct-access"></a>Accesso diretto a System.IO

Sia Novell. iOS che Novell. Android consentono l'accesso file system usando le classi nello spazio dei nomi `System.IO`.

Ogni piattaforma presenta restrizioni di accesso diverse che devono essere prese in considerazione:

- le applicazioni iOS vengono eseguite in una sandbox con accesso al file System molto limitato. Apple stabilisce ulteriormente come usare il file system specificando determinati percorsi di cui viene eseguito il backup (e altri non). Per altri dettagli, vedere la Guida [uso del file System in Novell. iOS](~/ios/app-fundamentals/file-system.md) .
- Android limita inoltre l'accesso a determinate directory correlate all'applicazione, ma supporta anche supporti esterni (ad esempio, Schede SD) e accesso ai dati condivisi.
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
Console.WriteLine (System.IO.ReadAllText (filePath));
```

Per altre informazioni sulle funzionalità del [file System](~/ios/app-fundamentals/file-system.md) specifiche di iOS, vedere il documento relativo all'uso di Novell. iOS. Quando si scrive il codice di accesso ai file multipiattaforma, tenere presente che alcuni file System distinguono tra maiuscole e minuscole e hanno separatori di directory diversi. È consigliabile usare sempre la stessa combinazione di maiuscole e minuscole per i nomi di file e il metodo `Path.Combine()` durante la costruzione di percorsi di file o directory.

### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows. storage per Windows 8 e Windows 10

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

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Spazio di memorizzazione isolato in Windows Phone 7 & 8 (Silverlight)

Lo spazio di memorizzazione isolato è un'API comune per il salvataggio e il caricamento di file in tutte le piattaforme iOS, Android e Windows Phone precedenti.

Si tratta del meccanismo predefinito per l'accesso ai file in Windows Phone (Silverlight) implementato in Novell. iOS e Novell. Android per consentire la scrittura di codice di accesso ai file comune. È possibile fare riferimento alla classe `System.IO.IsolatedStorage` in tutte e tre le piattaforme di un [progetto condiviso](~/cross-platform/app-fundamentals/shared-projects.md).

Per ulteriori informazioni, fare riferimento alla [Panoramica dello spazio di memorizzazione isolato per Windows Phone](https://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx) .

Le API dello spazio di memorizzazione isolato non sono disponibili nelle [librerie di classi](~/cross-platform/app-fundamentals/pcl.md)portabili. Un'alternativa per PCL è [PCLStorage NuGet](https://pclstorage.codeplex.com/)

### <a name="cross-platform-file-access-in-pcls"></a>Accesso a file multipiattaforma in classi portabili

È anche disponibile un NuGet- [PCLStorage](https://www.nuget.org/packages/PCLStorage/) , compatibile con PCL, che facilita l'accesso ai file multipiattaforma per le piattaforme supportate da Novell e le API Windows più recenti.

## <a name="network-operations"></a>Operazioni di rete

La maggior parte delle applicazioni per dispositivi mobili avrà un componente di rete, ad esempio:

- Download di immagini, video e audio (ad esempio anteprime, foto, musica).
- Download di documenti (ad esempio HTML, PDF).
- Caricamento dei dati utente (ad esempio foto o testo).
- Accesso ai servizi Web o alle API di terze parti (incluso SOAP, XML o JSON).

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

### <a name="httpwebrequest"></a>HttpWebRequest

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

I dispositivi mobili operano in una serie di condizioni di rete da connessioni Wi-Fi o 4G veloci a aree di ricezione non soddisfacenti e collegamenti dati di EDGE lenti. Per questo motivo, è consigliabile rilevare se la rete è disponibile e, in tal caso, il tipo di rete disponibile, prima di tentare la connessione ai server remoti.

Le azioni che un'app per dispositivi mobili può prendere in queste situazioni includono:

- Se la rete non è disponibile, avvisare l'utente. Se è stato disabilitato manualmente, ad esempio Per risolvere il problema, è possibile disattivare la modalità aereo o spegnere il Wi-Fi.
- Se la connessione è 3G, le applicazioni possono comportarsi in modo diverso (ad esempio, Apple non consente il download di app di dimensioni superiori a 20Mb su 3G). Le applicazioni possono utilizzare queste informazioni per avvisare l'utente di tempi di download eccessivi durante il recupero di file di grandi dimensioni.
- Anche se la rete è disponibile, è consigliabile verificare la connettività con il server di destinazione prima di avviare altre richieste. In questo modo si eviterà il timeout delle operazioni di rete dell'app e si consentirà anche di visualizzare un messaggio di errore più informativo per l'utente.

È disponibile un [esempio di Novell. iOS](https://github.com/xamarin/monotouch-samples/tree/master/ReachabilitySample) (basato sul codice di esempio di [raggiungibilità](https://developer.apple.com/library/ios/#samplecode/Reachability/Introduction/Intro.html) di Apple) che consente di rilevare la disponibilità della rete.

## <a name="webservices"></a>WebServices

Vedere la documentazione sull' [uso dei servizi Web](~/cross-platform/data-cloud/web-services/index.md), che illustra l'accesso agli endpoint REST, SOAP e WCF con Novell. iOS. È possibile creare richieste di servizio Web e analizzare le risposte, ma sono disponibili librerie che rendono questa operazione molto più semplice, tra cui Azure, RestSharp e ServiceStack. È anche possibile accedere alle operazioni WCF di base nelle app Novell.

### <a name="azure"></a>Azure

Microsoft Azure è una piattaforma cloud che offre un'ampia gamma di servizi per le app per dispositivi mobili, tra cui l'archiviazione e la sincronizzazione dei dati e le notifiche push.

Visitare [Azure.Microsoft.com](https://azure.microsoft.com/) per provarlo gratuitamente.

### <a name="restsharp"></a>RestSharp

RestSharp è una libreria .NET che può essere inclusa nelle applicazioni per dispositivi mobili per fornire un client REST che semplifica l'accesso ai servizi Web. Consente di fornire un'API semplice per richiedere i dati e analizzare la risposta REST. RestSharp può essere utile

Il [sito Web RestSharp](http://restsharp.org/) contiene la [documentazione](https://github.com/restsharp/RestSharp/wiki) su come implementare un client REST usando RestSharp.
RestSharp fornisce esempi di Novell. iOS e Novell. Android su [GitHub](https://github.com/restsharp/RestSharp/).

Nella [documentazione dei servizi Web](~/cross-platform/data-cloud/web-services/index.md)è presente anche un frammento di codice Novell. iOS.

 <a name="ServiceStack" />

### <a name="servicestack"></a>ServiceStack

A differenza di RestSharp, ServiceStack è una soluzione lato server per ospitare un servizio Web, oltre a una libreria client che può essere implementata nelle applicazioni per dispositivi mobili per accedere a tali servizi.

Il [sito Web ServiceStack](http://servicestack.net/) illustra lo scopo del progetto e i collegamenti a documenti ed esempi di codice. Gli esempi includono un'implementazione completa sul lato server di un servizio Web, oltre a diverse applicazioni lato client che possono accedervi.

È disponibile un [esempio di Novell. iOS](http://www.servicestack.net/monotouch/remote-info/) nel sito Web ServiceStack e un frammento di codice nella [documentazione dei servizi Web](~/cross-platform/data-cloud/web-services/index.md).

### <a name="wcf"></a>WCF

Gli strumenti di Novell consentono di utilizzare alcuni servizi di Windows Communication Foundation (WCF). In generale, Novell supporta lo stesso subset lato client di WCF fornito con il runtime di Silverlight. Sono incluse le implementazioni più comuni di codifica e protocollo di WCF: messaggi SOAP con codifica testo sul protocollo di trasporto HTTP utilizzando il `BasicHttpBinding`.

A causa delle dimensioni e della complessità del framework WCF, potrebbero essere presenti implementazioni del servizio correnti e future che esulano dall'ambito supportato dal dominio del sottoinsieme client di Novell. Inoltre, il supporto WCF richiede l'utilizzo di strumenti disponibili solo in un ambiente Windows per generare il proxy.

 <a name="Threading" />

## <a name="threading"></a>Threading

La velocità di risposta dell'applicazione è importante per le applicazioni per dispositivi mobili: gli utenti si aspettano che le applicazioni vengano caricate e Una schermata ' bloccata ' che interrompe l'accettazione dell'input utente apparirà per indicare che l'applicazione si è arrestata in modo anomalo, quindi è importante non bloccare il thread dell'interfaccia utente con chiamate di blocco con esecuzione prolungata, ad esempio richieste di rete o operazioni locali lente (ad esempio la decompressione di un file). In particolare, il processo di avvio non deve contenere attività a esecuzione prolungata. tutte le piattaforme mobili uccidono un'app che impiega troppo tempo per il caricamento.

Ciò significa che l'interfaccia utente deve implementare un indicatore di stato o un'interfaccia utente "utilizzabile" che è rapida da visualizzare e attività asincrone per eseguire operazioni in background. Per eseguire attività in background è necessario usare i thread, il che significa che le attività in background necessitano di un modo per comunicare al thread principale per indicare lo stato di avanzamento o dopo il completamento.

 <a name="Parallel_Task_Library" />

### <a name="parallel-task-library"></a>Libreria di attività in parallelo

Le attività create con la libreria di attività in parallelo possono essere eseguite in modo asincrono e restituire sul thread chiamante, rendendole molto utili per attivare operazioni a esecuzione prolungata senza bloccare l'interfaccia utente.

Una semplice operazione parallela dell'attività potrebbe essere simile alla seguente:

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

Se il codice avvia attività da altri thread, usare il modello seguente per creare un riferimento al thread dell'interfaccia utente e l'attività può comunque richiamarla:

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />

### <a name="invoking-on-the-ui-thread"></a>Richiamo nel thread UI

Per il codice che non utilizza la libreria di attività in parallelo, ogni piattaforma dispone di una propria sintassi per eseguire il marshalling delle operazioni nel thread dell'interfaccia utente:

- **iOS** -`owner.BeginInvokeOnMainThread(new NSAction(action))`
- **Android** -`owner.RunOnUiThread(action)`
- **Novell. Forms** : `Device.BeginInvokeOnMainThread(action)`
- **Windows** -`Deployment.Current.Dispatcher.BeginInvoke(action)`

Per la sintassi iOS e Android è necessaria la disponibilità di una classe ' context ', che significa che il codice deve passare questo oggetto in tutti i metodi che richiedono un callback sul thread UI.

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

## <a name="platform-and-device-capabilities-and-degradation"></a>Funzionalità e riduzione della piattaforma e dei dispositivi

Ulteriori esempi specifici di gestione delle diverse funzionalità sono forniti nella documentazione sulle funzionalità della piattaforma. Si tratta del rilevamento di diverse funzionalità e di come ridurre normalmente un'applicazione per offrire un'esperienza utente ottimale, anche quando l'app non può funzionare con il suo pieno potenziale.
