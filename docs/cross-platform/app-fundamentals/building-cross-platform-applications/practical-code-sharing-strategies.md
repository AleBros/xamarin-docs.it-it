---
title: Parte 5 - pratico strategie di condivisione del codice
ms.prod: xamarin
ms.assetid: 328D042A-FF78-A7B6-1574-B5AF49A1AADB
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 234f6399a163572538755c41e4c58cf0a80e0d3e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="part-5---practical-code-sharing-strategies"></a>Parte 5 - pratico strategie di condivisione del codice

In questa sezione vengono forniti alcuni esempi su come condividere il codice per scenari comuni di applicazione.



## <a name="data-layer"></a>Livello dati

Il livello dati è costituito da un motore di archiviazione e i metodi per leggere e scrivere informazioni. Per garantire la compatibilità delle prestazioni, flessibilità e multipiattaforma di SQLite motore di database è consigliato per applicazioni multipiattaforma con Xamarin.
È in esecuzione su una vasta gamma di piattaforme, tra cui Windows, Android, iOS e Mac.


### <a name="sqlite"></a>SQLite

SQLite è un'implementazione di database open source. L'origine e la documentazione è reperibile in [SQLite.org](http://www.sqlite.org/). Supporto di SQLite è disponibile in ogni piattaforma per dispositivi mobili:

-  **iOS** – incorporato nel sistema operativo.
- **Android** – incorporato nel sistema operativo dall'Android 2.2 (API livello 10).
- **Windows** – vedere la [SQLite per estensione Universal Windows Platform](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936).


Anche il motore di database disponibile in tutte le piattaforme, i metodi nativi per accedere al database sono diversi. Sia iOS e Android offrono incorporate API per accedere a SQLite che può essere usato da xamarin o xamarin, tuttavia utilizzando i metodi nativi di SDK non offre alcuna possibilità di condividere il codice (ad eccezione forse le query SQL, presupponendo che vengono archiviati come stringhe) . Per ulteriori informazioni sulla ricerca di funzionalità native di database per `CoreData` in iOS e dell'Android `SQLiteOpenHelper` classe, perché queste opzioni non sono più piattaforme sono esula dall'ambito di questo documento.



### <a name="adonet"></a>ADO.NET

Supporto di xamarin. IOS e xamarin `System.Data` e `Mono.Data.Sqlite` (vedere il xamarin [documentazione](~/ios/data-cloud/system.data.md) per altre informazioni).
Utilizzo di questi spazi dei nomi consente di scrivere codice ADO.NET che funziona in entrambe le piattaforme. Modificare i riferimenti del progetto per includere `System.Data.dll` e `Mono.Data.Sqlite.dll` e aggiungere queste istruzioni al codice using:

```csharp
using System.Data;
using Mono.Data.Sqlite;
```

È possibile utilizzare il codice di esempio seguente:

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

Ovviamente implementazioni reali di ADO.NET verrebbero suddiviso tra diversi metodi e le classi (in questo esempio è solo per scopi dimostrativi).



### <a name="sqlite-net--cross-platform-orm"></a>SQLite-NET-ORM multipiattaforma

Un ORM (o mapping relazionale a oggetti) tenta di semplificare la memorizzazione dei dati modellati nelle classi. Anziché manualmente la scrittura di query SQL che crea tabelle o selezionare, inserire ed eliminare i dati manualmente estratti dal classe campi e proprietà, ORM aggiunge un livello di codice che esegue automaticamente. Uso della reflection per esaminare la struttura delle classi, ORM potrà creare automaticamente le tabelle e colonne che corrispondono a una classe e generano query per leggere e scrivere i dati. In questo modo il codice dell'applicazione è sufficiente inviare e recuperare istanze di oggetti di ORM, che si occupa di tutte le operazioni SQL dietro le quinte.

SQLite NET agisce come un semplice ORM che consente di salvare e recuperare le classi in SQLite. Nasconde la complessità di cross platform SQLite accesso con una combinazione di direttive del compilatore e altri suggerimenti.

Funzionalità di rete di SQLite:

-  Le tabelle vengono definite mediante l'aggiunta di attributi per le classi del modello.
-  Un'istanza del database è rappresentata da una sottoclasse di `SQLiteConnection` , la classe principale della libreria di rete di SQLite.
-  È possibile inserire dati, eseguire query e di utilizzo di oggetti eliminati. Nessuna istruzione SQL sono necessario (sebbene sia possibile scrivere istruzioni SQL, se necessario).
-  Le query Linq di base possono essere eseguite nelle raccolte restituite da SQLite NET.


Il codice sorgente e la documentazione per SQLite NET è disponibile all'indirizzo [SQLite-Net su github](https://github.com/praeclarum/sqlite-net) ed è stato implementato in entrambi casi. Un semplice esempio di codice SQLite NET (dal *Tasky Pro* case study) è illustrato di seguito.

Prima di tutto, la `TodoItem` classe utilizza gli attributi per definire un campo da una chiave primaria del database:

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

In questo modo un `TodoItem` tabella da creare con la seguente riga di codice (e nessuna istruzione SQL) in un `SQLiteConnection` istanza:

```csharp
CreateTable<TodoItem> ();
```

Dati della tabella possono anche essere modificati con altri metodi nel `SQLiteConnection` (senza richiedere istruzioni SQL):

```csharp
Insert (TodoItem); // 'task' is an instance with data populated in its properties
Update (TodoItem); // Primary Key field must be populated for Update to work
Table<TodoItem>.ToList(); // returns all rows in a collection
```

Vedere il codice sorgente case study per esempi completi.



## <a name="file-access"></a>Accesso ai file

Accesso al file è determinato da una parte fondamentale di qualsiasi applicazione. Esempi comuni di file che potrebbero far parte di un'inclusione dell'applicazione:

-  File di database SQLite.
-  Generato dall'utente dei dati (testo, immagini, audio, video).
-  Dati scaricati per la memorizzazione nella cache (immagini, html o PDF).




### <a name="systemio-direct-access"></a>Accesso diretto di System.IO

Xamarin. IOS e xamarin consentire accesso al file system utilizzando le classi di `System.IO` dello spazio dei nomi.

Ogni piattaforma presenta le restrizioni di accesso diversi che devono essere presi in considerazione:

-  le applicazioni iOS eseguite in una sandbox con accesso molto limitato di file system. Apple ulteriormente determina come utilizzare il file system specificando determinati percorsi di backup (e altri utenti che non sono). Consultare il [funziona con il File System in xamarin. IOS](~/ios/app-fundamentals/file-system.md) Guida per ulteriori dettagli.
-  Android limita inoltre l'accesso a determinate directory correlate all'applicazione, ma supporta anche un supporto esterno (ad es. Schede SD) e l'accesso ai dati condivisi.
-  Windows Phone 8 (Silverlight) non consentono l'accesso diretto al file: file possono essere modificati solo tramite `IsolatedStorage`.
-  Progetti WinRT Windows 8.1 e Windows 10 UWP offrono solo operazioni asincrone sui file tramite `Windows.Storage` API, che sono diverse da altre piattaforme.

#### <a name="example-for-ios-and-android"></a>Esempio per iOS e Android

Seguito è riportato un esempio semplice che scrive e legge un file di testo.
Utilizzando `Environment.GetFolderPath` consente di eseguire in iOS e Android, ognuno dei quali restituiscono una directory valida in base alle convenzioni loro filesystem il codice stesso.

```csharp
string filePath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "MyFile.txt");
System.IO.File.WriteAllText (filePath, "Contents of text file");
Console.WriteLine (System.IO.ReadAllText (filePath));
```

Fare riferimento a di xamarin. IOS [funziona con il File System](~/ios/app-fundamentals/file-system.md) per ulteriori informazioni sulle funzionalità specifiche di iOS filesystem. Quando si scrive codice di accesso ai file multipiattaforma, ricordare che alcuni sistemi di file tra maiuscole e minuscole e presenti separatori di directory diversi. È consigliabile utilizzare sempre le stesse maiuscole e minuscole per nomi di file e `Path.Combine()` metodo durante la costruzione di percorsi di file o directory.



### <a name="windowsstorage-for-windows-8-and-windows-10"></a>Windows. Storage per Windows 8 e Windows 10

Il *la creazione di App per dispositivi mobili con xamarin. Forms* [book](https://developer.xamarin.com/r/xamarin-forms/book/)
[capitolo 20. Async e i/o File](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) include [esempi per Windows 8.1 e Windows 10](https://github.com/xamarin/xamarin-forms-book-preview-2/tree/master/Chapter20).

Utilizzando un [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) è possibile leggere e il file file su queste piattaforme utilizzando le API supportate:

```csharp
StorageFolder localFolder = ApplicationData.Current.LocalFolder;
IStorageFile storageFile = await localFolder.CreateFileAsync("MyFile.txt",
                                        CreationCollisionOption.ReplaceExisting);
await FileIO.WriteTextAsync(storageFile, "Contents of text file");
```

Consultare la [capitoli del libro rappresentati](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) per altri dettagli.


<a name="Isolated_Storage" />

### <a name="isolated-storage-on-windows-phone-7--8-silverlight"></a>Spazio di memorizzazione isolato in Windows Phone 7 e 8 (Silverlight)

Spazio di memorizzazione isolato è un'API comune per il salvataggio e caricamento di file tra tutti iOS, Android e piattaforme precedenti di Windows Phone.

È il meccanismo predefinito per l'accesso ai file di Windows Phone (Silverlight) che è stato implementato in xamarin. IOS e xamarin per consentire al codice di accesso ai file comuni da scrivere. Il `System.IO.IsolatedStorage` classe è possibile fare riferimento in tutte e tre le piattaforme in un [progetto condiviso](~/cross-platform/app-fundamentals/shared-projects.md).

Consultare la [isolato archiviazione Panoramica per Windows Phone](http://msdn.microsoft.com/library/windowsphone/develop/ff402541(v=vs.105).aspx) per ulteriori informazioni.

Le API di archiviazione isolata non sono disponibili in [librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md). È un'alternativa per la libreria di classi Portabile il [PCLStorage NuGet](https://pclstorage.codeplex.com/)



### <a name="cross-platform-file-access-in-pcls"></a>L'accesso ai file multipiattaforma PCLs

È inoltre disponibile un Nuget PCL compatibile: [PCLStorage](https://www.nuget.org/packages/PCLStorage/) – tale accesso al file multipiattaforma strutture per le piattaforme supportate Xamarin e le API di Windows più recente.


## <a name="network-operations"></a>Operazioni di rete

Applicazioni per più dispositivi mobili avranno il componente di rete, ad esempio:

-  Download di immagini, video e audio (ad es. anteprime, foto, musica).
-  Scaricamento di documenti (ad es. HTML, PDF).
-  Caricamento dei dati utente (ad esempio foto o testo).
-  L'accesso a servizi web o parti 3rd API (tra cui SOAP, XML o JSON).


.NET Framework fornisce alcune classi diverse per l'accesso alle risorse di rete: `HttpClient`, `WebClient`, e `HttpWebRequest`.

### <a name="httpclient"></a>HttpClient

Il `HttpClient` classe il `System.Net.Http` dello spazio dei nomi è disponibile in xamarin. IOS, xamarin e la maggior parte delle piattaforme di Windows. È presente un [Nuget della libreria Microsoft HTTP Client](https://www.nuget.org/packages/Microsoft.Net.Http/) che può essere utilizzato per visualizzare questa API nelle librerie di classi portabili (e Windows Phone 8 Silverlight).

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get, "https://xamarin.com");
var response = await myClient.SendAsync(request);
```

### <a name="webclient"></a>WebClient

La `WebClient` classe fornisce una semplice API per recuperare i dati remoti da server remoti.

Operazioni Windows Platofrm universale *deve* essere asincrono, anche se xamarin. IOS e xamarin supporta operazioni sincrone (che possono essere eseguite sul thread in background).

Il codice per un semplice asincrona `WebClient` operazione:

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

 `WebClient` dispone anche di `DownloadFileCompleted` e `DownloadFileAsync` per recuperare dati binari.

<a name="HttpWebRequest" />

### <a name="httpwebrequest"></a>HttpWebRequest

`HttpWebRequest` offre una maggiore personalizzazione di `WebClient` e pertanto richiede più codice da utilizzare.

Il codice per una semplice sincrono `HttpWebRequest` operazione:

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

Un esempio in nostro [documentazione di servizi Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="Reachability" />


### <a name="reachability"></a>Accessibilità

I dispositivi mobili operano con una varietà di condizioni di rete da veloce Wi-Fi o connessioni di 4 GB per le aree di ricezione debole e rallentamenti dei collegamenti dati EDGE. Per questo motivo, è buona norma per rilevare se la rete è disponibile e se in tal caso, il tipo di rete è disponibile, prima di tentare di connettersi a server remoti.

Un'app per dispositivi mobili potrebbero essere necessari in queste situazioni azioni includono:

-  Se la rete non è disponibile, informare l'utente. È stato disabilitato manualmente dall'utente (ad es. Modalità aereo o la disattivazione Wi-Fi), quindi consentono di risolvere il problema.
-  Se la connessione è 3G, le applicazioni possono comportarsi in modo diverso (ad esempio, Apple non consente le app superiori a 20Mb per essere scaricato in 3G). Applicazioni è possibile utilizzare queste informazioni per avvertire l'utente download eccessivo volte durante il recupero dei file di grandi dimensioni.
-  Anche se la rete è disponibile, è consigliabile verificare la connettività con il server di destinazione prima dell'avvio di altre richieste. Per impedire operazioni di rete dell'app timeout ripetutamente e anche un messaggio di errore più descrittivo da visualizzare all'utente.


È presente un [esempio xamarin](https://github.com/xamarin/monotouch-samples/tree/master/ReachabilitySample) disponibile (che si basa su Apple [codice di esempio raggiungibilità](http://developer.apple.com/library/ios/#samplecode/Reachability/Introduction/Intro.html) ) per rilevare la disponibilità di rete.


## <a name="webservices"></a>Servizi Web

Vedere la documentazione su [utilizzo dei servizi Web](~/cross-platform/data-cloud/web-services/index.md), riguardano l'accesso a REST, gli endpoint SOAP e WCF usando xamarin. IOS. È possibile per le richieste di servizio web per creare e analizzare le risposte, tuttavia, sono presenti raccolte disponibili per rendere molto più semplice, tra cui Azure, RestSharp e ServiceStack. Operazioni di base anche WCF sono accessibili in App Xamarin.

### <a name="azure"></a>Azure

Microsoft Azure è una piattaforma cloud che fornisce un'ampia gamma di servizi di App per dispositivi mobili, incluse le notifiche push e sincronizzazione e archiviazione dei dati.

Visitare [azure.microsoft.com](https://azure.microsoft.com/) prova gratuitamente.

### <a name="restsharp"></a>RestSharp

RestSharp è una libreria .NET che può essere inclusi in applicazioni per dispositivi mobili per fornire un client REST che semplifica l'accesso ai servizi web. È utile, fornendo un'API semplice per i dati della richiesta e analizzare la risposta REST. RestSharp può essere utile

Il [sito Web RestSharp](http://restsharp.org/) contiene [documentazione](https://github.com/restsharp/RestSharp/wiki) su come implementare un client REST mediante RestSharp.
RestSharp vengono forniti esempi di xamarin. IOS e xamarin su [github](https://github.com/restsharp/RestSharp/).

È inoltre disponibile un frammento di codice xamarin nel nostro [documentazione di servizi Web](~/cross-platform/data-cloud/web-services/index.md).

 <a name="ServiceStack" />


### <a name="servicestack"></a>ServiceStack

A differenza di RestSharp, ServiceStack è sia una soluzione sul lato server per ospitare un servizio web, nonché una libreria client che può essere implementata in applicazioni per dispositivi mobili per accedere a tali servizi.

Il [sito Web ServiceStack](http://servicestack.net/) illustra lo scopo del progetto e i collegamenti a documenti ed esempi di codice. Gli esempi includono un'implementazione sul lato server completa di un servizio web, nonché diverse applicazioni sul lato client che possono accedervi.

È presente un [esempio xamarin](http://www.servicestack.net/monotouch/remote-info/) il sito Web ServiceStack e un frammento di codice in nostro [documentazione di servizi Web](~/cross-platform/data-cloud/web-services/index.md).


### <a name="wcf"></a>WCF

Strumenti di Xamarin consente di utilizzare alcuni servizi Windows Communication Foundation (WCF). In generale, Xamarin supporta lo stesso subset di sul lato client di WCF che viene fornito con il runtime Silverlight. Ciò include le implementazioni più comuni di codifica e protocollo di WCF: testo con codifica di messaggi SOAP su HTTP di trasporto mediante protocollo il `BasicHttpBinding`.

A causa della dimensione e complessità del framework WCF, potrebbero essere presenti le implementazioni del servizio correnti e futuri che rientrano all'esterno dell'ambito è supportato dal dominio client subset di Xamarin. Inoltre, il supporto WCF richiede l'uso di strumenti disponibili solo in un ambiente di Windows per generare il proxy.

 <a name="Threading" />


## <a name="threading"></a>Threading

Velocità di risposta dell'applicazione è importante per le applicazioni per dispositivi mobili, gli utenti si aspettano di caricare ed eseguire rapidamente le applicazioni. Schermata 'bloccata' che verrà visualizzato viene arrestata l'accettazione dell'input dell'utente per indicare che l'applicazione si è arrestato, pertanto è importante evitare di bloccare il thread dell'interfaccia utente con le chiamate di blocco a esecuzione prolungata, ad esempio le richieste di rete o lente operazioni locale (ad esempio, decomprimere un file). In particolare, il processo di avvio non deve contenere attività a esecuzione prolungata, tutte le piattaforme per dispositivi mobili verranno terminare un'app che richiede troppo tempo per caricare.

Ciò significa che deve implementare l'interfaccia utente, un indicatore di stato' ' o dell'interfaccia utente in caso contrario utilizzabile rapido visualizzare e attività asincrone per eseguire operazioni in background. L'esecuzione di attività in background richiede l'uso di thread, ovvero le esigenze di attività in background consentono di comunicare al thread principale per indicare lo stato di avanzamento o quando è stata completata.

 <a name="Parallel_Task_Library" />


### <a name="parallel-task-library"></a>Task Parallel Library

Attività create con Task Parallel Library è possibile eseguire in modo asincrono e restituire il thread chiamante, rendendo molto utile per l'attivazione di operazioni di lunga durata senza bloccare l'interfaccia utente.

Un'operazione di attività parallele semplice potrebbe essere simile al seguente:

```csharp
using System.Threading.Tasks;
void MainThreadMethod ()
{
    Task.Factory.StartNew (() => wc.DownloadString ("http://...")).ContinueWith (
        t => label.Text = t.Result, TaskScheduler.FromCurrentSynchronizationContext()
    );
}
```

La chiave è `TaskScheduler.FromCurrentSynchronizationContext()` che riutilizzerà SynchronizationContext. Current del thread che chiama il metodo (il thread principale è in esecuzione qui `MainThreadMethod`) come un modo per il marshalling delle chiamate indietro per tale thread. Ciò significa che se il metodo viene chiamato sul thread dell'interfaccia utente, verrà eseguito il `ContinueWith` operazione nuovamente sul thread dell'interfaccia utente.

Se il codice di avvio delle attività da altri thread, usare il modello seguente per creare un riferimento al thread dell'interfaccia utente e l'attività è possibile comunque richiamare è:

```csharp
static Context uiContext = TaskScheduler.FromCurrentSynchronizationContext();
```

 <a name="Invoking_on_the_UI_Thread" />


### <a name="invoking-on-the-ui-thread"></a>La chiamata sul Thread dell'interfaccia utente

Per il codice che non utilizza la libreria di attività parallele, ogni piattaforma presenta una sintassi specifica per le operazioni di marshalling al thread dell'interfaccia utente:

-  **iOS** : `owner.BeginInvokeOnMainThread(new NSAction(action))`
-  **Android** : `owner.RunOnUiThread(action)`
-  **Xamarin. Forms** : `Device.BeginInvokeOnMainThread(action)`
-  **Windows** : `Deployment.Current.Dispatcher.BeginInvoke(action)`



La iOS e Android sintassi richiede una classe 'context' sia disponibile, pertanto è necessario passare questo oggetto in qualsiasi metodo che richiede un callback nel thread UI.

Per effettuare chiamate thread dell'interfaccia utente in codice condiviso, seguire la [IDispatchOnUIThread esempio](http://www.slideshare.net/follesoe/cross-platform-mobile-apps-using-net) (fornito da [ @follesoe ](http://jonas.follesoe.no/)). Dichiarare e programma per un `IDispatchOnUIThread` interfaccia nel codice condiviso e quindi implementare le classi specifiche della piattaforma, come illustrato di seguito:

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

Gli sviluppatori di xamarin. Forms devono usare [ `Device.BeginInvokeOnMainThread` ](~/xamarin-forms/platform/device.md#Device_BeginInvokeOnMainThread) nel codice comune (progetti condivisi o PCL).

 <a name="Platform_and_Device_Capabilities_and_Degradation" />


## <a name="platform-and-device-capabilities-and-degradation"></a>Piattaforma e delle funzionalità di dispositivo e riduzione

Ulteriori esempi specifici di gestione delle diverse funzionalità sono forniti nella documentazione di funzionalità della piattaforma. Deve gestire il rilevamento di diverse funzionalità e su come ridurre gradualmente il funzionamento un'applicazione di fornire un'esperienza utente soddisfacente, anche quando l'app non può funzionare per appieno il potenziale.
