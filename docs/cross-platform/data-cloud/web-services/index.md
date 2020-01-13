---
title: Introduzione ai servizi Web
description: In questa guida viene illustrato come utilizzare tecnologie di servizi Web diverse. Gli argomenti trattati includono la comunicazione con i servizi REST, i servizi SOAP e i servizi Windows Communication Foundation.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 50302b0b9cf96d211c704ab9e68d1c61d11e807a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016580"
---
# <a name="introduction-to-web-services"></a>Introduzione ai servizi Web

_In questa guida viene illustrato come utilizzare tecnologie di servizi Web diverse. Gli argomenti trattati includono la comunicazione con i servizi REST, i servizi SOAP e i servizi Windows Communication Foundation._

Per funzionare correttamente, molte applicazioni per dispositivi mobili dipendono dal cloud e pertanto l'integrazione dei servizi Web nelle applicazioni per dispositivi mobili è uno scenario comune. La piattaforma Novell supporta l'utilizzo di diverse tecnologie di servizi Web e include supporto integrato e di terze parti per l'utilizzo di servizi RESTful, ASMX e Windows Communication Foundation (WCF).

Per i clienti che usano Xamarin.Forms, sono disponibili esempi completi che usano ciascuna di queste tecnologie nella documentazione relativa ai [servizi Web Xamarin.Forms](~/xamarin-forms/data-cloud/index.yml) .

> [!IMPORTANT]
> In iOS 9, la sicurezza del trasporto app (ATS) applica connessioni sicure tra le risorse Internet (ad esempio il server back-end dell'app) e l'app, impedendo in tal modo la divulgazione accidentale di informazioni riservate.
> Poiché ATS è abilitato per impostazione predefinita nelle app compilate per iOS 9, tutte le connessioni saranno soggette ai requisiti di sicurezza di ATS. Se le connessioni non soddisfano questi requisiti, avranno esito negativo con un'eccezione.

È possibile rifiutare esplicitamente ATS se non è possibile usare il protocollo `HTTPS` e proteggere le comunicazioni per le risorse Internet. Questa operazione può essere eseguita aggiornando il file **info. plist** dell'app. Per altre informazioni, vedere [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md).

## <a name="rest"></a>REST

REST (Representational State Transfer) è uno stile di architettura per la creazione di servizi Web. Le richieste REST vengono effettuate tramite HTTP usando gli stessi verbi HTTP usati dai Web browser per recuperare le pagine Web e inviare dati ai server. I verbi sono:

- **Get** : questa operazione viene usata per recuperare i dati dal servizio Web.
- **Post** : questa operazione viene usata per creare un nuovo elemento di dati nel servizio Web.
- **Put** : questa operazione viene usata per aggiornare un elemento di dati nel servizio Web.
- **Patch** : questa operazione viene usata per aggiornare un elemento di dati nel servizio Web descrivendo una serie di istruzioni su come modificare l'elemento. Questo verbo non viene utilizzato nell'applicazione di esempio.
- **Delete** : questa operazione viene usata per eliminare un elemento di dati nel servizio Web.

Le API del servizio Web che rispettano REST sono denominate API RESTful e vengono definite usando:

- URI di base.
- Metodi HTTP, ad esempio GET, POST, PUT, PATCH o DELETE.
- Tipo di supporto per i dati, ad esempio JavaScript Object Notation (JSON).

La semplicità di REST ha aiutato a renderlo il metodo principale per accedere ai servizi Web nelle applicazioni per dispositivi mobili.

## <a name="consuming-rest-services"></a>Utilizzo dei servizi REST

Sono disponibili numerose librerie e classi che possono essere utilizzate per utilizzare i servizi REST e le sottosezioni seguenti. Per ulteriori informazioni sull'utilizzo di un servizio REST, vedere [utilizzo di un servizio Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md).

### <a name="httpclient"></a>HttpClient

Le [librerie client HTTP Microsoft](https://www.nuget.org/packages/Microsoft.Net.Http) forniscono la classe `HttpClient`, che viene usata per inviare e ricevere richieste tramite http. Fornisce funzionalità per l'invio di richieste HTTP e la ricezione di risposte HTTP da una risorsa identificata dall'URI. Ogni richiesta viene inviata come operazione asincrona. Per ulteriori informazioni sulle operazioni asincrone, vedere [Cenni preliminari sul supporto](~/cross-platform/platform/async.md)asincrono.

La classe `HttpResponseMessage` rappresenta un messaggio di risposta HTTP ricevuto dal servizio Web dopo che è stata effettuata una richiesta HTTP. Contiene informazioni sulla risposta, tra cui il codice di stato, le intestazioni e il corpo. La classe `HttpContent` rappresenta le intestazioni di contenuto e del corpo HTTP, ad esempio `Content-Type` e `Content-Encoding`. Il contenuto può essere letto usando uno dei metodi di `ReadAs`, ad esempio `ReadAsStringAsync` e `ReadAsByteArrayAsync`, in base al formato dei dati.

Per ulteriori informazioni sulla classe `HttpClient`, vedere [creazione dell'oggetto HTTPClient](~/xamarin-forms/data-cloud/web-services/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

La chiamata di servizi Web con `HTTPWebRequest` comporta:

- Creazione dell'istanza della richiesta per un URI specifico.
- Impostazione di varie proprietà HTTP nell'istanza della richiesta.
- Recupero di un `HttpWebResponse` dalla richiesta.
- Lettura dei dati dalla risposta.

Il codice seguente, ad esempio, recupera i dati dalla libreria nazionale statunitense del servizio Web di medicine:

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"https://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
request.ContentType = "application/json";
request.Method = "GET";

using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
  if (response.StatusCode != HttpStatusCode.OK)
     Console.Out.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
  using (StreamReader reader = new StreamReader(response.GetResponseStream()))
  {
               var content = reader.ReadToEnd();
               if(string.IsNullOrWhiteSpace(content)) {
                       Console.Out.WriteLine("Response contained empty body...");
               }
               else {
                       Console.Out.WriteLine("Response Body: \r\n {0}", content);
               }

               Assert.NotNull(content);
  }
}
```

Nell'esempio precedente viene creato un `HttpWebRequest` che restituirà i dati formattati come JSON. I dati vengono restituiti in un `HttpWebResponse`, da cui è possibile ottenere un `StreamReader` per leggere i dati.

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

Un altro approccio all'utilizzo dei servizi REST consiste nell'utilizzare la libreria [RestSharp](http://restsharp.org/) . RestSharp incapsula le richieste HTTP, incluso il supporto per il recupero dei risultati come contenuto della stringa non elaborata o come C# oggetto deserializzato. Il codice seguente, ad esempio, effettua una richiesta alla libreria nazionale degli Stati Uniti del servizio Web di medicine e recupera i risultati come stringa in formato JSON:

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` è un metodo che prenderà la stringa JSON non elaborata dalla proprietà `RestSharp.RestResponse.Content` e la convertirà C# in un oggetto. La deserializzazione dei dati restituiti dai servizi Web viene discussa più avanti in questo articolo.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

Oltre alle classi disponibili nella libreria di classi base (BCL) mono, ad esempio `HttpWebRequest`e librerie di terze C# parti, ad esempio RestSharp, sono disponibili anche classi specifiche della piattaforma per l'utilizzo di servizi Web. In iOS, ad esempio, è possibile usare le classi `NSUrlConnection` e `NSMutableUrlRequest`.

Nell'esempio di codice seguente viene illustrato come chiamare la libreria nazionale statunitense del servizio Web di medicine usando le classi iOS:

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("https://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
       NSUrlRequestCachePolicy.ReloadRevalidatingCacheData, 20);
request["Accept"] = "application/json";

var connectionDelegate = new RxTermNSURLConnectionDelegate();
var connection = new NSUrlConnection(request, connectionDelegate);
connection.Start();

public class RxTermNSURLConnectionDelegate : NSUrlConnectionDelegate
{
       StringBuilder _ResponseBuilder;
       public bool IsFinishedLoading { get; set; }
       public string ResponseContent { get; set; }

       public RxTermNSURLConnectionDelegate()
               : base()
       {
               _ResponseBuilder = new StringBuilder();
       }

       public override void ReceivedData(NSUrlConnection connection, NSData data)
       {
               if(data != null) {
                       _ResponseBuilder.Append(data.ToString());
               }
       }
       public override void FinishedLoading(NSUrlConnection connection)
       {
               IsFinishedLoading = true;
               ResponseContent = _ResponseBuilder.ToString();
       }
}
```

In genere, le classi specifiche della piattaforma per l'utilizzo di servizi Web devono essere limitate agli scenari in cui viene eseguito il C#trasferimento del codice nativo. Laddove possibile, il codice di accesso al servizio Web deve essere portabile in modo da poter essere condiviso tra più piattaforme.

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

Un'altra opzione per chiamare i servizi Web è la libreria [dello stack dei servizi](https://www.servicestack.net/) . Ad esempio, il codice seguente illustra come usare il metodo `IServiceClient.GetAsync` dello stack di servizi per emettere una richiesta di servizio:

```csharp
client.GetAsync<CustomersResponse>("",
          (response) => {
               foreach(var c in response.Customers) {
                       Console.WriteLine(c.CompanyName);
               }
       },
       (response, ex) => {
               Console.WriteLine(ex.Message);
       });
```

> [!IMPORTANT]
> Sebbene strumenti come ServiceStack e RestSharp semplificano la chiamata e l'utilizzo di servizi REST, a volte non è semplice utilizzare XML o JSON che non è conforme alle convenzioni di serializzazione _DataContract_ standard. Se necessario, richiamare la richiesta e gestire in modo esplicito la serializzazione appropriata usando la libreria ServiceStack. Text descritta di seguito.

<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>Utilizzo di dati RESTful

I servizi Web RESTful usano in genere i messaggi JSON per restituire i dati al client. JSON è un formato di interscambio dei dati basato su testo che genera payload Compact, con conseguente riduzione dei requisiti di larghezza di banda quando si inviano dati. In questa sezione vengono esaminati i meccanismi per l'utilizzo delle risposte RESTful in JSON e di POX (Plain-Old-XML).

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System. JSON

La piattaforma Novell viene fornita con il supporto per JSON predefinito. Utilizzando una `JsonObject`, è possibile recuperare i risultati come illustrato nell'esempio di codice seguente:

```csharp
var obj = JsonObject.Parse(json);
var properties = obj["rxtermsProperties"];
term.BrandName = properties["brandName"];
term.DisplayName = properties["displayName"];
term.Synonym = properties["synonym"];
term.FullName = properties["fullName"];
term.FullGenericName = properties["fullGenericName"];
term.Strength = properties["strength"];
```

È tuttavia importante tenere presente che gli strumenti di `System.Json` caricano interamente i dati in memoria.

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

La [libreria JSON.NET di NewtonSoft](https://www.newtonsoft.com/json) è una libreria ampiamente utilizzata per la serializzazione e la deserializzazione dei messaggi JSON. L'esempio di codice seguente illustra come usare JSON.NET per deserializzare un messaggio JSON in un C# oggetto:

```csharp
var term = new RxTerm();
var properties = JObject.Parse(json)["rxtermsProperties"];
term.BrandName = properties["brandName"].Value<string>();
term.DisplayName = properties["displayName"].Value<string>();
term.Synonym = properties["synonym"].Value<string>();;
term.FullName = properties["fullName"].Value<string>();;
term.FullGenericName = properties["fullGenericName"].Value<string>();;
term.Strength = properties["strength"].Value<string>();
term.RxCUI = properties["rxcui"].Value<string>();
```

<a name="Using_ServiceStack.Text" />

### <a name="servicestacktext"></a>ServiceStack. Text

ServiceStack. Text è una libreria di serializzazione JSON progettata per funzionare con la libreria ServiceStack. Nell'esempio di codice seguente viene illustrato come analizzare JSON utilizzando un `ServiceStack.Text.JsonObject`:

```csharp
var result = JsonObject.Parse(json).Object("rxtermsProperties")
       .ConvertTo(x => new RxTerm {
               BrandName = x.Get("brandName"),
               DisplayName = x.Get("displayName"),
               Synonym = x.Get("synonym"),
               FullName = x.Get("fullName"),
               FullGenericName = x.Get("fullGenericName"),
               Strength = x.Get("strength"),
               RxTermDoseForm = x.Get("rxtermsDoseForm"),
               Route = x.Get("route"),
               RxCUI = x.Get("rxcui"),
               RxNormDoseForm = x.Get("rxnormDoseForm"),
       });
```

<a name="Using_System.Xml.Linq" />

### <a name="systemxmllinq"></a>System.Xml.Linq

In caso di utilizzo di un servizio Web REST basato su XML, LINQ to XML possibile utilizzare per analizzare il codice XML e popolare un C# oggetto inline, come illustrato nell'esempio di codice seguente:

```csharp
var doc = XDocument.Parse(xml);
var result = doc.Root.Descendants("rxtermsProperties")
.Select(x=> new RxTerm()
       {
               BrandName = x.Element("brandName").Value,
               DisplayName = x.Element("displayName").Value,
               Synonym = x.Element("synonym").Value,
               FullName = x.Element("fullName").Value,
               FullGenericName = x.Element("fullGenericName").Value,
               //bind more here...
               RxCUI = x.Element("rxcui").Value,
       });
```

<a name="asmx" />

## <a name="aspnet-web-service-asmx"></a>Servizio Web ASP.NET (ASMX)

ASMX consente di compilare servizi Web che inviano messaggi utilizzando il Simple Object Access Protocol (SOAP). SOAP è un protocollo indipendente dalla piattaforma e indipendente dalla lingua per la creazione e l'accesso ai servizi Web. I consumer di un servizio ASMX non devono conoscere alcuna piattaforma, modello a oggetti o linguaggio di programmazione utilizzato per implementare il servizio. È sufficiente comprendere come inviare e ricevere messaggi SOAP.

Un messaggio SOAP è un documento XML contenente gli elementi seguenti:

- Elemento radice denominato *Envelope* che identifica il documento XML come messaggio SOAP.
- Elemento *intestazione* facoltativo che contiene informazioni specifiche dell'applicazione, ad esempio i dati di autenticazione. Se l'elemento dell' *intestazione* è presente, deve essere il primo elemento figlio dell'elemento *Envelope* .
- Elemento del *corpo* obbligatorio che contiene il messaggio SOAP destinato al destinatario.
- Elemento *fault* facoltativo utilizzato per indicare i messaggi di errore. Se l'elemento *fault* è presente, deve essere un elemento figlio dell'elemento *Body* .

SOAP può utilizzare molti protocolli di trasporto, tra cui HTTP, SMTP, TCP e UDP. Tuttavia, un servizio ASMX può funzionare solo tramite HTTP. La piattaforma Novell supporta le implementazioni standard di SOAP 1,1 su HTTP e include il supporto per molte delle configurazioni del servizio ASMX standard.

### <a name="generating-a-proxy"></a>Generazione di un proxy

È necessario generare un *proxy* per utilizzare un servizio ASMX, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito mediante l'utilizzo di metadati del servizio che definiscono i metodi e la configurazione del servizio associata. Questi metadati vengono esposti come documento di Web Services Description Language (WSDL) generato dal servizio Web. Il proxy viene compilato usando Visual Studio per Mac o Visual Studio per aggiungere un riferimento Web per il servizio Web ai progetti specifici della piattaforma.

L'URL del servizio Web può essere un'origine remota ospitata o una risorsa file system locale accessibile tramite il prefisso del percorso `file:///`, ad esempio:

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "The web service URL can either be a hosted remote source or local file system resource accessible via the file path prefix")](images/add-webreference-dialog.png#lightbox)

Viene generato il proxy nella cartella riferimenti Web o del servizio del progetto. Poiché il codice generato da un proxy non deve essere modificato.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Aggiunta manuale di un proxy a un progetto

Se si dispone di un proxy esistente che è stato generato con strumenti compatibili, questo output può essere utilizzato quando è incluso nel progetto. In Visual Studio per Mac usare l' **aggiunta di file..** . opzione di menu per aggiungere il proxy. Inoltre, è necessario fare riferimento in modo esplicito a *System. Web. Services. dll* utilizzando l' **aggiunta di riferimenti...** dialogo.

### <a name="consuming-the-proxy"></a>Utilizzo del proxy

Le classi proxy generate forniscono metodi per l'utilizzo del servizio Web che utilizza il modello di progettazione APM (Asynchronous Programming Model). In questo modello un'operazione asincrona viene implementata come due metodi denominati *BeginOperationName* e *EndOperationName*, che iniziano e terminano l'operazione asincrona.

Il metodo *BeginOperationName* avvia l'operazione asincrona e restituisce un oggetto che implementa l'interfaccia `IAsyncResult`. Dopo la chiamata a *BeginOperationName*, un'applicazione può continuare a eseguire le istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un thread del pool di thread.

Per ogni chiamata a *BeginOperationName*, l'applicazione deve anche chiamare *EndOperationName* per ottenere i risultati dell'operazione. Il valore restituito di *EndOperationName* è dello stesso tipo restituito dal metodo di servizio Web sincrono. Il codice seguente ne è un esempio:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

Il Task Parallel Library (TPL) può semplificare il processo di utilizzo di una coppia di metodi Begin/End APM incapsulando le operazioni asincrone nello stesso oggetto `Task`. Questo incapsulamento viene fornito da più overload del metodo `Task.Factory.FromAsync`. Questo metodo crea un `Task` che esegue il metodo `TodoService.EndGetTodoItems` una volta completato il metodo `TodoService.BeginGetTodoItems`, con il parametro `null` che indica che non è stato passato alcun dato al delegato `BeginGetTodoItems`. Infine, il valore dell'enumerazione `TaskCreationOptions` specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione delle attività.

Per ulteriori informazioni su APM, vedere il [modello di programmazione asincrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e la tradizionale .NET Framework la programmazione asincrona](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

Per ulteriori informazioni sull'utilizzo di un servizio ASMX, vedere la pagina relativa all'utilizzo di [un servizio Web ASP.NET (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

WCF è il Framework unificato di Microsoft per la creazione di applicazioni orientate ai servizi. Consente agli sviluppatori di creare applicazioni distribuite sicure, affidabili, transazionali e interoperabili.

WCF descrive un servizio con un'ampia gamma di contratti diversi, tra cui:

- **Contratti dati** : definiscono le strutture di dati che costituiscono la base per il contenuto in un messaggio.
- **Contratti di messaggio** : comporre messaggi da contratti dati esistenti.
- **Contratti di errore** : consente di specificare errori SOAP personalizzati.
- **Contratti di servizio** : specificare le operazioni supportate dai servizi e i messaggi necessari per interagire con ogni operazione. Specificano anche eventuali comportamenti di errore personalizzati che possono essere associati a operazioni su ogni servizio.

Esistono differenze tra i servizi Web ASP.NET (ASMX) e WCF, ma è importante comprendere che WCF supporta le stesse funzionalità fornite da ASMX, ovvero messaggi SOAP su HTTP.

> [!IMPORTANT]
> Il supporto della piattaforma Novell per WCF è limitato ai messaggi SOAP con codifica testo tramite HTTP/HTTPS usando la classe `BasicHttpBinding`. Inoltre, il supporto WCF richiede l'utilizzo di strumenti disponibili solo in un ambiente Windows per generare il proxy.

### <a name="generating-a-proxy"></a>Generazione di un proxy

È necessario generare un *proxy* per utilizzare un servizio WCF, che consente all'applicazione di connettersi al servizio. Il proxy viene costruito mediante l'utilizzo di metadati del servizio che definiscono i metodi e la configurazione del servizio associata. Questi metadati vengono esposti sotto forma di documento di Web Services Description Language (WSDL) generato dal servizio Web. Il proxy può essere compilato usando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 per aggiungere un riferimento al servizio per il servizio Web a una libreria di .NET Standard.

Un'alternativa alla creazione del proxy utilizzando il Microsoft WCF Web Service Reference Provider in Visual Studio 2017 consiste nell'utilizzare lo strumento ServiceModel Metadata Utility Tool (Svcutil. exe). Per ulteriori informazioni, vedere [ServiceModel Metadata Utility Tool (Svcutil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>Configurazione del proxy

La configurazione del proxy generato in genere accetta due argomenti di configurazione (a seconda di SOAP 1.1/ASMX o WCF) durante l'inizializzazione: il `EndpointAddress` e/o le informazioni di binding associate, come illustrato nell'esempio seguente:

```csharp
var binding = new BasicHttpBinding () {
       Name= "basicHttpBinding",
       MaxReceivedMessageSize = 67108864,
};

binding.ReaderQuotas = new System.Xml.XmlDictionaryReaderQuotas() {
       MaxArrayLength = 2147483646,
       MaxStringContentLength = 5242880,
};

var timeout = new TimeSpan(0,1,0);
binding.SendTimeout= timeout;
binding.OpenTimeout = timeout;
binding.ReceiveTimeout = timeout;

client = new Service1Client (binding, new EndpointAddress ("http://192.168.1.100/Service1.svc"));
```

Un'associazione viene utilizzata per specificare i dettagli di trasporto, codifica e protocollo necessari per la comunicazione tra le applicazioni e i servizi. Il `BasicHttpBinding` specifica che i messaggi SOAP con codifica testo verranno inviati tramite il protocollo di trasporto HTTP. La specifica di un indirizzo endpoint consente all'applicazione di connettersi a istanze diverse del servizio WCF, purché siano presenti più istanze pubblicate.

### <a name="consuming-the-proxy"></a>Utilizzo del proxy

Le classi proxy generate forniscono metodi per l'utilizzo dei servizi Web che utilizzano il modello di progettazione APM (Asynchronous Programming Model). In questo modello, un'operazione asincrona viene implementata come due metodi denominati *BeginOperationName* e *EndOperationName*, che iniziano e terminano l'operazione asincrona.

Il metodo *BeginOperationName* avvia l'operazione asincrona e restituisce un oggetto che implementa l'interfaccia `IAsyncResult`. Dopo la chiamata a *BeginOperationName*, un'applicazione può continuare a eseguire le istruzioni sul thread chiamante, mentre l'operazione asincrona viene eseguita in un thread del pool di thread.

Per ogni chiamata a *BeginOperationName*, l'applicazione deve anche chiamare *EndOperationName* per ottenere i risultati dell'operazione. Il valore restituito di *EndOperationName* è dello stesso tipo restituito dal metodo di servizio Web sincrono. Il codice seguente ne è un esempio:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

Il Task Parallel Library (TPL) può semplificare il processo di utilizzo di una coppia di metodi Begin/End APM incapsulando le operazioni asincrone nello stesso oggetto `Task`. Questo incapsulamento viene fornito da più overload del metodo `Task.Factory.FromAsync`. Questo metodo crea un `Task` che esegue il metodo `TodoServiceClient.EndGetTodoItems` una volta completato il metodo `TodoServiceClient.BeginGetTodoItems`, con il parametro `null` che indica che non è stato passato alcun dato al delegato `BeginGetTodoItems`. Infine, il valore dell'enumerazione `TaskCreationOptions` specifica che deve essere utilizzato il comportamento predefinito per la creazione e l'esecuzione delle attività.

Per ulteriori informazioni su APM, vedere il [modello di programmazione asincrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e la tradizionale .NET Framework la programmazione asincrona](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) su MSDN.

Per ulteriori informazioni sull'utilizzo di un servizio WCF, vedere [utilizzare un servizio Web Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>Uso della sicurezza del trasporto

I servizi WCF possono utilizzare la sicurezza a livello di trasporto per impedire l'intercettazione di messaggi. La piattaforma Novell supporta le associazioni che utilizzano la sicurezza a livello di trasporto tramite SSL. In alcuni casi, tuttavia, potrebbe essere necessario che lo stack convalidi il certificato, il che comporta un comportamento imprevisto. La convalida può essere sottoposta a override registrando un delegato `ServerCertificateValidationCallback` prima di richiamare il servizio, come illustrato nell'esempio di codice seguente:

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

Questa operazione mantiene la crittografia del trasporto ignorando la convalida del certificato sul lato server. Questo approccio, tuttavia, ignora in modo efficace i problemi di attendibilità associati al certificato e potrebbe non essere appropriato. Per altre informazioni, vedere [uso di radici attendibili rispetto](https://www.mono-project.com/UsingTrustedRootsRespectfully) a [Mono-Project.com](https://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>Uso della sicurezza delle credenziali client

I servizi WCF possono anche richiedere ai client del servizio di eseguire l'autenticazione utilizzando le credenziali. La piattaforma Novell non supporta il protocollo WS-Security, che consente ai client di inviare le credenziali all'interno della busta dei messaggi SOAP. Tuttavia, la piattaforma Novell supporta la possibilità di inviare le credenziali di autenticazione di base HTTP al server specificando i `ClientCredentialType`appropriati:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

È quindi possibile specificare le credenziali di autenticazione di base:

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

Nell'esempio precedente, se si riceve il messaggio "esaurito i trampolini di tipo 0", è possibile aumentare il numero di elastici di tipo 0 aggiungendo l'argomento `–aot “trampolines={number of trampolines}”` alla compilazione. Per altre informazioni, vedere [Risoluzione dei problemi](~/ios/troubleshooting/troubleshooting.md#trampolines).

Per ulteriori informazioni sull'autenticazione di base HTTP, anche se nel contesto di un servizio Web REST, vedere [autenticazione di un servizio Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="related-links"></a>Collegamenti correlati

- [Servizi Web in Xamarin.Forms](~/xamarin-forms/data-cloud/index.yml)
- [ServiceModel Metadata Utility Tool (Svcutil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](https://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
